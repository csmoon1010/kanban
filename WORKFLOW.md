# WORKFLOW

---

## 1. 프롬프트

```
@../../day13/todo/에서 AGENTS.md와 CLAUDE.md를 이 폴더로 복사해줘. 기존 프로젝트에 의존적인 내용은 모두 삭제하고 중립적인 규칙만 남겨줘. 이번에 만들 어플리케이션은 칸반보드야.
```

### 결과
- `../../day13/todo/`의 `AGENTS.md`, `CLAUDE.md`를 이 폴더로 복사했다.
- 원본 내용을 검토한 결과 todo 프로젝트에 의존적인 내용(특정 파일·기술 스택·명령어 등)이 없어 규칙은 그대로 유지했다. (기본 규칙, Git 규칙, WORKFLOW.md 작업 기록 규칙)
- `CLAUDE.md`는 `@AGENTS.md`를 참조하는 한 줄 그대로 유지했다.
- 작업 기록 규칙에 따라 `WORKFLOW.md`를 새로 만들고 이 항목을 기록했다.

---

## 2. 프롬프트

```
HTML과 css, js만으로 구성되는 칸반보드를 만들려고 해. 보드에는 To-Do, In-Progress, Done의 세가지 컬럼이 있어. 카드를 드래그해서 각 컬럼간 이동이 가능하게 해야 돼. HTML과 css, js는 각각 다른 파일로 저장해줘. UI에는 머티리얼을 적용해주고 반응형으로 모바일에서도 문제없이 보이게 해줘. 테스트는 jest로 구성하고 테스트가 완료되어야 작업이 끝나는 거야.
```

### 결과
- 플랜 모드에서 구현 계획만 작성했다. 코드는 아직 작성하지 않았다.
- 계획 요약:
  - 파일 구성: `index.html` / `style.css` / `board.js`(순수 상태 로직) / `app.js`(DOM·드래그·저장)
  - 저장: `localStorage`
  - Material: Material Design 3 스타일을 CSS로 직접 구현
  - 드래그: Pointer Events로 마우스와 터치 지원
  - 반응형: 데스크톱 3컬럼, 모바일은 세로로 쌓기
  - 테스트: Jest + jsdom

---

## 3. 프롬프트

```
이 계획을 PLAN.md로 저장해줘
```

### 결과
- 2번 프롬프트에서 작성한 구현 계획을 프로젝트 루트의 `PLAN.md`로 저장했다.
- 검증 단계의 WORKFLOW 항목 번호를 "2번"에서 "다음 번호"로 바꿨다.

---

## 4. 프롬프트

```
PLAN.md를 기반으로 다음 문서들을 만들어줘: PRD, TRD, User Flow, Database Design, Design System, TASKS. 모두 md로 작성해줘.
```

### 결과
- `PLAN.md`를 바탕으로 기획·설계 문서 6개를 만들었다.
  - `PRD.md`: 목표, 비목표, 사용자 스토리, 기능·비기능 요구사항, 출시 기준
  - `TRD.md`: 기술 스택, 아키텍처(`board.js` 순수 로직 / `app.js` DOM), 모듈 API, Pointer Events 드래그 엔진, 테스트 전략
  - `USER_FLOW.md`: 화면 구성, 전체 흐름과 드래그 시퀀스(Mermaid), 추가·편집·삭제·드래그·키보드 이동 흐름, 예외 흐름
  - `DATABASE_DESIGN.md`: `localStorage` 키 `kanban.board`, 정규화 스키마(`cards` 맵 + `columns` id 배열, `version: 1`), 무결성 규칙, 마이그레이션 방침
  - `DESIGN_SYSTEM.md`: M3 색상·타이포·shape·elevation·motion 토큰(라이트/다크), 컴포넌트 규격, 반응형 구간, 접근성 체크리스트
  - `TASKS.md`: Phase 0~6 작업 목록. 단계마다 `npm test` 통과를 완료 조건으로 둔다.
