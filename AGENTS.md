# MicrocosmLab — 코딩 에이전트 작업 규칙

## 시작 전 필독

1. **[문서 맥락 허브](docs/context/README.md)부터** [현재 상태](docs/context/STATUS.md), [제품·UI](docs/context/PRODUCT.md), [아키텍처](docs/context/ARCHITECTURE.md), [설계 결정](docs/context/DECISIONS.md), [로드맵](docs/context/ROADMAP.md), [인수인계](docs/context/HANDOFF.md)를 확인한다.
2. `README.md`는 사용자용 소개다. **개발 상세·결정·실험 기록은 `docs/`가 단일 관리처**다. 최근 main 코드·커밋·PR·Actions가 문서와 충돌하면 재현 가능한 근거를 우선해 문서를 수정한다.
3. 요청되지 않은 대량 수집/삭제·개인정보 서버 전송·원본 영상 재배포·실험 모델의 운영 승격을 하지 않는다.

## 구현·검증

- UX: YouTube와 유사한 영상 탐색, 최소한의 한국어 문구, 흰색/검정색/파란색, 원본 관찰 영상의 비율 보존.
- 콘텐츠: 영상 ID·출처·저작자·라이선스·시리즈 유지. 메타데이터 확인과 장면별 동정/행동 검수를 혼동하지 않는다.
- 추천: 운영 V3 보존, 연구 후보는 독립 비교. 생물종/카테고리 동일성을 정답으로 삼은 대리 지표가 KNN에 유리함을 명시하고 **실사용자 추천 정확도라고 주장하지 않는다.** 사람의 평가 기반 벤치마크는 아직 미구현.
- 개인정보: 기존 행동 신호는 로컬 전용. 서버 수집은 동의·보관/삭제·보안 설계 승인 전 활성화하지 않는다.
- 변경 시 해당 테스트, `node scripts/validate-content.mjs`, `npm run build`, GitHub Actions를 검증하고 PR/배포 성공을 명시한다.

## 완료 후 필수 문서화

- 구현 상태·실험 지표·검증 근거 → [STATUS.md](docs/context/STATUS.md), 분야별 `docs/<주제>/`.
- 계획·우선순위 → [ROADMAP.md](docs/context/ROADMAP.md).
- 변경된 결정과 근거 → [DECISIONS.md](docs/context/DECISIONS.md).
- 장기 맥락/인수인계 변경 → [HANDOFF.md](docs/context/HANDOFF.md).
- 필요한 docs 갱신을 **같은 PR·커밋에 포함**하고, 루트 README에 장문 개발 일지를 추가하지 않는다.
- ChatGPT 채팅 내용은 자동 저장되지 않음. 확인되지 않은 결과를 완료로 기록하지 않는다.
