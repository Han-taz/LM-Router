# 공개 구현과 운영 서비스 비교

확인일: 2026-09-06 · 상태: 문서·소스 조사, 직접 실행 검증 없음 · [문서 안내](../README.md)

## 먼저 읽을 구현

추가 조사로 vLLM Semantic Router·Bifrost·Otari를 포함했다. 아래 표는 기능 위치 요약이며, 현재 API·실험적 adapter·라이선스·재사용 경계는 [구현 재사용 계획](../design/implementation-reuse-plan.md)을 우선한다. 설치·운영 성능은 검증하지 않았다.

| 대상 | 역할 | 무학습 관점 | 참고할 부분 | 직접 해결하지 않는 부분 |
|---|---|---|---|---|
| NVIDIA Switchyard | 에이전트 호출별 선택 알고리즘·통합 라이브러리 | 고정 규칙 Stage 또는 기존 LLM judge, 권장값에 저자 보정 존재 | 선택 주기, 도구 진행 신호, 한 번 승격, 판단 비용 추적 | 확인한 Stage scorer는 캐시 비용 최적화기가 아님 |
| Weave Router | 모델 추천 + 세션·캐시 경제성 + 제공자 전송 | 기본 추천은 학습 artifact 의존, 캐시 planner는 결정적 계산 | 캐시 보존 대 전환의 비용 비교와 실측 회계 분리 | 전체를 순수 무학습 라우터로 분류할 수 없음 |
| vLLM Semantic Router | base route + session/cache/tool protection | adaptation을 끈 보호 경로 가능; base 분류기 조건은 별도 | 현재 protection 호출, switch guard·rescue·telemetry | 점수 기반 보호를 정확한 미래 dollar 최적화와 구분 [R11](../references/sources.md#r11) |
| Bifrost | gateway + semantic complexity + tier session | 고정 예시 embedding; LLM fallback은 선택 | tier 상향 유지·도구 후속 재사용·provider usage | exact model/provider pin 아님; user-only 입력 한계 [R12](../references/sources.md#r12) |
| Mozilla Otari | kNN·quality memory·trace_sticky | neural 학습 없음, 품질 점수 데이터 필요 | 불확실 시 default·allowlist·대화 유지 | tools present에서 decline; v1은 list price, cache 경제성은 제안 [R13](../references/sources.md#r13) |
| BlockRun router-core | 요청별 모델 선택 | 로컬 규칙·수동 가중치·모델 프로필 | 분류·기능 필터·후보 점수·선택 이유 | 실제 캐시 청구를 반영한 전체 작업 최적화 |
| ClawRouter | 라우터를 프록시·세션에 통합 | core 정책 + 세션·실패 상태 | 주 모델 유지, 승격 상태, 비용 누적 | 제품별 의존성과 결제·캐시 동작 검증 필요 |
| LiteLLM Complexity Router | 여러 분류 방식과 전송 통합 | `heuristic`, `llm`, `heuristic_first`를 구분 | 규칙 기준선과 선택적 LLM 판단을 같은 틀에서 비교 | 옵션 전체가 순수 무학습인 것은 아님 |
| Portkey Gateway | 조건부 요청 전달 | 수동 조건 엔진 | metadata·요청 파라미터 기반 정책, fallback 조합 | 쿼리의 의미·난이도를 자동으로 알아내는 기능 |
| SGLang cache-aware policy | worker 선택·부하 분산 | prefix·부하 기반 계산 | 캐시 유사성과 부하 사이의 절충 | 이종 모델의 답변 품질 비교 |
| Aurelio Semantic Router | 예시 발화의 의미로 경로 선택 | 정적 경로는 추가 학습 없이 구성, threshold fitting은 선택 | 사람이 정한 의미 경로·매칭 없음 처리 | 경로 매칭만으로 모델별 성공률·가격을 알 수 없음 |
| ULab LLMRouter KNNRouter | 사례 기반 모델 분류 | 신경망 학습은 아니지만 성능 label 필요 | 이웃 분류기와 공통 평가 틀 | 확인한 kNN은 성능·비용 utility 예측기가 아님 |

‘router’라는 이름만으로 같은 문제를 푸는 시스템으로 묶지 않는다. 모델 선택 정책, 제공자/서버 선택, 도구 선택, 응답 재사용은 서로 다른 계층이다.

## R01. BlockRun router-core

[저장소와 확인 소스](../references/sources.md#r01)

`rules.ts`의 점수화, `portfolio.ts`의 후보 비교, `selector.ts`의 기능·용량·비용 관련 경로를 확인했다. 외부 LLM 호출 없이 결정하는 코어이며, 호스트가 모델 카탈로그와 운영 프로필을 제공할 수 있다. 프록시 전체보다 정책 코어를 읽기 좋은 대상이다.

우리 용도의 검토 사항:

- 확인한 `estimatedCost()`는 입력·출력 단가로 계산한다. 이 함수에는 cache read/write 구분이 없어 그대로 가져오면 이번 캐시 문제를 해결하지 못한다.
- `calculateModelCost()`에는 해당 제품의 결제 마진·최소 비용이 들어간다. 우리 제공자의 회계로 그대로 복사하면 안 된다.
- README의 ‘fails closed’ 표현과 달리 확인한 `isEligible()`은 명세가 없는 후보를 허용하고, 해당 코드 경로에는 필터 결과가 비면 원래 chain을 재사용하는 분기가 있다. 전체 통합 경로의 보장을 별도 검증해야 한다.
- 수동 affinity·모델 프로필은 품질 가정이다. 특정 제품의 벤치마크·언어·모델 카탈로그에서 얻은 가정을 우리 작업군의 보장으로 사용하지 않는다.

업체의 속도·절감률 주장은 자체 측정이다. 이번 조사에서는 그 수치를 재현하지 않았다.

## R02. ClawRouter

[저장소와 확인 소스](../references/sources.md#r02)

`src/session.ts`에는 모델·tier·최근 사용 시각·명시적 사용자 pin·승격 상태·누적 비용 등의 상태가 있다. 라우팅 규칙과 세션 상태를 따로 관리하는 예시다. 실제 승격 동작 전체는 이 파일만으로 검증한 것이 아니다.

기능 문서의 **response cache는 이전 응답을 돌려주는 캐시**다. 공급자의 KV/prompt cache와 같은 기능으로 해석하면 안 된다. 제품 전체 설치보다 session 상태 모델과 core 호출 경계를 참고한다. [R02](../references/sources.md#r02)

## R03. LiteLLM Complexity Router

[저장소·README·구현 코드](../references/sources.md#r03)

조사 시점에 사용자 조건과 직접 관련 있는 경로가 이미 존재한다.

| 옵션 | 설명 | 현재 분류 |
|---|---|---|
| `classifier_type: heuristic` | 기존 수동 가중치 scorer | 추가 라우터 학습·LLM 호출 없음 |
| `classifier_type: llm` | 설정한 기존 LLM으로 분류 | 추가 학습 없이 판단 호출, 비용 발생 |
| `classifier_type: heuristic_first` | 규칙을 먼저 적용하고 일부 요청만 LLM에 전달 | 규칙+선택적 LLM 후보와 직접 대응 |
| `classifier_type: heuristic_v2` | 번들 UltraFeedback 자료와 tier 성공 추정·임계값 사용 | 데이터로 맞춘 구성. 순수 수동 규칙과 구분 |

구현은 실제 어떤 경로가 실행됐는지 `cause`로 기록하고, 실패 시 분류 fallback을 둔다. 문서에는 세션 affinity와 modality 관련 예외도 있어, pin이 기능 검사를 우회하는 설정이 없는지 구현 전에 확인해야 한다.

별도의 `lowest_cost.py`는 주어진 model group의 healthy deployment를 비용으로 선택하는 경로다. 이것만으로 질의 난이도에 맞는 이종 모델을 알아서 선택한다고 설명하면 부정확하다. 기본 규칙과 LLM 혼합 후보를 비교할 때 먼저 읽을 구현이다.

## R04. Portkey Gateway

[저장소·조건 엔진·공식 문서](../references/sources.md#r04)

`src/services/conditionalRouter.ts`는 metadata·파라미터·URL에서 조건을 평가해 target을 선택한다. 조건별 전달과 fallback·load balancing 조합을 참고할 수 있다.

이 엔진이 의미적 난이도 점수를 직접 만들어주는 것은 아니다. 예를 들어 `metadata.phase`나 `metadata.requires_reasoning`을 정책에 사용하려면 그 값을 신뢰할 수 있게 만드는 단계가 별도로 필요하다. 순수 규칙 라우팅의 운영·전송 계층 참고 자료다.

## R05. SGLang cache-aware policy

[확인한 코드](../references/sources.md#r05)

worker별 prefix 이력의 근사 트리와 부하 정보를 사용한다. 부하가 균형적일 때 캐시 일치를 선호하고, 불균형하면 큐 길이를 고려한다. 캐시 추정만 극대화하면 특정 서버에 요청이 몰릴 수 있다는 설계 포인트를 보여준다.

이 정책을 GPT·Claude 등 서로 다른 모델의 품질 라우터로 소개하지 않는다. 주어진 모델의 실행 위치를 정하는 참고이며, 관리형 API에서 그와 같은 worker 상태를 우리가 볼 수 있다고 가정하지 않는다.

## R06. NVIDIA Switchyard

[저장소·고정 ref·확인한 문서와 코드](../references/sources.md#r06)

**현재 무학습 에이전트 정책 조사에서 우선 읽을 대상이다.** 동일 프로젝트 안에서 초기 선택, 단계별 재평가, 승격, advisor를 구분한다. Rust 코어를 Python/Rust harness에 삽입하거나 게이트웨이에 통합할 수 있다. 선택과 전송을 분리하는 구조도 참고할 만하다.

### 선택 주기를 직접 비교할 수 있다

| Classifier 설정 | 다시 판단하는 시점 | 이전 질문과의 관계 |
|---|---|---|
| `every_request` | 모든 모델 요청, 도구 후속 요청 포함 | 단계별 재선택 |
| `user_turn` | 새로운 사용자 메시지 | 도구 루프 동안 유지 |
| `new_session` | 세션 최초 요청 | 여러 사용자 질문을 포함할 수 있는 세션 동안 유지 |

고정 동작에는 세션 식별자가 필요하다. 첫 사용자 메시지의 해시를 쓰는 fallback은 동일한 시작 문구를 가진 독립 세션을 합칠 수 있다. 또한 위 설정은 독립 Classifier 경로의 옵션이며, Stage에 삽입한 judge에 그대로 적용된다고 가정하지 않는다.

판단 주기와 판단용 입력은 별개다. Classifier 기본 입력은 첫 작업+최신 사용자 메시지이며, 중간 assistant·도구 맥락은 `recent_turn_window`로 확대한다. ‘이건’ 같은 지시어에서 생길 수 있는 누락은 [입력 맥락 조사](../design/router-input-and-context.md)에 정리했다.

### 학습이 없는 경로와 비용

- **Stage, judge 생략:** 도구 결과에서 오류 심각도, 진행 없는 반복, 탐색, 최근 쓰기·편집 비율을 추출한다. 고정 가중치의 합을 `tanh`로 변환하고 임계값·중대 오류 등의 규칙으로 결정한다. 실행 중 학습이나 라우터 LLM 호출이 없다.
- **Stage + judge / Capability:** 기존 모델의 구조화된 판단을 사용한다. 전용 라우터 가중치를 학습하지 않아도 판단용 추론 비용·지연이 든다.
- **Escalation:** 저가 응답을 먼저 생성·버퍼링한 뒤 judge가 평가한다. 확인이 누적되면 그 응답을 버리고 강한 모델을 호출하며, 이후에는 강한 모델로 고정한다. 전환 단계에는 저가+judge+강한 모델 비용이 모두 생기고, 미전환 단계도 저가+judge 지연을 기다린다.

권장 Stage 임계값 `0.5`는 문서상 SWE-Bench Pro Python-75 보정에서 나온 값이다. ‘실행 시 라우터 학습 없음’이지 ‘데이터에 맞춘 설정이 전혀 없음’은 아니다. 또한 점수의 크기를 confidence라고 부르지만 실측 성공 확률로 곧바로 해석할 수 없다.

### 캐시와 도입상의 한계

확인한 Stage 선택 함수는 도구 진행 신호를 쓰며, 캐시 읽기·쓰기 단가와 남은 호출 비용을 직접 비교하지 않는다. 별도 `theoretical_cache_hit_rate`는 텍스트 길이·prefix 지문에 의한 이론적 재사용 가능성으로, 제공자 usage의 실제 캐시 적중률이 아니다. 추적 기능은 메모리가 계속 증가할 수 있어 코드상 평가용 opt-in이다.

README는 전체가 pre-1.0이고, 코어 라이브러리는 Beta, 독립 서버는 Demo·비운영용이라고 명시한다. 벤치마크는 NVIDIA 내부 추론 endpoint와 평균 ISP 토큰 비용 기준이므로 우리 제공자의 실제 캐시 포함 청구 절감으로 옮기지 않는다. 라이선스는 Apache-2.0이다.

## R07. Weave Router

[저장소·고정 ref·확인한 문서와 코드](../references/sources.md#r07)

**캐시를 고려한 전환 설계와 비용 회계의 우선 참고 자료다.** 문서는 사용자 대화의 turn과 API 요청인 action을 명시적으로 구분한다. action마다 결정하지만 세션 pin과 운영 상태가 최종 선택에 개입하므로, 매 action 모델이 바뀐다는 뜻은 아니다.

### 전체 라우터가 무학습인 것은 아니다

기본 `cluster` 경로는 로컬 ONNX 임베딩과 사전 생성된 centroid·클러스터별 모델 품질표를 쓴다. artifact 문서에는 trainer와 학습 시점의 설정이 명시되어 있다. 사용자가 재학습하지 않고 실행할 수 있어도, 전용 학습 결과에 의존하는 방식이다.

README가 연결하는 배경 논문은 **Avengers-Pro**다. 이는 신경망 라우터 fine-tuning 없이 클러스터별 성능·비용표를 구성하는 방법이다. 클러스터 fitting·성능표 생성과 HMM/XGBoost 학습을 같은 방식으로 묶지는 않는다. [논문 P10](../references/sources.md#p10), [Avengers 계열 방법 비교](training-free-methods.md)

기본 임베딩 경로는 사용자 텍스트를 모아 tail을 절단하며, 프록시가 가진 전체 role별 대화를 그대로 읽는 것은 아니다. assistant 답변을 가리키는 후속 질문의 한계와 다른 정책용 구조화 이력은 [입력 맥락 조사](../design/router-input-and-context.md)에 구분했다.

선택적 frozen HMM도 PCA/HMM/XGBoost artifact를 사용한다. 공개 sidecar는 온라인 학습을 하지 않지만 이미 학습된 정책이며, 지정된 Google 임베딩 공간과 API 호출에 의존한다. 기본 로컬 cluster scorer와 같은 비용·의존성을 가진다고 보면 안 된다.

### 추천과 캐시 전환 판단이 분리되어 있다

`internal/router/planner/policy.go`는 새 추천과 현재 pin을 받아 유지·전환을 결정하는 순수 계산 함수다. `turnloop.go`에서 실제로 호출되며 다른 승격·전략별 예외도 적용된다.

- 캐시 읽기 할인, 만료 추정, 예상 입력량, 남은 단계 수, 전환 임계값을 사용한다.
- `CorrectedEconomics` 경로는 재사용 prefix 비율, 캐시 쓰기 프리미엄, 이전 출력량을 이용한 출력 비용 추정도 반영한다.
- 코드 기본값은 planner 활성화, `CorrectedEconomics=false`다. 따라서 개선된 비용식이 모든 배포에서 사용된다고 주장하지 않는다.
- 관측된 재사용 길이 0과 미관측을 구분한다. 다만 미관측 때 전체 캐시 가능으로 간주하는 fallback이 있어, 우리 정책에서 그대로 채택할지는 따로 판단해야 한다.
- 예상 남은 단계 수와 전환 비용은 추정이다. 코드가 `eviction_cost`라 부르는 값도 실제 캐시 삭제 명령이나 별도 청구 수수료가 아니다.

`catalog/cost.go`는 선택 전 예측과 별개로 사용 후 캐시 읽기·쓰기·신규 입력을 회계한다. 제공자에 따라 전체 input에 cached input이 포함되는지 구분한다. `sessionpin/store.go`에는 직전 사용량, 실제 제공자·모델, 이전 전환 이력이 남는다. 이 분리가 이번 프로젝트의 핵심 참고점이다.

### 도입상의 한계

학습된 cluster/HMM까지 통째로 도입하는 것은 엄격한 무학습 방향과 맞지 않는다. 우리 목적에는 **추천기와 캐시 planner를 분리한 설계 원리**가 더 직접적이다. TTL·직전 사용량·고정 horizon에 기초한 추정이 실제 비용을 얼마나 맞추는지도 평가해야 한다.

라이선스는 Elastic License 2.0이며 hosted/managed 제공 관련 제한이 있다. Apache-2.0과 같은 조건으로 코드를 복사·서비스화할 수 있다고 가정하지 말고 도입 전에 라이선스 범위를 검토한다. README의 hosted 경로는 확인했지만, 서비스의 성능·비용 절감률은 직접 검증하지 않았다.

## R08. Aurelio Semantic Router

[저장소와 확인 소스](../references/sources.md#r08)

`Route.utterances`에 사람이 예시를 정의하고 frozen dense embedding으로 색인을 만들어 경로를 고르는 라이브러리다. `SemanticRouter`와 `BaseRouter.__call__`의 입력 임베딩·검색·경로 점수화, 선택적인 `fit`의 labeled threshold 탐색을 구분해서 확인했다.

정적 경로를 만드는 데 모델별 정답·성능표는 필수가 아니다. 대신 경로와 모델의 연결은 별도 정책이다. 의미 매칭 성공을 적절한 저가 모델 선택의 성공과 동일하게 평가하지 않는다. 자세한 적용 범위는 [추가 방법론](alternative-routing-methods.md)에 있다.

## R09. ULab LLMRouter의 kNN

[저장소와 확인 소스](../references/sources.md#r09)

연구용 여러 라우터를 공통 틀에서 비교할 때 참고할 구현이다. 확인한 `KNNRouter`는 질의별 최대 `performance`의 모델을 label로 정하고, Longformer embedding과 sklearn 이웃 분류기로 선택한다. `trainer.py`의 `.fit`는 추가 신경망 학습을 하지 않지만 모델 성능 label과 저장 artifact가 필요하다.

따라서 [P11](../references/sources.md#p11)의 ‘이웃에서 모델별 성능과 비용을 각각 예측하는’ utility router와 동일하게 취급하지 않는다. 캐시 정책·전체 agent 실행·라이브러리의 다른 전략들은 이 소스 확인만으로 검증한 것이 아니다.

Smoothie의 공개 코드는 별도의 [P14](../references/sources.md#p14)에 등록했다. 원문 알고리즘과 사전 생성 파일 기반 평가 runner를 읽는 참고이며, 그대로 운영 프록시를 제공하는 저장소로 분류하지 않는다.

## 현재 운영 제품에서 확인한 동작

### OpenRouter Auto Router

최신 공식 페이지는 작업 유형 분류→유형별 최근 시장 지출 점유율 순위→비용 구간·제약→fallback 순서를 설명한다. 세션에서는 기존 모델을 선호하되, 새 요청에서도 상위 후보일 때 재사용한다. 유형이 바뀌면 모델도 바뀔 수 있다. [D05](../references/sources.md#d05)

참고할 부분은 작업 유형별 후보 순위, 재평가와 모델 유지의 조합, 실제 사용 모델을 응답으로 노출하는 방식이다. 지출 점유율은 품질 라벨이 아니며, 분류기 내부가 공개되지 않아 전체 시스템을 순수 무학습으로 분류하지 않는다.

**갱신 주의:** 이번 검색 결과에는 과거 Not Diamond 기반 설명과 무조건 모델 pin 설명이 남아 있었지만, 실제 열린 최신 페이지는 위와 달랐다. 구현 전 원문을 다시 확인한다.

### Not Diamond

공식 문서는 cross-domain 데이터로 학습한 사전학습 라우터와 사용자 평가 데이터로 학습하는 custom router를 구분한다. 사전학습 버전을 쓰면 우리가 새로 학습할 필요는 없지만, 이것이 전용 라우터 학습을 배제한 방식은 아니다. 제품 인터페이스와 품질·비용 절충을 참고할 비교 대상으로 둔다. [D06](../references/sources.md#d06)

### Portkey

호스팅 제품에도 조건 라우팅이 문서화되어 있다. 공개 조건 엔진과 함께 ‘학습 없이 설정한 정책을 운영 요청에 적용하는 사례’로 볼 수 있다. 자동 의미 분류 성능이나 비용 절감률을 입증하는 자료는 아니다. [R04](../references/sources.md#r04)

### Augment Prism

[공식 엔지니어링 설명](../references/sources.md#d09)은 사용자 턴 시작의 작은 planner 호출, 턴 내 tool-result 후속 요청의 선택 재사용, 다음 사용자 턴의 cache-aware switching을 설명한다. 학습 방식과 내부 정책 코드는 확인하지 못했으므로 순수 무학습 구현으로 분류하지 않는다.

운영 관점의 핵심은 추가 판단 호출이 총비용에서는 작아도 새 사용자 턴의 첫 응답 대기에는 클 수 있다는 점이다. 업체의 공개 지연·비용 수치를 우리 환경의 보장으로 사용하지 않고, 선택 주기와 측정 분모를 참고한다. [추가 방법론 비교](alternative-routing-methods.md)

## 선정 기준

이 프로젝트의 후보 평가에서는 다음을 순서대로 확인한다.

1. 선택 경로가 규칙인지, 기존 LLM 판단인지, 학습·보정된 전용 모델인지.
2. 같은 기능이 README뿐 아니라 현재 소스·설정 경로에도 있는지.
3. 실제 API 청구와 같은 캐시 회계인지, 단순 input/output 가격 비교인지.
4. 에이전트 상태·도구 결과를 읽을 수 있는지, 최초 prompt만 읽는지.
5. 모델·제공자·정책 버전과 선택 이유를 남기는지.
6. 세션 pin이 도구·이미지·컨텍스트 등 필수 기능 검사를 무력화하지 않는지.

사용자가 제시한 두 저장소를 추가한 뒤 우선순위를 조정했다. **에이전트 무학습 정책과 선택 주기는 Switchyard**, **캐시 전환 경제성과 회계는 Weave**를 먼저 읽는다. LiteLLM·BlockRun은 규칙·혼합 정책 비교, ClawRouter는 세션 통합, Portkey는 조건부 전송 참고로 유지한다. 전체 저장소를 그대로 도입하자는 결정은 아니다.
