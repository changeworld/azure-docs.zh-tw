---
title: IoT 隨插即用程式庫和 Sdk
description: 適用于開發 IoT 隨插即用啟用的解決方案之裝置和服務程式庫的相關資訊。
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6ea80c88f2c16614c8568bc6ccfa3f4308e954b0
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577300"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>適用于 IoT 隨插即用的 Microsoft Sdk

IoT 隨插即用程式庫和 Sdk 可讓開發人員在多個平臺上使用各種程式設計語言來建立 IoT 解決方案。 下表包含範例和快速入門的連結，可協助您開始使用：

## <a name="device-sdks"></a>裝置 SDK

| Language | Package | 程式碼存放庫 | 範例 | 快速入門 | 參考 |
|---|---|---|---|---|---|
| C-裝置 | [vcpkg 1.3。9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [範例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [連線至 IoT 中樞](quickstart-connect-device-c.md) | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-裝置 | [NuGet 1.31。0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [連線至 IoT 中樞](quickstart-connect-device-csharp.md) | [參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| JAVA-裝置 | [Maven 1.25。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [連線至 IoT 中樞](quickstart-connect-device-java.md) | [參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python-裝置 | [pip 2.3。0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [範例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [連線至 IoT 中樞](quickstart-connect-device-python.md) | [參考](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| 節點-裝置 | [npm 1.17。2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [範例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [連線至 IoT 中樞](quickstart-connect-device-node.md) | [參考](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |

## <a name="device-sdks-preview"></a> (預覽版的裝置 Sdk) 

| 語言 | 程式碼存放庫/範例 |
|---|---|
|適用于 Embedded 的 Azure SDK| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTO IoT 中介軟體| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Azure RTO 快速入門手冊 | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks"></a>服務 SDK

| Language | Package | 程式碼存放庫 | 範例 | 快速入門 | 參考 |
|---|---|---|---|---|---|
| .NET-IoT 中樞服務 | [NuGet 1.31。0](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/A | [參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| JAVA-IoT 中樞服務 | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/A | [參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| 節點-IoT 中樞服務 | [npm 1.13。0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [範例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/A | [參考](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python-數位 Twins 服務 | [pip 2.2。3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [範例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [與 IoT 中樞數位 Twins API 互動](quickstart-service-python.md) | N/A |
| 節點-數位 Twins 服務 | [npm 1.13。0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [範例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [與 IoT 中樞數位 Twins API 互動](quickstart-service-node.md) | N/A |

## <a name="next-steps"></a>後續步驟

若要試用 Sdk 和程式庫，請參閱  [開發人員指南](concepts-developer-guide-device-csharp.md) 和 [裝置快速](quickstart-connect-device-c.md) 入門和 [服務快速入門](quickstart-service-node.md)。
