---
title: 更改 Azure Blob 存儲中的源（預覽） |微軟文檔
description: 瞭解 Azure Blob 存儲中的更改源日誌以及如何使用它們。
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536882"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>更改 Azure Blob 存儲中的源支援（預覽）

更改源的目的是提供存儲帳戶中 Blob 和 Blob 中繼資料發生的所有更改的事務日誌。 更改源提供這些更改的**有序**、**保證**、**耐用**、**不可變**、**唯讀**日誌。 用戶端應用程式可以隨時讀取這些日誌，無論是在流式處理還是批次處理模式下。 更改源使您能夠構建高效且可擴展的解決方案，以低成本處理 Blob 存儲帳戶中發生的變更事件。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

更改源以標準[Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)成本作為[blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)存儲在存儲帳戶中的特殊容器中。 您可以根據您的要求控制這些檔的保留期（請參閱當前版本的[條件](#conditions)）。 變更事件作為[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式規範中的記錄追加到更改源：一種緊湊、快速、二進位的格式，提供具有內聯架構的豐富資料結構。 此格式廣泛運用在 Hadoop 生態系統、串流分析和 Azure Data Factory。

您可以非同步、增量或完整地處理這些日誌。 任意數量的用戶端應用程式都可以並行和按照自己的節奏獨立讀取更改源。 分析應用程式（如[Apache Drill](https://drill.apache.org/docs/querying-avro-files/)或 Apache [Spark）](https://spark.apache.org/docs/latest/sql-data-sources-avro.html)可以直接使用作為 Avro 檔使用的日誌，這樣您就可以以低成本、高頻寬處理日誌，而無需編寫自訂應用程式。

更改源支援非常適合基於已更改的物件處理資料的方案。 例如，應用程式可以：

  - 更新輔助索引，與緩存、搜尋引擎或任何其他內容管理方案同步。
  
  - 根據物件在流式處理或批次處理模式下發生的更改提取業務分析見解和指標。
  
  - 存儲、審核和分析物件在任何時間段內的更改，以確保企業資料管理的安全性、合規性或智慧性。

  - 構建用於備份、鏡像或複製帳戶中的物件狀態的解決方案，以實現災難管理或合規性。

  - 構建基於創建或更改的物件對變更事件或計畫執行做出反應的連接應用程式管道。

> [!NOTE]
> 更改源提供了對 Blob 發生的更改的持久有序日誌模型。 更改將在更改源日誌中寫入並在更改後的幾分鐘內提供。 如果應用程式必須對此更快地對事件做出反應，請考慮改用 Blob[存儲事件](storage-blob-event-overview.md)。 [Blob 存儲事件](storage-blob-event-overview.md)提供即時一次性事件，使 Azure 函數或應用程式能夠對 Blob 發生的更改快速回應。 

## <a name="enable-and-disable-the-change-feed"></a>啟用並禁用更改源

您必須啟用存儲帳戶上的更改源才能開始捕獲和記錄更改。 禁用更改源以停止捕獲更改。 您可以使用門戶或 Powershell 上的 Azure 資源管理器範本啟用和禁用更改。

在啟用更改源時，需要記住以下幾點。

- 每個存儲帳戶中只有一個 blob 服務的更改源，並且存儲在 **$blobchangefeed**容器中。

- 創建、更新和刪除更改僅在 blob 服務等級捕獲。

- 更改源捕獲帳戶上發生的所有可用事件*的所有*更改。 用戶端應用程式可以根據需要篩選出事件種類。 （請參閱當前版本[的條件](#conditions)）。

- 只有 GPv2 和 Blob 存儲帳戶才能啟用更改源。 當前不支援高級塊 Blob 存儲帳戶和啟用階層命名空間的帳戶。 不支援 GPv1 存儲帳戶，但可以升級到 GPv2，無需停機，有關詳細資訊，請參閱[升級到 GPv2 存儲帳戶](../common/storage-account-upgrade.md)。

> [!IMPORTANT]
> 更改源處於公共預覽版中，可在**西中部**和**西部 2**區域提供。 請參閱本文[的條件](#conditions)部分。 要註冊預覽版，請參閱本文的["註冊訂閱](#register)"部分。 必須先註冊訂閱，然後才能在存儲帳戶上啟用更改源。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶在存儲帳戶上啟用更改源：

1. 在[Azure 門戶](https://portal.azure.com/)中，選擇存儲帳戶。 

2. 導航到**Blob 服務**下的**資料保護**選項。

3. 按一下 **"Blob 更改源**下**啟用"**

4. 選擇 **"保存**"按鈕以確認資料保護設置

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 啟用更改源：

1. 安裝最新的電源殼獲取。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. 關閉，然後重新打開電源殼主控台。

3. 安裝**Az.存儲**預覽模組。

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示進行驗證。

   ```powershell
   Connect-AzAccount
   ```

5. 為存儲帳戶啟用更改源。

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[範本](#tab/template)
使用 Azure 資源管理器範本通過 Azure 門戶在現有存儲帳戶上啟用更改源：

1. 在 Azure 門戶中，選擇 **"創建資源**"。

2. 在 [搜尋 Marketplace]**** 中，輸入**範本部署**，然後按 **ENTER**。

3. 選擇**["部署自訂範本](https://portal.azure.com/#create/Microsoft.Template)**"，然後在**編輯器中選擇"構建您自己的範本**"。

4. 在範本編輯器中，粘貼到以下 json 中。 使用您的儲存體帳戶名稱取代 `<accountName>` 預留位置。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. 選擇 **"保存**"按鈕，指定帳戶的資源組，然後選擇 **"購買**"按鈕以部署範本並啟用更改源。

---

## <a name="consume-the-change-feed"></a>使用更改源

更改源生成多個中繼資料和日誌檔。 這些檔位於存儲帳戶**的$blobchangefeed**容器中。 

> [!NOTE]
> 在當前版本中 **，$blobchangefeed**容器在 Azure 存儲資源管理器或 Azure 門戶中不可見。 當前，當您調用 ListContainer API 時，您當前看不到$blobchangefeed容器，但可以直接在容器上調用 ListBlobs API 以查看 blob。

用戶端應用程式可以使用與更改饋送處理器 SDK 一起提供的 Blob 更改源處理器庫來使用更改源。 

請參閱[Azure Blob 存儲中的進程更改源日誌](storage-blob-change-feed-how-to.md)。

## <a name="understand-change-feed-organization"></a>瞭解更改源組織

<a id="segment-index"></a>

### <a name="segments"></a>使用者分佈

更改源是按**小時***細分*組織的更改日誌，但每隔幾分鐘追加並更新一次。 僅當該小時內發生 Blob 變更事件時，才會創建這些段。 這使用戶端應用程式能夠使用特定時間範圍內發生的更改，而無需在整個日誌中搜索。 要瞭解更多資訊，請參閱[規範](#specifications)。

更改源的可用每小時段在清單檔中描述，該檔指定該段的更改原始檔案的路徑。 `$blobchangefeed/idx/segments/`虛擬目錄的清單顯示按時間排序的這些段。 段的路徑描述段表示的每小時時間範圍的開始。 您可以使用該清單篩選出您感興趣的日誌段。

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> 啟用`$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`更改源時自動創建 。 您可以安全地忽略此檔。 它是一個始終為空的初始化檔。 

段清單檔 （`meta.json`） 在`chunkFilePaths`屬性中顯示該段的更改原始檔案的路徑。 下面是段清單檔的示例。

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> 只有在`$blobchangefeed`您啟用帳戶上的更改源功能後，才會顯示該容器。 啟用更改源後，必須等待幾分鐘，然後才能在容器中列出 Blob。 

<a id="log-files"></a>

### <a name="change-event-records"></a>變更事件記錄

更改原始檔案包含一系列變更事件記錄。 每個變更事件記錄對應于單個 Blob 的一個更改。 記錄使用[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式規範進行序列化並寫入檔。 可以使用 Avro 檔案格式規範讀取記錄。 有幾個庫可用於處理該格式的檔。

更改原始檔案作為[追加](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)blob`$blobchangefeed/log/`存儲在虛擬目錄中。 每個路徑下的第一個更改原始檔案將在`00000`檔案名中（例如`00000.avro`）。 添加到該路徑的每個後續日誌檔的名稱將遞增 1（例如： `00001.avro`。

下面是從轉換原始檔案轉換為 Json 的變更事件記錄的示例。

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

有關每個屬性的說明，請參閱[Blob 存儲的 Azure 事件網格事件架構](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)。

> [!NOTE]
> 創建段後，段的更改原始檔案不會立即顯示。 延遲長度在更改源的發佈延遲的正常間隔內，該間隔在更改後的幾分鐘內。

<a id="specifications"></a>

## <a name="specifications"></a>規格

- 變更事件記錄僅追加到更改源中。 一旦這些記錄被追加，它們是不可改變的，並且記錄位置是穩定的。 用戶端應用程式可以在更改源的讀取位置上維護自己的檢查點。

- 變更事件記錄在更改後的幾分鐘內附加。 用戶端應用程式可以選擇使用記錄，因為它們被追加用於流式訪問，或者在任何其他時間批量使用。

- 變更事件記錄按**每個 blob**的修改順序排序。 在 Azure Blob 存儲中未定義跨 Blob 的更改順序。 前一段中的所有更改都發生在後續段的任何更改之前。

- 使用[Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html)格式規範將變更事件記錄序列化到日誌檔中。

- 更改值為 的內部`eventType``Control`系統記錄且不反映帳戶中物件的更改的事件記錄。 您可以安全地忽略這些記錄。

- 屬性包中`storageDiagnonstics`的值僅供內部使用，不設計供應用程式使用。 您的應用程式不應對該資料具有合同依賴性。 您可以安全地忽略這些屬性。

- 段表示的時間**近似**，邊界為 15 分鐘。 因此，為了確保在指定時間內消耗所有記錄，消耗連續的上一小時和下一小時段。

- 由於日誌流的內部分區來管理發佈輸送量`chunkFilePaths`，每個段可以具有不同數量。 每個`chunkFilePath`日誌檔都保證包含互斥的 Blob，並且可以並行使用和處理，而不會違反反覆運算期間每個 Blob 的修改順序。

- 段以`Publishing`狀態開始。 一旦將記錄追加到段後，它將是`Finalized`。 應用程式不應使用`LastConsumable``$blobchangefeed/meta/Segments.json`檔中屬性日期之後的任何段中的日誌檔。 下面是檔中`LastConsumable`屬性的示例`$blobchangefeed/meta/Segments.json`：

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>註冊訂閱（預覽版）

由於更改源僅在公共預覽版中，因此您需要註冊訂閱才能使用該功能。

### <a name="register-by-using-powershell"></a>使用 PowerShell 進行註冊

在 PowerShell 主控台中，運行以下命令：

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 註冊

在 Azure 雲外殼中，運行以下命令：

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>條件和已知問題（預覽版）

本節介紹更改源的當前公共預覽中的已知問題和條件。 
- 對於預覽版，必須先[註冊訂閱](#register)，然後才能為西中部或西越地區的存儲帳戶啟用更改源。 
- 更改源僅捕獲創建、更新、刪除和複製操作。 中繼資料更新當前未在預覽中捕獲。
- 任何單個更改的變更事件記錄可能會多次顯示在更改源中。
- 您還不能通過設置基於時間的保留原則來管理更改源日誌檔的存留期，並且無法刪除 Blob 
- 日誌檔`url`的屬性當前始終為空。
- 段`LastConsumable`.json 檔的屬性不列出更改源完成的第一個段。 此問題僅在第一個段完成後才會出現。 第一小時後的所有後續段都準確捕獲在屬性中`LastConsumable`。
- 當前，當您調用 List容器 API 時，您當前看不到 **$blobchangefeed**容器，並且該容器不會顯示在 Azure 門戶或存儲資源管理器上
- 以前啟動[帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md)的存儲帳戶可能存在日誌檔未出現的問題。 任何將來的帳戶容錯移轉也可能在預覽期間影響日誌檔。

## <a name="faq"></a>常見問題集

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>更改源和存儲分析日誌記錄之間的區別是什麼？
分析日誌具有所有已讀、寫入、清單和刪除操作的記錄，這些操作具有跨所有操作的成功和失敗的請求。 分析日誌是盡力而為的，並且不保證訂購。

更改源是一種解決方案，它提供成功突變或帳戶更改（如 Blob 創建、修改和刪除）的事務日誌。 更改源保證按每個 Blob 成功更改的順序記錄和顯示所有事件，因此您不必從大量讀取操作或失敗的請求中過濾掉雜色。 更改源從根本上針對需要某些保證的應用程式開發進行了設計和優化。

### <a name="should-i-use-change-feed-or-storage-events"></a>我應該使用更改源或存儲事件？
您可以利用更改源和[Blob 存儲事件](storage-blob-event-overview.md)這兩個功能，提供相同的資訊，同時保證相同的傳遞可靠性，主要區別在於事件記錄的延遲、排序和存儲。 更改源在更改後的幾分鐘內將記錄發佈到日誌，並保證每個 Blob 的更改操作順序。 存儲事件即時推送，可能無法排序。 更改源事件可持久地存儲在存儲帳戶中，作為唯讀穩定日誌，具有自己的定義保留，而存儲事件是暫時性的，由事件處理常式使用，除非您顯式存儲它們。 使用更改源，任意數量的應用程式都可以使用 blob API 或 SDK 在自身方便時使用日誌。 

## <a name="next-steps"></a>後續步驟

- 請參閱如何使用 .NET 用戶端應用程式讀取更改源的示例。 請參閱[Azure Blob 存儲中的進程更改源日誌](storage-blob-change-feed-how-to.md)。
- 瞭解如何即時回應事件。 請參閱[對 Blob 存儲事件的反應](storage-blob-event-overview.md)
- 詳細瞭解所有請求的成功操作和失敗操作的詳細日誌記錄資訊。 請參閱[Azure 存儲分析日誌記錄](../common/storage-analytics-logging.md)
