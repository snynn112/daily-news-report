# Daily News Report 작성 지침

이 저장소는 스케줄러 앱(https://workingtorchscheduletracker.netlify.app)의 `Daily News Report` 탭이 읽는 리포트 보관함이다.
매일 새벽 Claude 예약 작업 3개가 이 지침에 따라 리포트를 만들어 올린다. 예약 작업은 이 파일을 먼저 끝까지 읽고 그대로 따른다.

- 날짜 기준: 한국 시간(Asia/Seoul). `DATE`는 실행 시점의 한국 날짜 `YYYY-MM-DD`.
- 리포트 제목: `YYYY. MM. DD. Daily News Report` (예: `2026. 11. 22. Daily News Report`)
- 이 파일, `index.json`, `reports/`, `parts/`, `runs/` 외의 파일은 만들거나 고치지 않는다.

## 1. 실행 단계

| 단계 | 시각(KST) | 담당 | 결과 파일 |
|---|---|---|---|
| part1 | 04:43 | 권역 ① 아시아-태평양, ② 중국, ③ 미국 | `parts/DATE-part1.json` |
| part2 | 05:17 | 권역 ④ 유럽(동유럽, 서유럽), ⑤ 중동, ⑥ 아프리카 | `parts/DATE-part2.json` |
| part3 | 05:51 | 헤드라인, 경제, 기술과학 + part1·part2를 합쳐 최종 리포트 완성 | `reports/DATE.json`, `index.json` |

공통 절차:
1. `git pull` 로 최신 상태를 받는다.
2. **멱등성:** `reports/DATE.json`이 이미 있고 `status`가 `complete`면 아무것도 하지 않고 끝낸다. 자기 단계의 `parts/` 파일이 이미 있으면 그 단계는 다시 하지 않는다(part3은 합치기만 다시 할 수 있다).
3. 조사·작성 후 결과 파일과 `runs/DATE.json`(자기 단계 항목만 갱신)을 커밋하고 `main`에 푸시한다. 커밋 메시지: `DATE partN` 형식.
4. 푸시가 충돌하면 `git pull --rebase` 후 한 번 더 푸시한다.

part3 추가 절차:
- `parts/DATE-part1.json`, `parts/DATE-part2.json`이 없으면, 없는 권역은 `groups`를 비우지 말고 해당 섹션 `note`에 "이 권역 조사 단계가 실패해 포함되지 않음"을 적고 `status`를 `partial`로 한다. 시간이 허락하면 빠진 권역을 간략히라도 직접 조사해 채운다.
- 최종 `reports/DATE.json`을 만들고 `index.json`의 `dates` 맨 앞에 DATE를 추가(중복 금지), `latest`와 `updated`를 갱신한다.
- 마지막 응답(예약 작업 알림에 표시됨)은 한국어 3줄 이내: 상태, 기사 수, 빈 칸 수.

## 2. 참고 매체

- 일반 뉴스: AP News, Reuters, Euronews, South China Morning Post(SCMP), POLITICO, The Economist, The Wall Street Journal
- 기술·과학: **TLDR 웹사이트**를 우선 확인한다. 최신 호 주소는 `https://tldr.tech/tech/YYYY-MM-DD`(평일 발행; 오늘 호가 없으면 직전 발행일 호), 필요하면 `https://tldr.tech/ai/YYYY-MM-DD`도 본다. 호 안의 기사 중 중요한 것을 고르고, 링크는 TLDR 페이지가 아니라 **TLDR이 가리키는 원문 기사 주소**를 쓴다(`outlet`은 원문 매체명). Ars Technica·WIRED로 보완한다. Gmail은 사용하지 않는다.
- The Economist, WSJ: 공개적으로 볼 수 있는 범위(제목·요약)만 이용한다. 구독 제한을 우회하지 않는다. 원문 링크는 붙인다.

## 3. 기사 선정·작성 원칙

- 세부 카테고리(그룹)마다 **중요도순 최소 3개, 최대 5개**. 지정 매체를 충분히 확인한 뒤 고른다. 검색이 부족하다는 이유로 줄이지 않는다.
- 정말 관련 보도가 없을 때만 `items`를 비우고 `note`에 `특이사항 없음 (확인 범위: …)`을 적는다.
- 같은 사건을 다룬 여러 매체 기사는 한 항목으로 묶고 `links`에 여러 원문을 단다. 같은 기사를 여러 칸에 반복해 개수를 채우지 않는다.
- 사건 발생일(`event_date`)과 기사 게시일(`published`)이 다르면 둘 다 적는다.
- 확인되지 않은 주장은 단정하지 않는다. 당사자 주장, 정부 발표, 언론 보도를 구별해 쓴다.
- 경제 지표는 기준 날짜·기간, 수치, 단위, 출처를 `figure`와 `source`에 적는다.
- `title_orig`은 원문 제목 그대로, `title_ko`는 한국어 제목. 링크는 반드시 **기사 페이지로 직접 연결되는 URL**(홈페이지·검색 결과 링크 금지). 링크는 실제로 열어 보거나 검색 결과에서 확인한 것만 쓴다.
- 요약(`summary`)은 한국어 2~3문장, 자기 말로 쓴다. 원문 문장을 옮겨 적지 않는다(인용은 15단어 미만, 기사당 1회 이내).
- 기준 기간: 원칙적으로 직전 24시간(전날 06:00 KST 이후) 보도. 큰 사건의 후속 보도는 포함 가능.

## 4. 파일 형식

### 기사 항목 (item)
```json
{
  "title_ko": "한국어 제목",
  "title_orig": "Original headline",
  "summary": "한국어 요약 2~3문장.",
  "published": "2026-11-21",
  "event_date": "2026-11-20",
  "figure": "3.4% (2026년 10월, 전년동월비)",
  "source": "통계청",
  "links": [{ "outlet": "Reuters", "url": "https://www.reuters.com/...", "title": "Original headline" }]
}
```
`event_date`, `figure`, `source`는 해당될 때만 넣는다.

### 그룹 (group)
```json
{ "name": "정치 및 외교", "items": [ /* item 3~5개 */ ], "note": "" }
```

### 섹션 (section)
```json
{ "id": "asia-pacific", "part": "2. 권역별 뉴스", "name": "1) 아시아-태평양", "groups": [ /* group */ ], "note": "" }
```

섹션 목록과 고정 순서(`id` / `part` / `name` / 그룹 이름):
| id | part | name | groups |
|---|---|---|---|
| headline | 1. 헤드라인 | 헤드라인 | 오늘의 헤드라인 (5~8개) |
| asia-pacific | 2. 권역별 뉴스 | 1) 아시아-태평양 | 정치 및 외교 · 경제 · 사회 및 문화 · 사건사고 |
| china | 2. 권역별 뉴스 | 2) 중국 | (같음) |
| usa | 2. 권역별 뉴스 | 3) 미국 | (같음) |
| eu-east | 2. 권역별 뉴스 | 4) 유럽 (1) 동유럽 | (같음) |
| eu-west | 2. 권역별 뉴스 | 4) 유럽 (2) 서유럽 | (같음) |
| middle-east | 2. 권역별 뉴스 | 5) 중동 | (같음) |
| africa | 2. 권역별 뉴스 | 6) 아프리카 | (같음) |
| economy | 3. 경제 | 경제 | 헤드라인 · 한국 및 미국 주요 거시경제 지표 · 한국 주식시장 · 미국 주식시장 · 주요 기업 |
| tech | 4. 기술과학 | 기술과학 | 주요 기사 (TLDR 웹사이트 우선, 5~8개) |

