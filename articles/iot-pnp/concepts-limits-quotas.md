---
title: 限制和配額 IoT 隨插即用 |Microsoft Docs
description: 瞭解當您使用 IoT 隨插即用時適用的限制、配額和節流。
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577980"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>IoT 隨插即用限制、配額和節流

本文說明 IoT 隨插即用特定的限制、配額和節流。 也適用現有的 [IoT 中樞配額和節流](../iot-hub/iot-hub-devguide-quotas-throttling.md) 。

## <a name="iot-hub"></a>IoT 中樞

下列限制和配額適用于 IoT 中樞：

| 限制、限制和節流 | 值 | 注意 |
|-----|-----|-----|
| 每個中樞可註冊的介面數目 | 1500 ||
| 元件名稱的大小上限 | 1-64 個字元 | 允許的字元： a-z、a-z、0-9 (不是第一個字元) ，而底線 (不是第一個或最後一個字元) 。 |
| 屬性名稱的大小上限 | 1-64 個字元 | 允許的字元： a-z、a-z、0-9 (不是第一個字元) ，而底線 (不是第一個或最後一個字元) 。 |
| 屬性值的大小上限 | 與數位 Twins 定義語言[屬性](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)相同 | 深度的5個層級，而且不可以是陣列或包含陣列的任何複雜架構 |
| 命令名稱的大小上限 | 1-64 個字元 | 允許的字元： a-z、a-z、0-9 (不是第一個字元) ，而底線 (不是第一個或最後一個字元) 。|
| 裝置對應項大小 | 與[IoT 中樞限制](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size)相同 ||

## <a name="parser-library"></a>剖析器程式庫

剖析器程式庫遵循適用于 [數位 Twins 定義語言](https://github.com/Azure/opendigitaltwins-dtdl)的限制。

## <a name="next-steps"></a>後續步驟

建議的下一個步驟是複習 [IoT 隨插即用架構](concepts-architecture.md)。
