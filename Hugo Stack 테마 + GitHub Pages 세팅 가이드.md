---
createDate: 2026-02-20:23
Pinned: false
tags:
Cssclasses: dataview-scroll
---
## Summary

> DevSecOps 포트폴리오 블로그 구축 (한/영 다국어)

---

## 📋 사전 준비

### 필요한 도구 설치

```bash
# macOS
brew install hugo git

# Windows (winget)
winget install Hugo.Hugo.Extended
winget install Git.Git

# 버전 확인 (반드시 Extended 버전)
hugo version
# hugo v0.xxx.x+extended ...
```

> ⚠️ Stack 테마는 **Hugo Extended** 버전 필요 (SCSS 컴파일)

---

## Step 1. Hugo 프로젝트 생성

```bash
# 프로젝트 생성
hugo new site my-blog
cd my-blog

# Git 초기화
git init
```

---

## Step 2. Stack 테마 설치

```bash
# 서브모듈로 추가 (업데이트 관리 편함)
git submodule add https://github.com/CaiJimmy/hugo-theme-stack.git themes/hugo-theme-stack

# 테마 예제 설정 복사
cp -r themes/hugo-theme-stack/demo/content ./
cp -r themes/hugo-theme-stack/demo/config/_default ./config/
```



## Step 3. 다국어(한/영) 설정

```
config/
├── hugo.toml       ← baseURL, title, 언어 설정 (핵심)
├── languages.toml  ← 한/영 언어 정의
├── params.toml     ← 사이드바, 댓글, 다크모드 등
├── menu.toml       ← 네비게이션 메뉴
├── markup.toml     ← 마크다운 렌더링 설정
└── related.toml    ← 관련 포스트 설정
```


`config/_default/hugo.toml` 파일을 아래처럼 수정:

```toml
baseURL                = "https://rybbit3.github.io/"
languageCode           = "ko"
title                  = "리빗의 보안 블로그"
theme                  = "hugo-theme-stack"

defaultContentLanguage = "ko"
hasCJKLanguage         = true

[pagination]
    pagerSize = 5

[permalinks]
    post = "/p/:slug/"
    page = "/:slug/"
```

---

## Step 4. 폴더 구조 세팅

```bash
# 1. 한/영 폴더 생성
mkdir -p content/ko/post
mkdir -p content/ko/page
mkdir -p content/en/post
mkdir -p content/en/page

# 2. 기존 영어 포스트/페이지를 ko로 이동 (한국어 기본으로 사용)
mv content/post content/ko/post_temp && mv content/ko/post_temp/* content/ko/post/ && rmdir content/ko/post_temp
mv content/page content/ko/page_temp && mv content/ko/page_temp/* content/ko/page/ && rmdir content/ko/page_temp

# 3. 기존 _index.md를 ko 홈으로 이동
mv content/_index.md content/ko/_index.md

# 4. 영어 홈 _index.md 생성
echo '---
title: "Home"
---' > content/en/_index.md

# 5. 불필요한 다국어 파일 삭제
rm -f content/_index.ja.md content/_index.zh.md content/_index.zh-hant-tw.md

# 6. categories 폴더 이동
mv content/categories content/ko/categories

# ja, zh, zh-hant-tw suffix 파일 일괄 삭제
find content/ko -name "*.ja.md" -delete
find content/ko -name "*.zh.md" -delete
find content/ko -name "*.zh-hant-tw.md" -delete

# 결과 확인
find content/ -type f -name "*.md" | sort
```

**`i18n/ko.yaml`** (UI 문자열 한국어 번역):

```yaml
home:
  other: 홈
article:
  other: 포스트
categories:
  other: 카테고리
tags:
  other: 태그
search:
  other: 검색
readMore:
  other: 더 보기
archives:
  other: 아카이브
links:
  other: 링크
```

**`i18n/en.yaml`**:

```yaml
home:
  other: Home
article:
  other: Posts
categories:
  other: Categories
tags:
  other: Tags
search:
  other: Search
readMore:
  other: Read More
archives:
  other: Archives
links:
  other: Links
```

---

## Step 5. 카테고리 페이지 생성

```bash
# 한국어 카테고리
hugo new content ko/page/security-ops/_index.md
hugo new content ko/page/cloud-security/_index.md
hugo new content ko/page/devsecops/_index.md
hugo new content ko/page/study-log/_index.md
```

**`content/ko/page/devsecops/_index.md`** 예시:

```yaml
---
title: "DevSecOps"
description: "IaC, CI/CD 보안, 컨테이너 보안 실습 기록"
image: "devsecops-cover.jpg"
links:
  - title: GitHub
    description: 프로젝트 소스코드
    website: https://github.com/YOUR_USERNAME
    image: https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png
menu:
  main:
    weight: 3
    params:
      icon: code
---
```

