---
title: 自動完成和建議的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 本教學課程示範自動完成和建議，作為向使用下拉式清單的使用者收集搜尋字詞輸入的方法。 它是以現有的飯店專案為基礎而建置的。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 8f244d64fe33a1529cf66314515bbe16e05ccffb
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121542"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>C# 教學課程：新增自動完成和建議 - Azure 認知搜尋

了解如何在使用者開始在搜尋方塊中輸入時實作自動完成 (預先輸入及建議)。 在此教學課程中，我們將分別顯示預先輸入結果和建議結果，並示範結合兩者以建立更豐富的使用者體驗的方法。 使用者只需要輸入兩個或三個按鍵，就可以找到可用的所有結果。 此教學課程建置於 [C# 教學課程：搜尋結果分頁 - Azure 認知搜尋](tutorial-csharp-paging.md)教學課程。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 新增建議
> * 在建議中加入醒目提示
> * 新增自動完成
> * 結合自動完成和建議

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

啟動並執行 [C# 教學課程：搜尋結果分頁 - Azure 認知搜尋](tutorial-csharp-paging.md)專案。 此專案可以是您自己在上一個教學課程中完成的版本，或從 GitHub 安裝的版本：[建立第一個應用程式](https://github.com/Azure-Samples/azure-search-dotnet-samples)。

## <a name="add-suggestions"></a>新增建議

讓我們從為使用者提供替代方案的最簡單案例開始：建議的下拉式清單。

1. 在 index.cshtml 檔案中，將 **TextBoxFor** 陳述式變更如下。

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    此處的重點是我們已經將搜尋方塊的識別碼設定為 **azureautosuggest**。

2. 在此陳述式結尾的 **&lt;/div&gt;** 之後，輸入這個指令碼。

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    我們已經透過相同的識別碼，將此指令碼連接到搜尋方塊中。 此外，最少需要兩個字元才能觸發搜尋，而且我們會使用兩個查詢參數，在主控制器中呼叫 **Suggest** 動作：**highlights** 和 **fuzzy**，在此案例中，這兩者均設為 false。

### <a name="add-references-to-jquery-scripts-to-the-view"></a>將 jquery 指令碼的參考新增至檢視

在上述指令碼中呼叫的自動完成函式不需要我們自己編寫，因為該函式可在 jquery 程式庫中取得。 

1. 若要存取 jquery 程式庫，請將檢視檔案的 &lt;head&gt; 區段變更為下列程式碼。

    ```cs
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

2. 我們也需要移除或註解化 _Layout.cshtml 檔案中參考 jquery 的那一行 (在 **Views/Shared** 資料夾中)。 找出下列幾行，並註解化第一個指令碼行，如下所示。 這項變更可避免對 jquery 參考的衝突。

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    現在我們可以使用預先定義的自動完成 jquery 函式。

### <a name="add-the-suggest-action-to-the-controller"></a>將建議動作新增至控制器

1. 在主控制器中，新增 **Suggest** 動作 (例如，在 **Page** 動作之後)。

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    **Top** 參數會指定要傳回多少結果 (如果未指定，預設值為 5)。 _建議工具_是在 Azure 索引上指定的，這會在資料設定時完成，而且不是由用戶端應用程式完成，例如此教學課程。 在此案例中，建議工具稱為 "sg"，而且它會搜尋 **HotelName** 欄位 - 沒有其他項目。 

    模糊比對可在輸出中包含「接近未命中」。 如果 **highlights** 參數設為 true，則粗體顯示的 HTML 標記就會加入至輸出。 在下一節中，我們會將這兩個參數設定為 true。

2. 您可能會收到一些語法錯誤。 若是如此，請在檔案頂端加入下列兩個 **using** 陳述式。

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. 執行應用程式。 例如，當您輸入 "po" 時，您是否會獲得一系列的選項？ 現在，請嘗試輸入 "pa"。

    ![輸入 "po" 可顯示兩個建議](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    請注意，您輸入的字母_必須_是一個單字的開始，而不只是這個字中的一個字母。

4. 在檢視指令碼中，將 **&fuzzy** 設為 true，然後再次執行應用程式。 現在，請輸入 "po"。 請注意，搜尋服務會假設您有一個字母錯誤！
 
    ![將 fuzzy 設定為 true 時，輸入 "pa"](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    如果您感興趣，[Azure 認知搜尋服務中的 Lucene 查詢語法](https://docs.microsoft.com/azure/search/query-lucene-syntax)詳細描述模糊搜尋中使用的邏輯。

## <a name="add-highlighting-to-the-suggestions"></a>在建議中加入醒目提示

我們可以將 **highlights** 參數設為 true，藉此稍微改善向使用者提供之建議的外觀。 不過，首先我們需要將一些程式碼加入至檢視，以顯示粗體文字。

1. 在檢視 (index.cshtml) 中，於您上面輸入的 **azureautosuggest** 指令碼後面加入下列指令碼。

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

2. 現在，變更文字方塊的識別碼，因此它會讀取如下。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. 再次執行應用程式，您應該會看到您輸入的文字在建議中呈現粗體。 例如，嘗試輸入 "pa"。
 
    ![輸入醒目提示的 "pa"](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. 上述反白顯示指令碼中使用的邏輯非常簡單。 如果您輸入的詞彙在相同的名稱中出現兩次，則粗體的結果不是您想要的結果。 嘗試輸入 "mo"。

    開發人員必須回答的其中一個問題是指令碼什麼時候運作得「夠好」，以及什麼時候應該處理其花體時。 此外，我們將不會在此教學課程中採取任何進一步地醒目提示，但如果要進一步地醒目提示，必須考慮尋找一個精確的演算法。

## <a name="add-autocompletion"></a>新增自動完成

另一種與建議稍有不同的變化是自動完成功能 (有時稱為「預先輸入」)。 同樣地，我們會先從最簡單的實作開始，然後再繼續改善使用者體驗。

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

2. 現在，變更文字方塊的識別碼，因此它會讀取如下。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. 在主控制器中，我們需要輸入 **Autocomplete** 動作，例如，在 **Suggest** 動作下面。

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    請注意，我們在自動完成搜尋中使用的*建議工具*功能 (稱為 "sg") 與我們用於建議的相同 (因此我們只要嘗試自動完成旅館名稱)。

    有多種 **AutocompleteMode** 設定，而我們會使用 **OneTermWithContext**。 如需此處選項範圍的描述，請參閱 [Azure 自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。

4. 執行應用程式。 請注意，下拉式清單中所顯示的選項範圍是單一文字。 請嘗試輸入開頭為 "re" 的文字。 請注意，輸入的字母越多，選項的數量越少。

    ![使用基本的自動完成輸入](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    就目前而言，您之前執行的建議指令碼可能比這個自動完成指令碼更有用。 若要讓使用者更容易使用自動完成，最好將其加入至建議搜尋。

## <a name="combine-autocompletion-and-suggestions"></a>結合自動完成和建議

結合自動完成和建議是選項中最複雜的，而且可能會提供最佳的使用者體驗。 我們想要顯示的是內嵌正在輸入的文字，也就是 Azure 認知搜尋服務對於自動完成文字的第一個選擇。 此外，我們想要一系列的建議作為下拉式清單。

有提供這項功能 (通常稱為「內嵌自動完成」或類似的名稱) 的程式庫。 不過，我們要以原生方式實作這項功能，讓您可以看到發生的情況。 在此範例中，我們將先開始處理控制器。

1. 我們需要將動作新增至只傳回一個自動完成結果，以及指定之建議數目的控制器。 我們將此動作稱為 **AutocompleteAndSuggest**。 在主控制器中，加入下列動作，後面接著其他新的動作。

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    系統會在**結果**清單的最上方傳回一個自動完成選項，後面接著所有建議。

2. 在檢視中，首先我們會實作一個技巧，讓淺灰色的自動完成文字呈現在使用者所輸入的粗體文字正下方。 HTML 會包含用於此目的的相對位置。 將 **TextBoxFor** 陳述式 (及其周圍的 &lt;div&gt; 陳述式) 變更如下，並注意，將被視為 **underneath** 的另一個搜尋方塊拉開其預設位置 39 個像素，該搜尋方塊就會位於我們一般搜尋方塊的正下方！

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    請注意，在此案例中，我們將識別碼再次變更為 **azureautocomplete**。

3. 此外，在檢視中，將下列指令碼輸入到您到目前為止所輸入之所有指令碼的後面。 這個檢視中有很多指令碼。

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
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
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

    請留意 **interval** 函式的巧妙用法可在加底線的文字不再符合使用者輸入的內容時加以清除，也可以在使用者輸入時設定相同的大小寫 (大寫或小寫) (因為在搜尋時，"pa" 會比對 "PA"、"pA"、"Pa")，讓重疊的文字很整齊。

    完整閱讀指令碼中的註解以獲得更全面的了解。

4. 最後，我們需要微調兩個 HTML 類別，讓它們變成透明的。 將下列這一行加入至 hotels.css 檔案中的 **searchBoxForm** 和 **searchBox** 類別。

    ```html
        background: rgba(0,0,0,0);
    ```

5. 現在，執行應用程式。 在搜尋方塊中輸入"pa"。 您是否獲得 "palace" 這個自動完成建議，以及包含 "pa" 的兩間旅館？

    ![以內嵌的自動完成及建議輸入](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. 請嘗試使用 Tab 鍵接受自動完成建議，然後嘗試使用方向鍵和 Tab 鍵選擇建議，最後再次嘗試使用滑鼠按一下。 請確認指令碼會靈巧地處理所有這些情況。

    您可能認為在為您提供此功能的程式庫中載入比較簡單，但現在您至少知道一種讓內嵌的自動完成運作的方法！

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


