+++
title       = 'Hugo 架站指南：快速打造靜態個人網站'
date        = 2025-08-02T22:57:51+08:00
draft       = true
author      = 'Jim00000'
tags        = ['blogger', 'hugo']
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
git serve
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

以下列出幾項最基本且必須的設定項目：

- `baseURL`: 網站的網址（例如 https://myblog.com/）
- `languageCode`: 網站的語言代碼（如 zh-tw）
- `title`: 網站標題，會顯示在瀏覽器分頁與搜尋結果中
- `theme`: 所使用的佈景主題名稱（如 hugo-PaperMod）


