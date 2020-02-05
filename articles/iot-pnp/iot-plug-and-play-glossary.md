---
title: 詞彙詞彙-IoT 隨插即用預覽 |Microsoft Docs
description: 概念-與 IoT 隨插即用 Preview 相關的常見詞彙詞彙。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ef5ce9cc1cda7f1ff6b1985771e20cb20123e264
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025634"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 隨插即用預覽詞彙詞彙

IoT 隨插即用文章中所使用之常見詞彙的定義。

## <a name="azure-certified-for-iot-portal"></a>Azure IoT 認證入口網站

您可以使用[Azure IoT 認證入口](https://aka.ms/ACFI)網站來執行下列動作：

- 完成[IoT 隨插即用裝置](#iot-plug-and-play-device)的[認證](#device-certification)程式。
- 尋找[裝置功能模型](#device-capability-model)。
- 將裝置功能模型發佈至[公用模型存放庫](#public-model-repository)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是用來管理 Azure 資源的跨平臺命令列工具。 適用于 Azure CLI 的 Azure IoT 擴充功能是用來與[IoT 隨插即用裝置](#iot-plug-and-play-device)互動和測試的命令列工具。 您可以使用擴充功能來執行下列動作：

- 連接到 IoT 隨插即用裝置。
- 查看裝置所傳送的[遙測](#telemetry)。
- 使用裝置[屬性](#properties)。
- 呼叫裝置[命令](#commands)。
- 管理[模型存放庫](#model-repository)、[介面](#interface)和[裝置功能模型](#device-capability-model)。

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central 是完全受控的軟體即服務解決方案，可讓您輕鬆地連線、監視及管理您的[IoT 隨插即用裝置](#iot-plug-and-play-device)。 您可以使用[裝置功能模型](#device-capability-model)來自動設定 IoT Central 應用程式，以監視和管理您的裝置。

## <a name="azure-iot-certification-service"></a>Azure IoT 認證服務

當您透過[Azure IoT 認證入口網站](#azure-certified-for-iot-portal)提交[IoT 隨插即用裝置](#iot-plug-and-play-device)以進行認證時，Azure IoT 認證服務會執行一組認證測試。 裝置必須經過認證，您才能將裝置新增至[IoT 認證裝置目錄](#certified-for-iot-device-catalog)。

## <a name="azure-iot-tools-extension"></a>Azure IoT Tools 延伸模組

Azure IoT Tools 是[Visual Studio 程式碼](#visual-studio-code)中的延伸模組集合，可協助您與 IoT 中樞和開發 IoT 裝置進行互動。 針對 IoT 隨插即用裝置開發，它可協助您：

- 撰寫[裝置功能模型](#device-capability-model)和[介面](#interface)。
- 發行至[模型存放庫](#model-repository)。
- 產生用來執行裝置應用程式的基本架構程式碼。

## <a name="azure-iot-explorer-tool"></a>Azure IoT explorer 工具

Azure IoT explorer 是一種圖形化工具，可讓您用來與[IoT 隨插即用裝置](#iot-plug-and-play-device)互動並加以測試。 在您的本機電腦上安裝此工具之後，您可以使用它來執行下列動作：

- 查看連線到您[IoT 中樞](#azure-iot-hub)的裝置。
- 連接到 IoT 隨插即用裝置。
- 查看裝置所傳送的[遙測](#telemetry)。
- 使用裝置[屬性](#properties)。
- 呼叫裝置[命令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中心

IoT 中樞是託管於雲端中的受控服務，可做為 IoT 應用程式與其管理裝置之間雙向通訊的中央訊息中樞。 [Iot 隨插即用裝置](#iot-plug-and-play-device)可以連線到 iot 中樞。 IoT 解決方案會使用 IoT 中樞來啟用：

- 用來將遙測傳送至雲端式解決方案的裝置。
- 以雲端為基礎的解決方案，可管理已連線的裝置。

## <a name="azure-iot-device-sdk"></a>Azure IoT 裝置 SDK

有多種語言的裝置 Sdk，可讓您用來建立 IoT 隨插即用裝置用戶端應用程式。 [裝置認證](#device-certification)的其中一個需求是裝置用戶端程式代碼會使用其中一個 Azure IoT 裝置 sdk。

## <a name="certified-for-iot-device-catalog"></a>IoT 認證裝置目錄

[IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)會列出已通過[裝置認證](#device-certification)測試的[IoT 隨插即用裝置](#iot-plug-and-play-device)。 IoT 隨插即用裝置在目錄中的[裝置功能模型](#device-capability-model)，併發布于公用模型存放庫中。

## <a name="commands"></a>命令

在[介面](#interface)中定義的命令代表可以在[數位](#digital-twin)對應項上執行的方法。 例如，用來重新開機裝置的命令。

## <a name="common-interface"></a>通用介面

所有[IoT 隨插即用裝置](#iot-plug-and-play-device)都應該執行一些通用[介面](#interface)。 例如，裝置資訊介面會定義裝置的硬體和作業系統資訊。 [裝置認證](#device-certification)會要求您的裝置執行數個通用介面。 您可以從公用模型存放庫取得通用介面定義。

## <a name="company-model-repository"></a>公司模型存放庫

組織可以使用公司模型存放[庫](#model-repository)作為[裝置功能模型](#device-capability-model)和[介面](#interface)的私用存放區。

## <a name="connection-string"></a>連接字串

連接字串會封裝連接到端點所需的資訊。 連接字串通常包含端點位址和安全性資訊，但連接字串的格式會因服務而不同。 IoT 中樞服務有兩種相關的連接字串︰

- 裝置連接字串可讓[iot 隨插即用裝置](#iot-plug-and-play-device)連線到 iot 中樞上的裝置面向端點。 裝置上的用戶端程式代碼會使用連接字串來建立與 IoT 中樞的安全連線。
- IoT 中樞連接字串可讓後端解決方案和工具安全地連接到 IoT 中樞上的服務面向端點。 這些解決方案和工具會管理 IoT 中樞和與其連線的裝置。
- 公司模型存放庫連接字串可讓後端解決方案和工具安全地連接到[公司模型存放庫](#company-model-repository)。 這些解決方案和工具會使用或管理存放庫中的[裝置功能模型](#device-capability-model)和[介面](#interface)。

## <a name="device-capability-model"></a>裝置功能模型

裝置功能模型會描述[IoT 隨插即用裝置](#iot-plug-and-play-device)，並定義裝置所執行的一組[介面](#interface)。 裝置功能模型通常會對應至實體裝置、產品或 SKU。 您可以使用[數位對應項定義語言](#digital-twin-definition-language)來定義裝置功能模型。

## <a name="device-certification"></a>裝置認證

裝置認證是認證[IoT 隨插即用裝置](#iot-plug-and-play-device)的程式，可將其新增至[IoT 認證裝置目錄](#certified-for-iot-device-catalog)，並將其[裝置功能模型](#device-capability-model)和[介面](#interface)新增至[公用模型存放庫](#public-model-repository)。

## <a name="device-developer"></a>裝置開發人員

裝置開發人員使用[裝置功能模型](#device-capability-model)、[介面](#interface)和[Azure iot 裝置 SDK](#azure-iot-device-sdk)來執行程式碼，以在[IoT 隨插即用裝置](#iot-plug-and-play-device)上執行。

## <a name="device-modeling"></a>裝置模型

[裝置開發人員](#device-developer)使用數位對應項[定義語言](#digital-twin-definition-language)來建立[IoT 隨插即用裝置](#iot-plug-and-play-device)的功能模型。 您可以使用模型存放庫來共用模型。 裝置開發人員可以從模型產生基本架構裝置程式碼。 [解決方案開發人員](#solution-developer)可以從模型設定 IoT 解決方案。

## <a name="device-provisioning-service"></a>裝置佈建服務

[Azure IoT Central](#azure-iot-central)使用裝置布建服務來管理所有裝置註冊和連線。 如需詳細資訊，請參閱[Azure 中的裝置連線能力 IoT Central](../iot-central/core/concepts-get-connected.md)。 您也可以使用裝置布建服務來管理裝置註冊，並聯機至 IoT 中樞型 IoT 解決方案。 如需詳細資訊，請參閱[使用 Azure IoT 中樞裝置](../iot-dps/about-iot-dps.md)布建服務布建裝置。

## <a name="device-registration"></a>裝置註冊

在[iot 隨插即用裝置](#iot-plug-and-play-device)可以連線到 iot 解決方案之前，必須先向解決方案註冊該裝置。 [Azure IoT Central](#azure-iot-central)使用裝置布建[服務](#device-provisioning-service)來管理裝置註冊。 在自訂 IoT 解決方案中，您可以在 Azure 入口網站中或以程式設計方式向 IoT 中樞註冊裝置。

## <a name="device-first"></a>裝置-第一

[Azure IoT Central](#azure-iot-central)支援裝置優先的註冊和連線案例。 在此案例中， [IoT 隨插即用裝置](#iot-plug-and-play-device)可以連接到 IoT Central 應用程式，而不需要事先註冊。 當裝置第一次連線到應用程式時，會自動進行註冊。

## <a name="digital-twin"></a>數位對應項

數位對應項是[IoT 隨插即用裝置](#iot-plug-and-play-device)的模型。 數位對應項是使用數位對應項[定義語言](#digital-twin-definition-language)來建立模型。 您可以使用[Azure IoT 裝置 sdk](#azure-iot-device-sdk) ，在執行時間與數位 twins 互動。 例如，您可以在裝置上的數位對應項中設定屬性值，SDK 就會將這項變更傳達給雲端中的 IoT 解決方案。

## <a name="digital-twin-change-events"></a>數位對應項變更事件

當[iot 隨插即用裝置](#iot-plug-and-play-device)連線到[iot 中樞](#azure-iot-hub)時，中樞可以使用其路由功能來傳送數位對應項變更的通知。 例如，每當裝置上的[屬性](#properties)值變更時，IoT 中樞可以將通知傳送至端點（例如服務匯流排佇列）。

## <a name="digital-twin-definition-language"></a>數位對應項定義語言

用來描述[IoT 隨插即用裝置](#iot-plug-and-play-device)之模型和介面的語言。 使用數位對應項[定義語言](https://aka.ms/DTDL)來描述數位對應項[的](#digital-twin)功能，並啟用 iot 平臺和 iot 解決方案以利用實體的語義。

## <a name="digital-twin-route"></a>數位對應項路由

在[IoT 中樞](#azure-iot-hub)內設定的路由，可將[數位對應項變更事件](#digital-twin-change-events)傳遞至和端點，例如服務匯流排的佇列。

## <a name="interface"></a>介面

介面會描述[IoT 隨插即用裝置](#iot-plug-and-play-device)或[數位](#digital-twin)對應項所執行的相關功能。 您可以跨不同的[裝置功能模型](#device-capability-model)重複使用介面。

## <a name="iot-hub-query-language"></a>IoT 中樞查詢語言

IoT 中樞查詢語言會用於多個用途。 例如，您可以使用語言來搜尋向 IoT 中樞[註冊的裝置](#device-registration)，或調整數位對應項[路由](#digital-twin-route)行為。

## <a name="iot-plug-and-play-device"></a>IoT 隨插即用裝置

IoT 隨插即用裝置通常是小型的獨立計算裝置，可收集資料或控制其他裝置，以及執行軟體或固件來實行[裝置功能模型](#device-capability-model)。  例如，IoT 隨插即用裝置可能是環境監視裝置，或是智慧型農業灌溉系統的控制器。 您可以撰寫雲端裝載的 IoT 解決方案，從 IoT 隨插即用裝置進行命令、控制和接收資料。 [Azure IoT 認證裝置目錄](#certified-for-iot-device-catalog)會列出可用的 IoT 隨插即用裝置。 目錄中的每個 IoT 隨插即用裝置都已經過驗證，而且具有[裝置功能模型](#device-capability-model)。

## <a name="microsoft-partner-center"></a>Microsoft 合作夥伴中心

[Microsoft 合作夥伴中心](https://docs.microsoft.com/partner-center/)是您的組織管理其與 Microsoft 的端對端關係的地方。 您需要 Microsoft 合作夥伴中心帳戶，才能在[Azure IoT 認證入口網站](#azure-certified-for-iot-portal)中認證您的[IoT 隨插即用裝置](#iot-plug-and-play-device)。

## <a name="model-discovery"></a>模型探索

當[iot 隨插即用裝置](#iot-plug-and-play-device)連線到 iot 解決方案時，解決方案可以藉由尋找[裝置功能模型](#device-capability-model)來探索裝置的功能。 裝置可以將其功能模型傳送至解決方案，或解決方案可以在[模型存放庫](#model-repository)中找到裝置功能模型。

## <a name="model-repository"></a>模型存放庫

模型存放庫會儲存[裝置功能模型](#device-capability-model)和[介面](#interface)。 有單一[公用模型存放庫](#public-model-repository)。 組織可以建立自己的組織模型存放庫。

## <a name="model-repository-rest-api"></a>模型存放庫 REST API

用來管理和與模型存放庫互動的 API。 例如，您可以使用 API 來新增[裝置功能模型](#device-capability-model)，並搜尋功能模型。

## <a name="properties"></a>屬性

屬性是在[介面](#interface)中定義的資料欄位，代表數位對應項的某些狀態。 您可以將屬性宣告為唯讀或可寫入。 唯讀屬性（例如序號）是由[IoT 隨插即用裝置](#iot-plug-and-play-device)本身上執行的程式碼所設定。  可寫入的屬性（例如警示閾值）通常是從雲端型 IoT 解決方案進行設定。

## <a name="public-model-repository"></a>公用模型存放庫

有一個可儲存[認證裝置](#device-certification)之[裝置功能模型](#device-capability-model)和[介面](#interface)的單一公用模型存放庫。 公用模型存放庫也會儲存[常用的介面](#common-interface)定義。

## <a name="registration-id"></a>註冊識別碼

註冊識別碼可唯一識別裝置布建[服務](#device-provisioning-service)中的裝置。 此識別碼與裝置識別碼不同，這是[IoT 中樞](#azure-iot-hub)內裝置的唯一識別碼。

## <a name="scope-id"></a>範圍識別碼

範圍識別碼範圍會唯一識別裝置布建[服務](#device-provisioning-service)實例。

## <a name="shared-access-signature"></a>共用存取簽章

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 共用存取簽章驗證有兩個元件：共用存取原則和共用存取簽章（通常稱為權杖）。 [Iot 隨插即用裝置](#iot-plug-and-play-device)會使用共用存取簽章來向[IoT 中樞](#azure-iot-hub)進行驗證。

## <a name="solution-developer"></a>解決方案開發人員

解決方案開發人員會建立解決方案後端。 解決方案開發人員通常會使用 Azure 資源，例如[IoT 中樞](#azure-iot-hub)和[模型存放庫](#model-repository)，或與[IoT Central](#azure-iot-central)搭配運作。

## <a name="telemetry"></a>遙測

在[介面](#interface)中定義的遙測欄位代表測量。 這些測量通常是[IoT 隨插即用裝置](#iot-plug-and-play-device)傳送做為資料流程的感應器讀數之類的值。

## <a name="visual-studio-code"></a>Visual Studio 程式碼

Visual Studio 程式碼是適用于多個平臺的現代化程式碼編輯器。 擴充功能（例如[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)套件中的延伸模組）可讓您自訂編輯器，以支援各種不同的開發案例。
