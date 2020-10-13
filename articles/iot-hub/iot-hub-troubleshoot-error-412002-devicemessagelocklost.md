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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960759"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

本文說明 **412002 DeviceMessageLockLost** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

當嘗試傳送雲端到裝置的訊息時，要求會失敗，並出現錯誤 **412002 DeviceMessageLockLost**。

## <a name="cause"></a>原因

當裝置接收來自佇列的雲端到裝置訊息時 (例如，使用 [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)) 訊息會由 IoT 中樞鎖定一分鐘的鎖定超時時間。 如果裝置在鎖定超時時間過期後嘗試完成訊息，IoT 中樞會擲回此例外狀況。

## <a name="solution"></a>解決方法

如果 IoT 中樞未在一分鐘的鎖定超時時間內取得通知，則會將訊息設定回已加入 *佇列* 的狀態。 裝置可以嘗試再次接收訊息。 為了避免未來發生此錯誤，請在接收訊息的一分鐘內，執行裝置端邏輯來完成訊息。 這一分鐘的時間無法變更。