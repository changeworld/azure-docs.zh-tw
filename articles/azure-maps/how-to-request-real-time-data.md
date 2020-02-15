---
title: 要求即時傳輸資料 |Microsoft Azure 對應
description: 使用 Microsoft Azure Maps 行動服務來要求即時資料。
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9710366bdb7d8e86c8abb54b29b8dde3cc315692
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209897"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>使用 Azure 地圖服務行動服務來要求即時資料

本文說明如何使用 Azure 地圖服務[行動服務](https://aka.ms/AzureMapsMobilityService)來要求即時傳輸資料。

在本文中，您將了解如何：


 * 針對抵達給定停止的所有行要求下一次即時抵達
 * 要求指定自行車銜接站的即時資訊。


## <a name="prerequisites"></a>Prerequisites

您必須先擁有 Azure 地圖服務帳戶和訂用帳戶金鑰，才能對 Azure 地圖服務公用傳輸 Api 進行任何呼叫。 如需相關資訊，請遵循[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示來建立 Azure 地圖服務帳戶。 請依照[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟來取得您帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。


本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。


## <a name="request-real-time-arrivals-for-a-stop"></a>要求停止的即時抵達

為了要求特定公用傳輸的即時抵達資料停止，您必須向 Azure 地圖服務[行動服務](https://aka.ms/AzureMapsMobilityService)的[即時抵達 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)提出要求。 您將需要**metroID**和**stopID**才能完成要求。 若要深入瞭解如何要求這些參數，請參閱我們的指南，瞭解如何[要求公用傳輸路由](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。 

讓我們使用 "522" 作為我們的 metro 識別碼，也就是「西雅圖– Tacoma – Bellevue，WA」區域的 metro 識別碼。 使用 "522---2060603" 做為停止識別碼，此匯流排停止是在 "Ne 24 日 St & 162nd 中 Ne，Bellevue WA"。 若要要求接下來的五個即時抵達資料，在此停止所有下一次即時抵達時，請完成下列步驟：

1. 開啟 Postman 應用程式，讓我們建立集合來儲存要求。 在 Postman 應用程式頂端附近，選取 [**新增**]。 在 [**建立新**視窗] 中，選取 [**集合**]。  將集合命名為，然後選取 [**建立**] 按鈕。

2. 若要建立要求，請再次選取 [**新增**]。 在 [**建立新**視窗] 中，選取 [**要求**]。 輸入要求的 [**要求名稱**]。 選取您在上一個步驟中建立的集合，做為用來儲存要求的位置。 然後選取 [儲存]。

    ![在 Postman 中建立要求](./media/how-to-request-transit-data/postman-new.png)

3. 選取 [建立器] 索引標籤上的 [**取得**HTTP] 方法，然後輸入下列 URL 來建立 GET 要求。 使用您的 Azure 地圖服務主要金鑰取代 `{subscription-key}`。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 成功要求之後，您會收到下列回應。  請注意，參數 ' scheduleType ' 會定義預估抵達時間是否以即時或靜態資料為基礎。

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


## <a name="real-time-data-for-bike-docking-station"></a>自行車銜接站的即時資料

「[取得傳輸 Dock」資訊 API](https://aka.ms/AzureMapsMobilityTransitDock)可讓使用者要求靜態和即時資訊。 例如，使用者可以要求自行車或機車銜接站的可用性和空缺資訊。 [取得傳輸 Dock 資訊 API](https://aka.ms/AzureMapsMobilityTransitDock)也是 Azure 地圖服務[行動服務](https://aka.ms/AzureMapsMobilityService)的一部分。

為了向「[取得傳輸」 Dock 資訊 API](https://aka.ms/AzureMapsMobilityTransitDock)提出要求，您需要該工作站的**dockId** 。 您可以使用指派給 "bikeDock" 的**objectType**參數向「[取得附近傳輸 API](https://aka.ms/AzureMapsMobilityNearbyTransit) 」提出搜尋要求，以取得 dock 識別碼。 請遵循下列步驟，取得適用于自行車的銜接站的即時資料。


### <a name="get-dock-id"></a>取得停駐識別碼

若要取得**dockID**，請遵循下列步驟向「取得附近傳輸」 API 提出要求：

1. 在 Postman 中，按一下 **新增要求** | **取得要求**，並將它命名為 **取得 dock 識別碼**。

2.  在 [建立器] 索引標籤上，選取 [**取得**HTTP 方法]，輸入下列要求 URL，然後按一下 [**傳送**]。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 成功要求之後，您會收到下列回應。 請注意，在回應中現在會有**識別碼**，稍後可在取得傳輸 DOCK 資訊 API 的要求中，將其用作查詢參數。

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


### <a name="get-real-time-bike-dock-status"></a>取得即時自行車 dock 狀態

請遵循下列步驟向「取得傳輸 Dock」資訊 API 提出要求，以取得所選 Dock 的即時資料。

1. 在 Postman 中，按一下 **新增要求** | **取得要求**，並將其命名為**取得即時 dock 資料**。

2.  在 [建立器] 索引標籤上，選取 [**取得**HTTP 方法]，輸入下列要求 URL，然後按一下 [**傳送**]。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 成功要求之後，您會收到下列結構的回應：

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

瞭解如何使用行動服務要求傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

探索 Azure 地圖服務行動服務 API 檔：

> [!div class="nextstepaction"]
> [行動服務 API 檔](https://aka.ms/AzureMapsMobilityService)
