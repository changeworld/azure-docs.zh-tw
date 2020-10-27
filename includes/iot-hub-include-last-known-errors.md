---
title: Include 檔案
description: Include 檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0b7a1c600fe81081fbfe8d33c3878f68e730888
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547348"
---
REST API 中的 [[取得端點健康](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)情況] 會提供端點的健康情況狀態，以及最後一個已知的錯誤，以識別端點狀況不良的原因。 下表列出最常見的錯誤。

|上次已知錯誤|描述/發生時|可能的緩和措施|
|-----|-----|-----|
|暫時性|發生暫時性錯誤，IoT 中樞將會重試此操作。|觀察 [路由資源記錄](https://docs.microsoft.com/azure/iot-hub/monitor-service-reference#routes)。|
|InternalError|將訊息傳遞至端點時發生錯誤。|這是內部例外狀況，但也會觀察 [路由資源記錄](https://docs.microsoft.com/azure/iot-hub/monitor-service-reference#routes)。|
|未經授權|IoT 中樞沒有將訊息傳送至指定端點的授權。|驗證端點的連接字串是最新的。 如果已變更，請考慮 IoT 中樞上的更新。 如果端點使用受控識別，請確認 IoT 中樞主體具有目標的必要許可權。|
|調整執行速度|IoT 中樞正在將訊息寫入端點時受到節流。|檢查受影響端點的節流限制。 修改端點的設定，以便在需要時擴大。|
|逾時|作業逾時。|重試作業。|
|找不到|目標資源不存在。|確定目標資源存在。|
|找不到容器|儲存體容器不存在。|確定儲存體容器存在。|
|容器已停用|儲存體容器已停用。|確定已啟用儲存體容器。|
|MaxMessageSizeExceeded|訊息路由的訊息大小限制為 256 Kb。路由的訊息大小超過此限制。|使用較少的應用程式屬性或較少的訊息擴充來檢查是否可以降低訊息大小。|
|PartitioningAndDuplicateDetectionNotSupported|服務匯流排可能未啟用重複偵測。|從服務匯流排停用重複偵測，或考慮使用實體而不重複偵測。|
|SessionfulEntityNotSupported|服務匯流排可能未啟用會話。|停用來自服務匯流排的會話，或考慮使用沒有會話的實體。|
|NoMatchingSubscriptionsForMessage|在服務匯流排主題上沒有寫入訊息的訂用帳戶。|建立要路由傳送的 IoT 中樞訊息訂用帳戶。|
|EndpointExternallyDisabled|端點未處於作用中狀態，因此 IoT 中樞可以將訊息傳送給它。|啟用端點使其恢復為使用中狀態。|
|DeviceMaximumQueueDepthExceeded|已達到服務匯流排大小的限制。|請考慮從目標事件中樞移除訊息，以允許將新訊息內嵌至事件中樞。|