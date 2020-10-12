---
title: Azure Blob 儲存體中的變更摘要 |Microsoft Docs
description: 瞭解 Azure Blob 儲存體中的變更摘要記錄，以及如何使用它們。
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: c3348356561ea74bb5e0b5bc46fccee1ada82755
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568229"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Azure Blob 儲存體中的變更摘要支援

變更摘要的目的是要提供儲存體帳戶中 blob 和 blob 中繼資料所發生之所有變更的交易記錄。 變更摘要提供這些變更的已 **排序**、 **保證**、 **持久**、 **不可變**、 **唯讀** 記錄。 用戶端應用程式可以在資料流程或批次模式中隨時讀取這些記錄。 變更摘要可讓您建立有效率且可調整的解決方案，以低成本的方式處理 Blob 儲存體帳戶中發生的變更事件。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

變更摘要會以 [blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 的形式儲存在儲存體帳戶的特殊容器中，標準 [blob 價格](https://azure.microsoft.com/pricing/details/storage/blobs/) 成本。 您可以根據需求來控制這些檔案的保留期限 (查看目前版本) 的 [條件](#conditions) 。 變更事件會附加至變更摘要做為 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 格式規格中的記錄：精簡、快速、二進位格式，可使用內嵌架構提供豐富的資料結構。 此格式廣泛運用在 Hadoop 生態系統、串流分析和 Azure Data Factory。

您可以用累加方式或完整方式來處理這些記錄。 任何數目的用戶端應用程式都可以依自己的步調，以平行方式獨立讀取變更摘要。 [Apache 演練](https://drill.apache.org/docs/querying-avro-files/)或[Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html)之類的分析應用程式可以直接取用記錄檔做為 Avro 檔案，讓您以低成本的方式處理這些記錄，並使用高頻寬，而不需要撰寫自訂的應用程式。

變更摘要支援非常適合用來根據已變更之物件處理資料的案例。 例如，應用程式可以：

  - 更新次要索引，並與快取、搜尋引擎或任何其他內容管理案例進行同步處理。
  
  - 根據物件發生的變更，以串流方式或分批模式，擷取商務分析見解和計量。
  
  - 針對企業資料管理的安全性、合規性或情報，儲存、稽核及分析任何期間的物件變更。

  - 建立解決方案來備份、鏡像或複寫您帳戶中的物件狀態，以進行嚴重損壞管理或合規性。

  - 建立已連線的應用程式管線，以根據已建立或已變更的物件來回應變更事件或排程執行。
  
變更摘要是區塊 blob 的 [物件](object-replication-overview.md) 複寫和 [時間點還原](point-in-time-restore-overview.md)的必要條件功能。

> [!NOTE]
> 變更摘要可針對 blob 所發生的變更，提供持久、排序的記錄模型。 變更會在變更的幾分鐘內，于變更摘要記錄檔中寫入和提供。 如果您的應用程式必須以更快的速度回應事件，請考慮改用 [Blob 儲存體事件](storage-blob-event-overview.md) 。 [Blob 儲存體事件](storage-blob-event-overview.md) 提供即時的一次性事件，可讓您的 Azure Functions 或應用程式快速回應發生于 Blob 的變更。 

## <a name="enable-and-disable-the-change-feed"></a>啟用和停用變更摘要

您必須在儲存體帳戶上啟用變更摘要，才能開始捕獲和記錄變更。 停用變更摘要以停止捕獲變更。 您可以使用入口網站或 PowerShell 上的 Azure Resource Manager 範本來啟用和停用變更。

當您啟用變更摘要時，請記住以下幾點。

- 每個儲存體帳戶中的 blob 服務只會有一個變更摘要，而且會儲存在 **$blobchangefeed** 容器中。

- 建立、更新和刪除變更只會在 blob 服務層級上進行。

- 變更摘要會針對帳戶中發生的所有可用事件，捕捉 *所有* 變更。 用戶端應用程式可以視需要篩選出事件種類。  (查看目前版本) 的 [條件](#conditions) 。

- 只有 GPv2 和 Blob 儲存體帳戶可以啟用變更摘要。 目前不支援 Premium BlockBlobStorage 帳戶和已啟用階層命名空間的帳戶。 GPv1 儲存體帳戶不受支援，但可升級至 GPv2 而不會有停機時間，如需詳細資訊，請參閱 [升級至 GPv2 儲存體帳戶](../common/storage-account-upgrade.md) 。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站，在您的儲存體帳戶上啟用變更摘要：

1. 在 [Azure 入口網站中](https://portal.azure.com/)，選取您的儲存體帳戶。

2. 瀏覽至 [Blob 服務] 底下的 [資料保護] 選項。

3. 按一下 [ **Blob 變更**摘要] 下的 [**啟用**]。

4. 選擇 [ **儲存** ] 按鈕以確認您的 **資料保護** 設定。

    ![顯示資料保護設定的螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 啟用變更摘要：

1. 安裝最新的 PowershellGet。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. 關閉，然後重新開啟 PowerShell 主控台。

3. 安裝2.5.0 版或更新版本的 **Az** 模組。

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示進行驗證。

   ```powershell
   Connect-AzAccount
   ```

5. 啟用儲存體帳戶的變更摘要。

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[範本](#tab/template)
使用 Azure Resource Manager 範本，透過 Azure 入口網站在您現有的儲存體帳戶上啟用變更摘要：

1. 在 [Azure 入口網站中，選擇 [ **建立資源**]。

2. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。

3. 選擇 [ **[部署自訂範本](https://portal.azure.com/#create/Microsoft.Template)**]，然後選擇 **編輯器中的 [建立您自己的範本**]。

4. 在範本編輯器中，貼上下列 json。 使用您的儲存體帳戶名稱取代 `<accountName>` 預留位置。

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
    
5. 選擇 [ **儲存** ] 按鈕，指定帳戶的資源群組，然後選擇 [ **購買** ] 按鈕以部署範本並啟用變更摘要。

---

## <a name="consume-the-change-feed"></a>使用變更摘要

變更摘要會產生數個中繼資料和記錄檔。 這些檔案位於儲存體帳戶的 **$blobchangefeed** 容器中。 

> [!NOTE]
> 在目前的版本中，Azure 儲存體總管或 Azure 入口網站中看不到 **$blobchangefeed** 的容器。 當您呼叫 ListContainers API 時，您目前無法看到 $blobchangefeed 容器，但您可以直接在容器上呼叫 ListBlobs API 來查看 blob。

您的用戶端應用程式可以使用變更摘要處理器 SDK 隨附的 blob 變更摘要處理器程式庫來取用變更摘要。 

請參閱 [Azure Blob 儲存體中的處理變更摘要記錄](storage-blob-change-feed-how-to.md)。

## <a name="understand-change-feed-organization"></a>瞭解變更摘要組織

<a id="segment-index"></a>

### <a name="segments"></a>使用者分佈

變更摘要是組織成**每小時***區段*的變更記錄，但會每隔幾分鐘附加到並更新。 只有在該小時內發生 blob 變更事件時，才會建立這些區段。 這可讓您的用戶端應用程式使用在特定時間範圍內發生的變更，而不需要搜尋整個記錄。 若要深入瞭解，請參閱 [規格](#specifications)。

變更摘要的可用每小時區段描述于指定該區段變更摘要檔案路徑的資訊清單檔中。 虛擬目錄的清單會 `$blobchangefeed/idx/segments/` 顯示依時間排序的這些區段。 區段的路徑描述區段所代表的每小時時間範圍開始。 您可以使用該清單來篩選出您感興趣的記錄區段。

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`當您啟用變更摘要時，會自動建立。 您可以放心地忽略此檔案。 它是永遠空白的初始化檔。 

區段資訊清單檔 (`meta.json`) 會顯示內容中該區段之變更摘要檔案的路徑 `chunkFilePaths` 。 以下是區段資訊清單檔的範例。

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
> `$blobchangefeed`只有在您的帳戶上啟用變更摘要功能之後，才會顯示容器。 您必須在啟用變更摘要之後等候幾分鐘，才能列出容器中的 blob。 

<a id="log-files"></a>

### <a name="change-event-records"></a>變更事件記錄

變更摘要檔案包含一連串的變更事件記錄。 每個變更事件記錄都會對應到個別 blob 的一項變更。 記錄會使用 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 格式規格進行序列化並寫入至檔案。 您可以使用 Avro 檔案格式規格來讀取記錄。 有數個程式庫可以用來處理該格式的檔案。

變更摘要檔案會以 `$blobchangefeed/log/` [附加 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)的形式儲存在虛擬目錄中。 每個路徑下的第一個變更摘要檔案將會有 `00000` 檔案名 (例如 `00000.avro`) 。 新增至該路徑的每個後續記錄檔的名稱將遞增 1 (例如： `00001.avro`) 。

下列事件種類會在變更摘要記錄中捕捉：
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

以下是變更摘要檔案中轉換為 Json 的變更事件記錄範例。

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

如需每個屬性的說明，請參閱 [適用于 Blob 儲存體的 Azure 事件方格事件架構](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)。 BlobPropertiesUpdated 和 BlobSnapshotCreated 事件目前僅供變更摘要，Blob 儲存體事件尚未支援。

> [!NOTE]
> 區段的變更摘要檔案不會在建立區段之後立即顯示。 延遲的長度是在變更摘要的正常間隔時間內，也就是變更摘要的幾分鐘內。

<a id="specifications"></a>

## <a name="specifications"></a>規格

- 變更事件記錄只會附加至變更摘要。 附加這些記錄之後，這些記錄是不可變的，且記錄位置是穩定的。 用戶端應用程式可以在變更摘要的讀取位置上維護自己的檢查點。

- 變更事件記錄會在變更的幾分鐘內附加。 用戶端應用程式可以選擇在附加記錄以進行串流存取，或在任何其他時間進行大量使用記錄。

- 變更事件記錄依 **每個 blob**的修改順序排序。 Azure Blob 儲存體中未定義跨 blob 的變更順序。 前一個區段中的所有變更都是在後續區段的任何變更之前。

- 變更事件記錄會使用 [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) 格式規格序列化到記錄檔中。

- 變更的事件記錄，其中的 `eventType` 值為 `Control` 內部系統記錄，而且不會反映您帳戶中物件的變更。 您可以放心地忽略這些記錄。

- 屬性包中的值 `storageDiagnonstics` 僅供內部使用，不適用於您的應用程式。 您的應用程式不應對該資料具有契約相依性。 您可以放心地忽略這些屬性。

- 區段表示的時間 **大約** 是15分鐘的界限。 因此，若要確保在指定的時間內使用所有記錄，請取用連續和下一個小時的區段。

- `chunkFilePaths`由於記錄資料流程的內部資料分割可管理發佈輸送量，因此每個區段可能會有不同的數目。 每個記錄檔中的記錄檔 `chunkFilePath` 都可保證包含互斥的 blob，而且可以平行取用和處理，而不會在反復專案期間違反每個 blob 的修改順序。

- 區段開始 `Publishing` 狀態。 將記錄附加到區段之後，將會是 `Finalized` 。 在檔案中的屬性日期之後的任何區段中，記錄檔 `LastConsumable` `$blobchangefeed/meta/Segments.json` 不應由您的應用程式使用。 以下是檔案 `LastConsumable` 中的屬性範例 `$blobchangefeed/meta/Segments.json` ：

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

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>條件和已知問題

本節說明目前版本之變更摘要的已知問題和條件。 

- 變更摘要中的任何單一變更事件記錄可能會出現一次以上。
- 您還無法藉由設定以時間為基礎的保留原則，來管理變更摘要記錄檔的存留期，而無法刪除這些 blob。
- 記錄檔的 `url` 屬性目前一律是空的。
- 檔案 `LastConsumable` segments.js的屬性不會列出變更摘要所完成的第一個區段。 只有在第一個區段完成後，才會發生此問題。 第一個小時之後的所有後續區段都會正確地在 `LastConsumable` 屬性中捕捉。
- 當您呼叫 ListContainers API 時，您目前無法看到 **$blobchangefeed** 容器，且容器未顯示在 Azure 入口網站或儲存體總管上。 您可以直接在 $blobchangefeed 容器上呼叫 ListBlobs API 來查看內容。
- 先前已起始 [帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md) 的儲存體帳戶，可能會發生記錄檔未出現的問題。 任何未來的帳戶容錯移轉也可能會影響記錄檔。

## <a name="faq"></a>常見問題集

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>變更摘要和儲存體分析記錄之間有何差異？
分析記錄有所有讀取、寫入、列出和刪除作業的記錄，以及所有作業的成功和失敗要求。 分析記錄是最好的工作，而且不保證任何順序。

變更摘要是一種解決方案，可為您的帳戶提供成功突變或變更的交易式記錄，例如建立、修改和刪除 blob。 變更摘要可保證每個 blob 的成功變更順序記錄和顯示所有事件，因此您不需要篩選出大量讀取作業或失敗要求的雜訊。 變更摘要基本上是針對需要特定保證的應用程式開發而設計和優化。

### <a name="should-i-use-change-feed-or-storage-events"></a>我應該使用變更摘要或儲存體事件嗎？
您可以同時利用這兩項功能，因為變更摘要和 [Blob 儲存體事件](storage-blob-event-overview.md) 都提供相同的資訊給相同的傳遞可靠性保證，主要差異在於事件記錄的延遲、排序和儲存。 變更摘要會在變更的幾分鐘內將記錄發佈到記錄，也可保證每個 blob 的變更作業順序。 儲存體事件會即時推送，而且可能不會進行排序。 變更摘要事件會永久儲存在儲存體帳戶內，並以您自己定義的保留區作為唯讀穩定記錄，而事件處理常式會暫時取用儲存體事件，除非您明確儲存它們。 有了變更摘要，任何數目的應用程式都可以使用 blob Api 或 Sdk，自行使用記錄。 

## <a name="next-steps"></a>後續步驟

- 請參閱如何使用 .NET 用戶端應用程式讀取變更摘要的範例。 請參閱 [Azure Blob 儲存體中的處理變更摘要記錄](storage-blob-change-feed-how-to.md)。
- 瞭解如何即時回應事件。 查看 [對 Blob 儲存體事件的反應](storage-blob-event-overview.md)
- 深入瞭解所有要求的成功和失敗作業的詳細記錄資訊。 查看 [Azure 儲存體分析記錄](../common/storage-analytics-logging.md)