- 결정 사항:
  - 카드의 컬럼과 순서는 `columns` 배열 위치로만 표현한다.
  - 제목은 100자, 설명은 1000자로 제한한다.
  - 모바일에서는 컬럼을 세로로 쌓고, 600~899px 구간은 가로 스크롤 보드로 만든다.

---

## 5. 프롬프트

```
문서를 모두 확인했으니 이제 TASKS.md의 순서대로 작업을 진행해줘
```

### 결과
- TASKS.md의 Phase 0~6을 순서대로 진행했다.
  - Phase 0: `package.json`(jest, jest-environment-jsdom), `.gitignore`, `npm install`, 스모크 테스트로 Jest 동작 확인(확인 후 삭제)
  - Phase 1: `board.js`(불변 순수 함수, 에러 `code` 부여)와 `tests/board.test.js`
  - Phase 2: `index.html`(앱 바, 3컬럼, FAB, 카드 메뉴, 추가·편집 다이얼로그, 삭제 확인 다이얼로그, `aria-live` 영역)
  - Phase 3: `style.css`(M3 토큰과 다크모드, 상태 레이어, 카드·고스트·플레이스홀더, 버튼·메뉴·다이얼로그·Outlined Text Field, 반응형 3구간, reduced-motion)
  - Phase 4·5: `app.js`(렌더링, 다이얼로그, 메뉴 키보드 탐색, Pointer Events 드래그, `localStorage` 저장)와 `tests/app.test.js`
  - Phase 6: `README.md` 작성, `TASKS.md` 체크박스 갱신
- 드래그 구현:
  - 마우스는 4px 이동부터 드래그로 본다.
  - 터치는 200ms 롱프레스 후 드래그가 시작되고, 그 전 10px 넘게 움직이면 스크롤로 본다.
  - Esc, pointercancel, 보드 밖에 놓기는 취소로 처리하고, 드래그 직후의 click은 무시한다.
  - 화면 가장자리에서 자동 스크롤하고, 고스트 이동은 rAF로 묶는다.
- 결정 사항:
  - `moveCard`, `updateCard`는 변경이 없으면 같은 보드 객체를 돌려준다. 앱은 이를 이용해 저장을 생략한다.
  - 메뉴가 열린 상태에서 메뉴 밖을 클릭하면 메뉴만 닫는다.
- 테스트 결과:
  - `npm test`: 2개 스위트, 103개 테스트 모두 통과
  - 커버리지: `board.js` 문장 99%, `app.js` 문장 93%
- 미완료: T6-1(브라우저 수동 점검), T6-2(라이트·다크 대비 확인)는 이 환경에 브라우저가 없어 사용자 확인으로 남겼다.

---

## 6. 프롬프트

```
T6-1, T6-2는 완료했어. 그런데 앞으로는 브라우저에서의 테스트도 네가 할 수 있도록 playwright를 사용했으면 좋곘어. 그리고 테스트시에는 반드시 red-green-refactor의 순서로, test first의 순서로 진행하는 걸 원칙으로 하자. playwright를 설치하고 User Flow 기반으로 E2E 테스트를 추가하는 걸로 계획을 변경하고 구현할 거야. 내가 입력한 프롬프트 기반으로 설계문서들을 변경해줘. TASKS.md에서는 내가 직접 해야 하는 일이 있을 것 같은데 이 부분은 남은 작업 중 가장 앞쪽에 몰아서 배치해줘. 그후 네가 한번에 구현할 수 있게 하자.
```

