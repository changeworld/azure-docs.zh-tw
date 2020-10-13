---
title: 關於使用 Facet 協助導覽的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 延續 [分頁結果] 以加入多面向導覽。 了解如何使用 Facet 輕鬆地縮小搜尋範圍。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ab15af07c5f63d375d8fdb4fc38e0853e207a0be
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667277"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>教學課程：使用 .NET SDK 新增多面向導覽

Facet 藉由提供一組用來篩選結果的連結，啟用自我導向導覽。 在本教學課程中，多面向導覽結構會放在頁面的左側，並具有標籤和可點選文字來修剪結果。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 將模型屬性設定為 _IsFacetable_
> * 在應用程式中新增 Facet 導覽

## <a name="overview"></a>總覽

Facet 是以您搜尋索引中的欄位為基礎。 包含 facet=[string] 的查詢要求會提供 Facet 作為依據的欄位。 通常會包含多個 Facet，例如 `&facet=category&facet=amenities`，每個都以 & 符號字元分隔。 您必須同時指定 Facet 和篩選條件，才能執行多面向導覽結構。 此篩選條件會用於 click 事件，以縮小結果範圍。 例如，按一下 [預算] 就會根據該準則來篩選結果。

本教學課程會擴充在[將分頁新增至搜尋結果](tutorial-csharp-paging.md)教學課程中所建立的分頁專案。

您可以在下列專案中找到本教學課程中的最終版本程式碼：

* [4-add-facet-navigation (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>必要條件

* [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) 解決方案。 此專案可以是您自己在上一個教學課程中建置的版本，或從 GitHub 複製的版本。

本教學課程已更新為使用 [Azure.Search.Documents (第 11 版)](https://www.nuget.org/packages/Azure.Search.Documents/) 套件。 如需舊版的 .NET SDK，請參閱 [Microsoft.Azure.Search (第 10 版) 程式碼範例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)。

## <a name="set-model-properties-as-isfacetable"></a>將模型屬性設定為 IsFacetable

為了可在 Facet 搜尋中找到模型屬性，必須使用 **IsFacetable** 來為它加上標記。

1. 檢查 **Hotel** 類別。 例如，**Category** 和 **Tags** 都會標記為 **IsFacetable**，但 **HotelName** 和 **Description** 則不會。 

    ```cs
    public partial class Hotel
    {
        [SimpleField(IsFilterable = true, IsKey = true)]
        public string HotelId { get; set; }

        [SearchableField(IsSortable = true)]
        public string HotelName { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public string Category { get; set; }

        [SearchableField(IsFilterable = true, IsFacetable = true)]
        public string[] Tags { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public bool? ParkingIncluded { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true)]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
    ```

1. 我們將不會在進行此教學課程的過程中變更任何標記，因此請依原樣關閉 hotel.cs 檔案。

    > [!Note]
    > 如果未適當地標記搜尋中所要求的欄位，則 Facet 搜尋將擲回錯誤。

## <a name="add-facet-navigation-to-your-app"></a>在應用程式中新增 Facet 導覽

針對此範例，我們要讓使用者能夠從結果左側所顯示的連結清單中，選取一個旅館類別或一個設施。 使用者一開始先輸入一些搜尋文字，然後選取類別或設施來逐漸縮小搜尋結果的範圍。

將 Facet 的清單傳遞到檢視是控制器的作業。 為了在搜尋過程中維護使用者選取項目，我們會使用暫存位置作為保留資料的機制。

![使用 Facet 導覽來縮小 "pool" 的搜尋範圍](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>將篩選字串新增至 SearchData 模型

1. 開啟 SearchData.cs 檔案，然後將字串屬性新增至 **SearchData** 類別，以保留 Facet 篩選條件字串。

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>新增 Facet 動作方法

首頁控制器需要一個新動作 **Facet**，並更新其現有的 **Index** 和 **Page** 動作，以及更新 **RunQueryAsync** 方法。

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. 取代 **Index(SearchData model)** 動作方法。

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
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. 取代 **PageAsync(SearchData model)** 動作方法。

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
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

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. 新增 **FacetAsync(SearchData model)** 動作方法，以便在使用者按一下 Facet 連結時啟動。 此模型將包含類別或設施搜尋篩選。 將其新增到 **PageAsync** 動作之後。

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>設定搜尋篩選

當使用者選取特定的 Facet 時，例如，當他們按下 [Resort and Spa]  類別時，則結果中應該只會傳回已指定為此類別的旅館。 若要以此方式縮小搜尋範圍，我們需要設定「篩選」  。

1. 使用下列程式碼來取代 **RunQueryAsync** 方法。 首先，其會取得類別篩選字串和設施篩選字串，並設定 **SearchOptions** 的 **Filter** 參數。

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    請注意，已將 **Category** 和 **Tags** 屬性新增至要傳回的 **Select** 項目清單。 這項新增並不是可讓 Facet 導覽運作的需求，但是我們會使用此資訊來確認篩選正常運作。

### <a name="add-lists-of-facet-links-to-the-view"></a>將 Facet 連結清單新增至檢視

檢視將需要有一些重大變更。 

1. 首先，開啟 hotels.css 檔案 (在 wwwroot/css 資料夾中)，然後新增下列類別。

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. 針對檢視，將輸出組織為表格，以便在左側整齊地對齊 Facet 清單，並在右側顯示結果。 開啟 index.cshtml 檔案。 使用下列程式碼來取代 HTML &lt;body&gt; 標記的整個內容。

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
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
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    請注意 **Html.ActionLink** 呼叫的使用。 當使用者按一下 Facet 連結時，此呼叫會將有效的篩選字串傳遞給控制器。 

### <a name="run-and-test-the-app"></a>執行和測試應用程式

對使用者而言，Facet 導覽的優點是它們可透過按一下的方式來縮小搜尋範圍，而我們可依照下列順序來顯示。

1. 執行應用程式，輸入 "airport" 作為搜尋文字。 確認 Facet 清單會整齊地顯示於左側。 這些 Facet 全都適用於其文字資料中含有 "airport" 的旅館及其發生頻率的計數。

    ![使用 Facet 導覽來縮小 "airport" 的搜尋範圍](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. 按一下 [Resort and Spa]  類別。 確認此類別中的所有結果。

    ![將搜尋範圍縮小為 "Resort and Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. 按一下 [continental breakfast]  設施。 使用選取的設施，確認所有結果仍都位於 "Resort and Spa" 類別中。

    ![將搜尋範圍縮小為 "continental breakfast"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. 嘗試選取任一個其他類別，然後選取一個設施，並檢視縮小範圍的結果。 接著反向嘗試另一種方式，先選取一個設施，然後選取一個類別。 傳送空白搜尋以重設頁面。

    >[!Note]
    > 在 Facet 清單中選取一個項目 (例如類別) 時，它將會在類別清單中覆寫任何先前的選取項目。

## <a name="takeaways"></a>重要心得

請考慮此專案的下列重點：

* 務必要將每個可 Facet 欄位標記為 **IsFacetable** 屬性，以包含在 Facet 導覽中。
* Facet 會與篩選結合以減少結果。
* Facet 是累計的，其中的每個選項會根據上一個選項建置，以進一步縮小結果範圍。

## <a name="next-steps"></a>後續步驟

在下一個教學課程中，我們將探討排序結果。 到目前為止，結果都只是按照它們在資料庫中的順序來排序。

> [!div class="nextstepaction"]
> [C# 教學課程：排序結果 - Azure 認知搜尋](tutorial-csharp-orders.md)
