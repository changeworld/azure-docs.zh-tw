---
title: 阿茲皮斯長凳 |微軟文檔
description: 本文為阿茲比圖工作臺命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518300"
---
# <a name="azcopy-bench"></a>azcopy 工作台

通過將測試資料上載到指定目標來運行性能基準。 測試資料將自動生成。

基準命令運行與"複製"相同的上載過程，但以下情況除外：

  - 沒有源參數。  該命令只需要一個目標 URL。 在當前版本中，此目標 URL 必須引用 Blob 容器。
  
  - 有效負載由命令列參數描述，這些參數控制自動生成的檔數及其大。 生成過程完全發生在記憶體中。 不使用磁片。
  
  - 僅支援複製命令可用的幾個可選參數。
  
  - 測量並報告其他診斷。
  
  - 預設情況下，傳輸的資料在測試回合結束時被刪除。

基準模式將自動調整到提供最大輸送量的並行 TCP 連接數。 它將在末尾顯示該數位。 為了防止自動調整，將AZCOPY_CONCURRENCY_VALUE環境變數設置為特定數量的連接。

支援所有常用的身份驗證類型。 但是，基準測試最方便的方法是使用 SAS 權杖創建空容器並使用 SAS 身份驗證。

## <a name="examples"></a>範例

```azcopy
azcopy bench [destination] [flags]
```

使用預設參數運行基準測試（適用于高達 1 Gbps 的基準網路）：'

- 茲比貝長凳"HTTPs：//_帳戶]blob.core.windows.net/[容器]？<SAS>"

運行一個基準測試，上傳 100 個檔，每個 2 個 GiB 大小：（適用于快速網路上的基準測試，例如 10 Gbps）：'

- 茲比貝長凳"HTTPs：//_帳戶]blob.core.windows.net/[容器]？<SAS>" --檔計數 100 -- 每個檔案大小 2G

與上述相同，但使用 50，000 個檔，每個 8 MiB 的大小並計算其 MD5 雜湊值（與 --put-md5 標誌在複製命令中執行此項的方式相同）。 當基準測試時 --put-md5 的目的是測試 MD5 計算是否影響所選檔計數和大小的輸送量：

- 茲比貝長凳"HTTPs：//_帳戶]blob.core.windows.net/[容器]？<SAS>" --檔計數 50000 -- 每個檔案大小 8M -- put-md5

## <a name="options"></a>選項。

**--blob 類型**字串 定義目標上的 blob 類型。 用於允許對不同的 Blob 類型進行基準測試。 與複製命令中的相同具名引數相同（預設"檢測"）。

**--塊大小-mb**浮點使用此塊大小（在 MiB 中指定）。 預設值根據檔案大小自動計算。 允許十進位分數 - 例如 0.25。 與複製命令中的同名參數相同。

**--刪除測試資料** 如果為 true，基準資料將在基準運行結束時刪除。  如果要將資料保留在目標狀態，請將其設置為 false - 例如，將其用於基準模式以外的手動測試（預設為 true）。

**--檔計數**uint 要使用的自動生成資料檔案的數量（預設 100）。

**-h， -- 説明** 長凳説明

**--日誌級**字串 定義日誌檔的日誌詳細性、可用級別：INFO（所有請求/回應）、警告（慢速回應）、ERROR（僅失敗的請求）和 NONE（無輸出日誌）。 （預設"INFO"）

**--put-md5** 創建每個檔的 MD5 雜湊，並將雜湊保存為目標 blob/檔的內容-MD5 屬性。 （預設情況下不會創建雜湊。與複製命令中的同名參數相同。

**--每個**自動生成的資料檔案的大小每個檔字串大小。 必須是一個數位，緊跟 K、M 或 G. 12k 或 200G（預設"250M"）。

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

**--蓋-mbps uint32** 以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。

**--輸出類型**字串 命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。 （預設"文本"）。

## <a name="see-also"></a>另請參閱

- [阿茲比貝](storage-ref-azcopy.md)
