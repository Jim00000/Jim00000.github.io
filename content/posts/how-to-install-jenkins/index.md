+++
title       = '在 WSL/Ubuntu 上安裝部署 Jenkins：從零開始的建置流程'
date        = 2025-08-05T22:21:45+08:00
draft       = true
author      = 'Jim00000'
tags        = ['Jenkins', '開發工具', 'CI/CD']
categories  = ['軟體開發與技術']
+++

在現代軟體開發流程中，自動化已成為不可或缺的一環。Jenkins 作為最受歡迎的 CI/CD 工具之一，能協助開發者自動執行建置、測試與部署流程。本篇文章將以 WSL 環境為例，逐步說明 Jenkins 的安裝流程，並補充必要的設定細節，協助你順利啟動第一個 Jenkins 任務。

<!--more-->

<figure>
    <img src="https://i.postimg.cc/g2Kr2FCS/jenkins-logo.png" style="width:35%; height:auto;">
    <figcaption class="image-caption">Jenkins的logo</figcaption>
</figure>

## 1 Jenkins 是什麼

[Jenkins](https://www.jenkins.io/) 是一款開源的自動化伺服器，主要用途是協助開發團隊實現 **持續整合**（CI）與 **持續交付**（CD）的流程。它能自動執行軟體的**建置**、**測試**與**部署**，讓整個開發流程更加高效、可靠與可視化。

Jenkins 在軟體開發流程中扮演著自動化中樞的角色，以下是它的主要用途：

- 自動建置：每次程式碼提交後，Jenkins 可自動拉取最新版本，執行編譯、打包等建置流程，確保程式碼能順利轉換為可執行的產物。
- 自動測試：整合各種測試框架，自動執行單元測試與整合測試，並產出可視化報告，即時回饋程式碼品質。
- 自動部署：將建置完成的應用程式部署至測試或正式環境，支援定時任務、條件觸發與手動操作，大幅簡化部署流程。
- 確保品質：可作為「品質稽查員」，在程式碼合併前自動執行測試與檢查，確保每次提交都符合標準，避免錯誤進入主分支。

## 2 建立 Jenkins 執行環境

在開始使用 Jenkins 進行自動化建置與部署之前，首先需要建立一個穩定的執行環境。Jenkins 是一款基於 Java 的應用程式，因此在安裝前必須先準備好 Java 執行環境，並設定好必要的系統資源。
本篇將以 Ubuntu（WSL）環境為例，逐步說明 Jenkins 的安裝流程。

### 2.1 安裝 OpenJDK

Jenkins 是以 Java 為基礎的應用程式，因此在安裝 Jenkins 前，必須先準備好 Java 執行環境。這裡我們選用 OpenJDK 21 作為範例版本，因其為 LTS（長期支援）版本，適合穩定的建置環境。

```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre
```

### 2.2 安裝 Jenkins LTS (Long-Term Support) Release 

Jenkins 提供 LTS（長期支援）版本，適合用於穩定的建置與部署環境。以下步驟將引導你在 Ubuntu（WSL）環境中安裝 Jenkins[^1]：

[^1]:本文撰寫時，Jenkins 的版本為 2.516.1

1. 新增 Jenkins 套件庫與密鑰

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

2. 更新套件並安裝 Jenkins

```bash
sudo apt update
sudo apt install jenkins
```

安裝完成後，Jenkins 將會被設定為背景服務，並預設使用 port 8080。

### 2.3 驗證Jenkins已完成安裝

安裝完成後，我們可以透過 `systemctl` 指令確認 Jenkins 是否已成功啟動並執行中。

```bash
sudo systemctl status jenkins
```

若 Jenkins 正常執行，將顯示如下資訊：

{{< infobox >}}
● jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled; preset: enabled)
     Active: active (running) since Tue 2025-08-05 23:16:07 CST; 1min 23s ago
   Main PID: 7626 (java)
      Tasks: 68 (limit: 19063)
     Memory: 678.1M (peak: 822.2M)
        CPU: 11.722s
     CGroup: /system.slice/jenkins.service
             └─7626 /usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --webroot=/var/cache/jenkin>
{{< /infobox >}}

到這裡為止，已經初步完成Jenkins的安裝。你可以在瀏覽器中輸入 http://localhost:8080 進入 Jenkins 首頁。

