---
title: 在搜尋框中新增自動完成與建議
titleSuffix: Azure Cognitive Search
description: 通過創建建議器並編寫使用已完成的字詞或短語自動完成搜尋框的請求,在 Azure 認知搜尋中啟用"即用即用即搜尋"查詢操作。 您還可以返回建議的匹配項。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758118"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>將客戶端應用程式加入自動完成與建議

按類型搜索是提高用戶啟動查詢的工作效率的常用技術。 在 Azure 認知搜尋中,此體驗通過*自動完成*支援 ,它根據部分輸入完成一個術語或短語(使用"microsoft"完成"微")。 另一種形式是*建議*:匹配文檔的簡短清單(傳回帶有 ID 的書籍標題,以便您可以連結到詳細資訊頁)。 自動完成和建議都基於索引中的匹配項。 該服務不會提供返回零結果的查詢。

在 Azure 認知搜尋中實現這些體驗,您需要:

+ 後端介面的*推薦器*。
+ 指定[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)或[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)API*查詢*。
+ 處理客戶端使用的搜尋類型互動的*UI 控制檔*。 為此,我們建議使用現有的 JavaScript 庫。

在 Azure 認知搜尋中,自動完成的查詢和建議的結果將從已向建議程式註冊的選定欄位從搜索索引中檢索。 建議器是索引的一部分,它指定哪些欄位將提供完成查詢、建議結果或同時執行兩者的內容。 創建和載入索引時,將在內部創建建議器資料結構,以存儲用於在部分查詢上匹配的前置碼。 對於建議,選擇唯一或至少不重複的合適欄位對於體驗至關重要。 有關詳細資訊,請參閱[建立建議器](index-add-suggesters.md)。

本文的其餘部分側重於查詢和客戶端代碼。 它使用 JAVAScript 和 C# 來說明要點。 REST API 範例用於簡明地呈現每個操作。 有關端到端代碼範例的連結,請參閱[後續步驟](#next-steps)。

## <a name="set-up-a-request"></a>設定要求

請求的元素包括一種即用即用搜索 API、部分查詢和建議程式。 以下文稿使用自動完成 REST API 作為範例,演示了請求的元件。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**建議名稱**為您提供了用於完成術語或建議的暗示感知欄位。 特別是對於建議,欄位清單應由在匹配結果之間提供明確選擇的建議組成。 在銷售電腦遊戲的網站上,該欄位可能是遊戲標題。

**搜索**參數提供部分查詢,其中字元通過 jQuery 自動完成控制件饋送到查詢請求。 在上面的示例中,"minecraf"是控制項可能傳入的靜態說明。

API 不對部分查詢施加最小長度要求;因此,API 不會對部分查詢施加最小長度要求。它可以是一個字元。 但是,jQuery 自動完成提供了最小長度。 通常至少有兩到三個字元。

匹配位於輸入字串中任意位置的術語的開頭。 給定"快速棕色狐狸",自動完成和建議將匹配部分版本的"","快速","棕色",或"狐狸",但不是部分固定術語,如"rown"或"牛"。 此外,每個匹配設置下游擴展的範圍。 部分查詢「快速br」將在「快速棕色」或「快速麵包」上匹配,但「棕色」或「麵包」本身與「快速」或「麵包」不匹配,除非「快速」在它們前面。

### <a name="apis-for-search-as-you-type"></a>以依類型搜尋的 API

依以下連結進行 REST 和 .NET SDK 參考頁:

