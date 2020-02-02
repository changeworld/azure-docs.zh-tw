---
title: 疑難排解 Azure IoT 中樞錯誤 412002 DeviceMessageLockLost
description: 瞭解如何修正錯誤 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960759"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

本文說明**412002 DeviceMessageLockLost**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

嘗試傳送雲端到裝置訊息時，要求失敗，並出現錯誤**412002 DeviceMessageLockLost**。

## <a name="cause"></a>原因

當裝置收到來自佇列的雲端到裝置訊息（例如，使用[`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)）時，訊息會被 IoT 中樞鎖定，鎖定超時期間為一分鐘。 如果裝置嘗試在鎖定超時到期後完成訊息，IoT 中樞會擲回此例外狀況。

## <a name="solution"></a>解決方案

如果 IoT 中樞不會在一分鐘的鎖定超時時間內收到通知，則會將訊息設回已加入*佇列*的狀態。 裝置可以嘗試再次接收訊息。 為避免未來發生此錯誤，請執行裝置端邏輯，在接收訊息的一分鐘內完成訊息。 這一分鐘的超時時間無法變更。