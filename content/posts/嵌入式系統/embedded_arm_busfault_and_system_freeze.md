+++
title       = 'ARM 架構的 Bus Fault 與 系統死機'
date        = '2026-05-15T12:00:00+08:00'
draft       = true
author      = 'Jim00000'
tags        = ['ARM', '嵌入式系統', '學習筆記']
categories  = ['嵌入式系統']
+++

在ARM Cortex M33 上開發最常見到的問題：BusFault 或是 系統不明原因整個凍結死機 (system freeze，典型的狀況是CPU完全部會動，JTAG / SWD 會完全連不上) 了。


## AHB 的錯誤回應機制

- **HREADYOUT**：由 slave 端輸出，表示當前傳輸是否完成
    - `HREADYOUT=1` → 傳輸完成，Master 可以繼續下一筆
    - `HREADYOUT=0` → 傳輸延遲，Master 必須等待

- **HRESP**：由 slave 端輸出，表示傳輸結果
    - `HRESP = 0` (OKAY) → 傳輸成功
    - `HRESP = 1` (ERROR) → 傳輸失敗，表示 bus error

下面來個別推測造成的原因：

## Bus Fault

當 `HREADYOUT = 1` 且 `HRESP = 1` 時，代表 slave 已經完成傳輸，但結果是錯誤 (bus error)。這會被 CPU 視為 BusFault，進入 BusFault Handler。

## 系統死機

如果 slave 沒有回應 (decoder 沒有對應 slave)，HREADYOUT 一直是 0，匯流排會一直等待，CPU 看不到「完成」訊號。從使用者角度來看行為就是系統死機而卡住沒有回應。

常見情境是使用到未映射位址。