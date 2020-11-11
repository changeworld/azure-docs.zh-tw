---
title: 疑難排解 Azure IoT 中樞錯誤 400027 ConnectionForcefullyClosedOnNewConnection
description: 瞭解如何修正錯誤 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506323"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

本文說明 **400027 ConnectionForcefullyClosedOnNewConnection** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

您的裝置會與使用 .NET SDK 和 MQTT 傳輸類型的 **Communication_Error** **ConnectionStatusChangeReason** 中斷連線。

您的裝置到雲端對應項作業 (例如讀取或修補報告的屬性) 或直接方法調用失敗，錯誤碼為 **400027** 。

## <a name="cause"></a>原因

另一個用戶端使用相同的認證來建立與 IoT 中樞的新連線，因此 IoT 中樞會關閉先前的連線。 IoT 中樞不允許有多個用戶端使用同一組認證進行連線。

## <a name="solution"></a>解決方案

確定每個用戶端都使用自己的身分識別連線到 IoT 中樞。