若成功進入 Jenkins 首頁，表示 Jenkins 已正確安裝並啟動，你應該會看到 Jenkins 的初始設定畫面，要求輸入解鎖密碼。

(可選) 如果有需要搭配結合 SSL 支援，可以本節參考後續步驟：

### 2.4 結合 SSL 支援

若你希望透過 HTTPS 安全地存取 Jenkins，可以使用 Nginx 作為反向代理伺服器，將外部請求導向 Jenkins 的內部服務，並加上 SSL 加密與 Header 處理。以下步驟將引導你完成整合流程。

#### 2.4.1 安裝 Nginx

首先，安裝 Nginx：

```bash
sudo apt install nginx
```

安裝完成後，打開瀏覽器並輸入 http://localhost，若出現「Welcome to nginx!」畫面，即表示安裝成功。

#### 2.4.2 使用 mkcert 取得本機憑證

為了在本機環境中建立受信任的 SSL 憑證，我們使用 mkcert 工具。

安裝 mkcert：

```bash
sudo apt install mkcert
```

初始化本機憑證機構（Local CA）：

```bash
mkcert -install
```

產生針對 Jenkins 使用的憑證檔案：

```bash
mkcert -cert-file jenkins.pem -key-file jenkins.key localhost
```

這會在目前目錄下建立 jenkins.pem（憑證）與 jenkins.key（私鑰）兩個檔案。請妥善保管。

#### 2.4.3 設定 Nginx 反向代理 Jenkins

接著，建立 Nginx 設定檔 `/etc/nginx/sites-available/jenkins.conf`，內容如下：

```nginx
upstream jenkins {
  keepalive 32;          # keepalive connections
  server localhost:8080; # jenkins ip and port
}

server {
  listen          443 ssl default_server;
  listen          [::]:443 ssl default_server;

  ssl_certificate     /path/to/jenkins.pem;
  ssl_certificate_key /path/to/jenkins.key;

  server_name     localhost;

  #this is the jenkins web root directory (mentioned in the /etc/default/jenkins file)
  root            /var/run/jenkins/war/;

  access_log      /var/log/nginx/access.log;
  error_log       /var/log/nginx/error.log;
  ignore_invalid_headers off; #pass through headers from Jenkins which are considered invalid by Nginx server.

  location ~ "^/static/[0-9a-fA-F]{8}\/(.*)$" {
    #rewrite all static files into requests to the root
    #E.g /static/12345678/css/something.css will become /css/something.css
    rewrite "^/static/[0-9a-fA-F]{8}\/(.*)" /$1 last;
  }

  location /userContent {
    #have nginx handle all the static requests to the userContent folder files
    #note : This is the $JENKINS_HOME dir
        root /var/lib/jenkins/;
    if (!-f $request_filename){
      #this file does not exist, might be a directory or a /**view** url
      rewrite (.*) /$1 last;
          break;
    }
        sendfile on;
  }

  location / {
      sendfile off;
      proxy_pass         http://jenkins;
      proxy_redirect     default;
      proxy_http_version 1.1;

      proxy_set_header   Host              $host;
      proxy_set_header   X-Real-IP         $remote_addr;
      proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header   X-Forwarded-Proto $scheme;
      proxy_max_temp_file_size 0;

      #this is the maximum upload size
      client_max_body_size       10m;
      client_body_buffer_size    128k;

      proxy_connect_timeout      90;
      proxy_send_timeout         90;
      proxy_read_timeout         90;
      proxy_buffering            off;
      proxy_request_buffering    off; # Required for HTTP CLI commands in Jenkins > 2.54
      proxy_set_header Connection ""; # Clear for keepalive
  }
}
```

接下來，請將憑證路徑替換為你實際產生的檔案位置。假設你在家目錄下建立憑證，設定範例如下：

```nginx
ssl_certificate     /home/your_user_name/path/to/jenkins.pem;
ssl_certificate_key /home/your_user_name/path/to/jenkins.key;
```

請確保路徑正確，並具有 Nginx 可讀取的權限。

記得建立符號連結至 /etc/nginx/sites-enabled/：

```bash
sudo ln -s /etc/nginx/sites-available/jenkins.conf /etc/nginx/sites-enabled/jenkins.conf
```

修改後請重新啟動 Jenkins 與 Nginx：

