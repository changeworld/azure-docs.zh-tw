---
title: 自動完成和建議的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 新增自動完成和建議，以使用下拉式清單向使用者收集搜尋字詞輸入。 此教學課程會以現有的飯店專案為基礎。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/22/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 06c0b25bcf64cfce01b4144550ef69da8c96ee0e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785849"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>教學課程：使用 .NET SDK 新增自動完成和建議

了解如何在使用者開始在搜尋方塊中輸入時實作自動完成 (預先輸入查詢及建議的結果)。 在本教學課程中，我們會分別示範自動完成的查詢和建議結果，然後將這兩者合併在一起。 使用者只需要輸入兩或三個字元，就可以找到可用的所有結果。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 新增建議
> * 在建議中加入醒目提示
> * 新增自動完成
> * 結合自動完成和建議

## <a name="overview"></a>總覽

本教學課程會將自動完成和建議的結果新增至先前的[將分頁新增至搜尋結果](tutorial-csharp-paging.md)教學課程。

您可以在下列專案中找到本教學課程中的最終版本程式碼：

* [3-add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>必要條件

* [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) 解決方案。 此專案可以是您自己在上一個教學課程中建置的版本，或從 GitHub 複製的版本。

本教學課程已更新為使用 [Azure.Search.Documents (第 11 版)](https://www.nuget.org/packages/Azure.Search.Documents/) 套件。 如需舊版的 .NET SDK，請參閱 [Microsoft.Azure.Search (第 10 版) 程式碼範例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)。

## <a name="add-suggestions"></a>新增建議

讓我們從為使用者提供替代方案的最簡單案例開始：建議結果的下拉式清單。

1. 在 index.cshtml 檔案中，將 **TextBoxFor** 陳述式的 `@id` 變更為 **azureautosuggest**。

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. 在此陳述式結尾的 **&lt;/div&gt;** 之後，輸入這個指令碼。 此指令碼會利用來自開放原始碼 jQuery UI 程式庫的[自動完成 widget](https://api.jqueryui.com/autocomplete/)，來呈現建議結果的下拉式清單。

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    識別碼 `"azureautosuggest"` 會將上述指令碼連線到搜尋方塊。 widget 來源選項會設定為使用這兩個查詢參數來呼叫建議 API 的建議方法：**highlights** 和 **fuzzy**，兩者都在此實例中設定為 false。 此外，若要觸發搜尋，至少需要兩個字元。

### <a name="add-references-to-jquery-scripts-to-the-view"></a>將 jQuery 指令碼的參考新增至檢視

1. 若要存取 jQuery 程式庫，請將檢視檔案的 &lt;head&gt; 區段變更為下列程式碼：

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. 因為我們引進了新的 jQuery 參考，所以我們也需要在 _Layout.cshtml 檔案 (位於 **Views/Shared** 資料夾) 中將預設的 jQuery 參考移除或標示為註解。 找出下列幾行，並註解化第一個指令碼行，如下所示。 這項變更可避免對 jQuery 參考的衝突。

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    現在我們可以使用預先定義的自動完成 jQuery 函式。

### <a name="add-the-suggest-action-to-the-controller"></a>將建議動作新增至控制器

1. 在主控制器中，新增 **SuggestAsync** 動作 (在 **PageAsync** 動作之後)。

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
        var options = new SuggestOptions()
        {
            UseFuzzyMatching = fuzzy,
            Size = 8,
        };

        if (highlights)
        {
            options.HighlightPreTag = "<b>";
            options.HighlightPostTag = "</b>";
        }

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    **Size** 參數會指定要傳回多少結果 (如果未指定，預設值為 5)。 建立索引時，會在搜尋索引上指定 _建議工具_。 在 Microsoft 所裝載的範例旅館索引中，建議工具名稱是 "sg"，建議工具會搜尋 **HotelName** 欄位中以獨佔方式建議的相符項目。

    模糊比對可在輸出中包含「接近未命中」(最多一個編輯距離)。 如果 **highlights** 參數設為 true，則粗體顯示的 HTML 標記就會加入至輸出。 在下一節中，我們會將這兩個參數設定為 true。

2. 您可能會收到一些語法錯誤。 若是如此，請在檔案頂端加入下列兩個 **using** 陳述式。

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. 執行應用程式。 例如，當您輸入 "po" 時，您是否會獲得一系列的選項？ 現在，請嘗試輸入 "pa"。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="輸入 *po* 可顯示兩個建議" border="false":::

    請注意，您輸入的字母 _必須_ 是一個單字的開始，而不只是這個字中的一個字母。

4. 在檢視指令碼中，將 **&fuzzy** 設為 true，然後再次執行應用程式。 現在，請輸入 "po"。 請注意，搜尋服務會假設您有一個字母錯誤。
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="將 fuzzy 設定為 true 時，輸入 *pa*" border="false":::

    如果您感興趣，[Azure 認知搜尋服務中的 Lucene 查詢語法](./query-lucene-syntax.md)詳細描述模糊搜尋中使用的邏輯。

## <a name="add-highlighting-to-the-suggestions"></a>在建議中加入醒目提示

我們可以將 **highlights** 參數設為 true，藉此改善向使用者提供的建議外觀。 不過，首先我們需要將一些程式碼加入至檢視，以顯示粗體文字。

1. 在檢視 (index.cshtml) 中，於前面所述的 `"azureautosuggest"` 指令碼後面新增下列指令碼。

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. 現在，變更文字方塊的識別碼，因此它會讀取如下。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. 再次執行應用程式，您應該會看到您輸入的文字在建議中呈現粗體。 嘗試輸入 "pa"。
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="輸入醒目提示的 *pa*" border="false":::

   上述反白顯示指令碼中使用的邏輯非常簡單。 如果您輸入的詞彙在相同的名稱中出現兩次，則粗體的結果不是您想要的結果。 嘗試輸入 "mo"。

   開發人員必須回答的其中一個問題是指令碼什麼時候運作得「夠好」，以及什麼時候應該處理其花體時。 此外，我們將不會在此教學課程中採取任何進一步地醒目提示，但如果醒目提示未對您的資料產生作用，則必須考慮尋找一個精確的演算法。 如需詳細資訊，請參閱[命中結果醒目提示](search-pagination-page-layout.md#hit-highlighting)。

## <a name="add-autocomplete"></a>新增自動完成

另一種與建議稍有不同的變化是完成查詢字詞的自動完成功能 (有時稱為「預先輸入」)。 同樣地，我們會先從最簡單的實作開始，然後再改善使用者體驗。

1. 依照您先前的指令碼，將下列指令碼輸入檢視中。

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. 現在，變更文字方塊的識別碼，因此它會讀取如下。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. 在主控制器中，在 **SuggestAsync** 動作之後新增 **AutocompleteAsync** 動作。

    ```cs
    public async Task<ActionResult> AutoCompleteAsync(string term)
    {
        InitSearch();

        // Setup the autocomplete parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 6
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

        // Convert the autocompleteResult results to a list that can be displayed in the client.
        List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

        return new JsonResult(autocomplete);
    }
    ```

    請注意，我們在自動完成搜尋中使用的 *建議工具* 功能 (稱為 "sg") 與我們用於建議的相同 (因此我們只要嘗試自動完成旅館名稱)。

    有多種 **AutocompleteMode** 設定，而我們會使用 **OneTermWithContext**。 如需其他選項的說明，請參閱[自動完成 API](/rest/api/searchservice/autocomplete)。

1. 執行應用程式。 請注意，下拉式清單中所顯示的選項範圍是單一文字。 請嘗試輸入開頭為 "re" 的文字。 請注意，輸入的字母越多，選項的數量越少。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="使用基本的自動完成輸入" border="false":::

    就目前而言，您之前執行的建議指令碼可能比這個自動完成指令碼更有用。 若要讓自動完成更方便使用者使用，請考慮將其與建議的結果搭配使用。

## <a name="combine-autocompletion-and-suggestions"></a>結合自動完成和建議

結合自動完成和建議是選項中最複雜的，而且可能會提供最佳的使用者體驗。 我們想要顯示的是內嵌正在輸入的文字，也就是 Azure 認知搜尋服務對於自動完成文字的第一個選擇。 此外，我們想要一系列的建議作為下拉式清單。

有提供這項功能 (通常稱為「內嵌自動完成」或類似的名稱) 的程式庫。 不過，我們要以原生方式實作這項功能，讓您可以探索 API。 在此範例中，我們將先開始處理控制器。

1. 將動作新增至只傳回一個自動完成結果，以及指定建議數目的控制器。 我們將此動作稱為 **AutoCompleteAndSuggestAsync**。 在主控制器中，加入下列動作，後面接著其他新的動作。

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    系統會在 **結果** 清單的最上方傳回一個自動完成選項，後面接著所有建議。

1. 在檢視中，首先我們會實作一個技巧，讓淺灰色的自動完成文字呈現在使用者所輸入的粗體文字正下方。 HTML 會包含用於此目的的相對位置。 將 **TextBoxFor** 陳述式 (及其周圍的 &lt;div&gt; 陳述式) 變更如下，並注意，將被視為 **underneath** 的另一個搜尋方塊拉開其預設位置 39 個像素，該搜尋方塊就會位於我們一般搜尋方塊的正下方！

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    請注意，在此案例中，我們將識別碼再次變更為 **azureautocomplete**。

1. 此外，在檢視中，將下列指令碼輸入到您到目前為止所輸入之所有指令碼的後面。 由於指令碼所處理的各種輸入行為，所以指令碼很冗長且複雜。

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    請留意 **interval** 函式的用法可在加底線的文字不再符合使用者輸入的內容時加以清除，也可以在使用者輸入時設定相同的大小寫 (大寫或小寫) (因為在搜尋時，"pa" 會比對 "PA"、"pA"、"Pa")，讓重疊的文字很整齊。

    完整閱讀指令碼中的註解以獲得更全面的了解。

1. 最後，我們需要微調兩個 HTML 類別，讓它們變成透明的。 將下列這一行加入至 hotels.css 檔案中的 **searchBoxForm** 和 **searchBox** 類別。

    ```html
    background: rgba(0,0,0,0);
    ```

1. 現在，執行應用程式。 在搜尋方塊中輸入"pa"。 您是否獲得 "palace" 這個自動完成建議，以及包含 "pa" 的兩間旅館？

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="以內嵌的自動完成及建議輸入" border="false":::

1. 請嘗試使用 Tab 鍵接受自動完成建議，然後嘗試使用方向鍵和 Tab 鍵選擇建議，最後再次嘗試使用滑鼠按一下。 請確認指令碼會靈巧地處理所有這些情況。

    您可能認為在為您提供此功能的程式庫中載入比較簡單，但現在您至少知道一種讓內嵌的自動完成運作的方法。

## <a name="takeaways"></a>重要心得

請考慮此專案的下列重點：

* 自動完成 (亦稱為「預先輸入」) 和建議可讓使用者只輸入幾個鍵，就可以找到他們想要的字。
* 自動完成和建議一起運作可以提供豐富的使用者體驗。
* 請務必使用所有形式的輸入，測試自動完成功能。
* 使用 **setInterval** 函式可能在驗證及更正 UI 元素時很有用。

## <a name="next-steps"></a>後續步驟

在下一個教學課程中，我們來看看另一種使用 Facet 搭配按一下滑鼠來縮小搜尋範圍，以改善使用者體驗的方法。

> [!div class="nextstepaction"]
> [C# 教學課程：使用 Facet 協助導覽 - Azure 認知搜尋](tutorial-csharp-facets.md)
