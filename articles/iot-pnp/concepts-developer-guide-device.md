---
title: 裝置開發人員指南 (C) -IoT 隨插即用 |Microsoft Docs
description: C 裝置開發人員的 IoT 隨插即用描述
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8d8da39f038f465030a2dced092ab1b008e30e5e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511416"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT 隨插即用裝置開發人員指南

IoT 隨插即用可讓您建立智慧型裝置，以將其功能公告至 Azure IoT 應用程式。 當客戶將裝置連線到已啟用 IoT 隨插即用的應用程式時，IoT 隨插即用裝置不需要手動設定。

智慧型裝置可能會直接實作為、使用 [模組](../iot-hub/iot-hub-devguide-module-twins.md)，或使用 [IoT Edge 模組](../iot-edge/about-iot-edge.md)。

本指南說明建立遵循 [IoT 隨插即用慣例](../iot-pnp/concepts-convention.md)之裝置、模組或 IoT Edge 模組所需的基本步驟。

若要建立 IoT 隨插即用裝置、模組或 IoT Edge 模組，請遵循下列步驟：

1. 確定您的裝置使用 MQTT 或 MQTT over Websocket 通訊協定來連線到 Azure IoT 中樞。
1. 建立 [數位 Twins 定義語言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) 模型來描述您的裝置。 若要深入瞭解，請參閱 [瞭解 IoT 隨插即用模型中的元件](concepts-components.md)。
1. 更新您的裝置或模組，以宣告 `model-id` 為裝置連線的一部分。
1. 使用[IoT 隨插即用慣例](concepts-convention.md)來執行遙測、屬性和命令

當您的裝置或模組實施就緒之後，請使用 [Azure IoT explorer](howto-use-iot-explorer.md) 來驗證裝置是否遵循 IoT 隨插即用慣例。

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>後續步驟

現在您已瞭解 IoT 隨插即用裝置開發，以下有一些額外的資源：

- [數位分身定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型元件](concepts-components.md)
- [安裝和使用 DTDL authoring tools](howto-use-dtdl-authoring-tools.md)
- [IoT 隨插即用 service 開發人員指南](concepts-developer-guide-service.md)
