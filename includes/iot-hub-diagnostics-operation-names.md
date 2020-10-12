---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 362c13771e7382ead1ba5aebd99a69fd86cd718c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793269"
---
<!-- operation names for the diag logs for IoT Hub -->

|作業名稱|層級|描述|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|資訊|無法以提供條件來評估訊息。 例如，如果訊息中沒有路由查詢準則中的屬性，則為。 深入瞭解 [路由查詢語法](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)。|
|RouteEvaluationError|錯誤|因為訊息格式發生問題，所以評估訊息時發生錯誤。 例如，如果未指定內容編碼，或訊息中的內容類型無效，則會記錄此錯誤。 這些必須在 [系統屬性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中設定。|
|DroppedMessage|錯誤|訊息已卸載且未路由傳送。 這可能是因為訊息不符合任何路由查詢或端點未通過的原因，而且在數次重試之後無法傳遞訊息。 建議您使用 REST API [取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)來取得端點的詳細資料。|
|EndpointUnhealthy|錯誤|端點尚未接受來自 IoT 中樞的訊息，且 IoT 中樞正在嘗試重新傳送訊息。 建議您透過 REST API [取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)觀察最後已知的錯誤。|
|EndpointDead|錯誤|端點未接受來自 IoT 中樞的訊息超過一小時。 建議您透過 REST API [取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)觀察最後已知的錯誤。|
|EndpointHealthy|資訊|端點狀況良好，並接收來自 IoT 中樞的訊息。 此訊息不會持續記錄，但只有當端點再次變成狀況良好時才會記錄。 這則訊息表示 IoT 中樞無法將訊息傳送至端點，但端點現在狀況良好。|
|OrphanedMessage|資訊|訊息不符合任何路由。|
|InvalidMessage|錯誤|訊息無效，因為與端點不相容。 建議您檢查端點的設定。|


Operations *UndefinedRouteEvaluation*、 *RouteEvaluationError* 和 *OrphanedMessage* 會針對每個 IoT 中樞進行節流處理，而且每分鐘只會記錄一次。