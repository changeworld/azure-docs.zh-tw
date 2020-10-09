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
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960694"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

本文說明 **429001 ThrottlingException** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

您對 IoT 中樞的要求失敗，並出現錯誤 **429001 ThrottlingException**。

## <a name="cause"></a>原因

要求的作業已超出 IoT 中樞 [節流限制](./iot-hub-devguide-quotas-throttling.md) 。

## <a name="solution"></a>解決方法

藉由比較 *遙測訊息傳送嘗試* 計量與上述指定的限制，檢查您是否達到節流限制。 您也可以檢查 *節流錯誤* 度量的數目。 如需 IoT 中樞可用的這些計量和其他計量的詳細資訊，請參閱 [Iot 中樞計量及其使用方式](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)。

只有在違反了一段時間的限制之後，IoT 中樞才會傳回 429 ThrottlingException。 這樣做的目的是要讓您的訊息不會在您的 IoT 中樞取得高載流量時中斷。 在此同時，IoT 中樞會以作業節流速率處理訊息，如果待處理項目中有太多流量，該速率可能會很慢。 若要深入了解，請參閱 [IoT 中樞流量成形](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)。

## <a name="next-steps"></a>後續步驟

如果您正在執行配額或節流限制，請考慮 [相應增加您的 IoT 中樞](./iot-hub-scaling.md) 。