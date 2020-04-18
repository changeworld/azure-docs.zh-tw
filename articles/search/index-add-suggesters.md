---
title: 建立建議工具
titleSuffix: Azure Cognitive Search
description: 通過創建建議器和制定調用自動完成或自動建議的查詢詞的請求,在 Azure 認知搜尋中啟用提前鍵入的查詢操作。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1e2a837acef976b6b872c2d4002ee49d662ad594
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641333"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>建立建議程式以在查詢中開啟自動完成與建議的結果

在 Azure 認知搜尋中,透過添加到[搜尋索引](search-what-is-an-index.md)**的建議器**建構啟用「按類型搜尋」。 建議者支援兩個體驗:*自動完成*,它完成術語或短語,以及傳回符合文件的簡短清單*的建議*。  

以下螢幕截圖在[C# 中創建第一個應用](tutorial-csharp-type-ahead-and-suggestions.md)說明了這兩個範例。 自動完成預期一個潛在的術語,用"in"完成"tw"。 建議是迷你搜尋結果,其中像酒店名稱這樣的欄位表示索引中的匹配酒店搜索文檔。 有關建議,可以顯示任何提供描述性資訊的欄位。

![自動完成與建議的查詢的視覺化比較](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自動完成與建議的查詢的視覺化比較")

您可以單獨或一起使用這些功能。 要在 Azure 認知搜索中實現這些行為,有一個索引和查詢元件。 

+ 在索引中,向索引添加建議器。 您可以使用門戶[、REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)或[.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 本文的其餘部分側重於創建建議器。

+ 在查詢請求中,調用[下面列出的 API](#how-to-use-a-suggester)之一。

基於字串欄位的按欄位啟用搜索即類型支援。 如果您希望體驗類似於螢幕截圖中所示的體驗,則可以在同一搜索解決方案中實現兩種排位前行行為。 這兩個請求都以特定索引*的文檔*集合為目標,並在使用者至少提供三個字元輸入字串後返回回應。

## <a name="what-is-a-suggester"></a>什麼是建議者?

建議程式是一種數據結構,通過存儲用於在部分查詢上匹配的首碼,支援按類型搜索行為。 與標記化術語類似,首碼儲存在倒置索引中,對於建議器欄位集合中指定的每個字段一個。

創建首碼時,建議器有自己的分析鏈,類似於用於全文搜索的分析鏈。 但是,與全文搜索中的分析不同,建議器只能在使用標準 Lucene 分析器(預設)或[語言分析器](index-add-language-analyzers.md)的欄位上運行。 使用[自定義分析器](index-add-custom-analyzers.md)或[預先定義分析儀](index-add-custom-analyzers.md#predefined-analyzers-reference)(標準 Lucene 除外)的欄位明確不允許防止不良結果。

> [!NOTE]
> 如果需要解決分析器約束,請使用兩個單獨的欄位來訪問同一內容。 這將允許其中一個字段具有建議程式,而另一個字段可以使用自定義分析器配置進行設置。

## <a name="define-a-suggester"></a>定義建議者

要建立建議程式,請向[索引架構](https://docs.microsoft.com/rest/api/searchservice/create-index)添加一個,並[設定每個屬性](#property-reference)。 在索引中,可以有一個建議器(具體來說,建議者集合中有一個建議器)。 創建建議器的最佳時間是定義將使用它的欄位。

### <a name="choose-fields"></a>選擇欄位

儘管建議者具有多個屬性,但它主要是啟用搜索即用式體驗的欄位的集合。 對於建議,請選擇最能表示單個結果的欄位。 區分多個匹配項的名稱、標題或其他唯一欄位最符合最佳功能。 如果欄位由重複值組成,則建議由相同的結果組成,使用者不知道要單擊哪個欄位。

確保每個欄位使用在索引期間執行詞法分析的分析器。 您可以使用預設標準 Lucene 分析`"analyzer": null`器 ( ) 或`"analyzer": "en.Microsoft"`[語言分析器](index-add-language-analyzers.md)(例如 。 

您選擇的分析器決定了欄位如何標記並隨後預定。 例如,對於連字元字串(如"上下文敏感"),使用語言分析器將導致這些令牌組合:「上下文」、"敏感"、"上下文敏感"。 如果使用標準 Lucene 分析器,則連字元字串將不存在。

> [!TIP]
> 請考慮使用[分析文本 API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)來深入瞭解術語如何標記並隨後預綴。 生成索引後,可以在字串上嘗試各種分析器以查看它發出的權杖。

### <a name="when-to-create-a-suggester"></a>何時建立建議器

建立建議器的最佳時間是創建欄位定義本身。

如果嘗試使用預先存在的欄位創建建議程式,API 將禁止它。 首碼在索引期間生成,當兩個或多個字元組合中的部分術語與整個術語一起標記時。 鑒於現有欄位已標記化,如果要將它們添加到建議器,則必須重新生成索引。 有關詳細資訊,請參閱[如何重建 Azure 認知搜尋索引](search-howto-reindex.md)。

## <a name="create-using-rest"></a>使用 REST 建立

在 REST API 中,透過[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)添加建議程式。 

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

在 C# 中,定義[建議物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters`是集合,但它只能獲取一個專案。 

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
|`searchMode`  |用來搜尋候選片語的策略。 當前支援的唯一模式是`analyzingInfixMatching`,當前與 術語開頭匹配。|
|`sourceFields`|建議之內容來源的一或多個欄位清單。 欄位必須為類型`Edm.String``Collection(Edm.String)`與 。 如果在現場指定了分析器,它必須是[此清單中](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)的命名分析器(而不是自定義分析器)。<p/> 最佳做法是僅指定那些適合預期和適當回應的欄位,無論是搜索欄中的已完成字串還是下拉清單中的已完成字串。<p/>酒店名稱是一個很好的候選者,因為它具有精度。 描述和註釋等詳細欄位過於密集。 同樣,重複欄位(如類別和標記)的效率較低。 在示例中,我們無論如何都會包含"類別",以證明您可以包含多個字段。 |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>使用建議器

查詢中使用建議器。 建立建議程式後,呼叫以下 API 之一搜尋即用式體驗:

+ [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [建議使用HttpMessagesasync方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [使用 HTTPMessagesasync 方法自動完成](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

在搜尋應用程式中,客戶端代碼應利用[jQuery UI 自動完成](https://jqueryui.com/autocomplete/)等庫來收集部分查詢並提供匹配項。 有關此工作的詳細資訊,請參閱[將自動完成或建議的結果新增到客戶端碼](search-autocomplete-tutorial.md)。

API 用法在以下對自動完成 REST API 的調用中進行了說明。 此示例有兩個要點。 首先,與所有查詢一樣,該操作針對索引的文檔集合,查詢包含**一個搜索**參數,在這種情況下,該參數提供部分查詢。 其次,您必須向請求加入**建議名稱**。 如果在索引中未定義建議程式,則自動完成或建議的調用將失敗。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>範例程式碼

+ [在 C# 中建立第一個應用(第 3 課 - 添加搜尋即鍵入)](tutorial-csharp-type-ahead-and-suggestions.md)範例展示建議器建構、建議查詢、自動完成和分面導航。 此代碼示例在沙箱 Azure 認知搜尋服務上運行並使用預載入的 Hotels 索引,因此所有需要執行的只是按 F5 來運行應用程式。 無需訂閱或登錄。

+ [DotNetToToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)是包含 C# 和 Java 代碼的舊範例。 它還演示了建議器建構、建議查詢、自動完成和分面導航。 此代碼示例使用託管的[NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)範例資料。 

## <a name="next-steps"></a>後續步驟

我們建議使用以下示例來瞭解如何制定請求。

> [!div class="nextstepaction"]
> [新增客戶端碼加入自動完成與建議](search-autocomplete-tutorial.md) 
