# 신규성·개발 타당성 판단

기준일: 2026-09-06 · 상태: 조사에 근거한 판단, 성능 실험 없음 · [문서 안내](../README.md)

## 판단 요약

**만들 수는 있다. 하지만 ‘학습 없는 대화·캐시 인식 라우터’ 자체는 새로운 연구 주제가 아니다.** 새로운 범용 gateway를 처음부터 만드는 필요성도 현재 근거로는 약하다. 기존 실행 계층에 작은 정책 모듈을 붙이고, 명확한 좁은 가설을 검증하는 것이 가장 타당하다.

- **연구:** 미지의 잔여 단계·모델별 살아 있는 캐시·판정 비용을 결합한 전환 결정을 후보로 삼을 수 있다. 아직 선행연구가 없다고 확정하지 않았고 새로운 정식화·결과가 필요하다.
- **구현:** 규칙/프로필/kNN 중 하나로 기본 추천을 만들고 상태·경제성 정책을 분리하면 추가 neural training 없이 구현 가능한 경로가 있다. 기존 모듈을 단순 연결하는 integration 작업은 그 자체로 알고리즘 신규성이 아니다.
- **경쟁력:** 현 프로젝트는 문서만 있으며 **기존 레포보다 우수하다는 실측 근거가 없다.** 데이터·특정 harness 적합성·유지보수 부담까지 포함한 비교로 입증해야 한다.

조사 범위와 검토 깊이는 [방법론 지도](literature-landscape.md)에 있다. 아래에서 ‘기존에 없음’은 전 세계 부재 증명이 아니라 **확인한 자료의 명시적 기능 범위를 벗어나는 후보**라는 뜻으로만 사용한다.

## 넓은 신규성 주장이 어려운 이유

