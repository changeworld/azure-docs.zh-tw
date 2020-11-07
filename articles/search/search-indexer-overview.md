---
title: 匯入期間用於編目資料的索引子
titleSuffix: Azure Cognitive Search
description: 編目 Azure SQL Database、SQL 受控執行個體、Azure Cosmos DB 或 Azure 儲存體，以解壓縮可搜尋的資料並填入 Azure 認知搜尋索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 305682812896bb74474b5065cfd56a071a73ed15
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358774"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure 認知搜尋中的索引子

Azure 認知搜尋中的 *索引子* 是一種編目程式，可從外部 Azure 資料來源中解壓縮可搜尋的資料和中繼資料，並根據索引和資料來源之間的欄位對欄位對應填入索引。 這種方法有時稱為「提取模型」，因為服務會在中提取資料，而不需要撰寫任何程式碼來將資料新增至索引。

索引子是以資料來源類型或平臺為基礎，其中包含適用于 Azure 上 SQL Server 的個別索引子、Cosmos DB、Azure 資料表儲存體和 Blob 儲存體。 Blob 儲存體索引子具有 blob 內容類型特定的其他屬性。

您可以使用索引子做為擷取資料的唯一手段，或結合使用多項技術 (包含使用索引子) 來僅載入索引中的某些欄位。

您可以視需要執行索引子，或依週期性的資料重新整理排程執行，頻率為每五分鐘一次。 更頻繁的更新需要推播模型，同時更新 Azure 認知搜尋和您的外部資料源中的資料。

## <a name="approaches-for-creating-and-managing-indexers"></a>建立與管理索引子的方法

您可以使用這些方法建立和管理索引子：

