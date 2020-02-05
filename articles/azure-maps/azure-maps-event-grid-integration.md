---
title: 使用事件方格來回應對應事件 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用事件方格來回應 Microsoft Azure 對應事件。
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a89983a9ae45f21deb7a823de049373b4ff9b935
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989054"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>使用事件格線回應 Azure 地圖服務事件 

Azure 地圖服務與 Azure 事件方格整合，讓使用者可以將事件通知傳送給其他服務並觸發下游進程。 本文的目的是要協助您設定商務應用程式，以接聽 Azure 地圖服務的事件。 這項服務可讓您以可靠、可擴充且安全的方式回應重大事件。 例如，使用者可以建立應用程式來更新資料庫、建立票證，以及在每次裝置進入地理柵欄時傳遞電子郵件通知。

Azure 事件方格是完全受控的事件路由服務，它會使用發佈-訂閱模型。 事件方格內建對 Azure 服務的支援，例如[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)和[Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)。 它可以使用 webhook 將事件警示傳遞至非 Azure 服務。 如需事件方格所支援的事件處理常式完整清單，請參閱 [Azure 事件方格簡介](https://docs.microsoft.com/azure/event-grid/overview)。


![Azure 事件格線運作模型](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure 地圖服務事件類型

Event Grid 使用[事件訂閱](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)將事件訊息路由至訂閱者。 Azure 地圖服務帳戶會發出下列事件類型： 

| 事件類型 | 說明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 會在接收到的座標已從指定的地理柵欄外移至柵欄內時引發 |
| Microsoft.Maps.GeofenceExited | 會在接收到的座標已從指定的地理柵欄內移至柵欄外時引發 |
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

* 可以將多個訂用帳戶設定為將事件路由到相同的事件處理常式。 請務必不要假設事件是來自特定來源。 請一律檢查訊息主題，以確保訊息來自您預期的來源。
* 訊息的抵達可能會不按順序或有延遲。 使用回應標頭中的 [`X-Correlation-id`] 欄位，以瞭解您的物件相關資訊是否為最新狀態。
* 在模式參數設定為 `EnterAndExit` 時呼叫 Get 和 POST Geofence API 的情況下，系統會針對地理柵欄中每個狀態自上一個地理柵欄 API 呼叫已變更的幾何產生 Enter 或 Exit 事件。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用地理柵欄來控制工地的作業，請參閱：

> [!div class="nextstepaction"] 
> [使用 Azure 地圖服務設定地理柵欄](tutorial-geofence.md)
