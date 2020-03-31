---
title: 排除 Azure IoT 中心錯誤 403002 IoTHub 配額超過
description: 瞭解如何修復錯誤 403002 IoTHub 配額超過
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961110"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

本文介紹了**403002 IoTHubQuota 超過**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

所有對 IoT 中心的請求都失敗，錯誤**403002 IoTHubQuota 超過**。 在 Azure 門戶中，IoT 中心設備清單不會載入。

## <a name="cause"></a>原因

超過 IoT 中心的日常消息配額。 

## <a name="solution"></a>解決方法

[升級或增加 IoT 中心上的單位數量](iot-hub-upgrade.md)，或等待下一個 UTC 日更新每日配額。

## <a name="next-steps"></a>後續步驟

* 要瞭解如何將操作計入配額（如雙查詢和直接方法），請參閱[瞭解 IoT 中心定價](iot-hub-devguide-pricing.md#charges-per-operation)
* 要設置每日配額使用方式的監視，使用指標使用*的消息總數*設置警報。 有關分步說明，請參閱[使用 IoT 中心設置指標和警報](tutorial-use-metrics-and-diags.md#set-up-metrics)