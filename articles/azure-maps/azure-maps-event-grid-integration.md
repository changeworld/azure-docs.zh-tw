---
title: 使用事件網格回應映射事件 |微軟 Azure 地圖
description: 在本文中，您將學習如何通過使用事件網格對 Microsoft Azure 映射事件做出反應。
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335724"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>使用事件格線回應 Azure 地圖服務事件 

Azure 映射與 Azure 事件網格集成，以便使用者可以將事件通知發送到其他服務並觸發下游進程。 本文的目的是説明您配置商務應用程式以偵聽 Azure 地圖事件。 這允許使用者以可靠、可擴展和安全的方式對關鍵事件做出反應。 例如，使用者可以構建應用程式以更新資料庫、創建票證和在每次設備進入地理圍欄時發送電子郵件通知。

Azure 事件網格是一個完全託管的事件路由服務，它使用發佈-訂閱模型。 事件網格具有對[Azure 函數](https://docs.microsoft.com/azure/azure-functions/functions-overview)和[Azure 邏輯應用](https://docs.microsoft.com/azure/azure-functions/functions-overview)等 Azure 服務的內置支援。 它可以使用 Webhook 向非 Azure 服務提供事件警報。 如需事件方格所支援的事件處理常式完整清單，請參閱 [Azure 事件方格簡介](https://docs.microsoft.com/azure/event-grid/overview)。


![Azure 事件格線運作模型](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure 地圖服務事件類型

Event Grid 使用[事件訂閱](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)將事件訊息路由至訂閱者。 Azure 地圖服務帳戶會發出下列事件類型： 

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 接收座標時從給定地理圍欄外部移動到內部時引發 |
| Microsoft.Maps.GeofenceExited | 接收座標從給定的地理圍欄內移動到外部時引發 |
| Microsoft.Maps.GeofenceResult | 會在每次地理柵欄查詢傳回結果時引發，無論狀態為何 |

## <a name="event-schema"></a>結構描述

下面的示例顯示了 GeofenceResult 的架構：

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

* 配置多個訂閱以將事件路由到同一事件處理常式。 請務必不要假設事件是來自特定來源。 始終檢查訊息主旨，以確保消息來自您期望的源。
* 使用回應`X-Correlation-id`標頭中的欄位瞭解有關物件的資訊是否最新。 訊息的抵達可能會不按順序或有延遲。
* 當使用模式參數設置為`EnterAndExit`調用 Geofence API 中的 GET 或 POST 請求時，將針對狀態與以前的 Geofence API 呼叫更改的地理圍欄中的每個幾何體生成 Enter 或 Exit 事件。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用地理柵欄來控制工地的作業，請參閱：

> [!div class="nextstepaction"] 
> [使用 Azure 地圖服務設定地理柵欄](tutorial-geofence.md)
