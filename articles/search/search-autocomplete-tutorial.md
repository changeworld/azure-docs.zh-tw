---
title: 在搜尋方塊中新增建議和自動完成
titleSuffix: Azure Cognitive Search
description: 藉由建立建議工具，並制定以已完成的字詞或片語填入搜尋方塊的要求，在 Azure 認知搜尋中啟用自動提示查詢動作。
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "72792513"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>將建議或自動完成新增至您的 Azure 認知搜尋應用程式

在本文中，您將了解如何使用 [建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)來建置功能強大的搜尋方塊，以支援在輸入期間進行搜尋的行為。

+ 「建議」  是您輸入時所產生的建議結果，其中的每個建議分別是索引中與您到目前為止輸入的內容相符的單一結果。 

+ 「自動完成」  會「完成」使用者目前輸入的單字或片語。 它不會傳回結果，而是會完成查詢，讓您可接著執行以傳回結果。 如同建議，查詢中完成的單字或片語取決於索引中的相符項目。 此服務提供的查詢不會從索引傳回零個結果。

您可以從 **DotNetHowToAutocomplete** 下載並執行範例程式碼，以評估這些功能。 範例程式碼以填入 [NYCJobs 示範資料](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)的預建索引為目標。 NYCJobs 索引包含[建議工具建構](index-add-suggesters.md)，這是使用建議或自動完成功能的必要項目。 您可以使用在沙箱服務中裝載的既有索引，也可以使用 NYCJobs 解決方案範例中的資料載入器來[填入您自己的索引](#configure-app)。 

**DotNetHowToAutocomplete** 範例會示範 C# 和 JavaScript 語言版本的建議與自動完成功能。 C# 開發人員可以逐步執行使用 [Azure 認知搜尋 .NET SDK](https://aka.ms/search-sdk) 的 ASP.NET MVC 型應用程式。 您可以在 HomeController.cs 檔案中找到進行自動完成和建議查詢呼叫的邏輯。 JavaScript 開發人員可在 IndexJavaScript.cshtml 中找到等同的查詢邏輯，其中包括對 [Azure 認知搜尋 REST API](https://docs.microsoft.com/rest/api/searchservice/) 的直接呼叫。 

這兩種語言版本的前端使用者體驗以 [jQuery UI](https://jqueryui.com/autocomplete/) 和 [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) 程式庫為基礎。 我們會使用這些程式庫來建置同時支援建議和自動完成的搜尋方塊。 在搜尋方塊中收集的輸入會與建議和自動完成動作配對，例如在 HomeController.cs 或 IndexJavaScript.cshtml 中定義的動作。

此練習將逐步引導您完成下列工作：

> [!div class="checklist"]
> * 在 JavaScript 中實作搜尋輸入方塊，並針對建議的相符項目或自動完成的字詞發出要求
> * 使用 C# 時，在 HomeController.cs 中定義建議和自動完成動作
> * 使用 JavaScript 時，直接呼叫 REST API 以提供相同的功能

## <a name="prerequisites"></a>Prerequisites

Azure 認知搜尋服務在此練習中是選擇性的，因為解決方案所使用的即時沙箱服務裝載了備妥的 NYCJobs 示範索引。 如果您想要在自己的搜尋服務上執行此範例，請參閱[設定 NYC 作業索引](#configure-app)以取得相關指示。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 的任何版本。 範例程式碼和指示已在免費的 Community 版本上經過測試。

* 下載 [DotNetHowToAutoComplete 範例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)。

此範例非常完整，涵蓋了建議、自動完成、多面向導覽和用戶端快取等功能。 如需範例所提供之內容的完整說明，請參閱讀我檔案和註解。

## <a name="run-the-sample"></a>執行範例

1. 在 Visual Studio 中開啟 **AutocompleteTutorial.sln**。 解決方案中包含連線至 NYC 作業示範索引的 ASP.NET MVC 專案。

2. 按 F5 執行專案，並在所選擇的瀏覽器中載入頁面。

在頂端，您會看到可供選取 C# 或 JavaScript 的選項。 C# 選項會從瀏覽器呼叫 HomeController，並使用 Azure 認知搜尋 .Net SDK 來擷取結果。 

JavaScript 選項會直接從瀏覽器呼叫 Azure 認知搜尋 REST API。 此選項會將控制器拿到流程外，因此其效能通常會顯著提升。 您可以選擇適合您需求和語言喜好設定的選項。 頁面上有幾個自動完成範例，且各有一些指導方針。 每個範例都有一些建議的文字範例可供您嘗試。  

請嘗試在每個搜尋方塊中輸入幾個字母來看看會發生什麼事情。

## <a name="search-box"></a>搜尋方塊

C# 和 JavaScript 版本的搜尋方塊實作方式完全相同。 

開啟 \Views\Home 資料夾底下的 **Index.cshtml** 檔案，以檢視程式碼：

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

此範例是簡單的輸入文字方塊，並附有用來設定樣式的類別、JavaScript 所要參考的識別碼，和預留位置文字。  其神奇之處在於內嵌的 JavaScript。

C# 語言範例會在 Index.cshtml 中使用 JavaScript，以運用 [jQuery UI 的自動完成程式庫](https://jqueryui.com/autocomplete/)。 此程式庫會對 MVC 控制器發出非同步呼叫以擷取建議，藉以在搜尋方塊中新增自動完成體驗。 JavaScript 語言版本位於 IndexJavaScript.cshtml 中。 其中包含下列用於搜尋列的指令碼，以及 REST API 對 Azure 認知搜尋的呼叫。

我們來看看第一個範例的 JavaScript 程式碼，它會呼叫 jQuery UI 自動完成函式，並傳入建議的要求：

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

上述程式碼會在頁面載入期間於瀏覽器中執行，以對 "example1a" 輸入方塊設定 jQuery UI 自動完成。  `minLength: 3` 可確保在搜尋方塊中至少有三個字元時，才會顯示建議。  來源值很重要：

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

以上這一行會向 jQuery UI 自動完成函式指出應在何處取得項目清單，以顯示在搜尋方塊下。 此專案是 MVC 專案，因此會呼叫 HomeController.cs 中的建議函式，其中包含傳回查詢建議的邏輯 (如需「建議」的詳細資訊，請參閱下一節)。 此函式也會傳遞幾個參數來控制醒目提示、模糊比對和字詞。 自動完成 JavaScript API 會新增字詞參數。

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>延伸範例以支援模糊比對

當使用者在搜尋方塊中拼錯字組時，模糊搜尋依然可讓其根據相近的字組取得結果。 此功能雖非必要，但可大幅改善自動提示體驗的健全性。 請將來源程式行變更為啟用模糊比對，試試這項功能。

將下面這行從：

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

變更為以下程式碼：

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

按 F5 啟動應用程式。

試試看輸入「execative」之類的字詞，並注意其變回「executive」結果的情形 (雖然這些結果並未與您所輸入的字母完全相符)。

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>Azure 認知搜尋自動完成支援的 jQuery 自動完成

到目前為止，搜尋 UX 程式碼主要是用在建議方面。 下一個程式碼區塊會顯示 jQuery UI 自動完成函式 (index. cshtml 中的 91 行)，並傳入 Azure 認知搜尋自動完成的要求：

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
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
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# 範例

現在，我們已檢閱網頁的 JavaScript 程式碼，接下來要看看使用 Azure 認知搜尋 .NET SDK 實際擷取建議相符項目的 C# 伺服器端控制器程式碼。

開啟 Controllers 目錄下的 **HomeController.cs** 檔案。 

您可能會先注意到類別 `InitSearch` 頂端的方法。 此方法會建立已對 Azure 認知搜尋服務進行驗證的 HTTP 索引用戶端。 如需詳細資訊，請參閱[如何從 .NET 應用程式使用 Azure 認知搜尋](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

在 41 行上，您會看到建議函式。 其基礎為 [DocumentsOperationsExtensions.Suggest 方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
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

在 69 行上，您會看到自動完成函式。 其基礎為 [DocumentsOperationsExtensions.Autocomplete 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。

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

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

自動完成函式會取用搜尋字詞輸入。 此方法會建立 [AutoCompleteParameters 物件](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。 結果接著會轉換為 JSON，以供在用戶端中顯示。

(選擇性) 在建議函式開頭新增中斷點，然後逐步執行程式碼。 請注意 SDK 所傳回的回應，以及它會如何轉換為方法所傳回的結果。

頁面上的其他範例會依照相同模式新增命中結果醒目提示和 Facet，以支援自動完成結果的用戶端快取。 檢閱上述每一個項目以了解其運作方式，以及如何在搜尋體驗中運用這些項目。

## <a name="javascript-example"></a>JavaScript 範例

自動完成和建議的 JAVAscript 實作會使用 URI 作為指定索引和作業的來源，呼叫 REST API。 

若要檢閱 JavaScript 實作情形，請開啟 **IndexJavaScript.cshtml**。 請注意，jQuery UI 自動完成函式也會用於搜尋方塊，以收集搜尋字詞輸入，以及對 Azure 認知搜尋進行非同步呼叫，進而擷取建議的相符項目或已完成的字詞。 

請看第一個範例的 JavaScript 程式碼：

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

如果您比較此範例與上述呼叫首頁控制器的範例，您會發現幾個相似之處。  `minLength` 和 `position` 的自動完成設定完全相同。 

這邊的重大變更是來源。 系統不會在首頁控制器中呼叫建議方法，而是會在 JavaScript 函式中建立 REST 要求並使用 Ajax 來執行。 回應接著會「成功」處理，並作為來源。

REST 呼叫會使用 URI 來指定是要進行[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)還是[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions) API 呼叫。 下列 URI 分別位於第 9 和 10 行上。

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

在 148 行上，您可以找到呼叫 `autocompleteUri` 的指令碼。 對 `suggestUri` 的第一個呼叫位於 39 行上。

> [!Note]
> 此處提供以 JavaScript 對服務進行的 REST 呼叫，以便示範 REST API，但您不應將其視為最佳做法或建議。 在指令碼中納入 API 金鑰和端點，會使您的服務暴露於拒絕服務攻擊下，任何人只要能從指令碼中讀取這些值，都可發動攻擊。 雖然基於學習目的使用 JavaScript 並無安全上的顧慮 (可能用在裝載於免費服務上的索引)，但我們建議在生產程式碼中應使用 Java 或 C# 進行索引編製和查詢作業。 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>設定要在您的服務上執行的 NYCJobs

到目前為止，您使用的都是裝載的 NYCJobs 示範索引。 如果您想要完整查看所有程式碼 (包括索引)，請依照下列指示，在您自己的搜尋服務中建立和載入索引。

1. [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 在此範例中，您可以使用免費服務。 

   > [!Note]
   > 如果您使用免費的 Azure 認知搜尋服務，則最多只能使用三個索引。 NYCJobs 資料載入器會建立兩個索引。 請確定服務內有空間可接受新的索引。

1. 下載 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 範例程式碼。

1. 在 NYCJobs 範例程式碼的 DataLoader 資料夾中，使用 Visual Studio 開啟 **DataLoader.sln**。

1. 新增 Azure 認知搜尋服務的連線資訊。 開啟 DataLoader 專案中的 App.config，然後變更 TargetSearchServiceName 和 TargetSearchServiceApiKey appSettings，以反映 Azure 認知搜尋服務和 Azure 認知搜尋服務 API 金鑰。 您可以在 Azure 入口網站中找到這項資訊。

1. 按 F5 以啟動應用程式，建立兩個索引並匯入 NYCJob 範例資料。

1. 開啟 **AutocompleteTutorial.sln**，並編輯 **AutocompleteTutorial** 專案中的 Web.config。 將 SearchServiceName 和 SearchServiceApiKey 值變更為對您的搜尋服務有效的值。

1. 按 F5 執行應用程式。 範例 Web 應用程式會在預設瀏覽器中開啟。 此體驗與沙箱版本相同，但索引和資料會裝載於您的服務上。

## <a name="next-steps"></a>後續步驟

此範例示範了一些基本步驟，讓您了解如何建置支援自動完成和建議的搜尋方塊。 您已看到如何建置 ASP.NET MVC 應用程式，並使用 Azure 認知搜尋 .NET SDK 或 REST API 來擷取建議。

在後續步驟中，請試著將建議和自動完成整合到搜尋體驗中。 下列參考文章應該有所幫助。

> [!div class="nextstepaction"]
> [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [建立索引 REST API 上的 Facet 索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index)