### 결과
- 설계 문서를 Playwright E2E 도입과 TDD 원칙에 맞게 고쳤다. 코드와 설치는 아직 하지 않았다.
  - `PLAN.md`: "계획 변경" 섹션을 추가했다.
  - `PRD.md`: G5, NFR-8(E2E), NFR-9(TDD)를 넣고 출시 기준을 갱신했다.
  - `TRD.md`: 스택에 Playwright와 정적 서버를 넣었다.
    - 파일 구조에 `e2e/`, `playwright.config.js`를 넣고, 스크립트 `test`/`test:e2e`/`test:all`을 정의했다.
    - §5.1 TDD, §5.2 테스트 레벨, §5.3 E2E 구성(desktop/mobile 프로젝트, CDP 터치 드래그, 폰트 차단, `waitForTimeout` 금지)을 썼다.
  - `USER_FLOW.md`: §5에 E2E-01~10 시나리오 매핑 표를 넣었다.
  - `DATABASE_DESIGN.md`: §8에 `addInitScript` 시딩, `readBoard` 검증, 손상·쓰기 실패 시뮬레이션을 썼다.
  - `DESIGN_SYSTEM.md`: §8에 360·768·1280px 레이아웃, 다크모드, 포커스 링, 모션 줄이기 검증 기준을 썼다.
  - `TASKS.md`: T6-1, T6-2를 완료 처리했다. 사용자 작업(Phase 7)을 남은 작업 맨 앞에 두고, Phase 8(셋업), 9(E2E, TDD), 10(마무리)을 추가했다.
  - `AGENTS.md`: TDD 원칙(Red → Green → Refactor, test first)을 넣고, E2E도 함께 실행하도록 테스트 규칙을 바꿨다.
- 환경 점검 결과:
  - Chromium 실행에 필요한 `libnss3`, `libnspr4`, `libasound2`가 없다.
  - sudo는 비밀번호가 필요하고, node가 nvm으로 설치되어 있다.
  - 그래서 U-1 명령을 `sudo env "PATH=$PATH" npx -y playwright install-deps chromium`으로 정했다.

---

## 7. 프롬프트

```
Phase 7이 끝났으니 이제 공용 repo의 /home/ubuntu/work/kosa-ict-genai-2026-hyundai/src/exercise/bardroh 폴더에 이 프로젝트의 필요한 파일들을 복사하고 commit & push해줘. 그후 Phase 8부터 Phase 10까지 모두 구현해줘
```

### 결과
- **공용 repo 반영:**
  - 기존 관례(`bardroh/day13/todo`)에 맞춰 `src/exercise/bardroh/day14/kanban/`에 프로젝트 파일을 복사했다(`node_modules`, `coverage` 제외).
  - 원격이 1커밋 앞서 있어 `git pull --no-rebase`로 merge했다(충돌 없음).
  - `main`에 커밋 `16a8457`("[day14] bardroh: 칸반보드 앱 - 3컬럼 드래그 앤 드롭·머티리얼 디자인")을 만들고 push했다.
  - 이 커밋은 Phase 8~10 이전 상태다.
- **Phase 8 (Playwright 셋업):**
  - `@playwright/test` 1.65.0과 Chromium headless shell을 설치했다.
  - `e2e/server.js`(의존성 없는 정적 서버)와 `playwright.config.js`(desktop 1280×800, mobile Pixel 7)를 만들었다.
  - 스크립트 `test`/`test:e2e`/`test:all`을 정의하고, Jest에서 `e2e/`를 제외했다. `.gitignore`도 갱신했다.
  - `e2e/helpers.js`를 만들었다: 폰트 차단 fixture, `seedBoard`, `readBoard`, `countStorageWrites`, `mouseDrag`, CDP `touchDrag`.
  - TDD:
    - 스모크 E2E를 먼저 작성해, 서버와 baseURL이 없어 실패하는 것(Red)을 확인한 뒤 셋업으로 통과(Green)시켰다.
    - `tests/server.test.js`는 경로 검사를 깨뜨려 Red를 검증했다.
    - 처음 작성한 경로 탐색 기대값은 WHATWG URL 정규화 때문에 틀려서 테스트를 바로잡았다.
