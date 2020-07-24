---
title: Azure 地圖服務路線規劃服務的最佳做法 |Microsoft Azure 對應
description: 瞭解如何使用來自 Microsoft Azure 地圖的路線規劃服務來路由車輛。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 79e9096030aada9fa368bb2e78af323139c0586c
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132206"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure 地圖服務路線規劃服務的最佳作法

Azure 地圖服務[路線規劃服務](https://docs.microsoft.com/rest/api/maps/route)中的路由方向和路線規劃矩陣 api 可用來計算每個所要求路由的預估抵達時間（eta）。 路由 Api 會考慮像是即時流量資訊和歷史流量資料之類的因素，例如在一周中所要求的一天和一天的一般道路速度。 Api 會根據時間或距離，以序列或優化的順序，一次傳回多個目的地可使用的最短或最快路由。 使用者也可以要求行人、自行車騎士和商業車輛（例如卡車）的特殊路線和詳細資料。 在本文中，我們將分享呼叫 Azure 地圖服務[路線規劃服務](https://docs.microsoft.com/rest/api/maps/route)的最佳作法，而您將瞭解如何：

* 選擇路由方向 Api 和矩陣路由 API
* 根據即時和歷史流量資料，要求歷史和預測的旅遊時間
* 針對整個路由和路線的每個階段要求路線詳細資料（例如時間和距離）
* 商業車輛的要求路線，例如卡車
* 沿著路線要求流量資訊，例如卡紙和收費資訊
* 要求包含一或多個停止的路由（之導航點）
* 將一或多個停止的路由優化，以取得最佳的流覽順序（導航點）
* 使用支援點優化替代路由。 例如，提供通過電動車輛充電站的替代路由。
* 使用 Azure 地圖服務 Web SDK 的[路線規劃服務](https://docs.microsoft.com/rest/api/maps/route)

## <a name="prerequisites"></a>必要條件

若要對 Azure 地圖服務 Api 進行呼叫，您需要 Azure 地圖服務帳戶和金鑰。 如需詳細資訊，請參閱[建立帳戶](quick-demo-map-app.md#create-an-azure-maps-account)和[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 主要金鑰也稱為「主要訂用帳戶金鑰」或「訂用帳戶金鑰」。

如需 Azure 地圖服務中驗證的相關資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。 如需有關路線規劃服務涵蓋範圍的詳細資訊，請參閱[路由涵蓋範圍](routing-coverage.md)。

本文使用[Postman 應用程式](https://www.postman.com/downloads/)來建立 REST 呼叫，但您可以選擇任何 API 開發環境。

## <a name="choose-between-route-directions-and-matrix-routing"></a>選擇路由方向和矩陣路由

路線指示 Api 會傳回指示，包括行進時間和路線路徑的座標。 路由矩陣 API 可讓您針對來源和目的地位置所定義的一組路由，計算其行進時間和距離。 針對每個指定的原點，矩陣 API 會計算從該原點路由到每個指定目的地的成本（行進時間和距離）。 所有這些 Api 都可讓您指定參數，例如所需的出發時間、抵達時間和車輛類型（如汽車或卡車）。 它們會據此使用即時或預測流量資料，以傳回最佳路由。

如果您的案例是，請考慮呼叫路線指示 Api：

* 要求兩個或多個已知位置之間的最短或最快駕駛路線，以取得傳遞車輛的精確抵達時間。
* 要求詳細的路線指導方針，包括路由幾何，以視覺化地圖上的路線
* 提供客戶位置的清單，計算可造訪每個客戶位置並返回原始來源的最短可能路線。 此案例通常稱為「旅遊推銷員」問題。 您最多可以在一個要求中傳遞150之導航點（停止）。
* 只使用單一 API 呼叫，將查詢的批次傳送至路由方向批次 API。

如果您的案例是要執行下列動作，請考慮呼叫矩陣路由 API：

* 計算一組來源與目的地之間的旅遊時間或距離。 例如，您有12個驅動程式，而且您需要尋找最接近的可用驅動程式，才能從餐廳中挑選食物傳遞。
* 依據實際的旅遊距離或時間來排序潛在路線。 矩陣 API 只會傳回每個原點和目的地組合的行進時間和距離。
* 以行進時間或距離為基礎的叢集資料。 例如，您的公司有50名員工，尋找您辦公室在20分鐘內居住的所有員工。

以下是顯示路由方向和矩陣 Api 之某些功能的比較：

| Azure 地圖服務 API | 要求中的查詢數目上限 | 避免區域 | 卡車和電動車輛路線 | 之導航點和旅遊的推銷員優化 | 支援點 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 取得路線指示 | 1 | | X | X | |
| 張貼路線指示 | 1 | X | X | X | X |
| Post 路線指示批次 | 700 | | X | X | |
| Post 路線矩陣 | 700 | | X | | |

若要深入瞭解電動車輛路由功能，請參閱我們的教學課程，瞭解如何[使用 Azure Notebooks 搭配 Python 來路由傳送電子車輛](tutorial-ev-routing.md)。

## <a name="request-historic-and-real-time-data"></a>要求歷史和即時資料

根據預設，路由服務會假設旅行模式是汽車，而出發時間現在是。 它會根據即時流量條件傳回路由，除非路由計算要求另有指定。 已修正與時間相依的流量限制，例如「不允許在 4:00 PM 到 6:00 PM 之間進行左轉轉」，而且路由引擎會將其視為。 除非您特別要求的路由會忽略目前的即時流量，否則將會考慮像是 roadworks 的道路。 若要忽略目前的流量， `traffic` 請 `false` 在您的 API 要求中將設定為。

[路由計算] **travelTimeInSeconds**值包含因流量而造成的延遲。 當出發時間設定為 [now] 時，會利用目前和歷程記錄的旅遊時間資料來產生它。 如果您的出發時間是在未來設定，則 Api 會根據歷程記錄資料傳回預測的行進時間。

如果您的要求中包含**computeTravelTimeFor = all**參數，則回應中的 summary 元素會有下列額外欄位，包括歷程記錄流量條件：

| 元素 | 描述|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | 估計的行進時間，如同因流量狀況而沒有延遲（例如因為擁塞） |
| historicTrafficTravelTimeInSeconds | 預估的旅遊時間是使用時間相依的歷程記錄流量資料所計算 |
| liveTrafficIncidentsTravelTimeInSeconds | 使用即時速度資料計算的預估行進時間 |

下一節將示範如何使用討論的參數對路由 Api 進行呼叫。

### <a name="sample-query"></a>範例查詢

在下面的第一個範例中，在撰寫時，出發時間會設定為未來。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

回應包含摘要元素，如下所示。 由於出發時間已設定為未來，因此**trafficDelayInSeconds**值為零。 **TravelTimeInSeconds**值是使用時間相依的歷史流量資料來計算。 因此，在此情況下， **travelTimeInSeconds**值等於**historicTrafficTravelTimeInSeconds**值。

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

在下面的第二個範例中，我們有即時路由要求，此時出發時間是。 因為它是預設值，所以不會在 URL 中明確指定。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

回應包含摘要，如下所示。 由於壅塞， **trafficDelaysInSeconds**值大於零。 它也大於**historicTrafficTravelTimeInSeconds**。

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

## <a name="request-route-and-leg-details"></a>要求路線和支線詳細資料

根據預設，路由服務會傳回座標陣列。 回應會包含組成清單中路徑的座標，名稱為 `points` 。 路由回應也包括從路由開始到預估經過時間的距離。 這些值可以用來計算整個路線的平均速度。

下圖顯示 `points` 元素。

<center>

![點清單](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

展開 `point` 元素以查看路徑的座標清單：

<center>

![點清單](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

路由方向 Api 支援不同的指令格式，可以藉由指定**instructionsType**參數來使用。 若要格式化簡單電腦處理的指示，請使用**instructionsType = 編碼**。 使用**instructionsType = 標記**為使用者顯示文字的指示。 此外，您也可以將指令格式化為文字，其中指示的某些元素會標示出來，而指令會以特殊格式呈現。 如需詳細資訊，請參閱[支援的指令類型清單](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)。

當要求指示時，回應會傳回名為的新元素 `guidance` 。 `guidance`元素包含兩項資訊：輪流指示和摘要指示。

<center>

![指示類型](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

`instructions`元素會保存旅程的輪流指示，而則 `instructionGroups` 包含摘要指示。 每個指令摘要都會涵蓋可能涵蓋多個道路的旅程區段。 Api 可以傳回路由區段的詳細資料。 例如，流量卡的座標範圍或流量的目前速度。

<center>

![輪流指示](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![摘要說明](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>申請商業車輛的路線

Azure 地圖服務的路由 Api 支援商用車輛路線，涵蓋商用卡車路線。 Api 會考慮指定的限制。 例如，車輛的高度和權數，以及車輛是否攜帶危險貨物。 例如，如果車輛攜帶易燃，則路由引擎會避免在居住區域附近的特定通道。

### <a name="sample-query"></a>範例查詢

下列範例要求會查詢商業卡車的路線。 卡車會攜帶類別1的危險浪費。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

路由 API 會傳回可容納卡車尺寸和危險浪費的指示。 您可以藉由展開元素來讀取路由指示 `guidance` 。

<center>

![類別 1 hazwaste 的卡車](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>範例查詢

變更上述查詢中的 US Hazmat 類別，將會產生不同的路由來容納這項變更。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

下面的回應適用于攜帶課堂9危險材料的卡車，其危險程度較于類別1的危險材質。 當您展開 `guidance` 元素以閱讀指示時，您會注意到方向並不相同。 有更多路線指示適用于攜帶類別1危險資料的卡車。

<center>

![課堂 9 hazwaste 的卡車](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>沿著路線要求流量資訊

藉由 Azure 地圖服務的路由方向 Api，開發人員可以在要求中包含參數，以要求每個區段類型的詳細資料 `sectionType` 。 例如，您可以要求每個交通卡紙區段的速度資訊。 如需瞭解您可以要求的各種詳細資料，請參閱[sectionType 索引鍵的值清單](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype)。

### <a name="sample-query"></a>範例查詢

下列查詢會將設 `sectionType` 為 `traffic` 。 它會要求包含來自西雅圖和聖地牙哥之流量資訊的區段。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

回應包含適用于沿著指定座標的流量的區段。

<center>

![流量區段](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

當轉譯對應時，可以使用這個選項來為區段上色，如下圖所示： 

<center>

![流量區段](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>計算並優化多個停止路由

Azure 地圖服務目前提供兩種形式的路由優化：

* 根據要求的路由類型進行優化，而不會變更之導航點的順序。 您可以在[這裡找到支援的路由類型](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* 出差的推銷員優化，這會變更之導航點的順序，以取得最佳的流覽順序

針對多個路由，可以在單一路由要求中指定最多150之導航點。 開始和結束座標位置可以相同，就像是來回行程的情況一樣。 但您至少需要提供一個額外的導航點，才能進行路線計算。 之導航點可以加入至來源與目的座標之間的查詢中。

如果您想要優化流覽給定之導航點的最佳順序，則必須指定**computeBestOrder = true**。 這種情況也稱為「旅遊推銷員優化」的問題。

### <a name="sample-query"></a>範例查詢

下列查詢會要求六個之導航點的路徑，並將 `computeBestOrder` 參數設定為 `false` 。 它也是參數的預設值 `computeBestOrder` 。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

回應會將路徑長度描述為140851計量，而且需要9991秒的時間來移動該路徑。

<center>

![未優化回應](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

下圖說明此查詢所產生的路徑。 此路徑是一個可能的路由。 這不是根據時間或距離的最佳路徑。

<center>

![非優化影像](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

此路由導航點順序為：0、1、2、3、4、5和6。

### <a name="sample-query"></a>範例查詢

下列查詢會要求相同六個之導航點的路徑，如上述範例所示。 這次， `computeBestOrder` 參數會設定為 `true` （旅遊的推銷員優化）。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

回應會將路徑長度描述為91814計量，而且需要7797秒的時間來移動該路徑。 這兩者的旅遊距離和行進時間都較低，因為 API 傳回了優化的路線。

<center>

![未優化回應](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

下圖說明此查詢所產生的路徑。

<center>

![非優化影像](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

最佳路由具有下列導航點順序：0、5、1、2、4、3和6。

>[!TIP]
> 來自路由服務的優化導航點訂單資訊會提供一組索引。 這些會排除來源和目的地索引。 您必須將這些值遞增1，以考慮來源。 然後，將目的地新增至結尾，以取得完整的已排序導航點清單。

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>使用支援點來計算和偏差替代路由

在某些情況下，您可能會想要重建路由來計算參考路由的零或多個替代路由。 例如，您可能會想要顯示通過零售商店的客戶替代路由。 在此情況下，您需要使用支援點來偏差位置。 以下是偏差某個位置的步驟：

1. 依情況計算路由，並從路由回應取得路徑
2. 使用路由路徑來尋找路由路徑中或附近的所需位置。 例如，您可以使用 Azure 地圖服務[點感興趣的 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) ，或在您的資料庫中查詢您自己的資料。  
3. 根據從路線起點開始的距離來排序位置
4. 將這些位置新增為[發佈路由方向 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)的新路由要求中的支援點。 若要深入瞭解支援點，請參閱[文章路由方向 API 檔](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)。 

呼叫[Post Route 路線指示 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)時，您可以設定最小偏差時間或距離條件約束，以及支援點。 如果您想要提供替代的路由，但也想要限制行進時間，請使用這些參數。 當使用這些條件約束時，替代路由會遵循給定時間或距離的來源參考路由。 換句話說，其他路由會根據指定的條件約束，從參照路由中分離出來。

下圖是以時間和距離的指定偏差限制呈現替代路由的範例。

<center>

![替代路由](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>在 web 應用程式中使用路由服務

Azure 地圖服務 Web SDK 提供[服務模組](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest)。 此模組是協助程式程式庫，可讓您使用 JavaScript 或 TypeScript，輕鬆地在 web 或 Node.js 應用程式中使用 Azure 地圖服務 REST Api。 服務模組可用來在地圖上呈現傳回的路由。 模組會自動判斷要搭配 GET 和 POST 要求使用哪個 API。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱：

> [!div class="nextstepaction"]
> [Azure 地圖服務路線規劃服務](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [如何使用服務模組](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [在地圖上顯示路線](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure 地圖服務 NPM 套件](https://www.npmjs.com/package/azure-maps-rest  )
