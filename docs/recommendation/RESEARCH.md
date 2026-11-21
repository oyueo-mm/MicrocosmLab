# MicrocosmLab 추천 연구 V4 — 실험 계획·데이터 아키텍처

> **기준일 2026-11-21**. 프로덕션 추천 V3는 유지한다. 아래 표는 실험 코드의 존재, 실제 모델 학습, 온라인 노출, 서버 데이터 확보를 구분한다. 실제 사용자 선호 성능 향상을 검증하지 않은 후보를 승자로 발표하지 않는다.

## 1. 구현 및 검증 상태

**후속 실험:** [DISCOVERY-STUDY-2026-11-21.md](DISCOVERY-STUDY-2026-11-21.md)에 선택형 콜드 스타트·편집자/품질/다양성 추천·사람 평가·인기 추정 조건을 정리했다. 사람 평가 없이는 기존 V3보다 우수하다고 주장하지 않는다.

| 연구 대상 | 현재 상태 | 근거 / 제한 |
| --- | --- | --- |
| TF-IDF (한국어/영어 토큰, 한국어 문자 2-gram) | 오프라인 후보 구현 | `src/recommendationLab.ts`, `scripts/test-recommendation-lab.mjs` |
| BM25 (k1=1.2, b=.75) | 오프라인 후보 구현 | 텍스트 관련도 후보. 데이터가 부족한 일반적 메타데이터를 완전한 장면 정보로 간주하지 않음 |
| 콘텐츠 KNN | 오프라인 후보 구현 | 생물/카테고리/검수된 태그의 가중 Jaccard 유사도 |
| Hybrid + MMR | 오프라인 후보 구현 | 텍스트·BM25·KNN 합성과 재랭킹, 후보 출처/카테고리 편중 감시 |
| Sentence Embedding | **실제 다국어 384차원 임베딩 생성·평가 완료; 기본 추천 미변경** | `scripts/export-recommendation-corpus.mjs`, `scripts/generate-sentence-embeddings.py`, `.github/workflows/recommendation-embeddings.yml`. 모델을 실행하여 벡터를 생성한 경우에만 비교 |
| 세션 기반 추천 | **브라우저 행동 신호 수집 + V3의 제한적 반영 구현** | `src/usePreferences.ts`, `src/ShortFeed.tsx`, `src/recommendationV3.ts`. 서버 전송 없음 |
| Contextual Bandit | **LinUCB 순수 알고리즘 프로토타입/합성 데이터 단위 검사** | `src/recommendationOnlineLab.ts`; 실서비스 정책 탐색·무작위 노출 없음 |
| ItemKNN 협업 필터링 | **합성 사용자–영상 그래프 구현/검사** | 실사용자 공동 선호 데이터가 없어 품질 평가/배포 불가 |
| LightGCN | **그래프 전파 원리 검증용 프로토타입** | BPR 최적화로 학습한 LightGCN 모델이 아님. 실제 LightGCN 학습·튜닝·평가 **미구현** |
| 사용자 간 선호 집계 | **설계 단계, 미배포** | 동의 UI, 서버 인증/보안, 백엔드, 개인정보 정책 필요 |

## 2. 현재 데이터 흐름

`사용자 → GitHub Pages → JS/정적 카탈로그 → 브라우저 localStorage → 개인화`

- 기존 개인화: `liked`, `hidden`, `recent`, `played`, `watched`.
- 새로 추가한 로컬 신호: `signals[videoId]`의 `impressions`(실제 재생 시작), `watchSeconds`(재생 중 시간 누적), `quickSkips`(3초 이내 직접 넘김), `completes`(해당 쇼츠의 미리보기 구간 끝), `replays`(뒤로 탐색). 값·항목 수를 제한한다.
- 기존 `microcosmlab-preferences-v1` 키 유지: 과거 로컬 선호 기록을 이주 없이 보존. 페이지 방문과 실제 재생, 45초 미리보기 완료와 **전체 원본 영상 완료**를 혼동하지 않는다.
- `src/recommendationV3.ts`는 신호를 제한된 양/음의 가중치로 사용자 특징에 반영한다. **쇼츠 플레이리스트는 즉시 실시간 재정렬되지 않으며**, 재진입 시 개선된 선호를 기반으로 갱신되는 구조다.
- 사용자별 데이터는 다른 브라우저·기기로 동기화되지 않는다. 사이트 서버에 업로드하지 않는다.