### parts/DATE-partN.json
```json
{ "date": "DATE", "part": 1, "generatedAt": "ISO8601", "sections": [ /* 담당 섹션 */ ], "notes": [] }
```

### reports/DATE.json (최종)
```json
{
  "date": "DATE",
  "title": "YYYY. MM. DD. Daily News Report",
  "generatedAt": "ISO8601 (+09:00)",
  "status": "complete | partial | failed",
  "counts": { "items": 0, "emptyGroups": 0 },
  "notes": ["TLDR 2026-11-21호 없음(주말): 직전 호와 Ars Technica·WIRED로 보완"],
  "sections": [ /* 위 표 순서대로 10개 */ ]
}
```

### runs/DATE.json
```json
{
  "date": "DATE",
  "part1": { "status": "ok | partial | failed", "startedAt": "", "finishedAt": "", "notes": [] },
  "part2": { "status": "", "startedAt": "", "finishedAt": "", "notes": [] },
  "part3": { "status": "", "startedAt": "", "finishedAt": "", "notes": [] }
}
```

### index.json
```json
{ "latest": "DATE", "updated": "ISO8601", "dates": ["DATE", "…최신순"] }
```

## 5. 점검 (푸시 전)

- JSON이 문법적으로 올바른지 확인한다(`python3 -m json.tool`).
- 그룹마다 items 3~5개인지, 아니면 `note`에 이유가 있는지 확인한다.
- 모든 item에 `links`가 1개 이상 있고 URL이 `https://`로 시작하며 기사 페이지 주소인지 확인한다.
- `status`는 빈 그룹이 3개 이하이고 모든 섹션이 있으면 `complete`, 그 밖에는 `partial`.
