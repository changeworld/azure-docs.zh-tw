---
title: 服務開發人員指南-IoT 隨插即用 |Microsoft Docs
description: 服務開發人員的 IoT 隨插即用描述
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: df913716ff34a61e5bde4c0771ea8b7599db3d30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521359"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 隨插即用 service 開發人員指南

IoT 隨插即用可讓您建立智慧型裝置，以將其功能公告至 Azure IoT 應用程式。 當客戶將裝置連線到已啟用 IoT 隨插即用的應用程式時，IoT 隨插即用裝置不需要手動設定。

IoT 隨插即用可讓您使用已向 IoT 中樞宣告其模型識別碼的裝置。 例如，您可以直接存取裝置的屬性和命令。

若要使用連線到 IoT 中樞的 IoT 隨插即用裝置，其中一個 IoT 服務 Sdk：

## <a name="service-sdks"></a>服務 SDK

使用您解決方案中的 Azure IoT 服務 Sdk 來與裝置和模組互動。 例如，您可以使用服務 Sdk 來讀取和更新對應項屬性，並叫用命令。 支援的語言包括 c #、JAVA、Node.js 和 Python。

服務 Sdk 可讓您從解決方案存取裝置資訊，例如桌面或 web 應用程式。 服務 Sdk 包含兩個命名空間和物件模型，您可以使用這些命名空間和物件模型來取得模型識別碼：

- Iot 中樞服務用戶端。 這項服務會將模型識別碼公開為裝置對應項屬性。

- 數位 Twins 用戶端。 新的數位 Twins API 適用于 [數位 Twins 定義語言 (DTDL) ](concepts-digital-twin.md) 模型結構，例如元件、屬性和命令。 數位對應項 Api 可讓解決方案產生器更輕鬆地建立 IoT 隨插即用的解決方案。

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>後續步驟

現在您已瞭解裝置模型化，以下是一些額外的資源：

- [數位分身定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 裝置 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [模型元件](./concepts-components.md)
- [安裝和使用 DTDL authoring tools](howto-use-dtdl-authoring-tools.md)