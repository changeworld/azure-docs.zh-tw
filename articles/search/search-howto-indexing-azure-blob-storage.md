---
title: 搜尋 Azure Blob 儲存體內容
titleSuffix: Azure Cognitive Search
description: 瞭解如何在 Azure Blob 儲存體中編制檔索引，以及如何使用 Azure 認知搜尋從檔中將文字解壓縮。
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 9caa377ebcdff5b0ae379f1b0b8269dac5b8f499
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924090"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>如何使用 Azure 認知搜尋為 Azure Blob 儲存體中的檔編制索引

本文說明如何使用 Azure 認知搜尋來編制檔的索引 (例如 Pdf、Microsoft Office 檔，以及) 儲存在 Azure Blob 儲存體中的數種其他常見格式。 首先，它會說明安裝和設定 blob 索引子的基本概念。 然後，它會提供可能會發生之行為和案例的更深入探索。

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>支援的文件格式
blob 索引子可以從下列文件格式擷取文字：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>設定 blob 編製索引
您可以使用下列項目設定 Azure Blob 儲存體索引子︰

* [Azure 入口網站](https://ms.portal.azure.com)
* Azure 認知搜尋 [REST API](/rest/api/searchservice/Indexer-operations)
* Azure 認知搜尋 [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> 某些功能 (例如，欄位對應) 尚未在入口網站中提供使用，而必須以程式設計方式來使用。
>

我們會在此示範使用 REST API 的流程。

### <a name="step-1-create-a-data-source"></a>步驟 1:建立資料來源
資料來源能指定哪項資料要編製索引、存取資料需要哪些認證，以及哪些原則能有效識別資料變更 (新增、修改或刪除的資料列)。 資料來源可供同一個搜尋服務中的多個索引子使用。

若要為 Blob 編製索引，資料來源必須具有下列必要屬性︰

* **名稱**是搜尋服務中資料來源的唯一名稱。
* **type** 必須是 `azureblob`。
* **認證**可提供儲存體帳戶連接字串來做為 `credentials.connectionString` 參數。 請參閱下面的[如何指定認證](#Credentials)了解詳細資訊。
* **容器**會指定儲存體帳戶中的容器。 根據預設，容器內的所有 Blob 都可擷取。 如果您只想要在特定虛擬目錄為 Blob 編製索引，您可以使用選擇性的**查詢**參數指定該目錄。

若要建立資料來源：

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   
```

如需建立資料來源 API 的詳細資訊，請參閱 [建立資料來源](/rest/api/searchservice/create-data-source)。

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>如何指定認證 ####

您可以採取下列其中一種方式提供 blob 容器的認證︰

- **完整存取儲存體帳戶連接字串**： `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` 若要從 Azure 入口網站取得連接字串，您可以流覽至儲存體帳戶分頁 > 設定 > 適用于傳統儲存體帳戶的金鑰 () 或 > 儲存體帳戶 (的設定 Azure Resource Manager 存取金鑰) 。
- **儲存體帳戶共用存取簽章** (SAS) 連接字串：`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS 應該有容器和物件 (在此案例中為 Blob) 上的列出和讀取權限。
-  **容器共用存取**簽章： `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS 應該具有容器的清單和讀取權限。

如需儲存體共用存取簽章的詳細資訊，請參閱[使用共用存取簽章](../storage/common/storage-sas-overview.md)。

> [!NOTE]
> 如果您使用 SAS 認證，您必須使用更新的簽章定期更新資料來源認證以防止其到期。 如果 SAS 認證過期，索引子將會失敗並出現類似 `Credentials provided in the connection string are invalid or have expired.` 的錯誤訊息。  

### <a name="step-2-create-an-index"></a>步驟 2：建立索引
索引會指定文件、屬性和其他建構中可形塑搜尋體驗的欄位。

以下說明如何使用可搜尋的 `content` 欄位建立索引，以儲存從 blob 擷取的文字︰   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

如需建立索引的詳細資訊，請參閱[建立索引](/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>步驟 3：建立索引子
索引子會以目標搜尋索引連接資料來源，並提供排程來自動重新整理資料。

建立索引和資料來源之後，您就可以開始建立索引子︰

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。 若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性 - 如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](/rest/api/searchservice/create-indexer)。

如需有關定義索引子排程的詳細資訊，請參閱[如何排程 Azure 認知搜尋的索引子](search-howto-schedule-indexers.md)。

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Azure 認知搜尋如何編制 blob 的索引

取決於[組態](#PartsOfBlobToIndex)，blob 索引子只可以編製儲存體中繼資料的索引 (僅當您關注中繼資料且無須編製 blob 內容的索引時很有用)，儲存體和內容中繼資料，或中繼資料和文字內容。 根據預設，索引子會擷取中繼資料和內容。

> [!NOTE]
> 根據預設，結構化內容 (例如 JSON 或CSV) 的 Blob 會以單一區塊文字編製索引。 如果您想要以結構化方式編制 JSON 和 CSV blob 的索引，請參閱 [編制 json Blob 索引](search-howto-index-json-blobs.md) 和 [編制 csv blob](search-howto-index-csv-blobs.md) 的索引，以取得詳細資訊。
>
> 複合檔案或內嵌檔 (例如 ZIP 封存、包含附件之內嵌 Outlook 電子郵件的 Word 檔，或。具有附件) 的 MSG 檔案也會以單一檔的方式來編制索引。 例如，從的附件解壓縮的所有影像。訊息檔案將會在 normalized_images 欄位中傳回。

* 文件的文字內容會擷取至名為 `content` 的字串欄位。

> [!NOTE]
> Azure 認知搜尋會根據定價層限制要解壓縮的文字數目：適用于免費層的32000個字元、適用于 4000000 Basic 的64000、適用于 standard 的、標準 S2 的標準版、標準 S2 的8000000和標準 S3 的16000000。 在已截斷的文件中，索引子的狀態回應會包含警告。  

* 顯示在 blob 中的使用者指定中繼資料屬性 (如果有的話)，會逐字擷取。 請注意，這需要在索引中定義欄位，其名稱與 blob 的中繼資料索引鍵相同。 例如，如果您的 blob 具有具有值的中繼資料 `Sensitivity` `High` 索引鍵，則您應該在搜尋索引中定義一個名為的欄位， `Sensitivity` 然後它會填入值 `High` 。
* 標準 blob 中繼資料屬性會擷取到下列欄位：

  * **metadata\_storage\_name** (Edm.String) - blob 的檔案名稱。 例如，如果您有 blob /my-container/my-folder/subfolder/resume.pdf，這個欄位的值是 `resume.pdf`。
  * **metadata\_storage\_path** (Edm.String) - blob 的完整 URI，包括儲存體帳戶。 例如， `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String) - 內容類型，如同您用來上傳 blob 的程式碼所指定。 例如 `application/octet-stream`。
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) - 上次修改 blob 的時間戳記。 Azure 認知搜尋使用此時間戳記來識別已變更的 blob，以避免在初始編制索引之後重新建立所有專案的索引。
  * **metadata\_storage\_size** (Edm.Int64) - blob 大小 (位元組)。
  * **metadata\_storage\_content\_md5** (Edm.String) - blob 內容的 MD5 雜湊，如果有的話。
  * **中繼資料 \_ 儲存體 \_ Sas \_ 權杖** (Edm. 字串) -可供 [自訂技能](cognitive-search-custom-skill-interface.md) 用來取得 blob 存取權的暫時性 sas 權杖。 此權杖不應儲存供日後使用，因為它可能會過期。

* 每個文件格式特有的中繼資料屬性會擷取到[這裡](#ContentSpecificMetadata)列出的欄位。

您不需要在您的搜尋索引中針對上述所有屬性定義欄位 - 只擷取您的應用程式所需的屬性。

> [!NOTE]
> 通常，您現有的索引中的欄位名稱會與文件擷取期間所產生的欄位名稱不同。 您可以使用 **欄位** 對應，將 Azure 認知搜尋提供的屬性名稱對應到搜尋索引中的功能變數名稱。 您會在下面看到使用欄位對應的範例。
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>定義文件索引鍵和欄位對應
在 Azure 認知搜尋中，檔索引鍵會唯一識別檔。 每個搜尋索引必須確實具有一個 Edm.String 類型的索引鍵欄位。 要新增至索引的每個文件需要有索引鍵欄位 (實際上它是唯一必要的欄位)。  

您應該仔細考慮哪一個擷取的欄位應該對應至您的索引的索引鍵欄位。 候選對象是：

* **metadata\_storage\_name** - 這可能是方便的候選對象，但是請注意，1) 名稱可能不是唯一的，因為您在不同的資料夾中可能會有相同名稱的 blob，以及 2) 名稱可能包含在文件所索引鍵中無效的字元，例如連字號。 您可以藉由使用 `base64Encode` [欄位對應函式](search-indexer-field-mappings.md#base64EncodeFunction)，處理無效的字元。如果您這麼做，請記得在將它們傳入例如「查閱」的 API 呼叫時，對文件索引鍵進行編碼。 (例如，在 .NET 中您可以針對該目的使用 [UrlTokenEncode 方法](/dotnet/api/system.web.httpserverutility.urltokenencode?view=netframework-4.8))。
* **metadata\_storage\_path** - 使用完整路徑以確保唯一性，但是路徑明確包含 `/` 字元，該字元[在文件索引鍵中無效](/rest/api/searchservice/naming-rules)。  如上所述，您可以選擇使用 `base64Encode` [函式](search-indexer-field-mappings.md#base64EncodeFunction)來編碼索引鍵。
* 如果上述任何選項都不適合，您可以在 blob 中新增自訂中繼資料屬性。 但是，此選項需要您的 blob 上傳程序，將該中繼資料屬性新增至所有 blob。 因為索引鍵是必要屬性，所以沒有該屬性的所有 blob 都無法編製索引。

> [!IMPORTANT]
> 如果索引中的索引鍵欄位沒有明確的對應，Azure 認知搜尋會自動使用 `metadata_storage_path` 做為索引鍵，而以64為基礎的索引鍵值會 () 上述的第二個選項。
>
>

對於此範例，讓我們挑選 `metadata_storage_name` 欄位做為文件索引鍵。 同時假設您的索引具有名為 `key` 的索引鍵欄位和欄位 `fileSize`，來儲存文件大小。 若要連接所需的項目，在建立或更新您的索引子時，指定下列欄位對應：

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

若要將所有項目整合在一起，以下是您新增欄位對應和針對現有索引子啟用 base-64 索引鍵編碼的方式：

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }
```

> [!NOTE]
> 若要深入了解欄位對應，請參閱[這篇文章](search-indexer-field-mappings.md)。
>
>

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>如果您需要編碼欄位以將它當作索引鍵使用，但您也想要搜尋它呢？

有時候您需要使用編碼的欄位編碼版本（例如 metadata_storage_path）作為索引鍵，但您也需要將該欄位設為可搜尋 (，而不需要編碼) 。 為了解決這個問題，您可以將它對應到兩個欄位;用來作為索引鍵的金鑰，以及將用於搜尋用途的另一個金鑰。 在下列範例中，索引 *鍵* 欄位包含編碼的路徑，而 *path* 欄位未編碼，將用作索引中可搜尋的欄位。

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>控制要編製哪些 blob 的索引
您可以控制要編製哪些 blob 的索引，以及哪些要略過。

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>只將具有特定副檔名的 Blob 編製成索引
您可以使用 `indexedFileNameExtensions` 索引子組態參數，只將具有指定副檔名的 Blob 編製成索引。 值是包含副檔名 (有前置句點) 逗號分隔清單的字串。 例如，若只要將 .PDF 和 .DOCX Blob 編製成索引，請執行這項操作︰

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-with-specific-file-extensions"></a>排除具有特定副檔名的 Blob
您可以使用 `excludedFileNameExtensions` 組態參數，在編製索引時排除具有特定副檔名的 Blob。 值是包含副檔名 (有前置句點) 逗號分隔清單的字串。 例如，若要將除 .PNG 和 .JPEG 副檔名以外的所有 Blob 都編製成索引，請執行下列動作︰

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

如果同時有 `indexedFileNameExtensions` 和 `excludedFileNameExtensions` 參數，Azure 認知搜尋首先會查看 `indexedFileNameExtensions` ，然後是 `excludedFileNameExtensions` 。 這表示，如果兩份清單中有相同的副檔名，就會排除在索引編製外。

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>控制要編製 blob 哪些部分的索引

您可以使用 `dataToExtract` 組態參數來控制要編製 blob 哪些部分的索引。 它可以採用下列值：

* `storageMetadata` - 指定只有[標準 blob 屬性和使用者指定的中繼資料](../storage/blobs/storage-blob-container-properties-metadata.md)會編製索引。
* `allMetadata` - 指定儲存體中繼資料和從 blob 內容擷取的[內容型別特定中繼資料](#ContentSpecificMetadata)會編製索引。
* `contentAndMetadata` - 指定所有中繼資料和從 blob 擷取的文字內容會編製索引。 這是預設值。

例如，若只要編製儲存中繼資料的索引，請使用︰

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>使用 blob 中繼資料來控制編製 blob 索引的方式

上述的組態參數適用於所有的 blob。 有時候，您可能想要控制編製*個別 blob* 索引的方式。 若要執行此動作，您可以新增下列 blob 中繼資料屬性和值︰

| 屬性名稱 | 屬性值 | 說明 |
| --- | --- | --- |
| AzureSearch_Skip |"true" |指示 blob 索引子以完全略過 blob。 不會嘗試擷取中繼資料或內容。 當特定 blob 一直失敗，並且中斷編製索引程序時，這非常有用。 |
| AzureSearch_SkipContent |"true" |這是相當於[上方](#PartsOfBlobToIndex)所描述之範圍設定為特定 blob 的 `"dataToExtract" : "allMetadata"` 設定。 |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>處理錯誤

根據預設，一旦遇到不受支援內容類型 (例如影像) 的 blob 時，blob 索引子就會停止。 您當然可以使用 `excludedFileNameExtensions` 參數來略過特定內容類型。 不過，您可能需要編製 blob 的索引，而不需要事先知道所有可能的內容類型。 若要在遇到不受支援的內容類型時繼續編製索引，請將 `failOnUnsupportedContentType` 組態參數設定為 `false`：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

某些 blob 的 Azure 認知搜尋無法判斷內容類型，或無法處理其他支援之內容類型的檔。 若要略過此失敗模式，請將 `failOnUnprocessableDocument` 組態參數設定為 false：

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure 認知搜尋限制已編制索引之 blob 的大小。 這些限制記載于 [Azure 認知搜尋中的服務限制](./search-limits-quotas-capacity.md)。 預設會將過大的 Blob 視為錯誤。 不過，如果您將 `indexStorageMetadataOnlyForOversizedDocuments` 組態參數設為 true，仍可以針對過大 Blob 的儲存體中繼資料編列索引： 

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

如果在處理期間發生任何錯誤，當剖析 blob 或是將文件新增至索引時，您還是可以繼續編製索引。 若要忽略特定錯誤數目，請將 `maxFailedItems` 和 `maxFailedItemsPerBatch` 組態參數設定為所需的值。 例如：

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

## <a name="incremental-indexing-and-deletion-detection"></a>增量編製索引和刪除偵測

當您設定 blob 索引子排程執行時，它只會針對變更的 blob (由 blob 的 `LastModified` 時間戳記決定) 來重新編制索引。

> [!NOTE]
> 您不需要指定變更偵測原則 – 會自動為您啟用增量編制索引。

若要支援刪除文件，請使用「虛刪除」方法。 如果您直接刪除 blob，對應的文件將不會在搜尋索引中移除。

有兩種方式可以實行虛刪除方法。 兩者都如下所述。

### <a name="native-blob-soft-delete-preview"></a>原生 Blob 虛刪除 (預覽)

> [!IMPORTANT]
> 原生 blob 虛刪除支援目前為預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 2020-06-30-Preview 版](./search-api-preview.md)提供這項功能。 目前沒有入口網站或 .NET SDK 支援。

> [!NOTE]
> 使用原生 blob 虛刪除原則時，索引中的檔索引鍵必須是 blob 屬性或 blob 中繼資料。

在此方法中，您將使用 Azure Blob 儲存體所提供的 [原生 blob 虛刪除](../storage/blobs/soft-delete-blob-overview.md) 功能。 如果您的儲存體帳戶已啟用原生 blob 虛刪除，則您的資料來源會設定原生虛刪除原則，而索引子會尋找已轉換為虛刪除狀態的 blob，索引子會從索引中移除該檔。 從 Azure Data Lake Storage Gen2 為 blob 編制索引時，不支援原生 blob 虛刪除原則。

使用下列步驟：
1. [針對 Azure Blob 儲存體啟用原生虛刪除](../storage/blobs/soft-delete-blob-overview.md)。 建議您將保留原則設定為比索引子間隔排程更高的值。 如此一來，如果執行索引子時發生問題，或如果您有大量的檔要編制索引，則索引子會有很多時間最後處理虛刪除的 blob。 如果 blob 處於虛刪除狀態，Azure 認知搜尋索引子只會刪除索引中的檔。
1. 在資料來源上設定原生 blob 虛刪除偵測原則。 範例如下所示。 因為這項功能處於預覽狀態，所以您必須使用預覽版 REST API。
1. 執行索引子，或將索引子設定為依排程執行。 當索引子執行並處理 blob 時，會從索引中移除檔。

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>重新索引刪除的 blob

如果您從 Azure Blob 儲存體中刪除已在儲存體帳戶上啟用原生虛刪除的 blob，blob 將會轉換為虛刪除狀態，讓您可以選擇在保留期限內取消刪除該 blob。 當 Azure 認知搜尋資料來源具有原生 blob 虛刪除原則，而且索引子處理虛刪除的 blob 時，它會從索引中移除該檔。 如果該 blob 稍後刪除，則索引子不一定會重新編制該 blob 的索引。 這是因為索引子會根據 blob 的時間戳記來決定要編制索引的 blob `LastModified` 。 刪除虛刪除的 blob 時，它的 `LastModified` 時間戳記不會更新，所以如果索引子已處理的 blob 的 `LastModified` 時間戳記比刪除的 blob 還新，它就不會重新索引刪除的 blob。 若要確定已取消刪除的 blob 重新建立索引，您將需要更新 blob 的 `LastModified` 時間戳記。 其中一個方法是重新儲存該 blob 的中繼資料。 您不需要變更中繼資料，但重新儲存中繼資料會更新 blob 的 `LastModified` 時間戳記，讓索引子知道它需要重新索引此 blob。

### <a name="soft-delete-using-custom-metadata"></a>使用自訂中繼資料的虛刪除

在此方法中，您將使用 blob 的中繼資料，來指出何時應從搜尋索引中移除檔。

使用下列步驟：

1. 將自訂中繼資料索引鍵/值組新增至 blob，以指出 Azure 認知搜尋它會以邏輯方式刪除。
1. 在資料來源上設定虛刪除資料行偵測原則。 範例如下所示。
1. 一旦索引子處理 blob，並從索引中刪除檔之後，您就可以刪除 Azure Blob 儲存體的 blob。

例如，如果 blob 有值為 `true` 的中繼資料屬性 `IsDeleted`，則下列原則會認為 blob 已刪除：

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

#### <a name="reindexing-undeleted-blobs"></a>重新索引刪除的 blob

如果您在資料來源上設定虛刪除資料行偵測原則，然後將自訂中繼資料新增至具有標記值的 blob，然後執行索引子，則索引子會從索引中移除該檔。 如果您想要將該檔重新編制索引，只要變更該 blob 的虛刪除中繼資料值，然後重新執行索引子即可。

## <a name="indexing-large-datasets"></a>編製索引大型資料集

編製 blob 的索引可能會是耗時的程序。 在您要編製數以百萬計的 blob 索引情況下，您可以分割資料並使用多個索引子以平行方式處理資料來加速編製索引。 下列是您可以如此設定的方式：

- 將資料分割成多個 blob 容器或虛擬資料夾
- 設定數個 Azure 認知搜尋資料來源，每個容器或資料夾一個。 若要指向 blob 資料夾，則使用 `query` 參數︰

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- 針對每個資料來源建立對應的索引子。 所有索引子可以指向相同的目標搜尋索引。  

- 服務中的單一搜尋單位一次只能執行一個索引子。 以上述方式建立多個索引子，只有在這些索引子都以平行的方式執行時才會有幫助。 若要平行執行多個索引子，請透過建立適當數目的磁碟分割和複本，來對搜尋服務進行相應放大。 例如，如果您的搜尋服務具有 6 個搜尋單位 (例如 2 個磁碟分割 x 3 個複本)，則 6 個索引子將可以同時執行，並使編製索引的輸送量提升六倍。 若要深入瞭解規模調整和容量規劃，請參閱 [Azure 認知搜尋中的調整查詢和編制索引工作負載的資源層級](search-capacity-planning.md)。

## <a name="indexing-documents-along-with-related-data"></a>為文件及相關資料編製索引

您可能會想在索引中「組合」來自多個來源的文件。 例如，您可能會想要將來自 Blob 的文字與儲存在 Cosmos DB 中的其他中繼資料合併。 您甚至可以搭配各種索引子使用推送編製索引 API，以建立來自多個部分的搜尋文件。 

若要達成此目的，所有索引子和其他元件都需要在文件索引鍵上達成協議。 如需本主題的其他詳細資料，請參閱為 [多個 Azure 資料來源編制索引](./tutorial-multiple-data-sources.md)。 如需詳細的逐步解說，請參閱這篇外部文章： [在 Azure 認知搜尋中將檔與其他資料合併](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)。

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>編制純文字的索引 

如果所有的 Blob 都包含相同編碼的純文字，您可以使用「文字剖析模式」**** 來大幅提升編制索引的效能。 若要使用文字剖析模式，請將 `parsingMode` 設定屬性設定為 `text`：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

根據預設，會假定使用 `UTF-8` 編碼。 若要指定其他編碼，請使用 `encoding` 設定屬性： 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>內容類型特定的中繼資料屬性
下表摘要說明每個檔案格式完成的處理，並描述 Azure 認知搜尋所解壓縮的中繼資料屬性。

| 文件格式/內容類型 | 內容類型特定的中繼資料屬性 | 處理詳細資料 |
| --- | --- | --- |
| HTML (text/html)  |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |移除 HTML 標記並且擷取文字 |
| PDF (應用程式/pdf)  |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |擷取文字，包括內嵌文件 (不含影像) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |擷取文字，包括內嵌文件 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |擷取文字，包括內嵌文件 |
| DOCM (application/vnd.ms-word.doc>ument macroenabled. 12)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |擷取文字，包括內嵌文件 |
| WORD XML (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet word2006ml)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |移除 XML 標記並且擷取文字 |
| WORD 2003 XML (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. ms-wordml)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |移除 XML 標記並且擷取文字 |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |擷取文字，包括內嵌文件 |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |擷取文字，包括內嵌文件 |
| XLSM (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. vnd.ms-excel. macroenabled 12)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |擷取文字，包括內嵌文件 |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |擷取文字，包括內嵌文件 |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |擷取文字，包括內嵌文件 |
| PPTM (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet ms-powerpoint macroenabled. 12)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |擷取文字，包括內嵌文件 |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |解壓縮文字，包括從附件解壓縮的文字。 `metadata_message_to_email``metadata_message_cc_email`和 `metadata_message_bcc_email` 是字串集合，其餘的欄位都是字串。|
| ODT (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet oasis。 text)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |擷取文字，包括內嵌文件 |
| ODS (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. oasis)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |擷取文字，包括內嵌文件 |
| ODP (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. oasis presentation)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |擷取文字，包括內嵌文件 |
| ZIP (application/zip) |`metadata_content_type` |從封存中的所有文件擷取文字 |
| GZ (application/gzip)  |`metadata_content_type` |從封存中的所有文件擷取文字 |
| EPUB (application/EPUB + zip)  |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |從封存中的所有文件擷取文字 |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |移除 XML 標記並且擷取文字 |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |擷取文字<br/>注意：如果您需要從 JSON Blob 擷取多個文件欄位，請參閱[編製索引 JSON Blob](search-howto-index-json-blobs.md) 的詳細資訊 |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |擷取文字，包括附件 |
| RTF (應用程式/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 擷取文字|
| 純文字 (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 擷取文字|


## <a name="help-us-make-azure-cognitive-search-better"></a>協助我們改善 Azure 認知搜尋
如果您有功能要求或改進的想法，請在我們的 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)與我們連絡。