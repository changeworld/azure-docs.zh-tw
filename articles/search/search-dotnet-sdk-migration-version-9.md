---
title: 升級至 Azure 搜尋服務 .NET SDK 第9版
titleSuffix: Azure Cognitive Search
description: 將程式碼遷移至舊版中的 Azure 搜尋服務 .NET SDK 版本9。 了解新功能與必要的程式碼變更。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72793010"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>升級至 Azure 搜尋服務 .NET SDK 第9版

如果您使用7.0 預覽版或更舊版本的[Azure 搜尋服務 .NET SDK](https://aka.ms/search-sdk)，本文將協助您將應用程式升級為使用第9版。

> [!NOTE]
> 如果您想要使用 8.0-preview 版本來評估尚未正式推出的功能，您也可以遵循本文中的指示，從舊版升級至 8.0-preview。

如需包括範例的 SDK 一般逐步解說，請參閱 [如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)。

第9版的 Azure 搜尋服務 .NET SDK 包含較舊版本的許多變更。 其中一些是重大變更，但它們只需要對您的程式碼進行相對較小的變更。 請參閱 [升級步驟](#UpgradeSteps) 以取得如何變更您的程式碼以使用新的 SDK 版本的指示。

> [!NOTE]
> 如果您使用 4.0-preview 版或更舊版本，您應該先升級至第5版，然後再升級至版本9。 如需相關指示，請參閱[升級至 Azure 搜尋服務 .NET SDK 第5版](search-dotnet-sdk-migration-version-5.md)。
>
> 您的「Azure 搜尋服務」執行個體支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>第9版的新功能
Azure 搜尋服務 .NET SDK 的第9版以 Azure 搜尋服務 REST API 的最新正式推出版本為目標，特別是2019-05-06。 這可讓您從 .NET 應用程式中使用 Azure 搜尋服務的新功能，包括：

* [AI 擴充](cognitive-search-concept-intro.md)是從影像、blob 和其他非結構化資料來源解壓縮文字的能力-豐富內容，使其在 Azure 搜尋服務索引中更容易搜尋。
* [複雜類型](search-howto-complex-data-types.md)的支援可讓您在 Azure 搜尋服務索引中建立幾乎任何嵌套 JSON 結構的模型。
* [[自動完成](search-autocomplete-tutorial.md)] 會提供**建議**API 的替代方法，以執行「搜尋型別」行為。 「自動完成」會「完成」使用者目前輸入的單字或片語。
* [JsonLines 剖析模式](search-howto-index-json-blobs.md)是 Azure Blob 索引的一部分，會針對每個以分行符號分隔的 JSON 實體建立一個搜尋檔。

### <a name="new-preview-features-in-version-80-preview"></a>版本 8.0-preview 中的新預覽功能
8.0 版-預覽 Azure 搜尋服務 .NET SDK 的目標 API 版本 2017-11-11-Preview。 此版本包含所有版本9的相同功能，加上：

* 服務端的[客戶管理加密金鑰](search-security-manage-encryption-keys.md)-待用加密是一項新的預覽功能。 除了由 Microsoft 所管理的內建待用加密之外，您還可以套用額外的加密層級，其中您是金鑰的唯一擁有者。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟
首先，更新 `Microsoft.Azure.Search` 的 NuGet 參考，方法是使用 NuGet 封裝管理員主控台，或是在 Visual Studio 中用滑鼠右鍵按一下您的專案參考並選取 [管理 NuGet 封裝]。

一旦 NuGet 下載新的封裝和其相依性，請重建您的專案。 根據您的程式碼結構情況，它可能會順利重新建置。 如果是這樣，代表您已準備就緒！

如果您的組建失敗，您將需要修正每個組建錯誤。 如需如何解決每個可能的組建錯誤的詳細資訊，請參閱第[9 版的重大變更](#ListOfChanges)。

您可能會看到與過時的方法或屬性相關的其他建置警告。 警告將會包含要使用哪些內容取代過時功能的指示。 例如，如果您的`DataSourceType.DocumentDb`應用程式使用屬性，您應該會收到一則警告，指出「這個成員已被取代。 請改用 CosmosDb。

在您修正所有建置錯誤或警告之後，就可以視需要對您的應用程式進行變更，以利用新的功能。 第[9 版的新](#WhatsNew)功能會詳述 SDK 的新功能。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>第9版的重大變更

除了重建您的應用程式以外，第9版中有幾項重大變更可能需要變更程式碼。

> [!NOTE]
> 以下的變更清單並不完整。 某些變更可能不會導致組建錯誤，但是在技術上會中斷，因為它們會破壞與舊版 Azure 搜尋服務 .NET SDK 元件相依的元件的二進位相容性。 以下不會列出這類變更。 升級至第9版時，請重建您的應用程式，以避免發生二進位相容性問題。

### <a name="immutable-properties"></a>不可變屬性

數個模型類別的公用屬性現在是不可變的。 如果您需要建立這些類別的自訂實例以進行測試，您可以使用新的參數化函式：

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>對欄位所做的變更

`Field`類別現在已經變更，它也可以代表複雜的欄位。

下列`bool`屬性現在是可為 null：

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

這是因為這些屬性現在必須是`null`複雜欄位的情況。 如果您有可讀取這些屬性的程式碼，就必須準備好處理`null`。 請注意，的`Field`所有其他屬性一律會保持為可為 null，而其中部分也會是`null`複雜欄位的情況，特別是下列各項：

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

`Field`已建立`internal`的無參數的函式。 從現在開始，每`Field`個都需要在結構時使用明確的名稱和資料類型。

### <a name="simplified-batch-and-results-types"></a>簡化的批次和結果類型

在 7.0-preview 版和更早版本中，封裝檔群組的各種類別結構化為平行類別階層：

  -  `DocumentSearchResult`和`DocumentSearchResult<T>`繼承自`DocumentSearchResultBase`
  -  `DocumentSuggestResult`和`DocumentSuggestResult<T>`繼承自`DocumentSuggestResultBase`
  -  `IndexAction`和`IndexAction<T>`繼承自`IndexActionBase`
  -  `IndexBatch`和`IndexBatch<T>`繼承自`IndexBatchBase`
  -  `SearchResult`和`SearchResult<T>`繼承自`SearchResultBase`
  -  `SuggestResult`和`SuggestResult<T>`繼承自`SuggestResultBase`

沒有泛型型別參數的衍生類型，是要在「動態類型」案例中使用，並假設使用`Document`類型。

從版本 8.0-preview 開始，基底類別和非泛型衍生類別全都已移除。 針對動態類型的案例，您可以使用`IndexBatch<Document>`、 `DocumentSearchResult<Document>`等等。
 
### <a name="removed-extensibleenum"></a>已移除 ExtensibleEnum

已移除 `ExtensibleEnum` 基底類別。 所有衍生自它的類別現在都是結構，例如`AnalyzerName`、 `DataType`和`DataSourceType` 。 其`Create`方法也已移除。 您可以直接移除對`Create`的呼叫，因為這些類型可從字串隱含轉換。 如果這會導致編譯器錯誤，您可以透過轉型為區分類型來明確叫用轉換運算子。 例如，您可以變更程式碼，如下所示：

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

變更為以下程式碼：

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

保留這些類型之選用值的屬性現在已明確地輸入為 nullable，因此它們會繼續是選擇性的。

### <a name="removed-facetresults-and-hithighlights"></a>已移除 FacetResults 和 HitHighlights

`FacetResults`和`HitHighlights`類別已移除。 Facet 結果現在會輸入為`IDictionary<string, IList<FacetResult>>` ，並將重點`IDictionary<string, IList<string>>`視為。 解決這項變更所引進之組建錯誤的快速方法，就`using`是在使用已移除類型的每個檔案頂端新增別名。 例如：

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>變更為 SynonymMap 

`SynonymMap` 建構函式不再具有 `SynonymMapFormat` 的參數 `enum`。 這個 enum 只有一個值，因此是多餘的。 如果您看到因此發生的建置錯誤，只需移除對 `SynonymMapFormat` 參數的參考即可。

### <a name="miscellaneous-model-class-changes"></a>其他模型類別變更

的`AutocompleteMode`屬性不再`AutocompleteParameters`是可為 null。 如果您有將此屬性指派給`null`的程式碼，您可以直接將它移除，而且屬性會自動初始化為預設值。

此函式的參數`IndexAction`順序已變更，現在已自動產生此函數。 我們建議您不要使用此函式，而是使用`IndexAction.Upload`factory `IndexAction.Merge`方法，等等。

### <a name="removed-preview-features"></a>已移除的預覽功能

如果您要從版本 8.0-preview 升級到第9版，請注意，已移除客戶管理金鑰的加密，因為這項功能仍處於預覽狀態。 具體而言， `EncryptionKey` `Index`和`SynonymMap`的屬性已移除。

如果您的應用程式對這項功能有困難的相依性，您將無法升級至第9版的 Azure 搜尋服務 .NET SDK。 您可以繼續使用版本 8.0-preview。 但請記住，**我們不建議在實際執行應用程式中使用預覽 SDK**。 預覽功能僅供評估，可能會變更。

> [!NOTE]
> 如果您使用版本 8.0-preview 來建立加密的索引或同義字對應，您仍然可以使用 sdk 的第9版來修改其定義，而不會對其加密狀態造成不良影響。 第9版的 SDK 不會將`encryptionKey`屬性傳送至 REST API，因此 REST API 不會變更資源的加密狀態。 

### <a name="behavioral-change-in-data-retrieval"></a>資料抓取中的行為變更

如果您使用的是「動態類型」 `Search`、 `Suggest`或`Get`傳回類型`Document`實例的 api，請注意它們現在會將空的`object[]` `string[]`JSON 陣列還原序列化為，而不是。

## <a name="conclusion"></a>結論
如需更多有關使用 Azure 搜尋服務 .NET SDK 的詳細資料，請參閱 [.NET 做法](search-howto-dotnet-sdk.md)。

歡迎您提供 SDK 的意見反應。 如果您遇到問題，歡迎詢問我們[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)的協助。 如果您發現錯誤，您可以在 [Azure .NET SDK GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/issues)中提出問題。 請務必在問題的標題前面加上「[Azure 搜尋服務]」前置詞。

感謝您使用 Azure 搜尋服務！
