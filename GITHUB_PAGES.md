# GitHub Pages 배포 가이드

이 가이드는 현재 Kanban 프로젝트 폴더를 새 GitHub 저장소로 만들고, GitHub Actions를 통해 GitHub Pages에 배포하는 절차를 설명합니다. 앱은 HTML, CSS, JavaScript로 된 정적 사이트이며 빌드 단계가 필요하지 않습니다. 보드 데이터는 방문자의 브라우저 `localStorage`에 저장되고 서버에는 전송되지 않습니다.

## 준비 사항

- Git이 설치되어 있어야 합니다.
- GitHub 계정이 필요합니다.
- 터미널에서 저장소를 만들려면 GitHub CLI(`gh`)를 설치하고 로그인합니다. 브라우저에서 저장소를 만들어도 됩니다.
- GitHub Free 계정에서 Pages를 쓰려면 저장소를 public으로 만드세요. private 저장소 Pages 사용 가능 여부는 GitHub 요금제에 따라 달라집니다.

GitHub CLI 로그인을 아직 하지 않았다면 다음 명령을 실행하고 안내에 따라 GitHub 계정으로 인증합니다.

```bash
gh auth login
gh auth status
```

## 1. GitHub Actions 배포 workflow 추가

프로젝트 루트에 `.github/workflows/kanban-pages.yml` 파일을 만들고 아래 내용을 저장합니다. 이 프로젝트는 앱 파일이 저장소 루트에 있으므로 별도의 하위 경로를 지정하지 않습니다.

```yaml
name: Deploy Kanban to GitHub Pages

on:
  push:
    branches: [main]
    paths:
      - 'index.html'
      - 'style.css'
      - 'board.js'
      - 'app.js'
      - '.github/workflows/kanban-pages.yml'
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: github-pages
  cancel-in-progress: true

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Configure GitHub Pages
        uses: actions/configure-pages@v5

      - name: Prepare static site
        run: |
          mkdir -p _site
          cp index.html _site/
          cp style.css _site/
          cp board.js _site/
          cp app.js _site/

      - name: Upload Kanban site
        uses: actions/upload-pages-artifact@v3
        with:
          path: _site

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

이 workflow는 네 앱 파일만 Pages artifact로 올립니다. 테스트, 문서, 작업 기록은 배포 대상에 포함하지 않습니다. 앱 파일을 다른 경로로 옮기면 `paths`와 `cp` 원본 경로도 함께 수정하세요.

## 2. 새 GitHub 저장소 생성 및 최초 push

현재 Kanban 프로젝트 폴더에서 다음 절차를 실행합니다. GitHub 저장소 이름은 예시로 `kanban`을 사용합니다. 원하는 이름으로 바꿔도 됩니다.

먼저 Git 저장소를 초기화하고 기본 브랜치를 `main`으로 설정합니다.

```bash
git init
git branch -M main
```

그 다음 GitHub에서 저장소를 만드는 두 방법 중 하나를 선택하세요.

### 방법 A: GitHub CLI로 생성 및 연결

아래 명령은 `OWNER`를 본인의 GitHub 사용자명 또는 조직명으로 바꿉니다. `--public`은 공개 저장소를 생성합니다. 비공개 저장소가 필요하고 계정이 Pages를 지원하면 `--private`을 사용하세요.

```bash
gh repo create OWNER/kanban --public --source=. --remote=origin
```

### 방법 B: GitHub 웹사이트에서 생성

1. GitHub에서 **New repository**를 선택하고 저장소 이름을 입력합니다.
2. 공개 여부를 선택하고 저장소를 생성합니다.
3. 초기 commit 충돌을 피하려면 생성 화면에서 README, `.gitignore`, License 추가를 선택하지 않습니다.
4. GitHub 저장소 화면의 주소를 복사하고 로컬 프로젝트 폴더에서 원격 저장소로 등록합니다.

```bash
git remote add origin https://github.com/OWNER/kanban.git
```

HTTPS 대신 SSH를 쓰는 경우 원격 주소를 `git@github.com:OWNER/kanban.git` 형식으로 지정할 수 있습니다.

### 최초 commit 및 push

저장소를 공개하기 전에 `.gitignore`와 commit할 파일을 검토합니다. 특히 `node_modules/`, 테스트 산출물, 개인용 파일은 올리지 마세요. 현재 프로젝트의 `.gitignore` 규칙을 확인하고 필요한 경우에만 수정합니다.

```bash
git status --short
git add .
git status --short
git commit -m "Initial Kanban app"
git push -u origin main
```

`git add .` 후 두 번째 `git status`에서 원격에 공개할 파일 목록을 확인하세요. `origin`이 이미 등록되어 있다면 다시 추가하지 말고 `git remote -v`로 주소를 확인합니다. 기존 GitHub 저장소에 연결해야 하는 경우 새 저장소 생성 명령 대신 해당 저장소의 주소를 `git remote add origin <URL>`에 사용합니다.

## 3. GitHub Pages 설정 및 최초 배포

1. GitHub에서 방금 만든 저장소의 **Settings → Pages**를 엽니다.
2. **Build and deployment → Source**에서 **GitHub Actions**를 선택합니다.
3. 저장소의 **Actions** 탭에서 `Deploy Kanban to GitHub Pages` 실행을 열고 완료될 때까지 기다립니다.
4. 성공한 workflow의 `github-pages` environment 링크 또는 **Settings → Pages**에 표시된 주소를 엽니다.

최초 push가 workflow를 실행합니다. 프로젝트 사이트 주소는 보통 `https://OWNER.github.io/REPOSITORY/` 형식입니다. 실제 배포 주소는 Pages 설정 화면에서 확인하세요.