```bash
sudo systemctl restart jenkins
sudo systemctl restart nginx
```

打開瀏覽器並輸入 https://localhost 確認。

## 3 開始使用

首次進入 Jenkins 頁面時，系統會提示你進行「解鎖 Jenkins」的操作：

{{< infobox >}}

Unlock Jenkins
To ensure Jenkins is securely set up by the administrator, a password has been written to the log (not sure where to find it?) and this file on the server:

/var/lib/jenkins/secrets/initialAdminPassword

Please copy the password from either location and paste it below.

Administrator password
{{< /infobox >}}

為了確保 Jenkins 是由管理員安全安裝，系統已將初始密碼寫入伺服器上的記錄檔。
請從上述路徑提示處複製密碼，並貼入下方欄位以完成解鎖。

{{< admonition tip "如何使用終端機取得 Jenkins 初始密碼" >}}

你可以透過以下指令，在終端機中讀取 Jenkins 的初始管理員密碼：

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

執行後，終端機會顯示一串密碼，請將其複製並貼入 Jenkins 解鎖頁面。

{{< /admonition >}}

接下來，Jenkins 會提示你選擇插件的安裝方式。你可以選擇「**安裝推薦插件**（Install suggested plugins）」，讓系統自動安裝一組常用插件；也可以選擇「**手動選擇要安裝的插件**（Select plugins to install）」，依照自己的需求逐一挑選。

如果你是第一次使用 Jenkins，建議先選擇「**安裝推薦插件**」，這樣可以快速開始使用，不必擔心少裝了重要的元件。

完成插件安裝後，Jenkins 會要求你建立管理員帳號，以便後續登入與系統管理。

🔠 後續功能介紹界面語言將以英文為主

### 3.1 切換介面顯示語言

Jenkins 會根據作業系統的語言設定，自動選擇預設的介面語言。

若你希望切換成其他語言（例如繁體中文或英文），則需要額外安裝 Locale 插件，並進行相關設定。

以下是操作步驟：

1. 在 Jenkins 主頁面點選齒輪圖示 ⚙️( `Manage Jenkins` )，接著依序進入：`System Configuration` → `Plugins` → `Available plugins` →  🔍 `Search available plugins`
2. 在搜尋欄輸入 `Locale plugin`，找到後點選安裝
3. - 安裝完成後，回到 Jenkins 主頁面，再次點選齒輪 ⚙️( `Manage Jenkins` )，依序進入：`System Configuration` → `Appearance`，找到 `Default Language` 標籤，即可選擇並切換介面語言

## 4 開始建立你的 Jenkins 專案

根據需求的複雜程度，在 Jenkins 中提供多種專案類型供選擇。

常見 Jenkins 專案類型：

- **Freestyle**專案：最基本的類型，適合簡單任務或初學者。
- **Pipeline**專案：使用 Jenkinsfile 定義整個 CI/CD 流程，適合複雜或多階段部署。

### 4.1 實戰教學：Freestyle 專案範例

建立 Freestyle 專案後，請依照以下步驟設定建置流程：

1. 進入專案頁面，點選 `Configure`
2. 找到 `Build Steps` 區塊，點選 `Add build step` → 選擇 `Execute shell`
3. 在指令欄位中輸入你要執行的 Shell 指令，例如： 

```bash
# 這裡可以根據實際需求撰寫建置腳本，例如編譯程式、執行測試、部署等
echo "Hello Jenkins!"
```

4. 設定完成後，回到專案頁面，點選 `Build Now` 即可執行建置流程
5. 如果一切順利，你會看到以下結果：- ✅ Success 標籤：表示建置成功，Shell 指令已正確執行

{{< admonition info "如果 Shell 腳本發生錯誤？" >}}

當 Shell 指令的回傳值（exit code）不是 0 時，Jenkins 會判定該步驟執行失敗。
你可以透過 Console Output 來檢查錯誤訊息，並針對問題進行修正。

{{< /admonition >}}

{{< admonition tip "讓 Console Output 更乾淨" >}}

在 Shell 腳本中，使用 `set -x` 可以開啟指令追蹤，讓每一行指令在執行前都顯示在 Console Output 中，方便除錯。如果你希望輸出更乾淨、只顯示執行結果，可以使用 `set +x` 來關閉指令提示。

{{< /admonition >}}