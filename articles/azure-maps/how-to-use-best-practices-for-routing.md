---
title: Azure 地圖路由服務的最佳做法 |微軟 Azure 地圖
description: 瞭解如何使用 Microsoft Azure 地圖中的路由服務高效地路由。
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335408"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure 地圖路由服務的最佳做法

Azure 地圖[路徑服務](https://docs.microsoft.com/rest/api/maps/route)中的路由方向和路徑矩陣 API 可用於計算每個請求的路由的估計到達時間 （ETA）。 路線 API 會考慮即時交通資訊和歷史交通資料等因素，例如一周中請求的一天和一天中請求的典型道路速度。 API 根據時間或距離，根據時間或距離，根據時間或距離，根據時間或距離，根據時間或距離，根據時間或距離，一次或按優化順序返回可用於多個目標的最短或最快的路由。 使用者還可以為步行者、騎自行車者和卡車等商用車輛請求專用路線和詳細資訊。 在本文中，我們將分享調用 Azure 地圖[路由服務的](https://docs.microsoft.com/rest/api/maps/route)最佳做法，並學習如何操作：

* 在路由方向 API 和矩陣路由 API 之間進行選擇
* 根據即時和歷史流量資料請求歷史和預測旅行時間
* 請求整個路線和路線各段的路線詳細資訊（如時間和距離）
* 請求商用車輛的路線，如卡車
* 沿路線請求交通資訊，如擁堵和通行費資訊
* 請求由一個或多個停靠點（航點）組成的路線
* 優化一個或多個停靠點的路徑，以獲得訪問每個停靠點的最佳順序（航點）
* 使用支援點優化其他路線。 例如，提供通過電動汽車充電站的替代路線。
* 將[路由服務](https://docs.microsoft.com/rest/api/maps/route)與 Azure 地圖 Web SDK 一起使用

## <a name="prerequisites"></a>Prerequisites

要調用 Azure 地圖 API，需要 Azure 地圖帳戶和金鑰。 有關詳細資訊，請參閱[創建帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)並[獲取主金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 主金鑰也稱為主訂閱金鑰或訂閱金鑰。

有關 Azure 映射中的身份驗證的資訊，請參閱[在 Azure 映射中管理身份驗證](./how-to-manage-authentication.md)。 有關路由服務覆蓋範圍的詳細資訊，請參閱[路由覆蓋範圍](routing-coverage.md)。

本文使用[Postman 應用](https://www.postman.com/downloads/)生成 REST 調用，但您可以選擇任何 API 開發環境。

## <a name="choose-between-route-directions-and-matrix-routing"></a>在路線方向和矩陣路由之間進行選擇

路徑方向 API 返回說明，包括行駛時間和路徑路徑的座標。 路由矩陣 API 允許您計算由起點和目標位置定義的一組路由的行駛時間和距離。 對於每個給定的源，矩陣 API 計算從該原點到每個給定目的地的路由的成本（行駛時間和距離）。 所有這些 API 都允許您指定參數，如所需的出發時間、到達時間和車輛類型（如汽車或卡車）。 它們都相應地使用即時或預測流量資料來返回最佳路線。

如果您的方案是：

* 請求兩個或兩個以上已知地點之間的最短或最快的行駛路線，以獲得送貨車輛的精確到達時間。
* 請求詳細的路線指南（包括路徑幾何體）以視覺化地圖上的路徑
* 給定客戶位置清單，計算訪問每個客戶位置並返回原點的最短路線。 此方案通常稱為旅行推銷員問題。 在一個請求中，您最多可以傳遞 150 個航點（停靠點）。
* 只需使用單個 API 呼叫即可將批次處理查詢發送到路由方向批次處理 API。

如果方案是：

* 計算一組起點和目的地之間的行駛時間或距離。 例如，您有 12 名司機，您需要找到最接近的可用司機來從餐廳領取食物。
* 按潛在路線的實際行駛距離或時間進行排序。 矩陣 API 僅返回每個起點和目的地組合的行程時間和距離。
* 基於行駛時間或距離的群集資料。 例如，您的公司有 50 名員工，請查找所有員工，這些員工在 20 分鐘車程內從您的辦公室找到。

下面是顯示路由方向和矩陣 API 的一些功能的比較：

| Azure 地圖 API | 請求中查詢的最大數量 | 避開區域 | 卡車和電動汽車路線 | 航點和旅行推銷員優化 | 支援點 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 取得路線指示 | 1 | | X | X | |
| 發佈路線方向 | 1 | X | X | X | X |
| 發佈路線方向批次 | 700 | | X | X | |
| Post 路線矩陣 | 700 | | X | | |

要瞭解有關電動汽車路由功能的更多資訊，請參閱有關如何使用[帶有 Python 的 Azure 筆記本路由電動汽車的](tutorial-ev-routing.md)教程。

## <a name="request-historic-and-real-time-data"></a>請求歷史和即時資料

預設情況下，路線服務假定行駛模式為汽車，且起飛時間為現在。 它基於即時交通狀況返回路由，除非路由計算請求另有指定。 固定時間相關的流量限制（如"下午 4：00 到 6：00 之間不允許左轉"）將被捕獲，路由引擎將考慮這些限制。 道路封閉，如道路工程，將考慮，除非你特別要求的路線，忽略目前的現場交通。 要忽略當前流量，請在`traffic`API`false`請求中設置為。

路徑計算**行程時間時間秒**值包括由於流量而導致的延遲。 它是通過利用當前和歷史旅行時間資料生成的，此時出發時間設置為現在。 如果出發時間設置為將來，API 會根據歷史資料返回預測旅行時間。

如果在請求中包含**計算 TravelTimeFor_all**參數，則回應中的摘要元素將具有以下其他欄位，包括歷史流量條件：

| 元素 | 描述|
| :--- | :--- |
| 無交通旅行時間以秒為單位 | 估計行駛時間計算，例如，由於交通狀況，例如，由於擁堵，路線上沒有延誤 |
| 歷史交通旅行時間以秒為單位 | 使用時間相關的歷史流量資料計算的估計行駛時間 |
| 即時交通事件旅行時間以秒為單位 | 使用即時速度資料計算的估計行駛時間 |

以下各節演示如何使用討論的參數對路由 API 進行調用。

### <a name="sample-query"></a>範例查詢

在下面的第一個示例中，出發時間設置為將來，在編寫本文時。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

回應包含摘要元素，如下所示。 由於出發時間設置為將來，**因此流量延遲秒**值為零。 使用時間相關的歷史流量資料計算**行駛時間（以秒為單位**）值。 因此，在這種情況下，**行駛時間秒**值等於**歷史流量旅行時間秒**值。

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>範例查詢

在下面的第二個示例中，我們有一個即時路由請求，此時的出發時間是現在。 URL 中未顯式指定它，因為它是預設值。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

回應包含摘要，如下所示。 由於擁塞，**流量延遲秒**值大於零。 它也比**歷史性的交通時間在秒**。

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>請求路線和腿部詳細資訊

預設情況下，路由服務將返回座標陣列。 回應將包含組成路徑的座標在名為`points`的清單中。 路由回應還包括從路徑開始的距離和估計的經過時間。 這些值可用於計算整個路徑的平均速度。

下圖顯示了元素`points`。

<center>

![點清單](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

展開`point`元素以查看路徑的座標清單：

<center>

![點清單](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

路由方向 API 支援不同的指令格式，可以通過指定**指令類型**參數來使用。 要格式化說明以方便電腦處理，請使用**說明類型_編碼**。 使用 **"類型"標記的說明**將說明顯示為使用者的文本。 此外，指令可以格式化為文本，其中指令的某些元素被標記，並且說明顯示的特殊格式。 有關詳細資訊，請參閱[受支援的說明類型清單](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)。

請求指令時，回應將返回名為`guidance`的新元素。 該`guidance`元素包含兩條資訊：逐圈方向和匯總說明。

<center>

![說明類型](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

元素`instructions`保存行程的逐圈方向，並且已`instructionGroups`匯總說明。 每個說明摘要都涵蓋了行程的一部分，可以覆蓋多條道路。 API 可以返回路由部分的詳細資訊。 例如，交通堵塞的座標範圍或當前交通速度。

<center>

![按轉彎說明](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![總結說明](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>請求商用車輛的路線

Azure 地圖路由 API 支援商用車輛路由，涵蓋商用卡車路由。 API 考慮指定的限制。 如車輛的高度和重量，以及車輛是否攜帶危險貨物。 例如，如果車輛攜帶易燃物品，則路由引擎會避開住宅區附近的某些隧道。

### <a name="sample-query"></a>範例查詢

下面的示例請求查詢了商用卡車的路線。 卡車裝載的是1類危險廢物。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

路線 API 返回適合卡車尺寸和危險廢物的方向。 您可以通過展開`guidance`元素來讀取路由說明。

<center>

![卡車與1類垃圾](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>範例查詢

從上述查詢更改美國 Hazmat 類將導致不同的路由以適應此更改。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

以下回應適用于裝載 9 類危險材料的卡車，該危險品比 1 類危險材料危險度低。 當您展開元素`guidance`以讀取方向時，您會注意到方向不同。 對於裝載 1 類危險材料的卡車，還有更多的路線說明。

<center>

![卡車與9級垃圾](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>沿路線請求流量資訊

使用 Azure 地圖路由方向 API，開發人員可以通過在請求中包含`sectionType`參數來請求每個節類型的詳細資訊。 例如，您可以請求每個交通堵塞段的速度資訊。 請參閱[節類型鍵的值清單](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype)，以瞭解您可以請求的各種詳細資訊。

### <a name="sample-query"></a>範例查詢

以下查詢將 集`sectionType``traffic`到 。 它請求包含從西雅圖到聖地牙哥的交通資訊的分區。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

回應包含適合沿給定座標的流量的節。

<center>

![交通部分](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

此選項可用於在渲染地圖時對各節著色，如下圖所示： 

<center>

![交通部分](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>計算和優化多站路徑

Azure 地圖當前提供兩種形式的路由優化：

* 基於請求的路由類型的優化，而不更改航點的順序。 您可以[在此處找到支援的路由類型](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* 旅行推銷員優化，它改變航點的順序，以獲得訪問每個停靠點的最佳順序

對於多站路由，在單個路由請求中最多可以指定 150 個航點。 開始和結束座標位置可以相同，往返位置也是如此。 但是，您需要提供至少一個額外的航點來計算路徑。 航點可以添加到原點座標和目標座標之間的查詢中。

如果要優化訪問給定航點的最佳順序，則需要指定**計算BestOrder_true**。 此方案也稱為旅行推銷員優化問題。

### <a name="sample-query"></a>範例查詢

以下查詢請求六個航點的路徑，`computeBestOrder`參數設置為`false`。 它也是參數的`computeBestOrder`預設值。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

回應描述路徑長度為 140，851 米，並且需要 9，991 秒才能行駛該路徑。

<center>

![非優化回應](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

下圖說明瞭此查詢產生的路徑。 此路徑是一條可能的路線。 它不是基於時間或距離的最佳路徑。

<center>

![非優化圖像](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

此路線航點順序為：0、1、2、3、4、5 和 6。

### <a name="sample-query"></a>範例查詢

以下查詢請求路徑為相同的六個航點，如上例所示。 這一次，`computeBestOrder`參數設置為`true`（旅行推銷員優化）。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

回應描述路徑長度為 91，814 米，並且需要 7，797 秒才能行駛該路徑。 此處的行駛距離和行駛時間都較低，因為 API 返回了優化的路線。

<center>

![非優化回應](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

下圖說明瞭此查詢產生的路徑。

<center>

![非優化圖像](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

最佳路線的順序如下：0、5、1、2、4、3 和 6。

>[!TIP]
> 路由服務的優化航點訂單資訊提供一組索引。 這些不包括原點和目標索引。 您需要將這些值遞增 1 來考慮原點。 然後，將目標添加到末尾，以獲得完整的有序航點清單。

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>使用支撐點計算和偏置替代路徑

您可能有這樣的情況是，要重建路由以計算參考路由的零個或多個替代路由。 例如，您可能希望向客戶顯示通過零售商店的替代路由。 在這種情況下，您需要使用支撐點偏置位置。 以下是偏置位置的步驟：

1. 按"按"計算路徑並從路由回應獲取路徑
2. 使用路徑查找路徑沿線或附近的所需位置。 例如，可以使用 Azure 地圖[興趣點 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)或在資料庫中查詢自己的資料。  
3. 根據路線起點的距離對位置進行排序
4. 將這些位置作為支援點添加到新的路由請求到[後路由方向 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)。 要瞭解有關支援點的更多資訊，請參閱[後路線方向 API 文檔](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)。 

調用[後路由方向 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)時，可以設置最小偏差時間或距離約束以及支撐點。 如果要提供替代路線，但還要限制行駛時間，請使用這些參數。 使用這些約束時，替代路由將遵循給定時間或距離的原點中的參考路線。 換句話說，其他路由與給定約束的參考路由不同。

下圖是渲染具有指定偏差限制的時間和距離的替代路徑的示例。

<center>

![替代路線](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>在 Web 應用中使用路由服務

Azure 映射 Web SDK 提供[服務模組](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest)。 此模組是一個説明庫，使用 JavaScript 或 TypeScript，可以輕鬆地在 Web 或 Node.js 應用程式中使用 Azure 映射 REST API。 服務模組可用於在地圖上呈現返回的路由。 模組會自動確定與 GET 和 POST 請求一起使用的 API。

## <a name="next-steps"></a>後續步驟

要瞭解更多資訊，請參閱：

> [!div class="nextstepaction"]
> [Azure 地圖路由服務](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [如何使用服務模組](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [在地圖上顯示路線](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure 映射 NPM 包](https://www.npmjs.com/package/azure-maps-rest  )