- **Phase 9 (User Flow E2E-01~10):** spec 9개를 작성했다: board, card-crud, drag-mouse, drag-touch, keyboard, resilience, responsive, theme-a11y, smoke.
  - E2E가 찾은 실제 버그(Red):
    - 아이콘 폰트를 불러오지 못하면 `more_vert` 글자가 ⋮ 버튼 밖으로 넘쳐 1280px에서 페이지 가로 넘침(1299px)이 생겼다.
    - CSS 레이아웃 문제라 jsdom으로는 재현할 수 없어서 E2E를 Red로 삼았다.
    - `.material-symbols-rounded`에 `width/height: 1em; overflow: hidden`을 넣어 고쳤다(Green).
  - 테스트 쪽 오류 수정:
    - 카운터 기대값을 9에서 8로 고쳤다(공백 포함 8자).
    - 다이얼로그 폭은 열림 애니메이션이 끝난 뒤 재도록 했다.
  - 이미 구현된 동작이라 바로 통과한 시나리오는 뮤테이션 16개로 Red를 검증했다. 모두 실패를 확인하고 원복했다.
- **Phase 10:**
  - Refactor: `titleField`/`descField` 헬퍼로 중복을 정리했다. `waitForTimeout`은 쓰지 않는다.
  - E2E를 3회 반복 실행해 156개가 통과했다(flaky 없음).
  - README에 E2E 실행 방법과 사전 준비를 적었다.
  - TRD(파일 구조), DESIGN_SYSTEM(아이콘 규칙)을 보완하고, TASKS와 PRD 체크박스를 완료로 바꿨다.
- **테스트 결과:**
  - Jest: 3개 스위트, 115개 통과
  - Playwright: 52개 통과, 2개는 설계상 건너뜀(데스크톱 전용 배경 클릭, 모바일 전용 FAB)

---

## 8. 프롬프트

```
공용 repo에도 복사해서 commit & push해줘
```

### 결과
- Phase 8~10 결과를 공용 repo의 `src/exercise/bardroh/day14/kanban/`에 다시 복사했다. `node_modules`, `coverage`, `test-results`, `playwright-report`, `blob-report`는 제외했다.
- 원격 변경을 `git pull --no-rebase`로 merge한 뒤 `main`에 커밋하고 push했다.

---

## 9. 프롬프트

```
kanban 어플리케이션의 현재 구현 상태를 알려줘.
- 로컬에서만 구동 가능한 상태인지? 
- 백엔드 로직이 존재하는지?
그리고 구현정도에 맞게 시스템을 구동시켜주세요.
단, 이제부터 모든 지침과 작업은 AGENTS.md 를 따릅니다.
```

### 결과
- `AGENTS.md`의 한국어 안내, 작업 범위, 기록 규칙을 확인했다. PowerShell 기본 출력 인코딩에서 한글이 깨져 보여 UTF-8로 다시 읽었다.
- 코드와 설계 문서를 확인한 결과, 프런트엔드는 HTML/CSS/JavaScript 정적 앱이며 보드 데이터는 브라우저 `localStorage`의 `kanban.board`에 저장된다. 백엔드 API나 서버 DB는 없다. `e2e/server.js`는 앱 정적 파일을 제공하는 Node HTTP 서버다.
- 현재 환경은 Node.js v24.16.0, npm 11.16.0이고 `node_modules`는 없다. 앱 제공 서버는 의존성 없이 구동하므로 설치 작업 없이 로컬 서버를 시작했다.
- `http://localhost:4173/`와 `/app.js` 요청이 각각 HTTP 200임을 확인했다.

---

## 10. 프롬프트

```
실행 환경을 확인한 뒤 AGENTS.md를 읽고 다시 진행해줘.
```

### 결과
- PowerShell 실행 환경과 프로젝트 경로를 확인한 뒤 `AGENTS.md` 및 `WORKFLOW.md`를 UTF-8로 읽었다.
- 9번 프롬프트의 구현 현황 확인과 로컬 구동 요청을 수행하고 결과를 기록했다.

