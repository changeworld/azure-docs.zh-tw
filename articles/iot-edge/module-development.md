---
title: 開發適用於 Azure IoT Edge 的模組 | Microsoft Docs
description: 開發可與執行階段和 IoT 中樞通訊的 Azure IoT Edge 自訂模組
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271378"
---
# <a name="develop-your-own-iot-edge-modules"></a>開發您自己的 IoT Edge 模組

Azure IoT Edge 模組可以與其他 Azure 服務連線，並提供給您更大的雲端資料管線。 本文說明如何開發模組以便與 IoT Edge 執行階段和 IoT 中樞進行通訊，從而開發 Azure 雲端的其餘部分。

## <a name="iot-edge-runtime-environment"></a>IoT Edge 執行階段環境

IoT Edge 執行階段提供基礎結構來整合多個 IoT Edge 模組的功能，並將其部署至 IoT Edge 裝置。 任何程式都可以打包為 IoT 邊緣模組。 為了充分利用 IoT Edge 通信和管理功能，在模組中運行的程式可以使用 Azure IoT 設備 SDK 連接到本地 IoT Edge 中心。

## <a name="using-the-iot-edge-hub"></a>使用 IoT Edge 中樞

IoT Edge 中樞提供兩個主要功能：Proxy 到 IoT 中樞，以及本機通訊。

### <a name="iot-hub-primitives"></a>IoT 中樞基本項目

IoT 中樞會看見一個類似裝置的模組執行個體，這表示：

* 它有一個模組孿生，與[設備孿生](../iot-hub/iot-hub-devguide-device-twins.md)和該設備的其他模組孿生分離;
* 它會傳送[裝置到雲端的訊息](../iot-hub/iot-hub-devguide-messaging.md)；
* 它可以接收特別在其身分識別上設為目標的[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

目前，模組無法接收雲到設備的消息或使用檔上載功能。

編寫模組時，可以使用[Azure IoT 設備 SDK](../iot-hub/iot-hub-devguide-sdks.md)連接到 IoT 邊緣中心，並使用上述功能，就像將 IoT 中心與設備應用程式一起使用時一樣。 IoT Edge 模組和 IoT 設備應用程式的唯一區別是，您必須引用模組標識而不是設備標識。

### <a name="device-to-cloud-messages"></a>裝置到雲端的訊息

為了能夠對裝置到雲端的訊息進行複雜處理，IoT Edge 中樞在模組之間以及模組與 IoT 中樞之間提供宣告式路由傳送。 宣告式路由可讓模組攔截並處理其他模組所傳送的訊息，並將它們傳播至複雜的管線。 如需詳細資訊，請參閱[在 IoT Edge 中部署模組及建立路由](module-composition.md)。

IoT Edge 模組（與正常的 IoT 中心設備應用程式相比）可以接收由其本地 IoT Edge 中心進行處理的設備到雲的消息。

IoT Edge 中樞會根據[模組資訊清單](module-composition.md)中所述的宣告式路由，將訊息傳播至模組。 在開發 IoT Edge 模組時，您可以透過設定訊息處理常式來接收這些訊息。

為了簡化路由的建立，IoT Edge 新增了模組*輸入*和*輸出*端點的概念。 模組可以接收路由傳送給它的所有裝置到雲端訊息，而不需指定任何輸入，而且可以在未指定任何輸出的情況下傳送裝置到雲端訊息。 不過，使用明確的輸入和輸出，可讓路由傳送規則更容易了解。

最後，使用下列系統屬性來為 Edge 中樞所處理的裝置到雲端訊息加上戳記：

| 屬性 | 描述 |
| -------- | ----------- |
| $connectionDeviceId | 傳送訊息之用戶端的裝置識別碼 |
| $connectionModuleId | 傳送訊息之模組的模組識別碼 |
| $inputName | 收到這則訊息的輸入。 可以是空的。 |
| $outputName | 用來傳送訊息的輸出。 可以是空的。 |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>從模組連線到 IoT Edge 中樞

從模組連線到本機 IoT Edge 中樞涉及兩個步驟：

1. 在您的應用程式中建立 ModuleClient 執行個體。
2. 請確定您的應用程式接受 IoT Edge 中樞在該裝置上所提供的憑證。

建立 ModuleClient 執行個體，以便將您的模組連接到裝置上執行的 IoT Edge 中樞，這與 DeviceClient 執行個體將 IoT 裝置連接到 IoT 中樞的方式類似。 有關 ModuleClient 類及其通信方法的詳細資訊，請參閱首選 SDK 語言的 API 引用：C#、C、Python、JAVA 或[Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

## <a name="language-and-architecture-support"></a>語言和體系結構支援

IoT Edge 支援多種作業系統、設備體系結構和開發語言，以便您可以構建符合您需求的方案。 使用此部分可以瞭解用於開發自訂 IoT Edge 模組的選項。 您可以在[為 IoT Edge 準備開發和測試環境](development-environment.md)中瞭解有關每種語言的工具支援和要求。

### <a name="linux"></a>Linux

對於下表中的所有語言，IoT Edge 支援 AMD64 和 ARM32 Linux 設備的開發。

| 開發語言 | 開發工具 |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>ARM64 Linux 設備的開發和調試支援處於[公共預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 如需詳細資訊，請參閱[在 Visual Studio Code (預覽)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) 中開發和偵錯 ARM64 IoT Edge 模組。

### <a name="windows"></a>Windows

對於下表中的所有語言，IoT Edge 支援 AMD64 Windows 設備的開發。

| 開發語言 | 開發工具 |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | 視覺化工作室代碼（無調試功能）<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>後續步驟

[準備 IoT Edge 適用的開發和測試環境](development-environment.md)

[使用視覺化工作室為 IoT 邊緣開發 C# 模組](how-to-visual-studio-develop-module.md)

[使用視覺化工作室代碼為 IoT 邊緣開發模組](how-to-vs-code-develop-module.md)

[了解和使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)
