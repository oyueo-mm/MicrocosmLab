# MicrocosmLab — 새 채팅·에이전트 맥락 진입점

> **전체 맥락의 기준은 [docs/context/README.md](docs/context/README.md)입니다.** 이 루트 파일은 새로운 대화·에이전트가 처음 읽는 짧은 인덱스입니다.

- 저장소: https://github.com/oyueo-mm/MicrocosmLab
- 서비스: https://oyueo-mm.github.io/MicrocosmLab/
- 목적: 실제 미시 세계 관찰 장면의 흥미로운 발견 경험과 정확한 원본 정보 제공.
- 운영: React/TypeScript/Vite 정적 앱·GitHub Pages. 영상 파일 원본은 보관하지 않음.
- 추천: **운영은 V3**, V4 TF-IDF/BM25/KNN/MMR/문장 임베딩은 연구 결과가 있으며 실사용자 선호 개선 확증 없음. 시청 신호는 브라우저 로컬 저장. 협업 필터링용 사용자 DB는 미운영.
- UI: YouTube에 익숙한 홈·도감·상세·쇼츠, 흰색/검정색/파란색, 최소 문구와 한국어 우선, 원본 영상 비율 보존.

## 읽는 순서

1. [맥락 허브](docs/context/README.md)
2. [현재 검증된 상태](docs/context/STATUS.md)
3. [제품·UI 원칙](docs/context/PRODUCT.md) · [데이터·기술 구조](docs/context/ARCHITECTURE.md)
4. [설계 결정](docs/context/DECISIONS.md) · [향후 로드맵](docs/context/ROADMAP.md)
5. [새 에이전트 인수인계](docs/context/HANDOFF.md) · [에이전트 규칙](AGENTS.md)
6. [추천 연구](docs/recommendation/RESEARCH.md) · [추천 결과](docs/recommendation/RESULTS-2026-11-21.md)

**필수:** 실제 main 코드, 최근 PR/Actions와 문서를 대조하세요. 중요한 변경과 검증 결과는 **같은 PR에서 관련 `docs/` 파일에 기록**하고 완료·미구현·미검증을 구분하세요. 채팅 내용은 GitHub에 자동 동기화되지 않습니다.

새 채팅 전달용:

> MicrocosmLab 저장소의 `docs/context/README.md`와 `AGENTS.md`부터 읽고, 현재 main 소스·실험·CI를 대조해 이어서 작업해 줘. 완료 후 관련 docs 문서도 갱신하고 빌드·배포 결과를 보고해 줘.
