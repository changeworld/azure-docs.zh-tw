---
title: 升級至 .NET SDK 版本11
titleSuffix: Azure Cognitive Search
description: 從舊版將程式碼遷移至 Azure 認知搜尋 .NET SDK 11 版。 了解新功能與必要的程式碼變更。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 644184f5bb51d049d890655ada5be5ebd4c11bf7
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908896"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>升級至 Azure 認知搜尋 .NET SDK 第11版

如果您使用的是10.0 或更舊版本的[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)，本文將協助您升級至第11版。

第11版是完全重新設計的用戶端程式庫，由 azure SDK 開發團隊發行 (舊版是由 Azure 認知搜尋開發小組) 所產生。 程式庫已經過重新設計，以與其他 Azure 用戶端程式庫更高的一致性，相依于[Azure 核心](https://docs.microsoft.com/dotnet/api/azure.core)和[System.Text.Js](https://docs.microsoft.com/dotnet/api/system.text.json)，並針對一般工作執行熟悉的方法。

您在新版本中會注意到的一些主要差異包括：

+ 一個套件和程式庫，而不是多個
+ 新的封裝名稱：， `Azure.Search.Documents` 而不是 `Microsoft.Azure.Search` 。
+ 三個用戶端，而不是兩個： `SearchClient` 、 `SearchIndexClient` 、`SearchIndexerClient`
+ 各種 Api 的命名差異，以及簡化某些工作的小型結構差異

## <a name="package-and-library-consolidation"></a>封裝和程式庫匯總

第11版會將多個套件和程式庫合併成一個。 遷移後，您將會有較少的程式庫可供管理。

+ [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [用戶端程式庫的 API 參考](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>用戶端差異

在適用的情況下，下清單格會對應兩個版本之間的用戶端程式庫。

| 作業的範圍 | &nbsp;V10)  (搜尋 | Azure.Search.Documents &nbsp; (v11)  |
|---------------------|------------------------------|------------------------------|
| 用於查詢和填入索引的用戶端。 | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| 用於索引、分析器、同義字對應的用戶端 | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| 用於索引子、資料來源、技能集的用戶端 | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**新**) ](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> 版本11會將第 10 `SearchIndexClient` 版 `SearchClient` 重新命名為，然後重複使用與索引、分析器和同義字地圖物件搭配運作的用戶端名稱。 更新用戶端參考時，請依照步驟中的步驟順序[進行升級](#UpgradeSteps)，以避免在搜尋和取代期間產生混淆。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>命名和其他 API 差異

除了先前所述的用戶端差異 (，因此在此省略) ，已重新命名多個其他 Api，在某些情況下會重新設計。 類別名稱的差異摘要如下。 這份清單並不完整，但它會依工作群組 API 變更，這有助於在特定程式碼區塊上進行修訂。 如需 API 更新的詳細清單，請參閱 GitHub 上的[變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)檔 `Azure.Search.Documents` 。

### <a name="authentication-and-encryption"></a>驗證和加密

| 第10版 | 版本11對等 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (以正式運作的功能形式存在[預覽 SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview)中)  | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>索引、分析器、同義字對應

| 第10版 | 版本11對等 |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [欄位](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [透過 searchfield](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [分析器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyze) (也 `AnalyzerName` `LexicalAnalyzerName`)  |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (也 `StandardTokenizerV2` `LuceneStandardTokenizerV2`)  |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (也 `TokenizerName` `LexicalTokenizerName`)  |
| [SynonymMap。格式](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | 無。 移除對 `Format` 的參考。 |

欄位定義經過簡化： [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield)、 [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield)、 [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield)是用來建立欄位定義的新 api。

### <a name="indexers-datasources-skillsets"></a>索引子、資料來源、技能集

| 第10版 | 版本11對等 |
|------------|-----------------------|
| [索引子](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [源](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [技能](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [技能集](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskillse) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>資料匯入

| 第10版 | 版本11對等 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>查詢定義和結果

| 第10版 | 版本11對等 |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1)或[SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1)，視結果是單一檔或多個而定。 |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>版本11的內容

Azure 認知搜尋用戶端程式庫的每個版本都是以對應的 REST API 版本為目標。 REST API 會被視為服務的基礎，而個別的 Sdk 會包裝版本的 REST API。 身為 .NET 開發人員，如果您想要在特定物件或作業上擁有更多背景，則查看[REST API 檔](https://docs.microsoft.com/rest/api/searchservice/)會很有説明。

版本11以[2020-06-30 搜尋服務](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)為目標。 因為版本11也是從頭開始建立的新用戶端程式庫，所以大部分的開發工作都著重于版本10的對等，並有一些 REST API 功能支援仍在擱置中。

版本11完全支援下列物件和作業：

+ 索引建立和管理
+ 同義字地圖的建立和管理
+ 除了地理空間篩選以外，所有的查詢類型和語法 () 
+ 為 Azure 資料來源編制索引的索引子物件和作業，包括資料來源和技能集

### <a name="pending-features"></a>暫止功能

版本11中尚未提供下列版本10功能。 如果您使用這些功能，請按住遷移，直到支援為止。

+ [地理空間類型](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.serialization.geojsonextensions)
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (，雖然您可以使用[這個](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)因應措施) 。
+ [知識存放區](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟

下列步驟可讓您開始進行程式碼遷移，方法是逐步執行第一組必要的工作，特別是關於用戶端參考。

1. 以滑鼠右鍵按一下您的專案參考，然後選取 [管理 NuGet 套件 ...]，以安裝[Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)在 Visual Studio。

1. 以下列內容取代 using 指示詞：

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 將[SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials)取代為[AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential)。

1. 更新索引子相關物件的用戶端參考。 如果您使用的是索引子、資料來源或技能集，請將用戶端參考變更為[SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient)。 此用戶端在第11版中是新的，而且沒有任何 antecedent。

1. 更新查詢和資料匯入的用戶端參考。 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)的實例應該變更為[SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient)。 為避免名稱混淆，請務必先攔截所有實例，再繼續進行下一個步驟。

1. 更新索引、索引子、同義字對應和分析器物件的用戶端參考。 [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)的實例應該變更為[SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchindexclient)。 

1. 盡可能更新類別、方法和屬性，以使用新程式庫的 Api。 [[命名差異](#naming-differences)] 區段是一個開始的地方，但您也可以查看[變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)檔。

   如果您在尋找對等的 Api 時遇到問題，建議您記錄問題， [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) 讓我們可以改善檔或調查問題。

1. 重建方案。 修正任何組建錯誤或警告之後，您可以對應用程式進行其他變更，以利用[新功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>版本11中的重大變更

針對程式庫和 Api 的最新變更，升級至第11版並不簡單，並在您的程式碼將不再與第10版和更早版本相容的情況下構成重大變更。 如需完整的差異檢查，請參閱的[變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)檔 `Azure.Search.Documents` 。

就服務版本而言，從10變更為11，會引進下列行為變更： 

+ [BM25 排名演算法](index-ranking-similarity.md)會以較新的技術取代先前的排名演算法。 新的服務會自動使用此演算法。 針對現有的服務，您必須將參數設定為使用新的演算法。

+ Null 值的[排序結果](search-query-odata-orderby.md)在此版本中已變更，如果排序是，則會先出現 null 值，如果排序是，則為 `asc` 最後一個 `desc` 。 如果您撰寫程式碼來處理 null 值的排序方式，您應該檢查並盡可能移除該程式碼（如果已不再需要）。

## <a name="next-steps"></a>後續步驟

+ [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub 範例](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document able API 參考](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)