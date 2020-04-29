---
title: 使用事件方格來回應對應事件 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用事件方格來回應 Microsoft Azure 對應事件。
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335724"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>使用事件格線回應 Azure 地圖服務事件 

Azure 地圖服務與 Azure 事件方格整合，讓使用者可以將事件通知傳送給其他服務並觸發下游進程。 本文的目的是要協助您設定商務應用程式，以接聽 Azure 地圖服務的事件。 這可讓使用者以可靠、可擴充且安全的方式回應重大事件。 例如，使用者可以建立應用程式來更新資料庫、建立票證，以及在每次裝置進入地理柵欄時傳遞電子郵件通知。

Azure 事件方格是完全受控的事件路由服務，它會使用發佈-訂閱模型。 事件方格內建對 Azure 服務的支援，例如[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)和[Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)。 它可以使用 webhook 將事件警示傳遞至非 Azure 服務。 如需事件方格所支援的事件處理常式完整清單，請參閱 [Azure 事件方格簡介](https://docs.microsoft.com/azure/event-grid/overview)。


![Azure 事件格線運作模型](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure 地圖服務事件類型

Event Grid 使用[事件訂閱](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)將事件訊息路由至訂閱者。 Azure 地圖服務帳戶會發出下列事件類型： 

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 當接收的座標已從指定地理柵欄的外部移至內時引發 |
| Microsoft.Maps.GeofenceExited | 當接收的座標已從指定的地理柵欄移至外部時引發 |
| Microsoft.Maps.GeofenceResult | 會在每次地理柵欄查詢傳回結果時引發，無論狀態為何 |

## <a name="event-schema"></a>結構描述

下列範例顯示 Geofenceresult 架構的架構：

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>取用事件的秘訣

處理 Azure 地圖服務地理柵欄事件的應用程式應該遵循幾個建議做法：

* 設定多個訂閱以將事件路由至相同的事件處理常式。 請務必不要假設事件是來自特定來源。 請一律檢查訊息主題，以確保訊息來自您預期的來源。
* 使用回應`X-Correlation-id`標頭中的欄位，來瞭解您的物件相關資訊是否為最新狀態。 訊息的抵達可能會不按順序或有延遲。
* 當呼叫地理柵欄 API 中的 GET 或 POST 要求，並將 mode 參數設為`EnterAndExit`時，就會針對地理柵欄中的每個幾何（其狀態已從先前的地理柵欄 API 呼叫變更）產生 Enter 或 Exit 事件。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用地理柵欄來控制工地的作業，請參閱：

> [!div class="nextstepaction"] 
> [使用 Azure 地圖服務設定地理柵欄](tutorial-geofence.md)
