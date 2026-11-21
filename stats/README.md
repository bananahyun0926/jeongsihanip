# 한입 스탯 — 정시한입 · 수시한입 통합 통계 대시보드

정시한입(`jeongsihanip.github.io` · `@jeongsihanip`)과 수시한입(`susihanip.github.io` · `@susihanip`)의
**웹사이트 방문자·페이지뷰·유입 경로·인기 대학 클릭**과
**유튜브 구독자·조회수·영상별 좋아요**를 한 화면에서 나란히 보는 정적 대시보드입니다.

## 파일 구조

```
hanip-stats-web/
├─ index.html        화면 뼈대 (헤더·히어로·KPI·섹션·푸터)
├─ style.css         다크 네이비 + 민트/블루(정시) · 골드/옐로우(수시) 테마, 반응형
├─ app.js            data/stats.json 을 읽어 카드·차트(SVG)·랭킹 렌더링
├─ data/stats.json   모든 수치의 단일 소스
└─ README.md
```

외부 라이브러리·CDN 의존은 폰트(Pretendard)뿐이며, 차트는 JS로 그리는 인라인 SVG입니다.
`data/stats.json`만 바꾸면 화면 전체가 갱신됩니다.

## 데이터 구분 원칙 (중요)

| 구분 | 상태 | 화면 표기 |
|---|---|---|
| 유튜브 조회수·좋아요·구독자 | **실측** (공개 채널/영상 페이지 직접 확인) | 초록 `실측` 배지 |
| 웹사이트 방문자·페이지뷰·유입·대학 클릭 | **집계 대기** (GA4 미연결) | 회색 `집계 대기` 배지 + `측정 시작(집계 대기)` |
| 차트·랭킹 모양 | **샘플** (레이아웃 확인용) | 노랑 점선 `샘플` 배지, 하단 캡션 |

- 집계 전 값은 `null`로 두고 화면에는 숫자 대신 `측정 시작(집계 대기)`를 표시합니다.
  **추정·가짜 숫자를 실제처럼 넣지 않습니다.**
- 샘플 데이터는 `stats.json`의 `sample` 블록에만 존재하며, 실제 집계가 시작되면
  `site.daily` · `site.trafficSources` · `site.topUniversities` 에 실제 값을 넣고
  화면에서 샘플 대신 그 값을 쓰도록 바꾸면 됩니다(현재는 항상 `sample`을 읽습니다).

## stats.json 스키마

```jsonc
{
  "updatedAt": "2026-11-21T19:46:00+09:00",
  "updatedAtLabel": "2026-11-21 19:46 (KST)",
  "source": { "youtube": "...", "website": "..." },
  "summary": {
    "websiteVisitors": null, "pageviews": null,   // null = 집계 대기
    "youtubeViews": 1126, "subscribers": 0, "videoCount": 2, "likes": 4,
    "status": { "website": "pending", "youtube": "live" }
  },
  "jeongsihanip": {
    "name": "정시한입", "handle": "@jeongsihanip", "theme": "mint-blue",
    "site": { "url": "...", "universityCount": 23, "status": "pending",
              "visitors": null, "pageviews": null,
              "daily": [], "trafficSources": [], "topUniversities": [] },
    "youtube": { "subscribers": 0, "totalViews": 1126, "likes": 4, "videoCount": 2,
                 "videos": [ { "id": "...", "title": "...", "shortTitle": "...",
                               "url": "...", "publishedLabel": "...", "durationSec": 44,
                               "views": 705, "likes": 1 } ] }
  },
  "susihanip": { ... },
  "sample": { "jeongsihanip": { "daily": [...], "trafficSources": [...], "topUniversities": [...] },
              "susihanip": { ... } }
}
```

### 지금 들어 있는 실측값 (2026-11-21 19:44 KST 확인)

| 채널 | 영상 | 조회수 | 좋아요 | 길이 | 게시 |
|---|---|---|---|---|---|
| 정시한입 | 건국대 서울캠퍼스 `OEZeBpUk8yc` | 705 | 1 | 0:44 | 11-21 15:11 |
| 정시한입 | 서울대 `p5DjyAgTPG0` | 421 | 3 | 0:47 | 11-21 18:03 |
| 수시한입 | (아직 없음) | 0 | 0 | — | 11/22 15:00 첫 업로드 예정 |

- 합계: 조회수 1,126 · 좋아요 4 · 영상 2편
- 구독자: 두 채널 모두 YouTube 공개 페이지에 구독자 수가 표시되지 않아 `0`으로 두고
  `subscribersStatus: "public_hidden"` 으로 근거를 남겼습니다(화면에 `공개 미표시` 표기).
- 정시한입 "총 조회수"는 **공개 영상 조회수 합**입니다. YouTube Studio의 채널 총 조회수와는 다를 수 있습니다.

## 갱신 방법

1. **유튜브(지금 가능)**: 공개 채널/영상 페이지에서 영상별 조회수·좋아요를 확인해
   `youtube.videos[]`, `youtube.totalViews`, `summary.youtubeViews` 를 수정하고
   `updatedAt` / `checkedAt` 을 바꿉니다. (Studio 총 조회수는 채널 소유자 로그인이 필요해 사용하지 않았습니다.)
2. **웹사이트(GA4 연동 후)**: 두 사이트에 GA4 측정 ID를 넣고, 하루 1회
   (a) GA4 Data API에서 일별 방문자·페이지뷰·유입 경로, (b) 대학 카드 클릭 이벤트 집계를 받아
   `site.daily` · `site.trafficSources` · `site.topUniversities` 에 채우면 됩니다.
   같은 작업에서 `summary.websiteVisitors` · `summary.pageviews` 도 함께 채웁니다.
3. 대학 클릭은 두 요강 사이트의 카드에 `요강 PDF / 입학처 공지 / 쇼츠` 클릭 이벤트
   (GA4 `select_content`, `item_id=대학명`)를 심어야 잡힙니다 — 아직 미구현.

## 로컬에서 보기

```bash
cd hanip-stats-web
python3 -m http.server 8917
# 브라우저에서 http://127.0.0.1:8917/ 열기
```

`index.html`을 파일로 바로 열면 `fetch('data/stats.json')`이 막히므로 반드시 HTTP 서버로 열어야 합니다.

## 검증 기록 (2026-11-21)

- Chromium(Playwright)으로 `http://127.0.0.1:8917/` 렌더링 — 콘솔 오류 0건
- 데스크톱 1440px: `attachments/2026-11-21/hanip-stats-preview.png` (2880×4320, 2x)
- 모바일 390px: `attachments/2026-11-21/hanip-stats-mobile.png` — 가로 스크롤 없음(`scrollWidth = 390`)
- 4개 외부 링크 모두 HTTP 200 확인 (정시·수시 사이트, 두 유튜브 채널)

## 배포 (아직 안 함)

이 폴더는 정적 사이트라 그대로 GitHub Pages·Cloudflare Pages·Vercel 어디든 올릴 수 있습니다.
주소 예: `https://hanip-stats.github.io/` 또는 기존 사이트 하위 경로(`/stats/`).
새 저장소 생성·Pages 활성화는 사용자 확인이 필요한 단계라 실행하지 않았습니다.
