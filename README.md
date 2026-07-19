
공통 (두 프로젝트 모두)
□ Trigger 1개 이상
□ Action 2개 이상
□ 조건 분기(Filter/Router) 1개 이상
□ 각 분기 경로 실제 실행 결과 캡처


프로젝트 1 추가
□ 도구 2개 이상 사용
□ 동일한 워크플로우 구조
□ 비교 분석 보고서 (5개 이상 비교 항목)
자동화 도구 비교 분석 보고서
Make vs Zapier — Google Form 기반 자동화 워크플로우 구현 비교

1. 개요
항목	내용
프로젝트명	자동화 도구 비교 구현
비교 도구	Make (구 Integromat) vs Zapier
구현 워크플로우	Google Form 응답 기반 점수 분기 자동화 + Discord 알림 연동
분기 조건	점수 ≥ 80점 (합격 그룹) / 점수 < 80점 (불합격 그룹)
2. 구현 워크플로우 설명
공통 시나리오 구조
code
📋 복사
[Google Form 제출]
        ↓
  점수 조건 분기
   ↙           ↘
80점 이상      80점 미만
(합격 그룹)   (불합격 그룹)
        ↓           ↓
  Discord 알림  Discord 알림
Google Form으로 응답이 제출되면, 점수 필드를 기준으로 두 그룹을 자동 분류하고 각 그룹에 맞는 Discord 메시지를 자동 전송하는 워크플로우를 Make와 Zapier로 각각 구현하였다.

3. 도구별 구현 방식
3-1. Make
UI 구조: 캔버스형 — 모듈을 자유롭게 배치하고 연결선으로 흐름 구성

구현 흐름:

code
📋 복사
Google Forms (Watch Responses)
        ↓
   Router 모듈
   ↙              ↘
Filter (≥ 80)    Filter (< 80)
   ↓                  ↓
Discord (합격     Discord (불합격
 메시지 전송)      메시지 전송)
핵심 설정:

Watch Responses 모듈로 폼 응답 실시간 감지
Router 모듈 1개로 두 경로 동시 분기
각 경로에 Filter 조건 개별 설정
경로 1: {{score}} >= 80
경로 2: {{score}} < 80
Discord 모듈에서 Webhook URL 연결 후 메시지 템플릿 작성
3-2. Zapier
UI 구조: 선형 단계형 — Trigger → Filter → Action 순서로 구성

구현 흐름:

code
📋 복사
[Zap 1 — 합격 그룹]
Trigger: Google Forms (New Response)
   ↓
Filter: Score ≥ 80
   ↓
Action: Discord (합격 메시지 전송)

[Zap 2 — 불합격 그룹]
Trigger: Google Forms (New Response)
   ↓
Filter: Score < 80
   ↓
Action: Discord (불합격 메시지 전송)
핵심 설정:

New Response in Spreadsheet 트리거로 폼 응답 감지
Filter 스텝에서 드롭다운으로 조건 설정
조건 불충족 시 해당 Zap 자동 중단
Discord 액션에서 Webhook 또는 Bot으로 메시지 전송
4. Discord 연동 워크플로우 설계
4-1. Discord Webhook 설정 방법
code
📋 복사
Discord 서버 → 채널 설정 → 연동 → 웹후크 → 새 웹후크 생성
→ Webhook URL 복사
4-2. Make — Discord 연동 설계
단계	설정 내용
모듈 선택	Discord > Send a Message via Webhook
Webhook URL	Discord에서 복사한 URL 입력
합격 메시지	✅ {{이름}}님, 점수: {{점수}}점 — 합격입니다!
불합격 메시지	❌ {{이름}}님, 점수: {{점수}}점 — 재도전을 응원합니다!
전송 채널	Webhook 생성 시 지정한 채널로 자동 전송
Make 설계 흐름 (Discord 포함 전체):

code
📋 복사
[Google Forms - Watch Responses]
            ↓
        [Router]
       ↙         ↘
  [Filter]      [Filter]
  score ≥ 80    score < 80
       ↓              ↓
[Discord Webhook] [Discord Webhook]
 "합격 메시지"    "불합격 메시지"
4-3. Zapier — Discord 연동 설계
단계	설정 내용
Action 앱	Discord
연동 방식	Webhook URL 또는 Discord Bot 연결
합격 메시지	✅ {{이름}}님, 점수: {{점수}}점 — 합격입니다!
불합격 메시지	❌ {{이름}}님, 점수: {{점수}}점 — 재도전을 응원합니다!
전송 채널	Webhook 생성 시 지정한 채널로 자동 전송
Zapier 설계 흐름 (Discord 포함 전체):

