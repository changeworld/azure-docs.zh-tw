---
title: 匯入期間用於編目資料的索引子
titleSuffix: Azure Cognitive Search
description: 爬網 Azure SQL Database、SQL 受控執行個體、Azure Cosmos DB 或 Azure 儲存體，以解壓縮可搜尋的資料並填入 Azure 認知搜尋索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f93df91f87f8119a503f2f7c452b61e3af5924f8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208765"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure 認知搜尋中的索引子

Azure 認知搜尋中的 *索引子* 是一種編目程式，它會從外部 Azure 資料來源中抽取可搜尋的資料和中繼資料，並根據索引和資料來源之間的欄位對欄位對應填入索引。 這種方法有時稱為「提取模型」，因為服務會提取中的資料，而您不需要撰寫任何將資料加入至索引的程式碼。

索引子是以資料來源類型或平臺為基礎，其中包含 Azure 上 SQL Server 的個別索引子、Cosmos DB、Azure 表格儲存體和 Blob 儲存體。 Blob 儲存體索引子具有 blob 內容類型特有的其他屬性。

您可以使用索引子做為擷取資料的唯一手段，或結合使用多項技術 (包含使用索引子) 來僅載入索引中的某些欄位。

您可以視需要執行索引子，或以週期性的資料重新整理排程，每隔五分鐘執行一次。 較頻繁的更新需要推送模型，同時在 Azure 認知搜尋和您的外部資料源中同時更新資料。

## <a name="approaches-for-creating-and-managing-indexers"></a>建立與管理索引子的方法

您可以使用這些方法建立和管理索引子：

