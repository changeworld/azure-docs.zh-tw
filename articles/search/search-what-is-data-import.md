---
title: 在搜尋索引中匯入和資料內嵌
titleSuffix: Azure Cognitive Search
description: 從外部資料源填入資料，並將其上傳至 Azure 認知搜尋中的索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282753"
---
# <a name="data-import-overview---azure-cognitive-search"></a>資料匯入總覽-Azure 認知搜尋

在 Azure 認知搜尋中，查詢會透過載入並儲存在[搜尋索引](search-what-is-an-index.md)中的內容執行。 本文將探討兩種用來填入索引的基本方法：以程式設計方式將資料*推送*至索引，或在支援的資料來源中指向[Azure 認知搜尋索引子](search-indexer-overview.md)以*提取*資料。

無論使用哪一種方法，目標都是要將資料從外部資料源*載入*Azure 認知搜尋索引。 Azure 認知搜尋可讓您建立空的索引，但在您將資料推送或提取到其中時，它無法進行查詢。

## <a name="pushing-data-to-an-index"></a>將資料推送到索引
推送模型，用來以程式設計方式將資料傳送至 Azure 認知搜尋，是最具彈性的方法。 首先，它沒有資料來源類型的限制。 所有由 JSON 檔組成的資料集都可以推送至 Azure 認知搜尋索引，假設資料集中的每個檔都有對應至索引架構中所定義之欄位的欄位。 其次，它沒有執行頻率的限制。 您可以視需要經常將變更推送到索引。 若應用程式需要極低的延遲 (例如，如果您需要讓搜尋作業與動態庫存資料庫保持同步)，推送模式是您的唯一選擇。

這種方法比提取模型更有彈性，因為您可以個別或批次上傳文件 (每個批次最多 1000 個或 16 MB，視何者先到達)。 推送模型也可讓您將檔上傳至 Azure 認知搜尋，而不論您的資料位於何處。

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>如何將資料推送至 Azure 認知搜尋索引

您可以使用下列 API，將單一或多份文件載入索引中：

