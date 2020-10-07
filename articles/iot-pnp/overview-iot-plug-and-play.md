---
title: IoT 隨插即用簡介 | Microsoft Docs
description: 了解 IoT 隨插即用。 IoT 隨插即用以開放式模型語言為基礎，可讓智慧型 IoT 裝置宣告其功能。 當 IoT 裝置連線到雲端解決方案時，會出現該宣告並呼叫裝置型號。 接著，雲端解決方案可自動了解裝置並開始與其互動；您不需要撰寫任何程式碼。
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 3f74c593cd44470efd231578fddcf53715a3979a
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575090"
---
# <a name="what-is-iot-plug-and-play"></a>什麼是 IoT 隨插即用？

IoT 隨插即用可讓解決方案建立器將智慧型裝置整合到解決方案中，而不需手動設定。 IoT 隨插即用的核心是裝置「型號」，裝置會使用此型號向已啟用 IoT 隨插即用的應用程式公告其功能。 此模型是結構化的一組元素，可定義：

- 代表裝置或其他實體的唯讀或可寫入狀態的_屬性_。 例如，裝置序號可能是唯讀屬性，而控溫器上的目標溫度可能是可寫入屬性。
- 屬於裝置所發出之資料的_遙測_，無論這項資料是感應器讀數的一般串流、偶爾發生的錯誤，還是資訊訊息。
- 說明可在裝置上完成之函式或作業的_命令_。 例如，命令可以重新啟動閘道或使用遠端相機拍照。

您可以將介面中的這些元素分組跨裝置重複使用，以簡化共同作業及加速開發工作。

為了讓 IoT 隨插即用與 [Azure Digital Twins](../digital-twins/about-digital-twins.md) 搭配使用，您可以使用 [Digital Twins 定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 定義模型和介面。 IoT 隨插即用和 DTDL 開放給社群使用，且 Microsoft 歡迎客戶、合作夥伴和業界共同合作。 這兩者都是以開放式 W3C 標準 (例如, JSON-LD 和 RDF) 為基礎，可讓您更輕鬆地跨服務和工具加以採用。

使用 IoT 隨插即用和 DTDL 並不會產生額外費用。 [Azure IoT 中樞](../iot-hub/about-iot-hub.md)和其他 Azure 服務的標準費率維持不變。

本文概述：

- 與使用 IoT 隨插即用的專案相關聯的一般角色。
- 如何在您的應用程式中使用 IoT 隨插即用裝置。
- 如何開發支援 IoT 隨插即用的 IoT 裝置應用程式。

## <a name="user-roles"></a>使用者角色

IoT 隨插即用適用於兩種類型的開發人員：

- _解決方案建置人員_負責使用 Azure IoT 中樞和其他 Azure 資源開發 IoT 解決方案，以及識別要整合的 IoT 裝置。
- _裝置建置人員_會建立可在連線到解決方案的裝置上執行的程式碼。

## <a name="use-iot-plug-and-play-devices"></a>使用 IoT 隨插即用裝置

身為解決方案建置人員，您可以開發使用 IoT 隨插即用裝置的雲端裝載 IoT 解決方案。 使用 [IoT 中樞](../iot-hub/about-iot-hub.md) - 一個受控雲端服務，可作為您的 IoT 應用程式與裝置之間進行安全的雙向通訊時的訊息中樞。

當您將 IoT 隨插即用裝置連線到 IoT 中樞時，可以使用 [Azure IoT 總管](./howto-use-iot-explorer.md)工具來檢視在組成模型的介面中定義的遙測、屬性和命令。

如果您有現有的感應器連接至 Windows 或 Linux 閘道，則可以使用 [IoT 隨插即用橋接器](./concepts-iot-pnp-bridge.md)來連接這些感應器，並建立 IoT 隨插即用裝置，而不需要撰寫裝置軟體/韌體 (適用於[支援的通訊協定](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors))。

## <a name="develop-an-iot-device-application"></a>開發 IoT 裝置應用程式

身為裝置建置人員，您可以開發支援 IoT 隨插即用的 IoT 硬體產品。 此程序包括三個主要步驟：

1. 定義裝置型號。 您會使用 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl) 撰寫一組 JSON 檔案，用以定義裝置的功能。 模型會說明完整實體 (例如實體產品)，並定義該實體所實作的介面集。 介面是共用的合約，可唯一識別裝置所支援的遙測資料、屬性和命令。 介面可以跨不同的模型重複使用。

1. 遵循 IoT 隨插即用慣例的方式，以其遙測、屬性和命令撰寫裝置軟體或韌體。 如果您要連接的現有感應器已連接至 Windows 或 Linux 閘道，[IoT 隨插即用橋接器](./concepts-iot-pnp-bridge.md)可以簡化此步驟。

1. 裝置會在 MQTT 連線中公告模型識別碼。 Azure IoT SDK 包含新的結構，可在連線時提供模型識別碼。

> [!Important]
> IoT 隨插即用裝置必須使用 MQTT 或透過 WebSocket 的 MQTT。 其他通訊協定 (例如 AMQP 或 HTTP) 無效，無法實作 IoT 隨插即用裝置。

## <a name="device-certification"></a>裝置認證

[IoT 隨插即用裝置認證方案](howto-certify-device.md)會驗證裝置是否符合 IoT 隨插即用認證需求。 您可以將已認證的裝置新增至 [Azure IoT 裝置目錄的公開認證](https://aka.ms/devicecatalog)。

## <a name="regional-availability"></a>區域可用性

本 IoT 隨插即用重新整理目前可在美國中部、北歐和日本東部區域中已建立的 IoT 中樞上使用。

## <a name="next-steps"></a>後續步驟

現在您已大致了解 IoT 隨插即用，建議執行的下一個步驟是嘗試進行其中一個快速入門：

- [將裝置連線到 IoT 中樞 (C)](./quickstart-connect-device-c.md)
- [從您的解決方案與裝置互動 (Node.js)](./quickstart-service-node.md)