code
📋 복사
[Zap 1]
Google Forms (New Response)
        ↓
Filter: Score ≥ 80
        ↓
Discord: "✅ 합격 메시지 전송"

[Zap 2]
Google Forms (New Response)
        ↓
Filter: Score < 80
        ↓
Discord: "❌ 불합격 메시지 전송"
4-4. Discord 전송 메시지 예시
합격 그룹 메시지:

code
📋 복사
✅ [합격 알림]
이름: 홍길동
점수: 92점
결과: 합격입니다! 수고하셨습니다 🎉
불합격 그룹 메시지:

code
📋 복사
❌ [재응시 안내]
이름: 홍길동
점수: 65점
결과: 아쉽지만 재도전을 응원합니다 💪
5. 비교 분석
5-1. 주요 항목별 비교표 (5가지 이상)
비교 항목	Make	Zapier
① UI 구조	캔버스형 자유 배치	선형 단계형 (Step by Step)
② 학습 난이도	중~상 (초기 진입 장벽 있음)	하~중 (직관적, 빠른 적응)
③ 분기 처리 방식	Router 1개로 단일 시나리오 내 다중 분기	조건별 Zap을 각각 분리하여 구성
④ 조건 설정 방식	수식 직접 입력 (>=, AND, OR 등)	드롭다운 선택 방식 (코드 불필요)
⑤ 자유도	높음 (복잡한 로직, 데이터 가공 가능)	보통 (단순 자동화에 최적화)
⑥ 직관성	보통 (학습 후 강력함 발휘)	높음 (누구나 바로 사용 가능)
⑦ 무료 플랜	월 1,000 operations	월 100 tasks
⑧ 지원 앱 수	1,000개 이상	6,000개 이상
⑨ Discord 연동	Webhook 모듈 직접 구성	Discord 앱 또는 Webhook 액션
⑩ 복잡 워크플로우 관리	시나리오 1개로 통합 관리	Zap 여러 개로 분산 관리
5-2. 사용 경험 기반 분석
✅ Make — "자유도 높은 세밀한 설계"
Make는 캔버스 위에 모듈을 배치하고 연결하는 방식으로, 워크플로우를 시각적으로 설계하는 느낌이 강하다. Router 하나로 여러 분기를 한 시나리오에서 처리할 수 있어 복잡한 로직 구현에 유리하다. 변수 매핑, 데이터 가공, 조건 수식 등을 직접 다룰 수 있어 개발자 친화적인 도구에 가깝다. 단, 처음 접하는 사용자에게는 모듈 개념과 데이터 흐름 이해가 선행되어야 하므로 진입 장벽이 존재한다.

✅ Zapier — "직관적인 단계별 자동화"
Zapier는 Trigger → Filter → Action의 선형 구조로, 누구나 쉽게 자동화 흐름을 이해하고 구성할 수 있다. 각 단계가 명확히 구분되어 있어 실수가 적고 유지보수가 쉽다. 조건 설정도 드롭다운 방식이라 별도 학습 없이 바로 사용 가능하다. 다만, 복잡한 분기 로직은 여러 Zap으로 분리해야 하므로 관리 포인트가 늘어나는 단점이 있다.

6. 결론 및 도구 선택 가이드
상황	추천 도구
자동화 처음 입문	✅ Zapier
단순 알림 / 메시지 전송 자동화	✅ Zapier
조건 분기가 복잡한 경우	✅ Make
데이터 가공이 필요한 경우	✅ Make
다수의 앱 연동이 필요한 경우	✅ Zapier
비용 효율을 중시하는 경우	✅ Make
Discord 알림 등 단순 연동	✅ Zapier
분기별 Discord 메시지 차별화	✅ Make
7. 총평
두 도구 모두 Google Form 트리거 → 점수 기반 분기 → Discord 알림이라는 동일한 워크플로우를 성공적으로 구현할 수 있었다.

Make는 "내가 원하는 대로 설계하는 자동화"
Zapier는 "쉽고 빠르게 연결하는 자동화"

자동화 도구 선택은 사용자의 기술 수준과 워크플로우의 복잡도에 따라 달라지며, 두 도구를 상황에 맞게 병행 활용하는 것도 좋은 전략이다.