## 4. 이후 변경 사항 배포

앱 파일 또는 workflow를 수정한 뒤 기본 브랜치에 commit하고 push합니다.

```bash
git add index.html style.css board.js app.js .github/workflows/kanban-pages.yml
git commit -m "Update Kanban app"
git push origin main
```

수정한 파일만 `git add`에 지정해도 됩니다. 앱 파일 네 개 중 하나가 `main`에 push되면 배포가 자동으로 실행됩니다. GitHub에서 만든 저장소 이름과 사용자명은 이 명령에 넣을 필요가 없습니다. 연결된 `origin`을 사용합니다.

## 5. 배포 확인

- To-Do, In-Progress, Done 컬럼 및 카드 추가·편집 기능이 표시되고 동작하는지 확인합니다.
- 새로고침 후 같은 브라우저에서 카드 상태가 유지되는지 확인합니다. 데이터는 브라우저별 `localStorage`에 저장되어 다른 브라우저나 기기와 공유되지 않습니다.
- `index.html`은 CSS와 JavaScript를 상대 경로로 불러오므로 저장소 사이트의 하위 경로에서도 동작합니다.
- Google Fonts는 외부 네트워크에서 불러옵니다. 폰트 연결에 실패해도 기본 대체 글꼴로 앱을 사용할 수 있습니다.

## 제한 사항

GitHub Pages는 정적 호스팅입니다. 사용자 간 데이터 동기화, 공유 보드, 서버 저장이 필요하면 별도의 백엔드와 인증·접근 제어가 필요합니다. 현재 앱은 이런 기능을 제공하지 않습니다.

## 문제 해결

| 증상 | 확인할 내용 |
| --- | --- |
| `gh auth` 또는 저장소 생성 실패 | `gh auth status`로 로그인 상태를 확인하고, 사용자명·조직의 저장소 생성 권한을 확인합니다. |
| push할 `origin`이 없거나 잘못됨 | `git remote -v`로 확인합니다. 없으면 `git remote add origin <URL>`을 실행하고, 주소가 틀렸으면 `git remote set-url origin <URL>`로 수정합니다. |
| workflow가 실행되지 않음 | 기본 브랜치가 `main`인지, 앱 파일 경로가 저장소 루트인지, 변경 파일이 workflow의 `paths`에 포함되는지 확인합니다. Actions 탭에서 수동 실행도 할 수 있습니다. |
| Pages 배포가 권한 오류로 실패함 | **Settings → Pages**에서 GitHub Actions 배포를 선택했는지 확인합니다. workflow에는 `pages: write`, `id-token: write` 권한이 필요합니다. |
| 사이트가 404로 표시됨 | Actions의 최신 배포가 성공했는지와 Pages 화면에 표시된 주소를 확인한 뒤 잠시 기다립니다. |
| CSS 또는 JavaScript가 로드되지 않음 | 파일명이 workflow의 `cp` 대상과 일치하는지, HTML이 `/style.css` 같은 루트 절대 경로를 사용하지 않는지 확인합니다. |
| 변경 사항이 보이지 않음 | 최신 Actions 실행이 완료됐는지 확인하고 브라우저에서 강력 새로고침합니다. |
