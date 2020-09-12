---
title: Microsoft Azure 對應中 Azure 地圖服務路由服務的最佳作法
description: 瞭解如何使用 Microsoft Azure Maps 的路線服務來路由車輛。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b957453758b9b8e34989877516a9083f06a85ed8
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400770"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure 地圖服務路線規劃服務的最佳作法

您可以使用 Azure 地圖服務 [Route 服務](https://docs.microsoft.com/rest/api/maps/route) 中的路由方向和路由矩陣 api，來計算每個要求路由 (的預估抵達時間) 。 路由 Api 會考慮像是即時交通資訊和歷程記錄流量資料等因素，例如在一周的要求日和一天的時間內，一般的道路速度。 API 會根據時間或距離，以序列或最佳化順序，一次傳回多個目的地可行的最短或最快路線。 使用者也可以針對行人、自行車騎士和商用車輛（例如卡車）要求特製化路線和詳細資料。 在本文中，我們將分享用來呼叫 Azure 地圖服務 [Route Service](https://docs.microsoft.com/rest/api/maps/route)的最佳做法，而您將瞭解如何：

> [!div class="checklist"]
> * 選擇路由路線 Api 和矩陣路由 API
> * 根據即時和歷程的交通資料，要求歷程記錄和預測的行進時間
> * 要求路由詳細資料，例如時間和距離，適用于整個路由和路由的每個階段
> * 商業車輛的要求路線，例如貨車
> * 要求沿著路線的交通資訊，例如卡出和收費資訊
> * 要求包含一或多個停止 (導航點的路由) 
> * 將一或多個停止的路線優化，以取得每個 stop (waypoint 的最佳流覽順序) 
> * 使用支援點優化替代路由。 例如，提供通過電車輛充電站的替代路由。
> * 使用 Azure 地圖服務 Web SDK 的[路線規劃服務](https://docs.microsoft.com/rest/api/maps/route)

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。

如需路線規劃服務涵蓋範圍的詳細資訊，請參閱 [路由涵蓋範圍](routing-coverage.md)。

本文使用 [Postman 應用程式](https://www.postman.com/downloads/) 來建立 REST 呼叫，但您可以選擇任何 API 開發環境。

## <a name="choose-between-route-directions-and-matrix-routing"></a>選擇路由方向和矩陣路由

路線指示 Api 會傳回指示，包括移動時間和路線路徑的座標。 路由矩陣 API 可讓您計算來源和目的地位置所定義的一組路線的行進時間和距離。 矩陣 API 會針對每個給定的來源，計算從該來源到每個指定目的地之路由的 (行進時間和距離) 成本。 這些 Api 都可讓您指定參數，例如所需的起飛時間、抵達時間，以及車輛類型，例如汽車或貨車。 它們都會據以使用即時或預測流量資料，以傳回最適合的路由。

如果您的案例是：

* 要求兩個或多個已知位置之間的最短或最快的駕駛路線，以取得交付車輛的精確抵達時間。
* 要求詳細的路線指引，包括路由幾何，以視覺化方式呈現地圖上的路線
* 根據客戶位置的清單，計算出可造訪每個客戶位置並返回來源的最短可能路線。 此案例通常稱為「旅遊的推銷員」問題。 您最多可以傳遞150導航點 (在一個要求中停止) 。
* 只使用單一 API 呼叫，將查詢批次傳送至路由路線的批次 API。

如果您的案例是：

* 計算一組來源與目的地之間的行進時間或距離。 例如，您有12個驅動程式，而您需要尋找最接近的可用驅動程式來收取來自餐廳的食物傳遞。
* 依實際的旅遊距離或時間排序潛在路線。 矩陣 API 只會傳回每個來源和目的地組合的行進時間和距離。
* 以行進時間或距離為基礎的叢集資料。 例如，您的公司有50的員工，找出您辦公室在20分鐘記憶體留的所有員工。

以下是顯示路由方向和矩陣 Api 的一些功能的比較：

| Azure 地圖服務 API | 要求中的查詢數目上限 | 避免區域 | 貨車和電動車輛路線 | 導航點和旅遊的推銷員優化 | 支援點 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 取得路線指示 | 1 | | X | X | |
| 張貼路線路線 | 1 | X | X | X | X |
| 張貼路線指示批次 | 700 | | X | X | |
| Post 路線矩陣 | 700 | | X | | |

若要深入瞭解電子車輛路線的功能，請參閱我們的教學課程，瞭解如何 [使用 Azure Notebooks 搭配 Python 來路由傳送電動車輛](tutorial-ev-routing.md)。

## <a name="request-historic-and-real-time-data"></a>要求歷史和即時資料

根據預設，路線規劃服務會假設旅行模式是汽車，而起飛時間現在是。 除非路由計算要求另外指定，否則它會根據即時流量條件傳回路由。 已修正與時間相關的流量限制，例如「在 4:00 PM 到 6:00 PM 之間不允許使用左回合」，路由引擎將會考慮這些限制。 除非您特別要求的路由會忽略目前的即時流量，否則會考慮諸如 roadworks 等道路。 若要忽略目前的流量， `traffic` 請 `false` 在您的 API 要求中將設定為。

路由計算 **travelTimeInSeconds** 值包含流量所造成的延遲。 當起飛時間設定為 [now] 時，就會利用目前和歷史旅遊時間資料來產生它。 如果您的起飛時間是在未來設定，則 Api 會根據歷程記錄資料傳回預測的行進時間。

如果您在要求中包含 **computeTravelTimeFor = all** 參數，則回應中的摘要元素將會有下列額外的欄位，包括歷史流量條件：

| 項目 | 描述|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | 預估行進時間的計算方式，是因為流量狀況（例如因為擁塞）而不會延遲路由 |
| historicTrafficTravelTimeInSeconds | 使用時間相依的歷史流量資料計算的預估行進時間 |
| liveTrafficIncidentsTravelTimeInSeconds | 使用即時速度資料計算的預估行進時間 |

下一節將示範如何使用討論的參數呼叫路由 Api。

### <a name="sample-query"></a>範例查詢

在下一個範例中，在撰寫本文時，起飛時間會設定為未來。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

回應包含摘要元素，如下所示。 因為起飛時間設定為未來， **trafficDelayInSeconds** 值為零。 **TravelTimeInSeconds**值會使用與時間相關的歷程記錄流量資料來計算。 因此，在此情況下， **travelTimeInSeconds** 值等於 **historicTrafficTravelTimeInSeconds** 值。

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

在下列第二個範例中，我們有即時路由要求，其中的起飛時間現在是。 因為它是預設值，所以未在 URL 中明確指定。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

回應會包含摘要，如下所示。 由於壅塞， **trafficDelaysInSeconds** 值大於零。 它也大於 **historicTrafficTravelTimeInSeconds**。

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

## <a name="request-route-and-leg-details"></a>要求路由和階段詳細資料

根據預設，路線規劃服務會傳回座標陣列。 回應將會包含在名為的清單中組成路徑的座標 `points` 。 路由回應也包含從路由開始到預估經過時間的距離。 這些值可以用來計算整個路線的平均速度。

下圖顯示 `points` 元素。

![Points 元素](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

展開 `point` 元素以查看路徑的座標清單：

![展開的點元素](media/how-to-use-best-practices-for-routing/points-list-img.png)

路由路線 Api 支援不同的指令格式，可透過指定 **instructionsType** 參數來使用。 若要格式化便於進行電腦處理的指示，請使用 **instructionsType = 編碼**。 使用 **instructionsType = 標記** ，將指示顯示為使用者的文字。 此外，您可以將指令的格式設定為文字，其中會標示某些指示的元素，並以特殊格式呈現指示。 如需詳細資訊，請參閱 [支援的指令類型清單](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)。

當要求指令時，回應會傳回名為的新元素 `guidance` 。 `guidance`元素包含兩項資訊：輪流指示和摘要指示。

![指示類型](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

專案會 `instructions` 保留旅程的輪流方向，而且 `instructionGroups` 有摘要指示。 每個指令摘要都涵蓋可涵蓋多個道路的行程區段。 Api 可以傳回路由區段的詳細資料。 例如，流量堵塞的座標範圍或目前的流量速度。

![輪流指示](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![摘要指示](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>要求商業車輛的路線

Azure 地圖服務的路由 Api 支援商用車輛路線，涵蓋商用卡車路線。 Api 會考慮指定的限制。 例如，車輛的高度和權數，以及車輛是否攜帶危險貨物。 例如，如果車輛攜帶易燃，則路由引擎會避免在居住區域附近的特定通道。

### <a name="sample-query"></a>範例查詢

下列範例要求會查詢商用貨車的路線。 貨車攜帶了類別1的危險浪費教材。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

路由 API 會傳回可容納貨車尺寸和危險浪費的指示。 您可以藉由展開元素來讀取路由指示 `guidance` 。

![具有類別 1 hazwaste 的貨車](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>範例查詢

變更上述查詢中的 US Hazmat 類別，將會產生不同的路由來容納這項變更。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

以下的回應適用于攜帶類別9有害材質的貨車，相較于類別1的危險材料，其風險較低。 當您展開 `guidance` 元素來讀取指示時，您會發現方向不相同。 貨車有更多有關攜帶類別1危險材質的路線指示。



![具有類別9的貨車 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>沿著路由要求流量資訊

藉由 Azure 地圖服務路由方向 Api，開發人員可以在要求中包含參數，以要求每個區段類型的詳細資料 `sectionType` 。 例如，您可以要求每個交通卡出區段的速度資訊。 請參閱 [sectionType 索引鍵的值清單](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) ，以瞭解您可以要求的各種詳細資料。

### <a name="sample-query"></a>範例查詢

下列查詢會將設定 `sectionType` 為 `traffic` 。 它會要求包含從西雅圖到聖地牙哥的流量資訊的區段。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

回應包含適用于沿著指定座標之流量的區段。

![流量區段](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

此選項可用來在轉譯地圖時將區段上色，如下圖所示： 

![地圖上呈現的彩色區段](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>計算和優化多個停止路由

Azure 地圖服務目前提供兩種形式的路由優化：

* 根據要求的路由類型進行優化，而不會變更導航點順序。 您可以在 [這裡找到支援的路由類型](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* 旅遊的推銷員優化，這會變更導航點的順序，以取得每個停止的最佳流覽順序

若為多重停止路由，單一路由要求中最多可以指定150導航點。 開始和結束座標位置可以相同，就像往返的情況一樣。 但是您必須提供至少一個額外的 waypoint 來進行路由計算。 導航點可以在來源和目的地座標之間的查詢中加入。

如果您想要將最佳的順序優化以流覽指定的導航點，您必須指定 **computeBestOrder = true**。 這種情況也稱為「旅遊的推銷員優化」問題。

### <a name="sample-query"></a>範例查詢

下列查詢會要求六個導航點的路徑，並將 `computeBestOrder` 參數設定為 `false` 。 它也是參數的預設值 `computeBestOrder` 。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

回應會將路徑長度描述為140851計量，而且需要9991秒來移動該路徑。

![未優化的回應](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

下圖說明此查詢所產生的路徑。 此路徑是一個可能的路由。 它不是以時間或距離為基礎的最佳路徑。

![非優化影像](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



此路由 waypoint 順序為：0、1、2、3、4、5和6。

### <a name="sample-query"></a>範例查詢

下列查詢會要求相同六個導航點的路徑（如上述範例所示）。 這次， `computeBestOrder` 參數設定為 `true` (旅遊的推銷員優化) 。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

回應會將路徑長度描述為91814計量，而且需要7797秒來移動該路徑。 移動距離和行進時間都在此較低，因為 API 傳回優化的路線。

![優化的回應](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

下圖說明此查詢所產生的路徑。

![優化的影像](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

最佳路由具有下列 waypoint 順序：0、5、1、2、4、3和6。

>[!TIP]
> 來自路由服務的優化 waypoint 訂單資訊會提供一組索引。 這些會排除來源和目的地索引。 您必須將這些值遞增1，以作為來源的帳戶。 然後，將您的目的地新增至結尾，以取得完整的排序 waypoint 清單。

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>使用支援點計算和偏差替代路由

在某些情況下，您可能會想要重建路由，以計算參考路由的零或多個替代路由。 例如，您可能想要顯示通過零售商店的客戶替代路由。 在此情況下，您必須使用支援點來偏差位置。 以下是偏差位置的步驟：

1. 依原樣計算路由並取得路由回應的路徑
2. 您可以使用路由路徑，在路由路徑旁或附近尋找所需的位置。 例如，您可以使用 Azure 地圖服務 [點感興趣的 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) ，或在您的資料庫中查詢您自己的資料。  
3. 根據路線開頭的距離排序位置
4. 將這些位置新增至 [Post Route 指示 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)的新路由要求中的支援點。 若要深入瞭解支援點，請參閱 [Post 路線指示 API 檔](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)。 

在呼叫 [Post 路線指示 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)時，您可以設定最小偏差時間或距離條件約束，以及支援點。 如果您想要提供替代路由，但您也想要限制行進時間，請使用這些參數。 使用這些條件約束時，替代路由會在指定時間或距離的起點下，遵循參考路由。 換句話說，其他路由會根據指定的條件約束，與參考路由分離。

下圖是使用指定的時間和距離的偏差來呈現替代路由的範例。

![替代路由](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>在 web 應用程式中使用路由服務

Azure 地圖服務 Web SDK 提供 [服務模組](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest)。 此模組是協助程式程式庫，可讓您輕鬆地在 web 或 Node.js 應用程式中使用 JavaScript 或 TypeScript 來使用 Azure 地圖服務 REST Api。 服務模組可以用來在地圖上呈現傳回的路由。 模組會自動判斷要搭配 GET 和 POST 要求使用的 API。

## <a name="next-steps"></a>接下來的步驟

若要深入瞭解，請參閱：

> [!div class="nextstepaction"]
> [Azure 地圖服務路線規劃服務](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [如何使用服務模組](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [顯示地圖上的路線](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure 地圖服務 NPM 套件](https://www.npmjs.com/package/azure-maps-rest  )
