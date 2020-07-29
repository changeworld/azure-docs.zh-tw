---
title: Azure 地圖服務長時間執行作業 API
description: 了解 Azure 地圖服務中長時間執行的非同步背景處理
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 360fc4af688e393bb8639ee773f0bf0de603a425
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596047"
---
# <a name="creator-long-running-operation-api"></a>建立工具長時間執行作業 API

Azure 地圖服務中的某些 API 會使用[非同步要求 - 回復模式](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)。 此模式可讓 Azure 地圖服務提供高可用性和回應性的服務。 本文說明 Azure 地圖服務之長時間執行非同步背景處理的特定實作。

## <a name="submitting-a-request"></a>提交要求

用戶端應用程式會透過對 HTTP API 的同步呼叫來啟動長時間執行的作業。 一般而言，此呼叫的格式為 HTTP POST 要求。 成功建立非同步工作負載時，API 會傳回 HTTP `202` 狀態碼，表示已接受要求。 此回應包含指向端點的 `Location` 標題，用戶端可以輪詢該端點以檢查長時間執行作業的狀態。

### <a name="example-of-a-success-response"></a>成功回應範例

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

如果呼叫未通過驗證，則 API 會改為傳回不正確要求的 HTTP `400` 回應。 回應主體會提供用戶端有關要求無效原因的詳細資訊。

### <a name="monitoring-the-operation-status"></a>監視作業狀態

可輪詢已接受回應標題中所提供的位置端點，以檢查長時間執行作業的狀態。 作業狀態要求的回應主體一律會包含 `status` 和 `createdDateTime` 屬性。 `status` 屬性表示長時間執行作業的目前狀態。 可能狀態包括 `"NotStarted"`、`"Running"`、`"Succeeded"` 以及 `"Failed"` 。 `createdDateTime` 屬性會顯示開始進行長時間執行之作業的初始要求。 當狀態為 `"NotStarted"` 或 `"Running"` 時，系統也會提供回應的 `Retry-After` 標題。 `Retry-After` 標題 (以秒為單位) 可以用來決定何時應該進行下一個輪詢呼叫作業狀態 API。

### <a name="example-of-running-a-status-response"></a>執行狀態回應的範例

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>處理作業完成

完成長時間執行的作業時，回應的狀態會是 `"Succeeded"` 或 `"Failed"`。 從長時間執行的作業建立新的資源時，成功回應會傳回 HTTP `201 Created` 狀態碼。 回應也會包含指向資源相關中繼資料的 `Location` 標題。 並未建立新的資源時，成功回應會傳回 HTTP `200 OK` 狀態碼。 失敗時，回應狀態碼也會是 `200 OK` 程式碼。 不過，回應會有主體中的 `error` 屬性。 錯誤資料依循 OData 錯誤規格。

### <a name="example-of-success-response"></a>成功回應範例

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>失敗回應範例

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```