+ [新增、更新或刪除檔（REST API）](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) 或 [indexBatch class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

目前沒有工具可支援透過入口網站推送資料。

如需每個方法的簡介，請參閱[快速入門：使用 PowerShell 建立 Azure 認知搜尋索引](search-create-index-rest-api.md)或[c # 快速入門：使用 .Net SDK 建立 azure 認知搜尋索引](search-get-started-dotnet.md)。

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>索引動作：上傳、合併、mergeOrUpload、刪除

您可以針對每個檔控制索引動作的類型，指定要將檔完整上傳、與現有的檔內容合併，或刪除。

在 REST API 中，對 Azure 認知搜尋索引的端點 URL 發出具有 JSON 要求本文的 HTTP POST 要求。 「值」陣列中的每個 JSON 物件都包含檔的索引鍵，並指定是否要新增、更新或刪除檔內容。 如需程式碼範例，請參閱[載入檔](search-get-started-dotnet.md#load-documents)。

在 .NET SDK 中，將您的資料封裝到`IndexBatch`物件中。 會`IndexBatch`封裝`IndexAction`物件的集合，其中每一個都包含一個檔和一個屬性，告訴 Azure 認知搜尋要對該檔執行什麼動作。 如需程式碼範例，請參閱[c # 快速入門](search-get-started-dotnet.md)。


| @search.action | 描述 | 每個文件的必要欄位 | 備忘錄 |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |`upload` 動作類似「upsert」，如果是新文件，就會插入該文件，如果文件已經存在，就會更新/取代它。 |索引鍵以及其他任何您想要定義的欄位 |在更新/取代現有文件時，要求中未指定的欄位會將其欄位設定為 `null`。 即使先前已將欄位設定為非 null 值也是一樣。 |
| `merge` |使用指定的欄位更新現有文件。 如果文件不存在於索引中，合併就會失敗。 |索引鍵以及其他任何您想要定義的欄位 |您在合併中指定的任何欄位將取代文件中現有的欄位。 在 .NET SDK 中，這包括類型`DataType.Collection(DataType.String)`的欄位。 在 REST API 中，這包括類型`Collection(Edm.String)`的欄位。 例如，如果文件包含欄位 `tags` 且值為 `["budget"]`，而您使用值 `["economy", "pool"]` 針對 `tags` 執行合併，則 `tags` 欄位最後的值會是 `["economy", "pool"]`。 而不會是 `["budget", "economy", "pool"]`。 |
| `mergeOrUpload` |如果含有指定索引鍵的文件已經存在於索引中，則此動作的行為會類似 `merge`。 如果文件不存在，其行為會類似新文件的 `upload` 。 |索引鍵以及其他任何您想要定義的欄位 |- |
| `delete` |從索引中移除指定的文件。 |僅索引鍵 |您指定的所有欄位 (索引鍵欄位除外) 都將被忽略。 如果您想要從文件中移除個別欄位，請改用 `merge` ，而且只需明確地將該欄位設為 null。 |

## <a name="decide-which-indexing-action-to-use"></a>決定要使用的索引編製動作
使用 .NET SDK （上傳、合併、刪除和 mergeOrUpload）匯入資料。 依據您在以下動作中所做的選擇，每個文件內只需包含某些欄位：


### <a name="formulate-your-query"></a>編寫查詢
有兩種方式可以 [使用 REST API 搜尋索引](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 其中一種方式是發出 HTTP POST 要求，並將查詢參數定義在要求主體的 JSON 物件中。 另一種方式是發出 HTTP GET 要求，並將查詢參數定義在要求 URL 中。 在查詢參數的大小方面，POST 比 GET 擁有[更寬鬆的限制](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 因此，除非您的情況特殊導致使用 GET 會比較方便，否則建議您使用 POST。

不管是 POST 還是 GET，您都需要在要求 URL 中提供服務名稱**、索引名稱** 和適當的 API 版本** (本文件發行時的最新 API 版本是 `2019-05-06`)。 若為 GET，URL 結尾的「查詢字串」** 是您用來提供查詢參數的位置。 URL 的格式如下所示：

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

POST 的格式同上，但查詢字串參數中只有 API 版本。


## <a name="pulling-data-into-an-index"></a>將資料提取到索引中
提取模型會將支援的資料來源編目，並自動將資料上傳到您的索引。 在「Azure 認知搜尋」中，這項功能是透過*索引子*來執行，目前適用于這些平臺：

+ [Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
+ [資料表儲存體](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL database 和 Azure Vm 上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

索引子可將索引連接到資料來源 (通常是資料表、檢視或對等結構)，並將來源欄位對應至索引中的對等欄位。 在執行期間，資料列集會自動轉換為 JSON 並載入指定的索引。 所有索引子都支援排程，以便您指定資料重新整理的頻率。 如果資料來源支援索引子，則大部分的索引子都會提供變更追蹤。 除了辨識新文件，索引子還會追蹤現有文件的變更和刪除，讓您不必主動管理索引中的資料。 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>如何將資料提取到 Azure 認知搜尋索引中

索引子功能會在 [Azure 入口網站](search-import-data-portal.md)、[REST API](/rest/api/searchservice/Indexer-operations) 以及 [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions)中出現。 

使用入口網站的優點是，Azure 認知搜尋通常可以藉由讀取源資料集的中繼資料，為您產生預設的索引架構。 直到處理索引後，您才可以修改產生的索引，而後只允許不需要重新編製索引的結構描述編輯。 如果您想要進行的變更會直接影響結構描述，您必須重建索引。 

## <a name="verify-data-import-with-search-explorer"></a>透過搜尋總管確認資料匯入

對文件上傳執行初步檢查的快速方法是使用入口網站中的 [搜尋總管]****。 此總管可讓您查詢索引，而不需撰寫任何程式碼。 搜尋體驗是以預設設定為基礎，例如[簡單語法](/rest/api/searchservice/simple-query-syntax-in-azure-search)和預設[searchMode 查詢參數](/rest/api/searchservice/search-documents)。 結果會以 JSON 格式傳回，以便您檢查整份文件。

> [!TIP]
> 許多[Azure 認知搜尋程式碼範例](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)都包含內嵌或立即可用的資料集，並提供簡單的方法來開始著手。 入口網站也會提供範例索引子和資料來源 (由稱為 "realestate-us-sample" 的小型不動產資料集所組成)。 當您在範例資料來源上執行預先設定的索引子時，系統會建立索引並隨著文件載入，而後即可在 [搜尋總管] 中或由您撰寫的程式碼查詢索引。

## <a name="see-also"></a>請參閱

+ [索引子概觀](search-indexer-overview.md)
+ [入口網站逐步解說：建立、載入、查詢索引](search-get-started-portal.md)
