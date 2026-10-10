# 출처 목록과 확인 범위

확인일: **2026-09-06** · [문서 안내](../README.md)

원문 링크와 조사 범위의 기준 문서다. 논문 실험은 저자의 결과이고, 제품·README 성능 주장은 제공자의 결과다. 이번 조사에서 유료 API나 외부 라우터를 직접 실행해 재현하지 않았다. 논문·코드를 열어 방법과 해당 부분을 확인한 것이며 모든 저장소의 전체 감사를 수행한 것은 아니다.

## 논문

### P01

**MTRouter: Cost-Aware Multi-Turn LLM Routing with History-Model Joint Embeddings** · 2026-04

- [논문 정보](https://arxiv.org/abs/2604.23530), [확인한 HTML v1](https://arxiv.org/html/2604.23530v1), [저자가 제시한 코드](https://github.com/ZhangYiqun018/MTRouter).
- 확인: 단계별 이력·모델 표현, 비용 정의, 전환 분석, prompt caching/switching overhead 한계.
- 분류: 학습형 에이전트 라우터. 코드 실행·논문 수치 재현은 하지 않음.

### P02

**TwinRouterBench: Fast Static and Live Dynamic Evaluation for Realistic Agentic LLM Routing** · 2026-05

- [논문 정보](https://arxiv.org/abs/2605.18859), [확인한 HTML v1](https://arxiv.org/html/2605.18859v1), [저자가 제시한 코드](https://github.com/CommonstackAI/TwinRouterBench).
- 확인: 정적·동적 평가 구분, 4범주 토큰 비용, 캐시 모델, 100문제 실제 실행 결과 표 3, 실패 패널티.
- 한계: 동적 결과는 특정 모델 풀·100문제. 학습된 정책의 절감률을 무학습의 증거로 사용하지 않음.

### P03

**SWE-Router: Routing in Multi-turn Agentic Software Engineering Tasks** · 2026-06-30 제출

- [논문 정보](https://arxiv.org/abs/2607.00053), [확인한 HTML v1](https://arxiv.org/html/2607.00053v1).
- 확인: 저가 모델의 탐색 prefix로 판단하는 구조, value function, 강한 모델의 원래 질문에서 재시작.
- 한계: 학습된 판단 함수. 기존 상태 인계와 재시작을 혼동하지 않음.

### P04

**TACIT-Switch: Cost-Aware Model Escalation for LLM Agents from Censored Supervision** · 2026-08-28

- [논문 정보](https://arxiv.org/abs/2608.27911), [확인한 HTML v1](https://arxiv.org/html/2608.27911v1).
- 확인: 누적 위험에 따른 한 번의 영구 handoff, 학습용 teacher 관측, 비용 proxy와 limitations.
- 한계: 최근 preprint. 모델 규모로 가중한 호출 수는 실제 캐시 포함 API 청구액이 아님.

### P05

**Eagle: Efficient Training-Free Router for Multi-LLM Inference** · 2024-09

- [논문 정보](https://arxiv.org/abs/2409.15518), [확인한 HTML](https://arxiv.org/html/2409.15518).
- 확인: global/local Elo, 과거 질의 임베딩·모델 비교 피드백을 이용한 순위.
- 분류: 별도 신경망 학습 없음, 과거 데이터·순위 갱신 의존. 논문 원문 확인; 코드 실행 없음.

### P06

**SkewRoute: Training-Free LLM Routing for Knowledge Graph Retrieval-Augmented Generation via Score Skewness of Retrieved Context** · 2025-05 / Findings of EMNLP 2025

- [논문 정보](https://arxiv.org/abs/2505.23841), [확인한 HTML](https://arxiv.org/html/2505.23841), [학회판](https://aclanthology.org/2025.findings-emnlp.606/), [저자가 제시한 코드](https://github.com/hrwang00/SkewRoute).
- 확인: 검색 점수 분포, 누적합·entropy·Gini 기반 규칙과 임계값.
- 한계: KG-RAG 검색 점수라는 특수 신호를 사용. 일반 에이전트 도구 루프에 대한 성능 보장 아님.

### P07

**Efficient Training-Free Online Routing for High-Volume Multi-LLM Serving (PORT)** · 2025-09

- [논문 정보](https://arxiv.org/abs/2509.02718), [확인한 HTML](https://arxiv.org/html/2509.02718), [저자가 제시한 코드](https://github.com/fzwark/PORT).
- 확인: 과거 데이터의 ANN 기반 효용·비용 추정, 초기 질의 표본에서 한 번 수행하는 최적화.
- 한계: data-free·optimization-free가 아님. 트래픽 예산 배분과 단일 에이전트의 캐시 전환은 다른 문제.

### P08

**VDAR-Router: Adaptive LLMs Routing via Verbalized Query Difficulty Analysis Retrieval** · 2026-07

- [논문 정보](https://arxiv.org/abs/2607.18098), [확인한 HTML](https://arxiv.org/html/2607.18098), [저자가 제시한 코드](https://github.com/NYCU-NLP-Lab/VDAR-Router).
- 확인: Difficulty Analyst, 임베딩 검색, 과거 성능·선호도와 가격 기반 선택.
- 한계: 추가 가중치 학습은 없지만 과거 데이터 구축·LLM 분석·임베딩 비용이 있음. 코드 실행 없음.

### P09

**The Avengers: A Simple Recipe for Uniting Smaller Language Models to Challenge Proprietary Giants** · 최초 제출 2025-05-26, 확인 버전 v3 (2025-06-18)

- [논문 정보](https://arxiv.org/abs/2505.19797), [확인한 HTML v3](https://arxiv.org/html/2505.19797v3).
- 확인: 초록·버전 이력·3절 The Avengers. 오프라인 질의 임베딩·클러스터링·모델별 클러스터 성능표와 온라인 최근접 클러스터 선택, 반복 샘플링·투표.
- 분류: 신경망 라우터 추가 학습 대신 validation 데이터에 의한 clustering/profiling. 데이터·보정 없는 방법이 아니며, 전체 방식의 투표 생성 비용을 라우터 선택 비용과 구분한다.
- 한계: 논문의 모델·벤치마크 성과를 현재 에이전트의 캐시 포함 비용 절감으로 일반화하지 않음. 결과 재현 없음.

### P10

**Beyond GPT-5: Making LLMs Cheaper and Better via Performance-Efficiency Optimized Routing (Avengers-Pro)** · 최초 제출 2025-08-18, 확인 버전 v2 (2025-10-22), DAI 2025

- [논문 정보](https://arxiv.org/abs/2508.12631), [확인한 HTML v2](https://arxiv.org/html/2508.12631v2), [저자 코드](https://github.com/ZhangYiqun018/AvengersPro).
- 확인: 3절 성능·비용 정규화 점수 및 top-p 클러스터 합산, 4.1절 labeled 데이터의 70/30 분리와 구현 조건. Qwen3-Embedding-8B, k=60, p=4가 실험 설정에 명시됨.
- 코드 확인 ref: `dfb60b156c4b6011b89edfc8516aa16d8e4f4d38` (`master`, 2025-12-11).
- [README](https://github.com/ZhangYiqun018/AvengersPro/blob/dfb60b156c4b6011b89edfc8516aa16d8e4f4d38/README.md), [simple_cluster_router.py](https://github.com/ZhangYiqun018/AvengersPro/blob/dfb60b156c4b6011b89edfc8516aa16d8e4f4d38/simple_cluster_router.py), [balance_cluster_router.py](https://github.com/ZhangYiqun018/AvengersPro/blob/dfb60b156c4b6011b89edfc8516aa16d8e4f4d38/balance_cluster_router.py): query/records/usages 자료형, train/test 분리, KMeans `fit_predict`, 순위표 구성과 온라인 조회의 핵심 구간.
- 한계: 논문·README의 기본 파라미터와 표현이 완전히 일치하지 않으므로 재현 시 버전·설정을 고정해야 한다. 6개 벤치마크에 τ²-bench가 포함되지만, 대화 지시어 해석이나 단계별 캐시 전환 최적성의 증거는 아니다. 설치·실행·결과 재현 없음.

### P11

**Rethinking Predictive Modeling for LLM Routing: When Simple kNN Beats Complex Learned Routers** · 최초 2025-05-19, 확인 v2 (2026-05-14)

- [논문 정보](https://arxiv.org/abs/2505.12601), [HTML v2](https://arxiv.org/html/2505.12601v2).
- 확인: 3절 selection과 utility prediction 구분, 5절 kNN 이웃의 성능·비용 평균, 6절 결과·분포 이동·시간 측정 범위.
- 분류: 비모수 검색. 추가 신경망 학습은 없지만 후보별 성능·비용이 기록된 support set 필요.
- 한계: 제목을 ‘모든 학습형보다 항상 우수’로 읽지 않는다. 텍스트 평균 AUC에서 kNN(k=100) 52.68, Linear 53.14로 보고됨. 에이전트 캐시 전환 실험이 아님. 아래 R09는 참고용 kNN 구현이지 이 논문의 공식 재현 코드로 확인한 것이 아니다.

### P12

**CP-Router: An Uncertainty-Aware Router Between LLM and LRM** · arXiv v1 2025-05-26 / AAAI 2026

- [논문 정보](https://arxiv.org/abs/2505.19970), [확인한 HTML v1](https://arxiv.org/html/2505.19970v1), [학회판 정보](https://ojs.aaai.org/index.php/AAAI/article/view/40589).
- 확인: 2절 labeled calibration·확률/선택지 logits·prediction set, 3절 singleton 기반 선택·FBE, 4–5절 비용 지표와 open-ended 확장.
- 분류: 가중치 학습 없음, 정답이 있는 calibration 필요. 확인한 v1은 test 입력의 prediction-set 분포로 FBE를 최적화해 α를 고른다고 명시한다. 우리 실험의 독립 개발/보정/평가 분리와 구분한다.
- 한계: prediction-set marginal coverage를 선택된 저가 모델의 조건부 정확도나 에이전트 성공률 보장으로 확대하지 않는다. 출력 토큰 지표는 캐시 포함 API 총비용과 다름. 학회판 전문·코드 재현은 하지 않음.

### P13

**Routing Without Training: Controllable-Ratio LLM Offloading via Reliability Gating (CARGO)** · 확인 v1

- [논문 정보](https://arxiv.org/abs/2607.20481), [확인한 HTML v1](https://arxiv.org/html/2607.20481v1).
- 날짜 주의: 확인한 arXiv 페이지는 제출일을 **2026-05-30**으로 표시하지만 식별자는 `2607.20481`이다. 번호에서 제출 월을 추정하거나 불일치를 임의로 보정하지 않는다.
- 확인: 3절 system-prompt 변형·deterministic sampling·Beta 기반 일치도 추정·credible-interval early stop·warmup λ 조정·확률적 offloading. 4절 cloud 호출 비율 및 cloud-token 예산 실험.
- 분류: 별도 신경망 라우터 학습 없음. 라벨 없는 warmup 질의와 정책 파라미터 보정은 존재하며, 온라인에 작은 모델을 반복 생성한다.
- 한계: 응답 일치도는 정답 확률이 아니다. cloud 사용률·cloud 토큰 절감을 로컬 반복 생성과 캐시를 포함한 총비용 절감으로 해석하지 않는다. 확인한 본문에서 공식 코드 링크는 찾지 못했으며 실행 없음.

### P14

**Smoothie: Label Free Language Model Routing** · 2024-12-06 v1 / NeurIPS 2024

- [논문 정보](https://arxiv.org/abs/2412.04692), [HTML v1](https://arxiv.org/html/2412.04692v1), [저자 저장소](https://github.com/HazyResearch/smoothie).
- 확인: 4절 출력 임베딩·triplet 기반 closed-form 품질 추정, 본문의 Improving efficiency에서 Smoothie-Train 변형, 독립 오류·가격 미고려 한계.
- 코드 ref: `8f8bd153058d3551955cf9fd62a501ee1e6a324f` (2024-12-10), MIT 표기.
- [README](https://github.com/HazyResearch/smoothie/blob/8f8bd153058d3551955cf9fd62a501ee1e6a324f/README.md), [src/model.py](https://github.com/HazyResearch/smoothie/blob/8f8bd153058d3551955cf9fd62a501ee1e6a324f/src/model.py), [src/run_smoothie.py](https://github.com/HazyResearch/smoothie/blob/8f8bd153058d3551955cf9fd62a501ee1e6a324f/src/run_smoothie.py): 수식 계산과 `train_time`/`test_time` 경로. runner는 사전 생성된 test 응답을 불러오는 평가 스크립트이며 배포용 live API 라우터가 아님.
- 분류: 라벨·SGD 없음, 통계적 품질 가중치 추정은 존재. 기본형은 test 질의마다 여러 모델 생성 필요; Train 변형은 별도 질의의 사전 생성 기록으로 선택 가능. 비용 최적화·에이전트 전환은 별도. 코드 실행 없음.

### P15

**Learning Agent Routing From Early Experience (BoundaryRouter)** · 2026-05-08 v1

- [논문 정보](https://arxiv.org/abs/2605.07180), [확인한 HTML v1](https://arxiv.org/html/2605.07180v1).
- 확인: 3절 seed 질의의 LLM/agent 출력·지연 memory, hybrid retrieval, 기존 LLM의 rubric 기반 선택; Reproducibility Statement.
- 분류: 추가 가중치 학습·gold routing label 없이 동작. 양쪽 시스템을 미리 실행한 경험 데이터와 온라인 routing LLM 호출은 필요.
- 한계: 직접 LLM 답변 대 전체 agent의 **실행 방식 선택**. 에이전트 안의 모델별 tool-step 전환과 다르다. benchmark 평가용 정답/route label과 라우터에 제공되는 무라벨 memory를 구분한다. 확인 본문은 코드·데이터를 publication 시 공개하겠다고 설명하며, 공식 구현은 이번 조사에서 확인하지 못함.

### P16

**AutoMix: Automatically Mixing Language Models** · 최초 2023-10-19, 확인 v5 (2025-01-19), NeurIPS 2024

- [논문 정보](https://arxiv.org/abs/2310.12963), [확인한 HTML v5](https://arxiv.org/html/2310.12963v5), [저자가 제시한 코드](https://github.com/automix-llm/automix).
- 확인: 4절 작은 모델 생성→같은 모델의 few-shot entailment 검증→threshold/POMDP 선택. 검증은 여러 샘플의 판정 비율 사용.
- 경계: verifier 자체를 추가 학습하지 않지만 POMDP의 관측 분포는 성능 정보가 있는 train set에서 KDE로 추정하고 계획 solver를 사용한다. 전체를 data-free·정책 fitting 없는 방법으로 분류하지 않는다.
- 한계: context-grounded QA의 자기 검증과 실행 가능한 테스트를 구분한다. 작은 모델 답변·복수 검증·상위 모델 호출 비용을 모두 계산해야 함. 코드 링크 확인, 실행·코드 감사 없음.

### P17

**LLMRouterBench: A Massive Benchmark and Unified Framework for LLM Routing** · 2026-01-12 v1

- [원문](https://arxiv.org/html/2601.07206v1), [저자 저장소](https://github.com/ynulihao/LLMRouterBench).
- 확인: 3절 모델 풀·평가 단위·cost/Pareto 지표, 4절 방법 간 비교·model recall·모델 풀 규모, limitations.
- 초록의 ‘400K+ instances’를 고유 질문 수로 옮기지 않는다. 본문 표 2는 23,945 prompts와 391,645 모델 실행 instances를 구분한다. 비교 실험은 여러 방식이 고정 모델을 안정적으로 이기지 못함을 보이며, 모든 환경의 우열을 정하지는 않는다.
- 한계: latency는 token 사용량과 serving 통계로 근사. SWE-Bench/τ²-bench 포함 자체가 도구 단계별 동적 전환·실측 캐시 효과 평가를 뜻하지 않는다. 당시 OpenRouter의 다른 모델 풀·설정을 현재 제품의 성능으로 일반화하지 않는다.

### P18

**ContinuityBench: A Benchmark and Systems Study of Stateful Failover in Multi-Provider LLM Routing** · 2026-07-17 v1

- [원문](https://arxiv.org/html/2607.15899v1), [저자 저장소](https://github.com/Vishal-sys-code/continuity-bench).
- 확인: 3절 마지막 질문만 전달 대 전체 messages 전달, 4절 150개 합성 대화·고정 fault injection·anchor recall, 7절 limitations.
- 용도: 상태 전달·장애 주입 평가의 선행연구. ‘맥락 보존 failover’를 새 아이디어라고 주장하지 않음.
- 한계: 저자의 stateless baseline을 현재 모든 gateway의 기본 동작으로 간주하지 않는다. text/batch·한 번의 마지막-turn failure·제한된 제공자 조합이며 streaming·tool side effect·복수 failover를 검증한 것이 아니다. 논문의 production Rust 지연 주장과 Python 실험 결과도 분리한다.

### P19

**Most of the LLM Routing Gap Is Task Type** · 최초 2026-08-24, 확인 v2 (2026-08-25)

- [원문](https://arxiv.org/html/2608.23023v2).
- 확인: 2절 14모델×294질의, 한국어·영어·힌디어 병렬 구성·2회 실행, 7절 같은 표에서 선택/평가한 한계, 8절 결론.
- 용도: 한국어·작업 유형별 모델 선택과 반복 변동성의 관련 사례. ‘한국어 라우팅 자체가 미연구’라는 주장에 반례.
- 한계: holdout 없음, 작업 유형과 데이터 출처가 결합됨, 대부분 번역 질의, 일부 원시 artifact 미공개. 이 실험의 절감률을 일반화하거나 ‘라우터는 불필요’의 증거로 사용하지 않는다. 모델-질의 쌍의 flip 비율과 질의별 성능 차이의 분모도 다르다.

### P20

**RouteJudge: An Open Platform for Reproducible and Preference-Aware LLM Routing** · 최초 2026-06-17, 확인 v2 (2026-06-19)

- [원문](https://arxiv.org/html/2606.18774v2), [ORBIT](https://github.com/LAMDA-Model-Reuse/ORBIT).
- 확인: 3절 동일 모델 풀·예산·맥락의 router 추천, 모델 투표 상위 두 응답의 익명 비교, router별 점수 귀속·participation, ORBIT와의 역할 분리.
- 용도: 공통 protocol·온라인 사용자 선호·router별 평가 기록이 이미 연구됐다는 근거.
- 한계: 선택된 두 모델 밖을 고른 router에는 결과가 관측되지 않는다. 참여율·노출 편향과 실제 episode 비용을 별도 고려해야 한다. 모델 응답 비교를 에이전트 단계 전환의 인과적 평가로 대체하지 않는다.

### P21

**Dynamic Model Routing and Cascading for Efficient LLM Inference: A Survey** · 확인 v3, 2026-08-30 개정

- [원문 v3](https://arxiv.org/html/2603.04445v3), [메타데이터](https://arxiv.org/abs/2603.04445), [저자 inventory](https://github.com/ymoslem/awesome-llm-routing-cascading).
- 확인: 분류 체계, 1.4절, 9.3–10절의 비교 한계·구성 방식; v1의 방법별 설명도 참고. 난이도·선호·클러스터·RL/bandit·불확실성·cascade를 결정 시점/입력 신호/계산 방식으로 교차 분류한다.
- 용도: 조사 범위의 기준. 표에 실린 방법을 모두 직접 재현했다는 뜻이 아니다. 본문도 benchmark 이름이 같아도 모델 풀·비용·평가 단위가 다를 수 있다고 경고한다.
- 날짜: metadata의 최초 제출은 2026-02-23으로 표시됨. `2603` 번호에서 월을 추정하지 않는다.

### P22

**Doing More with Less — LLM routing survey** · 최초 2025-02-01

- [확인한 본문 v1](https://arxiv.org/html/2502.00409v1), [최신 metadata v3](https://arxiv.org/abs/2502.00409), [보조 survey inventory](https://github.com/wwdda/Awesome-LLM-Routing).
- 확인: v1의 범위·분류·데이터 자원·similarity/contrastive 설명, v3 초록/버전 이력. 버전별 제목이 달라 최신 본문까지 읽은 것으로 표시하지 않는다.
- 용도: pre/post-generation, 학습 조건, 단계·토큰·신뢰성 등 빠진 연구 축을 점검하는 보조 지도. 독립 성능 근거가 아니다.

### P23

**FrugalGPT: How to Use Large Language Models While Reducing Cost and Improving Performance** · 2023-05-09 v1

- [원문](https://arxiv.org/html/2305.05176v1).
- 확인: 3–5절 신뢰도 회귀 scorer 학습, 모델 순서·threshold 최적화, 데이터와 비용. 사례에서는 DistilBERT와 labeled representative examples를 사용한다.
- 한계: foundational cascade이지만 무학습·무보정은 아님. 단일 응답 QA/분류 실험이며 agent tool-step·캐시 전환 검증이 아니다.

### P24

**RouteLLM: Learning to Route LLMs from Preference Data** · 최초 2024-06-26, 확인 v4 (2025-02-23)

- [원문](https://arxiv.org/html/2406.18665v4).
- 확인: 4.2–5절. Similarity-weighted ranking은 과거 선호를 질의 유사도로 가중하고 **추론 시 Bradley–Terry 계수를 최적화**한다. MF/BERT/causal LLM 변형은 파라미터 학습을 한다.
- 한계: RouteLLM 전체를 추가 neural training 필수라고 분류하지 않는다. SW 변형도 preference-data-free·optimization-free는 아니다. 새 모델 쌍 전이는 초기 학습·데이터가 없다는 뜻이 아니다.

### P25

**RouterBench: A Benchmark for Multi-LLM Routing System** · 최초 2024-03-18, 확인 v2 (2024-03-28)

- [원문](https://arxiv.org/html/2403.12031v2), [저자 저장소](https://github.com/withmartian/routerbench).
- 확인: 4–6절, README. 4.3절은 RAG 확장을 포함해 405,467 samples·11 models·8 datasets·64 tasks를 보고한다.
- 용도: 정적 출력 재사용 평가와 단순 KNN/MLP·Zero Router 비교. 단순 예측기가 항상 혼합 기준선을 유의하게 이기지는 않는다.
- 한계: 지연·처리량 평가 부재, route-dependent 도구 실행 이력·실제 캐시 미반영. 표본 수를 고유 사용자 질의 수로 재명명하지 않는다.

### P26

**Hybrid LLM: Cost-Efficient and Quality-Aware Query Routing** · 2024-04-22 v1

- [원문](https://arxiv.org/html/2404.14618v1).
- 확인: 3–4절 DeBERTa router, MixInstruct 10k, 반복 응답·BART 기반 확률 label, 학습·validation threshold 선택.
- 한계: 학습형 비교군. 주된 비용 척도인 작은 모델 배정 비율을 캐시 포함 실제 총 청구액으로 해석하지 않는다.

### P27

**RouterDC: Query-Based Router by Dual Contrastive Learning for Assembling Large Language Models** · 2024-09-30 v1

- [원문](https://arxiv.org/html/2409.19886v1).
- 확인: 3–4.1절 mDeBERTa와 모델 embedding의 query–model/query–query contrastive learning, 정답·응답 평가·선택지 확률 supervision.
- 한계: 학습형 representation 비교군. ID/OOD 답변 성능이 live agent 성공·캐시 경제성을 입증하지 않는다.

### P28

**GraphRouter: A Graph-based Router for LLM Selections** · 최초 2024-10-04, 확인 v2 (2025-03-17)

- [원문](https://arxiv.org/html/2410.03834v2).
- 확인: 2.3–4.2절 task/query/model GNN 학습, capability/price description, 새 모델의 **80개 auxiliary query 실행 결과**를 이용한 graph 연결.
- 한계: 새 모델을 재학습 없이 연결하는 것과 초기 무학습·새 모델 profiling 불필요는 다르다. 비용 추정은 캐시 연속성 모델이 아니다.

### P29

**Arch-Router — preference-aligned conversational routing** · 2025-06-19 v1

- [원문](https://arxiv.org/html/2506.16655v1).
- 확인: 3–6절과 Appendix C. 1.5B router에 43k SFT; policy 설명·대화를 받아 policy를 선택하고 사용자가 지정한 policy→model 매핑을 따른다.
- 중요한 관련성: Appendix C가 생략된 후속 질문·지시어를 포함한다. conversation-aware routing 자체를 미연구라고 할 수 없다.
- 한계: policy-match accuracy는 실제 모델의 cost–quality 적합도와 다르다. frozen checkpoint 이용은 사용자 측 추가 학습이 없지만 알고리즘은 학습형이다.

### P30

**ICL-Router** · 최초 2025-10-10, 확인 v3 (2025-11-14)

- [원문](https://arxiv.org/html/2510.09719v3).
- 확인: 3–4절 frozen Qwen3 embedding, projector/router 학습, in-context performance vectors, 500개 대표 질의의 모델별 profiling.
- 한계: in-context 입력·새 모델 무재학습을 전체 무학습으로 읽지 않는다. 새 후보의 평가 실행 비용이 존재하며 도구 루프·캐시 비용은 별도다.

### P31

**Faster Cascades via Speculative Decoding** · 최초 2024-05-29, 확인 v2 (2024-10-21)

- [원문](https://arxiv.org/html/2405.19261v2).
- 확인: 3–4절, 6절, Appendix E/F의 token confidence/deferral·speculative verification. 별도 neural router fitting 없는 규칙이 있지만 token distributions와 draft/verify 실행 제어가 필요하다.
- 한계: black-box API proxy에 바로 붙이는 방법이 아님. 기반 모델 학습 조건·wall-clock/rejection-rate 지표가 실험별로 다르다. 일반 speculative decoding의 분포 보존을 임의 cascade에 확대하지 않는다.

### P32

**MetaLLM** · 최초 2024-07-15, 확인 v3 (2025-04-22)

- [원문](https://arxiv.org/html/2407.10834v3).
- 확인: 3.3–4.1절 Sentence-BERT 문맥과 arm별 선형 reward predictor, labeled 초기화·선택적 온라인 갱신·비용 가중치 validation.
- 한계: zero-shot은 대상 LLM의 분류 방식이지 무학습 router라는 뜻이 아니다. 정답 feedback을 실제 배포에서 얻는 방법이 따로 필요하다.

### P33

**PILOT: Adaptive LLM Routing under Budget Constraints** · 2025-08-28 v1 / Findings of EMNLP 2025

- [원문](https://arxiv.org/html/2508.21141v1), [학회판](https://aclanthology.org/2025.findings-emnlp.1301/).
- 확인: 2–3절과 limitations. offline preference embedding 학습, bandit arm 갱신, budget allocation; RouterBench 분할로 online traffic을 모사한다.
- 한계: single-turn이며 budget 제약이 deployment에 적용되고 learning 비용까지 포괄하지 않는다. 온라인 feedback·학습을 제외하는 사용자 제약과는 맞지 않는다.

### P34

**A Unified Approach to Routing and Cascading for LLMs — Cascade Routing** · 최초 2024-10-14, 확인 v3 (2025-05-22)

- [원문](https://arxiv.org/html/2410.10347v3).
- 확인: 2–5절·Appendix D, 응답 관측 후 다음 모델 선택, quality/cost estimator, 회귀 기반 구현.
- 한계: RouterBench에서 true score에 noise를 더한 estimator 실험, SWE-Bench에서 기존 시스템 로그·ground-truth tests·완료 시간 비용을 사용하는 조건을 구분한다. 실용 verifier의 live agent 전환·API 캐시 절감 증거가 아니다.
- 용도: 사전 선택+검증+승격의 조합 자체가 새로운 알고리즘이라는 주장에 직접적인 반례.

### P35

**HuggingGPT: Solving AI Tasks with ChatGPT and its Friends in Hugging Face** · 최초 2023-03-30, 확인 v4 (2023-12-03)

- [원문](https://arxiv.org/html/2303.17580v4).
- 확인: 초록·1–3.2절, 기존 LLM의 계획·자연어 model description 선택·실행·종합. 설명을 넣어 expert 후보를 확장한다.
- 한계: 추가 router training 없는 LLM controller의 초기 사례지만, 서로 대체 가능한 LLM의 비용 최적화·캐시 인식 에이전트 전환과 같은 문제는 아니다.

### P36

**From Myopic Selection to Long-Horizon Awareness: Sequential LLM Routing for Multi-Turn Dialogue — DialRouter** · 2026-04-14 v1

- [원문](https://arxiv.org/html/2604.12385v1).
- 확인: 4절 user simulation·MCTS trajectory·behavior cloning, state retrieval·학습 gate; Appendix D의 trainable 44.42M parameters; 5절 식 8–9·각주 3의 캐시 비용.
- 중요: 장기 대화 라우팅과 캐시 비용은 이미 결합되어 있다. 본문은 모델을 바꿀 때 전체 캐시가 무효화되는 **worst-case 가정**을 명시한다.
- 한계: 실제 모델별 prefix/TTL/A→B→A 복귀 캐시·도구 루프의 실측 billing을 그대로 다룬 것은 아니다. 본문 방법명을 SeqRouter가 아닌 DialRouter로 사용한다.

### P37

**Pandora’s AI Model Routing Box** · 확인 v2

- [원문](https://arxiv.org/html/2608.20316v2).
- 확인: 2.1절 cheap kNN와 fine-tuned costly estimator, train/calibration/test 분리, 4절 Gaussian signal·보정 회귀·reservation price 기반 정보 가치.
- 중요: 판정 비용까지 포함해 ‘어떤 추가 판정을 살 것인가’를 결정하는 선행연구. 수학의 일부 reasoning이나 RAG retrieval을 판정 입력으로 사용한다.
- 한계: 추가 estimator 학습·보정이 있어 그대로는 무학습 후보가 아님. utility proxy·가격 정의도 실제 provider 청구와 구분한다. 정보 가치 일반 원리 자체는 신규성이 없다.

### P38

**Routing with Generated Data — CASCAL** · 2026-01-14 v1

- [원문](https://arxiv.org/html/2601.09692v1), [저자 저장소](https://github.com/tianyiniu/RoutingGenData).
- 확인: 3절·Appendix A. 생성 질의를 전체 모델 풀에 실행하고 normalized log-prob·응답 consensus로 skill별 KMeans centroid와 모델 순위를 구성한다. Top-1과 Top-3 변형을 구분한다.
- 분류: gold label·추가 neural router 학습 없음, 데이터 생성·후보 실행·log-prob 접근·clustering fitting 필요. 논문이 이 구축을 training이라고 부르는 점도 기록한다.
- 한계: Top-3 ensemble과 Top-1 선택의 온라인 비용이 다르며, 일반 도구 단계·캐시 최적화 방식이 아니다.

### P39

**RouteProfile** · 확인 v2

- [원문](https://arxiv.org/html/2605.00180v2), [저자 저장소](https://github.com/ulab-uiuc/RouteProfile).
- 확인: model card·family·공개 benchmark 점수·domain graph, 5.2/5.5절 training-free SimRouter 및 별도 trained MLP/GraphRouter 비교.
- 분류: SimRouter는 target query-response-reward 없이 profile 유사도로 시작 가능. frozen aggregation/LLM text propagation은 가능하지만 기존 embedding·공개 평가 신호·준비 비용은 존재한다.
- 한계: ‘새 모델만 재학습 없음’인 학습형 실험과 전체 training-free 실험을 분리한다. ORBIT의 ProfileRouter 표기와 이 논문의 현재 RouteProfile 명칭을 혼동하지 않는다.

### P40

**InferenceDynamics** · 2025-05-22 v1

- [원문](https://arxiv.org/html/2505.16303v1).
- 확인: 3절 capability/knowledge 추출·중요도 순위·모델별 반복 평가, 4.2절 유사 항목 통합·빈도 filtering.
- 분류: 별도 router weight 학습 없음. 3.3절은 정답 y와 비교한 labeled benchmark profiling을 명시하므로 label-free가 아니다.
- 한계: 새 모델도 benchmark 실행이 필요하다. evolving agent state는 이 실험으로 검증되지 않았다.

### P41

**Ares — adaptive reasoning effort for agents** · 2026-03-09 v1

- [원문](https://arxiv.org/html/2603.07915v1).
- 확인: interaction history→같은 모델의 reasoning effort, 성공 trajectory 기반 최소 effort label, Qwen3-1.7B router fine-tuning·SFT/RL, TAU-Bench/BrowseComp-Plus/WebArena 평가.
- 용도: 다른 모델로 전환하지 않고도 비용을 조절하는 필수 대안. 학습형이므로 그대로의 구현 채택과 개념 baseline을 구분한다.
- 한계: reasoning-token 절감이 현재 API의 총비용·캐시 보존을 그대로 보장하지 않는다. 설정 변경의 캐시 영향은 제공자별 확인 대상이다.

### P42

**ParetoBandit** · 확인 v2

- [원문](https://arxiv.org/html/2604.00136v2), [저자 저장소](https://github.com/ParetoBandit/ParetoBandit).
- 확인: 3절 LinUCB 회귀·primal-dual budget pacer·forgetting·warm start, offline judge reward–cost matrix 실험.
- 분류: 추가 neural training 없음, **온라인 통계 학습·reward feedback·PCA fitting·튜닝**은 있음.
- 한계: unknown horizon은 요청 스트림 길이이며 현재 agent의 남은 도구 단계와 다르다. 3.2절은 기존 BwK 이론 보장이 그대로 적용되지 않는다고 설명한다. 실험적 budget compliance를 보편 이론 보장으로 쓰지 않는다.

### P43

**Ski Rental with Distributional Predictions of Unknown Quality** · 2026-02-24 v1

- [원문](https://arxiv.org/html/2602.21104v1).
- 확인: 2.1절 rent/buy 가정, unknown-quality distributional advice의 robustness/consistency.
- 용도: 미지의 잔여 길이·전환비용 손익분기의 인접 이론. ‘예측이 불확실하니 보수적으로 전환’ 원리 자체의 신규성을 주장하지 않기 위한 경계.
- 한계: 여러 모델·TTL·비대칭 캐시 비용·선택에 따라 달라지는 종료 시점은 기본 ski-rental과 다르다. 이론 보장을 LLM 정책으로 옮기려면 환원 조건 또는 새 증명이 필요하다.

## 공개 구현

아래 고정 ref는 확인한 파일을 다시 찾기 위한 것이다. 최신 `main`과 다를 수 있다. 설치 버전 채택을 의미하지 않는다.

### R01

**BlockRunAI/router-core** · TypeScript · [저장소](https://github.com/BlockRunAI/router-core)

- 확인 ref: `814fd3b6bc7880a84bbe030bd08c98e8670031e7`.
- [README](https://github.com/BlockRunAI/router-core/blob/814fd3b6bc7880a84bbe030bd08c98e8670031e7/README.md): 공개 정책 구조와 제품 경계.
- [rules.ts](https://github.com/BlockRunAI/router-core/blob/814fd3b6bc7880a84bbe030bd08c98e8670031e7/rules.ts): 가중 규칙과 tier 분류.
- [portfolio.ts](https://github.com/BlockRunAI/router-core/blob/814fd3b6bc7880a84bbe030bd08c98e8670031e7/portfolio.ts): 기능 검사·수동 affinity·후보 점수·비용 계산.
- [selector.ts](https://github.com/BlockRunAI/router-core/blob/814fd3b6bc7880a84bbe030bd08c98e8670031e7/selector.ts): 용량 필터와 제품별 결제 비용 계산.
- 검토 한계: 정적 소스 일부 확인. README의 fail-closed 주장과 fallback 분기의 관계는 통합 경로 검증이 필요하다.

### R02

**BlockRunAI/ClawRouter** · TypeScript · [저장소](https://github.com/BlockRunAI/ClawRouter)

- 소스 확인 ref: `29f3cb58a25f587ddb64f5c722e844f6067281dc`. 이후 main 조회에서는 `526572fd4d49fa45934d69a49ed4717ac65483f3`도 확인되어, 문서가 가리키는 ref를 고정했다.
- [session.ts](https://github.com/BlockRunAI/ClawRouter/blob/29f3cb58a25f587ddb64f5c722e844f6067281dc/src/session.ts): 모델 pin·최근 사용·승격·비용 상태.
- [기능 문서](https://github.com/BlockRunAI/ClawRouter/blob/main/docs/features.md): 응답 캐시와 세션 기능 설명.
- 확인: README·세션 저장소·기능 문서. 프록시 통합·결제·캐시·승격 전체 실행은 검증하지 않음.

### R03

**BerriAI/litellm — Complexity Router** · Python · [저장소](https://github.com/BerriAI/litellm)

- 확인 ref: `7d5b6456baaf8be6ac1a60db2b710566b5344adf`.
- [Complexity Router README](https://github.com/BerriAI/litellm/blob/7d5b6456baaf8be6ac1a60db2b710566b5344adf/litellm/router_strategy/complexity_router/README.md).
- [complexity_router.py](https://github.com/BerriAI/litellm/blob/7d5b6456baaf8be6ac1a60db2b710566b5344adf/litellm/router_strategy/complexity_router/complexity_router.py).
- [lowest_cost.py](https://github.com/BerriAI/litellm/blob/7d5b6456baaf8be6ac1a60db2b710566b5344adf/litellm/router_strategy/lowest_cost.py).
- 확인: 수동 heuristic, LLM, heuristic-first, 데이터 기반 heuristic-v2의 구분; 실제 분류 cause·fallback; model group의 비용 기반 deployment 선택.
- 검토 한계: 선택한 경로의 정적 확인이며 전체 라이브러리·배포 설정의 동작을 검증하지 않음.

### R04

**Portkey-AI/gateway — Conditional Routing** · TypeScript · [저장소](https://github.com/Portkey-AI/gateway)

- 확인 ref: `669825cbe89ee51569918b8f78a9db486fd69dd4`.
- [conditionalRouter.ts](https://github.com/Portkey-AI/gateway/blob/669825cbe89ee51569918b8f78a9db486fd69dd4/src/services/conditionalRouter.ts).
- [공식 조건 라우팅 문서](https://portkey.ai/docs/product/ai-gateway/conditional-routing).
- 확인: metadata·params·URL 조건, 연산자, target 선택·default, 다른 전략과의 조합.
- 한계: 의미적 품질 예측기가 아니라 운영자가 정한 조건을 실행하는 엔진.

### R05

**SGLang Model Gateway — Cache-aware policy** · Rust

- [저장소](https://github.com/sgl-project/sglang), [확인한 코드 경로](https://github.com/sgl-project/sglang/blob/main/sgl-model-gateway/src/policies/cache_aware.rs).
- 확인: worker별 근사 prefix 트리·부하 분산 정책 설명과 파일의 정책 구분. ref는 `main`이므로 갱신 가능.
- 한계: 모델 실행 위치 최적화 참고. 이종 모델 품질 라우팅·우리 API 환경에서의 효과는 검증하지 않음.

### R06

**NVIDIA-NeMo/Switchyard** · Rust 코어·Python 바인딩 · [저장소](https://github.com/NVIDIA-NeMo/Switchyard)

- 사용자 제시 자료, 추가 확인일: 2026-09-06. 확인 ref: `9a743e89223a0d5b14011f1226d5b068f730a3b8` (2026-09-05).
- [README](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/README.md): 라이브러리·게이트웨이·데모 서버 구분, 안정성, 벤치마크 산정 조건.
- [Stage 문서](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/docs/routing_algorithms/stage_router_routing.md), [stage.rs](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/crates/libsy/src/algorithms/stage.rs), [점수 계산](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/crates/libsy/src/algorithms/util/stage.rs): 도구 신호·고정 가중치·선택적 judge. 권장 임계값은 저자 보정에서 도출됨.
- [Classifier 문서](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/docs/routing_algorithms/llm_classifier_routing.md), [affinity.rs](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/crates/libsy/src/algorithms/util/affinity.rs): `every_request`·`user_turn`·`new_session`, 세션 식별 필요성.
- 입력 맥락 추가 확인: [llm_class.rs](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/crates/libsy/src/algorithms/llm_class.rs)의 `task_messages`·`trim_messages`·`window_start`·`TaskInput`, [llm_judge.rs](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/crates/libsy/src/algorithms/util/llm_judge.rs)의 별도 judge 요청 구성. 첫 작업+최신 user 기본값, role별 창·도구 쌍 보존 경로.
- [Escalation 문서](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/docs/routing_algorithms/escalation_router_routing.md), [escalation.rs](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/crates/libsy/src/algorithms/escalation.rs): 저가 응답 생성 후 judge, 확인 누적과 strong 고정, 버퍼링 비용.
- [cache_eligibility.rs](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/crates/switchyard-server/src/stats/cache_eligibility.rs): 텍스트 길이·prefix 지문 기반 이론적 재사용 지표. 실제 cache-hit 관측과 구분.
- [LICENSE](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/LICENSE): Apache-2.0.
- 확인 범위: 위 문서와 선택·상태·캐시 통계의 핵심 소스 구간. 설치·컴파일·서버 실행·벤치마크 재현은 하지 않음.
- 통합 추가 확인: [LiteLLM 예제 README](https://github.com/NVIDIA-NeMo/Switchyard/blob/9a743e89223a0d5b14011f1226d5b068f730a3b8/examples/litellm/README.md), `crates/protocol/README.md`, `crates/switchyard-translation/README.md`. 예제는 checkout-only experimental이고 당시 PyPI 0.2.0보다 새 API를 사용한다. Stage/history·Random의 decision-only 경로를 지원하고 내부 `CallModel`/이미 생성한 응답은 거절한다. 원본 tools·sampling·output/provider args의 판단기 전달 범위도 제한됨.

### R07

**weave-os/router** · Go 프록시·ONNX scorer·선택적 Python sidecar · [저장소](https://github.com/weave-os/router)

- 사용자 제시 자료, 추가 확인일: 2026-09-06. 확인 ref: `03319b1d4b14ebb2ac569059c95982e9bea3c63c` (2026-09-05).
- [README](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/README.md), [SEMANTICS](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/docs/SEMANTICS.md): action별 라우팅, hosted/self-hosted 경로, 세션과 사용자 질문의 구분.
- [클러스터 artifact 문서](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/router/cluster/artifacts/README.md), [scorer.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/router/cluster/scorer.go): 사전 생성된 centroid·품질표·모델별 축, 임베딩 호환성. 학습 스크립트는 별도 `router-internal/eval/` 경로로 안내됨.
- [frozen HMM 문서](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/sidecars/hmm/README.md): PCA/HMM/XGBoost artifact와 특정 외부 임베딩 의존. frozen inference와 무학습 알고리즘의 차이.
- [planner/policy.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/router/planner/policy.go), [turnloop.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/proxy/turnloop.go): fresh 후보와 pin의 비용 비교, 캐시 수명·재사용 비율·출력 비용, 실제 호출 경로와 추가 정책 예외.
- [main.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/cmd/router/main.go), [service.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/proxy/service.go): planner 활성화와 `CorrectedEconomics`의 코드 기본값. 관리형 배포의 실제 설정은 미확인.
- [catalog/cost.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/router/catalog/cost.go), [sessionpin/store.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/router/sessionpin/store.go): 제공자별 캐시 토큰 회계와 직전 실제 사용량·모델·제공자 저장.
- 입력 맥락 추가 확인: [translate/features.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/translate/features.go), [translate/conversation_messages.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/translate/conversation_messages.go), [proxy/conversation_messages.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/proxy/conversation_messages.go), 앞의 `service.go`·`scorer.go`. 사용자 텍스트 집계·별도 구조화 이력·기본 cluster 입력의 바이트 기준 tail 절단을 구분함.
- [설정 문서](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/docs/CONFIGURATION.md), [pin_eviction.go](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/internal/proxy/pin_eviction.go): 전략·pin 설정과 오류 유형별 해제 경로.
- [LICENSE](https://github.com/weave-os/router/blob/03319b1d4b14ebb2ac569059c95982e9bea3c63c/LICENSE): Elastic License 2.0. 코드 공개와 제한 없는 재배포·서비스 제공을 혼동하지 않음.
- 확인 범위: 위 문서 및 핵심 소스 구간의 정적 조사. hosted 서비스 접속 실험·외부 모델 호출·전체 전략 감사·벤치마크 재현은 하지 않음.

### R08

**Aurelio Semantic Router** · Python · [저장소](https://github.com/aurelio-labs/semantic-router)

- 확인 ref: `d926cfcf8523cc28a468e59535899d16277fe55b` (2026-08-24), MIT 표기.
- [README](https://github.com/aurelio-labs/semantic-router/blob/d926cfcf8523cc28a468e59535899d16277fe55b/README.md): 사람이 정의한 `Route.utterances`, embedding, 매칭 없음 반환.
- [semantic.py](https://github.com/aurelio-labs/semantic-router/blob/d926cfcf8523cc28a468e59535899d16277fe55b/semantic_router/routers/semantic.py): dense encoder·예시 문장 색인, 기본 top_k=5/mean aggregation.
- [base.py](https://github.com/aurelio-labs/semantic-router/blob/d926cfcf8523cc28a468e59535899d16277fe55b/semantic_router/routers/base.py): `__call__`의 vector 검색·경로 점수화, `fit`의 labeled route 데이터 기반 threshold random search.
- 분류: 정적 예시 경로는 fine-tuning 없이 구성 가능. 선택적 `fit`와 dynamic route의 LLM 호출은 별개. 경로 의미 매칭 자체가 모델 성능·가격 최적화는 아님. 설치·실행 없음.

### R09

**ULab LLMRouter — KNNRouter** · Python · [저장소](https://github.com/ulab-uiuc/LLMRouter)

- 확인 ref: `da3430baaea672743c3957457b0c76faba19876e` (2026-08-15), MIT 표기.
- [router.py](https://github.com/ulab-uiuc/LLMRouter/blob/da3430baaea672743c3957457b0c76faba19876e/llmrouter/models/knnrouter/router.py): 질의별 `performance.idxmax()`로 모델 label을 만들고 Longformer embedding과 `KNeighborsClassifier.predict`로 선택.
- [trainer.py](https://github.com/ulab-uiuc/LLMRouter/blob/da3430baaea672743c3957457b0c76faba19876e/llmrouter/models/knnrouter/trainer.py): sklearn `.fit(X, y)` 및 artifact 저장. 신경망 gradient 학습은 아니지만 성능 label 데이터에 의존.
- 한계: 확인한 kNN 경로는 가격을 함께 예측하는 P11 utility 방식과 다르다. 라이브러리의 다른 학습형 라우터·서빙 전체를 무학습 또는 검증된 production 동작으로 분류하지 않음. 설치·실행 없음.

### R10

**ORBIT — Optimal Routing and Budgeted Inference Toolbox** · [저장소](https://github.com/LAMDA-Model-Reuse/ORBIT)

- 확인 ref: `494810de2605f69737e72b55baf6e60c95c6dec0` (2026-08-26).
- [README](https://github.com/LAMDA-Model-Reuse/ORBIT/blob/494810de2605f69737e72b55baf6e60c95c6dec0/README.md): 28개 방법/변형 목록, 공통 interface·budget sweep·nAUC/QNC/RCI, RouteJudge 연동.
- 용도: 방법론 누락을 점검하는 inventory와 오프라인 비교 틀. 28개에는 같은 논문의 변형·Oracle도 포함되어 28개 독립 논문 또는 배포 가능한 정책을 뜻하지 않는다.
- 한계: LLMRouterBench loader의 split·zero-cost 보정·가격 정규화는 원본과 별도 protocol이다. 이 수치를 실제 달러 비용과 혼합하지 않는다. 전체 adapter를 검증·실행한 것은 아님.

### R11

**vLLM Semantic Router** · [저장소](https://github.com/vllm-project/semantic-router) · Apache-2.0

- 확인 ref: `ffe2bb6424a97c4282a2befe822d11584a0c50ce` (2026-09-05).
- [Protection 문서](https://github.com/vllm-project/semantic-router/blob/ffe2bb6424a97c4282a2befe822d11584a0c50ce/website/docs/tutorials/learning/protection.md), [현재 호출 경로](https://github.com/vllm-project/semantic-router/blob/ffe2bb6424a97c4282a2befe822d11584a0c50ce/src/semantic-router/pkg/extproc/router_learning_protection.go), [선택 점수](https://github.com/vllm-project/semantic-router/blob/ffe2bb6424a97c4282a2befe822d11584a0c50ce/src/semantic-router/pkg/selection/session_aware_scoring.go).
- 추가 확인: `req_filter_classification.go`, `learning_config.go`, selection·session/cache state·protocol 경로. base route 뒤 protection이 SessionAwareSelector를 호출하는 연결을 확인했다. 과거 `algorithm: session_aware` 설정을 현재 API처럼 제안하지 않는다.
- 기능: conversation/session stickiness, cache·tool-loop·handoff·switch-history 비용 신호, 잔여 길이 prior, 진단·bounded rescue. `learning.enabled` 아래에서 adaptation을 명시적으로 끄고 protection만 사용하는 경로가 있다. 이름만 보고 neural training 필수라고 분류하지 않는다.
- 한계: 점수는 정규화된 선택 utility이며 모델별 미래 dollar cost의 정확한 최적화와 다르다. 캐시 actual/estimated·known-zero 구분 기능이 있으므로 missingness 자체도 신규 기능이라고 주장하지 않는다. 분류기까지 strict 무학습으로 구성 가능한지는 base route와 함께 확인해야 한다.
- CPU/외부 API backend 배포 경로도 있어 반드시 자체 GPU 모델 서빙이 필요한 것으로 설명하지 않는다. 정적 코드 확인이며 설치·동작·성능 재현은 하지 않음.

### R12

**Bifrost — Complexity Router** · [저장소](https://github.com/maximhq/bifrost) · Apache-2.0 core

- 확인 ref: `03ab391865710462302bbcf52dca2f32682b91b5` (2026-09-04).
- [공식 소스 문서](https://github.com/maximhq/bifrost/blob/03ab391865710462302bbcf52dca2f32682b91b5/docs/features/governance/complexity-router.mdx), [tier session 코드](https://github.com/maximhq/bifrost/blob/03ab391865710462302bbcf52dca2f32682b91b5/plugins/routing/complexitysession.go), [입력 추출](https://github.com/maximhq/bifrost/blob/03ab391865710462302bbcf52dca2f32682b91b5/plugins/routing/complexity/extract.go).
- 확인: 고정 reference 문장과 frozen embedding의 semantic tier 판정, 선택적 LLM fallback, CEL 규칙, upward-only tier session·tool continuation 재사용·background 우회, HTTP/SSE와 native WebSocket 차이.
- 입력 한계: 최신 또는 최근 N개 **user 메시지만** embedding하며 assistant/system은 제외. session은 model/provider/key가 아니라 tier를 유지하므로 정확한 캐시 위치 pin과 다르다.
- [provider usage 정규화](https://github.com/maximhq/bifrost/blob/03ab391865710462302bbcf52dca2f32682b91b5/core/providers/anthropic/passthrough_usage.go)도 확인. billing 기록 지원이 곧 미래 캐시 경제성 최적화를 뜻하지 않는다. 일부 enterprise 기능은 별도 범위; 실행 없음.

### R13

**Mozilla Otari — kNN routing** · [저장소](https://github.com/mozilla-ai/otari)

- 확인 ref: `9930c865e390d3dce4e96a5b57f6e3873c648ef4`; 초기 병합 `8b20687b89ab4cf0e33cddad3c7b69beefd0cdde`.
- [issue #187](https://github.com/mozilla-ai/otari/issues/187), [merged PR #188](https://github.com/mozilla-ai/otari/pull/188), [knn.py](https://github.com/mozilla-ai/otari/blob/9930c865e390d3dce4e96a5b57f6e3873c648ef4/src/gateway/services/routing/knn.py), [decide.py](https://github.com/mozilla-ai/otari/blob/9930c865e390d3dce4e96a5b57f6e3873c648ef4/src/gateway/services/routing/decide.py).
- 구현 확인: 사용자별 quality-scored memory의 kNN, quality−α·normalized list price, cold/sparse/low-confidence fallback, allowlist·trace_sticky. 도구가 있으면 선택을 decline하여 기본 target을 유지한다.
- 상태 구분: **cache-aware effective cost·switch penalty까지의 설계는 issue에 있고, v1 코드의 비용은 list price**다. issue가 열려 있다고 전체 미구현도 아니고, 제안 내용이 전부 배포됐다고 할 수도 없다.
- 한계: 과거 품질 점수 필요, process 내 bounded LRU stickiness, standalone 범위. 실행·라이선스 상세 재사용 검토는 하지 않음.

### R14

**everton-dgn/llm-router — adaptive routing state machine** · [저장소](https://github.com/everton-dgn/llm-router)

- 확인 ref: `5b34a315a07d7c943544d12d8285a13e21aed190`.
- [routing modes](https://github.com/everton-dgn/llm-router/blob/5b34a315a07d7c943544d12d8285a13e21aed190/docs/routing-modes.md), [adaptive_routing.mjs](https://github.com/everton-dgn/llm-router/blob/5b34a315a07d7c943544d12d8285a13e21aed190/opencode/lib/adaptive_routing.mjs).
- 확인: 즉시 upgrade, 2턴 최소 유지, 동일 downgrade 두 번 확인, cooldown 1턴, 짧은 follow-up downgrade 억제, pin.
- 한계: 확인한 전환 정책에 cache token 가격·TTL·복귀 캐시·break-even 최적화는 없다. 전체 classifier 학습 조건·배포·라이선스를 감사한 것이 아니므로 저장소 전체 무학습 또는 즉시 재사용 가능으로 단정하지 않는다.

## 공식 제품·API 문서

### D01

**OpenAI Prompt caching** · [공식 문서](https://developers.openai.com/api/docs/guides/prompt-caching)

확인: KV 상태·prefix 일치·캐시 위치·수명·모델 세대별 읽기/쓰기 과금 차이. 가격·TTL·지원 옵션을 고정 상수로 옮기지 않는다.

### D02

**Anthropic Optimizing for cost and intelligence** · [공식 문서](https://platform.claude.com/docs/en/about-claude/models/optimizing-for-cost-and-intelligence)

확인: 작업당 비용 비교, 캐시, advisor와 orchestrator, consultation 빈도, 단일 모델의 effort와 비교. 제공자 내부 측정이며 일반적인 절감률 보장이 아니다.

### D03

**Claude Thinking** · [공식 문서](https://platform.claude.com/docs/en/about-claude/models/extended-thinking-models)

확인: 모델 전환 시 thinking 블록 보존·호환성, 설정 변경과 캐시, reasoning 비용. 동작은 모델·API 버전에 따라 달라질 수 있다.

### D04

**LangChain Custom middleware** · [공식 문서](https://docs.langchain.com/oss/python/langchain/middleware/custom)

확인: `wrap_model_call`의 dynamic model selection 예시와 모델 요청 교체. 개입 위치의 구현 근거이며 선택 정책의 품질 보장이 아니다.

### D05

**OpenRouter Auto Router** · [공식 문서](https://openrouter.ai/docs/guides/routing/routers/auto-router)

확인한 최신 본문: 작업 분류·최근 7일 지출 점유율·비용 구간·제약·fallback·조건부 세션 모델 유지. 분류기 내부 학습 방식은 이 공개 페이지에서 확정하지 않았다.

갱신 주의: 검색 인덱스의 과거 Not Diamond 기반·무조건 모델 pin 설명과 최신 본문이 달랐다. 본 문서 묶음은 실제 열린 최신 본문을 기준으로 한다. ‘운영 서비스’는 공식 제공 기능을 확인했다는 의미이며 가입·유료 호출·독립 성능 검증을 수행했다는 뜻이 아니다.

### D06

**Not Diamond Key concepts** · [공식 문서](https://docs.notdiamond.ai/docs/key-concepts)

확인: cross-domain 데이터로 학습한 사전학습 라우터와 custom router의 구분, 품질·비용 절충 API. 사용자 측 학습이 불필요한 서비스와 무학습 알고리즘을 구분하는 근거.

### D07

**OpenAI Model guidance — reasoning configuration updates** · [공식 모델 가이드](https://developers.openai.com/api/docs/guides/latest-model)

확인: 지원 모델에서 `configuration_update`로 기존 prefix를 바꾸지 않고 effort를 조절하는 설명. 전체 모델에 일반화하지 않고 구현 시 호환 범위를 다시 확인한다.

### D08

**Claude Tool use with prompt caching** · [공식 문서](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-use-with-prompt-caching)

확인: 도구 정의·deferred tool의 prefix 영향, 제공자 내부 server tool 결과의 캐시 breakpoint. 응답 캐시와 구분한다.

### D09

**Augment Prism** · [공식 엔지니어링 글](https://www.augmentcode.com/blog/augment-prism-model-routing-to-reduce-cost-and-maintain-quality) · 게시 2026-05-02, 본문 갱신 2026-06-18

- 확인: planner가 user turn 시작에 선택, 해당 tool-result 후속 요청에는 결정 재사용, 턴 사이에서 캐시 전환 이득 비교·context 관리.
- 업체의 공개 측정: 캐시 읽기/쓰기를 포함하는 내부 Go 저장소 benchmark, 별도 Terminal Bench·SWE-Bench Pro 표, planner 비용과 사용자 턴의 체감 지연 구분.
- 한계: planner의 학습 과정·실제 비용식·서비스 내부 코드는 공개 본문만으로 확정하지 못함. 순수 무학습 증거가 아니라 운영 구조 참고. 성능·트래픽 수치는 업체 측정이며 독립 재현 없음.
- 캐시 해석 주의: 글의 ‘eviction/throws the cache away’는 제품 설명이다. 모든 API에서 모델 전환이 이전 모델의 캐시를 물리적으로 삭제한다는 보편 명제로 옮기지 않음.

## 자료를 추가할 때

1. 논문·저자 저장소·제품 공식 문서를 우선한다.
2. 발표일·확인일·버전·실제로 읽은 부분을 적는다.
3. 학습 유무뿐 아니라 과거 데이터·보정·탐색·추론 호출 필요 여부를 적는다.
4. 에이전트 전체 실행인지 단일 질의인지, 실측 비용인지 proxy인지 구분한다.
5. 광고성 절감률이나 리더보드 점수를 조건 없이 설계 근거로 옮기지 않는다.
6. 코드에 나온 선택 옵션이 서비스나 README의 전체 보장과 같은지 확인한다.
