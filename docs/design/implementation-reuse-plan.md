# 구현 재사용 계획과 경쟁 기준선

기준일: 2026-09-06 · 상태: 조사 기반 구현 경로 제안, 미구현 · [문서 안내](../README.md)

## 권고

**기존 gateway 또는 agent harness 하나를 실행 계층으로 쓰고, 별도 정책 모듈을 실험한다.** Switchyard·Weave·vLLM·Bifrost를 모두 직렬로 연결하는 방식은 권하지 않는다. 중복 retry·pin·usage 집계·protocol 변환이 생겨 효과와 오류의 책임을 분리하기 어렵기 때문이다.

‘기존 것들을 조합한다’는 뜻은 다음처럼 역할을 나누는 것이다. 이것은 승인된 구현 명세나 이미 존재하는 프로젝트 API가 아니다.

```text
agent harness의 요청·도구 상태
          │
          ▼
입력 adapter ──► 신뢰된 기능/예산 제약 검사
                         │
                         ▼
                 기본 후보 추천기
             (규칙 / profile / kNN 중 하나)
                         │
                         ▼
              유지·전환·선택적 판정 정책 ◄── cache/사용량 상태
                         │
                         ▼
             실행 adapter / 기존 gateway
                         │
                         ▼
                  제공자·모델 호출
                         │
                 usage·결과·실패 환류
```

판단용 context를 줄이더라도 executor의 도구 이력을 임의로 줄이지 않는다. 실행 상태·판단용 입력·원본 요청을 분리하는 계약은 [입력 맥락 문서](router-input-and-context.md)를 따른다.

## 두 가지 현실적인 통합 경로

| 경로 | 사용할 때 | 재사용 | 직접 필요한 부분 | 주요 위험 |
|---|---|---|---|---|
| **A. Harness 내부 정책 + 기존 API gateway** | 한 종류의 agent에서 phase·tool 결과를 확실히 얻을 수 있음 | 기존 provider 호출·인증·retry·streaming; Switchyard 같은 policy library 또는 수동 selector | 상태 adapter, 경제성 정책, usage ledger, 평가 hook | harness 결합, 요청/상태 ID·원본 args 전달 범위 |
| **B. 기존 session-aware gateway 확장** | 여러 클라이언트가 native protocol로 연결해야 함 | vLLM Semantic Router protection 또는 기존 gateway routing hook | base selector 구성, 새 비용 정책 hook, identity/telemetry 연결 | 배포·설정 계층이 큼; request만으로 phase를 모를 수 있음 |

