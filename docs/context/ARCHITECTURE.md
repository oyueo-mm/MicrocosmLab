# 아키텍처·콘텐츠·운영

## 콘텐츠와 기술 구조

- 정적 **React 19 + TypeScript + Vite 7** 앱, GitHub Pages 제공. 별도의 로그인·추천 DB·자체 동영상 저장 서버는 현재 없다.
- `src/data.ts`가 `data/*.json`의 Commons 영상, 외부 영상, 생물종 메타데이터를 합쳐 공개 카탈로그를 구성한다.
  - `data/videos.json`, `data/featured-videos.json`, `data/pipeline-videos.json`: Wikimedia Commons 영상.
  - `data/external-videos.json`, `data/discovery-videos.json`, `data/discovery-extra.json`, `data/expansion-external.json`: 외부 공식 임베드 후보/등록 데이터.
  - `data/organisms.json`: 생물종·세포 등 등장 대상.
  - `data/candidates.json`: 비공개 후보 목록(등록 데이터와 구별).
- 2026-11-20 기준 **원본 기록 2,997개 = Commons 2,921개 + 외부 76개**, 동일 연구 촬영본을 묶은 **도감 대표 카드 1,196개**. 이는 날짜가 있는 스냅샷이며 수집·정리 이후 반드시 재계산한다. **영상 기록 수와 도감 카드 수는 다르다.**
- `src/grouping.ts`: 시리즈별 묶기·카드 중복 제거. 각각의 원본 영상과 ID는 보존.
- `src/hooks.ts`: Commons 미디어·썸네일 주소 동적 로딩, 요청 캐시/실패 후 재시도, 위키 링크. `src/contentQuality.ts`: 검수 상태 및 추천 품질 휴리스틱.
- 영상 바이너리를 Git에 저장하거나 외부 원본을 임의로 재배포하지 않는다. Commons의 저작자·라이선스, YouTube/JoVE 원본 임베드 정책을 존중한다.
- **메타데이터·라이선스 검사와 실제 장면별 생물학적 동정/행동 검수는 별개**다. 자동 수집된 자료를 “포식을 실제로 촬영했다”고 근거 없이 단정하지 않는다. 인상적인 영상 확대와 정확성 검증을 함께 추진한다.
- 한국어 제목은 `src/koreanTitles.ts` 기반의 연구 주제 요약이며, 완전한 전문 번역 또는 개별 장면 과학적 검증 결과가 아니다. 연구 원제를 보존한다.

## 수집·검증·배포 절차

- 수집: `scripts/batch-commons.mjs` (기본은 미리보기, `--write` 때만 승인된 등록 파일 수정), `scripts/discover-external.mjs`, `scripts/discover-commons.mjs`.
- 검증: `scripts/validate-content.mjs`, `scripts/test-catalog-cleanup.mjs`, `scripts/test-grouping.mjs`, `scripts/test-batch-pipeline.mjs`, `scripts/test-recommendations.mjs`, `scripts/test-recommendation-v3.mjs`, `scripts/test-explorer.mjs`, `scripts/test-korean-and-shorts.mjs`, `scripts/test-subjects.mjs`, `scripts/test-quality-report.mjs`.
- 표본 재생 점검: `scripts/audit-playback.mjs`와 `.github/workflows/playback-audit.yml`; 브라우저에서 모든 원본이 반드시 재생된다는 보장은 아니다.
- CI: `.github/workflows/build.yml` 및 `deploy-pages.yml`; `npm install --no-audit --no-fund`, 콘텐츠/추천 회귀 검사, `npm run build`. **npm run build = `tsc --noEmit && vite build`**. Pages는 base `/MicrocosmLab/`로 빌드.
- 자동 후보 수집: `.github/workflows/batch-discovery.yml`, `discover-content.yml`. 후보와 실제 등록/공개는 구분하고 원본 중복·저작권·장면 검수 수준을 확인한다. YouTube API 키는 GitHub Actions 시크릿 `YOUTUBE_API_KEY`가 필요할 수 있으나 **현재 존재 여부는 직접 확인해야 한다**. 키나 개인정보를 문서·로그에 남기지 않는다.
- 배포 전/후: 원본 ID 및 라이선스 보존, 대표 카드와 전체 수 비교, 한국어 제목 정확도, 영상 로드·쇼츠 전환, 모바일/PC 레이아웃, CI 결과 및 Pages URL을 검증하고 배포 결과를 보고한다. 롤백 가능한 PR/커밋을 유지한다.

## 문서화 시 주의

- 콘텐츠 개수는 수집 실행에 따라 바뀌므로 이곳 숫자는 **2026-11-20 기준 스냅샷**입니다. 현재 건수는 실제 `data/*.json`과 `src/grouping.ts`의 그룹 결과로 산출합니다.
- 라이선스·저작권 메타데이터, 실제 장면 판독, 외부 사이트의 임베드 가능 여부는 각각 별도 검증이 필요합니다.
- 대규모 신규 콘텐츠·백엔드 도입은 설계 문서와 검증 없이 자동 배포하지 않습니다.
