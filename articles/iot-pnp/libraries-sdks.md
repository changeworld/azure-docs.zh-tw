---
title: IoT 隨插即用庫和 SDK
description: 有關可用於開發支援 IoT 隨插即用解決方案的設備和服務庫的資訊。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064335"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT 隨插即用庫和 SDK

IoT 隨插即用庫和 SDK 使開發人員能夠在多個平臺上使用各種程式設計語言構建 IoT 解決方案。 下表包括指向示例的連結和説明您入門的快速入門：

## <a name="microsoft-supported-libraries-and-sdks"></a>微軟支援的庫和 SDK

| Platform | 庫/包 | 原始程式碼 | 範例 | 快速入門 | 參考資料 |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [apt-get 上的設備 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位雙用戶端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-c-linux.md) | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/視窗  | [Vcpkg 上的設備 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位雙用戶端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-c-windows.md) | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [嵌入上的設備 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位雙用戶端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/阿爾杜伊諾  | [Arduino IDE 中的設備 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位雙用戶端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [可哥盒上的設備 SDK](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [數位雙用戶端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [數位雙子樣本](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-csharp.md) | [參考](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [數位雙子樣本](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-java.md) | [參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [數位雙子樣本](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [連線至 IoT 中樞](./quickstart-connect-pnp-device-node.md) | [參考](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>IoT 中樞支援

IoT 隨插即用裝置功能僅受[免費和標準層 IoT 集線器](../iot-hub/iot-hub-scaling.md)的支援。

## <a name="next-steps"></a>後續步驟

除了設備 SDK 和庫之外，您還可以使用 REST API 與模型存儲庫進行交互。