---
title: 疑難排解 Azure IoT 中樞錯誤 403002 IoTHubQuotaExceeded
description: 瞭解如何修正錯誤 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76961110"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

本文說明**403002 IoTHubQuotaExceeded**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

IoT 中樞的所有要求都會失敗，並出現錯誤**403002 IoTHubQuotaExceeded**。 在 Azure 入口網站中，IoT 中樞裝置清單不會載入。

## <a name="cause"></a>原因

已超過 IoT 中樞的每日訊息配額。 

## <a name="solution"></a>解決方案

[升級或增加 IoT 中樞的單位數](iot-hub-upgrade.md)，或等待下一個 UTC 日，讓每日配額重新整理。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何將作業計入配額（例如對應項查詢和直接方法），請參閱[瞭解 IoT 中樞定價](iot-hub-devguide-pricing.md#charges-per-operation)
* 若要設定監視每日配額使用量，請使用計量*總數來*設定警示。 如需逐步指示，請參閱[使用 IoT 中樞設定計量和警示](tutorial-use-metrics-and-diags.md#set-up-metrics)