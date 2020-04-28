---
title: Azure 物聯網 (IoT) 簡介
description: 說明 Azure IoT 與 IoT 服務基本概念的簡介，包括協助說明 IoT 用法的範例。
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73eb0b3164a386bb270e42ceba56d5dc7045af1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728997"
---
# <a name="what-is-azure-internet-of-things-iot"></a>什麼是 Azure 物聯網 (IoT)？

Azure 物聯網 (IoT) 是由 Microsoft 管理的雲端服務集合，它會連線、監視及控制數十億個 IoT 資產。 簡單地說，IoT 解決方案是由一或多個 IoT 裝置所組成，而這些裝置會與裝載在雲端一或多個後端服務通訊。 

## <a name="iot-devices"></a>IoT 裝置

IoT 裝置通常是由已連接感應器的電路板所組成，而感應器會使用 WiFi 連線至網際網路。 例如：

* 遠端油泵上的壓力感應器。
* 空調機組中的溫度和溼度感應器。
* 電梯中的加速計。
* 房間中的狀態感應器。

不同的製造商可以使用各式各樣的裝置來打造您的解決方案。 如需已證實可與 Azure IoT 中樞搭配運作的裝置清單，請參閱 [Azure IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/alldevices)。 若要進行原型設計，您可以使用 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 或 [Raspberry Pi](https://www.raspberrypi.org/)等裝置。 Devkit 具有溫度、壓力、濕度的內建感應器，以及迴轉儀、加速計和磁力計。 Raspberry Pi 可讓您附加許多不同類型的感應器。 

Microsoft 提供開放原始碼的[裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)，可讓您用來建置在裝置上執行的應用程式。 這些 [SDK 會簡化並加速](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) IoT 解決方案的開發。

## <a name="communication"></a>通訊

通常，IoT 裝置會將來自感應器的遙測傳送至雲端的後端服務。 不過，可能會有其他類型的通訊，例如將命令傳送至您的裝置的後端服務。 以下是裝置到雲端和雲端到裝置通訊的一些範例：

* 行動冷藏車會每隔 5 分鐘將溫度傳送至 IoT 中樞。 

* 後端服務會將命令傳送至裝置，以變更其傳送遙測的頻率，進而協助診斷問題。 

* 裝置會根據其感應器中的讀取值來傳送警示。 例如，正在監視化學工廠中批次反應器的裝置，會在溫度超過特定值時傳送警示。

* 您的裝置會傳送資訊以在儀表板上顯示，以便操作人員進行檢視。 例如，煉油廠的控制室可能會顯示每個輸送管的溫度、壓力和流量，讓操作員能夠監視設備。 

[IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md) 和 IoT 中樞可支援常見的[通訊協定](../iot-hub/iot-hub-devguide-protocols.md)，例如 HTTP、MQTT 和 AMQP。

相較於其他用戶端 (例如瀏覽器和行動應用程式)，IoT 裝置有不同的特性。 裝置 SDK 可協助您解決將裝置安全且可靠地連線到後端服務的挑戰。  具體來說，IoT 裝置有以下特性：

* 通常是無人操作的嵌入式系統 (不同於手機)。
* 可以部署於實體存取成本昂貴的遠端位置。
* 可能只能透過解決方案後端來存取。
* 能力和/或處理資源可能都有限。
* 網路連線能力可能不穩定、緩慢或昂貴。
* 可能需要使用專屬、自訂或業界特定的應用程式通訊協定。

## <a name="back-end-services"></a>後端服務 

在 IoT 解決方案中，後端服務會提供如下的功能：

* 大規模接收來自裝置的遙測資料，並判斷如何處理與儲存該資料。
* 分析遙測資料並提供見解 (即時或事後)。
* 將命令從雲端傳送到特定裝置。 
* 佈建裝置並控制哪些裝置可連接到您的基礎結構。
* 控制裝置狀態並監視其活動。
* 管理您的裝置上安裝的韌體。

例如，在石油泵站的遠端監視解決方案中，雲端後端會使用來自油泵的遙測來識別異常行為。 當後端服務發現異常時，其可自動將命令傳送回裝置，以採取矯正措施。 此程序會在裝置與雲端間產生自動回應迴圈，大幅提升解決方案的效率。

## <a name="azure-iot-examples"></a>Azure IoT 範例

如需組織如何使用 IoT 的實際範例，請參閱 [IoT 的 Microsoft 技術案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。 

## <a name="next-steps"></a>後續步驟

如需一些實際的商業案例及所使用的架構資訊，請參閱 [Microsoft Azure IoT 技術案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。

如需可透過 IoT DevKit 嘗試完成的一些範例專案，請參閱 [IoT DevKit 專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。 

如需不同服務和其使用方式的更完整說明，請參閱 [Azure IoT 服務與技術](iot-services-and-technologies.md)。

如需 IoT 架構的深入討論，請參閱 [Microsoft Azure IoT 參考架構](https://aka.ms/iotrefarchitecture)。
