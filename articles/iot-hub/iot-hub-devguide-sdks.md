---
title: 了解 Azure IoT SDK | Microsoft Docs
description: 開發人員指南 - 可用來建置裝置應用程式和後端應用程式的各種 Azure IoT 裝置和服務 SDK 的相關資訊和連結。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom: mqtt
ms.openlocfilehash: 68186e777051e77deca7b6e5891eb392cac23260
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729042"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>了解和使用 Azure IoT 中樞 SDK

有兩種類別的軟體開發套件 (SDK) 可與「IoT 中樞」搭配使用：

* **IoT 中心設備 SDK**使您能夠使用設備用戶端或模組用戶端構建在 IoT 設備上運行的應用。 這些應用程式會將遙測傳送至您的 IoT 中樞，並選擇性地接收來自 IoT 中樞的訊息、作業、方法或對應項更新。  您也可以使用模組用戶端來撰寫適用於 [Azure IoT Edge 執行階段](../iot-edge/about-iot-edge.md)的[模組](../iot-edge/iot-edge-modules.md)。

* **IoT 中樞服務 SDK** 可讓您建置後端應用程式來管理 IoT 中樞，並選擇性地傳送訊息、排程作業、叫用直接方法，或是將所需的屬性更新傳送到您的 IoT 裝置或模組。

此外，我們也提供一組能與[裝置佈建服務](../iot-dps/about-iot-dps.md)搭配使用的 SDK。
* **佈建裝置 SDK** 可讓您建置能在 IoT 裝置上執行的應用程式，以便與裝置佈建服務進行通訊。

* **佈建服務 SDK** 可讓您建置後端應用程式，以管理裝置佈建服務中的註冊。

了解[使用 Azure IoT SDK 進行開發的優點](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) \(英文\)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>作業系統平台和硬體相容性

您可以在 [Azure IoT SDK 平台支援](iot-hub-device-sdk-platform-support.md)中找到支援 SDK 的平台。

如需 SDK 與特定硬體裝置之相容性的詳細資訊，請參閱 [Azure IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)或個別的存放庫。

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT 中樞裝置 SDK

Microsoft Azure IoT 裝置 SDK 包含有助於建置應用程式的程式碼，並使裝置和應用程式連線到 Azure IoT 中心服務，且由其進行管理。

適用於 .NET 的 Azure IoT 中樞裝置 SDK： 

