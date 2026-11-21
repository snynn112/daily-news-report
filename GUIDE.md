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
- `parts/DATE-part1.json`, `parts/DATE-part2.json`이 없으면 빠진 권역을 직접 조사해 채운다(그룹당 최소 3건 목표). 시간이 모자라 못 채운 권역은 그룹 `items`를 비워 두고 `status`를 `partial`로 하며, 사유는 `runs/DATE.json`과 리포트 최상위 `notes`에만 적는다.
- 최종 `reports/DATE.json`을 만들고 `index.json`의 `dates` 맨 앞에 DATE를 추가(중복 금지), `latest`와 `updated`를 갱신한다.
- 마지막 응답(예약 작업 알림에 표시됨)은 한국어 3줄 이내: 상태, 기사 수, 빈 칸 수.

## 2. 참고 매체와 검색 방법

### 2-1. 검색 범위: 신뢰도 높은 외신·주요 언론 전체

특정 몇 곳에 한정하지 않고 **신뢰도 높은 국제 언론과 각국 주요 언론 전체**에서 찾는다. 아래 매체를 우선 살피되, 같은 수준의 신뢰 매체라면 어디든 쓸 수 있다.

| 분야 | 우선 매체 (예시) |
|---|---|
| 국제 전반 | Reuters, BBC, The Guardian, The New York Times, The Washington Post, The Wall Street Journal, Financial Times, The Economist, Bloomberg, Al Jazeera, Euronews, NPR, PBS NewsHour, CNN, ABC News, CBS News, NBC News, DW, France 24, Le Monde |
| 아시아-태평양·중국 | South China Morning Post, Nikkei Asia, NHK, Kyodo, The Japan Times, Caixin, The Straits Times, ABC(호주), The Hindu, Channel NewsAsia |
| 한국 | Korea JoongAng Daily, The Korea Herald, The Korea Times, KBS, 조선·중앙·동아·한겨레·경향, 한국경제·매일경제, 정부 정책브리핑(korea.kr) |
| 미국 정치 | The Hill, Axios, NPR, PBS, 주요 방송사 본사 기사 |
| 중동·아프리카 | Al Jazeera, BBC, Reuters, The National, Times of Israel, Haaretz, Africa News, Daily Maverick, The East African |
| 경제·시장 | Bloomberg, Reuters, Financial Times, WSJ, CNBC, Nikkei, 각국 정부·중앙은행·통계기관 발표 |
| 기술과학 | TLDR(우선), WIRED, Ars Technica, The Verge, MIT Technology Review, Nature·Science 뉴스 |

- **접근이 막힌 매체:** AP News, POLITICO, 연합뉴스는 Claude의 검색·열람 도구에서 차단되어 있다. 웹 검색 `allowed_domains`에 넣지 않는다(검색 요청 전체가 거부됨). 일반 검색 결과에 이들 원문 주소가 직접 나오면 쓰되, **재게재 사이트·캐시·미러로 우회하지 않는다.**
- 검색은 기본적으로 `allowed_domains` 없이 하고 결과에서 신뢰 매체 기사만 고른다. 특정 매체를 좁혀 찾고 싶으면 접근 가능한 도메인만 `allowed_domains`에 넣는다.
- **한 매체 쏠림 금지:** 한 매체가 리포트 전체 링크의 25%를 넘지 않게 하고, 한 그룹(3~5건)의 기사가 모두 같은 매체에서 나오지 않게 한다. 권역 기사는 가능하면 그 지역 사정에 밝은 매체와 국제 통신·방송을 섞는다.
- 유료 기사(Bloomberg, FT, WSJ 등)는 공개된 제목·요약만 이용하고 구독 제한을 우회하지 않는다. 링크는 원문 주소.

### 2-2. 쓰지 않는 출처

- 투자 정보·주식 홍보 사이트(The Motley Fool, StockTitan, The Closing Print, Investing.com, FXStreet, InvestmentNews, Benzinga, Seeking Alpha 등)
- 지역 TV·라디오 계열사의 통신 재게재(ABC13, WSMV, WCAX, AJC 재게재 등)와 Yahoo·MSN 등 포털 재게재
- 뉴스 요약·다이제스트·주간 모음(The Neuron, SingularityHub 주간 모음 등), 블로그, 보도자료 배포 사이트(PR Newswire 등), 콘텐츠 농장
- 국영 선전 매체를 단독 출처로 쓰는 것(Global Times, RT, KCNA 등은 '당국 입장'을 전할 때만, 다른 매체와 함께)
- 단독 출처의 미확인 의혹 보도
- 경제 지표·시장 수치는 공식 기관 발표나 Bloomberg·Reuters·FT·WSJ·한국 주요 경제지 보도를 기준으로 하고, 기준 시점·단위·출처를 적는다.

