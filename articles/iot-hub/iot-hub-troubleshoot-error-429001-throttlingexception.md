---
title: 疑難排解 Azure IoT 中樞錯誤 429001 ThrottlingException
description: 瞭解如何修正錯誤 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545356"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

本文說明 **429001 ThrottlingException** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

您對 IoT 中樞的要求失敗，並出現錯誤 **429001 ThrottlingException** 。

## <a name="cause"></a>原因

要求的作業已超出 IoT 中樞 [節流限制](./iot-hub-devguide-quotas-throttling.md) 。

## <a name="solution"></a>解決方案

藉由比較 *遙測訊息傳送嘗試* 計量與上述指定的限制，檢查您是否達到節流限制。 您也可以檢查 *節流錯誤* 度量的數目。 如需這些計量的詳細資訊，請參閱 [裝置遙測計量](monitor-iot-hub-reference.md#device-telemetry-metrics)。 如需如何使用計量來協助您監視 IoT 中樞的詳細資訊，請參閱 [監視 Iot 中樞](monitor-iot-hub.md)。

只有在違反了一段時間的限制之後，IoT 中樞才會傳回 429 ThrottlingException。 這樣做的目的是要讓您的訊息不會在您的 IoT 中樞取得高載流量時中斷。 在此同時，IoT 中樞會以作業節流速率處理訊息，如果待處理項目中有太多流量，該速率可能會很慢。 若要深入了解，請參閱 [IoT 中樞流量成形](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)。

## <a name="next-steps"></a>下一步

如果您正在執行配額或節流限制，請考慮 [相應增加您的 IoT 中樞](./iot-hub-scaling.md) 。