---
title: IoT 隨插即用程式庫和 Sdk
description: 可用來開發啟用 IoT 隨插即用解決方案的裝置和服務程式庫的相關資訊。
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3331a0a9a8d3fb5d028d801d334daf2dbfa25235
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337325"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT 隨插即用程式庫和 Sdk

IoT 隨插即用程式庫和 Sdk 可讓開發人員在多個平臺上使用各種程式設計語言來建立 IoT 解決方案。 下表包含範例和快速入門的連結，可協助您開始使用：

## <a name="microsoft-sdks-for-iot-plug-and-play"></a>適用于 IoT 隨插即用的 Microsoft Sdk

**裝置 SDK**

| Language | Package | 程式碼存放庫 | 範例 | 快速入門 | 參考 |
|---|---|---|---|---|---|
| C-裝置 | [vcpkg 1.3。9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [範例](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [連線至 IoT 中樞](quickstart-connect-device-c.md) | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-裝置 | [NuGet 1.27。0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [範例](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [連線至 IoT 中樞](quickstart-connect-device-csharp.md) | [參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| JAVA-裝置 | [Maven 1.24。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [連線至 IoT 中樞](quickstart-connect-device-java.md) | [參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-裝置 | [pip 2.1。4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [範例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [連線至 IoT 中樞](quickstart-connect-device-python.md) | [參考](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| 節點-裝置 | [npm 1.17.0 或](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [範例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [連線至 IoT 中樞](quickstart-connect-device-node.md) | [參考](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

**服務 Sdk （預覽）**

| Language | Package | 程式碼存放庫 | 範例 | 快速入門 | 參考 |
|---|---|---|---|---|---|
| .NET-IoT 中樞服務預覽 | [NuGet 1.27.1-預覽-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [範例](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | NA | [參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet) |
| JAVA-IoT 中樞服務預覽 | [Maven 1.1。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [範例](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | NA | [參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable) |
| Python-IoT 中樞/數位 Twins 服務預覽 | [pip 2.2.1 rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [範例](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [與 IoT 中樞數位 Twins API 互動](quickstart-service-python.md) | [參考](https://docs.microsoft.com/python/api/azure-iot-hub/?view=azure-python) |
| 節點 IoT 中樞/數位 Twins 服務預覽 | [npm 1.0.0-pnp-重新整理。3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [範例](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [與 IoT 中樞數位 Twins API 互動](quickstart-service-node.md) | [參考](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest) |

## <a name="next-steps"></a>後續步驟

若要試用 Sdk 和程式庫，請參閱[開發人員指南](concepts-developer-guide.md)和[裝置快速入門](quickstart-connect-device-c.md)和[服務快速入門](quickstart-service-node.md)。
