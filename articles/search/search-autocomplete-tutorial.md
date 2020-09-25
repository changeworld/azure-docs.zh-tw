---
title: 在搜尋方塊中新增自動完成和建議
titleSuffix: Azure Cognitive Search
description: 藉由建立建議工具，並以完成的字詞或片語來產生自動完成搜尋方塊的要求，在 Azure 認知搜尋中啟用搜尋型別查詢動作。 您也可以傳回建議的相符專案。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: bf5ebafa1b1ad9e2cfabcfc3d474392ecdbc98e6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268276"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>將自動完成和建議新增至用戶端應用程式

搜尋即您類型是改善使用者起始查詢生產力的常見技術。 在 Azure 認知搜尋中，您可以透過 *自動完成*來支援此體驗，這項功能會根據部分輸入完成詞彙或片語， (以「microsoft」 ) 完成「微」。 另一種形式是 *建議*：相符檔的簡短清單 (以識別碼傳回書籍標題，以便您可以連結到詳細資料頁面) 。 自動完成和建議都會針對索引中的相符前提。 服務不會提供傳回零結果的查詢。

若要在 Azure 認知搜尋中執行這些體驗，您將需要：

+ 後端上的 *建議工具* 。
+ 在要求上指定[自動完成](/rest/api/searchservice/autocomplete)或[建議](/rest/api/searchservice/suggestions)API 的*查詢*。
+ 用來處理用戶端應用程式中搜尋型別互動的 *UI 控制項* 。 基於此目的，我們建議使用現有的 JavaScript 程式庫。

在 Azure 認知搜尋中，autocompleted 查詢和建議的結果會從搜尋索引中，從您向建議工具註冊的選取欄位中取出。 建議工具是索引的一部分，而且它會指定哪些欄位將提供完成查詢的內容、建議結果，或兩者都有。 建立並載入索引時，會在內部建立建議工具資料結構，以儲存用於比對部分查詢的前置詞。 若要取得建議，請選擇唯一的適當欄位，或至少不重複的欄位，這對體驗而言是不可或缺的。 如需詳細資訊，請參閱 [建立建議工具](index-add-suggesters.md)。

本文的其餘部分著重于查詢和用戶端程式代碼。 它使用 JavaScript 和 c # 來說明重點。 REST API 範例會用來將每項作業簡潔地呈現。 如需端對端程式碼範例的連結，請參閱 [後續步驟](#next-steps)。

## <a name="set-up-a-request"></a>設定要求

要求的元素包含其中一個搜尋型別 Api、部分查詢和建議工具。 下列腳本說明要求的元件，使用 [自動完成] REST API 作為範例。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName**會提供您用來完成條款或建議的建議工具感知欄位。 針對特別建議，欄位清單應由可在相符結果之間提供明確選擇的專案所組成。 在銷售電腦遊戲的網站上，此欄位可能是遊戲的標題。

**搜尋**參數提供部分查詢，其中的字元會透過 JQuery 自動完成控制項送至查詢要求。 在上述範例中，"minecraf" 是控制項可能傳入的靜態圖例。

Api 不會對部分查詢強加最小長度需求;它可以只是一個字元。 不過，jQuery 自動完成提供最小長度。 通常最少兩個或三個字元。

相符專案在輸入字串中的任何位置的開頭。 指定「快速棕色 fox」，自動完成和建議都會與部分版本的 "" "、" quick "、" 棕色 "或" fox "相符，但不會比對部分中置詞彙（例如" rown "或" ox "）。 此外，每個相符項都會設定下游擴充的範圍。 "Quick br" 的部分查詢會比對「快速棕色」或「快速階層」，但「棕色」或「階層」本身都不會比對，除非「快速」優先。

### <a name="apis-for-search-as-you-type"></a>適用于搜尋的 Api-類型

針對 REST 和 .NET SDK 參考頁面，請遵循下列連結：

