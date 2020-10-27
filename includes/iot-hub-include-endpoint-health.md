---
title: Include 檔案
description: Include 檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 36c05badb3b2292a29b8227c7f03b841474c97ad
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548121"
---
您可以使用 REST API [取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 來取得端點的健康情況狀態。 建議您在端點健康狀態為「無作用」或「狀況不良」時，使用與路由訊息延遲相關的 [IoT 中樞路由計量](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) 來識別及偵測錯誤，因為當端點處於其中一種狀態時，我們預期會有較高的延遲。 若要深入瞭解如何使用 IoT 中樞計量，請參閱 [監視 Iot 中樞](../articles/iot-hub/monitor-iot-hub.md)。

|健康情況狀態|描述|
|---|---|
|healthy|端點正如預期般接受訊息。|
|不良|端點不接受訊息，且 IoT 中樞正在重試將訊息傳送至此端點。|
|未知|IoT 中樞尚未嘗試將訊息傳遞至此端點。|
|退化|端點正在接受比預期更慢或正在從狀況不良狀態復原的訊息。|
|死|IoT 中樞不再將訊息傳遞至此端點。 重試將訊息傳送至此端點失敗。|
