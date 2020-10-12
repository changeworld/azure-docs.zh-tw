---
title: azcopy 工作臺 |Microsoft Docs
description: 本文提供 azcopy 工作臺命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282002"
---
# <a name="azcopy-benchmark"></a>azcopy 基準測試

藉由將測試資料上傳或下載至指定的目的地，來執行效能基準測試。 若為上傳，則會自動產生測試資料。

基準測試命令會執行與「複製」相同的處理常式，但下列情況除外： 

  - 基準測試不需要同時需要來源和目的地參數，而是只會採用一個。 這是您想要上傳或從中下載的 blob 容器、Azure 檔案儲存體共用或 Azure Data Lake Storage Gen2 檔案系統。

  - ' Mode ' 參數描述 AzCopy 是否應該測試上傳至指定的目標或從中下載。 有效的值為「上傳」和「下載」。 預設值為 [上傳]。

  - 針對上傳基準測試，裝載是由命令列參數所描述，可控制要自動產生的檔案數目，以及檔案的重要程度。 產生進程會完全在記憶體中進行。 未使用磁片。

  - 針對下載，承載會包含來源上已存在的任何檔案。  (請參閱下面的範例，瞭解如何在需要時產生測試檔案) 。
  
  - 只支援複製命令所提供的幾個選擇性參數。
  
  - 系統會測量並回報其他診斷。
  
  - 針對上傳，預設行為是在測試回合結束時刪除已傳送的資料。  針對下載，資料永遠不會儲存在本機。

基準測試模式會自動調整為提供最大輸送量的平行 TCP 連接數目。 它會在結尾顯示該數位。 若要防止自動優化，請將 AZCOPY_CONCURRENCY_VALUE 環境變數設定為特定數目的連接。 

所有一般驗證類型都受到支援。 不過，最便利的上傳方法通常是使用 SAS 權杖建立空白容器，並使用 SAS 驗證。  (下載模式需要有一組測試資料存在於目標容器中。 ) 

## <a name="examples"></a>範例

```azcopy
azcopy benchmark [destination] [flags]
```

使用預設參數執行基準測試， (適用于最多 1 Gbps 的網路效能評定) ： '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
執行基準測試，以上傳100檔案，每個大小為 2 GiB： (適用于快速網路上的效能評定，例如 10 Gbps) ： '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
執行基準測試測試，但使用50000檔案，每8個 MiB 大小並計算其 MD5 雜湊的 (方式，與 `--put-md5` 旗標在複製命令) 中執行這項工作的方式相同。 `--put-md5`基準測試的目的是要測試 MD5 計算是否會影響所選檔案計數和大小的輸送量：

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

執行基準測試，以從目標下載現有的檔案

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

執行不會刪除已傳送檔案的上傳。  (這些檔案可作為下載測試的承載) 

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>選項。

**--blob 類型** 字串會定義目的地的 blob 類型。 用來允許基準不同的 blob 類型。 與複製命令中的相同名稱參數相同 (預設的「偵測」 ) 。

**--區塊大小-mb** 浮點數使用 (在 MiB) 中指定的區塊大小。 預設值會根據檔案大小自動計算。 允許小數分數，例如0.25。 與複製命令中的名稱相同的參數相同。

**--檢查長度**  在傳輸之後，檢查目的地上的檔案長度。 如果來源和目的地之間有不相符的情況，則會將傳輸標示為失敗。  (預設值為 true) 

**--刪除-測試-資料**  若為 true，基準測試資料將會在基準測試執行結束時刪除。  如果您想要保留目的地的資料，請將它設定為 false-例如，將它用於基準測試模式以外的手動測試 (預設值為 true) 。

**--file-count** uint。  要使用 (預設 100) 的自動產生資料檔案數目。

**--**  說明 適用于工作臺的說明

**--記錄層級** 字串會定義記錄檔的記錄詳細資訊、可用層級： (所有要求/回應的資訊) 、警告 (回應緩慢) 、錯誤 (只有失敗的要求) ，以及無 (沒有輸出記錄) 。  (預設的 [資訊] ) 

**--mode** 字串會定義 Azcopy 是否應該測試這個目標的上傳或下載。 有效的值為「上傳」和「下載」。 預設選項為 [上傳]。  (預設的 [上傳] ) 

**--資料夾** uint 如果大於0，則建立資料夾來分割資料。

**--put-md5**  建立每個檔案的 MD5 雜湊，並將雜湊儲存為目的地 blob/檔案的 Content-type-MD5 屬性。  (預設不會建立雜湊。 ) 與複製命令中相同名稱的參數相同。

**--** 每個自動產生的資料檔案大小每個檔案的字串大小。 必須是緊接在 K、M 或 G 後面的數位。例如 12k 或 200G (預設值 "250M" ) 。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps float**  以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。

**--** 命令輸出的輸出類型字串格式。 選項包括： text、json。 預設值為 [text]。  (預設的「文字」 ) 。

**--受信任-microsoft 尾碼** 字串指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。


## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
