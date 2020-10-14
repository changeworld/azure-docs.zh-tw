---
title: IoT 隨插即用目前版本 | Microsoft Docs
description: 了解目前 IoT 隨插即用版本中包含的內容。
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4bd1bb93c9ce0f491c5bf1153917491b88d55109
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043709"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>目前 IoT 隨插即用版本中的內容

本文摘要說明支援目前 IoT 隨插即用版本的工具、SDK 和 API。 所顯示的版本號碼會反映 IoT 隨插即用正式推出時的版本號碼。 版本號碼可能會隨發行而遞增。

## <a name="modeling-language"></a>模型化語言

[數位分身定義語言 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl)。

若要深入了解 IoT 隨插即用裝置如何與 DTDL 搭配使用，請參閱 [IoT 隨插即用慣例](concepts-convention.md)。

## <a name="tools-and-utilities"></a>工具和公用程式

- Azure IoT 總管 0.12.0。

    如需詳細資訊，請參閱[安裝和使用 Azure IoT 總管](howto-use-iot-explorer.md)。

- VS Code 延伸模組 1.0.0。

    若要深入了解，請參閱[安裝及使用 DTDL 製作工具](howto-use-dtdl-authoring-tools.md)。

- Visual Studio 2019 延伸模組 1.0.0。

    若要深入了解，請參閱[安裝及使用 DTDL 製作工具](howto-use-dtdl-authoring-tools.md)。

- Azure CLI IoT 延伸模組 0.10.0。

    Azure IoT 延伸模組包含可協助認證裝置的命令。 請參閱 `az iot product -h`。

## <a name="libraries-and-sdks"></a>程式庫和 SDK

若要深入了解程式庫和 SDK，請參閱[適用於 IoT 的 Microsoft SDK 隨插即用](libraries-sdks.md)。

- C 裝置 SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- 內嵌 C 裝置 SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET 裝置 SDK [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Java 裝置 SDK [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python 裝置 SDK [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js 裝置 SDK [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET - IoT 中樞服務 [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java - IoT 中樞服務 [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js - IoT 中樞服務 [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python - IoT 中樞/數位對應項服務 [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL 模型剖析器 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)。

## <a name="rest-apis"></a>REST API

REST API [2020-09-30](/rest/api/iothub)。

若要深入了解，請參閱 [IoT 隨插即用開發人員指南](concepts-developer-guide-service.md)。

## <a name="iot-hub"></a>IoT 中樞

所有區域的 IoT 中樞都支援 IoT 隨插即用。 IoT 隨插即用僅支援標準或免費階層 IoT 中樞。

## <a name="announcements"></a>公告

針對目前和先前的 IoT 隨插即用公告，請參閱下列 blog 文章：

- [公開預覽重新整理 (於 2020 年 8 月 29 日公佈)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [為 IoT 隨插即用準備及認證您的裝置 (於 2020 年 8 月 26 日公佈)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT 隨插即用現已在預覽階段 (於 2019 年 8 月 22 日公佈)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [使用 Azure IoT Central 與 IoT 隨插即用建置 (於 2019 年 5 月 7 日公佈)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)