---
title: 在搜尋方塊中新增自動完成和建議
titleSuffix: Azure Cognitive Search
description: 藉由建立建議工具，並以完成的詞彙或片語自動完成搜尋方塊，以在 Azure 認知搜尋中啟用搜尋型別查詢動作。 您也可以傳回建議的相符專案。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 004f1ea55bcda68485d8b11ed472b6cab2ca7545
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85562492"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>將自動完成和建議新增至用戶端應用程式

「搜尋型別」是一種常見的技巧，可提升使用者起始查詢的生產力。 在 Azure 認知搜尋中，您可以透過*自動完成功能*來支援這項體驗，這會根據部分輸入完成一個詞彙或片語（以「microsoft」完成「微」）。 另一個表單是*建議*：比對檔的簡短清單（傳回具有識別碼的書籍標題，讓您可以連結至詳細資料頁面）。 自動完成和建議會在索引的相符項上前提。 服務不會提供傳回零結果的查詢。

若要在 Azure 認知搜尋中執行這些體驗，您將需要：

+ 後端的*建議工具*。
+ 在要求上指定[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)或[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)API 的*查詢*。
+ 用來處理用戶端應用程式中的搜尋型別互動的*UI 控制項*。 基於此目的，我們建議使用現有的 JavaScript 程式庫。

在 Azure 認知搜尋中，會從您已向建議工具註冊的選取欄位，從搜尋索引中取出標記查詢和建議的結果。 建議工具是索引的一部分，它會指定哪些欄位將提供完成查詢、建議結果，或同時執行這兩項工作的內容。 建立並載入索引時，會在內部建立建議工具資料結構，以儲存用來比對部分查詢的前置詞。 如需建議，請選擇唯一或至少不重複的適當欄位，這是經驗不可或缺的。 如需詳細資訊，請參閱[Create a 建議工具](index-add-suggesters.md)。

本文的其餘部分著重于查詢和用戶端程式代碼。 它會使用 JavaScript 和 c # 來說明重點。 REST API 範例用來簡潔呈現每個作業。 如需端對端程式碼範例的連結，請參閱[後續步驟](#next-steps)。

## <a name="set-up-a-request"></a>設定要求

要求的元素包含其中一個搜尋型別 Api、一個部分查詢和一個建議工具。 下列腳本說明要求的元件，使用 [自動完成] REST API 做為範例。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName**會提供您用來完成條款或建議的建議工具感知欄位。 如需特別的建議，欄位清單應包含在比對結果中提供清楚選擇的專案。 在銷售電腦遊戲的網站上，此欄位可能是遊戲的標題。

**搜尋**參數會提供部分查詢，其中字元會透過 JQuery 自動完成控制項送出至查詢要求。 在上述範例中，"minecraf" 是控制項可能傳入內容的靜態圖例。

Api 不會強加部分查詢的最小長度需求;它可以只是一個字元。 不過，jQuery 自動完成會提供最小長度。 通常至少有兩個或三個字元。

相符專案會在輸入字串中任何位置的開頭處。 假設「快速棕色 fox」，自動完成和建議會比對 "the"、"quick"、"棕色" 或 "fox" 部分版本，但不符合部分中置詞彙，例如 "rown" 或 "ox"。 此外，每個相符項都會設定下游擴充的範圍。 "Quick br" 的部分查詢將會比對 "quick 棕色" 或 "quick 麵包"，但除非「快速」在其前面，否則不會有任何「棕色」或「麵包」。

### <a name="apis-for-search-as-you-type"></a>適用于搜尋型別的 Api

針對 REST 和 .NET SDK 參考頁面，請遵循下列連結：

