---
title: azcopy 工作臺 |Microsoft Docs
description: 本文提供 azcopy 工作臺命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 40ff6c6c76e255945681e678ef296ffcf9978f61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485179"
---
# <a name="azcopy-benchmark"></a>azcopy 基準測試

將測試資料上傳至指定的目的地，以執行效能基準測試。 測試資料會自動產生。

基準測試命令會執行與「複製」相同的上傳程式，不同之處在于：

  - 沒有來源參數。  此命令只需要一個目的地 URL。 
  
  - 裝載是由命令列參數所描述，可控制自動產生的檔案數目和大小。 產生進程會完全在記憶體中進行。 未使用磁片。
  
  - 僅支援複製命令可用的幾個選擇性參數。
  
  - 系統會測量並回報額外的診斷。
  
  - 根據預設，在測試回合結束時，會刪除傳送的資料。

基準測試模式會自動將其本身調整為提供最大輸送量的平行 TCP 連接數目。 它會在結尾顯示該數位。 若要避免自動優化，請將 AZCOPY_CONCURRENCY_VALUE 環境變數設定為特定的連線數目。

所有一般的驗證類型都受到支援。 不過，最方便的效能評定方法通常是使用 SAS 權杖來建立空的容器，並使用 SAS 驗證。

## <a name="examples"></a>範例

```azcopy
azcopy benchmark [destination] [flags]
```

使用預設參數執行基準測試（適用于最多 1 Gbps 的效能評定網路）： '

- azcopy 工作臺 "HTTPs：//[account]. .net/[container]？ <SAS> "

執行上傳100檔案的基準測試，每2個 GiB 的大小：（適用于快速網路的效能評定，例如 10 Gbps）： '

- azcopy 工作臺 "HTTPs：//[account]. .net/[container]？ <SAS> "--檔案計數 100--每個檔案的大小2G

執行基準測試測試，但使用50000檔案，每 8 MiB 大小並計算其 MD5 雜湊（如同在 `--put-md5` copy 命令中執行此項的相同方式）。 效能評定的目的 `--put-md5` 是要測試 MD5 計算是否會影響所選檔案計數和大小的輸送量：

- azcopy 工作臺 "HTTPs：//[account]. .net/[container]？ <SAS> " --檔案計數 50000--每個檔案的大小 8M--put-md5

## <a name="options"></a>選項

**--blob 類型**字串會定義目的地的 blob 類型。 用來允許對不同的 blob 類型進行基準測試。 與 copy 命令中相同名稱的參數（預設值為 "偵測"）相同。

**--區塊大小-mb** float 使用此區塊大小（以 MiB 指定）。 預設值會根據檔案大小自動計算。 允許小數分數-例如0.25。 與 copy 命令中相同名稱的參數相同。

**--刪除-測試-資料** 若為 true，基準測試資料將會在基準測試執行結束時刪除。  如果您想要將資料保留在目的地（例如，用於在基準測試模式以外的手動測試，請將它設定為 false）。

**--檔案計數**uint 要使用之自動產生的資料檔案數目（預設值為100）。

**-h、--help** 適用于工作臺的說明

**--記錄層級**字串會定義記錄檔的記錄詳細資訊、可用的層級： INFO （所有要求/回應）、警告（回應緩慢）、錯誤（僅限失敗的要求）和無（沒有輸出記錄）。 （預設值為 "INFO"）

**--put-md5** 建立每個檔案的 MD5 雜湊，並將雜湊儲存為目的地 blob/檔案的 Content-MD5 屬性。 （根據預設，不會建立雜湊）。與 copy 命令中相同名稱的參數相同。

**--** 每個自動產生之資料檔案的每個檔案大小字串大小。 必須為數字，後面緊接著 K、M 或 G。例如 12k 或200G （預設值為 "250M"）。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps uint32** 上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**--輸出-** 命令輸出的類型字串格式。 選項包括： text、json。 預設值為「文字」。 （預設值為 "text"）。

**--[受信任]-[microsoft 尾碼**] 字串會指定可以傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