현재 프로젝트는 코드가 없고 harness도 미정이다. 따라서 **A를 작은 실험의 우선 후보로**, B를 이미 gateway가 필요하거나 상태 hook이 부족할 때의 대안으로 둔다. 이는 A가 더 빠르다는 실측 결론이 아니다. vLLM Semantic Router는 CPU·외부 API backend 배포도 가능하므로 B를 자체 GPU 서빙 전용으로 배제해서는 안 된다. [R11](../references/sources.md#r11)

## 레포별 재사용 가능 범위

### Switchyard — 선택 정책과 호출 주기

Apache-2.0의 Rust 코어/Python 바인딩. Stage의 도구 신호·규칙, classifier의 `user_turn`/`every_request`/`new_session`, escalation 구조를 비교에 쓸 수 있다. library·client runner·demo server의 성숙도가 다르므로 ‘NVIDIA production router를 그대로 배포’로 설명하지 않는다. [R06](../references/sources.md#r06)

확인한 **LiteLLM 예제는 완제품 연결점이 아니다.**

- source checkout 전용 experimental 예제로, 당시 PyPI 0.2.0보다 새로운 API와 LiteLLM 1.97.0 고정을 요구한다.
- Stage/history·Random 등 decision-only 계약을 지원한다. 내부 `Step.CallModel`이나 생성 완료 응답은 거절하므로 LLM classifier/escalation을 그대로 끼우는 경로가 아니다.
- Stage 후보는 정확히 두 deployment를 강한 모델·싼 모델 순서로 선언하는 제약이 있다. 추상 모델명과 실제 deployment ID를 구분해야 한다.
- 판단기에 넘기는 structured messages에는 원본 tools·sampling·output/provider args가 전부 포함되지 않는다. 응답 원본 전달과 판단용 정보 전달은 별도다.
- plugin/callback 둘 다 필요하며, provider 호출·retry·변환은 LiteLLM이 수행한다. `fallback_models` 진단이 실제 fallback 정책을 자동 구성하는 것은 아니다.

**권고:** 이 adapter를 무조건 fork하기 전에 선택할 harness에서 직접 policy library 호출이 가능한지 확인한다. 작은 baseline을 만드는 데 쓰되 코드 복사만으로 native protocol·모든 judge 기능이 연결된다고 가정하지 않는다.

### Weave — 비용 planner의 기준선

모델·provider pin, 실제 usage를 이용한 회계, 캐시/출력/남은 단계에 따른 전환 비교가 있어 가장 직접적인 경제성 비교 대상이다. recommendation scorer의 pretrained cluster/HMM artifact와 planner를 분리해 판단한다. [R07](../references/sources.md#r07)

- `CorrectedEconomics`의 확인 코드 기본값은 false다. 개선식을 평가한다고 하면서 기본 경로만 실행하지 않는다. on/off와 적용 policy 예외를 로그에 남긴다.
- 고정 artifact를 소비하는 것은 ‘우리의 추가 학습 없음’일 수 있지만 data-free 알고리즘은 아니다. 입력 embedding·모델 축이 맞아야 한다.
- 실제 관측 zero와 미관측 reuse를 어떤 fallback으로 처리하는지 비교한다. 기능 이름만 보고 모든 불확실성이 해결됐다고 가정하지 않는다.
- Elastic License 2.0이므로 Apache/MIT처럼 무제한 재사용 가능한 라이브러리로 취급하지 않는다. 특히 상용 hosted 서비스로 노출할 계획이 있다면 조건을 별도 검토한다. 모든 내부 사용이 금지된다는 뜻도 아니다.

**권고:** 초기에는 고정 ref의 동작을 baseline·설계 참고로 활용한다. 통째로 의존하거나 코드를 옮기는 결정은 제품 형태·라이선스 검토 후로 둔다. 이 문서는 법률 의견이 아니다.

### vLLM Semantic Router — 이미 있는 session/cache protection

Apache-2.0. 현재 코드에서는 base route 뒤 protection layer가 SessionAwareSelector를 호출한다. 캐시·도구 루프·handoff·switch history·잔여 길이 prior·진단·rescue가 이미 있다. 단순 ‘캐시 인식+sticky’ 기능을 새로 만들어 이것보다 우수하다고 할 수 없다. [R11](../references/sources.md#r11)

- 과거 `algorithm: session_aware` 예시와 현재 factory/보호 호출 경로를 혼동하지 않는다.
- `global.router.learning.enabled` 아래에서 `adaptation.enabled`를 **명시적으로 false**로 하고 protection을 켜는 경로가 있다. adaptation을 생략하면 켜질 수 있으므로 이름만 보고 판단하지 않는다. 이는 전체 실행 가능한 설정 예제가 아니라 확인한 구성 방향이다.
- base selector까지 strict 무학습으로 쓸 것인지, pretrained classifier를 허용할 것인지 따로 정한다. protection을 무학습으로 구성했다고 전체 분류기가 무학습이 되는 것은 아니다.
- conversation/session ID가 필요하고, identity 부재 시 보호를 적용하지 않는 진단 경로가 있다. matched decision이 바뀌거나 candidate pool 밖이면 기존 모델을 강제로 고수하지 않는다.
- score는 utility와 정규화 비용 신호의 조합이다. **정확한 달러 단위 잔여 비용 최소화**라고 재명명하지 않는다.
- cache actual/estimated·known-zero 처리가 이미 존재한다. 상류 telemetry→선택 상태까지 같은 의미가 보존되는지를 시험하는 것이 과제이며, 필드 하나 추가가 신규성이 아니다.

**권고:** B 경로를 택하면 새로운 sticky 구현보다 기존 protection 위의 비용 정책 실험이 먼저다. A 경로를 택해도 이 시스템의 보호 기능을 동일 조건의 경쟁 baseline으로 둔다.

### Bifrost/LiteLLM — gateway와 단순 baseline

Bifrost의 semantic complexity는 고정 reference 문장과 embedding을 사용하며, session tier를 상향 고정하고 도구 후속·background 요청을 구분한다. 다만 **tier pin은 exact model/provider/cache pin이 아니다.** assistant를 제외한 최근 user 메시지 추출은 지시어 문제를 남긴다. HTTP/SSE 경로를 native WebSocket까지 지원한다고 확장하지 않는다. [R12](../references/sources.md#r12)

LiteLLM의 heuristic·LLM·hybrid·heuristic-v2는 서로 다른 데이터/추론 조건이다. 기존 provider gateway 기능을 쓸 수 있지만 complexity score가 cache-aware agent 품질 예측을 대체하지는 않는다. [R03](../references/sources.md#r03)

**권고:** 이미 이 gateway를 쓴다면 그대로 유지하고 정책 hook의 입력·출력 계약만 검증한다. 라우터 비교 때문에 운영 계층까지 한꺼번에 바꾸지 않는다.

### Otari와 연구용 toolbox — 특정 부분만

Otari는 사용자 품질 memory·kNN·guardrail·trace_sticky 참고 구현이다. 확인 v1은 tool-bearing 요청에서 default를 쓰고 list price를 사용하므로 현재 agent cache 전환의 완성 베이스로 보기 어렵다. 라이선스·배포 범위는 재사용 전 추가 확인한다. [R13](../references/sources.md#r13)

ORBIT/RouterBench/ULab LLMRouter는 **오프라인 정책 평가·입출력 표준화 참고**다. 정규화된 비용과 static output 선택을 live usage ledger에 그대로 쓰지 않는다. ULab kNN의 최고 성능 label 투표와 kNN utility regression도 별도 정책이다. [R10](../references/sources.md#r10), [P25](../references/sources.md#p25), [R09](../references/sources.md#r09)

## 먼저 정해야 할 상태 계약

| 객체 | 필요한 정보 | 강제해야 할 구분 |
|---|---|---|
| 작업 상태 | session/episode/branch ID, 목표·제약·phase, 최근 실패·검증, 미해결 작업 | 사용자 질문·assistant 제안·tool 결과의 역할과 신뢰 수준 |
| 후보 카탈로그 | model/provider/deployment, 도구·출력 형식·context 지원, 가격 버전 | 품질 관측·수동 가정·미관측; 임베딩 유사도를 확률로 재명명 금지 |
| 캐시 관측 | 모델/제공자별 최근 usage·prefix 지문·시간·추정 TTL | 실제/추정/unknown, known zero, cached read/write/fresh input |
| 실행 제약 | provider 전용 state, pending tool pair, 허용 모델·지역·예산 | 비용 선호보다 protocol/보안 제약 우선 |
| 결정 기록 | base 추천, 제약 탈락, keep/switch/abstain, 판단 비용, 예상 이득 범위 | 실행 가능한 결정과 설명·예측을 분리 |
| 실행 ledger | 모든 model attempt·judge·retry·폐기 응답·tool 비용과 지연 | 성공한 최종 호출만 합산하지 않기 |

모델을 바꾸기 전에 공개 메시지·도구 상태가 대상 API에서 표현 가능한지 확인한다. `previous_response_id` 같은 서버 전용 상태나 reasoning 블록을 단순 문자열 변환으로 이식 가능하다고 가정하지 않는다. 진행 중 tool 실행은 이미 수행된 부작용이므로 모델 교체가 rollback은 아니다.

분기·동시 요청에서는 session 전체의 단일 mutable pin으로 충분한지 검증한다. tenant/branch를 섞지 않고, 같은 관측을 중복 반영하지 않아야 한다. 이는 구현 제안이며 이미 모든 gateway가 잘못 처리한다는 주장이 아니다.

## 단계별 확인 관문

날짜·인원·성능 목표를 임의로 확정하지 않는다. 각 단계의 완료 증거는 아래와 같다.

1. **범위 고정:** 한 harness·작업군·후보 풀·추가 학습의 허용 경계를 명시한다. 같은 모델의 effort baseline을 포함할 수 있는지 확인한다.
2. **선택 없는 통합 확인:** 고정 모델로 tool-call/result·streaming·retry·usage가 원래 harness와 동일하게 동작하는지 확인한다. 원본 args가 빠지면 이후 정책 실험으로 넘어가지 않는다.
3. **단순 baseline:** 고정 강/약 모델, 최초 선택 후 pin, 매 단계 동일 규칙, hysteresis를 넣는다. 성공 판정·전체 ledger·재현 seed/버전을 남긴다.
4. **기존 경쟁 정책:** Switchyard Stage, Weave 개선 planner, vLLM protection 중 환경에서 실행 가능한 가까운 비교군을 구성한다. 실행 불가한 경우 이유를 남기고 동등 기능 재구현은 ‘원본 재현’과 구분한다.
5. **하나의 신규 가설:** [H4](../research/novelty-and-feasibility.md#h4--잔여-길이-예측이-틀려도-견디는-전환-정책)를 먼저 평가한다. H5/H6는 효과 분리를 위한 선택 실험이다.
6. **채택/중단:** 성공률 조건·전체 비용·첫 응답 및 p95 지연·운영 부담으로 판단한다. 못 이기면 기존 제품 사용 또는 고정 모델 정책으로 축소한다.

이 순서는 구현 작업을 이미 승인·착수했다는 뜻이 아니다. 현재 산출물은 조사 문서이며 설치·빌드·유료 API 호출·benchmark는 수행하지 않았다.

관련: [구성 후보](router-options.md) · [평가 계획](../evaluation/experiment-plan.md) · [신규성 판단](../research/novelty-and-feasibility.md)