---

## Step 6. 첫 번째 포스트 작성

```bash
hugo new content ko/post/azure-sentinel-soar/index.md
```

**`content/ko/post/azure-sentinel-soar/index.md`**:

```yaml
---
title: "Azure Sentinel SOAR 자동화로 알람 피로 80% 감소"
description: "Logic Apps + Azure Sentinel로 반복 알람 자동 분류 및 대응 플레이북 구축"
date: 2025-02-20
image: cover.png
categories:
  - DevSecOps
  - Security-Ops
tags:
  - Azure Sentinel
  - SOAR
  - Logic Apps
  - Python
draft: false
---

## 배경

SOC 업무를 하다 보면...
```

**영어 번역본** (`content/en/post/azure-sentinel-soar/index.md`):

```yaml
---
title: "Reducing Alert Fatigue 80% with Azure Sentinel SOAR"
description: "Building automated alert classification and response playbooks"
date: 2025-02-20
image: cover.png   # 이미지는 공유됨
translationKey: azure-sentinel-soar   # 한/영 포스트 연결
categories:
  - DevSecOps
tags:
  - Azure Sentinel
  - SOAR
draft: false
---
```

> `translationKey`가 같으면 한/영 포스트가 서로 연결되어 언어 전환 버튼이 생깁니다.

---

## Step 7. 로컬 미리보기

```bash
hugo server -D
# http://localhost:1313 에서 확인
```

---

## Step 8. GitHub 레포 생성 및 연결

```bash
# GitHub에서 레포 생성: YOUR_USERNAME.github.io

git remote add origin https://github.com/YOUR_USERNAME/YOUR_USERNAME.github.io.git
git branch -M main
git add .
git commit -m "feat: init hugo blog with stack theme"
git push -u origin main
```

---

## Step 9. GitHub Actions 자동 배포 설정

**`.github/workflows/deploy.yml`** 파일 생성:

```yaml
name: Deploy Hugo Blog

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive   # 테마 서브모듈 포함
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true          # Extended 버전 필수

      - name: Build
        run: hugo --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## Step 10. GitHub Pages 활성화

1. GitHub 레포 → **Settings** → **Pages**
2. Source: **GitHub Actions** 선택
3. `git push` 후 Actions 탭에서 배포 확인
4. `https://YOUR_USERNAME.github.io` 접속 🎉

---

## Step 11. Obsidian 연동 (Enveloppe 플러그인)

### 플러그인 설치

Obsidian → 설정 → 커뮤니티 플러그인 → **Enveloppe** 검색 후 설치

### Enveloppe 설정

```
GitHub 설정:
  - Repo: YOUR_USERNAME.github.io
  - Branch: main
  - Token: GitHub Personal Access Token (repo 권한)

파일 경로 설정:
  - Default folder: content/ko/post
  - Root folder: (비워두기)

Frontmatter 설정:
  - publish key: publish
  - value: true
```

### Obsidian 노트 frontmatter 예시

```yaml
---
title: "Splunk SPL lookup 기반 false positive 자동 분류"
date: 2025-02-20
categories: ["Security-Ops"]
tags: ["Splunk", "SPL", "SOC"]
publish: true      ← 이게 있어야 Hugo로 내보내짐
draft: false
---
```

**발행 방법:** 노트 작성 후 Command Palette → `Enveloppe: Upload single file`

---

## 📊 최종 URL 구조

```
https://rybbit3.github.io/                    # 한국어 홈
https://rybbit3.github.io/en/                 # 영어 홈
https://rybbit3.github.io/p/첫-번째-포스트/   # 한국어 포스트
https://rybbit3.github.io/en/p/first-post/    # 영어 포스트
https://rybbit3.github.io/categories/         # 카테고리
```

---

## ✅ 세팅 체크리스트

- [x] Hugo Extended 설치
- [x] Stack 테마 서브모듈 추가
- [x] config/_default/hugo.toml 한/영 언어 설정
- [x] i18n/ko.yaml, en.yaml 번역 파일
- [x] content/ko, content/en 폴더 구조
- [x] 카테고리 페이지 생성
- [x] 첫 번째 포스트 작성
- [x] GitHub Actions deploy.yml 추가
- [x] GitHub Pages 소스 → GitHub Actions 설정
- [ ] Obsidian Enveloppe 플러그인 연동 (선택사항)

---

## 🔗 참고 링크

- [Hugo Stack 테마 공식 문서](https://stack.jimmycai.com/)
- [Hugo 다국어 공식 문서](https://gohugo.io/content-management/multilingual/)
- [Enveloppe 플러그인](https://github.com/Enveloppe/obsidian-enveloppe)
- [GitHub Actions Hugo Deploy](https://gohugo.io/hosting-and-deployment/hosting-on-github/)


## 참고노트



## 참고자료
