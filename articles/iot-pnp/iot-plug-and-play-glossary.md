---
title: 詞彙詞彙-IoT 隨插即用 |Microsoft Docs
description: 概念-與 IoT 隨插即用相關的常見詞彙術語。
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d44866e2d04ab1bab5d2eca01374350a7d73a0ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577334"
---
# <a name="glossary-of-terms-for-iot-plug-and-play"></a>IoT 隨插即用的詞彙

IoT 隨插即用文章中使用的常見術語定義。

## <a name="azure-iot-explorer-tool"></a>Azure IoT 總管工具

Azure IoT 總管是一種圖形化工具，其可用來與 [IoT 隨插即用裝置](#iot-plug-and-play-device)互動並加以測試。 當在本機電腦上安裝此工具之後，即可使用它來執行下列動作：

- 檢視連線到 [IoT 中樞](#azure-iot-hub)的裝置。
- 連線到 IoT 隨插即用裝置。
- 查看裝置 [元件](#component)。
- 檢視裝置所傳送的[遙測](#telemetry)。
- 使用裝置[屬性](#properties)。
- 呼叫裝置[命令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中樞

IoT 中樞是託管於雲端中的受控服務，可做為 IoT 應用程式與其管理裝置之間雙向通訊的中央訊息中樞。 [IoT 隨插即用裝置](#iot-plug-and-play-device)可連線到 IoT 中樞。 IoT 解決方案使用 IoT 中樞讓：

- 裝置將遙測傳送至雲端式解決方案。
- 雲端式解決方案管理已連線的裝置。

## <a name="azure-iot-device-sdk"></a>Azure IoT 裝置 SDK

您可使用多種語言版本的裝置 SDK 來建置 IoT 隨插即用裝置用戶端應用程式。 針對裝置使用 **>deviceclient** ，並針對模組和 IoT Edge 模組使用 **ModuleClient** 。

## <a name="commands"></a>命令

在[介面](#interface)中所定義命令代表可在[數位分身](#digital-twin)上執行的方法。 例如，重新啟動裝置的命令。

## <a name="component"></a>元件

元件可讓您將模型 [介面](#interface) 建立成其他介面的元件。 [裝置模型](#device-model)可以結合多個介面作為元件。 例如，模型可能包含 switch 元件和控溫器元件。 模型中的多個元件也可以使用相同的介面類別型。 例如，模型可能包含兩個控溫器元件。

## <a name="connection-string"></a>連接字串

連接字串會封裝連線到端點所需的資訊。 連接字串通常包含端點位址和安全性資訊，但連接字串的格式會因服務而不同。 IoT 中樞服務有兩種相關的連接字串︰

- 裝置連接字串可讓 [IoT 隨插即用裝置](#iot-plug-and-play-device)連線到 IoT 中樞上的裝置對向端點。 裝置上用戶端程式碼會使用連接字串來建立與 IoT 中樞的安全連線。
- IoT 中樞連接字串可讓後端解決方案和工具安全地連線到 IoT 中樞上的服務對向端點。 這些解決方案和工具會管理 IoT 中樞和連線的裝置。

## <a name="default-component"></a>預設元件

所有 [裝置型號](#device-model) 都有預設元件。 簡單的裝置型號只有預設元件，這類模型也稱為「沒有元件裝置」。 更複雜的模型會在預設元件底下嵌套多個元件。

## <a name="device-certification"></a>裝置認證

IoT 隨插即用裝置認證方案會驗證裝置是否符合 IoT 隨插即用認證需求。 您可以將已認證的裝置新增至 [Azure IoT 裝置目錄的公開認證](https://aka.ms/devicecatalog)。

## <a name="device-model"></a>裝置型號

裝置模型會描述 [IoT 隨插即用裝置](#iot-plug-and-play-device) ，並定義組成裝置的 [元件](#component) 。 簡單的裝置模型沒有個別的元件，而且包含單一介面的定義。 Azure IoT explorer 工具會顯示簡單的模型，作為單一 [預設元件](#default-component)。

更複雜的裝置模型包含多個元件。 裝置型號通常會對應至實體裝置、產品或 SKU。 您可以使用 [數位 Twins 定義語言第2版](#digital-twins-definition-language) 來定義裝置型號。

## <a name="device-builder"></a>Device builder

裝置產生器會在執行程式碼以在[IoT 隨插即用裝置](#iot-plug-and-play-device)上執行時，使用[裝置模型](#device-model)和[介面](#interface)。 裝置構建者通常會使用其中一個 [Azure IoT 裝置 sdk](#azure-iot-device-sdk) 來執行裝置用戶端，但這並非必要。

## <a name="device-modeling"></a>裝置模型

[裝置](#device-builder)產生器或[模組](#module-builder)產生器會使用[數位 Twins 定義語言](#digital-twins-definition-language)來建立[IoT 隨插即用裝置](#iot-plug-and-play-device)的功能模型。 [解決方案](#solution-builder)產生器可以從模型設定 IoT 解決方案。

## <a name="digital-twin"></a>數位分身

數位分身是 [IoT 隨插即用裝置](#iot-plug-and-play-device)的一種模型。 數位對應項會使用 [數位 Twins 定義語言](#digital-twins-definition-language)進行模型化。 您可使用 [Azure IoT 裝置 SDK](#azure-iot-device-sdk) 在執行階段與數位分身互動。 例如，您可設定裝置上數位分身中的屬性值，SDK 會將這項變更傳達給雲端中的 IoT 解決方案。

## <a name="digital-twin-change-events"></a>數位分身變更事件

當 [IoT 隨插即用裝置](#iot-plug-and-play-device)連線到 [IoT 中樞](#azure-iot-hub)時，中樞可使用其路由功能來傳送數位分身變更的通知。 例如，每當裝置上的 [屬性](#properties) 值變更時，IoT 中樞就可以將通知傳送至端點（例如事件中樞）。

## <a name="digital-twins-definition-language"></a>數位 Twins 定義語言

一種語言，其用來描述 [IoT 隨插即用裝置](#iot-plug-and-play-device)的模型和介面。 使用 [數位 Twins 定義語言第2版](https://github.com/Azure/opendigitaltwins-dtdl) 來描述數位對應項 [的](#digital-twin) 功能，並啟用 iot 平臺和 iot 解決方案來利用實體的語法。

## <a name="digital-twin-route"></a>數位分身路由

在 [IoT 中樞](#azure-iot-hub) 中設定的路由，可將 [數位對應項變更事件](#digital-twin-change-events) 傳遞至和端點（例如事件中樞）。

## <a name="interface"></a>介面

介面描述 [IoT 隨插即用裝置](#iot-plug-and-play-device)或[數位分身](#digital-twin)所實作的相關功能。 您可以跨不同的 [裝置模型](#device-model)重複使用介面。 在裝置模型中使用介面時，它會定義裝置的 [元件](#component) 。 簡單的裝置只包含預設介面。

## <a name="iot-hub-query-language"></a>IoT 中樞查詢語言

IoT 中樞查詢語言可用於多種用途。 例如，您可使用語言來搜尋已向 IoT 中樞註冊的裝置，或調整[數位分身路由](#digital-twin-route)行為。

## <a name="iot-plug-and-play-bridge"></a>IoT 隨插即用橋接器

IoT 隨插即用橋接器是開放原始碼應用程式，可讓連接至 Windows 或 Linux 閘道的現有感應器和週邊設備以 [IoT 隨插即用裝置](#iot-plug-and-play-device)連線。

## <a name="iot-plug-and-play-device"></a>IoT 隨插即用裝置

IoT 隨插即用裝置通常是小型的獨立計算裝置，可收集資料或控制其他裝置，以及執行軟體或可執行 [裝置型號](#device-model)的固件。  例如，IoT 隨插即用裝置可能是環境監視裝置，或智慧型農業灌溉系統的控制器。 IoT 隨插即用的裝置可能會直接實作為 IoT Edge 模組。 您可撰寫雲端託管的 IoT 解決方案，從 IoT 隨插即用裝置下命令、控制和接收資料。

## <a name="iot-plug-and-play-conventions"></a>IoT 隨插即用慣例

當 IoT 隨插即用 [裝置](#iot-plug-and-play-device) 與解決方案交換資料時，應遵循一組 [慣例](concepts-convention.md) 。

## <a name="model-id"></a>模型識別碼

當 IoT 隨插即用裝置連線到 IoT 中樞時，它會傳送它所執行之[DTDL](#digital-twins-definition-language)模型的**模型識別碼**。 這可讓解決方案找出裝置型號。

## <a name="model-repository"></a>模型存放庫

[模型存放庫](concepts-model-repository.md)會儲存[裝置模型](#device-model)和[介面](#interface)。

## <a name="model-repository-rest-api"></a>模型存放庫 REST API

用來管理和與模型存放庫互動的 API。 例如，您可以使用 API 來新增和搜尋 [裝置型號](#device-model)。

## <a name="module-builder"></a>模組產生器

當執行程式碼以在[IoT 隨插即用裝置](#iot-plug-and-play-device)上執行時，模組產生器會使用[裝置模型](#device-model)和[介面](#interface)。 模組產生器會將程式碼實作為模組或 IoT Edge 模組，以部署至裝置上的 IoT Edge 執行時間。

## <a name="properties"></a>屬性

屬性是在[介面](#interface)中定義的資料欄位，其代表數位分身的某種狀態。 您可將屬性宣告為唯讀或可寫入。 唯讀屬性 (例如序號) 是由 [IoT 隨插即用裝置](#iot-plug-and-play-device)本身上執行的程式碼所設定。  可寫入的屬性 (例如警示閾值) 通常是從雲端式 IoT 解決方案進行設定。

## <a name="shared-access-signature"></a>共用存取簽章

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 共用存取簽章驗證有兩個元件：共用存取原則和共用存取簽章 (通常稱為權杖)。 [IoT 隨插即用裝置](#iot-plug-and-play-device)使用共用存取簽章向 [IoT 中樞](#azure-iot-hub)進行驗證。

## <a name="solution-builder"></a>Solution builder

解決方案產生器會建立解決方案後端。 解決方案產生器通常適用于 Azure 資源，例如 [IoT 中樞](#azure-iot-hub) 和 [模型存放庫](#model-repository)。

## <a name="telemetry"></a>遙測

在[介面](#interface)中定義的遙測欄位代表量值。 這些量值通常是 [IoT 隨插即用裝置](#iot-plug-and-play-device)所傳送作為資料流的值 (例如感應器讀數)。
