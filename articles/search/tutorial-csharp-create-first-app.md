---
title: 建立第一個應用程式的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 了解如何逐步建置您的第一個 C# 搜尋應用程式。 此教學課程同時提供 GitHub 上可操作之應用程式的連結，以及從頭開始建置應用程式的完整程序。 了解 Azure 認知搜尋的基本元件。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 2b4f67fc448d98239947fd764d4926f1d590c5e2
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121583"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>C# 教學課程：建立第一個您的應用程式 - Azure 認知搜尋

了解如何建立 Web 介面，以使用 Azure 認知搜尋查詢並顯示來自索引的搜尋結果。 此教學課程會從裝載的現有索引開始，讓您可以將注意力放在建置搜尋頁面。 索引包含虛構的旅館資料。 一旦有基本頁面之後，您就可以在後續的課程中增強該頁面，包括分頁、Facet 和預先輸入的體驗。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 設定開發環境
> * 模型資料結構
> * 建立網頁
> * 定義方法
> * 測試應用程式

您也將了解搜尋呼叫有多麼簡單。 程式碼中您將開發的關鍵陳述式都會封裝在下列幾行。

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

這個一次呼叫會啟動 Azure 資料的搜尋，並傳回結果。

![搜尋 "pool"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

[安裝 Visual Studio](https://visualstudio.microsoft.com/) 以當作 IDE 使用。

### <a name="install-and-run-the-project-from-github"></a>從 GitHub 安裝並執行專案

1. 在 Github 上尋找範例：[建立第一個應用程式](https://github.com/Azure-Samples/azure-search-dotnet-samples)。
1. 選取 [複製或下載]  ，並建立專案的私人本機複本。
1. 使用 Visual Studio 瀏覽至基本搜尋頁面的方案並加以開啟，然後選取 [啟動但不偵錯]  (或按下 F5 鍵)。
1. 輸入一些文字 (例如 "wifi"、"view"、"bar"、"parking")，並檢查結果！

    ![搜尋 "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

希望這個專案將會順利執行，並讓 Azure 應用程式執行。 許多更複雜搜尋的基本元件都包含在這一個應用程式中，因此最好一一瀏覽，並逐步重新建立該應用程式。

若要從頭開始建立此專案，以利強化您所構思的 Azure 認知搜尋元件，請執行下列步驟。

## <a name="set-up-a-development-environment"></a>設定開發環境

1. 在 Visual Studio 2017 或更新版本中，選取 [新增/專案]  ，然後選取 [ASP.NET Core Web 應用程式]  。 為專案提供一個名稱，例如 "FirstAzureSearchApp"。

    ![建立一個雲端專案](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. 針對此專案類型按一下 [確定]  之後，將會顯示另一組適用於這個專案的選項。 選取 [Web 應用程式 (Model-View-Controller)]  。

    ![建立一個 MVC 專案](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. 接著，在 [工具]  功能表上，選取 [NuGet 套件管理員]  ，然後選取 [管理解決方案的 NuGet 套件]  。有一個我們需要安裝的套件。 選取 [瀏覽]  索引標籤，然後在搜尋方塊中輸入「Azure 認知搜尋」。 當 **Microsoft.Azure.Search** 出現在清單中 (版本 9.0.1 或更新版本) 時加以安裝。 您必須逐一點選一些其他對話方塊，才能完成安裝。

    ![使用 NuGet 來新增 Azure 程式庫](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>初始化 Azure 認知搜尋

在此範例中，我們會使用公開可用的旅館資料。 這項資料是 50 個虛構旅館名稱和描述的任意集合，專為提供示範資料而建立。 若要存取此資料，您需要指定名稱及其金鑰。

1. 在新的專案中開啟 appsettings.json 檔案，並以下列名稱和金鑰取代預設的幾行。 此處所示的 API 金鑰不是金鑰範例，它_就是_存取旅館資料所需的金鑰。 Appsettings.json 檔案現在看起來應該像這樣。

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. 我們還未完成此檔案，請選取此檔案的屬性，並將 [複製到輸出目錄]  變更為 [有更新時才複製]  。

    ![將應用程式設定複製到輸出](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>模型資料結構

模型 (C#類別) 用來傳達用戶端 (檢視)、伺服器 (控制器)，以及使用 MVC (模型、檢視、控制器) 架構之 Azure 雲端之間的資料。 一般而言，這些模型將會反映所存取之資料的結構。 此外，我們需要一個模型來處理檢視/控制器通訊。

1. 使用 [方案總管] 開啟專案的 **Models** 資料夾，您就會看到其中有一個預設的模型：**ErrorViewModel.cs**。

2. 以滑鼠右鍵按一下 **Models** 資料夾，然後依序選取 [新增]  和 [新增項目]  。 接著，在出現的對話方塊中，依序選取 **ASP.NET Core** 和第一個 [類別]  選項。 將 .cs 檔案重新命名為 Hotel.cs，然後按一下 [新增]  。 以下列程式碼取代 Hotel.cs 的所有內容。 請注意此類別的 **Address** 和 **Room** 成員，這些欄位是類別本身，因此我們也將需要這些類別的模型。

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnLucene)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [IsFilterable, IsSortable]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

3. 依照建立 **Address** 類別之模型的相同程序進行，但不要將檔案命名為 Address.cs。 將內容取代如下。

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. 同樣地，依照建立 **Room** 類別的相同程序進行，並將檔案命名為 Room.cs。 再次將內容取代如下。

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. **Hotel**、**Address** 和 **Room** 這組類別在 Azure 中已知為[_複雜類型_](search-howto-complex-data-types.md)，這是 Azure 認知搜尋的一項重要功能。 複雜類型可以是多層深的類別和子類別，而且會使用比_簡單類型_ (只包含基本成員的類別) 更複雜的資料結構表示。 我們還需要一個模型、因此請再進行一次建立新模型類別的程序，但是這次呼叫 SearchData.cs 類別，並以下列內容取代預設的程式碼。

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    此類別包含使用者的輸入 (**searchText**)，以及搜尋的輸出 (**resultList**)。 輸出的類型 (**DocumentSearchResult&lt;Hotel&gt;** ) 非常重要，因為這個類型完全符合搜尋的結果，以及我們需要將此參考傳遞到檢視。



## <a name="create-a-web-page"></a>建立網頁

您所建立的專案預設將會建立一些用戶端檢視。 確切的檢視取決於您使用的 Core .NET 版本 (在此範例中，我們使用 2.1)。 它們全都在專案的 **Views** 資料夾中。 您只需要修改 Index.cshtml 檔案 (在 **Views/Home** 資料夾中)。

刪除 Index.cshtml 中完整的內容，並使用下列步驟重建檔案。

1. 在此檢視中，我們會使用兩個小圖像。 您可以使用自己的圖像，或從 GitHub 專案複製不同的圖像：azure logo.png 和 search.png。 這兩個圖像應該置於 **wwwroot/images** 資料夾。

2. Index.cshtml 的第一行應該參考我們在用戶端 (檢視) 和伺服器 (控制站) 之間傳達資料所使用的模型，也就是我們建立的 **SearchData** 模型。 在 Index.cshtml 檔案中加入這一行。

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. 輸入檢視的標題是標準做法，因此接下來的幾行應該是：

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. 在標題後面輸入我們將在不久後建立的 HTML 樣式表的參考。

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. 現在輪到檢視的重心。 要記住的重點是檢視必須處理兩種情況。 首先，它必須處理應用程式第一次啟動，且使用者尚未輸入任何搜尋文字時的顯示。 其次，除了搜尋文字方塊之外，它還必須處理結果的顯示，以供使用者重複使用。 若要處理這兩種情況，我們必須檢查提供給檢視的模型是否為 Null。 Null 模型表示我們處於兩種情況中的第一種情況 (應用程式的初始執行)。 將下列內容加入至 Index.cshtml 檔案，並完整閱讀註解。

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. 最後，我們加入樣式表。 在 Visual Studio 的 [檔案]  功能表中，選取 [新增/檔案]  ，然後選取 [樣式表]  (醒目提示 [一般]  )。 使用下列程式碼來取代預設程式碼。 我們將不再詳細介紹此檔案，樣式為標準 HTML。

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. 將樣式表檔案儲存為 hotels.css，並與預設的 site.css 檔案一起儲存到 wwwroot/css 資料夾中。

如此便完成我們的檢視。 我們的進展良好。 模型和檢視已完成，只剩下用控制器將所有項目整合在一起。

## <a name="define-methods"></a>定義方法

我們需要修改預設建立的一個控制器 (**主控制器**) 的內容。

1. 開啟 HomeController.cs 檔案，然後將 **using** 陳述式取代如下。

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>加入 Index 方法

我們需要兩個 **Index** 方法：一個不採用任何參數 (適用於應用程式第一次開啟的情況)，另一個採用模型做為參數 (適用於使用者輸入搜尋文字的情況)。 預設會建立其中第一個方法。 

1. 在預設的 **Index()** 方法後面加上下列方法。

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    請注意方法的 **async** 宣告，以及對 **RunQueryAsync** 進行的 **await** 呼叫。 這些關鍵字負責讓呼叫變成非同步的，因此可避免阻絕伺服器上的執行緒。

    **catch** 區塊會使用預設為我們建立的錯誤模型。

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>請注意錯誤處理以及其他預設檢視和方法

根據您所使用的 .NET Core 版本，預設會建立一組稍有不同的預設檢視。 .NET Core 2.1 的預設檢視是 [索引]、[關於]、[連絡人]、[隱私權] 和 [錯誤]。 例如，.NET Core 2.2 的預設檢視是 [索引]、[隱私權] 和 [錯誤]。 在任一種情況下，您都可以在執行應用程式時檢視這些預設頁面，並檢查這些頁面在控制器中的處理方式。

我們將在此教學課程稍後測試 [錯誤] 檢視。

在 GitHub 範例中，我們已刪除未使用的檢視及其相關聯的動作。

### <a name="add-the-runqueryasync-method"></a>加入 RunQueryAsync 方法

Azure 認知搜尋呼叫會封裝在我們的 **RunQueryAsync** 方法中。

1. 首先，加入一些靜態變數以設定 Azure 服務，然後加入一個起始這些變數的呼叫。

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. 現在，加入 **RunQueryAsync** 方法本身。

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    在此方法中，我們要先確定 Azure 設定已起始，然後設定一些搜尋參數。 **Select** 參數中的欄位名稱會完全符合 **hotel** 類別中的屬性名稱。 您可以省略 **Select** 參數，在此情況下會傳回所有屬性。 不過，如果我們只對資料子集感興趣，不設定 **Select** 參數則效率不彰。 藉由指定我們感興趣的屬性，則只會傳回這些屬性。

    搜尋 (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);** ) 的非同步呼叫就是此教學課程和應用程式的全部內容。 **DocumentSearchResult** 類別是一個有趣的類別，而且在這裡 (當應用程式執行時) 設定中斷點，並使用偵錯工具檢查 **model.resultList** 的內容是個不錯的主意。 您應該會發現它是直覺式，而且只會為您提供您所要求的資料。

現在是真相的時刻。

### <a name="test-the-app"></a>測試應用程式

現在，讓我們來看看應用程式是否正確執行。

1. 選取 [偵錯/啟動但不偵錯]  ，或按 F5 鍵。 如果您編寫的程式碼正確，就會獲得初始的 [索引] 檢視。

     ![開啟應用程式](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. 輸入文字，例如 "beach" (或想到的任何文字)，然後按一下 [搜尋] 圖示。 您應該會得到一些結果。

     ![搜尋 "beach"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. 請嘗試輸入"five star"。 請注意您沒有得到任何結果。 更複雜的搜尋會將 "five star" 視為 "luxury" 的同義字，並傳回這些結果。 在 Azure 認知搜尋中可以使用同義字，但是我們不會將它涵蓋在第一個教學課程中。
 
4. 請嘗試輸入 "hot" 作為搜尋文字。 它_不會_傳回含有 "hotel" 字樣的項目。 我們的搜尋只會找出完整字詞，但是也會傳回幾個結果。

5. 請嘗試其他文字；"pool"、"sunshine"、"view" 等等。 您將會看到 Azure 認知搜尋以其最簡單、但仍令人信服的水準運作。

## <a name="test-edge-conditions-and-errors"></a>測試邊緣條件和錯誤

請務必確認我們的錯誤處理功能如預期般運作，即使一切正常運作也是如此。 

1. 在 **Index** 方法中的 **try {** 呼叫後面，輸入 **Throw new Exception()** 這一行。 當我們搜尋文字時，這個例外狀況會強制發生錯誤。

2. 執行應用程式、輸入 "bar" 作為搜尋文字，然後按一下 [搜尋] 圖示。 例外狀況應該會導致錯誤檢視。

     ![強制發生錯誤](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > 在錯誤頁面傳回內部錯誤編號會被視為安全性風險。 如果您的應用程式用於一般用途，請對發生錯誤時傳回的內容，調查其安全性和最佳做法。

3. 當錯誤處理如預期般運作時，移除 **Throw new Exception()** 。

## <a name="takeaways"></a>重要心得

請考慮此專案的下列重點：

* Azure 認知搜尋呼叫非常簡明扼要，而且很容易解譯結果。
* 非同步呼叫會對控制器增加少量的複雜性，但卻是開發高品質應用程式的最佳做法。
* 此應用程式會根據 **searchParameters** 中所設定內容的定義，執行簡單的文字搜尋。 不過，此類別可以填入許多可對搜尋增加複雜度的成員。 不需要太多額外的工作，就能讓此應用程式變得更強大。

## <a name="next-steps"></a>後續步驟

若要提供使用 Azure 認知搜尋的最佳使用者體驗，我們需要新增更多功能，尤其是分頁 (使用頁數或無限捲動)，以及自動完成/建議。 我們也應該考慮更複雜的搜尋參數 (例如，對指定點的指定半徑內的旅館進行地理搜尋，以及搜尋結果排序)。

在一系列的教學課程中，將會處理這些後續步驟。 讓我們從分頁著手。

> [!div class="nextstepaction"]
> [C# 教學課程：搜尋結果分頁 - Azure 認知搜尋](tutorial-csharp-paging.md)


