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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84793269"
---
<!-- operation names for the diag logs for IoT Hub -->

|作業名稱|層級|描述|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|資訊|無法以給定條件評估訊息。 例如，如果訊息中不存在路由查詢準則中的屬性，則為。 深入瞭解[路由查詢語法](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)。|
|RouteEvaluationError|錯誤|因為訊息格式發生問題，所以評估訊息時發生錯誤。 例如，如果未指定內容編碼或訊息中的內容類型無效，就會記錄這個錯誤。 這些必須在[系統屬性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中設定。|
|DroppedMessage|錯誤|訊息已卸載且未路由傳送。 這可能是因為訊息不符合任何路由查詢或端點未通過，且在數次重試之後無法傳遞訊息的原因所致。 我們建議使用 REST API[取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)，以取得更多有關端點的詳細資料。|
|EndpointUnhealthy|錯誤|端點尚未接受來自 IoT 中樞的訊息，而且 IoT 中樞嘗試重新傳送訊息。 我們建議您透過 REST API[取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)來觀察上一個已知的錯誤。|
|EndpointDead|錯誤|端點在一小時內未接受來自 IoT 中樞的訊息。 我們建議您透過 REST API[取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)來觀察上一個已知的錯誤。|
|EndpointHealthy|資訊|端點狀況良好，且接收來自 IoT 中樞的訊息。 這則訊息不會持續記錄，但只有在端點再次變成狀況良好時才會記錄。 此訊息表示 IoT 中樞無法將訊息傳送至端點，但端點現在狀況良好。|
|OrphanedMessage|資訊|訊息不符合任何路由。|
|InvalidMessage|錯誤|訊息無效，因為與端點不相容。 我們建議您檢查端點的設定。|


作業*UndefinedRouteEvaluation*、 *RouteEvaluationError*和*OrphanedMessage*會進行節流處理，且每個 IoT 中樞一分鐘不超過一次。