---

## 11. 프롬프트

```
kanban 애플리케이션을 github pages로 배포하고 싶어. 배포 가이드를 GITHUB_PAGES.md 로 정리해주세요.
```

### 결과
- `day12/tetris/GITHUB_PAGES.md`의 구성 방식을 참고해 Kanban용 GitHub Pages 가이드 `GITHUB_PAGES.md`를 작성했다.
- Kanban 폴더가 저장소 내부 하위 경로인 점을 반영해 GitHub Actions artifact 배포 방식을 안내하고, workflow 예시·저장소 설정·배포 후 확인·제약 및 문제 해결을 정리했다.
- 문서만 추가했으며 앱 소스, 설정, 의존성은 변경하지 않았다.

---

## 12. 프롬프트

```
2번을 진행하면 현재 연결된 깃 레파지토리로 push되는거구, 3번에서 현재 연결된 레파지토리 기준으로 github pages를 설정해야하는거야?
```

### 결과
- 2번의 `git push origin main`은 현재 저장소의 `origin` 원격으로 push하며, 원격이 의도한 GitHub 저장소인지 먼저 확인해야 한다고 안내했다.
- workflow는 push한 저장소의 루트에 두고, 3번 Pages 설정도 같은 저장소의 **Settings → Pages**에서 해야 한다고 설명했다.
- 현재 세션에서는 Git의 dubious ownership 안전 디렉터리 오류로 원격 주소를 확인하지 못했다.

---

## 13. 프롬프트

```
현재 day14 내의 파일들을 \\wsl.localhost\Ubuntu-22.04\home\ubuntu\work\kanban으로 위치를 옮기고 싶어. 
직접 옮길 수 있다면 옮겨주고 불가하다면 방법을 알려줘
```

### 결과
- `day14` 안에 Kanban 프로젝트와 `exercise1.txt`가 있고 지정된 목적지 폴더가 비어 있음을 확인했다.
- Kanban 프로젝트 파일을 목적지 폴더 루트로 옮기고 `exercise1.txt`도 함께 보존하도록 이동했다.
- 목적지가 기존 작업 저장소 외부여서 Git 메타데이터나 저장소 이력은 이동 대상에 포함되지 않는다.

---

## 14. 프롬프트

```
kanban 애플리케이션을 github pages로 배포하고 싶어서 배포 가이드를 GITHUB_PAGES.md 로 정리해두었습니다.
다만, 현재 환경은 github 연결이 되어있지 않은 환경이며, 레파지토리도 새로 만들어야합니다.
레파지토리 생성과 github 연결까지 포함해서 GITHUB_PAGES.md 를 다시 작성해주세요.
```

### 결과
- `GITHUB_PAGES.md`를 현재 프로젝트 루트 구조 기준으로 다시 작성했다.
- GitHub CLI 또는 웹사이트에서 새 저장소를 만드는 방법, 로컬 Git 초기화, 원격 `origin` 연결, 최초 commit/push, Pages 설정과 이후 배포 절차를 포함했다.
- GitHub Actions workflow 예시를 루트의 앱 파일에 맞춰 수정하고, public/private 저장소 Pages 조건 및 문제 해결 안내를 추가했다.
- 문서만 변경했으며 코드는 변경하지 않았다. 요청에 따라 테스트를 실행하지 않았다.

---

## 15. 프롬프트

```
준비사항 완료했어. 1번 작업 직접 진행해줘
```

### 결과
- 프로젝트 루트에 `.github/workflows/kanban-pages.yml`을 추가했다.
- `main`의 앱 파일 및 workflow 변경 시 실행되며, 앱의 정적 파일 네 개만 GitHub Pages에 배포하도록 구성했다.
- 이 폴더는 아직 Git 저장소가 아니어서 Git 상태를 확인할 수 없었다. 앱 코드 변경은 없어 테스트를 실행하지 않았다.
