---
title: 教學課程：使用 Azure Notebooks (Python) 規劃電動車的路線 | Microsoft Azure 地圖服務
description: 使用 Microsoft Azure 地圖服務路線規劃 API 和 Azure Notebooks 來規劃電動車的路線。
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3118ca39ec0efd42c9f7b622c91f857034ef4b03
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333825"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>教學課程：使用 Azure Notebooks (Python) 規劃電動車的路線

Azure 地圖服務是以原生方式整合到 Azure 的地理空間服務 API 組合。 這些 API 可讓開發人員、企業及 ISV 開發定位感知的應用程式、IoT、行動裝置、物流及資產追蹤解決方案。 

您可以從 Python 和 R 等語言呼叫 Azure 地圖服務 REST API，以啟用地理空間資料分析和機器學習案例。 Azure 地圖服務提供一組健全的[路線規劃 API](https://docs.microsoft.com/rest/api/maps/route) \(英文\)，讓使用者能夠計算數個資料點之間的路線。 計算會以各種條件為依據，例如，車輛類型或可抵達的區域。 

在本教學課程中，您將逐步協助電動車電量偏低的駕駛者。 駕駛者需要找到與車輛所在位置距離最近的充電站。

在本教學課程中，您將：

> [!div class="checklist"]
> * 在雲端的 [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) \(部分機器翻譯\) 上建立並執行 Jupyter 筆記本。
> * 在 Python 中呼叫 Azure 地圖服務 REST API。
> * 根據電動車的耗電量模型搜尋可達範圍。
> * 搜尋位於可抵達範圍或等時線內的電動車充電站。
> * 在地圖上轉譯可達範圍界限和充電站。
> * 根據行車時間尋找前往最近電動車充電站的路線，並將該路線視覺化。


## <a name="prerequisites"></a>Prerequisites 

若要完成此教學課程，您必須先建立 Azure 地圖服務帳戶，並取得主要金鑰 (訂用帳戶金鑰)。 

若要建立 Azure 地圖服務帳戶訂用帳戶，請依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示操作。 您必須要有採用 S1 定價層的 Azure 地圖服務帳戶訂用帳戶。 

若要取得帳戶的主要訂用帳戶金鑰，請依照[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的指示操作。

如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

## <a name="create-an-azure-notebook"></a>建立 Azure 筆記本

若要遵循此教學課程，您必須建立 Azure 筆記本專案，並下載及執行 Jupyter 筆記本檔案。 此 Notebook 檔案包含 Python 程式碼，可實作本教學課程中的案例。 若要建立 Azure Notebook 專案，並將 Jupyter Notebook 文件上傳到其中，請執行下列步驟：

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 如需詳細資訊，請參閱[快速入門：登入並設定使用者識別碼](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)。
1. 在您的公用設定檔頁面上，選取 [我的專案]  。

    ![[我的專案] 按鈕](./media/tutorial-ev-routing/myproject.png)

1. 在 [我的專案]  頁面上，選取 [新增專案]  。
 
   ![[新增專案] 按鈕](./media/tutorial-ev-routing/create-project.png)

1. 在 [建立新專案]  頁面中，輸入專案名稱和專案識別碼。
 
    ![[建立新專案] 窗格](./media/tutorial-ev-routing/create-project-window.png)

1. 選取 [建立]  。

1. 專案建立後，請從 [Azure 地圖服務 Jupyter Notebook 存放庫](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)下載 [Jupyter Notebook 文件檔案](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb)。

1. 在 [我的專案]  頁面上的專案清單中選取您的專案，然後選取 [上傳]  以上傳 Jupyter 筆記本文件檔案。 

    ![上傳 Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. 從您的電腦上傳檔案，然後選取 [完成]  。

1. 成功完成上傳之後，您的檔案就會顯示在專案頁面上。 按兩下該檔案，使其以 Jupyter Notebook 的形式開啟。

請嘗試了解在 Notebook 檔案中執行的功能。 在 Notebook 檔案中執行程式碼，一次執行一個儲存格。 您可以選取筆記本應用程式頂端的 [執行]  按鈕，以執行每個資料格中的程式碼。

  ![[執行] 按鈕](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>安裝專案層級套件

若要在 Notebook 中執行程式碼，請執行下列步驟，以在專案層級安裝套件：

1. 從 [Azure 地圖服務 Jupyter 筆記本存放庫](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) \(英文\) 下載 [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) 檔案，然後將其上傳至您的專案。
1. 在專案儀表板上，選取 [專案設定]  。 
1. 在 [專案設定]  窗格中，選取 [環境]  索引標籤，然後選取 [新增]  。
1. 在 [環境設定步驟]  下方，執行下列動作：   
    a. 在第一個下拉式清單中，選取 [Requirements.txt]  。  
    b. 在第二個下拉式清單中，選取您的 *Requirements.txt* 檔案。  
    c. 在第三個下拉式清單中，選取 [Python 3.6 版]  作為您的版本。
1. 選取 [儲存]  。

    ![安裝套件](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>載入必要的模組和架構

若要載入所有必要的模組和架構，請執行下列指令碼。

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>要求可抵達的範圍界限

某家快遞公司的車隊編制了一些電動車。 在一天之中，電動車必須能夠在外充電而不需返回倉庫。 每當剩餘電量低於一小時，您就要搜尋一組位於可抵達範圍內的充電站。 基本上，當電量偏低時，您就會搜尋充電站。 而且，您也會取得該充電站範圍的界限資訊。 

由於公司偏好使用兼顧經濟效益和速度的路線，因此要求的 routeType 為 *eco*。 下列指令碼會呼叫 Azure 地圖服務規劃路線服務的[取得路線範圍 API](https://docs.microsoft.com/rest/api/maps/route/getrouterange)。 此指令碼會在車輛的耗電量模型中使用參數。 指令碼接著會剖析回應以建立 geojson 格式的多邊形物件，其代表該輛車可抵達的最大範圍。

若要判斷電動車可抵達的範圍界限，請執行下列資料格中的指令碼：

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>在可抵達的範圍內搜尋電動車充電站

當您決定了電動車可抵達的範圍 (等時線) 之後，就可以在該範圍內搜尋充電站。 

下列指令碼會呼叫 Azure 地圖服務 [Post 幾何內搜尋 API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) \(英文\)。 此 API 會在汽車的最大可到達範圍界限內搜尋電動車輛的充電站。 然後，指令碼會將回應剖析為可存取的位置陣列。

若要在可抵達的範圍內搜尋電動車充電站，請執行下列指令碼：

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>將可抵達的範圍和充電地點上傳至 Azure 地圖服務資料服務

在地圖上，您可以將充電站和電動車可抵達的最大範圍界限視覺化。 若要這麼做，請將界限資料和充電站以 geojson 物件的形式上傳至 Azure 地圖服務資料服務。 使用[資料上傳 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)。 

若要將界限和充電地點資料上傳至 Azure 地圖服務資料服務，請執行下列兩個資料格：

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data Service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data Service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>在地圖上轉譯充電站和可抵達的範圍

將資料上傳至資料服務後，請呼叫 Azure 地圖服務的[取得地圖影像服務](https://docs.microsoft.com/rest/api/maps/render/getmapimage)。 此服務可藉由執行下列指令碼，用來在靜態地圖影像上轉譯充電地點和可抵達的最大界限：

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![顯示位置範圍的地圖](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>尋找最適合前往的充電站

首先，您想要判斷可抵達範圍內的所有充電站。 然後，您想要知道其中哪個充電站可在最短的時間內到達。 

下列指令碼會呼叫 Azure 地圖服務[矩陣路線規劃 API](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)。 該指令碼會傳回指定車輛位置前往每個充電站的行車時間和距離。 下一個資料格中的指令碼會剖析回應，以便依時間找到可抵達的最近充電站所在位置。

若要找出可在最短時間內抵達的最近可抵達充電站，請執行下列資料格中的指令碼：

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>計算最近充電站的路線

現在您已經找到最近的充電站，接下來可呼叫[取得路線指示 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) \(英文\)，以要求從電動車目前所在位置前往該充電站的詳細路線。

若要取得前往該充電站的路線，並剖析回應以建立代表路線的 geojson 物件，請執行下列資料格中的指令碼：

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>將路線視覺化

若要協助將路線視覺化，您首先要將路線資料 geojson 物件的形式上傳至 Azure 地圖服務資料服務。 若要這麼做，請使用 Azure 地圖服務[資料上傳 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)。 接著，請呼叫轉譯服務[取得地圖影像 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage)，以在地圖上轉譯該路線，並將其視覺化。

若要取得地圖上所轉譯路線的影像，請執行下列指令碼：

```python
# Upload the route data to Azure Maps Data Service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![顯示路線的地圖](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何直接呼叫 Azure 地圖服務 REST API，並使用 Python 來將 Azure 地圖服務資料視覺化。

若要探索此教學課程中所使用的 Azure 地圖服務 API，請參閱：

* [取得路線範圍](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Post 幾何內搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [資料上傳](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [轉譯 - 取得地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Post 路線矩陣](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [取得路線指示](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

如需 Azure 地圖服務 REST API 的完整清單，請參閱 [Azure 地圖服務 REST API](https://docs.microsoft.com/azure/azure-maps/consumption-model) \(部分機器翻譯\)。

若要深入了解 Azure Notebooks，請參閱 [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) \(部分機器翻譯\)。
