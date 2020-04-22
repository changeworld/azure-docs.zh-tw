---
title: 術語錶 - IoT 隨插即用預覽 |微軟文件
description: 概念 - 與 IoT 隨插即用預覽相關的常用術語的詞彙表。
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767061"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 隨插即用預覽的術語表

IoT 隨插即用文章中使用的常見術語的定義。

## <a name="azure-certified-for-iot-portal"></a>IoT 閘戶認證的 Azure

您可以使用 Azure[認證的 IoT 入口網站](https://aka.ms/ACFI):

- 完成[IoT 插即使用裝置認證](#iot-plug-and-play-device)[過程](#device-certification)。
- 尋找[裝置功能模型](#device-capability-model)。
- 將裝置功能模型到[公共模型儲存函式庫](#public-model-repository)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是一種跨平臺的命令列工具,用於管理 Azure 資源。 Azure CLI 的 Azure IoT 擴充是一個命令列工具,用於與[IoT 隨插即用設備](#iot-plug-and-play-device)進行互動和測試。 您可以使用延伸:

- 連接到 IoT 隨插即用設備。
- 查看設備發送的[遙測](#telemetry)數據。
- 使用裝置[屬性](#properties)。
- 呼叫裝置[指令](#commands)。
- 管理[模型儲存庫](#model-repository)、[介面](#interface)[與裝置功能模型](#device-capability-model)。

## <a name="azure-iot-central"></a>Azure IoT 中心

Azure IoT 中心是完全託管的軟體即服務解決方案,可輕鬆連接、監視和管理[IoT 隨插即用裝置](#iot-plug-and-play-device)。 您可以使用[裝置功能模型](#device-capability-model)自動配置 IoT 中心應用程式來監視和管理您的設備。

## <a name="azure-iot-certification-service"></a>Azure IoT 認證服務

當您透過[IoT 門戶](#azure-certified-for-iot-portal)提交[IoT 隨插即用裝置](#iot-plug-and-play-device)進行認證時,Azure IoT 認證服務將執行一組認證測試。 在將設備添加到[IoT認證設備目錄中](#certified-for-iot-device-catalog)之前,設備必須經過認證。

## <a name="azure-iot-tools-extension"></a>Azure IoT 工具延伸

Azure IoT 工具是 Visual Studio[代碼](#visual-studio-code)中的擴充的集合,可説明您與 IoT 中心進行互動並開發 IoT 設備。 對於 IoT 插即用裝置開發,它可以説明您:

- 編寫[裝置功能模型](#device-capability-model)與[介面](#interface)。
- 發布到[模型儲存函式庫](#model-repository)。
- 生成骨架代碼以實現設備應用程式。

## <a name="azure-iot-explorer-tool"></a>Azure IoT 資源管理員工具

Azure IoT 資源管理員是一種圖形工具,可用於與[IoT 隨插即用裝置](#iot-plug-and-play-device)進行互動和測試。 在本地電腦上安裝該工具後, 可以使用它:

- 檢視連線到[IoT 中心的裝置](#azure-iot-hub)。
- 連接到 IoT 隨插即用設備。
- 查看設備發送的[遙測](#telemetry)數據。
- 使用裝置[屬性](#properties)。
- 呼叫裝置[指令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中樞

IoT 中樞是託管於雲端中的受控服務，可做為 IoT 應用程式與其管理裝置之間雙向通訊的中央訊息中樞。 [IoT 隨插即用設備](#iot-plug-and-play-device)可以連接到IoT中心。 IoT 解決方案使用 IoT 中心開啟:

- 將遙測發送到基於雲的解決方案的設備。
- 基於雲端解決方案,用於管理連接的設備。

## <a name="azure-iot-device-sdk"></a>Azure IoT 裝置 SDK

有多種語言的設備 SDK 可用於建構 IoT 隨插即用設備用戶端應用程式。 [設備認證](#device-certification)的要求之一是設備用戶端代碼使用 Azure IoT 設備 SDK 之一。

## <a name="certified-for-iot-device-catalog"></a>IoT 裝置目錄認證

[IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)列出已透過[裝置認證](#device-certification)測試的[IoT 並插即用裝置](#iot-plug-and-play-device)。 目錄中的 IoT 隨插即用裝置[的裝置功能模型](#device-capability-model),並在公共模型儲存庫中發表。

## <a name="commands"></a>命令

在[介面](#interface)中定義的命令表示可以在[數位孿生](#digital-twin)上執行的方法。 例如,重新啟動設備的命令。

## <a name="common-interface"></a>通用介面

所有[物聯網隨插即用裝置](#iot-plug-and-play-device)都有望實現一些通用[介面](#interface)。 例如,設備資訊介面定義有關設備的硬體和作業系統資訊。 [設備認證](#device-certification)要求您的設備實現多個通用介面。 可以從公共模型存儲庫檢索通用介面定義。

## <a name="company-model-repository"></a>公司模型儲存庫

組織可以使用公司[模型儲存庫](#model-repository)作為[設備功能模型](#device-capability-model)和[介面](#interface)的專用存儲。

## <a name="connection-string"></a>連接字串

連接字串封裝連接到終結點所需的資訊。 連接字串通常包含端點位址和安全性資訊，但連接字串的格式會因服務而不同。 IoT 中樞服務有兩種相關的連接字串︰

- 設備連接字串使[IoT 隨插即用設備](#iot-plug-and-play-device)能夠連接到 IoT 中心上面向設備的終結點。 設備上的用戶端代碼使用連接字串與IoT中心建立安全連接。
- IoT 中心連接字串支援後端解決方案和工具,以安全地連接到 IoT 中心上面向服務的端點。 這些解決方案和工具管理IoT中心及其連接的設備。
- 公司模型儲存函式庫連接字串支援後端解決方案和工具安全地連接到[公司模型儲存庫](#company-model-repository)。 這些解決方案與工具使用或管理儲存庫中的[裝置功能模型](#device-capability-model)與[介面](#interface)。

## <a name="device-capability-model"></a>裝置功能模型

設備功能模型描述[IoT 隨插即用設備](#iot-plug-and-play-device),並定義裝置實現的[介面](#interface)集。 設備功能模型通常對應於實體設備、產品或 SKU。 您可以使用[數位雙定義語言](#digital-twin-definition-language)定義裝置功能模型。

## <a name="device-certification"></a>裝置認證

裝置認證是驗證[IoT 插即用裝置](#iot-plug-and-play-device)的過程,以便將其新增到[IoT 裝置認證目錄](#certified-for-iot-device-catalog)及其[裝置功能模型](#device-capability-model)和新增到[公共模型儲存庫](#public-model-repository)中的[介面](#interface)。

## <a name="device-developer"></a>裝置開發人員

設備開發人員使用[設備功能模型](#device-capability-model)、[介面](#interface)和 Azure [IoT 設備 SDK](#azure-iot-device-sdk)來實現在[IoT 隨插即用裝置上](#iot-plug-and-play-device)執行的代碼。

## <a name="device-modeling"></a>裝置建模

[設備開發人員](#device-developer)使用[數位雙定義語言](#digital-twin-definition-language)對[IoT隨插即用裝置的](#iot-plug-and-play-device)功能進行建模。 可以使用模型存儲庫共用模型。 設備開發人員可以從模型生成骨架設備代碼。 [解決方案開發人員](#solution-developer)可以從模型中配置IoT解決方案。

## <a name="device-provisioning-service"></a>裝置佈建服務

[Azure IoT 中心](#azure-iot-central)使用設備預配服務來管理所有設備註冊和連接。 有關詳細資訊,請參閱[Azure IoT 中心 中的裝置連接](../iot-central/core/concepts-get-connected.md)。 您還可以使用設備配置服務來管理設備註冊和連接到基於IoT集線器的IoT解決方案。 有關詳細資訊,請參閱使用[Azure IoT 中心裝置預配服務預配裝置](../iot-dps/about-iot-dps.md)。

## <a name="device-registration"></a>裝置註冊

在[IoT 隨插即用裝置](#iot-plug-and-play-device)連接到 IoT 解決方案之前,必須將其註冊到解決方案。 [Azure IoT 中心](#azure-iot-central)使用[設備預配服務](#device-provisioning-service)來管理設備註冊。 在自訂 IoT 解決方案中,可以在 Azure 入口中或以程式設計方式向 IoT 中心註冊設備。

## <a name="device-first"></a>裝置優先

[Azure IoT 中心](#azure-iot-central)支援設備優先註冊和連接方案。 在這種情況下[,IoT 隨插即用裝置](#iot-plug-and-play-device)可以連接到 IoT 中央應用程式,而無需提前註冊。 當設備首次連接到應用程式時,將自動進行註冊。

## <a name="digital-twin"></a>數位孿生

數位孿生是[物聯網即插即用設備的](#iot-plug-and-play-device)模型。 數位孿生使用[數位雙定義語言](#digital-twin-definition-language)進行建模。 您可以使用 Azure [IoT 裝置 SDK](#azure-iot-device-sdk)在執行時與數位孿生進行互動。 例如,您可以在設備上的數位孿生中設置屬性值,SDK 會將此更改傳達給雲中的 IoT 解決方案。

## <a name="digital-twin-change-events"></a>數位雙變更事件

當[IoT 隨插即用裝置](#iot-plug-and-play-device)連接到[IoT 中心](#azure-iot-hub)時,集線器可以使用其路由功能發送數位孿生更改通知。 例如,每當[屬性](#properties)值在設備上發生更改時,IoT 中心都可以向終結點(如服務總線佇列)發送通知。

## <a name="digital-twin-definition-language"></a>數位雙定義語言

用於描述[IoT 並插即用裝置的模型和介面的語言](#iot-plug-and-play-device)。 使用[數位雙定義語言](https://aka.ms/DTDL)描述[數位孿生的功能](#digital-twin),並使IoT平臺和IoT解決方案能夠利用實體的語義。

## <a name="digital-twin-route"></a>數字雙線

在[IoT 中心](#azure-iot-hub)中設定的路線,用於向和終結點(如服務匯流排排)提供[數位孿生變更事件](#digital-twin-change-events)。

## <a name="interface"></a>介面

介面描述由[IoT 隨插即用設備](#iot-plug-and-play-device)或[數位孿生](#digital-twin)實現的相關功能。 您可以在不同的[裝置功能模型中](#device-capability-model)重用介面。

## <a name="iot-hub-query-language"></a>IoT 中樞查詢語言

IoT 中心查詢語言用於多種用途。 例如,可以使用該語言搜索在 IoT 中心[註冊的設備](#device-registration)或優化[數位雙路由](#digital-twin-route)行為。

## <a name="iot-plug-and-play-device"></a>物聯網隨插即用裝置

IoT 隨插即用裝置通常是一種小規模的獨立計算設備,用於收集資料或控制其他設備,並運行實現[設備功能模型](#device-capability-model)的軟體或韌體。  例如,IoT 隨插即用設備可能是環境監測設備,也可以是智慧農業灌溉系統的控制器。 您可以編寫雲端託管 IoT 解決方案來命令、控制和接收來自 IoT 隨插即用設備的資料。 [適用於 IoT 裝置的 Azure 認證目錄](#certified-for-iot-device-catalog)清單列出了可用的 IoT 隨插即用裝置。 目錄中的每個 IoT 插即用裝置都已使用驗證,並且具有[裝置功能模型](#device-capability-model)。

## <a name="microsoft-partner-center"></a>Microsoft 合作夥伴中心

[Microsoft 合作夥伴中心](https://docs.microsoft.com/partner-center/)是您的組織管理其與 Microsoft 的端到端關係的地方。 您需要一個 Microsoft 合作夥伴中心帳戶,然後才能在[Azure 認證 IoT 門戶](#azure-certified-for-iot-portal)中驗證[IoT 隨插即用裝置](#iot-plug-and-play-device)。

## <a name="model-discovery"></a>模型探索

當[IoT 並插即用裝置](#iot-plug-and-play-device)連線到 IoT 解決方案時,該解決方案可以透過搜尋[裝置功能模型來發現裝置的功能](#device-capability-model)。 設備可以將其功能模型發送到解決方案,或者解決方案可以在[模型存儲庫](#model-repository)中找到設備功能模型。

## <a name="model-repository"></a>模型儲存庫

模型儲存函式[庫存放裝置功能模型](#device-capability-model)與[介面](#interface)。 有一個[公共模型儲存函式庫](#public-model-repository)。 組織可以創建自己的組織模型存儲庫。

## <a name="model-repository-rest-api"></a>模型儲存庫 REST API

用於管理和與模型存儲庫交互的 API。 例如,可以使用 API 添加[設備功能模型](#device-capability-model)並搜尋功能模型。

## <a name="properties"></a>屬性

屬性是在表示數位孿生的某些狀態的[介面](#interface)中定義的數據欄位。 您可以將屬性聲明為唯讀或可寫。 唯讀屬性(如序列號)由在[IoT 隨插即用設備上](#iot-plug-and-play-device)執行的代碼設定。  可寫屬性(如報警閾值)通常從基於雲的 IoT 解決方案設置。

## <a name="public-model-repository"></a>公共模型儲存庫

有一個公共模型儲存庫,用於儲存[裝置功能模型](#device-capability-model)和[認證裝置的](#device-certification)[介面](#interface)。 公共模型存儲庫還存儲[通用介面](#common-interface)定義。

## <a name="registration-id"></a>註冊識別碼

註冊 ID 唯一標識[設備預配服務](#device-provisioning-service)中的設備。 此 ID 與[IoT 中心](#azure-iot-hub)中設備的唯一識別符的設備 ID 不同。

## <a name="scope-id"></a>範圍識別碼

範圍 ID 範圍唯一標識[設備預配服務](#device-provisioning-service)實例。

## <a name="shared-access-signature"></a>共用存取簽章

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 共用訪問簽名身份驗證有兩個元件:共用訪問策略和共享訪問簽名(通常稱為權杖)。 [IoT 隨插即用裝置](#iot-plug-and-play-device)使用共享存取簽署使用[IoT中心](#azure-iot-hub)進行身分驗證。

## <a name="solution-developer"></a>解決方案開發人員

解決方案開發人員創建解決方案後端。 解決方案開發人員通常使用 Azure 資源(如[IoT 中心](#azure-iot-hub)與[模型儲存庫](#model-repository))或使用[IoT 中心](#azure-iot-central)。

## <a name="telemetry"></a>遙測

介面中定義的遙測[欄位表示度量](#interface)值。 這些測量通常是[IoT 隨插即用設備](#iot-plug-and-play-device)作為資料流發送的感測器讀數等值。

## <a name="visual-studio-code"></a>視覺工作室代碼

Visual Studio 代碼是一種現代代碼編輯器,可用於多個平臺。 擴展(如[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)套件中的擴展)使您能夠自訂編輯器以支援各種開發方案。
