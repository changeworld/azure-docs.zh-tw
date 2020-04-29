---
title: 建立建議工具
titleSuffix: Azure Cognitive Search
description: 藉由建立會叫用自動完成或 autosuggested 查詢詞彙的建議工具和表述要求，在 Azure 認知搜尋中啟用預先輸入的查詢動作。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 7eb2988628d60fa72c7d83b81a58a1e0fae5de33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770091"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>建立建議工具，以在查詢中啟用自動完成和建議的結果

在「Azure 認知搜尋」中，您可以透過新增至[搜尋索引](search-what-is-an-index.md)的**建議工具**結構來啟用「搜尋即輸入」。 建議工具支援兩種體驗： [*自動完成*]，這會完成整個詞彙查詢的部分輸入，以及邀請按一下以符合特定比對的*建議*。 自動完成會產生查詢。 建議會產生相符的檔。

下列螢幕擷取畫面是[以 c # 建立您的第一個應用程式](tutorial-csharp-type-ahead-and-suggestions.md)說明這兩者。 自動完成會預見可能的詞彙，並以 "in" 完成 "臺灣"。 建議是迷你搜尋結果，其中的欄位（例如飯店名稱）代表來自索引的相符旅館搜尋檔。 如需建議，您可以呈現任何提供描述性資訊的欄位。

![自動完成和建議查詢的視覺化比較](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自動完成和建議查詢的視覺化比較")

您可以單獨或一起使用這些功能。 若要在 Azure 認知搜尋中執行這些行為，有一個索引和查詢元件。 

+ 在索引中，將建議工具加入至索引。 您可以使用入口網站、 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)或[.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 本文的其餘部分著重于建立建議工具。

+ 在查詢要求中，呼叫下列其中一個[api](#how-to-use-a-suggester)。

針對字串欄位，會針對每個欄位啟用搜尋型別支援。 如果您想要有類似螢幕擷取畫面中所示的體驗，您可以在相同的搜尋解決方案內同時執行這兩個自動提示行為。 這兩個要求都會以特定索引的*檔*集合為目標，而在使用者至少提供了三個字元輸入字串之後，就會傳迴響應。

## <a name="what-is-a-suggester"></a>什麼是建議工具？

建議工具是一種內部資料結構，可透過儲存前置詞來比對部分查詢，以支援搜尋型別的行為。 如同已標記化的詞彙，前置詞會儲存在反向索引中，每個欄位都是建議工具 fields 集合中所指定的每一個欄位。

## <a name="define-a-suggester"></a>定義建議工具

若要建立建議工具，請將其中一個加入至[索引架構](https://docs.microsoft.com/rest/api/searchservice/create-index)，並[設定每個屬性](#property-reference)。 建立建議工具的最佳時機是當您同時定義將使用它的欄位時。

+ 僅使用字串欄位

+ 在欄位上使用預設的標準`"analyzer": null`Lucene 分析器（）或[語言分析器](index-add-language-analyzers.md)（例如`"analyzer": "en.Microsoft"`，）

### <a name="choose-fields"></a>選擇欄位

雖然建議工具有數個屬性，但它主要是您啟用搜尋型別體驗的字串欄位集合。 每個索引都有一個建議工具，因此建議工具清單必須包含所有提供建議和自動完成內容的欄位。

自動完成可從較大的欄位集區中受益，因為其他內容可能會有更多的詞彙完成。

另一方面，建議您在選擇欄位時，就會產生更好的結果。 請記住，建議是搜尋檔的 proxy，因此您會想要最能代表單一結果的欄位。 區分多個相符專案的名稱、標題或其他唯一欄位的效果最好。 如果欄位包含重複的值，則建議包含相同的結果，而且使用者不會知道要按一下哪一個。

為了滿足搜尋型別體驗，請新增自動完成所需的所有欄位，然後使用 **$select**、 **$top**、 **$filter**和**searchFields**來控制建議的結果。

### <a name="choose-analyzers"></a>選擇分析器

您選擇的分析器會決定如何標記欄位，並在後續加上前置詞。 例如，對於「區分內容」之類的連字號字串，使用語言分析器將會產生這些標記組合：「內容」、「敏感性」、「區分內容」。 當您使用標準 Lucene 分析器時，斷字元字串不存在。 

評估分析器時，請考慮使用[分析文字 API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) ，深入瞭解詞彙如何標記化，並在後續加上前置詞。 建立索引之後，您可以在字串上嘗試各種分析器來查看權杖輸出。

明確禁止使用[自訂分析器](index-add-custom-analyzers.md)或[預先定義分析器](index-add-custom-analyzers.md#predefined-analyzers-reference)（標準 Lucene 除外）的欄位，以避免結果不佳。

> [!NOTE]
> 如果您需要解決分析器條件約束，例如，如果您在某些查詢案例中需要關鍵字或 ngram 分析器，則應該針對相同的內容使用兩個不同的欄位。 這可讓其中一個欄位具有建議工具，而另一種則可以使用自訂分析器設定進行設定。

### <a name="when-to-create-a-suggester"></a>建立建議工具的時機

建立建議工具的最佳時機是當您也建立欄位定義本身時。

如果您嘗試使用既有的欄位來建立建議工具，API 將不會允許它。 在編制索引期間，當兩個或多個字元組合中的部分詞彙與整個詞彙一起標記時，會產生前置詞。 假設現有的欄位已標記為 token 化，如果您想要將其新增至建議工具，則必須重建索引。 如需詳細資訊，請參閱[如何重建 Azure 認知搜尋索引](search-howto-reindex.md)。

## <a name="create-using-rest"></a>使用 REST 建立

在 REST API 中，透過 [[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)] 或 [[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)] 來新增建議工具。 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>使用 .NET 建立

在 c # 中，定義[建議工具物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters`是集合，但只能接受一個專案。 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>屬性參考

|屬性      |描述      |
|--------------|-----------------|
|`name`        |建議工具的名稱。|
|`searchMode`  |用來搜尋候選片語的策略。 目前唯一支援的模式是`analyzingInfixMatching`，其目前符合詞彙開頭的。|
|`sourceFields`|建議之內容來源的一或多個欄位清單。 欄位的類型`Edm.String`必須是和`Collection(Edm.String)`。 如果在欄位上指定分析器，則[此清單](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)中必須是名稱為 analyzer （不是自訂分析器）。<p/> 最佳做法是只指定那些會讓自己成為預期和適當回應的欄位，不論它是搜尋列或下拉式清單中的完整字串。<p/>飯店名稱是很好的候選，因為它有精確度。 詳細資訊欄位（如描述和批註）太密集。 同樣地，重複的欄位（例如分類和標記）比較不有效率。 在範例中，我們仍會包含「類別」，以示範您可以包含多個欄位。 |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>使用建議工具

在查詢中使用建議工具。 建立建議工具之後，請呼叫下列其中一個 Api 來進行搜尋型別體驗：

+ [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

在搜尋應用程式中，用戶端程式代碼應該使用程式庫，例如[JQUERY UI 自動完成](https://jqueryui.com/autocomplete/)，以收集部分查詢並提供相符的結果。 如需這項工作的詳細資訊，請參閱[將自動完成或建議的結果新增至用戶端程式代碼](search-autocomplete-tutorial.md)。

下列 [自動完成] REST API 的呼叫中會說明 API 的使用方式。 這個範例有兩個優點。 首先，如同所有查詢，作業是針對索引的檔集合，而查詢則包含**搜尋**參數，在此案例中會提供部分查詢。 第二，您必須將**suggesterName**新增至要求。 如果索引中未定義建議工具，則對自動完成或建議的呼叫將會失敗。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>範例程式碼

+ 使用[c # 建立您的第一個應用程式（第3課-新增搜尋型別）](tutorial-csharp-type-ahead-and-suggestions.md)範例示範建議工具結構、建議的查詢、自動完成和多面向導覽。 此程式碼範例會在沙箱 Azure 認知搜尋服務上執行，並使用預先載入的飯店索引，因此您只需要按 F5 執行應用程式。 不需要訂用帳戶或登入。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)是較舊的範例，其中包含 c # 和 JAVA 程式碼。 它也會示範建議工具的結構、建議的查詢、自動完成和多面向導覽。 此程式碼範例會使用託管的[NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)範例資料。 

## <a name="next-steps"></a>後續步驟

建議您參閱下列文章，以深入瞭解要求的編寫方式。

> [!div class="nextstepaction"]
> [將自動完成和建議新增至用戶端程式代碼](search-autocomplete-tutorial.md) 
