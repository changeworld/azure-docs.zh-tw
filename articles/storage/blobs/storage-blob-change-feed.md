---
title: Azure Blob 儲存體 (預覽中變更摘要) |Microsoft Docs
description: 深入瞭解 Azure Blob 儲存體中的變更摘要記錄，以及如何使用它們。
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 09a97897ca7e3984c7003c1dbbca65cddaec1ee6
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88055412"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Azure Blob 儲存體中的變更摘要支援 (預覽)

變更摘要的目的是要為儲存體帳戶中 blob 和 blob 中繼資料所發生的所有變更提供交易記錄。 變更摘要會提供這些變更的已**排序**、**保證**、**持久**、**不可變**、**唯讀**記錄。 用戶端應用程式可以隨時在串流處理或批次模式中讀取這些記錄。 變更摘要可讓您建立有效率且可調整的解決方案，以低成本處理 Blob 儲存體帳戶中發生的變更事件。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

變更摘要會以[blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)的形式儲存在儲存體帳戶的特殊容器中，以標準[blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)成本為限。 您可以根據您的需求來控制這些檔案的保留期限 (查看目前版本) 的[條件](#conditions)。 變更事件會以[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式規格的記錄形式附加至變更摘要，這是精簡、快速的二進位格式，可使用內嵌架構提供豐富的資料結構。 此格式廣泛運用在 Hadoop 生態系統、串流分析和 Azure Data Factory。

您可以非同步或完整地處理這些記錄。 任何數目的用戶端應用程式都可以依自己的步調，以平行方式獨立讀取變更摘要。 [Apache](https://drill.apache.org/docs/querying-avro-files/)切入或[Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html)之類的分析應用程式可以直接取用記錄檔（例如 Avro 檔案），讓您以低成本的方式處理這些檔案，並具有高頻寬，而不需要撰寫自訂應用程式。

變更摘要支援非常適用于根據已變更物件來處理資料的案例。 例如，應用程式可以：

  - 更新次要索引、與快取、搜尋引擎或任何其他內容管理案例進行同步處理。
  
  - 根據物件發生的變更，以資料流程方式或批次模式，將商務分析見解和計量解壓縮。
  
  - 針對安全性、合規性或企業資料管理的智慧，儲存、審查和分析物件的變更。

  - 建立解決方案來備份、鏡像或複寫您帳戶中的物件狀態，以進行嚴重損壞的管理或合規性。

  - 建立連接的應用程式管線，以根據所建立或變更的物件來回應變更事件或排程執行。
  
變更摘要是區塊 blob 的[物件](object-replication-overview.md)複寫和[時間點還原](point-in-time-restore-overview.md)的必要條件功能。

> [!NOTE]
> 變更摘要為 blob 所發生的變更提供持久、已排序的記錄模型。 變更會以幾分鐘的時間，在變更摘要記錄檔中寫入並提供使用。 如果您的應用程式必須更快速地回應事件，請考慮改為使用[Blob 儲存體事件](storage-blob-event-overview.md)。 [Blob 儲存體事件](storage-blob-event-overview.md)會提供即時的一次性事件，讓您的 Azure Functions 或應用程式能夠快速回應 Blob 所發生的變更。 

## <a name="enable-and-disable-the-change-feed"></a>啟用和停用變更摘要

您必須啟用儲存體帳戶上的變更摘要，才能開始捕獲和記錄變更。 停用變更摘要以停止捕獲變更。 您可以使用入口網站或 PowerShell 上 Azure Resource Manager 範本來啟用及停用變更。

當您啟用變更摘要時，請記住以下幾點。

- 每個儲存體帳戶中的 blob 服務只有一個變更摘要，而且儲存在 **$blobchangefeed**容器中。

- 建立、更新和刪除變更只會在 blob 服務層級上捕捉。

- 變更摘要會針對帳戶上發生的所有可用事件，捕捉*所有*的變更。 用戶端應用程式可以視需要篩選掉事件種類。  (查看目前版本) 的[條件](#conditions)。

- 只有 GPv2 和 Blob 儲存體帳戶可以啟用變更摘要。 目前不支援 Premium BlockBlobStorage 帳戶和已啟用階層命名空間的帳戶。 不支援 GPv1 儲存體帳戶，但可在不停機的情況下升級至 GPv2，如需詳細資訊，請參閱[升級至 GPv2 儲存體帳戶](../common/storage-account-upgrade.md)。

> [!IMPORTANT]
> 變更摘要處於公開預覽狀態，並可在**美國中西部**、**美國西部 2**、**法國中部**、**法國南部**、**加拿大中部**和**加拿大東部**區域中取得。 請參閱本文的[條件](#conditions)一節。 若要註冊預覽，請參閱本文的[註冊您的訂](#register)用帳戶一節。 您必須先註冊您的訂用帳戶，才能在您的儲存體帳戶上啟用變更摘要。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站，在您的儲存體帳戶上啟用變更摘要：

1. 在 [Azure 入口網站中](https://portal.azure.com/)，選取您的儲存體帳戶。

2. 瀏覽至 [Blob 服務] 底下的 [資料保護] 選項。

3. 按一下 [ **Blob 變更**摘要] 底下的 [**已啟用**]。

4. 選擇 [**儲存**] 按鈕以確認您的**資料保護**設定。

    ![顯示資料保護設定的螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 啟用變更摘要：

1. 安裝最新的 PowershellGet。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. 關閉，然後重新開啟 PowerShell 主控台。

3. 安裝**Az** preview 模組。

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示進行驗證。

   ```powershell
   Connect-AzAccount
   ```

5. 為您的儲存體帳戶啟用變更摘要。

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[範本](#tab/template)
使用 Azure Resource Manager 範本，透過 Azure 入口網站在現有的儲存體帳戶上啟用變更摘要：

1. 在 [Azure 入口網站中，選擇 [**建立資源**]。

2. 在 **[搜尋 Marketplace**] 中，輸入**範本部署**，然後按**enter**。

3. 選擇 [**[部署自訂範本](https://portal.azure.com/#create/Microsoft.Template)**]，然後**在編輯器中選擇 [建立您自己的範本**]。

4. 在 [範本編輯器] 中，貼上下列 json。 使用您的儲存體帳戶名稱取代 `<accountName>` 預留位置。

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
    
5. 選擇 [**儲存**] 按鈕，指定帳戶的資源群組，然後選擇 [**購買**] 按鈕以部署範本並啟用變更摘要。

---

## <a name="consume-the-change-feed"></a>使用變更摘要

變更摘要會產生數個中繼資料和記錄檔。 這些檔案位於儲存體帳戶的 **$blobchangefeed**容器中。 

> [!NOTE]
> 在目前的版本中，Azure 儲存體總管或 Azure 入口網站中看不到 **$blobchangefeed**容器。 當您呼叫 ListContainers API 時，您目前無法看到 $blobchangefeed 容器，但您可以直接在容器上呼叫 ListBlobs API 來查看 blob。

您的用戶端應用程式可以使用變更摘要處理器 SDK 所提供的 blob 變更摘要處理器程式庫來取用變更摘要。 

請參閱[Azure Blob 儲存體中的處理變更摘要記錄](storage-blob-change-feed-how-to.md)。

## <a name="understand-change-feed-organization"></a>瞭解變更摘要組織

<a id="segment-index"></a>

### <a name="segments"></a>使用者分佈

變更摘要是組織成**每小時***區段*，但每隔幾分鐘就會附加和更新的變更記錄。 只有當該小時內發生 blob 變更事件時，才會建立這些區段。 這可讓您的用戶端應用程式取用在特定時間範圍內發生的變更，而不需要搜尋整個記錄檔。 若要深入瞭解，請參閱[規格](#specifications)。

變更摘要的可用小時區段會在指定該區段變更摘要檔案路徑的資訊清單檔中描述。 虛擬目錄的清單會 `$blobchangefeed/idx/segments/` 顯示依時間排序的這些區段。 區段的路徑描述區段所代表之每小時時間範圍的開始。 您可以使用該清單來篩選出您感利率的記錄區段。

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`當您啟用變更摘要時，會自動建立。 您可以放心地忽略此檔案。 這是一律是空的初始化檔。 

區段資訊清單檔案 (`meta.json`) 會在屬性中顯示該區段的變更摘要檔案路徑 `chunkFilePaths` 。 以下是區段資訊清單檔案的範例。

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
> `$blobchangefeed`只有在您已啟用帳戶的變更摘要功能之後，容器才會出現。 在您啟用變更摘要之後，您必須等候幾分鐘的時間，才能列出容器中的 blob。 

<a id="log-files"></a>

### <a name="change-event-records"></a>變更事件記錄

變更摘要檔案包含一系列的變更事件記錄。 每個變更事件記錄都會對應到個別 blob 的一項變更。 這些記錄會使用[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式規格進行序列化並寫入檔案。 您可以使用 Avro 檔案格式規格來讀取記錄。 有數個程式庫可用來處理該格式的檔案。

變更摘要檔案會以 `$blobchangefeed/log/` [附加 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)的形式儲存在虛擬目錄中。 每個路徑下的第一個變更摘要檔案會 `00000` 在檔案名 (，例如 `00000.avro`) 。 每個新增至該路徑的後續記錄檔名稱會遞增 1 (例如： `00001.avro`) 。

下列事件種類會在變更摘要記錄中捕捉：
- Microsoft.storage.blobcreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

以下是從變更摘要檔案轉換為 Json 的變更事件記錄範例。

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

如需每個屬性的說明，請參閱[適用于 Blob 儲存體的 Azure Event Grid 事件架構](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)。 BlobPropertiesUpdated 和 BlobSnapshotCreated 事件目前僅限於變更摘要，但 Blob 儲存體事件尚不支援。

> [!NOTE]
> 區段的變更摘要檔案不會立即在區段建立後出現。 延遲的長度是在變更摘要發佈延遲的標準間隔內，在變更摘要的幾分鐘內。

<a id="specifications"></a>

## <a name="specifications"></a>規格

- 變更事件記錄只會附加至變更摘要。 一旦附加這些記錄，它們就是不可變的，且記錄位置是穩定的。 用戶端應用程式可以在變更摘要的讀取位置維護自己的檢查點。

- 變更事件記錄會附加到變更幾分鐘的順序中。 用戶端應用程式可以選擇取用記錄，因為它們會針對串流存取而附加，或在任何其他時間大量地使用。

- 變更事件記錄是依**每個 blob**的修改順序排序。 在 Azure Blob 儲存體中，未定義跨 blob 的變更順序。 先前區段中的所有變更都會在後續區段中的任何變更之前。

- 變更事件記錄會使用[Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html)格式規格序列化成記錄檔。

- 變更事件記錄，其中的 `eventType` 值為 `Control` 內部系統記錄，而且不會反映您帳戶中的物件變更。 您可以放心地忽略那些記錄。

- 屬性包中的值 `storageDiagnonstics` 僅供內部使用，不適合應用程式使用。 您的應用程式不應對該資料具有合約相關性。 您可以放心地忽略這些屬性。

- 區段所表示的時間**大約**為15分鐘的界限。 因此，為了確保在指定的時間內，所有記錄的耗用量，會耗用連續的上一個和下一個小時區段。

- `chunkFilePaths`由於記錄資料流程的內部資料分割可管理發佈輸送量，每個區段的數目可能會不同。 每個中的記錄檔 `chunkFilePath` 保證會包含互斥的 blob，而且可以平行取用和處理，而不會違反反覆運算期間每個 blob 的修改順序。

- 區段會以狀態開始 `Publishing` 。 一旦將記錄附加至區段完成後，就會是 `Finalized` 。 `LastConsumable` `$blobchangefeed/meta/Segments.json` 您的應用程式不應使用在檔案中的屬性日期之後的任何區段中的記錄檔。 以下是檔案 `LastConsumable` 中的屬性範例 `$blobchangefeed/meta/Segments.json` ：

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

## <a name="register-your-subscription-preview"></a>註冊您的訂用帳戶 (預覽) 

因為變更摘要僅適用于公開預覽，所以您必須註冊您的訂用帳戶才能使用此功能。

### <a name="register-by-using-powershell"></a>使用 PowerShell 進行註冊

在 PowerShell 主控台中，執行下列命令：

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 進行註冊

在 Azure Cloud Shell 中，執行下列命令：

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a> (Preview) 的條件和已知問題

本節說明變更摘要目前公開預覽版中的已知問題和條件。 
- 針對預覽版本，您必須先[註冊您的訂](#register)用帳戶，才能在美國中西部、美國西部2、法國中部、法國南部、加拿大中部和加拿大東部區域中啟用儲存體帳戶的變更摘要。 
- 變更摘要只會捕捉建立、更新、刪除和複製作業。 也會捕捉 Blob 屬性和中繼資料變更。 不過，目前未捕捉到 [存取層] 屬性。 
- 變更任何單一變更的事件記錄，可能會在變更摘要中出現一次以上。
- 您還無法藉由在其上設定以時間為基礎的保留原則，而無法刪除 blob，來管理變更摘要記錄檔的存留期。
- 記錄檔的 `url` 屬性目前一律是空的。
- 檔案 `LastConsumable` 上 segments.js的屬性不會列出變更摘要完成的第一個區段。 只有在第一個區段完成後，才會發生此問題。 第一個小時之後的所有後續區段會正確地在 `LastConsumable` 屬性中捕捉。
- 當您呼叫 ListContainers API 時，您目前無法看到 **$blobchangefeed**容器，而且容器不會顯示在 Azure 入口網站或儲存體總管上。 您可以直接在 $blobchangefeed 容器上呼叫 ListBlobs API 來查看內容。
- 先前已起始[帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md)的儲存體帳戶，可能會有記錄檔未出現的問題。 任何未來的帳戶容錯移轉也可能會在預覽期間影響記錄檔。

## <a name="faq"></a>常見問題集

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>變更摘要和儲存體分析記錄之間的差異為何？
分析記錄具有所有讀取、寫入、列出和刪除作業的記錄，其中包含所有作業的成功和失敗要求。 分析記錄是最理想的做法，不保證任何順序。

「變更摘要」是一種解決方案，可提供成功變化的交易式記錄，或變更您的帳戶，例如建立、修改和刪除 blob。 變更摘要可保證所有事件都會以每個 blob 的成功變更順序來記錄和顯示，因此您不需要從大量讀取作業或失敗的要求中篩選出雜訊。 變更摘要基本上是針對需要特定保證的應用程式開發而設計和優化的。

### <a name="should-i-use-change-feed-or-storage-events"></a>我應該使用變更摘要或儲存體事件嗎？
您可以利用這兩種功能作為變更摘要和[Blob 儲存體事件](storage-blob-event-overview.md)，以相同的傳遞可靠性保證提供相同的資訊，主要差異在於事件記錄的延遲、排序和儲存。 變更摘要會在幾分鐘內將記錄發佈到記錄檔，同時也可保證每個 blob 的變更作業順序。 儲存體事件會即時推送，而且可能不會進行排序。 變更摘要事件會永久儲存在您的儲存體帳戶內，做為您自己定義的保留期的唯讀穩定記錄，而儲存體事件則是由事件處理常式使用的暫時性，除非您明確地儲存它們。 使用變更摘要時，任何數目的應用程式都可以使用 blob Api 或 Sdk，以自己的便利性取用記錄。 

## <a name="next-steps"></a>後續步驟

- 請參閱如何使用 .NET 用戶端應用程式讀取變更摘要的範例。 請參閱[Azure Blob 儲存體中的處理變更摘要記錄](storage-blob-change-feed-how-to.md)。
- 深入瞭解如何即時回應事件。 請參閱[回應 Blob 儲存體事件](storage-blob-event-overview.md)
- 深入瞭解所有要求的成功和失敗作業的詳細記錄資訊。 請參閱[Azure 儲存體分析記錄](../common/storage-analytics-logging.md)