작성일: 2025년
비교 기준: 무료 플랜 기능 범위 내 구현
프로젝트 2 추가
□ 반복 업무 1개 정의
□ 도구 선정 이유 작성
□ 워크플로우 설계 문서
□ Trigger 발생 시 자동 실행
🗺️ 단계별 수행 계획
STEP 1. 사전 준비 (환경 세팅)
1-1. 도구 계정 생성

code
📋 복사
□ Make (make.com) 무료 계정 가입
□ Zapier (zapier.com) 무료 계정 가입
  → 둘 다 무료 플랜으로 미션 완수 가능
1-2. 연동할 서비스 계정 준비

code
📋 복사
□ Google 계정 (Sheets, Forms, Gmail 등)
□ Slack 또는 Discord 계정 (알림용)
  → 권장 조합: Google Forms + Google Sheets + Slack/Discord
💡 팁: 계정이 많을수록 복잡해지니 Google + Slack 조합을 추천합니다!

STEP 2. 프로젝트 1 - 워크플로우 설계
2-1. 구현할 워크플로우 1개 결정

아래 예시 중 하나를 선택하거나 직접 설계하세요.

code
📋 복사
추천 예시 (난이도 ★★☆)
Google Form 제출
    → 조건 분기 (점수/응답 기준)
        → [분기 A] Google Sheets 기록
        → [분기 B] Slack/Discord 알림
2-2. 워크플로우 구조 문서화 (먼저 종이에 그려보기)

code
📋 복사
Trigger: _______________
    ↓
Filter/Router: _______________
    ↓              ↓
Action 1:___   Action 2:___
STEP 3. 프로젝트 1 - Make로 구현
code
📋 복사
□ Make 시나리오 새로 만들기
□ Trigger 모듈 추가 및 연결
□ Router/Filter 모듈 추가
□ Action 모듈 2개 이상 추가
□ 테스트 실행 → 각 분기 1회 이상 실행 확인
□ 화면 캡처 저장
    - 전체 워크플로우 구성 화면
    - 실행 결과(히스토리) 화면
    - 각 분기 실행 결과 화면
STEP 4. 프로젝트 1 - Zapier로 동일 구현
code
📋 복사
□ Zapier Zap 새로 만들기
□ 동일한 Trigger 설정
□ Filter 조건 설정
□ Action 2개 이상 추가
□ 테스트 실행 → 각 분기 1회 이상 실행 확인
□ 화면 캡처 저장
    - 전체 워크플로우 구성 화면
    - Task History 화면
    - 각 분기 실행 결과 화면
STEP 5. 프로젝트 1 - 비교 분석 보고서 작성
5개 이상 비교 항목을 표로 정리

비교 항목	Make	Zapier
UI/UX		
설정 난이도		
연동 서비스 범위		
무료 플랜 범위		
실행 로그 확인		
조건 분기 방식		
...		
code
📋 복사
보고서 포함 항목
□ 사용 도구 이름
□ 구현 과정 요약
□ 비교 항목 5개 이상 (표 형식 권장)
□ 각 도구 장단점 정리
□ 어떤 상황에서 어떤 도구가 적합한지 의견
STEP 6. 프로젝트 2 - 자유 주제 설계
6-1. 자동화할 반복 업무 정의

code
📋 복사
스스로에게 질문해보세요:
- 매일/매주 반복하는 귀찮은 작업이 있나요?
- 복사-붙여넣기를 자주 하나요?
- 알림을 수동으로 보내고 있나요?
추천 주제 예시

code
📋 복사
① 특정 키워드 이메일 수신 → Sheets 기록 + Slack 알림
② 새 유튜브 영상 업로드 → Discord 자동 공유
③ 구글 폼 설문 제출 → 결과 분류 + 이메일 발송
④ RSS 피드 새 글 → 요약 후 메신저 전송
6-2. 도구 선정 및 이유 작성

code
📋 복사
□ 도구 선정: Make / Zapier / 기타
□ 선정 이유 작성 (무료 범위, UI, 기능 등 근거 포함)
STEP 7. 프로젝트 2 - 구현 및 문서화
code
📋 복사
□ 워크플로우 흐름 다이어그램 작성
□ 실제 구현 (Trigger 자동 실행 필수!)
□ 테스트 실행 확인
□ 화면 캡처 저장
    - 워크플로우 구성 화면
    - 실행 결과 화면
STEP 8. 최종 제출물 정리
code
📋 복사
□ 민감정보 마스킹 확인 (API Key, 이메일 등)
□ 프로젝트 1 캡처 + 보고서 정리
□ 프로젝트 2 캡처 + 설계 문서 정리
□ Markdown 또는 PDF로 최종 정리
