---
title: 請求即時公共交通數據 |微軟 Azure 地圖
description: 使用 Microsoft Azure 地圖行動服務請求即時公共交通數據。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086072"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>使用 Azure 地圖行動服務要求即時公共交通資料

本文介紹如何使用 Azure 地圖[行動服務](https://aka.ms/AzureMapsMobilityService)請求即時公共交通數據。

在本文中,您將瞭解如何請求下一個即時到達到達給定停靠點的所有線路

## <a name="prerequisites"></a>Prerequisites

首先需要具有 Azure 地圖帳戶和訂閱密鑰,才能對 Azure 映射公共交通 API 進行任何調用。 有關詳細資訊,請按照[創建帳戶中的](quick-demo-map-app.md#create-an-account-with-azure-maps)說明創建 Azure 地圖帳戶。 按照[獲取主金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)的步驟獲取帳戶的主密鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。

## <a name="request-real-time-arrivals-for-a-stop"></a>要求即時到達,以便停車

為了請求特定公共交通站的即時到達數據,您需要向 Azure 地圖[行動服務](https://aka.ms/AzureMapsMobilityService)[的即時到達 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)發出請求。 您需要**metroID**和**stopID**才能完成請求。 要瞭解有關如何請求這些參數的更多資訊,請參閱我們的指南,瞭解如何[請求公共交通路線](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。

讓我們使用"522"作為我們的地鐵 ID,這是"華盛頓州西雅圖-塔科馬-貝爾維尤"地區的地鐵 ID。 使用「522---2060603」作為停車ID,此巴士站位於「Ne 24街&162大道內,貝爾維尤WA」。。 要請求接下來的五個即時到達數據,對於在此停靠點的所有下一個即時到達者,請完成以下步驟:

1. 打開 Postman 應用,讓我們創建一個集合來存儲請求。 在郵遞員應用的頂部附近,選擇 **"新建**"。 在 **"創建新'** 視窗中,選擇 **"集合**"。  命名集合並選擇「**建立**」 按鈕。

2. 要建立請求,請再次選擇 **"新建**"。 在 **"創建新'** 視窗中,選擇 **"請求**"。 輸入要求**的要求名稱**。 選擇您在上一步中創建的集合,作為保存請求的位置。 然後,選擇 **"保存**"。

    ![在郵遞員中建立要求](./media/how-to-request-transit-data/postman-new.png)

3. 選擇生成器選項卡上的**GET** HTTP 方法,然後輸入以下 URL 以建立 GET 請求。 使用`{subscription-key}`Azure 對應主鍵取代 。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 成功請求后,您將收到以下回應。  請注意,參數「計劃類型」定義估計到達時間是基於即時數據還是靜態數據。

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

瞭解如何使用行動服務請求傳輸資料:

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

瀏覽 Azure 地圖行動服務 API 文件:

> [!div class="nextstepaction"]
> [移動服務 API 文件](https://aka.ms/AzureMapsMobilityService)