* 從[NuGet 下載](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)。  命名空間是 Microsoft.Azure.Devices.Clients，其中包含 IoT 中樞裝置用戶端 (DeviceClient、ModuleClient)。
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-csharp)
* [API 參考資料](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [模組參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) \(英文\)

適用於 C 的 Azure IoT 中樞裝置 SDK (ANSI C - C99)：

* 從 [apt-get、MBED、Arduino IDE 或 iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-c)
* [編譯 C 裝置 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API 參考資料](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [模組參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) \(英文\)
* [將 C SDK 移轉到其他平台](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* 有關交叉編譯和不同平台的使用者入門等資訊，請參閱[開發人員文件](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc)。
* [Azure IoT 中心 C SDK 資源消耗資訊](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

適用於 Java 的 Azure IoT 中樞裝置 SDK： 

* 新增至 [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) 專案
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-java)
* [API 參考資料](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [模組參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) \(英文\)

適用於 Node.js 的 Azure IoT 中樞裝置 SDK： 

* 從 [npm](https://www.npmjs.com/package/azure-iot-device) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-node)
* [API 參考資料](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [模組參考](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest) \(英文\)

適用於 Python 的 Azure IoT 中樞裝置 SDK： 

* 從 [pip](https://pypi.org/project/azure-iot-device/) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-python)
* [API 參考資料](https://docs.microsoft.com/python/api/azure-iot-device)

適用於 iOS 的 Azure IoT 中樞裝置 SDK： 

* 從 [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) 安裝
* [範例](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API 參考：請參閱 [C API 參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) \(英文\)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT 中樞服務SDK

Microsoft Azure IoT 服務 SDK 包含可協助建置應用程式的程式碼，這些應用程式可直接與「IoT 中樞」互動來管理裝置和安全性。

適用於 .NET 的 Azure IoT 中樞服務 SDK：

* 從[NuGet 下載](https://www.nuget.org/packages/Microsoft.Azure.Devices/)。  命名空間是 Microsoft.Azure.Devices，其中包含 IoT 中樞服務用戶端 (RegistryManager、ServiceClients)。
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-csharp)
* [API 參考資料](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

適用於 Java 的 Azure IoT 中樞服務 SDK： 

* 新增至 [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) 專案
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-java)
* [API 參考資料](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

適用於 Node.js 的 Azure IoT 中樞服務 SDK： 

* 從 [npm](https://www.npmjs.com/package/azure-iothub) 下載
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-node)
* [API 參考資料](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

適用於 Python 的 Azure IoT 中樞服務 SDK： 

* 從 [pip](https://pypi.python.org/pypi/azure-iot-hub/) 下載
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-python/tree/master)

適用於 C 的 Azure IoT 中樞服務 SDK： 

* 從[貼切取得、MBED、Arduino IDE 或 NuGet 下載](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-c)

適用於 iOS 的 Azure IoT 中樞服務 SDK： 

* 從 [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) 安裝
* [範例](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> 請參閱 GitHub 儲存機制中的讀我檔案，以取得使用語言和平台特定套件管理員在開發電腦上安裝二進位檔和相依項目的相關資訊。

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure 佈建 SDK

**Microsoft Azure 佈建 SDK** 可讓您使用[裝置佈建服務](../iot-dps/about-iot-dps.md)，將裝置佈建到您的 IoT 中樞。

適用於 C# 的 Azure 佈建裝置與服務 SDK：

* 從 NuGet 的[裝置 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/)和[服務 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) 下載。
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API 參考資料](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

適用於 C 的 Azure 佈建裝置與服務 SDK：

* 從 [apt-get、MBED、Arduino IDE 或 iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API 參考資料](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

適用於 Java 的 Azure 佈建裝置與服務 SDK：

* 新增至 [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) 專案
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API 參考資料](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

適用於 Node.js 的 Azure 佈建裝置與服務 SDK：

* [原始程式碼](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API 參考資料](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* 從 npm 下載[裝置 SDK](https://badge.fury.io/js/azure-iot-provisioning-device) 和[服務 SDK](https://badge.fury.io/js/azure-iot-provisioning-service)

適用於 Python 的 Azure 佈建裝置與服務 SDK：

* [原始程式碼](https://github.com/Azure/azure-iot-sdk-python)
* 從 pip 下載[裝置 SDK](https://pypi.org/project/azure-iot-device/) 和[服務 SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/)

## <a name="next-steps"></a>後續步驟

Azure IoT SDK 也提供一組工具以協助開發：
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics)：跨平台命令列工具，可協助診斷與 IoT 中樞連線相關的問題。
* [azure-iot-explorer:](https://github.com/Azure/azure-iot-explorer)一個跨平臺桌面應用程式,用於連接到 IoT 中心並與 IoT 設備添加/管理/通信。

使用 Azure IoT SDK 進行開發的相關文件：
* 深入了解[如何使用 IoT 中樞 SDK 來管理連線能力和可靠傳訊](iot-hub-reliability-features-in-sdks.md)。
* 深入了解如何對 iOS 和 Android 等[行動平台進行開發](iot-hub-how-to-develop-for-mobile-devices.md)。
* [Auzre IoT SDK 平台支援](iot-hub-device-sdk-platform-support.md)


此 IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點](iot-hub-devguide-endpoints.md)
* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)
* [配額和節流](iot-hub-devguide-quotas-throttling.md)
* [IoT 中樞的 MQTT 支援](iot-hub-mqtt-support.md)
* [IoT 中樞 REST API 參考](/rest/api/iothub/)
