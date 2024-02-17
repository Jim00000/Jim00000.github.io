+++
title = '在Hyper-V虛擬機器上安裝Ubuntu'
date = 2024-02-13T14:51:51Z
draft = true
authorbox = false
sidebar = true
pager = true
comments = false
categories = ["Ubuntu"]
tags = ["Hyper-V", "Ubuntu"]
+++

在現代軟體開發和測試中，虛擬機器成為了不可或缺的工具。您可能會需要Linux環境做開發用途，同時也需要Windows平台使用Microsoft Office處理文書作業或遊玩Steam遊戲。在此應用場景下，透過虛擬化技術，我們可以在單一實體主機上運行多個獨立的作業系統，這對於測試、開發和學習來說非常有用。

本文將引導您如何在 Windows 11 中使用 Hyper-V 建立一個 Ubuntu 虛擬機器而非使用 **WSL**[^1]。Hyper-V 是 Windows 內建的虛擬機器，它允許我們在電腦上新增多部虛擬機器，並在其中安裝不同的作業系統，例如 Windows、Linux 等等。

讓我們一步步來設定您的 Hyper-V 環境並安裝 Ubuntu 吧！

[^1]: **WSL**: **W**indows **S**ubsystem for **L**inux. 供參考 [MSDN - What is the Windows Subsystem for Linux?](https://learn.microsoft.com/en-us/windows/wsl/about)

<!--more-->

## ◆ 準備好您的環境與Ubuntu軟體

- Windows 11 專業版 (家用版不支援Hyper-V)
- [Ubuntu Server ISO Image](https://ubuntu.com/download/server)

## ◆ 建立適用於Ubuntu的虛擬機器

![targets](/images/search-hyperv-manager.png)

![targets](/images/hyperv-manager-action-view.png)

### 設定虛擬交換器

接下來在**動作**頁面 ⇨ 點選**虛擬交換器管理員...**

![targets](/images/hyperv-virtual-switch-wizard-step1.png)

本文示範取名為 *Hyper-V Virtual Switch*，選外部網路(使用無線網路上網選Wi-Fi；有線網路選Ethernet)

![targets](/images/hyperv-virtual-switch-wizard-step2.png)

### 設定虛擬機器

接下來在**動作**頁面 ⇨ 點選**新增** ⇨ 點選**虛擬機器**

- 指定名稱與位置 : 本文示範取名為 *Ubuntu*

![targets](/images/hyperv-vm-create-wizard-step2.png)

- 指派世代 : 選擇 **第二代**
- 指派記憶體 : 依照個人電腦配置條整
- 設定網路功能 : 選擇連線至剛創立好的 **Hyper-V Virtual Switch**
- 連結虛擬硬碟 : 選建立虛擬硬碟，大小依照個人電腦配置條整
- 安裝選項 : 選 "可開機 CD/DVD-ROM 安裝作業系統"，映像檔(.iso)選 Ubuntu Server 的 ISO 檔

建立好之後在虛擬機器頁面下可以看到有Ubuntu的名稱，我們還要接著做些設定處裡**安全開機**、**網路介面卡**設定、**檢查點**(自行選用)

在Ubuntu處點選右鍵 ⇨ 點選**設定**

- 硬體 ⇨ 安全性 ⇨ 點選**啟用安全開機** ⇨ 範本選擇**Microsoft UEFI 憑證授權單位**
- 硬體 ⇨ 網路介面卡 ⇨ 進階功能 ⇨ MAC位址 ⇨ 點選**靜態**

檢查點的功能使用上發現蠻占用空間的，如果有空間使用考量可以考慮關閉此功能

- 管理 ⇨ 檢查點 ⇨ 檢查點類型 ⇨ 取消點選**啟用檢查點**

**自動選取動作**與**自動停止動作**，控制實體電腦開機/關機時虛擬機器的行為，可依照個人需求調整

## ◆ 安裝 Ubuntu Server

Ubuntu Server 的安裝介面如下圖，基本上狂按 Enter 鍵使用預設設定即可

有個選項會詢問是否要安裝OpenSSH，如果日後想要以 SSH 方式連進 Ubuntu 虛擬機記得點選安裝

![targets](/images/Ubuntu-Server-Install.png)

## ◆ 啟動 Ubuntu Server

在虛擬機器頁面下，Ubuntu項目點選右鍵 ⇨ 點選**啟動**，出現如下圖視窗：

![targets](/images/ubuntu-server-startup.png)

這時候可以輸入您的帳號與密碼登入Ubuntu Server囉~

## ◆ 在實體電腦端設定SSH免密碼方式連進Ubuntu Server

非必要的功能但是強烈建議您實裝此功能，就由透過於實體電腦端建立好SSH公私鑰與Ubuntu客體端認證，以免除繁雜的輸入密碼方式登入

首先，在實體電腦端安裝[git-scm](https://git-scm.com/downloads)，此安裝檔內附帶bash shell與SSH client/keygen。

安裝完畢git-scm後，到任意目錄下點選右鍵可以看到選單有"Open Git Bash Here"，點選可以於此目錄下開啟Bash shell

### 設定ssh config

首先，設定好Ubuntu Server的**hostname**以及登入的**username**，於%USERPROFILE%/.ssh的目錄下建立名為**config**的檔案，
並假設：

- hostname: **192.168.0.18**
- username: **alex**

```
Host Ubuntu-Server
    HostName 192.168.0.18
    User alex
```

Ubuntu Server的IP位址獲取方式可以用`ip a`方式得知，上述步驟完成，可以輸入`ssh alex@Ubuntu-Server`看能不能聯繫上 Ubuntu Server 虛擬機

### 設定ssh金鑰

可參照 Github文件[^2] 方式了解如何產生金鑰對，或是參考筆者的方法如下：

```shell {linenos=inline}
ssh-keygen -t ed25519 -C <your-email-address>
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh-copy-id -i ~/.ssh/id_ed25519.pub alex@Ubuntu-Server
```

以後可以免密碼方式SSH連入Ubuntu Server虛擬機~

在Windows端打開powershell或是git bash，輸入指令: `ssh alex@Ubuntu-Server`確認是否能免密碼登入Ubuntu

## ◆ 為Ubuntu Server設定防火牆 (UFW)

```shell {linenos=inline}
ufw default deny incoming
ufw default allow outgoing
# (可選) 允許SSH通訊
ufw allow ssh
# (可選) 允許Samba協議
ufw allow samba
```

以上都設定完畢後，瀏覽目前防火牆的狀態： `ufw status numbered`

[^2]: 可參考 [Github Authentication documentation](https://docs.github.com/en/authentication)