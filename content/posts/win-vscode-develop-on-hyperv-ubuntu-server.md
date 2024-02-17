+++
title = '開發超方便! 如何從Windows端vscode遠端至Ubuntu Server虛擬機寫code及開發'
date = 2024-02-17
thumbnail = {src = "/thumbnail/win-vscode-develop-on-hyperv-ubuntu-server_11zon.jpg", visibility = "list"}
draft = true
authorbox = false
sidebar = true
pager = true
comments = false
categories = ["Ubuntu"]
tags = ["Hyper-V", "Ubuntu", "VSCode"]
+++

平日使用Windows看劇打電動寫投影片，要開發時就切換到Ubuntu。開發者們經常因不同需要，在多種作業系統中切換。有時候，這可能會變得相當麻煩，尤其是當你需要在不同的作業系統中運行不同的應用程式或工具時。 但是，有沒有想過如果你可以在一台PC上運行兩套OS環境，並且可以輕鬆地從Windows端的vscode遠端至Ubuntu Server虛擬機寫code及開發，那會是多麼的方便呢？ 在這篇文章中將展示要如何做到這一點!

<!--more-->

首先，我們需要在Windows環境下架設Hyper-V虛擬機安裝Ubuntu Server，注意這邊**不會安裝Ubuntu桌面版本**，也就是不會有圖形介面的Ubuntu喔 (主要是沒有使用圖形介面的需求就裝輕量級的伺服器版本)。

請先參考這篇文章[在Hyper-V虛擬機器上安裝Ubuntu](/posts/install-ubuntu-on-hyperv-vm)完成Ubuntu Server安裝作業

{{< notice warning >}}
This is a warning notice. Be warned!
{{< /notice >}}