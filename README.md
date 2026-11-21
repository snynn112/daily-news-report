# daily-news-report

스케줄러 앱의 **Daily News Report** 탭이 읽는 리포트 보관함입니다.
매일 새벽(한국 시간) Claude 예약 작업이 `GUIDE.md` 지침에 따라 주요 외신을 조사해 한국어 리포트를 올립니다.

- `reports/YYYY-MM-DD.json` — 날짜별 최종 리포트
- `index.json` — 리포트 날짜 목록
- `runs/YYYY-MM-DD.json` — 실행 기록(성공·부분 완료·실패)
- `parts/` — 단계별 중간 결과
