# 스크린샷 갤러리 유지보수 가이드

포트폴리오의 "미리보기" 갤러리에 프로젝트 스크린샷을 추가·갱신하는 방법.
Claude Code에게 "capture.md 읽고 ○○ 프로젝트 스크린샷 다시 찍어서 배포해줘"라고 요청하면 된다.

## 구조

- `index.html` — 단일 파일. 하단 `<script>`의 `GALLERY` 객체가 갤러리 목록.
- `screenshots/` — webp 스크린샷. 파일명: `슬러그-순번.webp` (1부터).
- 촬영 도구는 맥미니 `~/작업 포트폴리오/capture/`에 있음:
  - `shoot.js` — 헤드리스 촬영: `node shoot.js shots/슬러그-1.png <URL> [대기ms]`
  - `shoot-click.js` — 특정 텍스트 클릭 후 촬영 (탭 전환용)
  - `login-shoot.js` — 로그인 필요 앱용. 브라우저 창을 띄우고 대기,
    `shots/슬러그.GO` 파일 생성 시 현재 화면 촬영, `shots/슬러그.QUIT` 생성 시 종료
  - `towebp.js` — `shots/*.png` → `../portfolio/screenshots/*.webp` (품질 80, 300KB 이하 자동)
  - `test-gallery.js` — 갤러리 동작 자동 테스트

## 새 프로젝트 추가 순서

1. 1920×1080으로 촬영 (`shoot.js` 또는 로그인 필요 시 `login-shoot.js`)
2. **개인정보 검수**: 학생 이름·성적, 실제 자산·계좌, 연락처가 보이면 중단하고 의뢰인에게 보고
3. `node towebp.js`로 webp 변환
4. `index.html`의 `GALLERY` 객체에 항목 추가:
   ```js
   '슬러그': { title: '프로젝트명', count: 장수 },
   ```
5. 해당 프로젝트 카드의 `.stack` 아래에 버튼 추가:
   ```html
   <button class="preview-btn" data-gallery="슬러그">미리보기 →</button>
   ```
6. `node test-gallery.js`로 동작 확인
7. **의뢰인 검수 승인 후에만** commit & push

## 프로젝트 URL 목록

| 슬러그 | 프로젝트 | URL | 로그인 |
|--------|---------|-----|--------|
| retire-simulator | 나의 은퇴 시뮬레이터 | https://y07e.github.io/retire-sim/ | 불필요 |
| eval-manage | 양산여중 평가 관리 | https://exam-manager-navy.vercel.app | 관리자만 |
| did-guide | 전자칠판 DID 안내 | https://ysgm-did.web.app | 불필요 |
| secretary | 쫑아빠 비서 | https://jjongappa-secretary.web.app | 필요(구글) |
| trading-desk | 트레이딩 데스크 | 로컬: `cd ~/toss-auto-trader && npm run dash` | 로컬 |
| free-semester | 자유학기제 일정 안내 | https://free-semester-schedule.vercel.app | 불필요 · 교사 이름 제거 필요 |
| did-class19 | 전자칠판 우리반(1-9) | https://ysgm-did.web.app/display/?grade=1&class=9 | 불필요 · 교사 이름 블러 필요 |
| travel-app | 여행 일정 관리 앱 | https://travel-route-tracker.vercel.app | 불필요(보기 모드) |
| moral-progress | 도덕 진도 관리 | https://y07e.github.io/moral-progress-tracker/ | 불필요 |
| stock-signal | 주식 신호 대시보드 | https://stock-signal-dashboard.onrender.com | 불필요 · 슬립 해제에 1~2분 |
| school-briefings | 학교 전달사항 안내 | https://school-briefings.vercel.app | 학교 네트워크 전용 · 학교에서 찍은 캡처를 clipboard로 전달받아 저장 |
| rehab-tracker | 재활 운동 기록 | https://rehab-tracker-ebon.vercel.app | 불필요 |
| travel-english | 우리 부부 세계여행 영어 | https://travel-english-blond.vercel.app (로컬 http://localhost:3021) | 구글 로그인 · Claude 브라우저 패널에서 로그인 후 html2canvas로 캡처 |
| did-admin | 전자칠판 관리자 | 의뢰인이 캡처한 화면을 clipboard로 전달 | 구글 로그인 |
| hakgeup-note | 학급노트 | 의뢰인이 캡처한 화면을 clipboard로 전달 | ⚠️ 학생 이름 27개·담임 이름을 sharp 오버레이로 가명 처리한 뒤 게시 (원본 png는 삭제) |

### 미촬영 (사유)

| 프로젝트 | 사유 |
|---------|------|
| 자산관리 포트폴리오 | ⚠️ 로그인 없이 실제 자산 노출 — 앱에 로그인 보호 추가 후 재촬영 |
| 은퇴 후 현금흐름 설계 | 이메일/비밀번호 로그인 필요 (https://retirement-cashflow-planner.vercel.app) |
| 시험지 열람 신청 | 구글 로그인 — 로그인 화면만 확보됨 |

### 교사 이름 마스킹 방법

- DID 계열: `page.addStyleTag({content:'.subject-teacher{filter:blur(8px)}'})` 적용 후 촬영
- 자유학기제: React 재렌더링 때문에 `setInterval`로 `(이름)` 패턴 텍스트를 반복 제거 후 촬영
- 구글 로그인 앱: 자동화 브라우저는 구글이 차단 → 사용자 Chrome 창을 AppleScript로 {0,0,1920,1250} 배치 후
  `screencapture -x` 전체 캡처 → sharp로 (0,300)부터 3840×2160 크롭(물리 2x) → 1920×1080 리사이즈

## 절대 규칙

- 검수 승인 전 push 금지 (공개 저장소 — 한 번 올라간 개인정보는 회수 불가)
- 학생 이름·학번·성적·기록, 실제 자산·계좌·보유 종목, 연락처가 보이는 화면은 사용 금지
