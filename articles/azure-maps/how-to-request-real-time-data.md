---
title: 請求即時傳輸資料 |微軟 Azure 地圖
description: 使用 Microsoft Azure 地圖移動服務請求即時資料。
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335473"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>使用 Azure 地圖移動服務請求即時資料

本文介紹如何使用 Azure 地圖[移動服務](https://aka.ms/AzureMapsMobilityService)請求即時傳輸資料。

在本文中，您將了解如何：


 * 請求下一個即時到達到達給定停靠點的所有線路
 * 請求給定自行車塢站的即時資訊。


## <a name="prerequisites"></a>Prerequisites

首先需要具有 Azure 地圖帳戶和訂閱金鑰，才能對 Azure 映射公共交通 API 進行任何調用。 有關詳細資訊，請按照[創建帳戶中的](quick-demo-map-app.md#create-an-account-with-azure-maps)說明創建 Azure 地圖帳戶。 按照[獲取主金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)的步驟獲取帳戶的主金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。


本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。


## <a name="request-real-time-arrivals-for-a-stop"></a>要求即時到達，以便停車

為了請求特定公共交通站的即時到達資料，您需要向 Azure 地圖[移動服務](https://aka.ms/AzureMapsMobilityService)[的即時到達 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)發出請求。 您需要**metroID**和**stopID**才能完成請求。 要瞭解有關如何請求這些參數的更多資訊，請參閱我們的指南，瞭解如何[請求公共交通路線](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。 

讓我們使用"522"作為我們的地鐵 ID，這是"華盛頓州西雅圖-塔科馬-貝爾維尤"地區的地鐵 ID。 使用"522---2060603"作為停車ID，此巴士站位於"Ne 24街&162大道內，貝爾維尤WA"。 要請求接下來的五個即時到達資料，對於在此停靠點的所有下一個即時到達者，請完成以下步驟：

1. 打開 Postman 應用，讓我們創建一個集合來存儲請求。 在郵遞員應用的頂部附近，選擇 **"新建**"。 在 **"創建新"** 視窗中，選擇 **"集合**"。  命名集合併選擇"**創建**"按鈕。

2. 要創建請求，請再次選擇 **"新建**"。 在 **"創建新"** 視窗中，選擇 **"請求**"。 輸入請求**的請求名稱**。 選擇您在上一步中創建的集合，作為保存請求的位置。 然後，選擇 **"保存**"。

    ![在郵遞員中創建請求](./media/how-to-request-transit-data/postman-new.png)

3. 選擇產生器選項卡上的**GET** HTTP 方法，然後輸入以下 URL 以創建 GET 請求。 使用`{subscription-key}`Azure 映射主鍵替換 。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 成功請求後，您將收到以下回應。  請注意，參數"計畫類型"定義估計到達時間是基於即時資料還是靜態資料。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>自行車塢站的即時資料

[獲取傳輸停靠資訊 API](https://aka.ms/AzureMapsMobilityTransitDock)允許使用者請求靜態和即時資訊。 例如，使用者可以請求自行車或滑板車塢站的可用性和空置資訊。 [獲取傳輸停靠站資訊 API](https://aka.ms/AzureMapsMobilityTransitDock)也是 Azure 地圖[移動服務的](https://aka.ms/AzureMapsMobilityService)一部分。

為了向[獲取傳輸停靠站資訊 API](https://aka.ms/AzureMapsMobilityTransitDock)發出請求，您需要該網站的**dockId。** 您可以通過向["獲取附近的傳輸 API"](https://aka.ms/AzureMapsMobilityNearbyTransit)發出搜索請求，將**物件類型**參數分配給"bikeDock"來獲取銜接站 ID。 按照以下步驟獲取自行車塢的即時資料。


### <a name="get-dock-id"></a>獲取停靠 ID

要獲取**dockID，** 請按照以下步驟向"獲取附近的傳輸 API"發出請求：

1. 在郵遞員中，按一下 **"新請求** | **GET"請求**並將其命名為 **"獲取停靠 ID**"。

2.  在"產生器"選項卡上，選擇**GET** HTTP 方法，輸入以下請求 URL，然後按一下"**發送**"。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 成功請求後，您將收到以下回應。 請注意，我們現在在回應中具有**ID，** 稍後可以在獲取傳輸停靠資訊 API 的請求中用作查詢參數。

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>獲取即時自行車停靠站狀態

按照以下步驟向獲取傳輸停靠站資訊 API 發出請求，以獲得所選銜接站的即時資料。

1. 在郵遞員中，按一下 **"新請求** | **GET 請求**"並命名它**獲取即時停靠資料**。

2.  在"產生器"選項卡上，選擇**GET** HTTP 方法，輸入以下請求 URL，然後按一下"**發送**"。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 請求成功後，您將收到以下結構的回應：

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>後續步驟

瞭解如何使用移動服務請求傳輸資料：

> [!div class="nextstepaction"]
> [如何請求傳輸資料](how-to-request-transit-data.md)

流覽 Azure 地圖移動服務 API 文檔：

> [!div class="nextstepaction"]
> [移動服務 API 文檔](https://aka.ms/AzureMapsMobilityService)