| 생각하기 쉬운 주장 | 가장 가까운 선행 사례 | 남는 구분점 |
|---|---|---|
| embedding만으로 모델 선택 | Avengers, kNN, RouteLLM SW, CASCAL [P09](../references/sources.md#p09) [P11](../references/sources.md#p11) [P24](../references/sources.md#p24) [P38](../references/sources.md#p38) | 어떤 데이터/통계와 효용 추정을 쓰는지 |
| label 없이 model description으로 시작 | RouteProfile SimRouter, HuggingGPT [P39](../references/sources.md#p39) [P35](../references/sources.md#p35) | profile 신뢰도·도구 역량·품질 검증 |
| 이전 대화와 ‘이건’까지 읽는 라우터 | Arch-Router, MTRouter, Switchyard [P29](../references/sources.md#p29) [P01](../references/sources.md#p01) [R06](../references/sources.md#r06) | policy match와 실제 모델 적합도는 다름 |
| 도구 단계별 승격·현재 모델 유지 | SWE-Router, TACIT-Switch, Stage, hysteresis 구현 [P03](../references/sources.md#p03) [P04](../references/sources.md#p04) [R06](../references/sources.md#r06) [R14](../references/sources.md#r14) | 인계/재시작, 승격 후 복귀, 실행 비용 |
| 장기 이력과 캐시 비용을 함께 고려 | DialRouter, Weave, vLLM protection [P36](../references/sources.md#p36) [R07](../references/sources.md#r07) [R11](../references/sources.md#r11) | 실제 cache 상태·잔여 단계 불확실성의 취급 |
| 애매할 때만 judge를 호출 | Pandora, Stage, cascade 계열 [P37](../references/sources.md#p37) [R06](../references/sources.md#r06) [P34](../references/sources.md#p34) | judge가 실제 전환 결정을 바꾸는 조건 |
| 무학습 검색+sticky+cache 경제성 조합 | Otari issue/부분 구현, Weave, vLLM [R13](../references/sources.md#r13) [R07](../references/sources.md#r07) [R11](../references/sources.md#r11) | 기능 존재가 아니라 구체 정책·평가 차이 |
| 한국어용·실측 비용 benchmark | 한국어 포함 task-type 연구, TwinRouterBench, RouteJudge [P19](../references/sources.md#p19) [P02](../references/sources.md#p02) [P20](../references/sources.md#p20) | 실제 한국어 도구 작업·재현 가능한 공정 비교의 품질 |

Otari는 특히 상태를 정확히 읽어야 한다. issue #187이 열린 채로 있어도 PR #188에서 kNN·trace_sticky는 **이미 병합**됐다. 반면 cache-aware effective cost는 제안에 남아 있고, 확인 코드에서는 tools가 있으면 default 모델을 유지한다. ‘아무것도 안 만들어졌다’와 ‘에이전트 캐시 정책이 완성됐다’ 모두 틀린 해석이다.

## 검증할 가치가 있는 좁은 연구 후보

### H4 — 잔여 길이 예측이 틀려도 견디는 전환 정책

현재 모델을 유지할지 판단할 때 단일 예측치만 쓰지 않고, 후보별 cache-prefix·TTL·provider 위치·재방문 가능성과 잔여 단계의 불확실성을 함께 둔다. 관측한 cache zero와 모르는 cache를 분리하되, **missingness 플래그 자체는 vLLM에 이미 있다.** 연구 대상은 그 불확실성이 최종 의사결정에 미치는 방식이다.

가장 단순한 제한 모델에서는 다음 손익분기를 쓸 수 있다.

```text
전환의 추가 준비 비용 K < 앞으로 N단계에서 아낄 비용 N × Δc
```

이 식은 새 방법이 아니라 baseline이다. 단계당 절감액·경로·품질이 같다는 가정을 완화하면 문제가 달라진다. 실제로는 모델을 바꿔 단계 수·출력 길이·복구가 달라지고, cache가 만료되거나 다시 살아 있을 수 있다. 앞선 비용은 sunk cost이며 앞으로의 cold input 비용에 K를 다시 더해 중복 계산하면 안 된다.

- **가까운 선행연구:** DialRouter의 worst-case cache 가정, Weave의 경제성 planner, vLLM의 잔여 길이 prior·cache/tool/switch 점수. ski-rental/online switching의 일반 원리도 신규성이 아니다. [P36](../references/sources.md#p36), [R07](../references/sources.md#r07), [R11](../references/sources.md#r11), [P43](../references/sources.md#p43)
- **검증:** 같은 base selector에서 고정 sticky, hysteresis, 고정 switching penalty, 점 예측 horizon, 후보 정책을 비교한다. 짧게 끝나는 작업·TTL 만료·A→B→A·prefix 변경을 포함한다.
- **반증 기준:** 강한 비교군 대비 성공률 조건 아래 비용·tail latency 이점이 없거나, horizon 오차에서 손실이 더 크면 이 후보를 중단한다.
- **보장 한계:** 가능한 N에 1이 포함되면 미래 여러 단계 절감으로 회수한다고 보장할 수 없다. 비용 bound가 있더라도 품질 가정을 별도로 명시해야 한다. 현재 증명·알고리즘·실험 결과는 없다.

### H5 — 추가 판정과 모델 전환을 함께 결정

‘불확실하면 judge’가 아니라 **judge 결과에 따라 실행 가능한 선택이 달라질 때만** 판정을 사는 정책을 검토한다. 예를 들어 protocol 제약으로 전환 불가하거나 어떤 유효한 판정에서도 switching margin을 넘지 못하면 판정의 가치는 낮다.

- **선행 충돌:** Pandora는 정보 가치·판정 비용을 이미 정식화한다. 학습형 estimator를 prompt로 바꾸는 것만으로 강한 신규성은 어렵다. [P37](../references/sources.md#p37)
- **추가 검증:** 같은 judge와 후보 풀에서 always-judge, margin-only selective, cache 전환까지 조건부인 selective 정책을 비교한다. judge 입력·출력·자체 cache·재시도·첫 응답 지연까지 포함한다.
- **반증 기준:** 생략한 judge 때문에 불필요 유지/잘못된 전환이 늘거나, 비용 이득이 판단 지연·실패에 의해 사라지면 채택하지 않는다. 확률 모델을 보정하면 ‘보정 없음’이라 부를 수 없다.

### H6 — 질의가 아니라 도구 상태에 조건부인 검색

목표·제약·최근 tool 결과·실패·미해결 작업을 포함하는 state representation이 query-only kNN보다 적합한 모델 선택에 도움을 주는지 본다. 입력 필드를 추가하는 것 자체는 신규성이 약하다.

- **선행 충돌:** history-model routing, verbalized retrieval, 경험 memory, conversational policy routing이 이미 있다. [P01](../references/sources.md#p01), [P08](../references/sources.md#p08), [P15](../references/sources.md#p15), [P29](../references/sources.md#p29)
- **의미 있는 실험:** 표면 질문은 같지만 이전 assistant 제안·도구 실패 원인·제약이 다른 상태쌍을 만들어 판단 차이를 확인한다. 전체 episode 실험도 필요하다.
- **반증 기준:** 전체 이력 창이나 단순 task-type baseline보다 이점이 없거나 state 추출 비용이 절감을 지우면 중단한다.
- **데이터 경계:** 테스트 통과 기록을 쓰면 human-gold-free일 수는 있지만 feedback-free가 아니다. 동일 episode·번역 쌍이 개발/평가 양쪽에 섞이지 않게 한다.

셋 중 처음부터 전부 구현할 필요는 없다. **H4를 주 가설**, H5/H6를 독립 ablation 후보로 두는 것이 이번 조사에 따른 우선순위 제안이다. 새 방법이라고 명명하거나 논문 기여를 확정한 것은 아니다.

## 기존 레포보다 무엇이 좋아질 수 있는가

| 비교 대상 | 이미 있는 강점 | 우리 쪽에서 입증해야 할 이점 | 공정 비교 조건 |
|---|---|---|---|
| Switchyard | 모듈형 선택 주기·stage 신호·classifier·escalation | 캐시 회계까지의 전환 정책이 실제 episode 비용을 줄임 | 같은 Stage/judge·후보 풀·입력; 라이브러리와 제한된 예제 adapter를 구분 |
| Weave | 모델/provider pin·토큰 회계·명시적인 경제성 planner | 캐시·잔여 단계 오차에서 손실이 작음 | `CorrectedEconomics` off/on을 구분하고 의도한 개선 경로와 비교 |
| vLLM Semantic Router | cache/tool/session 보호·진단·rescue·다양한 프로토콜 | 동일 보호 기능 위에서 달러 비용 예측·결정이 더 유리함 | 현재 protection 호출 경로 사용; adaptation과 base classifier 조건 명시 |
| Bifrost/LiteLLM | gateway·fallback·운영 기능·낮은 진입 비용 | 특정 harness에서 상태 표현 또는 exact candidate 유지로 비용/성공 개선 | 같은 제공자 기능·캐시·fallback 제공, 마케팅 지연 수치로 우열 판단 금지 |
| Otari | kNN·quality memory·guardrail·trace_sticky | 도구 작업을 실제로 다루는 전환 정책 | tool-bearing에서 decline하는 v1 한계를 명시; 이것만 이겨 전체 우월 주장 금지 |

‘운영 로그가 있다’, ‘추가 학습이 없다’, ‘context를 읽는다’, ‘캐시를 유지한다’, ‘설명 가능하다’, ‘native protocol을 지원한다’는 단독 차별점이 아니다. 구현량·설정 시간·운영 부담이 작다는 주장도 실제 설치·연동 측정 전에는 가설이다.

같은 모델의 수동 effort 조절을 반드시 비교해야 한다. 그것이 같은 성공률에서 더 저렴하면 이종 모델 router를 넣을 제품상 이유가 약해진다. Ares는 관련 학습형 선행연구이고, 수동 effort 정책은 무학습 baseline으로 가능하다. [P41](../references/sources.md#p41)

## 권고와 중단 조건

1. **채택:** 기존 gateway/harness 중 하나 + 분리된 결정 정책 + 실제 usage 회계. 현재 repo에 새 serving·인증·청구 시스템부터 만들지 않는다.
2. **연구 진행:** H4의 입력·가정·비교군을 고정하고, 최소 두 가지 단순 정책 및 Weave/vLLM의 가까운 구현과 비교한다.
3. **새 알고리즘 주장 보류:** 통합만으로 목표가 달성되면 ‘특정 에이전트 환경의 engineering 개선’으로 정직하게 보고한다.
4. **개발 중단/축소:** 고정 warm 모델·effort baseline을 비용/품질에서 못 이기거나 필요한 상태를 harness가 제공하지 못하면 범위를 줄인다. 추가 라우터의 운영 비용까지 회수하지 못하면 기존 제품 채택이 더 합리적이다.

구현 경계·재사용 위치는 [구현 재사용 계획](../design/implementation-reuse-plan.md), 측정 절차는 [평가 계획](../evaluation/experiment-plan.md)에 연결한다. 아직 주력 harness·모델 풀·품질 하한·데이터 허용 범위가 정해지지 않았으므로 지금 단계에서 특정 스택이나 수치 목표를 확정하지 않는다.
