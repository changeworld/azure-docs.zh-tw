---
title: Azure IoT 裝置 SDK 平台支援 | Microsoft Docs
description: '開放原始碼裝置 Sdk 可在 GitHub （C、.NET （c #）、JAVA、node.js 和 Python）中取得，以將裝置連接到 Azure IoT 中樞和裝置布建服務（DPS）。'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76045129"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT 裝置 Sdk 平臺支援

Microsoft 致力於持續擴充 Azure IoT 中樞功能強大的裝置。 Microsoft 會發佈 GitHub 上的開放原始碼裝置 Sdk，以協助將裝置連接到 Azure IoT 中樞和裝置布建服務。 裝置 Sdk 適用于 C、.NET （c #）、JAVA、node.js 和 Python。 Microsoft 會測試每個 SDK，以確保它會在[Microsoft sdk 和裝置平臺支援](#microsoft-sdks-and-device-platform-support)一節中詳述的支援設定上執行。

除了裝置 Sdk 之外，Microsoft 還提供數個其他途徑，讓客戶和開發人員能夠將他們的裝置連線至 Azure IoT：

* Microsoft 會與數個合作夥伴公司共同合作，協助他們根據 Azure IoT C SDK 發佈其硬體平臺的開發工具組。

* Microsoft 會與 Microsoft 信任的合作夥伴合作，以提供一組不斷擴充的裝置，並已針對 Azure IoT 進行測試和認證。 如需這些裝置的最新清單，請參閱[Azure IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)。