+ [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>結構回應

自動完成和建議的回應是您預期的模式：[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response)會傳回詞彙清單、[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions#response)傳回詞彙加上檔識別碼，讓您可以提取檔（使用[查閱檔](https://docs.microsoft.com/rest/api/searchservice/lookup-document)API 來提取詳細資料頁面的特定檔）。

回應是由要求上的參數所塑造。 針對 [自動完成]，設定[**autocompleteMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes)以判斷文字是否會在一或兩個詞彙上完成。 如需建議，您選擇的欄位會決定回應的內容。

如需建議，您應該進一步精簡回應以避免重複或看似不相關的結果。 若要控制結果，請在要求中包含更多參數。 下列參數適用于自動完成和建議，但可能更需要建議，特別是當建議工具組含多個欄位時。

| 參數 | 使用方式 |
|-----------|-------|
| **$select** | 如果您在建議工具中有多個**sourceFields** ，請使用 **$select**來選擇哪些欄位會提供值（ `$select=GameTitle` ）。 |
| **searchFields** | 將查詢限制為特定欄位。 |
| **$filter** | 將比對準則套用至結果集（ `$filter=Category eq 'ActionAdventure'` ）。 |
| **$top** | 將結果限制為特定數目（ `$top=5` ）。|

## <a name="add-user-interaction-code"></a>新增使用者互動程式碼

自動填入查詢字詞或下拉相符的連結清單需要使用者互動程式碼（通常是 JavaScript），其可以取用來自外部來源的要求，例如針對 Azure 搜尋服務認知索引的自動完成或建議查詢。

雖然您可以用原生方式撰寫此程式碼，但使用現有 JavaScript 程式庫的函式會更加容易。 本文將示範兩個，一個用於建議，另一個用於自動完成。 

+ 建議範例中會使用[自動完成 widget （JQUERY UI）](https://jqueryui.com/autocomplete/) 。 您可以建立 [搜尋] 方塊，然後在使用 [自動完成] widget 的 JavaScript 函式中參考它。 小工具上的屬性會設定來源（自動完成或建議功能）、採取動作之前的輸入字元長度下限，以及定位。

+ [XDSoft 自動完成外掛程式](https://xdsoft.net/jqplugins/autocomplete/)會使用自動完成範例。

我們會使用這些程式庫來建置同時支援建議和自動完成的搜尋方塊。 在 [搜尋] 方塊中收集的輸入會與建議和自動完成動作配對。

## <a name="suggestions"></a>建議

本節將逐步引導您執行建議的結果，從搜尋方塊定義開始。 它也會顯示如何和腳本，以叫用本文中參考的第一個 JavaScript 自動完成程式庫。

### <a name="create-a-search-box"></a>建立搜尋方塊

假設[JQUERY UI 自動完成程式庫](https://jqueryui.com/autocomplete/)和 c # 中的 MVC 專案，您可以在**索引 cshtml**檔案中使用 JavaScript 定義搜尋方塊。 程式庫會對 MVC 控制器發出非同步呼叫來取得建議，以將搜尋型別的互動新增至搜尋方塊。

在資料夾 \Views\Home 下的 [ **Index. cshtml** ] 中，建立搜尋方塊的一行可能如下所示：

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

此範例是簡單的輸入文字方塊，並附有用來設定樣式的類別、JavaScript 所要參考的識別碼，和預留位置文字。  

在相同的檔案中，內嵌參考 [搜尋] 方塊的 JavaScript。 下列函式會呼叫建議 API，它會根據部分詞彙輸入來要求建議的相符檔：

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

會 `source` 告訴 JQUERY UI 自動完成函式，在何處取得要在搜尋方塊底下顯示的專案清單。 由於此專案是 MVC 專案，因此它會呼叫**HomeController.cs**中的**建議**函式，其中包含傳回查詢建議的邏輯。 此函式也會傳遞幾個參數來控制醒目提示、模糊比對和字詞。 自動完成 JavaScript API 會新增字詞參數。

`minLength: 3`可確保只有在搜尋方塊中至少有三個字元時，才會顯示建議。

### <a name="enable-fuzzy-matching"></a>啟用模糊比對

當使用者在搜尋方塊中拼錯字組時，模糊搜尋依然可讓其根據相近的字組取得結果。 編輯距離是1，這表示使用者輸入與相符項之間可能會有一個字元的最大差異。 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>啟用反白顯示

反白顯示會將字型樣式套用至結果中對應至輸入的字元。 例如，如果部分輸入為「微」，則結果會顯示為「**微**軟」、「**微**範圍」等等。 反白顯示是根據 HighlightPreTag 和 HighlightPostTag 參數，並以建議函式進行內嵌定義。

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>建議函式

如果您使用 c # 和 MVC 應用程式，在 [控制器] 目錄下的**HomeController.cs**檔案就是您可以為建議的結果建立類別的位置。 在 .NET 中，建議函式是以[DocumentsOperationsExtensions 方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)為基礎。

`InitSearch`方法會對 Azure 認知搜尋服務建立已驗證的 HTTP 索引用戶端。 如需 .NET SDK 的詳細資訊，請參閱[如何從 .Net 應用程式使用 Azure 認知搜尋](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

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

到目前為止，搜尋 UX 程式碼已集中在建議的中心。 下一個程式碼區塊會顯示自動完成，使用 XDSoft jQuery UI 自動完成功能，傳入 Azure 認知搜尋自動完成的要求。 如同建議，在 c # 應用程式中，支援使用者互動的程式碼會在**index. cshtml**中進行。

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

自動完成是以 DocumentsOperationsExtensions 為基礎[。自動完成方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。 如同建議，此程式碼區塊會放在**HomeController.cs**檔案中。

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

請遵循下列連結，以取得端對端指示，或示範搜尋型別體驗的程式碼。 這兩個程式碼範例都包含混合式的建議和自動完成。

+ [教學課程：以 c # 建立您的第一個應用程式（第3課）](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C # 程式碼範例： azure-搜尋-dotnet-範例/建立-第一個-應用程式/3-新增-自動提示/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C # 和 JavaScript 搭配 REST 並存程式碼範例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)