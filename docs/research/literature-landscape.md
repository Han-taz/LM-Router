# LLM 라우팅 방법론 지도와 조사 범위

기준일: 2026-09-06 · 상태: 원문·선택 코드 조사, 재현 없음 · [문서 안내](../README.md)

## 결론부터

임베딩 라우팅은 여러 선택지 중 하나다. **무엇을 입력받는가, 언제 다시 결정하는가, 어떤 추정·학습을 하는가**를 분리하면 방법론을 덜 놓친다. 예를 들어 ‘kNN + 도구 상태 + 사용자 턴 내 고정’은 세 축을 결합한 구성이지 반드시 새로운 선택 알고리즘은 아니다.

‘라우터를 학습시키지 않는다’는 현재 제약에는 네 가지 경계가 있다.

| 조건 | 허용할 수 있는 예 | 그래도 필요한 것 |
|---|---|---|
| 추가 neural weight 학습 없음 | 고정 규칙, profile similarity, kNN, cluster lookup, prompting | 수동 정책·프로필·데이터·추론 비용 중 일부 |
| 우리 쪽 추가 학습 없음 | 공개 frozen Arch-Router·RouteLLM 등 | 타인이 학습한 checkpoint·모델 풀 적합성 확인 |
| 과거 성능 label 없음 | 수동 semantic 예시, RouteProfile SimRouter, CASCAL/Smoothie 변형 | 공개 정보 또는 무라벨 생성 데이터·통계 가정 |
| fitting·보정·온라인 학습까지 없음 | 동결된 수동 규칙/예시/프롬프트 | 사전 설정이 맞는지 평가할 데이터는 여전히 필요 |

이 문서의 기본 구현 후보는 첫 번째 조건이다. 사용자 의도보다 넓게 해석하지 않도록 **통계 학습·성능 memory·pretrained router는 선택 후보**로 표시한다. 학습형 논문도 신규성 비교에서는 제외하지 않는다.

## 조사 방법과 커버리지

단순 검색 결과 수 대신 다음 세 층으로 조사했다.