## 3. 실제 공유 사용자 데이터를 확보하는 현실적인 선택지

### A. 권장: 선택 동의 방식의 서버 수집 (별도 승인 후 구현)

`동의 UI → 이벤트 최소화/검증 → Cloudflare Worker 또는 Supabase Edge Function → DB → 주기적 합성 집계/검증 → 추천 실험`

- 기술적 후보: **Cloudflare Workers + D1**(정적 Pages를 그대로 유지하고 경량 이벤트 API를 별도 서비스로 운영), 또는 **Supabase Postgres + Edge Functions**. 비용·제한은 배포 시 공식 요금표를 다시 검증한다.
- 핵심 조건: 서버 기반 수집은 **기본 비활성화**, 목적·이벤트 범위·보유 기간·철회·삭제 방법을 설명하는 **명시적 선택 동의** 이후에만 활성화. 기존 로컬 신호를 묵시적으로 소급 업로드하지 않는다.
- 익명 사용자 키만으로 완전한 익명 데이터가 되는 것은 아니다. 동의 기록·가명 식별자·연결 가능성·삭제권을 개인정보 위험으로 검토한다.
- 설계 제안(미확정): 짧게 유지하는 원시 이벤트(예: 30일), 제한된 최소 집계(예: 90일), 서버 로그 최소화, 데이터 삭제 API, 대량 요청 차단, 서버 측 스키마 검증, 중복 이벤트 방지, CORS origin 제한, 알림 없는 무단 수집 금지.
- 추천에 필요한 것은 단순 조회 총합이 아니라 **같은 동의 사용자가 무엇을 연속해서 보았는지(사용자–영상 희소 행렬)**와 정책이 노출한 아이템 및 반응이다. 단일 영상의 전역 좋아요 수만 있으면 ItemKNN/LightGCN을 학습할 수 없다.
- 장치 간 선호 동기화까지 하려면 별도 계정/사용자 연결 또는 사용자가 직접 이전하는 암호화 백업 방식이 필요하다. 계정 없는 익명 키는 기기 간 자동 통합이 불가능하다.

### B. 더 저렴한 대안: 브라우저 전용 + 외부 공개 데이터셋

- 동의 서버가 준비되기 전에는 TF-IDF/BM25·임베딩·콘텐츠 KNN·MMR·로컬 세션 기반 랭커로 품질 개선을 연구한다.
- 공개 추천 데이터셋으로 ItemKNN/LightGCN **알고리즘 구현 검증**은 가능하지만, 영화·상품 이용자 데이터로 MicrocosmLab의 사용자 선호 개선을 입증할 수는 없다.
- 사용자의 실제 좋아요·관심없음·재생 비율은 컨텐츠가 흥미로운지에 대한 선호를 평가하는 신호지만, 단발 재생·45초 자동 넘김·외부 iframe 재생을 동일한 관심도로 취급하지 않는다.

## 4. 단계별 실험과 승격 기준

