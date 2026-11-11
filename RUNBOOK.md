# 디지털 조간 아침 발행 절차 (이 대화에서 실행)

원본: 저장소 RUNBOOK.md (작업 공간 사본 /home/claude/digital-jogan-runbook.md 가 없으면 이것을 쓴다)

사용자는 지켜보지 않는다. 질문하지 말고 끝까지 진행한다.

## 발행처
- GitHub 저장소 dohcjhw/digital-jogan (main) → https://dohcjhw.github.io/digital-jogan/
  - index.html 수정 금지. editions/YYYY-MM-DD.json 추가, editions/index.json 갱신.
  - 로컬 클론: /home/claude/digital-jogan (없거나 깨졌으면 add_repo(dohcjhw/digital-jogan, push) 후 `git clone --depth 1`).
- Claude 아티팩트 https://claude.ai/artifact/GEnhzFqUoHnpetWrsCwYT8
  - 로컬 원본: /home/claude/newspaper/index.html + edition.json. 작업 공간이 초기화돼 없으면 Artifact read(path "index.html")로 받아 <title>부터 </body> 직전까지만 잘라 /home/claude/newspaper/index.html 로 저장해 쓴다. 발행 전 반드시 action "read" + path "edition.json"으로 최신본을 읽는다(안 읽으면 거절됨). 거절되면 돌려받은 최신 HTML이 로컬 index.html과 같은지 확인하고 같으면 그대로 재발행.

## 순서
1. `TZ=Asia/Seoul date`로 오늘 날짜 확인. `git -C /home/claude/digital-jogan pull --rebase`. 오늘 파일이 이미 있으면 아티팩트만 맞추고 7단계로.
2. latest 호 JSON을 열어 스키마·issue 번호 확인(issue = latest + 1).
3. 취재 (WebSearch/WebFetch, 최근 24~48시간): AI, IT·통신, 미디어(OTT·방송·플랫폼), IPTV·유료방송, 홈IoT(스마트홈·Matter), 로봇(휴머노이드·산업/물류·서비스 로봇·로봇 AI·투자/상장·로보택시). 국내(한국어 검색)·해외(영어 검색) 대략 절반씩, 해외 0건 금지. 원문을 WebFetch로 읽고 확인한 사실·수치만. 1주일 넘은 기사 금지. 전날 기사 반복 금지(새 진전 없으면). 같은 기사 두 섹션 금지.
4. 작성 규칙
   - 전부 한국어. 해외는 번역·요약, origin "해외"/"국내".
   - 자기 말로 다시 쓰기(직접 인용 15단어 미만, 출처당 1회).
   - 기사마다 insight(기자의 시각) 3~5문장: 왜 중요한지, 국내 통신·미디어·홈IoT·로봇 사업자에게 갖는 의미, 볼 지표. 추측은 추측으로.
   - column(오늘의 해설) 3문단, numbers 실제 수치 4개, lead 1건(실제 비교 수치 있으면 chart).
   - sections 순서 고정: ai/AI/A2, media/미디어/A3, iptv/IPTV·유료방송/A4, homeiot/홈IoT/A5, robot/로봇/A6. 섹션당 기사 2~4 + 단신 1~4. 섹션 첫 기사가 1면 예고.
   - 모든 기사·단신에 실제 출처 이름+URL.
   - 고객 PII·개인 연락처 절대 금지.
   - date, dateLabel("2026년 11월 13일 금요일"), issue, edition "조간", updatedAt(+09:00).
5. GitHub: editions/<오늘>.json 쓰기, index.json에 {date,label,issue,headline} 추가·정렬·latest 갱신. python으로 유효성 + 섹션 순서 확인. 작성자 "디지털 조간 편집국" <noreply@anthropic.com>, 메시지 "디지털 조간 제N호 발행 (YYYY-MM-DD)" + 세션 attribution 줄. `git fetch` → rebase → push.
6. 아티팩트: 오늘 자 JSON을 /home/claude/newspaper/edition.json에 복사 → 위 규칙대로 read 후 publish(url, file_path=/home/claude/newspaper/index.html, files={"edition.json": ...}), icon 생략.
7. 다음 날 06:47 KST 실행을 send_later로 예약(at "<내일>T06:47:00[Asia/Seoul]", name "디지털 조간 아침 발행", initiation human_schedule, 메시지는 이 런북을 따르라는 내용). 예약이 이미 있으면 중복 예약하지 않는다.
8. SendUserMessage: 1면 톱 제목, 기사 수(국내/해외), 사이트 주소, 실패한 단계가 있으면 그 이유. PushNotification도 사용 가능하면 한 줄 알림.
