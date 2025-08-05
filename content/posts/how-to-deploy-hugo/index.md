+++
title       = 'Hugo 架站指南：快速打造靜態個人網站'
date        = 2025-08-02T22:57:51+08:00
draft       = true
author      = 'Jim00000'
tags        = ['Hugo', 'Blog', '教學']
categories  = ['部落格']
+++

想打造一個快速、輕量且高度可自訂的個人網站嗎？Hugo 是目前最受歡迎的靜態網站生成器之一，特別適合用來架設部落格。本文將循序帶領你完成 Hugo 的安裝、專案建立、佈景主題選擇，以及本地預覽與部署流程，協助你快速打造屬於自己的技術平台。考量到多數使用者採用 Windows 作業系統，本教學將以 Windows 11 為例，示範 Hugo 的安裝與設定流程。

<!--more-->

## 1 在 Windows 系統安裝 WSL 2

首先，請開啟 Windows 終端機。你可以透過以下任一方式：

- 在工作列的 **開始** 🪟 圖示上按右鍵，選擇「終端機」
- 或是同時按下 **Win + R** 鍵，輸入 `powershell` 並按下 Enter 鍵

接著，輸入以下指令以安裝 WSL 2 及預設的 Ubuntu 發行版：

```powershell
wsl --install
```

此命令會自動安裝 WSL 所需的元件，並下載並安裝 Ubuntu 作業系統。
安裝完成後，系統可能會提示重新啟動，請依指示操作。

## 2 於Ubuntu安裝Hugo

在 WSL 環境中，請先進入 Ubuntu 終端機。接下來，你可以透過以下任一方式安裝 Hugo：

### 方法一：使用 apt 套件庫安裝

```bash
sudo apt install hugo
```

- ✅ 安裝快速、操作簡便
- ❌ 套件庫中的版本通常較舊，可能不支援部分佈景主題

### 方法二：使用 Snap 套件安裝

```bash
sudo snap install hugo
```

- ✅ 安裝快速、可取得最新版
- ❌ 某些佈景主題可能不相容最新版 Hugo

### 方法三：從 GitHub Release 手動安裝

