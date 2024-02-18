+++
title = 'Ubuntu Server虛擬機沒有圖形介面，要如何查看產生的圖表/多媒體檔案呢? 透過Samba協議與Windows共享資料夾，從Windows端查看'
date = 2024-02-18
draft = true
authorbox = false
sidebar = true
pager = true
comments = false
categories = ["Ubuntu"]
tags = ["Hyper-V", "Ubuntu"]
+++

當你在虛擬機上運行 Ubuntu 時，有時會選擇不安裝圖形介面以節省資源並提高效能而安裝伺服器版本 Ubuntu 。但是，如果你需要查看產生的圖表或多媒體檔案，伺服器版本 Ubuntu 沒有圖型介面操作，該怎麼辦呢？透過 Samba 網路協定連結 Windows 共享資料夾，你可以從 Windows 端輕鬆檢視使用這些檔案。

<!--more-->

### 為 Ubuntu 安裝設定 Samba

```shell
sudo apt install samba
```

安裝完 Samba 套件後，調整 samba 設定檔: `sudo vim /etc/samba/smb.conf`，
在設定檔最後添加如下：

```
[samba]
    comment = samba file share
    path = /home/alex/samba
    available = yes
    valid users = alex
    browseable = yes
    writeable = yes
    read only = no
    guest ok = no
```

修改完畢後控制台輸入 `sudo service smbd restart` 重啟 Samba

### 為 Samba 協議添加使用者

為 Samba 協議添加使用者請先確認使用者是否已存在 Linux 帳戶裡，如果 Linux 裡面沒有此使用者是沒辦法加入 Samba 協議裡的

```shell
sudo smbpasswd -a alex
```

使用**pdbedit**指令查看使用者是否有加進 Samba 協議裡：

```shell
sudo pdbedit -L -v
```

### 讓 Ubuntu 防火牆 (UFW) 允許 Samba 服務

```shell
sudo ufw allow samba
```

### 從 Windows 端連入 Ubuntu 共享資料夾

打開檔案總管 ⇨ 左版面💻本機點擊滑鼠右鍵 ⇨ 連線網路磁碟機...

![](/images/windows-samba-localmachine-menu.png)

在資料夾處填寫格式如此處：`\\ubuntu-server-ip-address\samba`

![](/images/windows-samba-localmachine-webdisk.png)