+ [建議 REST API](/rest/api/searchservice/suggestions) 
+ [自動完成 REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>結構回應

自動完成和建議的回應是您預期的模式： [自動完成](/rest/api/searchservice/autocomplete#response) 會傳回詞彙清單、 [建議](/rest/api/searchservice/suggestions#response) 傳回詞彙加上檔識別碼，讓您可以提取檔 (使用 [查閱檔](/rest/api/searchservice/lookup-document) API 來提取特定檔以取得詳細資料頁面) 。

回應是由要求上的參數所塑造。 針對 [自動完成]，設定 [**autocompletemode.none**](/rest/api/searchservice/autocomplete#autocomplete-modes) 以判斷文字完成是否會在一或兩個詞彙上進行。 若為建議，您選擇的欄位會決定回應的內容。

針對建議，您應該進一步精簡回應以避免重複，或看似不相關的結果。 若要控制結果，請在要求中包含更多參數。 下列參數適用于自動完成和建議，但可能更是建議的必要項，特別是在建議工具組含多個欄位時。

| 參數 | 使用量 |
|-----------|-------|
| **$select** | 如果您在建議工具中有多個 **sourceFields** ，請使用 **$select** 選擇 () 中提供值的欄位 `$select=GameTitle` 。 |
| **searchFields** | 將查詢限制為特定欄位。 |
| **$filter** | 將比對準則套用至結果集 (`$filter=Category eq 'ActionAdventure'`) 。 |
| **$top** | 將結果限制為特定數目 (`$top=5`) 。|

## <a name="add-user-interaction-code"></a>新增使用者互動程式碼

自動填入查詢字詞或下拉相符的連結清單需要使用者互動程式碼（通常是 JavaScript），這類程式碼可以取用來自外部來源的要求，例如自動完成或針對 Azure 搜尋服務認知索引的建議查詢。

雖然您可以原生撰寫此程式碼，但從現有的 JavaScript 程式庫使用函式更為容易。 本文將示範兩個，其中一個適用于建議，另一個用於自動完成。 

+ 建議範例中使用[自動完成小工具 (JQUERY UI) ](https://jqueryui.com/autocomplete/) 。 您可以建立搜尋方塊，然後在使用 [自動完成] widget 的 JavaScript 函式中參考它。 Widget 上的屬性會將 [自動完成] 或 [建議] 函式設為來源 () 、採取動作之前輸入字元的最小長度以及定位。

+ [>xdsoft 自動完成外掛程式](https://xdsoft.net/jqplugins/autocomplete/) 會使用自動完成範例。

我們會使用這些程式庫來建置同時支援建議和自動完成的搜尋方塊。 在搜尋方塊中收集的輸入會與建議和自動完成動作配對。

## <a name="suggestions"></a>建議

本節將逐步引導您執行建議的結果，從搜尋方塊定義開始。 它也會顯示如何和腳本，以叫用本文中所參考的第一個 JavaScript 自動完成程式庫。

### <a name="create-a-search-box"></a>建立搜尋方塊

假設 [JQUERY UI 自動完成程式庫](https://jqueryui.com/autocomplete/) 和以 c # 撰寫的 MVC 專案，您可以在 **索引 cshtml** 檔案中使用 JavaScript 定義搜尋方塊。 此程式庫會對 MVC 控制器發出非同步呼叫以取得建議，以將與您一樣的搜尋與搜尋方塊互動。

在 \Views\Home 資料夾下的 **Index. cshtml** 中，建立搜尋方塊的行可能如下所示：

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

此範例是簡單的輸入文字方塊，並附有用來設定樣式的類別、JavaScript 所要參考的識別碼，和預留位置文字。  

在相同的檔案中，內嵌參考搜尋方塊的 JavaScript。 下列函式會呼叫建議 API，該 API 會根據部分字詞輸入要求建議的相符檔：

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

會 `source` 告知 JQUERY UI 自動完成函式，以取得要在搜尋方塊下顯示的專案清單。 因為這個專案是 MVC 專案，所以它會呼叫**HomeController.cs**中的**建議**函式，其中包含傳回查詢建議的邏輯。 此函式也會傳遞幾個參數來控制醒目提示、模糊比對和字詞。 自動完成 JavaScript API 會新增字詞參數。

`minLength: 3`可確保只有在搜尋方塊中至少有三個字元時，才會顯示建議。

### <a name="enable-fuzzy-matching"></a>啟用模糊比對

當使用者在搜尋方塊中拼錯字組時，模糊搜尋依然可讓其根據相近的字組取得結果。 編輯距離為1，表示使用者輸入與相符之間可能會有一個字元的最大差異。 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>啟用反白顯示

醒目提示會將字型樣式套用至結果中對應至輸入的字元。 例如，如果部分輸入為「微」，則結果會顯示為 **微型**軟、 **微**範圍等等。 醒目提示是以 HighlightPreTag 和 HighlightPostTag 參數為基礎，以內嵌于建議函式的方式定義。

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>建議函數

如果您使用 c # 和 MVC 應用程式，您可以在 [控制器] 目錄下的 **HomeController.cs** 檔案，建立建議的結果類別。 在 .NET 中，建議函式是以 [DocumentsOperationsExtensions. 建議方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)為基礎。 如需 .NET SDK 的詳細資訊，請參閱 [如何從 .Net 應用程式使用 Azure 認知搜尋](./search-howto-dotnet-sdk.md)。

`InitSearch`方法會建立已驗證的 HTTP 索引用戶端至 Azure 認知搜尋服務。 [SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters)類別上的屬性會決定要在結果中搜尋和傳回哪些欄位、相符的數目，以及是否使用模糊比對。 

針對自動完成，模糊比對會限制為一個編輯距離， (一個省略或錯置的字元) 。 請注意，自動完成查詢中的模糊比對有時可能會產生非預期的結果，視索引大小及其分區化方式而定。 如需詳細資訊，請參閱 [分割區和分區化概念](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards)。

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

建議函式會採用兩個參數，以決定是要傳回命中結果醒目提示，還是要使用搜尋字詞輸入外加模糊比對。 此方法會建立 [SuggestParameters 物件](/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)，然後傳遞到建議 API。 結果接著會轉換為 JSON，以供在用戶端中顯示。

## <a name="autocomplete"></a>自動完成

到目前為止，搜尋 UX 程式碼已集中于建議。 下一個程式碼區塊會使用 >xdsoft jQuery UI 自動完成函式來顯示自動完成，並在要求中傳遞 Azure 認知搜尋自動完成的功能。 如同建議，在 c # 應用程式中，支援使用者互動的程式碼會進入 **索引. cshtml**。

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

### <a name="autocomplete-function"></a>自動完成函數

自動完成是根據 [DocumentsOperationsExtensions。自動完成方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。 如同建議，此程式碼區塊會進入 **HomeController.cs** 檔案。

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

自動完成函式會取用搜尋字詞輸入。 此方法會建立 [AutoCompleteParameters 物件](/rest/api/searchservice/autocomplete)。 結果接著會轉換為 JSON，以供在用戶端中顯示。

## <a name="next-steps"></a>後續步驟

請遵循下列連結以取得端對端指示，或示範搜尋即用類型體驗的程式碼。 這兩個程式碼範例都包含混合的建議和自動完成。

+ [教學課程：以 c # 建立您的第一個應用程式 (第3課) ](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C # 程式碼範例： azure-搜尋-dotnet-sample/create-first-app/3-add-自動提示/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead)
+ [使用 REST 並行程式碼的 c # 和 JavaScript 範例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)