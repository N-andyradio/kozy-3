
공통 (두 프로젝트 모두)
□ Trigger 1개 이상
□ Action 2개 이상
□ 조건 분기(Filter/Router) 1개 이상
□ 각 분기 경로 실제 실행 결과 캡처 (갭쳐 화면 별도 업로드)


프로젝트 1 추가
□ 도구 2개 이상 사용(o)
□ 동일한 워크플로우 구조(o)
□ 비교 분석 보고서 (5개 이상 비교 항목o)

자동화 도구 비교 분석 보고서
Make vs Zapier — Google Form 기반 자동화 워크플로우 구현 비교

1. 개요
항목	내용
프로젝트명	자동화 도구 비교 구현
비교 도구	Make (구 Integromat) vs Zapier
구현 워크플로우	Google Form 응답 → Google Sheet 저장 → 점수 분기 → Discord 알림
분기 조건	점수 ≥ 80점 (합격 그룹) / 점수 < 80점 (불합격 그룹)
연동 서비스	Google Form, Google Sheet, Discord
2. 구현 워크플로우 설명
공통 시나리오 구조

[Google Form 제출]
        ↓
[Google Sheet 저장]
        ↓
  점수 조건 분기
   ↙           ↘
80점 이상      80점 미만
(합격 그룹)   (불합격 그룹)
   ↓               ↓
Discord 알림   Discord 알림
Google Form으로 응답이 제출되면 Google Sheet에 자동 저장되고,
점수 필드를 기준으로 두 그룹을 자동 분류하여
각 그룹에 맞는 Discord 메시지를 자동 전송하는 워크플로우를
Make와 Zapier로 각각 구현하였다.

3. 도구별 구현 방식
3-1. Make
UI 구조: 캔버스형 — 모듈을 자유롭게 배치하고 연결선으로 흐름 구성

구현 흐름:


[Google Sheets - Watch New Rows]
            ↓
        [Router]
       ↙         ↘
  [Filter]      [Filter]
  score ≥ 80    score < 80
       ↓              ↓
[Discord - Send  [Discord - Send
 a Message]       a Message]
"합격 메시지"    "불합격 메시지"
핵심 설정:

Google Form과 연결된 Google Sheet를 Watch New Rows 모듈로 감지
새로운 행(응답)이 추가될 때마다 시나리오 자동 실행
Router 모듈 1개로 두 경로 동시 분기
각 경로에 Filter 조건 개별 설정
경로 1: {{score}} >= 80
경로 2: {{score}} < 80
Discord 모듈에서 Bot 계정 연결 후 채널 및 메시지 설정
3-2. Zapier
UI 구조: 선형 단계형 — Trigger → Filter → Action 순서로 구성

구현 흐름:


[Zap 1 — 합격 그룹]
Google Sheets (New Spreadsheet Row)
        ↓
Filter: Score ≥ 80
        ↓
Discord: 합격 메시지 전송

[Zap 2 — 불합격 그룹]
Google Sheets (New Spreadsheet Row)
        ↓
Filter: Score < 80
        ↓
Discord: 불합격 메시지 전송
핵심 설정:

Google Form과 연결된 Google Sheet를 New Spreadsheet Row 트리거로 감지
새로운 행이 추가될 때마다 Zap 자동 실행
Filter 스텝에서 드롭다운으로 조건 설정
Score greater than or equal to 80
조건 불충족 시 해당 Zap 자동 중단
Discord 액션에서 Bot 연결 후 채널 및 메시지 설정
4. Discord 연동 워크플로우 설계
4-1. 전체 연동 구조

Google Form (응답 제출)
        ↓
Google Sheet (응답 자동 저장)
        ↓
  Make / Zapier (새 행 감지)
        ↓
    점수 조건 분기
   ↙           ↘
80점 이상      80점 미만
   ↓               ↓
