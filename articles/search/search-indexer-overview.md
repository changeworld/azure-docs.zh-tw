---
title: 匯入期間用於編目資料的索引子
titleSuffix: Azure Cognitive Search
description: 爬網 Azure SQL database、Azure Cosmos DB 或 Azure 儲存體，以將可搜尋的資料解壓縮並填入 Azure 認知搜尋索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282987"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure 認知搜尋中的索引子

Azure 認知搜尋中的*索引子*是一種編目程式，它會從外部 Azure 資料來源中抽取可搜尋的資料和中繼資料，並根據索引和資料來源之間的欄位對欄位對應填入索引。 這種方法有時稱為「提取模型」，因為服務會提取中的資料，而您不需要撰寫任何將資料加入至索引的程式碼。

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

與索引子相關的所有作業（包括狀態或定義的 GET 要求）都需要系統[管理員 api 金鑰](search-security-api-keys.md)。 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>支援的資料來源

索引子會搜耙 Azure 上的資料存放區。

* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) （處於預覽階段）
* [Azure 表格儲存體](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure 虛擬機器上的 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Azure 上的 SQL 受控實例](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>基本組態步驟
索引子可以提供資料來源特有的功能。 在這方面，索引子或資料來源組態的某些層面會因索引子類型而所有不同。 不過，所有索引子都有共用的的基本組成和需求。 下文涵蓋所有的索引子的通用步驟。

### <a name="step-1-create-a-data-source"></a>步驟 1：建立資料來源
索引子會從*資料來源*物件取得資料來源連接。 資料來源定義會提供連接字串，以及可能的認證。 呼叫[建立資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API 或 [DataSource 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource)來建立資源。

資料來源和使用資料來源的索引子是各自獨立設定與管理，這表示多個索引子可使用同一個資料來源來一次載入多個索引。

### <a name="step-2-create-an-index"></a>步驟 2：建立索引
索引子會自動執行有關資料擷取的某些工作，但是通常不包括建立索引。 若要滿足必要條件，您必須擁有預先定義的索引，且欄位必須與外部資料來源中的欄位相符。 欄位必須依名稱和資料類型進行比對。 如需結構化索引的詳細資訊，請參閱[建立索引（Azure 認知搜尋 REST API）](https://docs.microsoft.com/rest/api/searchservice/Create-Index)或[索引類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)。 如需欄位關聯的說明，請參閱[Azure 認知搜尋索引子中的欄位](search-indexer-field-mappings.md)對應。

> [!Tip]
> 雖然索引子不能為您產生索引，但入口網站中的 [匯入資料]**** 精靈有所幫助。 在大部分情況下，此精靈可以從來源中的現有中繼資料推斷索引結構描述，並呈現您可以在精靈作用中時以內嵌方式編輯的初步索引結構描述。 一旦在服務上建立索引後，在入口網站中的進一步編輯大部分都受限於新增欄位。 請考慮使用精靈進行建立，但非修改索引。 如需實際操作學習，請逐步執行[入口網站逐步解說](search-get-started-portal.md)。

### <a name="step-3-create-and-schedule-the-indexer"></a>步驟 3：建立和排程索引子
索引子定義是一種結構，可將與資料內嵌相關的所有元素結合在一起。 必要的元素包括資料來源和索引。 選擇性元素包括排程和欄位對應。 只有當來源欄位和索引欄位清楚地對應時，欄位對應才是選擇性的。 如需結構化索引子的詳細資訊，請參閱[建立索引子（Azure 認知搜尋 REST API）](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)。

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>視需要執行索引子

雖然排程編制索引很常見，但也可以在需要時使用[Run 命令](https://docs.microsoft.com/rest/api/searchservice/run-indexer)叫用索引子：

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> 當執行 API 成功傳回時，索引子叫用已經排程，但實際處理不會同步發生。 

您可以在入口網站中或透過取得索引子狀態 API 來監視索引子狀態。 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>取得索引子狀態

您可以透過[取得索引子狀態命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)來抓取索引子的狀態和執行歷程記錄：


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

回應包含整體索引子的狀態、最後 (或進行中) 的索引子叫用，以及最新的索引子叫用歷程記錄。

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

執行歷程記錄包含多達 50 個最近完成的執行，以倒序的方式進行儲存 (因此最新的執行會排在回應中的第一位)。

## <a name="next-steps"></a>後續步驟
既然您已瞭解基本概念，下一個步驟是檢閱需求和每個資料來源類型特有的工作。

* [Azure SQL Database 或 Azure 虛擬機器中的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* [Azure 表格儲存體](search-howto-indexing-azure-tables.md)
* [使用 Azure 認知搜尋 Blob 索引子編制 CSV blob 的索引](search-howto-index-csv-blobs.md)
* [使用 Azure 認知搜尋 Blob 索引子編制索引 JSON blob](search-howto-index-json-blobs.md)
