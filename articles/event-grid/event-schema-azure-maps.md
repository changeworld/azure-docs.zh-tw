---
title: Azure 事件方格 Azure 地圖服務事件結構描述
description: 描述使用 Azure 事件方格提供給 Azure 地圖服務事件的屬性和結構描述
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486354"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure 地圖服務的 Azure 事件方格事件結構描述

本文提供 Azure 地圖服務事件的屬性和結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](https://docs.microsoft.com/azure/event-grid/event-schema)。

## <a name="available-event-types"></a>可用的事件類型

Azure 地圖服務帳戶會發出下列事件類型：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 會在接收到的座標已從指定的地理柵欄外移至柵欄內時引發 |
| Microsoft.Maps.GeofenceExited | 會在接收到的座標已從指定的地理柵欄內移至柵欄外時引發 |
| Microsoft.Maps.GeofenceResult | 會在每次地理柵欄查詢傳回結果時引發，無論狀態為何 |

## <a name="event-examples"></a>事件範例

下列範例顯示 **GeofenceEntered** 事件的結構描述

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

下列範例顯示 **GeofenceResult** 的結構描述 

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

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | 地理柵欄事件資料。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| apiCategory | 字串 | 事件的 API 類別。 |
| apiName | 字串 | API 的名稱。 |
| 問題 | 物件 (object) | 列出在處理期間遇到的問題。 如果傳回任何問題，則不會有任何幾何隨回應傳回。 |
| responseCode | number | HTTP 回應碼 |
| geometries | 物件 (object) | 列出包含該座標位置或在該位置與 searchBuffer 周圍重疊的柵欄幾何。 |

在地圖服務 API 中發生錯誤時，會傳回錯誤物件。 錯誤物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| error | ErrorDetails |在地圖服務 API 中發生錯誤時，會傳回物件  |

在地圖服務 API 中發生錯誤時，會傳回 ErrorDetails 物件。 ErrorDetails 或物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 代碼 | 字串 | HTTP 狀態碼。 |
| message | 字串 | 人類看得懂的錯誤描述 (若可取得)。 |
| innererror | InnerError | 包含有關錯誤之服務特定資訊的物件 (若可取得)。 |

InnerError 是物件，其中包含有關錯誤的服務特定資訊。 InnerError 物件具有下列屬性： 

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 代碼 | 字串 | 錯誤訊息。 |

幾何物件，列出地理柵欄的幾何識別碼，這些識別碼相對於要求中的使用者時間已經過期。 幾何物件有包含下列屬性的幾何項目： 

| 屬性 | 類型 | 描述 |
|:-------- |:---- |:----------- |
| deviceid | 字串 | 裝置識別碼。 |
| distance | 字串 | <p>從該座標到幾何邊界的最短距離。 正值表示座標在地理柵欄外。 如果座標位在地理柵欄外，但與地理柵欄邊界的最短距離超過 searchBuffer 的值，則該值為 999。 負數表示座標在地理柵欄內。 如果座標位在多邊形內，但與地理柵欄邊界的最短距離超過 searchBuffer 的值，則該值為 -999。 值為 999 表示座標在地理柵欄外是極可信的。 值為 -999 表示座標在地理柵欄內是極可信的。<p> |
| geometryid |字串 | 識別地理柵欄幾何的唯一識別碼。 |
| nearestlat | number | 幾何之最接近點的緯度。 |
| nearestlon | number | 幾何之最接近點的經度。 |
| udId | 字串 | 上傳地理柵欄時，從使用者上傳服務傳回的唯一識別碼。 不會包含在地理柵欄張貼 API。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | 地理柵欄的幾何識別碼清單，其中的識別碼相對於要求中的使用者時間已經過期。 |
| geometries | geometries[] |列出包含該座標位置或在該位置與 searchBuffer 周圍重疊的柵欄幾何。 |
| invalidPeriodGeofenceGeometryId | string[]  | 地理柵欄的幾何識別碼清單，其中的識別碼相對於要求中的使用者時間處於無效期間。 |
| isEventPublished | boolean | 如果至少有一個事件發佈到 Azure 地圖服務事件訂閱者，則為 True。如果沒有事件發佈到 Azure 地圖服務事件訂閱者，則為 False。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。