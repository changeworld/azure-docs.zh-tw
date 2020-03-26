---
title: 排序結果的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 本教學課程會示範如何排序搜尋結果。 它是以先前的飯店專案為基礎所建置，依主要屬性、次要屬性進行排序，並包含評分設定檔來新增提升準則。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121556"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C# 教學課程：排序結果 - Azure 認知搜尋

在我們的教學課程中到目前為止，結果是以預設順序傳回和顯示。 這可能是資料配置的順序，也可能已定義預設「評分設定檔」  ，當未指定排序參數時會使用此設定檔。 在此教學課程中，我們將會探討如何根據主要屬性排序結果、如何排序主要屬性相同的結果，以及如何根據次要屬性排序選擇。 作為依數值排序的替代方案，最後一個範例示範如何依自訂評分設定檔排序。 我們也會進一步探討如何顯示「複雜類型」  。

為了輕鬆地比較傳回的結果，此專案是建置在 [C# 教學課程：搜尋結果分頁 - Azure 認知搜尋](tutorial-csharp-paging.md)教學課程。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 根據一個屬性排序結果
> * 根據多個屬性排序結果
> * 根據與地理點的距離篩選結果
> * 根據評分設定檔排序結果

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

啟動並執行 [C# 教學課程：搜尋結果分頁 - Azure 認知搜尋](tutorial-csharp-paging.md)專案。 此專案可以是您自己的版本，或從 GitHub 安裝：[建立第一個應用程式](https://github.com/Azure-Samples/azure-search-dotnet-samples)。

## <a name="order-results-based-on-one-property"></a>根據一個屬性排序結果

當我們根據一個屬性 (假設是旅館評等) 排序結果時，我們不只想排序結果，我們也想要確認順序正確無誤。 換句話說，如果我們依評等排序，我們應該在檢視顯示評等。

在此教學課程中，我們也會在結果的顯示新增更多項目、每家旅館最便宜和最貴的房間價格。 隨著我們探索排序，我們也會新增值，以確認我們的排序依據也顯示在檢視中。

若要啟用排序，不需要修改任何模型。 檢視和控制器需要更新。 首先，開啟首頁控制器。

### <a name="add-the-orderby-property-to-the-search-parameters"></a>將 OrderBy 屬性新增至搜尋參數

1. 只要將 **OrderBy** 參數設定為屬性名稱，就能根據單一數值屬性排序結果。 在 **Index(SearchData model)** 方法中，將下列行新增至搜尋參數。

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > 預設順序為遞增，您還是可以將 **asc** 加入屬性讓此意圖更清楚。 新增 **desc** 可指定遞減順序。

2. 現在，執行應用程式，並輸入任何一般搜尋字詞。 結果的順序可能不正確，因為您 (開發人員) 和使用者都沒有容易的方法可以驗證結果！

3. 讓我們清楚顯示結果是依評等排序的。 首先，將 hotels.css 檔案中的 **box1** 和 **box2** 類別取代為下列類別 (這些是此教學課程中所需的所有新類別)。

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >瀏覽器通常會快取 CSS 檔案，這可能導致使用舊的 CSS 檔案，而忽略您的編輯。 解決此問題的好方法是在連結新增包含版本參數的查詢字串。 例如：
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >如果您認為瀏覽器在使用舊版 CSS 檔案，請更新版本號碼。

4. 在 **Index(SearchData model)** 方法中，將 **Rating** 屬性新增至 **Select** 參數。

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. 開啟檢視 (index.cshtml)，並將轉譯迴圈 ( **&lt;!-- Show the hotel data. --&gt;** ) 取代為下列程式碼。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. 在第一次顯示的頁面中，以及透過無限捲動呼叫的後續頁面中都必須能取得評等。 在這兩個情況的後者中，我們必須更新控制器中的 **Next** 動作和檢視中的 **scrolled** 函式。 從控制器開始，將 **Next** 方法變更為下列程式碼。 此程式碼會建立評等文字，並進行通訊。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. 現在，更新檢視中的 **scrolled** 函式，以顯示評等文字。

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. 現在，再次執行應用程式。 搜尋任何一般字詞 (例如，"wifi")，並確認結果會依旅館評等遞減順序排序。

    ![根據評等排序](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    您會發現幾個旅館有相同評等，因此它們在顯示中的出現順序是找到資料的順序，而這是任意的。

    在探討新增排序的第二層級之前，讓我們新增一些程式碼來顯示房間價格範圍。 我們新增此程式碼來顯示從「複雜類型」  擷取資料，也讓我們可以討論根據價格排序 (可能先顯示最便宜的)。

### <a name="add-the-range-of-room-rates-to-the-view"></a>將房間價格範圍新增到檢視

1. 將包含最便宜和最貴房間價格的屬性新增至 Hotel.cs 模型。

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. 在首頁控制器中 **Index(SearchData model)** 動作的結尾計算房間價格。 在儲存暫存資料之後加入計算。

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. 在控制器的 **Index(SearchData model)** 動作方法中，將 **Rooms** 屬性新增至 **Select** 參數。

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. 變更檢視中的轉譯迴圈，以在結果的第一頁顯示價格範圍。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. 變更首頁控制器中的 **Next** 方法，為後續結果頁面通訊價格範圍。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. 更新檢視中的 **scrolled** 函式，以處理房間價格文字。

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. 執行應用程式，並確認房間價格範圍會顯示。

    ![顯示房間價格範圍](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

搜尋參數的 **OrderBy** 屬性不會接受 **Rooms.BaseRate** 之類的項目提供最便宜房間價格，即使房間已經根據價格排序。 在此情況下，會議室不會依費率排序。 為了顯示範例資料集中的旅館並根據房間價格排序，您需要在您的首頁控制器中排序結果，並按照想要的順序將結果傳送至檢視。

## <a name="order-results-based-on-multiple-values"></a>根據多個值排序結果

現在考慮的問題是，如何區分相同價格的旅館。 根據旅館上次整修的時間來排序是不錯的方法。 換句話說，越近期整修的旅館，於結果中出現在越前面。

1. 若要新增排序的第二個層級，變更 **Index(SearchData model)** 方法中的 **OrderBy** 和 **Select** 屬性，以包含 **LastRenovationDate** 屬性。

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >可在 **OrderBy** 清單中輸入任意數目的屬性。 如果旅館有相同的評等和整修日期，可以輸入第三個屬性來區分它們。

2. 同樣地，我們需要在檢視中查看整修日期，以確認排序是正確的。 使用年份作為整修日期應該就可以。 將檢視中的轉譯迴圈變更為下列程式碼。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. 變更首頁控制器中的 **Next** 方法，以轉送最近整修日期的年份元件。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. 變更檢視中的 **scrolled** 函式，以顯示整修文字。

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. 執行應用程式。 搜尋一般字詞，例如，"pool" (游泳池) 或 "view" (景觀)，並確認相同評等的旅館現在會依整修日期遞減順序顯示。

    ![依整修日期排序](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>根據與地理點的距離篩選結果

評等及重新裝修日期是最適合以遞減順序顯示的範例屬性。 字母順序是適合遞增排序的範例 (例如，如果只有一個 **OrderBy** 屬性且設定為 **HotelName**，則會依字母順序顯示)。 不過，對於我們的範例資料，與某個地理點的距離會更適當。

若要根據地理距離顯示結果，需要幾個步驟。

1. 藉由輸入包含經度、緯度和半徑參數的篩選條件，將從給定點算起在指定半徑外的旅館篩選掉。 首先提供給 POINT 函式的是經度。 半徑的單位是公里。

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. 上述篩選條件「不會」  根據距離排序結果，它只會移除極端值。 若要排序結果，請輸入指定 geoDistance 方法的 **OrderBy** 設定。

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. 雖然結果是由 Azure 認知搜尋使用距離篩選條件傳回，但「不會」  傳回所計算資料和指定點之間的距離。 如果您想要在結果中顯示距離，請在檢視或控制器中重新計算此值。

    下列程式碼會計算兩個 lat/lon 點之間的距離。

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. 現在，您必須將這些概念繫結在一起。 不過，這些程式碼片段僅止於我們的教學課程內容，建置地圖型應用程式留給讀者當作練習。 若要進一步修改此範例，請考慮輸入包含半徑的城市名稱，或在地圖上定位某個點並選取半徑。 若要進一步調查這些選項，請參閱下列資源：

* [Azure 的地圖文件](https://docs.microsoft.com/azure/azure-maps/)
* [使用 Azure 地圖服務搜尋服務來尋找地址](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address) \(部分機器翻譯\)

## <a name="order-results-based-on-a-scoring-profile"></a>根據評分設定檔排序結果

到目前為止，教學課程中提供的範例顯示如何依數值 (評等、整修日期、地理距離) 排序，它們提供排序的「確切」  程序。 不過，某些搜尋和某些資料本身不支援兩個資料元素之間進行這類簡單比較。 Azure 認知搜尋服務包含「評分」  的概念。 「評分設定檔」  可以指定給一組資料，用來提供更複雜且質化的比較，在比較文字型資料以決定顯示順序時最能發揮其效果。

評分設定檔不是由使用者定義，但通常是由資料集管理員定義。 旅館資料上已經設定數個評分設定檔。 讓我們看看評分設定檔的定義方式，然後嘗試撰寫程式碼搜尋它們。

### <a name="how-scoring-profiles-are-defined"></a>評分設定檔是如何定義的

讓我們看看三個評分設定檔範例，並考慮每個「應該」  如何影響結果順序。 身為應用程式開發人員，您不會撰寫這些設定檔，它們是由資料管理員撰寫，不過，查看語法會很有幫助。

1. 這是旅館資料集的預設評分設定檔，當您沒有指定任何 **OrderBy** 或 **ScoringProfile** 參數時會使用它。 如果搜尋文字出現在旅館名稱、描述或標籤清單 (amenities (便利設施)) 中，則此設定檔會提升旅館的「分數」  。 請注意評分的權數會如何偏好某些欄位。 如果搜尋文字出現在其他欄位中 (下面未列出)，則其權數為 1。 很明顯地，分數越高的結果，會越早出現在檢視中。

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. 如果提供的參數包括一或多個標籤清單 (稱為 "amenities")，則下列評分設定檔會大幅提升分數。 此設定檔的重點是，「必須」  提供包含文字的參數。 如果參數是空的或未提供，系統會擲回錯誤。
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. 在此第三個範例中，評等可讓分數大幅提升。 上次整修日期也會提升分數，但只有當該資料是在目前日期的 730 天 (2 年) 內時才有效。

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    現在，讓我們查看這些設定檔是否如我們預期般運作！

### <a name="add-code-to-the-view-to-compare-profiles"></a>將程式碼新增至檢視以比較設定檔

1. 開啟 index.cshtml 檔案，並將 &lt;body&gt; 區段取代為下列程式碼。

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. 開啟 SearchData.cs 檔案，並將 **SearchData** 類別取代為下列程式碼。

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. 開啟 hotels.css 檔案，並新增下列 HTML 類別。

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>將程式碼新增至控制器以指定評分設定檔

1. 開啟首頁控制器檔案。 新增下列 **using** 陳述式 (協助建立清單)。

    ```cs
    using System.Linq;
    ```

2.  針對此範例，我們需要 **Index** 的初始呼叫多做一些事，而不只是傳回初始檢視。 該方法現在會搜尋最多 20 個便利設施以在檢視中顯示。

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. 我們需要兩個私用方法來儲存暫存位置的 Facet，以及從暫存位置復原它們並填入模型。

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. 我們必須視需要設定  **OrderBy** 和 **ScoringProfile** 參數。 以下列程式碼取代現有 **Index(SearchData model)** 方法。

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
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
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    閱讀每個 **switch** 選項的註解。

5. 如果您已完成上一節根據多個屬性排序的其他程式碼，則我們不需要對 **Next** 動作進行任何變更。

### <a name="run-and-test-the-app"></a>執行和測試應用程式

1. 執行應用程式。 您應該會在檢視中看到便利設施的完整集合。

2. 針對排序，選取 [By numerical Rating] \(依數值評分\) 會提供您已在此教學課程中實作的數值排序，當評等相同時則依整修日期排序。

![根據評分排序 "beach" (海灘)](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. 現在，請嘗試 [By Amenities] \(依便利設施\) 設定檔。 選取不同便利設施，然後驗證有這些便利設施的旅館在結果清單中獲得提升。

![根據設定檔排序 "beach"](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. 請嘗試 [By Renovated date/Rating profile] \(依整修日期/評分設定檔\)，以查看是否取得如預期的結果。 只有最近已整修的旅館會獲得 _freshness_ 提升。

### <a name="resources"></a>資源

如需詳細資訊，請參閱下列[將評分設定檔新增至 Azure 認知搜尋索引](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)。

## <a name="takeaways"></a>重要心得

請考慮此專案的下列重點：

* 使用者會希望搜尋結果已排序，最相關的先出現。
* 資料需要結構化才能更容易地排序。 我們無法輕鬆地依「最便宜」優先來排序，因為資料未結構化為不需要其他程式碼就能排序。
* 您可以依許多層級來排序，以區分在排序較高層級有相同值的結果。
* 某些結果適合依遞增順序排序 (例如，與定點的距離)，而某些適合遞減順序 (例如，客戶的評等)。
* 當資料集不適用 (或不夠聰明) 數值比較時，可以定義評分設定檔。 為每個結果評分有助於智慧地排序和顯示結果。

## <a name="next-steps"></a>後續步驟

您已完成這一系列的 C# 教學課程，您應該獲得了寶貴的 Azure 認知搜尋 API 知識。

如需進一步的參考和教學課程，您可以瀏覽 [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)，或 [Azure 認知搜尋文件](https://docs.microsoft.com/azure/search/)中的其他教學課程。
