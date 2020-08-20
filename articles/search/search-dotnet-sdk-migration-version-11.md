---
title: 升級至 .NET SDK 11 版
titleSuffix: Azure Cognitive Search
description: 將程式碼遷移至舊版的 Azure 認知搜尋 .NET SDK 11 版。 了解新功能與必要的程式碼變更。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 83208ec792f40661861dd558ac2c1a1521c1d7fb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660964"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>升級至 Azure 認知搜尋 .NET SDK 11 版

如果您使用的是10.0 版或更舊版本的 [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)，本文將協助您升級至第11版。

第11版是經過完整重新設計的用戶端程式庫，由 Azure SDK 開發小組發行 (舊版是由 Azure 認知搜尋開發小組) 所產生。 此程式庫已經過重新設計，可與其他 Azure 用戶端程式庫更具一致性、相依于 [Azure Core](https://docs.microsoft.com/dotnet/api/azure.core) 和 [System.Text.Js](https://docs.microsoft.com/dotnet/api/system.text.json)，並針對一般工作執行熟悉的方法。

您在新版本中會注意到的一些主要差異包括：

+ 一個套件和程式庫，而非多個套件
+ 新的封裝名稱： `Azure.Search.Documents` 而不是 `Microsoft.Azure.Search` 。
+ 三個用戶端，而不是兩個： `SearchClient` 、 `SearchIndexClient` 、 `SearchIndexerClient`
+ 命名 Api 範圍和簡化部分工作的小型結構差異之間的差異

## <a name="package-and-library-consolidation"></a>封裝和程式庫合併

第11版會將多個套件和程式庫合併成一個。 遷移後，您將會有較少的管理程式庫。

+ [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [用戶端程式庫的 API 參考](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>用戶端差異

在適用的情況下，下表會對應兩個版本之間的用戶端程式庫。

| 作業範圍 | &nbsp;V10) 的搜尋 ( | Azure.Search.Documents &nbsp; (v11)  |
|---------------------|------------------------------|------------------------------|
| 用於查詢及填入索引的用戶端。 | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| 用於索引、分析器、同義字對應的用戶端 | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| 用於索引子、資料來源、技能集的用戶端 | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**新**) ](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` 存在於兩個版本中，但支援不同的專案。 在第10版中， `SearchIndexClient` 建立索引和其他物件。 在第11版中， `SearchIndexClient` 可以使用現有的索引。 為了避免在更新程式碼時產生混淆，請留意用戶端參考的更新順序。 遵循 [升級步驟](#UpgradeSteps) 中的順序應有助於減輕任何字串取代問題。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>命名和其他 API 差異

除了先前所述的用戶端差異 (也會在這裡省略) ，已重新命名多個其他 Api，而且在某些情況下已重新設計。 類別名稱差異摘要如下所示。 這份清單並不完整，但會依工作將 API 變更分組，這對特定程式碼區塊的修訂可能很有説明。 如需 API 更新的詳細清單，請參閱 GitHub 上的 [變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) 檔 `Azure.Search.Documents` 。

### <a name="authentication-and-encryption"></a>驗證和加密

| 第10版 | 第11版對等專案 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (已在 [預覽 SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) 中以正式運作的功能存在)  | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>索引、分析器、同義字對應

| 第10版 | 第11版對等專案 |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [欄位](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [>searchfield](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [分析器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (也 `AnalyzerName` `LexicalAnalyzerName`)  |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (也 `StandardTokenizerV2` `LuceneStandardTokenizerV2`)  |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (也 `TokenizerName` `LexicalTokenizerName`)  |
| [SynonymMap。格式](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | 無。 移除的參考 `Format` 。 |

欄位定義經過簡化： [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield)、 [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield)、 [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) 是新的 api，可用於建立欄位定義。

### <a name="indexers-datasources-skillsets"></a>索引子、資料來源、技能集

| 第10版 | 第11版對等專案 |
|------------|-----------------------|
| [索引編製程式](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [資料來源](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [技能](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [技能集](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>資料匯入

| 第10版 | 第11版對等專案 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>查詢定義和結果

| 第10版 | 第11版對等專案 |
|------------|-----------------------|
| [>documentsearchresult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [>.searchresult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) 或 [>.searchresults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1)，視結果為單一或多個檔而定。 |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [>searchparameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>第11版的功能

Azure 認知搜尋用戶端程式庫的每個版本都是以對應的 REST API 版本為目標。 REST API 會被視為服務的基礎，而個別的 Sdk 會包裝 REST API 的版本。 如果您想要更多有關特定物件或作業的背景，請以 .NET 開發人員的身分來查看 [REST API 檔](https://docs.microsoft.com/rest/api/searchservice/) 。

第11版以 [2020-06-30 搜尋服務](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)為目標。 由於第11版也是從頭開始建立的新用戶端程式庫，因此大部分的開發工作都著重于版本10的相等，但有些 REST API 功能支援仍在擱置中。

第11版完全支援下列物件和作業：

+ 索引建立與管理
+ 同義字對應的建立與管理
+ 除了地理空間篩選以外，所有的查詢類型和語法 () 
+ 為 Azure 資料來源編制索引的索引子物件和作業，包括資料來源和技能集

### <a name="pending-features"></a>暫止功能

第11版尚無法使用下列版本10功能。 如果您使用這些功能，請在不受支援的情況下，保留遷移。

+ 地理空間類型
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (雖然您可以使用 [此](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs) 因應措施) 。
+ [知識存放區](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟

下列步驟可讓您逐步完成第一組必要的工作，特別是關於用戶端參考，藉此開始進行程式碼遷移。

1. 以滑鼠右鍵按一下您的專案參考，然後選取 [管理 NuGet 套件 ...]，以安裝 [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/) 在 Visual Studio。

1. 將 using 指示詞取代為 Azure。搜尋如下：

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 修訂用戶端驗證碼。 在舊版中，您會使用用戶端物件上的屬性來設定 API 金鑰 (例如，SearchServiceClient) 的 [認證](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) 屬性。 在目前的版本中，使用 [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) 類別將金鑰以認證的形式傳遞，如此一來，您就可以在需要的情況下更新 API 金鑰，而不需要建立新的用戶端物件。

   用戶端屬性已簡化為只有 `Endpoint` 、 `ServiceName` 和 `IndexName` (適當的) 。 下列範例會使用系統 [Uri](https://docs.microsoft.com/dotnet/api/system.uri) 類別來提供端點和 [環境](https://docs.microsoft.com//dotnet/api/system.environment) 類別，以讀取金鑰值：

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. 加入索引子相關物件的新用戶端參考。 如果您使用索引子、資料來源或技能集，請將用戶端參考變更為 [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient)。 此用戶端是第11版中的新用戶端，而且沒有任何之前的版本。

1. 更新查詢和資料匯入的用戶端參考。 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)的實例應變更為[SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient)。 為了避免名稱混淆，請務必先攔截所有實例，再繼續進行下一個步驟。

1. 更新索引、索引子、同義字對應和分析器物件的用戶端參考。 [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)的實例應變更為[SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)。 

1. 請盡可能更新類別、方法和屬性，以使用新程式庫的 Api。 [ [命名差異](#naming-differences) ] 區段是要啟動的位置，但您也可以檢查 [變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)檔。

   如果您在尋找對等的 Api 時遇到問題，建議您記錄問題， [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) 讓我們可以改善檔或調查問題。

1. 重建方案。 修正任何組建錯誤或警告之後，您可以對應用程式進行其他變更，以利用 [新功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>第11版中的重大變更

由於程式庫和 Api 的清除變更，升級至第11版並不簡單，而且會導致您的程式碼不會再與第10版及更早版本相容的重大變更。 如需差異的完整評論，請參閱的 [變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) 檔 `Azure.Search.Documents` 。

從服務版本的角度來看，從10移至11會產生下列行為變更： 

+ [BM25 排名演算法](index-ranking-similarity.md) 會以較新的技術取代先前的排名演算法。 新服務會自動使用此演算法。 針對現有的服務，您必須將參數設定為使用新的演算法。

+ Null 值的[排序結果](search-query-odata-orderby.md)已在此版本中變更，如果排序是，則會先出現 null 值，如果 `asc` 排序是，則為最後一個 `desc` 。 如果您撰寫程式碼來處理 null 值的排序方式，您應該在不再需要時，檢查並可能移除該程式碼。

## <a name="next-steps"></a>後續步驟

+ [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub 範例](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Doc>ument API 參考](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)