---
title: IoT 隨插即用預覽版簡介 | Microsoft Docs
description: 了解 IoT 隨插即用預覽版。 IoT 隨插即用以開放式模型語言為基礎，可讓 IoT 裝置宣告其功能。 當 IoT 裝置連線到雲端解決方案 (例如 Azure IoT Central 或合作夥伴應用程式) 時，即會出現該宣告，名為裝置功能模型。 接著，雲端解決方案可自動了解裝置並開始與其互動 — 您不需要撰寫任何程式碼。
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770443"
---
# <a name="what-is-iot-plug-and-play-preview"></a>什麼是 IoT 隨插即用預覽版？

IoT 隨插即用預覽版可讓解決方案開發人員將裝置與解決方案整合，而不需要撰寫任何內嵌程式碼。 IoT 隨插即用的核心是說明裝置功能的_裝置功能模型_結構描述。 此架構是一個 JSON 文件，它結構化為一組介面，其中包含下列項目的定義：

- 代表裝置或其他實體的唯讀和讀取/寫入狀態的_屬性_。 例如，裝置序號可能是唯讀屬性，而控溫器上的目標溫度可能是讀取/寫入屬性。
- 屬於裝置所發出之資料的_遙測_，無論這項資料是感應器讀數的一般串流、偶爾發生的錯誤，還是資訊訊息。
- 說明可在裝置上完成之函式或作業的_命令_。 例如，命令可以重新啟動閘道或使用遠端相機拍照。

您可以跨裝置功能模型重複使用介面，以簡化共同作業及加速開發工作。

為了讓 IoT 隨插即用與 [Azure 數位對應項](../digital-twins/about-digital-twins.md)順暢地搭配使用，IoT 隨插即用結構描述採用[數位對應項定義語言 (DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) 進行定義。 IoT 隨插即用和 DTDL 開放給社群使用，且 Microsoft 歡迎客戶、合作夥伴和業界共同合作。 這兩者都是以開放式 W3C 標準 (例如, JSON-LD 和 RDF) 為基礎，可讓您更輕鬆地跨服務和工具加以採用。 此外，使用 IoT 隨插即用和 DTDL 並不會產生額外費用。 [Azure IoT 中樞](../iot-hub/about-iot-hub.md)、[Azure IoT Central](../iot-central/core/overview-iot-central.md) 和其他 Azure 服務的標準費率維持不變。

在 IoT 中樞或 IoT Central 上建置的解決方案，可受益於 IoT 隨插即用。

本文概述：

- 與使用 IoT 隨插即用的專案相關聯的一般角色。
- 如何在您的應用程式中使用 IoT 隨插即用裝置。
- 如何開發支援 IoT 隨插即用的 IoT 裝置應用程式。
- 如何認證 IoT 隨插即用裝置，並發佈至 [IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)。

## <a name="user-roles"></a>使用者角色

IoT 隨插即用適用於兩種類型的開發人員：

- _解決方案開發人員_負責使用 Azure IoT 和其他 Azure 資源開發 IoT 解決方案，以及識別要整合的 IoT 裝置。
- _裝置開發人員_會建立可在連線到解決方案的裝置上執行的程式碼。

## <a name="use-iot-plug-and-play-devices"></a>使用 IoT 隨插即用裝置

身為解決方案開發人員，您可以開發使用 IoT 隨插即用裝置的雲端裝載 IoT 解決方案。 您可以使用下列其中一項 Azure 服務：

- [IoT Central](../iot-central/core/overview-iot-central.md) - 一個完全受控的 IoT 軟體即服務解決方案，可讓您輕鬆地建立連接實體與數位世界的產品。
- [IoT 中樞](../iot-hub/about-iot-hub.md) - 一個受控雲端服務，可作為您的 IoT 應用程式與裝置之間進行安全的雙向通訊時的訊息中樞。

您可以透過 Azure IoT 認證裝置目錄來尋找 IoT 隨插即用裝置。 此目錄中的每個 IoT 隨插即用裝置都已經過驗證，且具有裝置功能模型。 請檢視裝置功能模型以了解裝置的功能，或用它來模擬 Azure IoT Central 中的裝置。

當您連接 IoT 隨插即用裝置時，您可以檢視其裝置功能模型、模型中包含的介面，以及這些介面中定義的遙測資料、屬性和命令。

## <a name="develop-an-iot-device-application"></a>開發 IoT 裝置應用程式

身為裝置開發人員，您可以開發支援 IoT 隨插即用的 IoT 硬體產品。 此程序涉及兩個主要步驟：

1. 定義裝置功能模型和介面。 您會使用 [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) 撰寫一組 JSON 檔案，用以宣告裝置的功能。 裝置功能模型會說明完整實體 (例如實體產品)，並定義該實體所實作的介面集。 介面是共用的合約，可唯一識別裝置所支援的遙測資料、屬性和命令。 介面可以跨不同的裝置功能模型重複使用。

1. 撰寫裝置軟體或韌體，以實作裝置功能模型和介面中宣告的功能。 Azure IoT SDK 包含用來實作裝置功能模型的 API。

[適用於 VS Code 的 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) \(英文\) 擴充套件提供了許多功能來協助您。 例如，身為裝置開發人員，您可以使用此延伸模組從功能模型產生基本架構 C 專案。 不過，您可以使用任何 IDE 來製作和實作裝置功能模型。

## <a name="certify-an-iot-plug-and-play-device"></a>認證 IoT 隨插即用裝置

身為裝置開發人員，您可以提交 IoT 硬體產品以進行認證。 您可以在 IoT 認證裝置目錄中發佈已認證的裝置。 認證程序的步驟包括：

- 加入 [Microsoft 合作夥伴網路](https://partner.microsoft.com)。
- 上線至 Azure IoT 認證入口網站。
- 提交 IoT 隨插即用裝置功能模型和行銷資訊，以建立新的裝置記錄。
- 為裝置傳入自動化的驗證測試集。
- 發佈至 IoT 認證裝置目錄。

## <a name="regional-availability"></a>區域可用性

在公開預覽期間，IoT 隨插即用適用於所有區域。

## <a name="message-quotas-in-iot-hub"></a>IoT 中樞的訊息配額
在公開預覽期間，IoT 隨插即用裝置會為每個介面傳送個別的訊息，這可能會增加您的[訊息配額](../iot-hub/iot-hub-devguide-quotas-throttling.md)中計入的訊息數目。

## <a name="next-steps"></a>後續步驟

現在您已大致了解 IoT 隨插即用，建議執行的下一個步驟是嘗試進行其中一個快速入門：

- [使用裝置功能模型來建立 IoT 隨插即用裝置](./quickstart-create-pnp-device-windows.md)
- [將裝置連線到 IoT 中樞](./quickstart-connect-pnp-device-c-windows.md)
- [連線到解決方案中的裝置](./quickstart-connect-pnp-device-solution-node.md)
