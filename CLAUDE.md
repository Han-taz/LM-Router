# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 상태

LM-Router는 사용자 질의와 에이전트 실행 상태(도구 호출, 다단계 모델 호출)에 따라 모델을 선택해, 작업 성공률·답변 품질을 유지하면서 전체 비용을 줄이는 라우터를 연구한다.

- **현재는 자료조사·설계 후보 단계다.** 소스 코드, 빌드·린트·테스트 명령, 유료 API 실험, 자체 벤치마크가 없다. 언어·스택·통합 지점(harness/gateway)도 미정이므로 구현을 시작하기 전에 사용자와 확정한다.
- **사용자 확정 제약: 라우터를 별도로 학습시키지 않는다(training-free).** 기존 LLM의 프롬프트 기반 판단 호출, 과거 성능표·임계값 보정을 어디까지 허용할지는 아직 미정이다. "라벨 없음 / 신경망 학습 없음 / 정책 보정 없음"은 서로 다른 조건으로 구분한다.
- 핵심 질문: 모델 전환의 이득이 프롬프트 캐시 손실, 추론 상태 호환성, 추가 호출·재시도 비용을 상쇄하는가?
- 현재 권고(`docs/research/novelty-and-feasibility.md`, `docs/design/implementation-reuse-plan.md`): 넓은 "무학습 + 대화 맥락 + 캐시 유지" 조합은 선행연구·구현이 이미 있다. 새 gateway를 만들지 말고, 기존 실행 계층 하나(Switchyard·Weave·vLLM Semantic Router·Bifrost/LiteLLM 중) 위에 별도 정책 모듈을 붙여 좁은 가설 H4–H6을 검증한다. 여러 gateway를 직렬로 연결하지 않는다.

## 문서 구조

모든 문서는 한국어로 작성한다. 시작점은 `docs/README.md`(읽는 순서·용어 정의 포함)다.

- `docs/research/` — 논문·공식 문서·공개 코드에서 관찰한 사실과 한계.
- `docs/design/` — 이 프로젝트에 대한 설계 **제안**. 승인된 구현 명세나 존재하는 API가 아니다. 미결정 사항 표는 `docs/design/router-options.md` 끝에 있다.
- `docs/evaluation/` — 앞으로 실행할 실험 **계획**. 결과가 아니다.
- `docs/references/sources.md` — 모든 출처의 기준 위치(원문 링크, 버전, 확인일, 확인 범위, 한계).

출처 ID: `Pxx` 논문, `Rxx` 공개 구현, `Dxx` 공식 제품·API 문서. 본문에서는 `[P01](../references/sources.md#p01)` 형식으로 링크한다.

## 문서 작업 규칙

1. 작업 전 `docs/research/novelty-and-feasibility.md`, `docs/design/implementation-reuse-plan.md`, `docs/design/router-options.md`의 미결정 사항·사용자 제약을 먼저 읽는다.
2. 새 출처는 `docs/references/sources.md`에 먼저 등록(다음 번호, 확인 범위·한계 기재)한 뒤 주제 문서에서 연결한다.
3. 출처에서 확인한 사실, 저자·업체의 주장, 이 프로젝트의 제안을 구분해 쓴다. 서비스 문서는 공개 동작의 근거일 뿐 독립 성능 검증이 아니다. 학습된 정책의 절감 수치를 무학습 접근의 근거로 쓰지 않는다.
4. "기존에 없음"은 확인한 자료 범위 밖이라는 뜻으로만 쓴다.
5. 기존 결과를 수정할 때 논문 버전·벤치마크 범위·측정 조건을 함께 갱신한다. 가격·모델 지원·캐시 TTL·제공자 동작은 구현 시 공식 문서에서 다시 확인한다.
6. 자체 실험 결과가 생기면 별도 결과 문서에 실행 설정·버전·원시 로그 위치를 남긴다. 계획 문서의 예상치를 실측치로 바꿔 쓰지 않는다.
7. 문서 상단의 기준일·상태 표기와 `docs/README.md`의 "최종 정리" 날짜를 갱신한다.
