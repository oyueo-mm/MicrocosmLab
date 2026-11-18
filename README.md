# MicrocosmLab

미시 세계에서 일어나는 실제 행동과 상호작용을 현미경 영상으로 탐색하는 한국어 아카이브. 현재 현미경 영상 **150개**를 분류별로 볼 수 있습니다.

**사이트:** https://oyueo-mm.github.io/MicrocosmLab/

## 콘텐츠

- `data/videos.json`: 사이트에 공개되는 승인된 현미경 영상. 영상 이름, 행동, 대상, 설명, 저작자, 라이선스를 포함합니다.
- `data/organisms.json`: 생물·세포 등 등장 대상과 한국어 위키백과 제목. 상세 이미지가 있을 때 위키백과 대표 이미지 또는 해당 대상만 촬영한 영상 썸네일을 사용합니다.
- `data/candidates.json`: 승인 전 후보. 이 목록은 사이트에 자동 게시되지 않습니다.
- `scripts/validate-content.mjs`: 내부 참조와 중복, 필수 저작권 정보 검증.
- `scripts/audit-commons.mjs`: Wikimedia Commons 원본의 파일 존재 여부 및 공개 라이선스 정보 점검. 결과는 GitHub Actions 아티팩트로 제공합니다.
- `scripts/discover-commons.mjs`: 지정한 Commons 분류에서 공개 라이선스의 비디오 파일을 탐색하고 **후보**만 아티팩트로 저장합니다. 결과를 검토하지 않은 상태로 공개 목록에 추가하지 않습니다.

## 개발

```bash
npm install
npm run validate:content
npm run dev
npm run build
```

## 영상 등록 절차

1. `npm run discover:commons` 실행 후 `artifacts/commons-candidates.json`에서 후보를 찾습니다.
2. 원본 파일 페이지에서 **실제 관찰 영상인지**, 등장 생물 및 행동 설명이 일치하는지, 저작자와 라이선스가 올바른지 검토합니다.
3. 검토한 파일만 `data/videos.json`에 추가하고, 신규 등장 대상이 필요하면 `data/organisms.json`에 등록합니다.
4. `npm run validate:content`와 `npm run audit:commons`로 검증합니다.
5. GitHub `main`에 반영하면 Actions가 GitHub Pages에 배포합니다.

## 사용자 인터페이스

- 영상 카드: 썸네일과 제목. 행동 분류는 상세 화면에서 확인합니다. 영상은 최초 24개를 노출하고 ‘더 보기’로 추가 로드합니다.
- 등장 대상: 작은 대표 이미지, 한국어 이름 및 학명. 이름 클릭 시 한국어 위키백과 문서로 이동하며 문서가 없다면 나무위키 검색으로 연결합니다.
- 추천 시스템, 회원가입, DB는 아직 구현하지 않았습니다.

## 저작권 및 라이선스

저장소의 자체 소스코드는 [MIT](LICENSE) 라이선스로 배포합니다. **영상과 대표 이미지는 각 원저작자의 라이선스를 따릅니다.** 외부 영상 원본은 이 저장소에 포함하지 않으며 상세 페이지에서 출처, 제작자 및 라이선스를 공개합니다.

자동 검증은 파일 존재 및 기계 판독 가능한 메타데이터를 확인하는 보조 절차일 뿐, 학명·행동·영상 장면을 과학적으로 검증하는 과정까지 대체하지 않습니다.
