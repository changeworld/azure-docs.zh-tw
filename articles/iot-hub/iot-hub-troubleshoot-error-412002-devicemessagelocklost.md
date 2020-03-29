---
title: 故障排除 Azure IoT 中心錯誤 412002 設備消息鎖定丟失
description: 瞭解如何修復錯誤 412002 設備消息鎖定丟失
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960759"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

本文介紹了**412002 設備消息鎖定錯誤**的原因和解決方案。

## <a name="symptoms"></a>徵狀

嘗試發送雲到設備的消息時，請求失敗，錯誤**412002 設備消息鎖定丟失**。

## <a name="cause"></a>原因

當設備從佇列收到雲到設備的消息時（例如，使用[`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)），該消息由 IoT 中心鎖定，鎖定超時持續時間為一分鐘。 如果設備嘗試在鎖定超時過期後完成消息，IoT 中心將引發此異常。

## <a name="solution"></a>解決方法

如果 IoT 中心在一分鐘的鎖定超時持續時間內未收到通知，則會將消息設置回*排隊*狀態。 設備可以嘗試再次接收消息。 為了防止將來發生錯誤，實現設備端邏輯，在收到消息後一分鐘內完成消息。 此一分鐘的超時無法更改。