---
title: 要求即時公開傳輸資料 |Microsoft Azure 對應
description: 瞭解如何要求即時公開傳輸資料，例如傳輸停止時的抵達。 請參閱如何使用 Azure 地圖服務行動服務來實現此目的。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 71dc67d4c142f6fb84458cd6cd0b33452f2217b3
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037281"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>使用 Azure 地圖服務行動服務來要求即時公開傳輸資料

本文說明如何使用 Azure 地圖服務[行動服務](https://aka.ms/AzureMapsMobilityService)來要求即時公開傳輸資料。

在本文中，您將瞭解如何針對抵達給定停止的所有行要求下一個即時抵達

## <a name="prerequisites"></a>必要條件

您必須先擁有 Azure 地圖服務帳戶和訂用帳戶金鑰，才能對 Azure 地圖服務公用傳輸 Api 進行任何呼叫。 如需相關資訊，請遵循[建立帳戶](quick-demo-map-app.md#create-an-azure-maps-account)中的指示來建立 Azure 地圖服務帳戶。 請依照[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟來取得您帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。

## <a name="request-real-time-arrivals-for-a-stop"></a>要求停止的即時抵達

為了要求特定公用傳輸的即時抵達資料停止，您必須向 Azure 地圖服務[行動服務](https://aka.ms/AzureMapsMobilityService)的[即時抵達 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)提出要求。 您將需要**metroID**和**stopID**才能完成要求。 若要深入瞭解如何要求這些參數，請參閱我們的指南，瞭解如何[要求公用傳輸路由](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。

讓我們使用 "522" 作為我們的 metro 識別碼，也就是「西雅圖– Tacoma – Bellevue，WA」區域的 metro 識別碼。 使用 "522---2060603" 做為停止識別碼，此匯流排停止是在 "Ne 24 日 St & 162nd 中 Ne，Bellevue WA"。 若要要求接下來的五個即時抵達資料，在此停止所有下一次即時抵達時，請完成下列步驟：

1. 開啟 Postman 應用程式，讓我們建立集合來儲存要求。 在 Postman 應用程式頂端處，選取 [新增]。 在 [新建] 視窗中，選取 [集合]。  為集合命名，然後選取 [建立] 按鈕。

2. 若要建立要求，請再次選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入要求的 [要求名稱]。 選取您在上一個步驟中建立的集合，做為用來儲存要求的位置。 然後選取 [儲存]。

    ![在 Postman 中建立要求](./media/how-to-request-transit-data/postman-new.png)

3. 選取 [建立器] 索引標籤上的 [**取得**HTTP] 方法，然後輸入下列 URL 來建立 GET 要求。 `{subscription-key}`將取代為您 Azure 地圖服務的主要金鑰。

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

## <a name="next-steps"></a>後續步驟

瞭解如何使用行動服務要求傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

探索 Azure 地圖服務行動服務 API 檔：

> [!div class="nextstepaction"]
> [行動服務 API 檔](https://aka.ms/AzureMapsMobilityService)