Discord 채널   Discord 채널
합격 메시지    불합격 메시지
4-2. Make — Discord 연동 설정
단계	설정 내용
트리거 모듈	Google Sheets > Watch New Rows
분기 모듈	Router + 각 경로에 Filter 조건 설정
Discord 모듈	Discord > Send a Message
채널 설정	Discord Bot이 참여한 채널 선택
합격 메시지	✅ 점수: 80점 이상 메시지
불합격 메시지	❌ 점수: 08점 이하 매시지
4-3. Zapier — Discord 연동 설정
단계	설정 내용
트리거	Google Sheets > New Spreadsheet Row
필터	Filter > Score 조건 설정
Discord 액션	Discord > Send Channel Message
채널 설정	Discord Bot이 참여한 채널 선택
합격 메시지	✅ 점수: 80점 이상 메시지
불합격 메시지	❌ 점수: 08점 이하 매시지
4-4. Discord 전송 메시지 예시
합격 그룹 메시지:


✅ [합격 알림]
이름: 홍길동
점수: 92점
결과: 점수: 80점 이상 매시지


❌ [불합격 알림]
이름: 홍길동
점수: 65점
결과: 아쉽지만 재도전을 응원합니다 💪
5. 비교 분석
5-1. 주요 항목별 비교표
비교 항목	Make	Zapier
① UI 구조	캔버스형 자유 배치	선형 단계형 (Step by Step)
② 학습 난이도	중~상 (초기 진입 장벽 있음)	하~중 (직관적, 빠른 적응)
③ 분기 처리 방식	Router 1개로 단일 시나리오 내 다중 분기	조건별 Zap을 각각 분리하여 구성
④ 조건 설정 방식	수식 직접 입력 (>=, AND, OR 등)	드롭다운 선택 방식 (코드 불필요)
⑤ 자유도	높음 (복잡한 로직, 데이터 가공 가능)	보통 (단순 자동화에 최적화)
⑥ 직관성	보통 (학습 후 강력함 발휘)	높음 (누구나 바로 사용 가능)
⑦ 무료 플랜	월 1,000 operations	월 100 tasks
⑧ 지원 앱 수	1,000개 이상	6,000개 이상
⑨ Google Sheet 연동	Watch New Rows로 실시간 감지	New Spreadsheet Row로 실시간 감지
⑩ 복잡 워크플로우 관리	시나리오 1개로 통합 관리	Zap 여러 개로 분산 관리
5-2. 사용 경험 기반 분석
✅ Make — "자유도 높은 세밀한 설계"
Make는 캔버스 위에 모듈을 배치하고 연결하는 방식으로, 워크플로우를 시각적으로 설계하는 느낌이 강하다. Router 하나로 여러 분기를 한 시나리오에서 처리할 수 있어 복잡한 로직 구현에 유리하다. Google Sheet의 새 행을 감지하는 방식도 세밀하게 설정할 수 있으며, 변수 매핑과 데이터 가공을 직접 다룰 수 있어 개발자 친화적인 도구에 가깝다. 단, 처음 접하는 사용자에게는 모듈 개념과 데이터 흐름 이해가 선행되어야 하므로 진입 장벽이 존재한다.

✅ Zapier — "직관적인 단계별 자동화"
Zapier는 Trigger → Filter → Action의 선형 구조로, 누구나 쉽게 자동화 흐름을 이해하고 구성할 수 있다. Google Sheet 트리거 설정도 드롭다운 방식으로 간단하게 완료되며, 각 단계가 명확히 구분되어 있어 실수가 적고 유지보수가 쉽다. 다만, 복잡한 분기 로직은 여러 Zap으로 분리해야 하므로 관리 포인트가 늘어나는 단점이 있다.

6. 결론 및 도구 선택 가이드
상황	추천 도구
자동화 처음 입문	✅ Zapier
단순 알림 / 메시지 전송 자동화	✅ Zapier
조건 분기가 복잡한 경우	✅ Make
데이터 가공이 필요한 경우	✅ Make
다수의 앱 연동이 필요한 경우	✅ Zapier
비용 효율을 중시하는 경우	✅ Make
분기별 Discord 메시지 차별화	✅ Make
빠른 Discord 알림 구축	✅ Zapier
7. 총평
두 도구 모두 Google Form → Google Sheet → 점수 분기 → Discord 알림이라는 동일한 워크플로우를 성공적으로 구현할 수 있었다.

