---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792079"
---
您可以使用 REST API [取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 來取得端點的健康情況狀態。 建議您使用與路由訊息延遲相關的 [IoT 中樞計量](../articles/iot-hub/iot-hub-metrics.md) ，以在端點健康狀態為「無作用」或「狀況不良」時找出錯誤並進行偵測，因為當端點處於其中一種狀態時，我們預期會有較高的延遲。


|健康情況狀態|描述|
|---|---|
|healthy|端點正如預期般接受訊息。|
|不良|端點不接受訊息，且 IoT 中樞正在重試將訊息傳送至此端點。|
|未知|IoT 中樞尚未嘗試將訊息傳遞至此端點。|
|退化|端點正在接受比預期更慢或正在從狀況不良狀態復原的訊息。|
|死|IoT 中樞不再將訊息傳遞至此端點。 重試將訊息傳送至此端點失敗。|
