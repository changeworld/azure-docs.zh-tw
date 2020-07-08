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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960694"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

本文說明**429001 ThrottlingException**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

您 IoT 中樞的要求失敗，並出現錯誤**429001 ThrottlingException**。

## <a name="cause"></a>原因

已超過要求之作業的 IoT 中樞[節流限制](./iot-hub-devguide-quotas-throttling.md)。

## <a name="solution"></a>解決方案

藉由比較您的*遙測訊息傳送嘗試*計量與上述指定的限制，檢查您是否達到節流限制。 您也可以檢查*節流錯誤*度量的數目。 如需 IoT 中樞可用的這些和其他計量的詳細資訊，請參閱[IoT 中樞計量和使用方式](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)。

IoT 中樞只有在違反了太長一段時間的限制之後，才會傳回 429 ThrottlingException。 這麼做的目的是為了讓您的 IoT 中樞取得高載流量時，不會捨棄您的訊息。 在此同時，IoT 中樞會以作業節流速率處理訊息，如果待處理項目中有太多流量，該速率可能會很慢。 若要深入了解，請參閱 [IoT 中樞流量成形](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)。

## <a name="next-steps"></a>後續步驟

如果您正在執行配額或節流限制，請考慮[相應增加您的 IoT 中樞](./iot-hub-scaling.md)。