### 2-3. 기준 기간

- 직전 24시간 보도를 우선하고, 최대 48시간까지만 쓴다. 그보다 오래된 기사는 쓰지 않는다(큰 사건의 최신 후속 보도는 예외). 48시간 안에 3건이 정말 없으면 있는 만큼만 쓴다.

### 2-4. 웹페이지 여는 방법 (중요)

- **주소를 직접 만들거나 추측해서 열지 않는다.** 자동 실행에서는 권한 확인을 받을 수 없어 실패한다(PROVENANCE_REQUIRED).
- 웹페이지는 (1) 예약 작업 지시문에 적힌 주소, (2) 웹 검색 결과에 나온 주소, (3) 이미 연 페이지 안의 링크만 연다.
- 리포트의 기사 링크는 검색 결과나 연 페이지에서 실제로 확인한 원문 주소만 쓴다. 다른 사이트 재게재본 주소는 쓰지 않는다.
- 기술과학: 지시문에 적힌 TLDR 보관함 페이지(https://tldr.tech/tech/archives, https://tldr.tech/ai/archives)를 열고 그 안의 최신 호 링크를 따라간다(평일 발행, 주말·휴일에는 직전 발행일 호). 링크는 TLDR 페이지가 아니라 TLDR이 소개한 원문 기사 주소를 쓴다(`outlet`은 원문 매체명). WIRED로 보완한다. Gmail은 사용하지 않는다.

## 3. 기사 선정·작성 원칙

- 세부 카테고리(그룹)마다 **중요도순 최소 3개, 최대 5개**. 검색 범위가 넓으니 여러 매체를 충분히 확인한 뒤 고른다. 검색이 부족하다는 이유로 줄이지 않는다.
- 정말 관련 보도가 없을 때만 `items`를 빈 배열로 둔다(앱에서 그 칸은 자동으로 숨겨진다).
- **앱에는 기사만 보인다.** 섹션·그룹의 `note`는 항상 빈 문자열 `""`로 둔다. 조사 경위·빈 칸 사유·대체 내역 같은 작업 메모는 `runs/DATE.json`의 `notes`에만 적는다.
- **요약에도 작업 메모를 쓰지 않는다.** "확인하지 못했다", "~로 대체함", "배경으로 포함함", "며칠 전 기사로", "재게재본" 같은 조사 과정 설명이나 매체 사정은 `summary`·`title_ko`에 넣지 않는다. 요약은 뉴스 내용만 담는다.
- 같은 사건을 다룬 여러 매체 기사는 한 항목으로 묶고 `links`에 여러 원문을 단다. 같은 기사를 여러 칸에 반복해 개수를 채우지 않는다.
- 사건 발생일(`event_date`)과 기사 게시일(`published`)이 다르면 둘 다 적는다.
- 확인되지 않은 주장은 단정하지 않는다. 당사자 주장, 정부 발표, 언론 보도를 구별해 쓴다.
- 경제 지표는 기준 날짜·기간, 수치, 단위, 출처를 `figure`와 `source`에 적는다.
- `title_orig`은 원문 제목 그대로, `title_ko`는 한국어 제목. 링크는 반드시 **기사 페이지로 직접 연결되는 URL**(홈페이지·검색 결과 링크 금지). 링크는 실제로 열어 보거나 검색 결과에서 확인한 것만 쓴다.
- 요약(`summary`)은 한국어 2~3문장, 자기 말로 쓴다. 원문 문장을 옮겨 적지 않는다(인용은 15단어 미만, 기사당 1회 이내).

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
`event_date`, `figure`, `source`는 해당될 때만 넣는다. `links`의 `outlet`은 매체 이름만 쓴다(예: `Reuters`; `AP (재게재: …)` 같은 표기 금지).

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
| tech | 4. 기술과학 | 기술과학 | 주요 기사 (TLDR 우선, WIRED 보완, 5~8개) |

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
  "notes": ["(앱에는 표시되지 않는 작업 기록) TLDR 2026-11-21호 없음(주말): 직전 호와 Ars Technica로 보완"],
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
- 그룹마다 items 3~5개인지 확인한다. 모든 섹션·그룹의 `note`가 빈 문자열인지, `summary`에 조사 과정 메모가 섞이지 않았는지 확인한다.
- 모든 item에 `links`가 1개 이상 있고 URL이 `https://`로 시작하며 기사 페이지 주소인지 확인한다.
- 링크 도메인 분포를 세어 한 매체가 25%를 넘지 않는지, 2-2의 '쓰지 않는 출처'가 섞이지 않았는지 확인하고, 걸리면 2-1의 신뢰 매체 기사로 바꾼다.
- `status`는 빈 그룹이 3개 이하이고 모든 섹션이 있으면 `complete`, 그 밖에는 `partial`.