+ [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [建議使用HttpMessagesasync方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [使用 HTTPMessagesasync 方法自動完成](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>建構回應

自動完成與建議的回應是您可能期望的模式:[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response)傳回術語清單,[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions#response)返回術語加上文件 ID,以便您可以獲取文檔(使用[查找文件](https://docs.microsoft.com/rest/api/searchservice/lookup-document)API 獲取詳細資訊頁的特定文檔)。

回應由請求上的參數形狀。 對於自動完成,設置[**自動完成模式**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes)以確定文本完成是否發生在一個或兩個術語上。 對於"建議",您選擇的欄位將確定回應的內容。

有關建議,應進一步優化回應,以避免重複或看似不相關的結果。 要控制結果,請在請求上包含更多參數。 以下參數適用於自動完成和建議,但可能更需要建議,尤其是當建議器包含多個字段時。

| 參數 | 使用量 |
|-----------|-------|
| **$select** | 如果建議器中有多個**源欄位**,請使用 **$select**選擇哪個欄`$select=GameTitle`位貢獻值 ( 。 |
| **searchFields** | 將查詢約束到特定欄位。 |
| **$filter** | 在結果集上套用符合`$filter=Category eq 'ActionAdventure'`條件 ( 。 |
| **$top** | 將結果限制為特定數位 ()。`$top=5`|

## <a name="add-user-interaction-code"></a>新增使用者互動碼

自動填充查詢術語或刪除匹配連結清單需要使用者互動代碼(通常是 JavaScript)來使用來自外部源的請求,例如針對 Azure 搜索認知索引的自動完成或建議查詢。

儘管可以本機編寫此代碼,但使用現有 JavaScript 庫中的函數要容易得多。 本文演示了兩個,一個用於建議,另一個用於自動完成。 

+ 「建議」範例中使用了[自動完成小部件(jQuery UI)。](https://jqueryui.com/autocomplete/) 您可以建立搜尋框,然後在使用"自動完成"小部件的 JavaScript 函數中引用它。 小部件上的屬性設定源(自動完成或建議函數)、執行操作前的輸入字元的最小長度以及定位。

+ [XDSoft 自動完成外掛程式](https://xdsoft.net/jqplugins/autocomplete/)是使用自動完成範例。

我們會使用這些程式庫來建置同時支援建議和自動完成的搜尋方塊。 在搜索框中收集的輸入與建議和自動完成操作配對。

## <a name="suggestions"></a>建議

本節將引導您從搜索框定義開始,完成建議結果的實現。 它還顯示了如何調用本文中引用的第一個 JavaScript 自動完成庫的腳本。

### <a name="create-a-search-box"></a>建立搜尋框

假設[jQuery UI 自動完成函式庫](https://jqueryui.com/autocomplete/)和 C# 中的 MVC 專案,則可以使用**Index.cshtml**檔中的 JavaScript 定義搜尋框。 庫通過對 MVC 控制器進行非同步調用來檢索建議,將「即用即用」的搜尋互動添加到搜尋框中。

在資料夾 [Views_Home] 下的**Index.cshtml**中,建立搜尋框的行可能如下所示:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

此範例是簡單的輸入文字方塊，並附有用來設定樣式的類別、JavaScript 所要參考的識別碼，和預留位置文字。  

在同一檔中,嵌入引用搜尋框的 JavaScript。 以下函數呼叫建議 API,請求基於部分字語輸入的匹配文件建議:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

告訴`source`jQuery UI 自動完成函數,您可以在何處獲取要在搜尋框下顯示的專案清單。 由於此專案是 MVC 專案,因此在 HomeController.cs 中調用**建議**函數,該**函數**包含用於返回查詢建議的邏輯。 此函式也會傳遞幾個參數來控制醒目提示、模糊比對和字詞。 自動完成 JavaScript API 會新增字詞參數。

`minLength: 3`確保建議僅在搜索框中至少有三個字元時顯示。

### <a name="enable-fuzzy-matching"></a>開啟模糊符合

當使用者在搜尋方塊中拼錯字組時，模糊搜尋依然可讓其根據相近的字組取得結果。 編輯距離為 1,這意味著使用者輸入和匹配項之間最大差異為一個字元。 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>開啟突顯

突出顯示將字體樣式應用於結果中對應於輸入的字元。 例如,如果部分輸入為「微」,則結果將顯示為**微**軟、**微**範圍等。 突出顯示基於突出顯示 Pretag 和高光 PostTag 參數,這些參數與"建議"功能內聯定義。

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>建議功能

如果使用 C# 和 MVC 應用程式,**則控制器**目錄下的 HomeController.cs 檔是可能為建議的結果創建類的位置。 在 .NET 中,建議函式以[文件操作延伸. 建議方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)。

該方法`InitSearch`將經過身份驗證的 HTTP 索引用戶端創建到 Azure 認知搜尋服務。 有關 .NET SDK 的詳細資訊,請參閱[如何使用 .NET 應用程式中的 Azure 認知搜尋](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

建議函式會採用兩個參數，以決定是要傳回命中結果醒目提示，還是要使用搜尋字詞輸入外加模糊比對。 此方法會建立 [SuggestParameters 物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)，然後傳遞到建議 API。 結果接著會轉換為 JSON，以供在用戶端中顯示。

## <a name="autocomplete"></a>自動完成

到目前為止,搜索UX代碼一直以建議為中心。 下一個程式碼塊使用 XDSoft jQuery UI 自動完成功能自動完成,在 Azure 認知搜尋自動完成請求中傳遞。 與建議一樣,在 C# 應用程式中,支援使用者互動的代碼將採用**index.cshtml**。

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>自動完成功能

自動完成此[文件操作延伸.自動完成方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。 與建議一樣,此代碼塊將位於**HomeController.cs**檔中。

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

自動完成函式會取用搜尋字詞輸入。 此方法會建立 [AutoCompleteParameters 物件](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。 結果接著會轉換為 JSON，以供在用戶端中顯示。

## <a name="next-steps"></a>後續步驟

請按照這些連結獲得端到端說明或代碼,演示兩種搜索即用式體驗。 這兩個代碼示例都包括建議的混合實現和自動完成。

+ [教學:在 C# 中建立第一個應用(第 3 課)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C# 程式碼範例:azure-搜尋-點點-樣本/創建優先應用/3 添加類型/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# 與 JavaScript,帶有 REST 並行代碼範例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)