* [入口網站 > 匯入資料嚮導](search-import-data-portal.md)
* [服務 REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

一開始，會宣布新的索引子作為預覽功能。 預覽功能會在 API (REST 和 .NET) 中引進，然後在准許正式推出之後整合到入口網站中。 如果您正在評估新的索引子，您應該計劃撰寫程式碼。

## <a name="permissions"></a>權限

與索引子相關的所有作業（包括狀態或定義的 GET 要求）都需要系統 [管理員 api 金鑰](search-security-api-keys.md)。

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>支援的資料來源

索引子會搜耙 Azure 上的資料存放區。

* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* 預覽中的[Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) () 
* [Azure 資料表儲存體](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database 和 SQL 受控執行個體](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure 虛擬機器上的 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL 受控執行個體](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="indexer-stages"></a>索引子階段

在初始執行時，當索引是空的時，索引子將會讀取資料表或容器中提供的所有資料。 在後續執行時，索引子通常可以偵測並只取出已變更的資料。 對於 blob 資料，變更偵測是自動的。 針對其他資料來源（例如 Azure SQL 或 Cosmos DB），必須啟用變更偵測。

針對它所內嵌的每個檔，索引子會執行或協調多個步驟，從檔抓取到最終搜尋引擎「遞交」以進行索引。 （選擇性）索引子也有助於駕駛技能集執行和輸出，前提是已定義技能集。

![索引子階段](./media/search-indexer-overview/indexer-stages.png "索引子階段")

### <a name="stage-1-document-cracking"></a>第1階段：檔破解

檔破解是開啟檔案和解壓縮內容的程式。 視資料來源的類型而定，索引子會嘗試執行不同的作業，以解壓縮可能可編制索引的內容。  

範例：  

* 當檔是 [AZURE SQL 資料來源](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)中的記錄時，索引子會將記錄的每個欄位解壓縮。
* 當檔是 [Azure Blob 儲存體資料來源](search-howto-indexing-azure-blob-storage.md)中的 PDF 檔案時，索引子將會解壓縮該檔案的文字、影像和中繼資料。
* 當檔是 [Cosmos DB 資料來源](search-howto-index-cosmosdb.md)中的記錄時，索引子將會從 Cosmos DB 檔中解壓縮欄位和子欄位。

### <a name="stage-2-field-mappings"></a>第2階段：欄位對應 

索引子會從來源欄位抽取文字，並將它傳送至索引或知識存放區中的目的地欄位。 當功能變數名稱和類型一致時，路徑會很清楚。 不過，您可能會想要輸出中有不同的名稱或類型，在這種情況下，您需要告訴索引子如何對應欄位。 此步驟會在檔破解後，但在轉換之前，于索引子從來源文件讀取時發生。 當您定義 [欄位對應](search-indexer-field-mappings.md)時，[來源] 欄位的值會以不修改的方式傳送至目的地欄位。 欄位對應是選擇性的。

### <a name="stage-3-skillset-execution"></a>第3階段：技能集執行

技能集執行是一個選擇性步驟，可叫用內建或自訂的 AI 處理。 對於光學字元辨識，您可能需要 (OCR) 以影像分析的形式，或者您可能需要語言轉譯。 無論轉換為何，技能集執行都會在擴充發生的位置進行。 如果索引子是管線，您可以將 [技能集](cognitive-search-defining-skillset.md) 視為管線中的「管線」。 技能集有自己的一系列的步驟，稱為技能。

### <a name="stage-4-output-field-mappings"></a>第4階段：輸出欄位對應

技能集的輸出其實是一種稱為擴充檔的資訊樹狀結構。 輸出欄位對應可讓您選取此樹狀結構的哪些部分，以對應至索引中的欄位。 瞭解如何 [定義輸出欄位](cognitive-search-output-field-mapping.md)對應。

就像欄位對應會將逐字值從來源關聯至目的地欄位，輸出欄位對應會告訴索引子如何將擴充的檔中轉換的值與索引中的目的地欄位產生關聯。 不同于欄位對應（視為選擇性），您一律需要為任何必須位於索引中的已轉換內容定義輸出欄位對應。

下一個影像顯示索引子階段的範例索引子 [debug 會話](cognitive-search-debug-session.md) 標記法：檔破解、欄位對應、技能集執行和輸出欄位對應。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="範例 debug 會話" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>基本組態步驟

索引子可以提供資料來源特有的功能。 在這方面，索引子或資料來源組態的某些層面會因索引子類型而所有不同。 不過，所有索引子都有共用的的基本組成和需求。 下文涵蓋所有的索引子的通用步驟。

### <a name="step-1-create-a-data-source"></a>步驟 1:建立資料來源
索引子會從 *資料來源* 物件取得資料來源連接。 資料來源定義會提供連接字串，以及可能的認證。 呼叫[建立資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API 或 [DataSource 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource)來建立資源。

資料來源和使用資料來源的索引子是各自獨立設定與管理，這表示多個索引子可使用同一個資料來源來一次載入多個索引。

### <a name="step-2-create-an-index"></a>步驟 2：建立索引
索引子會自動執行有關資料擷取的某些工作，但是通常不包括建立索引。 若要滿足必要條件，您必須擁有預先定義的索引，且欄位必須與外部資料來源中的欄位相符。 欄位必須依名稱和資料類型進行比對。 如需結構化索引的詳細資訊，請參閱 [ (Azure 認知搜尋建立索引 REST API) ](https://docs.microsoft.com/rest/api/searchservice/Create-Index) 或 [索引類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)。 如需欄位關聯的說明，請參閱 [Azure 認知搜尋索引子中的欄位](search-indexer-field-mappings.md)對應。

> [!Tip]
> 雖然索引子不能為您產生索引，但入口網站中的 [匯入資料]**** 精靈有所幫助。 在大部分情況下，此精靈可以從來源中的現有中繼資料推斷索引結構描述，並呈現您可以在精靈作用中時以內嵌方式編輯的初步索引結構描述。 一旦在服務上建立索引後，在入口網站中的進一步編輯大部分都受限於新增欄位。 請考慮使用精靈進行建立，但非修改索引。 如需實際操作學習，請逐步執行[入口網站逐步解說](search-get-started-portal.md)。

### <a name="step-3-create-and-schedule-the-indexer"></a>步驟 3：建立和排程索引子
索引子定義是一種結構，可將與資料內嵌相關的所有元素結合在一起。 必要的元素包括資料來源和索引。 選擇性元素包括排程和欄位對應。 只有當來源欄位和索引欄位清楚地對應時，欄位對應才是選擇性的。 如需結構化索引子的詳細資訊，請參閱 [建立索引子 (Azure 認知搜尋 REST API) ](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)。

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>視需要執行索引子

雖然排程編制索引很常見，但也可以在需要時使用 [Run 命令](https://docs.microsoft.com/rest/api/searchservice/run-indexer)叫用索引子：

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> 當執行 API 成功傳回時，索引子叫用已經排程，但實際處理不會同步發生。 

您可以在入口網站中或透過取得索引子狀態 API 來監視索引子狀態。 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>取得索引子狀態

您可以透過 [取得索引子狀態命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)來抓取索引子的狀態和執行歷程記錄：

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
* [使用 Azure 認知搜尋 Blob 索引子編制 CSV blob 的索引](search-howto-index-csv-blobs.md)
* [使用 Azure 認知搜尋 Blob 索引子編制索引 JSON blob](search-howto-index-json-blobs.md)
