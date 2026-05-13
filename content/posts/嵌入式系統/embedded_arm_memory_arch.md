+++
title       = 'ARM 架構的記憶體模型'
date        = '2026-05-13T11:03:25+08:00'
draft       = true
author      = 'Jim00000'
tags        = ['ARM', '嵌入式系統', '學習筆記']
categories  = ['嵌入式系統']
+++

在 AMBA AHB 匯流排協定 中，**HPROT** 是「保護控制信號 (Protection Control Signals)」，用來描述存取的屬性。

| BIT      | 欄位名稱    |
|:--------:|:----------:|
| HPROT[0] | Data/Inst  |
| HPROT[1] | Privileged |
| HPROT[2] | Bufferable |
| HPROT[3] | Modifiable |
| HPROT[4] | Lookup     |
| HPROT[5] | Allocate   |
| HPROT[6] | Sharable   |


## 常見問題

### Device-nE v.s. Device-E

Device-nE 與 Device-E 的差別在 「**<span style="color:red">寫入完成的判定方式</span>**」： <br/>
Device-E 允許 **Early Write Acknowledgement**（提早回覆寫入完成），Device-nE 則必須等到資料真正到達目的地才算完成。

| 類型 | 定義 | 行為 | 效能與風險 | 適用情境 | 舉例 |
|-----|------|-----|----------|---------|-----|
|Device-E|Early Write Acknowledgement|寫入在到達中介節點（如匯流排緩衝器）即可被視為完成，CPU 可繼續執行下一指令|效能較好，但可能導致程式認為寫入已完成，實際上週邊尚未更新。若程式馬上依賴該寫入結果，可能出現錯誤|適合對延遲不敏感的週邊，提升效能| FIFO |
|Device-nE|No Early Write Acknowledgement|必須等到寫入真正到達目的地（週邊寄存器）才算完成|安全可靠，確保週邊狀態已更新，但效能稍差|適合需要嚴格保證寫入完成的週邊，例如控制暫存器| 控制暫存器或需要嚴格同步的裝置 |

---

### Observable 的意思?

當一個寫入操作回覆成功 (write response) 給發起者時，這個寫入必須已經能被系統中所有其他的 Manager（通常指其他 CPU 核心或匯流排主控端）看見。

---

### Write-Back v.s. Write-Through

- **Write-Back** : 資料先寫入快取，只有在快取區塊被替換時才寫回主記憶體。
- **Write-Through** : 資料同時寫入快取與主記憶體。

| 特性 | Write-Through | Write-Back |
|:---:|:-------------:|:----------:|
|寫入速度|較慢|較快|
|資料一致性|永遠同步|可能不同步|
|管理複雜度|簡單|複雜|
|用途|可靠性|效能|