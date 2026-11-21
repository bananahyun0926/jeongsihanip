# 정시한입 — 2027학년도 대학별 정시모집요강 모아보기

유튜브 **정시한입**([@jeongsihanip](https://www.youtube.com/@jeongsihanip)) 쇼츠와 함께 쓰는
대학별 2027학년도 정시모집요강 링크 모음 페이지입니다.

- 배포 주소: https://bananahyun0926.github.io/jeongsihanip/
- 정적 사이트 (GitHub Pages, `main` 브랜치 루트): `index.html` 단일 페이지 + `data/universities.json`

## 새 대학 추가하는 법

`data/universities.json` 의 `universities` 배열에 아래 형식으로 **한 건만 추가**하고 `main` 브랜치에 push하면
사이트에 바로 반영됩니다. (별도 빌드 없음)

```json
{
  "id": "korea",
  "name": "고려대학교",
  "campus": "서울캠퍼스",
  "group": ["가군"],
  "quota": "정원 내 000명 (수시 이월 전)",
  "schedule": "2027. 1. 5.(화) ~ 1. 7.(목) 18:00",
  "summary": "전형별 수능 반영비율 요약",
  "noticeUrl": "https://입학처 공지 URL",
  "pdfUrl": "https://요강 PDF URL",
  "shortsUrl": "https://youtube.com/shorts/XXXX"
}
```

- `group`: `가군` / `나군` / `다군` 중 해당하는 값의 배열
- `updated`: 목록 상단 기준일(YYYY-MM-DD) — 파일 맨 위 값 갱신
- `quota`·`schedule`·`summary`·`noticeUrl`·`pdfUrl`·`shortsUrl`은 값이 없으면 해당 영역이 자동으로 숨겨집니다.

## 유의사항

- 모집인원은 **수시 이월 반영 전** 값입니다. 최종 인원은 각 대학 입학처 공지로 확인하세요.
- 모든 수치는 각 대학 입학처의 확정 요강을 기준으로 하며, 이 페이지는 원문 링크 안내용입니다.
