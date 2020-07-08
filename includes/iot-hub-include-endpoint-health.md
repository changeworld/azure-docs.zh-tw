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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792079"
---
您可以使用 REST API[取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)來取得端點的健全狀況狀態。 我們建議使用與路由訊息延遲相關的[IoT 中樞計量](../articles/iot-hub/iot-hub-metrics.md)，在端點健康狀態為 [無作用] 或 [狀況不良] 時識別和偵測錯誤，因為當端點處於其中一種狀態時，我們預期會有更高的延遲。


|健康情況狀態|Description|
|---|---|
|healthy|端點會如預期般接受訊息。|
|isapi|端點不接受訊息，IoT 中樞正在重試將訊息傳送到此端點。|
|未知|IoT 中樞未嘗試將訊息傳遞至這個端點。|
|降級|端點正在接受比預期慢的訊息，或從狀況不良狀態復原。|
|損|IoT 中樞不會再將訊息傳遞至這個端點。 重試將訊息傳送到此端點失敗。|
