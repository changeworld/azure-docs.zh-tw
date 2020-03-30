---
title: Azure IoT 裝置 SDK 平台支援 | Microsoft Docs
description: 開源設備 SDK 在 C、.NET （C#）、JAVA、Node.js 和 Python 中的 GitHub 上可用，用於將設備連接到 Azure IoT 中心和設備佈建服務 （DPS）。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045129"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT 設備 SDK 平臺支援

Microsoft 努力不斷擴展支援 Azure IoT 集線器的設備。 Microsoft 在 GitHub 上發佈開源設備 SDK，以説明將設備連接到 Azure IoT 中心和設備佈建服務。 設備 SDK 可用於 C、.NET （C#）、JAVA、Node.js 和 Python。 Microsoft 測試每個 SDK，以確保它在[Microsoft SDK 和設備平臺支援](#microsoft-sdks-and-device-platform-support)部分中詳細介紹的受支援的配置上運行。

除了設備 SDK 之外，Microsoft 還提供其他幾條途徑，使客戶和開發人員能夠將其設備連接到 Azure IoT：

* Microsoft 與多家合作夥伴公司合作，説明他們發佈基於 Azure IoT C SDK 的硬體平臺開發工具組。

* Microsoft 與 Microsoft 受信任的合作夥伴合作，提供一組已經過 Azure IoT 測試和認證的不斷擴大的設備。 有關這些設備的當前清單，請參閱 Azure[認證的 IoT 設備目錄](https://catalog.azureiotsolutions.com/)。

* Microsoft 在 Azure IoT 中心設備 C SDK 中提供了一個平臺抽象層 （PAL），可説明開發人員輕鬆地將 SDK 移植到其平臺。 要瞭解更多資訊，請參閱[C SDK 移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

本主題提供有關 Microsoft SDK 及其支援的平台佈建以及上面列出的其他每個選項的資訊。

## <a name="microsoft-sdks-and-device-platform-support"></a>微軟 SDK 和設備平臺支援

Microsoft 在 GitHub 上發佈針對以下語言的開源 SDK：C、.NET （C#）、Node.js、JAVA 和 Python。 本節列出了 SDK 及其依賴項。 SDK 在滿足這些依賴關係的任何設備平臺上都受支援。

對於列出的每個 SDK，Microsoft：

* 在多個流行的平臺上，針對 GitHub 中相關 SDK 的主分支援續構建和運行端到端測試。  為了跨不同的編譯器版本提供測試覆蓋率，我們通常根據最新的 LTS 版本和最流行的版本進行測試。

* 提供安裝指南或安裝包（如果適用）。

* 完全支援 GitHub 上的 SDK，包括開原始程式碼、客戶貢獻路徑以及與 GitHub 問題的產品團隊互動。

### <a name="c-sdk"></a>C SDK

[Azure IoT 中心 C 設備 SDK](https://github.com/Azure/azure-iot-sdk-c)已使用以下配置進行測試和支援。

| OS                  | TLS 程式庫                  | 其他需求                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | 開放SSL、WolfSSL 或熊 SSL | 伯克利插座</br></br>可擕式作業系統介面 （POSIX）                       |
| iOS 12.2            | OpenSSL                      | 在 OSX 10.13.4 中類比 X 碼                                                               |
| 視窗 10 系列   | SChannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [MXChip 物聯網開發套件](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure 球體作業系統     | 沃爾夫SSL                      | [Azure 球體 MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | 熊SSL                      | [ESP32 或 ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT 中心 Python 設備 SDK](https://github.com/Azure/azure-iot-sdk-python)已使用以下配置進行測試和支援。

| OS                  | 編譯器                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*，3.5 或更高版本 |
| MacOS High Sierra   | Python 2.7.*，3.5 或更高版本 |
| 視窗 10 系列   | Python 2.7.*，3.5 或更高版本 |

只有 Python 版本 3.5.3 或更高版本支援非同步 API，我們建議使用版本 3.7 或更高版本。

### <a name="net-sdk"></a>.NET SDK

[Azure IoT 中心 .NET （C#） 設備 SDK](https://github.com/Azure/azure-iot-sdk-csharp)已使用以下配置進行測試並支援。

| OS                                   | 標準                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 桌面和伺服器 SKU   | .NET 核心 2.1、.NET 框架 4.5.1 或 .NET 框架 4.7 |

.NET SDK 還可用於 Windows IoT 酷睿與[Azure 設備代理](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md)或[自訂 NT 服務，可以使用 RPC 與 UWP 應用程式進行通信](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)。

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT 中心 Node.js 設備 SDK](https://github.com/Azure/azure-iot-sdk-node)已使用以下配置進行測試並支援。

| OS                  | 節點版本    |
|---------------------|-----------------|
| Linux               | LTS 和電流 |
| 視窗 10 系列   | LTS 和電流 |

### <a name="java-sdk"></a>Java SDK

[Azure IoT 中心 JAVA 設備 SDK](https://github.com/Azure/azure-iot-sdk-java)已使用以下配置進行測試和支援。

| OS                     | Java 版本 |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| 視窗 10 系列 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>合作夥伴支援的開發工具組

Microsoft 與多個合作夥伴合作，為多個微處理器架構提供開發套件。 這些合作夥伴將 Azure IoT C SDK 移植到其平臺。 合作夥伴創建和維護 SDK 的平臺抽象層 （PAL）。 Microsoft 與這些合作夥伴合作提供擴展支援。

| Partner             | 裝置                            | 連結                     | 支援 |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE for IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [論壇](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 系列 <br/> STM32F4 系列 <br/>  STM32F7 系列 <br/>  適用於 IoT 節點的 STM32L4 Discovery Kit    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [支援](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF 啟動板 </br> CC3220S啟動板 </br> CC3235SF 啟動板 </br> CC3235S 啟動板 </br> MSP432E4 啟動板 | [適用於 SimpleLink 的 Azure IoT 外掛程式](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E 論壇](https://e2e.ti.com) <br/> [CC3220 的 TI E2E 論壇](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4 的 TI E2E 論壇](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>移植微軟 Azure IoT C SDK

如果設備平臺未由前面的部分之一涵蓋，則可以考慮移植 Azure IoT C SDK。 移植 C SDK 主要涉及實現 SDK 的平臺抽象層 （PAL）。 PAL 定義提供設備與 SDK 中更高級別功能之間的粘合基元。 有關詳細資訊，請參閱[移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>微軟合作夥伴和經過認證的 Azure IoT 設備

Microsoft 與多個合作夥伴合作，通過 Azure IoT 測試和認證設備不斷擴展 Azure IoT 領域。

* 要流覽 Azure IoT 認證設備，請參閱[Microsoft Azure 認證的 IoT 設備目錄](https://catalog.azureiotsolutions.com/)。

* 要瞭解有關 Azure 認證 IoT 生態系統的更多詳細資訊，請參閱[加入 IoT 認證生態系統](https://catalog.azureiotsolutions.com/register)。

## <a name="connecting-to-iot-hub-without-an-sdk"></a>無需 SDK 即可連接到 IoT 中心

如果您無法使用 IoT 中心設備 SDK 之一，則可以使用[IoT 中心 REST API](https://docs.microsoft.com/rest/api/iothub/)直接從能夠發送和接收 HTTPS 請求和回應的應用程式連接到 IoT 中心。

## <a name="support-and-other-resources"></a>支援和其他資源

如果在使用 Azure IoT 設備 SDK 時遇到問題，則有多種方法可以尋求支援。 您可以嘗試以下頻道之一：

**報告 Bug** – 可以在相關 GitHub 專案的問題頁上報告設備 SDK 中的 Bug。 我們也會快速地從專案中找出修正方式，並加入產品更新中。

* [Azure IoT 中心 C SDK 問題](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT 中心 .NET （C#） SDK 問題](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT 中心 JAVA SDK 問題](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT 中心節點.js SDK 問題](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT 中心 Python SDK 問題](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft 客戶支援小組**– 具有[支援計畫](https://azure.microsoft.com/support/plans/)的使用者可以通過直接從[Azure 門戶](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)創建新的支援請求來聯繫 Microsoft 客戶支援小組。

**功能請求**– Azure IoT 功能請求通過產品的["使用者語音"頁](https://feedback.azure.com/forums/321918-azure-iot)進行跟蹤。

## <a name="next-steps"></a>後續步驟

* [裝置與服務 SDK](iot-hub-devguide-sdks.md)
* [移轉指引](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