👉 [前往 Hugo GitHub Release 頁面](https://github.com/gohugoio/hugo/releases)

- ✅ 可依佈景主題需求選擇特定版本
- ❌ 安裝流程較繁瑣，需手動下載與設定

建議依據所使用的佈景主題之系統需求選擇 Hugo 版本。若佈景主題需要特定版本支援，請採用方法三進行安裝。

以下是我安裝 Hugo 以搭配 LoveIt 主題的示範流程：

#### 安裝 LoveIt 主題所需的 Hugo 版本

根據 [LoveIt v0.3.0 的系統需求](https://github.com/dillonzq/LoveIt?tab=readme-ov-file#compatibility)[^1]，Hugo 需使用 **v0.145.0** 才能完整支援相關功能。因此，我們將從 GitHub Release 頁面下載該版本的安裝檔並進行安裝：

[^1]: LoveIt 主題的相容性說明文件，詳見 GitHub README 中的 Compatibility 區段。

```bash
wget https://github.com/gohugoio/hugo/releases/download/v0.145.0/hugo_extended_0.145.0_linux-amd64.deb
sudo apt install ./hugo_extended_0.145.0_linux-amd64.deb
```

{{< admonition info "留意 Hugo 的 extended 版本" >}} 
請務必下載 extended 版本，以支援 SCSS 編譯等進階功能。這對 LoveIt 主題的樣式渲染與功能完整性來說是不可或缺的。
{{< /admonition >}}

## 3 初始化網站並設定主題

接下來，我們將使用 Hugo 初始化網站架構，並套用一個現成的佈景主題。這裡以 [PaperMod](https://themes.gohugo.io/themes/hugo-papermod/) 為範例：

```bash
hugo new site blog
cd blog
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
hugo serve
```

執行上述指令後，你應該會在主控台看到類似以下的輸出：

```text {linenos=false}
…
                   | EN
-------------------+-----
  Pages            |  8
  Paginator pages  |  0
  Non-page files   |  0
  Static files     |  0
  Processed images |  0
  Aliases          |  1
  Cleaned          |  0

Built in 8 ms
Environment: "development"
Serving pages from disk
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

這代表你已成功啟動 Hugo 的開發伺服器，可在瀏覽器中預覽網站，網址為 [http://localhost:1313/](http://localhost:1313/)。後續便能開始撰寫內容並自訂外觀！

### 3.1 配置hugo.toml

👉 想了解完整設定項目，可參考 [Hugo All Settings 官方文件](https://gohugo.io/configuration/all/)。
Hugo 提供高度彈性的設定方式，所有可調整的參數都已在官方文件中詳細列出。

以下是幾個基本且必須的設定項目：

- `baseURL`: 網站的網址， 例如 https://jim00000.github.io/
- `languageCode`: 網站的語言代碼（如 zh-tw）
- `title`: 網站標題，會顯示在瀏覽器分頁與搜尋結果中
- `theme`: 所使用的佈景主題名稱（如 hugo-PaperMod）

以下是設定檔的範例：

```toml
baseURL       =   'https://mysite.org/'
languageCode  =   'en-us'
title         =   '我的網站'
theme         =   'hugo-PaperMod' 
```

其他佈景主題相關的設定項目，請參考所使用主題的官方文件以獲得詳細說明。

## 4 新增文章

在 Hugo 中，網站的文章通常儲存在 `content` 目錄下，並採用 Markdown 格式撰寫。Hugo 會自動將這些 Markdown 檔案轉換為 HTML，讓瀏覽器能夠正確呈現內容。


你可以使用以下指令來新增文章。例如，建立一篇名為 first-post 的文章：

```bash
hugo new content content/posts/first-post.md
```

建立後，系統會自動產生對應的 Markdown 檔案。你可以打開它並開始編寫文章內容。

### 4.1 文章格式

每篇文章的開頭都需加入一段 metadata（中繼資料），用以描述文章的標題、建立時間、是否為草稿，以及所屬的分類與標籤等。這段設定通常採用 TOML 或 YAML 格式，範例如下：

```toml
+++
title = 'First Post'
date = 2025-08-05T00:00:00+00:00
draft = true
+++
```

這段中繼資料稱為 [Front Matter](https://gohugo.io/content-management/front-matter/)，是 Hugo 用來解析文章屬性的標準格式。
若要進一步設定分類（categories）與標籤（tags），可如下擴充：

```toml
+++
title = 'First Post'
date = 2025-08-05T00:00:00+00:00
draft = true
categories = ['技術筆記']
tags = ['Hugo', 'Blog', '教學']
+++
```

列出重要且基本的必填欄位項目：

- date: 文章建立時間
- draft: 是否為草稿，若為true，文章將不會顯示在正式網站中
- title: 文章標題

以下是補充欄位項目:

- author: 指定作者名稱，若網站支援多作者或要顯示作者資訊時會用到
- lastmod: 設定文章最近修改時間
- tags: 設定文章標籤，用來描述文章主題、方便搜尋與過濾
- categories: 設定文章所屬分類，有助於內容分類與導覽


### 4.2 摘要預覽設定

若希望在文章列表中只顯示部分內容作為摘要，可以在文章中插入 `<!-- more -->` 標記。這個標記會告訴 Hugo 在那個位置截斷內容，只顯示前面的部分作為預覽。

例如：

```markdown
這是文章的開場介紹，非常吸引人！

<!-- more -->

接著進入更深入的內容，例如程式碼範例或使用心得...
```

在使用這個標記後，訪客在列表中只會看到 `<!-- more -->` 之前的文字，並會看到「**閱讀更多**」或是「**Read More**」的連結導向完整文章。

### 4.3 文章內容

Hugo 採用 Markdown 格式撰寫文章，語法簡單、彈性高，非常適合用於技術文件與一般內容編輯。由於 Markdown 是廣泛使用的標記語言，這裡不再進一步說明。

撰寫時可搭配以下線上編輯器，以提升編輯效率與預覽體驗：

- [HackMD](https://hackmd.io/)
- [StackEdit](https://stackedit.io/)

若已啟用 Hugo，本地編輯文章時可即時預覽修改內容，提升撰寫效率與反饋速度。

## 5 公開網站

當你完成網站內容後，下一步就是將它正式上線。以下是幾種常見的部署方式：

- 自行架設本機伺服器：適合進階使用者，可完全掌控環境設定
- 租用雲端或實體主機：使用雲端主機提供商服務
- 使用 GitHub Pages[^2] 託管靜態網頁：免費且方便，適合個人或小型專案

[^2]: GitHub Pages 提供免費的靜態網站託管服務，能直接將原始碼儲存庫轉換為網頁。

若你希望降低成本、簡化維護流程，推薦選擇 GitHub Pages 來架設網站。它不僅穩定可靠，也與 Hugo 的建置流程高度相容。

在本教學中，我們將以 GitHub Pages 為例，逐步示範如何部署並公開你的 Hugo 網站。

### 5.1 部署 Hugo 到 GitHub Pages

在開始部署之前，先簡要了解 GitHub 提供的兩項主要服務，確保你知道使用的服務：

- 託管 Hugo 所產生的靜態網頁：公開的 `public` 資料夾內容，即網站最終呈現的 HTML 等檔案。
- 託管網站原始碼與設定檔：包含文章 Markdown 原稿、Hugo 配置檔案、佈景主題及任何客製化的設計。

值得注意的是，這些資料將會完全公開，意味著你的個人網站內容將能被任何人瀏覽。

{{< admonition info "事先準備" >}}
在開始操作之前，請先確認你已擁有可用的 GitHub 帳號。
{{< /admonition >}}

### 5.2 建立 GitHub 儲存庫

根據 GitHub Pages 的靜態網頁託管規則，若你要建立「使用者或組織網站」，儲存庫名稱必須為：`<owner>.github.io`

其中 `<owner>` 是你的 GitHub 帳號 ID。網站的預設網址將會是：`http(s)://<owner>.github.io`


{{< admonition note "調整 Hugo 設定" >}}

請記得在 `hugo.toml` 中設定正確的 `baseURL`，例如：

```toml
baseURL = "https://<owner>.github.io/"
```

這樣 Hugo 產生的連結才會正確對應到 GitHub Pages 的網址。

{{< /admonition >}}

### 5.3 使用 GitHub Actions 自動部署

為了讓 Hugo 網站在每次推送新內容後能自動部署，可以使用 GitHub Actions 進行自動化建置。請在 Hugo 專案的根目錄下新增 .github/workflows/hugo.yaml 檔案，內容如下範例：

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    # GitHub-hosted runners automatically enable `set -eo pipefail` for Bash shells.
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      DART_SASS_VERSION: 1.89.2
      HUGO_VERSION: 0.145.0
      HUGO_ENVIRONMENT: production
      TZ: America/Los_Angeles
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
          sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: |
          wget -O ${{ runner.temp }}/dart-sass.tar.gz https://github.com/sass/dart-sass/releases/download/${DART_SASS_VERSION}/dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz
          tar -xf ${{ runner.temp }}/dart-sass.tar.gz --directory ${{ runner.temp }}
          mv ${{ runner.temp }}/dart-sass/ /usr/local/bin
          echo "/usr/local/bin/dart-sass" >> $GITHUB_PATH
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Cache Restore
        id: cache-restore
        uses: actions/cache/restore@v4
        with:
          path: |
            ${{ runner.temp }}/hugo_cache
          key: hugo-${{ github.run_id }}
          restore-keys:
            hugo-
      - name: Configure Git
        run: git config core.quotepath false
      - name: Build with Hugo
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/" \
            --cacheDir "${{ runner.temp }}/hugo_cache"
      - name: Cache Save
        id: cache-save
        uses: actions/cache/save@v4
        with:
          path: |
            ${{ runner.temp }}/hugo_cache
          key: ${{ steps.cache-restore.outputs.cache-primary-key }}
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
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

當你將這份設定檔加入並推送到 GitHub 上，GitHub Actions 就會在每次推送到指定分支後觸發建置程序，自動產出網頁並部署到 GitHub Pages。

透過整合 GitHub Actions 自動化部署流程，Hugo 網站的維護與發佈不再繁瑣。每次推送新內容，只需專注創作，GitHub 就會在背後默默完成建置與部署。
這不僅提升開發效率，也讓網站更新變得簡單流暢。

## 6 小結

從初始安裝、內容撰寫，到 GitHub Actions 自動部署，本指南完整帶你踏上 Hugo 架站之旅。無論是個人部落格、作品集，或技術筆記，只要掌握好 Hugo 的靈活架構與 GitHub 的強大生態系，就能打造一個快速、輕量又具高度自訂性的網站。