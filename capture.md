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
| eval-manage | 양산여중 평가 관리 | https://yangsan-exam-system.vercel.app | 필요 |
| did-guide | 전자칠판 DID 안내 | https://ysgm-did.web.app | 불필요 |
| secretary | 쫑아빠 비서 | https://jjongappa-secretary.web.app | 필요 |
| trading-desk | 트레이딩 데스크 | 로컬: `cd ~/toss-auto-trader && npm run dash` | 로컬 |

## 절대 규칙

- 검수 승인 전 push 금지 (공개 저장소 — 한 번 올라간 개인정보는 회수 불가)
- 학생 이름·학번·성적·기록, 실제 자산·계좌·보유 종목, 연락처가 보이는 화면은 사용 금지