**Phase 1: 콘텐츠 추천 연구 V4**
1. `node scripts/test-recommendation-lab.mjs`로 V3 vs TF-IDF/BM25/콘텐츠 KNN/하이브리드/MMR 비교.
2. `artifacts/recommendation-v4-experiment.json`의 데이터 개수·메타데이터 정답 기준·Recall@10 대리 지표·NDCG@10 대리 지표·공급자/카테고리 다양성·중복을 함께 분석.
3. GitHub Actions **Multilingual Sentence Embedding research (manual)** 워크플로를 별도 수동 실행하면 실제 다국어 모델 벡터가 실행 산출물로 저장되고 같은 기준으로 비교. 첫 실제 실행은 [Actions #44840692750](https://github.com/oyueo-mm/MicrocosmLab/actions/runs/44840692750)에서 성공했고, 검증 결과는 [RESULTS-2026-11-21.md](RESULTS-2026-11-21.md)에 기록했다. 자동으로 서비스 배포하지 않는다.
4. 기존 V3를 기준선으로 보존하고, 실제 사용자 데이터 없이 오프라인 대리 지표만 좋아졌다면 실서비스 전체 기본 추천을 교체하지 않는다.

**Phase 2: 행동 신호와 합법적 수집 설계**
1. 브라우저 내 재생/넘김/시청 구간 등을 저장하되 영상 자동 재생을 강한 호감도로 추론하지 않는다.
2. 동의형 서버 데이터 수집의 설계·비용·보안 검토, 개인정보 관련 운영 공지, 동의 철회·삭제 흐름까지 별도 PR로 구현 및 검증한다.
3. 외부 iframe은 실제 시청 시간을 관측할 수 없는 경우가 있으므로 **수집되지 않은 값을 미측정으로 유지**한다.

**Phase 3: 온라인 실험·협업 모델**
1. 로그가 생긴 후 노출 사건의 `experiment_arm`, 노출 목록·시각·propensity(탐색 확률)를 기록해 밴딧의 편향된 피드백을 구분한다.
2. 충분한 사용자–아이템 데이터가 쌓이면 ItemKNN → 임베딩 기반 이웃 → BPR LightGCN 순으로 독립 실험한다.
3. 정량 기준: 표본 크기, 노출 편향, 시청 지속률, 3초 이내 이탈률, 좋아요/관심없음, 장면 검수·재생 성공률, 다양성, 신뢰구간. 최소 표본이 없으면 **효과 불확실**을 명시한다.
4. 실사용자 만족도 지표가 확인될 때만 후보 승격하고, 버전별 롤백 플래그를 유지한다.

## 5. 보안·비용·운영 체크포인트

- 저장소에 API key, 식별자, 원시 이벤트, 실제 사용자의 취향 기록을 커밋하지 않는다.
- 이벤트 API는 인증없는 공개 엔드포인트가 악용될 수 있으므로 레이트 리밋·재전송 방지·상한·어뷰징 대응과 공개 도메인 외 origin 거절을 **동시 설계**한다. CORS만으로 봇 악용이 방지되지는 않는다.
- `Cloudflare D1 Free`은 운영 한도 초과 시 쿼리가 실패할 수 있다(2026-09 제한 집행 공지). 서비스 규모와 트래픽이 실제로 발생한 뒤 비용·안정성 재산정이 필요하다.
- `src/recommendationLab.ts`는 현재 **오프라인 연구 목적의 독립 후보**이고 `src/App.tsx`는 V3 기본값을 유지한다. `src/recommendationOnlineLab.ts` 역시 서비스에서 호출하지 않는다.

## 6. 재현 명령

- `npm install --no-audit --no-fund`
- `node scripts/test-recommendation-v3.mjs`
- `node scripts/test-recommendation-lab.mjs`
- `node scripts/test-online-research-prototypes.mjs`
- `npm run build`
- 임베딩 수동 경로: `node scripts/export-recommendation-corpus.mjs` → Python `sentence-transformers` 설치 → `python scripts/generate-sentence-embeddings.py` → `node scripts/test-recommendation-lab.mjs`.

**중요:** 오프라인 결과가 임베딩/타 추천기를 승자로 만들어도 메타데이터 일치가 높았다는 뜻이지, 실제 이용자의 시청 만족도·정답 Precision@K/NDCG가 향상됐다는 뜻이 아니다.