* [入口網站 > 匯入資料 Wizard](search-import-data-portal.md)
* [服務 REST API](/rest/api/searchservice/Indexer-operations)
* [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

一開始，會宣布新的索引子作為預覽功能。 預覽功能會在 API (REST 和 .NET) 中引進，然後在准許正式推出之後整合到入口網站中。 如果您正在評估新的索引子，您應該計劃撰寫程式碼。

## <a name="permissions"></a>權限

與索引子相關的所有作業（包括狀態或定義的 GET 要求）都需要系統 [管理員 api 金鑰](search-security-api-keys.md)。

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>支援的資料來源

索引子會搜耙 Azure 上的資料存放區。

* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* 預覽版中的[Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) () 
* [Azure 資料表儲存體](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL 受控執行個體](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
* [Azure 虛擬機器上的 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="indexer-stages"></a>索引子階段

在首次執行時，索引為空白時，索引子會讀取資料表或容器中提供的所有資料。 在後續的執行中，索引子通常可以偵測並只取出已變更的資料。 若為 blob 資料，變更偵測為自動。 針對其他資料來源（例如 Azure SQL 或 Cosmos DB），必須啟用變更偵測。

針對它內嵌的每個檔，索引子會從檔抓取執行多個步驟，從檔抓取到最終搜尋引擎「提交」以進行編制索引。 （選擇性）索引子也有助於駕駛技能集執行和輸出（假設技能集已定義）。

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="索引子階段" border="false":::

### <a name="stage-1-document-cracking"></a>階段1：檔破解

檔破解是開啟檔案和解壓縮內容的程式。 根據資料來源的類型而定，索引子會嘗試執行不同的作業，以解壓縮可能可編制索引的內容。  

範例：  

* 當檔是 [AZURE SQL 資料來源](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)中的記錄時，索引子將會提取記錄的每個欄位。
* 當檔是 [Azure Blob 儲存體資料來源](search-howto-indexing-azure-blob-storage.md)中的 PDF 檔案時，索引子會將檔案的文字、影像和中繼資料解壓縮。
* 當檔是 [Cosmos DB 資料來源](search-howto-index-cosmosdb.md)中的記錄時，索引子會從 Cosmos DB 檔中解壓縮欄位和子欄位。

### <a name="stage-2-field-mappings"></a>階段2：欄位對應 

索引子會從來源欄位取出文字，並將它傳送至索引或知識存放區中的目的地欄位。 當功能變數名稱和類型一致時，就會清除此路徑。 不過，您可能會想要在輸出中有不同的名稱或類型，在這種情況下，您需要告訴索引子如何對應欄位。 此步驟會在檔破解之後，但在轉換之前，在索引子從來源文件中讀取時進行。 當您定義 [欄位對應](search-indexer-field-mappings.md)時，[來源] 欄位的值會依原樣傳送至目的地欄位，而不進行修改。 欄位對應是選擇性的。

### <a name="stage-3-skillset-execution"></a>階段3：技能集執行

技能集執行是一個選擇性步驟，可叫用內建或自訂的 AI 處理。 您可能需要將它用於光學字元辨識 (OCR) 以影像分析形式提供，或者您可能需要語言轉譯。 無論何種轉換，技能集執行都是擴充發生的地方。 如果索引子是管線，您可以將 [技能集](cognitive-search-defining-skillset.md) 視為「管線內的管線」。 技能集有自己的一連串步驟，稱為技能。

### <a name="stage-4-output-field-mappings"></a>階段4：輸出欄位對應

技能集的輸出其實是一種稱為擴充檔的資訊樹狀結構。 輸出欄位對應可讓您選取要將此樹狀結構中的哪些部分對應至索引中的欄位。 瞭解如何 [定義輸出欄位](cognitive-search-output-field-mapping.md)對應。

就像欄位對應會將逐字值從來源關聯到目的地欄位一樣，輸出欄位對應也會告知索引子如何將擴充檔中已轉換的值與索引中的目的地欄位產生關聯。 不同于欄位對應（這是選擇性的），您一律需要為任何需要位於索引中的已轉換內容定義輸出欄位對應。

下圖顯示索引子階段的範例索引子 [調試](cognitive-search-debug-session.md) 程式標記法：檔破解、欄位對應、技能集執行和輸出欄位對應。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="範例 debug 會話" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>基本組態步驟

索引子可以提供資料來源特有的功能。 在這方面，索引子或資料來源組態的某些層面會因索引子類型而所有不同。 不過，所有索引子都有共用的的基本組成和需求。 下文涵蓋所有的索引子的通用步驟。

### <a name="step-1-create-a-data-source"></a>步驟 1:建立資料來源
索引子會從 *資料來源* 物件取得資料來源連接。 資料來源定義會提供連接字串，以及可能的認證。 呼叫 [Create Datasource](/rest/api/searchservice/create-data-source) REST API 或 [SearchIndexerDataSourceConnection 類別](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) 來建立資源。

資料來源和使用資料來源的索引子是各自獨立設定與管理，這表示多個索引子可使用同一個資料來源來一次載入多個索引。

### <a name="step-2-create-an-index"></a>步驟 2：建立索引
索引子會自動執行有關資料擷取的某些工作，但是通常不包括建立索引。 若要滿足必要條件，您必須擁有預先定義的索引，且欄位必須與外部資料來源中的欄位相符。 欄位必須符合名稱和資料類型。 如需結構化索引的詳細資訊，請參閱 [建立索引 (Azure 認知搜尋 REST API) ](/rest/api/searchservice/Create-Index) 或 [SearchIndex 類別](/dotnet/api/azure.search.documents.indexes.models.searchindex)。 如需關於欄位關聯的說明，請參閱 [Azure 認知搜尋索引子中的欄位](search-indexer-field-mappings.md)對應。

> [!Tip]
> 雖然索引子不能為您產生索引，但入口網站中的 [匯入資料] 精靈有所幫助。 在大部分情況下，此精靈可以從來源中的現有中繼資料推斷索引結構描述，並呈現您可以在精靈作用中時以內嵌方式編輯的初步索引結構描述。 一旦在服務上建立索引後，在入口網站中的進一步編輯大部分都受限於新增欄位。 請考慮使用精靈進行建立，但非修改索引。 如需實際操作學習，請逐步執行[入口網站逐步解說](search-get-started-portal.md)。

### <a name="step-3-create-and-schedule-the-indexer"></a>步驟 3：建立和排程索引子
索引子定義是一種結構，可將所有與資料內嵌相關的元素結合在一起。 必要的元素包括資料來源和索引。 選擇性元素包括排程和欄位對應。 只有當來源欄位和索引欄位清楚地對應時，欄位對應才是選擇性的。 如需結構化索引子的詳細資訊，請參閱 [建立索引子 (Azure 認知搜尋 REST API) ](/rest/api/searchservice/Create-Indexer)。

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>視需要執行索引子

雖然排程編制索引很常見，但您也可以使用 [Run 命令](/rest/api/searchservice/run-indexer)視需要叫用索引子：

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> 當執行 API 成功傳回時，索引子叫用已經排程，但實際處理不會同步發生。 

您可以在入口網站中或透過取得索引子狀態 API 來監視索引子狀態。 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>取得索引子狀態

您可以透過 [取得索引子狀態命令](/rest/api/searchservice/get-indexer-status)，取得索引子的狀態和執行歷程記錄：

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

回應包含整體索引子的狀態、最後 (或進行中) 的索引子叫用，以及最新的索引子叫用歷程記錄。

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

執行歷程記錄包含多達 50 個最近完成的執行，以倒序的方式進行儲存 (因此最新的執行會排在回應中的第一位)。

## <a name="next-steps"></a>後續步驟
既然您已瞭解基本概念，下一個步驟是檢閱需求和每個資料來源類型特有的工作。

* [Azure 虛擬機器上的 Azure SQL Database、SQL 受控執行個體或 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* [Azure 資料表儲存體](search-howto-indexing-azure-tables.md)
* [使用 Azure 認知搜尋 Blob 索引子編制索引 CSV blob](search-howto-index-csv-blobs.md)
* [使用 Azure 認知搜尋 Blob 索引子編制 JSON blob 的索引](search-howto-index-json-blobs.md)