* Microsoft 在 Azure IoT 中樞裝置 C SDK 中提供平臺抽象層（PAL），可協助開發人員輕鬆地將 SDK 移植到其平臺。 若要深入瞭解，請參閱[C SDK 移植指引](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

本主題提供 Microsoft Sdk 和其支援的平臺設定，以及上述每個其他選項的相關資訊。

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft Sdk 和裝置平臺支援

Microsoft 會針對下列語言發行 GitHub 上的開放原始碼 Sdk： C、.NET （c #）、node.js、JAVA 和 Python。 這一節會列出 Sdk 及其相依性。 任何符合這些相依性的裝置平臺都支援 Sdk。

針對每個列出的 Sdk，Microsoft：

* 在數個熱門平臺上，針對 GitHub 中相關 SDK 的主要分支，持續建立並執行端對端測試。  為了在不同的編譯器版本之間提供測試涵蓋範圍，我們通常會針對最新的 LTS 版本和最熱門的版本進行測試。

* 提供安裝指引或安裝套件（如果適用的話）。

* 透過開放原始碼、客戶貢獻的路徑，以及 GitHub 問題的產品小組參與，在 GitHub 上完全支援 Sdk。

### <a name="c-sdk"></a>C SDK

[Azure IoT 中樞 C 裝置 SDK](https://github.com/Azure/azure-iot-sdk-c)會使用進行測試，並支援下列設定。

| OS                  | TLS 程式庫                  | 其他需求                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL、WolfSSL 或 BearSSL | Berkeley 通訊端</br></br>可移植的作業系統介面（POSIX）                       |
| iOS 12。2            | OpenSSL                      | OSX 10.13.4 中的 XCode 模擬                                                               |
| Windows 10 系列   | SChannel                     |                                                                                             |
| Mbed 作業系統5。4         | Mbed TLS 2                   | [MXChip IoT 開發工具組](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 或 ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT 中樞 Python 裝置 SDK](https://github.com/Azure/azure-iot-sdk-python)會使用進行測試，並支援下列設定。

| OS                  | 編譯器                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7. *、3.5 或更新版本 |
| MacOS High Sierra   | Python 2.7. *、3.5 或更新版本 |
| Windows 10 系列   | Python 2.7. *、3.5 或更新版本 |

僅 Python 版本3.5.3 或更新版本支援非同步 Api，建議使用3.7 版或更新版本。

### <a name="net-sdk"></a>.NET SDK

[Azure IoT 中樞 .net （c #）裝置 SDK](https://github.com/Azure/azure-iot-sdk-csharp)會使用進行測試，並支援下列設定。

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Desktop 和伺服器 Sku   | .NET Core 2.1、.NET Framework 4.5.1 或 .NET Framework 4。7 |

.NET SDK 也可以搭配使用 Windows IoT Core 與[Azure 裝置代理程式](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md)，或可[使用 RPC 與 UWP 應用程式通訊的自訂 NTService](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)。

### <a name="nodejs-sdk"></a>Node.js SDK

Azure IoT 中樞的 node.js[裝置 SDK](https://github.com/Azure/azure-iot-sdk-node)會使用進行測試，並支援下列設定。

| OS                  | 節點版本    |
|---------------------|-----------------|
| Linux               | LTS 和 Current |
| Windows 10 系列   | LTS 和 Current |

### <a name="java-sdk"></a>Java SDK

[Azure IoT 中樞 JAVA 裝置 SDK](https://github.com/Azure/azure-iot-sdk-java)會使用進行測試，並支援下列設定。

| OS                     | Java 版本 |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 系列 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>合作夥伴支援的開發工具組

Microsoft 與各種合作夥伴合作，為數個微處理器架構提供開發工具組。 這些合作夥伴已將 Azure IoT C SDK 移植到其平臺。 合作夥伴會建立並維護 SDK 的平臺抽象層（PAL）。 Microsoft 會與這些合作夥伴合作，以提供延伸支援。

| 合作夥伴             | 裝置                            | 連結                     | 支援 |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE for IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [論壇](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 系列 <br/> STM32F4 系列 <br/>  STM32F7 系列 <br/>  適用於 IoT 節點的 STM32L4 Discovery Kit    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [支援](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF 啟動控制板 </br> CC3220S 啟動控制板 </br> CC3235SF 啟動控制板 </br> CC3235S 啟動控制板 </br> MSP432E4 啟動控制板 | [適用於 SimpleLink 的 Azure IoT 外掛程式](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E 論壇](https://e2e.ti.com) <br/> [CC3220 的 TI E2E 論壇](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4 的 TI E2E 論壇](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>移植 Microsoft Azure 的 IoT C SDK

如果您的裝置平臺未涵蓋在先前的其中一節中，您可以考慮移植 Azure IoT C SDK。 移植 C SDK 主要牽涉到執行 SDK 的平臺抽象層（PAL）。 PAL 會定義基本專案，以提供您的裝置與 SDK 中較高層級函式之間的粘連。 如需詳細資訊，請參閱[移植指引](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft 合作夥伴和認證的 Azure IoT 裝置

Microsoft 與數個合作夥伴合作，以透過 Azure IoT 測試和認證的裝置持續擴展 Azure IoT universe。

* 若要流覽 Azure IoT 認證的裝置，請參閱[Microsoft Azure IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)。

* 若要深入瞭解 Azure IoT 認證生態系統，請參閱[加入 IoT 認證生態系統](https://catalog.azureiotsolutions.com/register)。

## <a name="connecting-to-iot-hub-without-an-sdk"></a>不使用 SDK 連接到 IoT 中樞

如果您無法使用其中一個 IoT 中樞裝置 Sdk，您可以從任何可傳送和接收 HTTPS 要求和回應的應用程式，使用[IOT 中樞 REST api](https://docs.microsoft.com/rest/api/iothub/)直接連接到 IoT 中樞。

## <a name="support-and-other-resources"></a>支援和其他資源

如果您在使用 Azure IoT 裝置 Sdk 時遇到問題，有幾種方式可以尋求支援。 您可以嘗試下列其中一個通道：

**報告 bug** -裝置 sdk 中的錯誤可以在相關 GitHub 專案的 [問題] 頁面上報告。 我們也會快速地從專案中找出修正方式，並加入產品更新中。

* [Azure IoT 中樞 C SDK 問題](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT 中樞 .NET （c #） SDK 問題](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT 中樞 JAVA SDK 問題](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT 中樞 node.js SDK 問題](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT 中樞 Python SDK 問題](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft 客戶支援小組**–具有[支援方案](https://azure.microsoft.com/support/plans/)的使用者可以直接從[Azure 入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)建立新的支援要求，以與 Microsoft 客戶支援小組接洽。

**功能要求**– Azure IoT 功能要求會透過產品的 [[使用者心聲] 頁面](https://feedback.azure.com/forums/321918-azure-iot)來追蹤。

## <a name="next-steps"></a>後續步驟

* [裝置與服務 SDK](iot-hub-devguide-sdks.md)
* [移轉指引](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