1. **넓이 확인:** 최신 routing/cascading survey 두 계열, 저자 inventory, ORBIT의 28개 방법/변형 목록으로 빠진 연구 축을 점검했다. Oracle·동일 논문의 여러 구현을 독립 논문으로 세지 않았다. [P21](../references/sources.md#p21), [P22](../references/sources.md#p22), [R10](../references/sources.md#r10)
2. **방법 확인:** 아래 대표 원문의 방법·실험·한계에서 추가 학습, 데이터 준비, 온라인 호출, 비용 정의를 확인했다. 검색 축은 training-free/embedding/kNN/model profile, cascade/verification/value of information, conversational/agentic/multi-turn, cache/switching/stickiness, bandit/budget/unknown horizon, evaluation/benchmark였다.
3. **구현 확인:** Switchyard·Weave에서 시작해 vLLM Semantic Router·Bifrost·Otari의 핵심 호출 경로와 설정을 읽었다. README, 실제 호출되는 코드, 아직 issue인 제안을 구분했다. 고정 ref와 읽은 파일은 [출처 목록](../references/sources.md)에 둔다.

**한계:** 체계적 문헌고찰의 완전한 검색 DB/export·중복 제거 기록은 만들지 않았다. 공개되지 않은 서비스 내부·모든 논문·모든 코드 경로를 조사했다는 의미가 아니다. 최신 preprint는 동료심사·독립 재현이 없을 수 있다. 이번 조사로 넓은 신규성 주장은 배제할 수 있지만, 특정 아이디어가 세계 최초라는 부재 증명은 할 수 없다.

## 방법론 비교

| 계열 | 대표 자료 | 핵심 결정 방식 | 학습·준비 조건 | 이 프로젝트에서의 위치 |
|---|---|---|---|---|
| 수동 조건·phase 규칙 | Switchyard Stage, LiteLLM heuristic, Portkey [R06](../references/sources.md#r06) [R03](../references/sources.md#r03) [R04](../references/sources.md#r04) | 기능·작업·도구 신호에 고정 규칙 적용 | neural 학습 없음; 규칙·임계값 설계 | 가장 단순한 baseline, 품질 예측으로 과장 금지 |
| 수동 semantic 예시 | Aurelio, Bifrost [R08](../references/sources.md#r08) [R12](../references/sources.md#r12) | 질의를 예시 문장과 비교해 경로/tier 선택 | frozen embedding; 선택적 threshold fitting/LLM fallback | 성능 label 없이 시작 가능; 의미 유사도≠모델 적합도 |
| 모델 프로필 유사도 | RouteProfile SimRouter, HuggingGPT [P39](../references/sources.md#p39) [P35](../references/sources.md#p35) | query와 model description/graph를 매칭하거나 LLM이 선택 | 공개 profile·embedding 또는 LLM 호출 | 새 모델 cold start 후보, 이미 선행연구 존재 |
| 이웃 효용·선호 검색 | kNN utility, Eagle, RouteLLM SW, PORT [P11](../references/sources.md#p11) [P05](../references/sources.md#p05) [P24](../references/sources.md#p24) [P07](../references/sources.md#p07) | 근처 질의의 모델별 품질·비용·선호를 집계 | labeled support·순위/최적화; 추가 neural 학습은 생략 가능 | 데이터 허용 시 강한 단순 baseline |
| 클러스터·skill 성능표 | Avengers(-Pro), InferenceDynamics [P09](../references/sources.md#p09) [P10](../references/sources.md#p10) [P40](../references/sources.md#p40) | 의미 cluster 또는 capability/knowledge별 모델 점수 | 성능 label·후보 profiling·clustering/LLM 분석 | 온라인 선택은 저렴할 수 있으나 준비 비용 존재 |
| 무라벨 생성·합의 | CASCAL, Smoothie, CARGO [P38](../references/sources.md#p38) [P14](../references/sources.md#p14) [P13](../references/sources.md#p13) | 생성 결과의 일치·유사도·확률로 신뢰도 추정 | gold label 없이 가능, 복수 출력·통계 fitting/보정 | 공유 오답·전체 생성 비용 확인 필요 |
| 기존 LLM 판단·경험 검색 | BoundaryRouter, VDAR, Switchyard classifier [P15](../references/sources.md#p15) [P08](../references/sources.md#p08) [R06](../references/sources.md#r06) | task/state/경험을 rubric이나 prompt로 판정 | 추가 가중치 학습 없음; LLM·memory 비용 | 애매한 경우만 호출하는 비교 후보 |
| 생성 후 검증·cascade | AutoMix, FrugalGPT, Cascade Routing [P16](../references/sources.md#p16) [P23](../references/sources.md#p23) [P34](../references/sources.md#p34) | 작은 모델 응답을 관측한 뒤 수락·승격 | verifier·정책 fitting은 방법별 다름 | 테스트 가능한 작업에 유망; 폐기 응답 비용 포함 |
| 보정된 불확실성 | CP-Router [P12](../references/sources.md#p12) | prediction set을 이용해 작은/큰 모델 배정 | labeled calibration·일부 logits 필요 | 제한된 출력 형식 후보; 전체 agent 성공 보장 아님 |
| 판정의 정보 가치 | Pandora [P37](../references/sources.md#p37) | 더 정확한 판정의 예상 이득이 판정 비용보다 큰지 계산 | 원문은 trained estimator·통계 보정 | 개념은 직접적 선행연구, 그대로는 무학습 아님 |
| 학습형 품질·representation | Hybrid, RouterDC, GraphRouter, ICL-Router [P26](../references/sources.md#p26) [P27](../references/sources.md#p27) [P28](../references/sources.md#p28) [P30](../references/sources.md#p30) | 질의별 모델 적합도를 학습 | 초기 학습, 일부는 새 모델도 profiling 필요 | 구현 우선순위 밖이어도 비교에서 제외 불가 |
| 대화 정책·장기 상태 | Arch-Router, MTRouter, DialRouter [P29](../references/sources.md#p29) [P01](../references/sources.md#p01) [P36](../references/sources.md#p36) | 이력·정책·미래 상태를 고려한 선택 | 원문은 학습형 | 지시어·대화·미래 비용 자체의 신규성 반례 |
| 실행 중 승격·재시작 | SWE-Router, TACIT-Switch, Switchyard [P03](../references/sources.md#p03) [P04](../references/sources.md#p04) [R06](../references/sources.md#r06) | 진행 중 강한 모델로 인계 또는 재시작 | learned value/hazard 또는 규칙·judge | 인계·재시작·행동 전 교체는 다른 실험 |
| cache/session 전환 제어 | Weave, vLLM Semantic Router, Prism [R07](../references/sources.md#r07) [R11](../references/sources.md#r11) [D09](../references/sources.md#d09) | 기본 추천과 유지·캐시·전환 비용을 함께 고려 | 규칙 planner와 추천기 학습 조건을 분리 | 가장 직접적인 구현 경쟁자 |
| online bandit·트래픽 예산 | MetaLLM, PILOT, ParetoBandit [P32](../references/sources.md#p32) [P33](../references/sources.md#p33) [P42](../references/sources.md#p42) | 보상 관측으로 모델 선택·예산 배분 갱신 | 통계/representation 학습·feedback·탐색 비용 | ‘학습을 안 한다’의 엄격한 의미에는 부적합 |
| 같은 모델의 effort 조절 | Ares, 제공자 effort/advisor [P41](../references/sources.md#p41) [D02](../references/sources.md#d02) [D07](../references/sources.md#d07) | 모델 유지, reasoning 계산량 또는 상담만 조절 | Ares는 학습형; 수동 규칙 baseline 가능 | 모델 전환이 꼭 필요한지 검증할 경쟁 방식 |
| token-level speculative cascade | Faster Cascades [P31](../references/sources.md#p31) | 토큰별 deferral·초안 검증 | logits와 실행 제어 필요 | black-box API 라우터의 즉시 채택 범위 밖 |
| 동일 모델의 cache-aware 배치 | SGLang [R05](../references/sources.md#r05) | prefix가 있는 worker와 부하 비교 | serving 상태·prefix 추적 | 모델 품질 선택과 다른 계층; 함께 최적화 가능 |

어벤져스 관련 자세한 내용은 [학습 없는 방법](training-free-methods.md), CP/CARGO/Smoothie 등은 [추가 방법론 비교](alternative-routing-methods.md)에 있다. 이 표는 기존 문서를 대체하지 않고 방법 간 위치를 연결한다.

## ‘다른 방법’을 더 찾을 때의 인접 영역

survey/inventory에서는 reward distillation/Zooter, EmbedLLM, test-time compute/BEST-Route, Router-R1 같은 RL, 안전성·멀티모달 라우팅, LoRA/adapter 선택·상주 캐시도 확인했다. **이번에는 inventory 수준의 범위 점검**이며 모두 원문 방법을 깊게 읽었다고 하지 않는다. LoRA residency의 cache는 provider prompt-prefix cache와 다르다. 프로젝트가 self-hosted token/adapter 제어로 넓어질 때 이 분기를 우선 추가 조사한다. [P21](../references/sources.md#p21), [P22](../references/sources.md#p22), [R10](../references/sources.md#r10)

## 평가 자료에서 배울 점

| 자료 | 활용할 부분 | 그대로 옮기면 안 되는 것 |
|---|---|---|
| RouterBench [P25](../references/sources.md#p25) | 단순 기준선·정적 선택 평가 | 고정 출력 replay를 실제 agent 전환 효과로 해석 |
| LLMRouterBench [P17](../references/sources.md#p17) | 모델 풀·방법 차이·task type·recall 진단 | 당시 모델·근사 지연의 현재 제품 일반화 |
| ORBIT [R10](../references/sources.md#r10) | 공통 interface·budget sweep·방법 비교 | 정규화 비용을 실제 달러로 해석 |
| TwinRouterBench [P02](../references/sources.md#p02) | static/live 분리·cache 포함 episode 회계 | 특정 학습형 정책의 성과를 무학습 성과로 재사용 |
| RouteJudge [P20](../references/sources.md#p20) | 같은 조건에서 router 추천·사용자 선호 수집 | 일부 모델만 비교한 feedback을 전 모델 정답표로 사용 |
| ContinuityBench [P18](../references/sources.md#p18) | failover 때 이력 보존의 영향 | latest-user-only라는 인위적 baseline을 업계 전체 관행으로 단정 |
| Task-type gap [P19](../references/sources.md#p19) | 유형별 단순 lookup·한국어 포함·반복 변동성 | holdout 없는 작은 번역 실험의 성능 일반화 |

## 지금 우선 읽을 자료

1. 신규성 판단: **DialRouter → Pandora → RouteProfile → Otari → vLLM protection → Weave planner**. 우리가 생각하기 쉬운 구성이 어디까지 이미 존재하는지 확인한다.
2. 무학습 초기 선택: **Switchyard Stage → 수동 semantic/profile → kNN utility/Avengers-Pro**. 성능 memory를 허용하는지에 따라 갈린다.
3. 실험 설계: **TwinRouterBench + RouterBench/ORBIT의 강한 단순 기준선**. 서로 평가 범위가 다르므로 한 점수표로 합치지 않는다.

다음: [신규성·개발 타당성 판단](novelty-and-feasibility.md) · [구현 재사용 계획](../design/implementation-reuse-plan.md)
