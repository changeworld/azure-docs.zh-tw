---
title: 關於搜尋結果分頁的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 本教學課程示範搜尋結果的分頁。 它是以現有的飯店專案為基礎而建置的，並依第一頁、下一頁、上一頁、最後一頁和編號按鈕分頁。 第二個分頁系統會使用無止盡捲動 (透過將垂直捲軸移至其下限來觸發)。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 9abfeb54be6e22885b8e973034a6d89df8272146
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121515"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C# 教學課程：搜尋結果分頁 - Azure 認知搜尋

了解如何實作兩個不同的分頁系統，第一個會以頁碼為依據，第二個則與無止盡捲動有關。 這兩個分頁系統都受到廣泛使用，而且選取正確的分頁系統取決於您所需結果的使用者體驗。 此教學課程會將分頁系統建置於 [C# 教學課程：建立第一個您的應用程式 - Azure 認知搜尋](tutorial-csharp-create-first-app.md)教學課程中所建立的專案之上。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 使用已編號的分頁擴充您的應用程式
> * 使用無止盡捲動擴充您的應用程式

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

啟動並執行 [C# 教學課程：建立您的第一個應用程式 - Azure 認知搜尋](tutorial-csharp-create-first-app.md)專案。 此專案可以是您自己的版本，或從 GitHub 安裝：[建立第一個應用程式](https://github.com/Azure-Samples/azure-search-dotnet-samples)。

## <a name="extend-your-app-with-numbered-paging"></a>使用已編號的分頁擴充您的應用程式

已編號的分頁是主要網際網路搜尋引擎和其他大部分搜尋網站所選擇的分頁系統。 除了一連串的實際頁碼，已編號的分頁通常還會包含 [下一頁] 和 [上一頁] 選項。 此外，可能也會提供 [第一頁] 和 [最後一頁] 選項。 這些選項肯定能讓使用者控制如何在以頁面為基礎的結果間進行巡覽。

我們將新增的系統會包含第一頁、上一頁、下一頁和最後一頁選項，以及不是從 1 開始的頁碼，而是改用使用者目前所在頁面前後範圍的頁碼 (因此，舉例來說，如果使用者正在查看第 10 頁，則可能顯示 8、9、10、11 和 12 等頁碼)。

系統將有足夠的彈性，能夠在全域變數中設定可見頁碼的數量。

系統會將最左邊和最右邊的頁碼按鈕視為特殊按鈕，這表示它們將觸發變更所顯示頁碼的範圍。 例如，如果顯示的頁碼為 8、9、10、11 和 12，而且使用者按一下 8，則顯示的頁碼範圍會變更為 6、7、8、9 和 10。 此外，如果他們選取了 12，則會產生類似的往右移位。

### <a name="add-paging-fields-to-the-model"></a>將分頁欄位新增至模型

開啟基本搜尋頁面解決方案。

1. 開啟 SearchData.cs 模型檔案。

2. 首先，新增一些全域變數。 在 MVC 中，在它們自己的靜態類別中宣告全域變數。 **ResultsPerPage** 會設定每一頁的結果數量。 **MaxPageRange** 會決定檢視上的可見頁碼數量。 **PageRangeDelta** 會在選取最左邊或最右邊的頁碼時，決定頁面範圍應該往左或往右移位多少頁。 通常後面的這個數字大約是 **MaxPageRange** 的一半。 將下列程式碼新增至命名空間。

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >如果您正在配備較小型螢幕的裝置 (例如膝上型電腦) 上執行此專案，則考慮將 **ResultsPerPage** 變更為 2。

3. 將 paging 屬性新增至 **SearchData** 類別，例如，在 **searchText** 屬性之後。

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>將分頁選項表新增至檢視

1. 開啟 index.cshtml 檔案，並將下列程式碼新增至結尾 &lt;/body&gt; 標記的正前方。 這個新的程式碼會呈現一個分頁選項表格：第一頁、上一頁、1、2、3、4、5、下一頁、最後一頁。

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&lt;</p>
                }
            </td>

            @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
            {
                <td>
                    @if (Model.currentPage == pn)
                    {
                        // Convert displayed page numbers to 1-based and not 0-based.
                        <p class="pageSelected">@(pn + 1)</p>
                    }
                    else
                    {
                        <p class="pageButton">
                            @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;|</p>
                }
            </td>
        </tr>
    </table>
    }
    ```

    我們會使用 HTML 表格，讓選項能夠整齊地對齊。 不過，所有動作均來自 @Html.ActionLink 陳述式，每個都使用**新**模型來呼叫控制器，而此模型是使用與我們稍早新增之 **paging** 屬性不同的項目所建立的。

    第一頁和最後一頁選項不會傳送 "first" 和 "last" 之類的字串，但會改為傳送正確的頁碼。

2. 將某些分頁類別新增至 hotels.css 檔案中的 HTML 樣式清單。 **pageSelected** 類別用來在頁碼清單中識別使用者目前正在檢視的頁面 (藉由讓該數字顯示為粗體)。

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>將 Page 動作新增至控制器

1. 開啟 HomeController.cs 檔案，然後新增 **Page** 動作。 此動作會回應所選取的任何頁面選項。

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    **RunQueryAsync** 方法現在將因為第三個參數而顯示語法錯誤 (我們很快就會討論到這一點)。

    > [!Note]
    > **TempData** 呼叫會將值 (一個**物件**) 儲存於暫存位置，儘管這個暫存位置「只」  會針對一次呼叫來保留。 如果我們將某個項目儲存於暫存資料中，則它將適用於下一次對控制器動作的呼叫，但在那之後該呼叫就一定會讓它消失！ 由於這個生命週期很短，因此，我們會在每次呼叫 **Page** 時，將搜尋文字和 paging 屬性儲存回暫存位置。

2. **Index(model)** 動作需要更新，才能儲存暫存變數，以及將最左邊的頁面參數新增至 **RunQueryAsync** 呼叫。

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. **RunQueryAsync** 方法需要大幅更新。 我們使用 **SearchParameters** 類別的 **Skip**、**Top** 及 **IncludeTotalResultCount** 欄位，只要求一頁有價值的結果 (從 **Skip** 設定開始)。 我們也需要針對檢視來計算分頁變數。 使用下列程式碼來取代整個方法。

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
                   SearchMode = SearchMode.All,

                   // Skip past results that have already been returned.
                   Skip = page * GlobalVariables.ResultsPerPage,

                   // Take only the next page worth of results.
                   Top = GlobalVariables.ResultsPerPage,

                   // Include the total number of results.
                   IncludeTotalResultCount = true,
               };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
    ```

4. 最後，需要對檢視進行小幅變更。 變數 **resultsList.Results.Count** 現在將包含一頁中所傳回的結果數量 (在我們的範例中為 3)，而非總數。 因為我們將 **IncludeTotalResultCount** 設定為 true，所以變數 **resultsList.Count** 現在會包含結果總數。 因此，找到檢視中顯示結果數量的地方，然後將它變更為下列程式碼。

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > 藉由將 **IncludeTotalResultCount** 設為 true，可對效能產生影響 (但通常不是很大)，因為這個總和需要透過 Azure 認知搜尋來計算。 使用複雜的資料集，會有一則警告，表示傳回的值為「近似值」  。 對於我們的旅館資料，它將是準確的。

### <a name="compile-and-run-the-app"></a>編譯並執行應用程式

現在，選取 [啟動但不偵錯]  (或按 F5 鍵)。

1. 搜尋某些將提供大量結果 (例如 "wifi") 的文字。 您可以在頁面上整齊地顯示結果嗎？

    ![已透過 "pool" 結果來將分頁編碼](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. 嘗試按一下最右邊的頁碼，稍後再按一下最左邊的頁碼。 頁碼能夠適當地調整以位於您所在頁面的中間嗎？

3. [第一頁] 和 [最後一頁] 選項是否實用？ 某些熱門的 Web 搜尋會使用這些選項，但其他的則不會。

4. 移至結果的最後一頁。 最後一頁是唯一可能包含少於 **ResultsPerPage** 筆結果的頁面。

    ![檢查 "wifi" 的最後一頁](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. 輸入 "town"，然後按一下 [搜尋]。 如果有價值的結果少於一頁，則不會顯示任何分頁選項。

    ![搜尋 "town"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

現在儲存並關閉此專案，然後讓我們試試這種分頁形式的替代方案。

## <a name="extend-your-app-with-infinite-scrolling"></a>使用無止盡捲動擴充您的應用程式

當使用者捲動垂直捲軸到所顯示的最後一個結果時，即會觸發無止盡捲動。 在此事件中，會呼叫伺服器以取得下一頁的結果。 如果沒有其他結果，則不會傳回任何項目，而且垂直捲軸不會變更。 如果有更多結果，就會將它們附加至目前的頁面，而捲軸會變更以顯示更多可供使用的結果。

這裡的重點是，顯示的頁面不會被取代，但會附加新結果。 使用者一律可以向上捲動回到第一個搜尋結果。

若要實作無止盡捲動，讓我們從已新增任何頁碼捲動元素之前的專案開始。 因此，如果您需要，請從 GitHub 取得另一個基本搜尋頁面複本：[建立第一個應用程式](https://github.com/Azure-Samples/azure-search-dotnet-samples)。

### <a name="add-paging-fields-to-the-model"></a>將分頁欄位新增至模型

1. 首先，將 **paging** 屬性新增至 **SearchData** 類別 (在 SearchData.cs 模型檔案中)。

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    此變數是一個字串，如果應該傳送下一頁結果，就會保留 "next"，或者，如果是搜尋的第一頁，則會是 Null。

2. 在同一個檔案中且於命名空間內，新增具有一個屬性的全域變數類別。 在 MVC 中，在它們自己的靜態類別中宣告全域變數。 **ResultsPerPage** 會設定每一頁的結果數量。 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>在檢視中新增垂直捲軸

1. 在 index.cshtml 檔案中找到顯示結果的區段 (它的開頭為 **@if (Model != null)** )。

2. 使用下列程式碼來取代該區段。 新的 **&lt;div&gt;** 區段位於應可捲動的區域附近，並新增 **overflow-y** 屬性和對 **onscroll** 函式的呼叫 (稱為 "scrolled()")，如下。

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. 在迴圈正下方的 &lt;/div&gt; 標記之後，新增 **scrolled** 函式。

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    上述指令碼中的 **if** 陳述式會進行測試，以查看使用者是否已將垂直捲軸捲動至底部。 如果是，則會對名為 **Next** 的動作呼叫 [首頁]  控制器。 控制器不需要任何其他資訊，它將傳回下一頁的資料。 接著會使用與原始頁面相同的 HTML 樣式來將此資料格式化。 如果未傳回任何結果，則不會附加任何項目，而所有項目都會保持原樣。

### <a name="handle-the-next-action"></a>處理 Next 動作

只有三個動作需要傳送到控制器：第一次執行應用程式，會呼叫 **Index()** ；使用者所進行的第一次搜尋，會呼叫 **Index(model)** ；接著是透過 **Next(model)** 進行後續呼叫以取得更多結果。

1. 開啟首頁控制器檔案，然後刪除來自原此教學課程的 **RunQueryAsync** 方法。

2. 使用下列程式碼來取代 **Index(model)** 動作。 它現在會在 **paging** 欄位為 Null 時處理該欄位，或設定為 "next"，並處理對 Azure 認知搜尋的呼叫。

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    與已編號的分頁方法類似，我們會使用 **Skip** 和 **Top** 搜尋設定，只要求傳回我們所需的資料。

3. 在主首頁控制器中新增 **Next** 動作。 請注意它傳回清單的方式，每家旅館都會在清單中新增兩個元素：旅館名稱和旅館描述。 此格式會設定來符合 **scrolled** 函式對檢視中已傳回資料所使用的格式。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. 如果您收到有關 **List&lt;string&gt;** 的語法錯誤，請將下列 **using** 指示詞新增至控制器檔案的標頭。

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>編譯並執行您的專案

現在，選取 [啟動但不偵錯]  (或按 F5 鍵)。

1. 輸入將提供大量結果的字詞 (例如 "pool")，然後測試垂直捲軸。 它會觸發新的結果頁面嗎？

    ![無止盡捲動 "pool" 結果](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > 為了確保第一頁會出現捲軸，第一頁的結果必須稍微超過顯示它們的區域高度。 在我們的範例中， **.box1** 的高度為 30 個像素、 **.box2** 的高度為 100 個像素「且」  下邊界為 24 個像素。 因此，每個項目都會使用 154 個像素。 三個項目將佔用 3 x 154 = 462 個像素。 為了確保垂直捲軸會出現，必須將顯示區域的高度設定為小於 462 個像素，甚至是 461 個像素才能運作。 此問題只會發生於第一頁，捲軸在那之後就一定會出現。 更新下列這一行： **&lt;div id="myDiv" style="width:800px; height:450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** 。

2. 一直向下捲動到結果底部。 請注意現在所有資訊均位於一個檢視頁面上。 您可以一直捲動回到頂端，而不會觸發任何伺服器呼叫。

更複雜的無止盡捲動系統可能會使用滑鼠滾輪或類似的其他機制，來觸發新結果頁面的載入。 我們將不會在這些教學課程中進一步採用無止盡捲動，但它有特定的常用鍵，可避免額外的滑鼠點選，而且您可能想要進一步調查其他選項！

## <a name="takeaways"></a>重要心得

請考慮此專案的下列重點：

* 如果搜尋的結果順序是可隨意自訂的，最好使用已編號的分頁，這表示後續頁面上可能也會出現您的使用者感興趣的項目。
* 若結果的順序特別重要，則最好使用無止盡捲動。 例如，假設會依與目的地城市中心的距離來排列結果。
* 已編號的分頁能夠進行一些更好的瀏覽。 例如，使用者可以記住有趣的結果是在第 6 頁，而在無止盡捲動中不存在這類簡單的參考。
* 無止盡捲動只有一個簡單的訴求：向上和向下捲動，而沒有需要點選的繁瑣頁碼。
* 無止盡捲動的重要功能之一是，會將結果附加至現有頁面，而不會取代該頁面，這是有效率的作法。
* 暫存位置只會針對一次呼叫來保留，而且需要重設才能在其他呼叫中存留。


## <a name="next-steps"></a>後續步驟

分頁是網際網路搜尋的基礎。 充分運用分頁之後，下一個步驟是藉由新增預先輸入的搜尋，進一步改善使用者體驗。

> [!div class="nextstepaction"]
> [C# 教學課程：新增自動完成和建議 - Azure 認知搜尋](tutorial-csharp-type-ahead-and-suggestions.md)
