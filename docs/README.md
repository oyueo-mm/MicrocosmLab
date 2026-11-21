# MicrocosmLab 문서 허브

프로젝트 관련 기술·설계·연구·결정·작업 기록은 **이 `docs/` 디렉터리에서 관리**합니다. 루트 `README.md`는 프로젝트 소개용이며, `CONTEXT.md` 및 `AGENTS.md`는 새 채팅·에이전트를 위한 짧은 진입점입니다.

## 주요 문서

| 구분 | 문서 | 용도 |
| --- | --- | --- |
| 전체 맥락 | [docs/context/README.md](context/README.md) | 읽는 순서·현재 프로젝트 방향 |
| 현재 구현 | [STATUS.md](context/STATUS.md) | 확인된 상태, 버전·제약·최신 결과 |
| 제품 UX | [PRODUCT.md](context/PRODUCT.md) | UI 원칙, 핵심 경험·기능 |
| 아키텍처·운영 | [ARCHITECTURE.md](context/ARCHITECTURE.md) | 데이터·권한·빌드·배포·검증 |
| 설계 결정 | [DECISIONS.md](context/DECISIONS.md) | 바꾸면 안 되는 결정·판단 근거 |
| 로드맵 | [ROADMAP.md](context/ROADMAP.md) | 완료/진행/제안 구분, 다음 작업 |
| 인수인계 | [HANDOFF.md](context/HANDOFF.md) | 새 에이전트의 읽기·쓰기 절차 |
| 추천 연구 | [RESEARCH.md](recommendation/RESEARCH.md) | V4 실험·행동 데이터 아키텍처 |
| 추천 결과 | [RESULTS-2026-11-21.md](recommendation/RESULTS-2026-11-21.md) | 측정 조건·실제 실험 결과·한계 |
| 기록 보존 | [DEVELOPMENT-2026-11.md](history/DEVELOPMENT-2026-11.md) | 이전 장문 README 개발 이력 원문 아카이브 |

## 문서 작성 원칙

- **`STATUS.md`는 현재 실제 구현과 검증된 상태**, `ROADMAP.md`는 앞으로의 계획, `DECISIONS.md`는 변경 근거를 담습니다. 아이디어·예정·미검증을 완료로 표현하지 않습니다.
- 실험 수치와 근거 로그는 분야별 `docs/<분야>/`에 두고, `STATUS.md`에서 링크합니다. 연구의 재현 조건·시드·정답 정의·편향도 함께 기록합니다.
- 이미 시행한 변경의 상세 타임라인은 `docs/history/`로 이동해 문서 중복을 피합니다. 긴 새 개발 기록을 루트 README에 추가하지 않습니다.
- 새 기능이나 연구를 완료한 작업은 **같은 PR에서 해당 docs 문서를 갱신**합니다. 코드·CI·Actions 결과가 낡은 문서보다 우선합니다.
- 이 저장소는 ChatGPT 대화를 자동으로 읽어 문서화하지 않으며, 에이전트가 의미 있는 결정을 문서로 커밋해야 지속됩니다.

## 새 채팅 시작

> https://github.com/oyueo-mm/MicrocosmLab/blob/main/docs/context/README.md 를 먼저 읽고 STATUS, DECISIONS, ROADMAP, HANDOFF 및 현재 main 코드를 확인해 줘. 끝나면 작업 결과를 관련 docs 문서에 기록하고 검증·배포 여부를 명시해 줘.
