---
title: 升級到 Azure 搜索 .NET SDK 版本 9
titleSuffix: Azure Cognitive Search
description: 將代碼從舊版本遷移到 Azure 搜索 .NET SDK 版本 9。 了解新功能與必要的程式碼變更。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793010"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>升級到 Azure 搜索 .NET SDK 版本 9

如果您使用的是[Azure 搜索 .NET SDK](https://aka.ms/search-sdk)的版本 7.0 預覽版或更舊版本，本文將説明您將應用程式升級到使用版本 9。

> [!NOTE]
> 如果您希望使用版本 8.0 預覽來評估通常尚未可用的功能，還可以按照本文中的說明從以前的版本升級到 8.0 預覽。

如需包括範例的 SDK 一般逐步解說，請參閱 [如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)。

Azure 搜索 .NET SDK 的版本 9 包含許多來自早期版本的更改。 其中一些是突發更改，但它們只需要對代碼進行相對較小的更改。 請參閱 [升級步驟](#UpgradeSteps) 以取得如何變更您的程式碼以使用新的 SDK 版本的指示。

> [!NOTE]
> 如果您使用的是版本 4.0 預覽版或更版本的版本，則應首先升級到版本 5，然後升級到版本 9。 有關說明[，請參閱升級到 Azure 搜索 .NET SDK 版本 5。](search-dotnet-sdk-migration-version-5.md)
>
> 您的「Azure 搜尋服務」執行個體支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>版本 9 中的新增功能
Azure 搜索 .NET SDK 的版本 9 面向 Azure 搜索 REST API 的最新一般版本，特別是 2019-05-06。 這可讓您從 .NET 應用程式中使用 Azure 搜尋服務的新功能，包括：

* [AI 擴充](cognitive-search-concept-intro.md)是從圖像、Blob 和其他非結構化資料來源中提取文本的能力 - 豐富內容，使其在 Azure 搜索索引中更具可搜索性。
* 對[複雜類型](search-howto-complex-data-types.md)的支援允許您在 Azure 搜索索引中類比幾乎任何嵌套的 JSON 結構。
* [自動完成](search-autocomplete-tutorial.md)提供了建議**API**的替代方法，用於實現"按類型搜索"行為。 「自動完成」會「完成」使用者目前輸入的單字或片語。
* [JsonLines 解析模式](search-howto-index-json-blobs.md)是 Azure Blob 索引的一部分，它每個 JSON 實體創建一個搜索文檔，該文檔由一條新線分隔。

### <a name="new-preview-features-in-version-80-preview"></a>版本 8.0 預覽版中的新預覽功能
Azure 搜索 .NET SDK 的版本 8.0 預覽版面向 API 版本 2017-11-11 預覽。 此版本包括版本 9 的所有相同功能，以及：

* [客戶管理的加密金鑰](search-security-manage-encryption-keys.md)用於服務端靜態加密是一項新的預覽功能。 除了 Microsoft 管理的內置靜態加密外，您還可以應用額外的加密層，其中您是金鑰的唯一擁有者。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟
首先，更新 `Microsoft.Azure.Search` 的 NuGet 參考，方法是使用 NuGet 封裝管理員主控台，或是在 Visual Studio 中用滑鼠右鍵按一下您的專案參考並選取 [管理 NuGet 封裝]。

一旦 NuGet 下載新的封裝和其相依性，請重建您的專案。 根據您的程式碼結構情況，它可能會順利重新建置。 如果是這樣，代表您已準備就緒！

如果生成失敗，則需要修復每個建置錯誤。 有關如何解決每個潛在建置錯誤的詳細資訊，請參閱[版本 9 中的"中斷更改](#ListOfChanges)"。

您可能會看到與過時的方法或屬性相關的其他建置警告。 警告將會包含要使用哪些內容取代過時功能的指示。 例如，如果應用程式使用 該`DataSourceType.DocumentDb`屬性，則應收到一條警告，指出"此成員已棄用"。 改用 CosmosDb"。

在您修正所有建置錯誤或警告之後，就可以視需要對您的應用程式進行變更，以利用新的功能。 SDK 中的新功能詳見版本[9 中的新增功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>版本 9 中突發更改

版本 9 中有幾個重大更改，除了重建應用程式之外，還需要更改代碼。

> [!NOTE]
> 下面的更改清單並非詳盡無遺。 某些更改可能不會導致建置錯誤，但在技術上會中斷，因為它們與依賴于早期版本的 Azure Search .NET SDK 程式集的程式集中斷二進位相容性。 下面未列出此類更改。 請重新生成應用程式時升級到版本 9，以避免任何二進位相容性問題。

### <a name="immutable-properties"></a>不變屬性

多個模型類的公共屬性現在是不可改變的。 如果需要創建這些類的自訂實例進行測試，可以使用新的參數化建構函式：

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>對欄位的更改

類`Field`現在已更改，因為它也可以表示複雜的欄位。

以下`bool`屬性現在為空：

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

這是因為這些屬性現在必須`null`位於複雜欄位中。 如果您有讀取這些屬性的代碼，則必須準備處理`null`。 請注意，的`Field`所有其他屬性始終為且繼續為空，其中一些屬性也將`null`位於複雜欄位中， 具體如下：

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

已生成`Field``internal`的無參數建構函式。 從現在開始，在構造時`Field`，每個都需要一個顯式名稱和資料類型。

### <a name="simplified-batch-and-results-types"></a>簡化的批次和結果類型

在版本 7.0 預覽版和早期版本中，封裝文檔組的各種類被構造成並行類層次結構：

  -  `DocumentSearchResult`並`DocumentSearchResult<T>`繼承從`DocumentSearchResultBase`
  -  `DocumentSuggestResult`並`DocumentSuggestResult<T>`繼承從`DocumentSuggestResultBase`
  -  `IndexAction`並`IndexAction<T>`繼承從`IndexActionBase`
  -  `IndexBatch`並`IndexBatch<T>`繼承從`IndexBatchBase`
  -  `SearchResult`並`SearchResult<T>`繼承從`SearchResultBase`
  -  `SuggestResult`並`SuggestResult<T>`繼承從`SuggestResultBase`

沒有泛型型別參數的派生類型旨在用於"動態類型"方案，並假定類型`Document`使用。

從版本 8.0 預覽開始，基本類和非泛型派生類已全部刪除。 對於動態類型方案，可以使用`IndexBatch<Document>`、`DocumentSearchResult<Document>`等。
 
### <a name="removed-extensibleenum"></a>已刪除可擴展的 Enum

已移除 `ExtensibleEnum` 基底類別。 派生自它的所有類現在都是結構，例如`AnalyzerName`，`DataType`和`DataSourceType`。 他們`Create`的方法也被移除。 您可以刪除對的調用，`Create`因為這些類型的從字串中隱式轉換。 如果這導致編譯器錯誤，則可以通過強制轉換顯式調用轉換運算子來消除類型歧義。 例如，您可以更改如下所示的代碼：

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

持有這些類型可選值的屬性現在顯式鍵入為空，因此它們仍然是可選的。

### <a name="removed-facetresults-and-hithighlights"></a>已刪除的面結果和命中突出顯示

和`FacetResults``HitHighlights`類已被刪除。 分面結果現在鍵入為`IDictionary<string, IList<FacetResult>>`和 命中突出顯示為`IDictionary<string, IList<string>>`。 解決此更改引入的建置錯誤的一種快速方法是在使用已`using`刪除類型的每個檔的頂部添加別名。 例如：

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>更改為同義字圖 

`SynonymMap` 建構函式不再具有 `SynonymMapFormat` 的參數 `enum`。 這個 enum 只有一個值，因此是多餘的。 如果您看到因此發生的建置錯誤，只需移除對 `SynonymMapFormat` 參數的參考即可。

### <a name="miscellaneous-model-class-changes"></a>其他模型類更改

的屬性`AutocompleteMode``AutocompleteParameters`不再為空。 如果代碼將此屬性分配給`null`，則可以簡單地將其刪除，並且該屬性將自動初始化為預設值。

現在，此建構函式是自動生成的`IndexAction`，參數對建構函式的順序已更改。 我們建議使用工廠方法`IndexAction.Upload`，`IndexAction.Merge`等等，而不是使用建構函式。

### <a name="removed-preview-features"></a>已移除的預覽功能

如果要從版本 8.0 預覽升級到版本 9，請注意，由於此功能仍處於預覽狀態，因此已刪除使用客戶管理金鑰的加密。 具體來說，`EncryptionKey`已刪除`Index`和`SynonymMap`的屬性。

如果應用程式對此功能具有硬依賴性，您將無法升級到 Azure 搜索 .NET SDK 的版本 9。 您可以繼續使用版本 8.0 預覽。 但請記住，**我們不建議在實際執行應用程式中使用預覽 SDK**。 預覽功能僅供評估，可能會變更。

> [!NOTE]
> 如果使用 SDK 版本 8.0 預覽創建加密索引或同義字映射，您仍可以使用它們並使用 SDK 版本 9 修改其定義，而不會對其加密狀態產生不利影響。 SDK 版本 9 不會將`encryptionKey`屬性發送到 REST API，因此 REST API 不會更改資源的加密狀態。 

### <a name="behavioral-change-in-data-retrieval"></a>資料檢索中的行為變化

如果您使用的`Search`是返回類型的`Suggest``Get``Document`實例的"動態類型"或 API，請注意，它們現在將空 JSON 陣列反序列化，`object[]`而不是`string[]`。

## <a name="conclusion"></a>結論
如需更多有關使用 Azure 搜尋服務 .NET SDK 的詳細資料，請參閱 [.NET 做法](search-howto-dotnet-sdk.md)。

歡迎您提供 SDK 的意見反應。 如果您遇到問題，請隨時向我們尋求説明[，在堆疊溢位](https://stackoverflow.com/questions/tagged/azure-search)。 如果您發現錯誤，您可以在 [Azure .NET SDK GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/issues)中提出問題。 請務必在問題的標題前面加上「[Azure 搜尋服務]」前置詞。

感謝您使用 Azure 搜尋服務！
