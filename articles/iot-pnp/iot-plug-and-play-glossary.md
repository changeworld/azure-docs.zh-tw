---
title: 名詞解釋 - IoT 隨插即用預覽版 | Microsoft Docs
description: 概念 - IoT 隨插即用預覽版相關常見術語詞彙。
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767061"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 隨插即用預覽版的名詞解釋

IoT 隨插即用文章中使用的常見術語定義。

## <a name="azure-certified-for-iot-portal"></a>Azure IoT 認證入口網站

您可使用 [Azure IoT 認證入口網站](https://aka.ms/ACFI)來執行下列動作：

- 完成 [IoT 隨插即用裝置](#iot-plug-and-play-device)的[認證流程](#device-certification)。
- 尋找[裝置功能模型](#device-capability-model)。
- 將裝置功能模型發佈至[公用模型存放庫](#public-model-repository)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是用來管理 Azure 資源的跨平台命令列工具。 適用於 Azure CLI 的 Azure IoT 延伸模組是一種命令列工具，可用來與 [IoT 隨插即用裝置](#iot-plug-and-play-device)互動並加以測試。 您可使用此延伸模組來執行下列動作：

- 連線到 IoT 隨插即用裝置。
- 檢視裝置所傳送的[遙測](#telemetry)。
- 使用裝置[屬性](#properties)。
- 呼叫裝置[命令](#commands)。
- 管理[模型存放庫](#model-repository)、[介面](#interface)和[裝置功能模型](#device-capability-model)。

## <a name="azure-iot-central"></a>Azure IoT 中心

Azure IoT Central 是完全受控的軟體即服務解決方案，其可供輕鬆地連線、監視和管理 [IoT 隨插即用裝置](#iot-plug-and-play-device)。 您可使用[裝置功能模型](#device-capability-model)以自動設定 IoT Central 應用程式來監視及管理裝置。

## <a name="azure-iot-certification-service"></a>Azure IoT 認證服務

當透過 [Azure IoT 認證入口網站](#azure-certified-for-iot-portal)提交 [IoT 隨插即用裝置](#iot-plug-and-play-device)進行認證時，Azure IoT 認證服務會執行一組認證測試。 裝置必須通過認證才能新增至 [IoT 認證裝置目錄](#certified-for-iot-device-catalog)。

## <a name="azure-iot-tools-extension"></a>Azure IoT Tools 延伸模組

Azure IoT Tools 是 [Visual Studio Code](#visual-studio-code) 中的延伸模組集合，可協助與 IoT 中樞互動及開發 IoT 裝置。 在開發 IoT 隨插即用裝置方面，可協助：

- 撰寫[裝置功能模型](#device-capability-model)和[介面](#interface)。
- 發佈至[模型存放庫](#model-repository)。
- 產生基本架構程式碼以實作裝置應用程式。

## <a name="azure-iot-explorer-tool"></a>Azure IoT 總管工具

Azure IoT 總管是一種圖形化工具，其可用來與 [IoT 隨插即用裝置](#iot-plug-and-play-device)互動並加以測試。 當在本機電腦上安裝此工具之後，即可使用它來執行下列動作：

- 檢視連線到 [IoT 中樞](#azure-iot-hub)的裝置。
- 連線到 IoT 隨插即用裝置。
- 檢視裝置所傳送的[遙測](#telemetry)。
- 使用裝置[屬性](#properties)。
- 呼叫裝置[命令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中樞

IoT 中樞是託管於雲端中的受控服務，可做為 IoT 應用程式與其管理裝置之間雙向通訊的中央訊息中樞。 [IoT 隨插即用裝置](#iot-plug-and-play-device)可連線到 IoT 中樞。 IoT 解決方案使用 IoT 中樞讓：

- 裝置將遙測傳送至雲端式解決方案。
- 雲端式解決方案管理已連線的裝置。

## <a name="azure-iot-device-sdk"></a>Azure IoT 裝置 SDK

您可使用多種語言版本的裝置 SDK 來建置 IoT 隨插即用裝置用戶端應用程式。 [裝置認證](#device-certification)的其中一個需求就是裝置用戶端程式碼會使用其中一個 Azure IoT 裝置 SDK。

## <a name="certified-for-iot-device-catalog"></a>IoT 認證裝置目錄

[IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)會列出已通過[裝置認證](#device-certification)測試的 [IoT 隨插即用裝置](#iot-plug-and-play-device)。 IoT 隨插即用裝置的[裝置功能模型](#device-capability-model)會在目錄中列出，並在公用模型存放庫中發佈。

## <a name="commands"></a>命令

在[介面](#interface)中所定義命令代表可在[數位分身](#digital-twin)上執行的方法。 例如，重新啟動裝置的命令。

## <a name="common-interface"></a>通用介面

所有 [IoT 隨插即用裝置](#iot-plug-and-play-device)都必須實作一些通用[介面](#interface)。 例如，裝置資訊介面會定義裝置的相關硬體和作業系統資訊。 [裝置認證](#device-certification)需要裝置實作數個通用介面。 您可從公用模型存放庫擷取通用介面定義。

## <a name="company-model-repository"></a>公司模型存放庫

組織可使用公司[模型存放庫](#model-repository)作為[裝置功能模型](#device-capability-model)和[介面](#interface)的私人存放區。

## <a name="connection-string"></a>連接字串

連接字串會封裝連線到端點所需的資訊。 連接字串通常包含端點位址和安全性資訊，但連接字串的格式會因服務而不同。 IoT 中樞服務有兩種相關的連接字串︰

- 裝置連接字串可讓 [IoT 隨插即用裝置](#iot-plug-and-play-device)連線到 IoT 中樞上的裝置對向端點。 裝置上用戶端程式碼會使用連接字串來建立與 IoT 中樞的安全連線。
- IoT 中樞連接字串可讓後端解決方案和工具安全地連線到 IoT 中樞上的服務對向端點。 這些解決方案和工具會管理 IoT 中樞和連線的裝置。
- 公司模型存放庫連接字串可讓後端解決方案和工具安全地連線到[公司模型存放庫](#company-model-repository)。 這些解決方案和工具會取用或管理存放庫中的[裝置功能模型](#device-capability-model)和[介面](#interface)。

## <a name="device-capability-model"></a>裝置功能模型

裝置功能模型描述 [IoT 隨插即用裝置](#iot-plug-and-play-device)，並定義該裝置所實作的[介面](#interface)集。 裝置功能模型通常會對應至實體裝置、產品或 SKU。 您可使用[數位分身定義語言](#digital-twin-definition-language)來定義裝置功能模型。

## <a name="device-certification"></a>裝置認證

裝置認證是認證 [IoT 隨插即用裝置](#iot-plug-and-play-device)的流程，以便將其新增至 [IoT 認證裝置目錄](#certified-for-iot-device-catalog)，並將其[裝置功能模型](#device-capability-model)和[介面](#interface)新增至[公用模型存放庫](#public-model-repository)。

## <a name="device-developer"></a>裝置開發人員

裝置開發人員使用[裝置功能模型](#device-capability-model)、[介面](#interface)和 [Azure IoT 裝置 SDK](#azure-iot-device-sdk) 來實作程式碼，以在 [IoT 隨插即用裝置](#iot-plug-and-play-device)上執行。

## <a name="device-modeling"></a>裝置模型

[裝置開發人員](#device-developer)使用[數位分身定義語言](#digital-twin-definition-language)來建立 [IoT 隨插即用裝置](#iot-plug-and-play-device)的功能模型。 您可使用模型存放庫來共用模型。 裝置開發人員可從模型產生基本架構裝置程式碼。 [解決方案開發人員](#solution-developer)可從模型設定 IoT 解決方案。

## <a name="device-provisioning-service"></a>裝置佈建服務

[Azure IoT Central](#azure-iot-central) 使用裝置佈建服務來管理所有裝置註冊和連線。 如需詳細資訊，請參閱 [Azure IoT Central 中的裝置連線能力](../iot-central/core/concepts-get-connected.md)。 您也可以使用裝置佈建服務來管理裝置註冊，以及與 IoT 中樞型 IoT 解決方案的連線。 如需詳細資訊，請參閱[使用 Azure IoT 中樞裝置佈建服務來佈建裝置](../iot-dps/about-iot-dps.md)。

## <a name="device-registration"></a>裝置註冊

[IoT 隨插即用裝置](#iot-plug-and-play-device)必須向 IoT 解決方案註冊，才能連線到該解決方案。 [Azure IoT Central](#azure-iot-central) 使用[裝置佈建服務](#device-provisioning-service)來管理裝置註冊。 在自訂 IoT 解決方案中，您可在 Azure 入口網站中或以程式設計方式向 IoT 中樞註冊裝置。

## <a name="device-first"></a>裝置優先

[Azure IoT Central](#azure-iot-central) 支援裝置優先註冊和連線案例。 在此案例中，[IoT 隨插即用裝置](#iot-plug-and-play-device)不需要事先註冊即可連線到 IoT Central 應用程式。 當裝置第一次連線到應用程式時，即會自動進行註冊。

## <a name="digital-twin"></a>數位分身

數位分身是 [IoT 隨插即用裝置](#iot-plug-and-play-device)的一種模型。 您可使用[數位分身定義語言](#digital-twin-definition-language)來建立數位分身模型。 您可使用 [Azure IoT 裝置 SDK](#azure-iot-device-sdk) 在執行階段與數位分身互動。 例如，您可設定裝置上數位分身中的屬性值，SDK 會將這項變更傳達給雲端中的 IoT 解決方案。

## <a name="digital-twin-change-events"></a>數位分身變更事件

當 [IoT 隨插即用裝置](#iot-plug-and-play-device)連線到 [IoT 中樞](#azure-iot-hub)時，中樞可使用其路由功能來傳送數位分身變更的通知。 例如，每當裝置上的[屬性](#properties)值變更時，IoT 中樞可將通知傳送至端點 (例如服務匯流排佇列)。

## <a name="digital-twin-definition-language"></a>數位分身定義語言

一種語言，其用來描述 [IoT 隨插即用裝置](#iot-plug-and-play-device)的模型和介面。 您可使用[數位分身定義語言](https://aka.ms/DTDL)來描述 [數位分身](#digital-twin)的功能，並讓 IoT 平台和 IoT 解決方案利用實體的語意。

## <a name="digital-twin-route"></a>數位分身路由

您可在 [IoT 中樞](#azure-iot-hub)內設定路由，將[數位分身變更事件](#digital-twin-change-events)傳遞至端點 (例如服務匯流排佇列)。

## <a name="interface"></a>介面

介面描述 [IoT 隨插即用裝置](#iot-plug-and-play-device)或[數位分身](#digital-twin)所實作的相關功能。 您可跨不同的[裝置功能模型](#device-capability-model)重複使用介面。

## <a name="iot-hub-query-language"></a>IoT 中樞查詢語言

IoT 中樞查詢語言可用於多種用途。 例如，您可使用語言來搜尋已向 IoT 中樞[註冊的裝置](#device-registration)，或調整[數位分身路由](#digital-twin-route)行為。

## <a name="iot-plug-and-play-device"></a>IoT 隨插即用裝置

IoT 隨插即用裝置通常是小型獨立運算裝置，可收集資料或控制其他裝置，以及執行實作[裝置功能模型](#device-capability-model)的軟體或韌體。  例如，IoT 隨插即用裝置可能是環境監視裝置，或智慧型農業灌溉系統的控制器。 您可撰寫雲端託管的 IoT 解決方案，從 IoT 隨插即用裝置下命令、控制和接收資料。 [Azure IoT 認證裝置目錄](#certified-for-iot-device-catalog)會列出可用的 IoT 隨插即用裝置。 此目錄中的每個 IoT 隨插即用裝置都已經過驗證，且具有[裝置功能模型](#device-capability-model)。

## <a name="microsoft-partner-center"></a>Microsoft 合作夥伴中心

[Microsoft 合作夥伴中心](https://docs.microsoft.com/partner-center/)是組織管理其與 Microsoft 端對端關係的地方。 您需要 Microsoft 合作夥伴中心帳戶，才能在 [Azure IoT 認證入口網站](#azure-certified-for-iot-portal)中認證 [IoT 隨插即用裝置](#iot-plug-and-play-device)。

## <a name="model-discovery"></a>模型探索

當 [IoT 隨插即用裝置](#iot-plug-and-play-device)連線到 IoT 解決方案時，解決方案可藉由尋找[裝置功能模型](#device-capability-model)來探索裝置的功能。 裝置可將其功能模型傳送至解決方案，解決方案也可以在[模型存放庫](#model-repository)中尋找裝置功能模型。

## <a name="model-repository"></a>模型存放庫

模型存放庫會儲存[裝置功能模型](#device-capability-model)和[介面](#interface)。 有一個[公用模型存放庫](#public-model-repository)。 組織可建立自己的組織模型存放庫。

## <a name="model-repository-rest-api"></a>模型存放庫 REST API

用來管理模型存放庫並與其互動的 API。 例如，您可使用此 API 來新增[裝置功能模型](#device-capability-model)及搜尋功能模型。

## <a name="properties"></a>屬性

屬性是在[介面](#interface)中定義的資料欄位，其代表數位分身的某種狀態。 您可將屬性宣告為唯讀或可寫入。 唯讀屬性 (例如序號) 是由 [IoT 隨插即用裝置](#iot-plug-and-play-device)本身上執行的程式碼所設定。  可寫入的屬性 (例如警示閾值) 通常是從雲端式 IoT 解決方案進行設定。

## <a name="public-model-repository"></a>公用模型存放庫

有一個公用模型存放庫，其可儲存[認證裝置](#device-certification)的[裝置功能模型](#device-capability-model)和[介面](#interface)。 公用模型存放庫也會儲存[通用介面](#common-interface)定義。

## <a name="registration-id"></a>註冊識別碼

註冊識別碼可唯一識別[裝置佈建服務](#device-provisioning-service)中的裝置。 此識別碼與裝置識別碼不同，後者是 [IoT 中樞](#azure-iot-hub)中裝置的唯一識別碼。

## <a name="scope-id"></a>範圍識別碼

範圍識別碼可唯一識別[裝置佈建服務](#device-provisioning-service)執行個體。

## <a name="shared-access-signature"></a>共用存取簽章

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 共用存取簽章驗證有兩個元件：共用存取原則和共用存取簽章 (通常稱為權杖)。 [IoT 隨插即用裝置](#iot-plug-and-play-device)使用共用存取簽章向 [IoT 中樞](#azure-iot-hub)進行驗證。

## <a name="solution-developer"></a>解決方案開發人員

解決方案開發人員會建立解決方案後端。 解決方案開發人員通常會使用 Azure 資源 (例如 [IoT 中樞](#azure-iot-hub)和[模型存放庫](#model-repository))，或使用 [IoT Central](#azure-iot-central)。

## <a name="telemetry"></a>遙測

在[介面](#interface)中定義的遙測欄位代表量值。 這些量值通常是 [IoT 隨插即用裝置](#iot-plug-and-play-device)所傳送作為資料流的值 (例如感應器讀數)。

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 是適用於多種平台的新式程式碼編輯器。 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 套件等延伸模組可供自訂編輯器，以支援各種不同的開發案例。