Make는 "내가 원하는 대로 설계하는 자동화"
Zapier는 "쉽고 빠르게 연결하는 자동화"

자동화 도구 선택은 사용자의 기술 수준과 워크플로우의 복잡도에 따라 달라지며, 두 도구를 상황에 맞게 병행 활용하는 것도 좋은 전략이다.

작성일: 2025년
비교 기준: 무료 플랜 기능 범위 내 구현


프로젝트 2 추가

프로젝트 2 분석 및 설명 📊
1. 자동화할 반복 업무 정의
"매일 뉴스를 직접 찾아보고, 읽고, 요약하는 반복 작업"

기존 방식	자동화 후
매일 직접 뉴스 사이트 방문	자동으로 RSS 수집
기사 하나씩 읽고 파악	AI가 자동 요약
중요 내용 따로 정리	디스코드로 자동 전송
2. 자동화 도구 선정
✅ 선정 도구: Make (구 Integromat)
선정 이유:

코딩 없이 노코드(No-Code) 방식으로 자동화 구현 가능
RSS, Google Gemini AI, Discord 등 다양한 서비스 연동 지원
스케줄 실행 기능으로 원하는 시간에 자동 실행 가능
시각적 워크플로우로 흐름 파악이 직관적
3. 워크플로우 흐름 설명


[RSS] ──────▶ [Google Gemini AI] ──────▶ [Discord]
뉴스 수집        AI 요약 생성           메시지 전송
📌 각 단계별 상세 설명
STEP 1 - RSS: Watch RSS feed items


역할: 뉴스 소스에서 새 기사 자동 감지
- 설정된 RSS 피드 URL을 주기적으로 모니터링
- 새로운 기사가 올라오면 다음 단계로 데이터 전달

STEP 2 - Google Gemini AI: Simple text prompt


역할: 수집된 뉴스 기사를 AI로 자동 요약
- RSS에서 받은 기사 제목/내용을 Gemini에 전달
- "이 기사를 요약해줘" 형태의 프롬프트 실행
- 요약된 텍스트를 다음 단계로 전달
- 실행 횟수: 6회 처리 확인됨 (✔6)
STEP 3 - Discord: Send a Message


역할: 요약된 뉴스를 디스코드 채널로 전송
- Gemini가 요약한 내용을 지정된 채널에 자동 발송
- 매일 오전 8:00 / 오후 8:00 자동 실행

4. 자동 실행 구조


⏰ 스케줄러
├── 매일 오전 08:00 자동 실행
└── 매일 오후 08:00 자동 실행
하단 바의 Daily at 오전 8:00, 오후 8:00 설정 확인 ✅

5. 전체 흐름 요약 다이어그램


┌─────────────────────────────────────────────────┐
│              뉴스 자동 수집 워크플로우              │
├─────────────────────────────────────────────────┤
│                                                  │
│  ⏰ 매일 8시        📡 RSS 피드                   │
│  자동 트리거  ──▶  새 기사 감지  ──▶  기사 데이터  │
│                                      ↓           │
│                              🤖 Gemini AI        │
│                              기사 내용 요약        │
│                                      ↓           │
│                              💬 Discord          │
│                              요약 메시지 전송      │
└─────────────────────────────────────────────────┘
6. 프로젝트 평가 포인트 체크 ✅
요구사항	충족 여부	내용
반복 업무 정의	✅	매일 뉴스 수집·요약 작업
도구 선정 + 이유	✅	Make, 노코드·다양한 연동
자동 실행 구조	✅	하루 2회 스케줄 실행
워크플로우 흐름	✅	3단계 파이프라인 구성
구현 화면 캡처	✅	Make 워크플로우 화면

