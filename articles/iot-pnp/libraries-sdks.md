---
title: IoT 隨插即用程式庫和 Sdk
description: 可用來開發啟用 IoT 隨插即用解決方案的裝置和服務程式庫的相關資訊。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80064335"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT 隨插即用程式庫和 Sdk

IoT 隨插即用程式庫和 Sdk 可讓開發人員在多個平臺上使用各種程式設計語言來建立 IoT 解決方案。 下表包含範例和快速入門的連結，可協助您開始使用：

## <a name="microsoft-supported-libraries-and-sdks"></a>Microsoft 支援的程式庫和 Sdk

| 平台 | 程式庫/套件 | 原始程式碼 | 範例 | 快速入門 | 參考 |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Apt 上的裝置 SDK-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位對應項用戶端範例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-c-linux.md) | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Vcpkg 上的裝置 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位對應項用戶端範例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-c-windows.md) | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [內嵌上的裝置 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位對應項用戶端範例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Arduino IDE 中的裝置 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位對應項用戶端範例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [CocoaPod 上的裝置 SDK](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位對應項用戶端範例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [數位對應項範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-csharp.md) | [參考](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [數位對應項範例](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-java.md) | [參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [數位對應項範例](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-node.md) | [參考](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>IoT 中樞支援

IoT 隨插即用裝置功能僅供[免費和標準層 iot 中樞](../iot-hub/iot-hub-scaling.md)支援。

## <a name="next-steps"></a>後續步驟

除了裝置 Sdk 和程式庫之外，您還可以使用 REST Api 來與模型存放庫互動。