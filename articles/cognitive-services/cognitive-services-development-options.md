---
title: Azure 認知服務開發選項
description: 瞭解如何使用 Azure 認知服務搭配不同的開發和部署選項，例如用戶端程式庫、REST Api、Logic Apps、Power Automate、Azure Functions、Azure App Service、Azure Databricks 等等。
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 975f7eec31839aefcb1782f573d0210df29a4d00
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873564"
---
# <a name="cognitive-services-development-options"></a>認知服務開發選項

本檔提供開發和部署選項的概要說明，以協助您開始使用 Azure 認知服務。

Azure 認知服務是雲端式 AI 服務，可讓開發人員在其應用程式和產品中建立智慧，而不需要深入瞭解機器學習。 有了認知服務，您就可以存取由 Microsoft 所建立、定型及更新的 AI 功能或模型，以供您在應用程式中使用。 在許多情況下，您也可以選擇為您的商務需求自訂模型。 

認知服務分為四類：決策、語言、語音和視覺。 通常您會透過 REST Api、用戶端程式庫和自訂工具 (（例如 Microsoft 所提供的命令列) 介面）來存取這些服務。 但是，這只是成功的一個途徑。 透過 Azure，您也可以存取數個開發選項，例如：

* 自動化和整合工具，例如 Logic Apps 和 Power Automate。
* 部署選項，例如 Azure Functions 和 App Service。 
* 可進行安全存取的認知服務 Docker 容器。
* 適用於巨量資料案例的 Apache Spark、Azure Databricks、Azure Synapse Analytics 和 Azure Kubernetes Service 等工具。 

在開始之前，請務必瞭解認知服務主要用於兩個不同的工作。 根據您想要執行的工作，您有不同的開發和部署選項可供選擇。 

* [預測和分析的開發選項](#development-options-for-prediction-and-analysis)
* [自訂和設定模型的工具](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>預測和分析的開發選項 

您將用來自訂和設定模型的工具，與您用來呼叫認知服務的工具不同。 大部分的認知服務都可讓您在沒有任何自訂的情況下，傳送資料並接收見解。 例如： 

* 您可以將影像傳送至電腦視覺服務，以偵測單字和片語，或計算框架中的人員人數
* 您可以將音訊檔案傳送到語音服務，並同時取得轉譯並將語音轉換成文字
* 您可以傳送 PDF 至表單辨識器服務，以及偵測這些儲存格內的資料表、資料格和文字，以及取得具有座標和詳細資料的 JSON 輸出

Azure 提供各種專為不同類型的使用者所設計的工具，其中有許多可搭配認知服務使用。 設計工具驅動的工具是最容易使用的工具，而且可以快速設定和自動化，但自訂時可能會有限制。 REST Api 和用戶端程式庫為使用者提供更多控制和彈性，但需要更多投入時間和專業知識來建立解決方案。 如果您使用 REST Api 和用戶端程式庫，則預期您會習慣使用新式程式設計語言，例如 c #、JAVA、Python、JavaScript 或其他受歡迎的程式設計語言。 

讓我們看看您可以使用認知服務的不同方式。

### <a name="client-libraries-and-rest-apis"></a>用戶端程式庫和 REST API

認知服務用戶端程式庫和 REST Api 可讓您直接存取您的服務。 這些工具可讓您以程式設計方式存取認知服務、其基準模型，而且在許多情況下，都可讓您以程式設計方式自訂模型和解決方案。 

* **目標使用者 ()**：開發人員和資料科學家
* **優點**：提供從任何語言和環境呼叫服務的最大彈性。 
* **UI**： N/A-僅限程式碼
* **訂用帳戶 (s)**： Azure 帳戶 + 認知服務資源

如果您想要深入瞭解可用的用戶端程式庫和 REST Api，請使用我們的 [認知服務總覽](index.yml) 來挑選和服務，並開始使用我們的其中一個視覺、決策、語言和語音快速入門。

### <a name="cognitive-services-for-big-data"></a>巨量資料的認知服務

透過適用於巨量資料的認知服務，您可以將持續改進的智慧型模型直接內嵌到 Apache Spark&trade; 和 SQL 計算內。 這些工具可讓開發人員擺脫低層級的網路詳細資料，從而專注於建立智慧型的分散式應用程式。 適用于 Big Data 的認知服務支援下列平臺和連接器： Azure Databricks、Azure Synapse、Azure Kubernetes Service 和資料連線器。

* **目標使用者 ()**：資料科學家和資料工程師
* **優點**：適用于 Big Data 的 Azure 認知服務可讓使用者透過認知服務使用 Apache Spark 通道數 tb 的資料 &trade; 。 您可以輕鬆地使用任何資料存放區建立大規模的智慧型應用程式。
* **UI**： N/A-僅限程式碼
* **訂用帳戶 (s)**： Azure 帳戶 + 認知服務資源

如果您想要深入瞭解認知服務的大型資料，有個[不錯的開端。](./big-data/cognitive-services-for-big-data.md) 如果您已經準備好開始建立，請嘗試我們的 [Python](./big-data/samples-python.md) 或 [Scala](./big-data/samples-scala.md) 範例。

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions 和 Azure 服務 Web 作業

[Azure Functions](../azure-functions/index.yml) 和 [Azure App Service Web 作業](../app-service/index.yml) 都提供專為開發人員設計的程式碼優先 integration services，並建置於 [Azure App 服務](../app-service/index.yml)上。 這些產品提供無伺服器的基礎結構來撰寫程式碼。 在該程式碼中，您可以使用用戶端程式庫和 REST Api 對我們的服務進行呼叫。 

* **目標使用者 ()**：開發人員和資料科學家
* **優點**：無伺服器計算服務，可讓您執行事件觸發的程式碼。 
* **UI**：是
* **訂用帳戶 (s)**： Azure 帳戶 + 認知服務資源 + Azure Functions 訂用帳戶

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) 與 Power Automate 共用相同的工作流程設計工具和連接器，但是提供更先進的控制，包括與 Visual Studio 和 DevOps 的整合。 Power Automate 可讓您透過服務特定的連接器，輕鬆地與您的認知服務資源整合，以提供 Api 的 proxy 或包裝函式。 這些是 Power Automate 中可用的連接器。 

* **目標使用者 ()**：開發人員、整合者、IT 專業人員、DevOps
* **優點**：設計師優先 (宣告式) 開發模型，在低程式碼解決方案中提供先進的選項和整合
* **UI**：是
* **訂用帳戶 (s)**： Azure 帳戶 + 認知服務資源 + Logic Apps 部署

### <a name="power-automate"></a>Power Automate 

Power 自動化是 [Power Platform](/power-platform/) 中的一項服務，可協助您在應用程式與服務之間建立自動化的工作流程，而不需要撰寫程式碼。 我們提供數個連接器，可讓您輕鬆地與 Power Automate 解決方案中的認知服務資源互動。 Power Automate 是以 Logic Apps 為基礎所建置。 

* **目標使用者 (s)**：商務使用者 (分析師) 和 Sharepoint 系統管理員
* **優點**：只需從桌面錄製滑鼠點按、按鍵和複製貼上步驟，就能自動執行重複的手動工作！
* **UI 工具**： Yes-僅限 ui
* **訂用帳戶 (s)**： Azure 帳戶 + 認知服務資源 + Power Automate 訂用帳戶 + Office 365 訂用帳戶

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview) 是一種 Microsoft Power Platform 的功能，可讓您藉由自動化程式並預測結果，來提升業務績效。 AI builder 透過點按一下體驗，將 AI 功能帶入您的解決方案。 許多認知服務（例如表單辨識器、文字分析和電腦視覺）都已直接整合到這裡，您不需要建立自己的認知服務。 

* **目標使用者 (s)**：商務使用者 (分析師) 和 Sharepoint 系統管理員
* **優點**：透過點按體驗來帶來 AI 威力的全包式解決方案。 不需要撰寫程式碼或資料科學技能。
* **UI 工具**： Yes-僅限 ui
* **訂用帳戶 (s)**： AI Builder

### <a name="continuous-integration-and-deployment"></a>持續整合與部署

您可以使用 Azure DevOps 和 GitHub 動作來管理您的部署。 在討論的 [下一節](#continuous-integration-and-delivery-with-devops-and-github-actions) 中，我們有兩個 CI/CD 整合範例，可訓練和部署適用于語音的自訂模型，以及 LANGUAGE UNDERSTANDING (LUIS) service。 

* **目標使用者 ()**：開發人員、資料科學家和資料工程師
* **優點**：可讓您以程式設計方式持續調整、更新和部署應用程式和模型。 定期使用您的資料來改善和更新語音、視覺、語言和決策的模型時，有顯著的好處。 
* **UI 工具**： N/A-僅限程式碼 
* **訂用帳戶 (s)**： Azure 帳戶 + 認知服務資源 + GitHub 帳戶

## <a name="tools-to-customize-and-configure-models"></a>自訂和設定模型的工具

當您使用認知服務來建立應用程式或工作流程時，您可能會發現需要自訂模型，以達到所需的效能。 我們的許多服務都可讓您建立在預先建立的模型之上，以符合您特定的業務需求。 針對所有可自訂的服務，我們提供 UI 導向的體驗，讓您逐步完成程式以及程式碼驅動定型的 Api。 例如：

* 您想要將自訂語音模型定型，以正確辨識文字錯誤率 (WER) 低於3% 的醫療詞彙
* 您想要使用可分辨 coniferous 與落葉樹狀結構之間差異的自訂視覺來建立影像分類器
* 您想要使用您的個人語音資料建立自訂神經語音，以獲得改良的自動化客戶體驗

您將用來定型和設定模型的工具，與您用來呼叫認知服務的工具不同。 在許多情況下，支援自訂的認知服務會提供入口網站和 UI 工具，其設計目的是協助您定型、評估和部署模型。 讓我們快速看一下一些選項：<br><br>

| 要素 | 服務 | 自訂 UI | 快速入門 |
|--------|---------|------------------|------------|
| 視覺 | 自訂視覺 | https://www.customvision.ai/ | [快速入門](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| 視覺 | 表單辨識器 | [範例標籤工具](https://fott-preview.azurewebsites.net/) | [快速入門](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| 決策 | 內容仲裁者 | https://contentmoderator.cognitive.microsoft.com/dashboard | [快速入門](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| 決策 | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [快速入門](./metrics-advisor/quickstarts/web-portal.md) |
| 決策 | 個人化工具 | UI 可在個人化工具資源下的 Azure 入口網站中取得。 | [快速入門](./personalizer/quickstart-personalizer-sdk.md) |
| Language | 語言理解 (LUIS) | https://www.luis.ai/ | |
| Language | QnA Maker | https://www.qnamaker.ai/ | [快速入門](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Language | Translator/自訂翻譯 | https://portal.customtranslator.azure.ai/ | [快速入門](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| 語音 | 自訂命令 | https://speech.microsoft.com/ | [快速入門](./speech-service/custom-commands.md) |
| 語音 | 客製化的語音 | https://speech.microsoft.com/ | [快速入門](./speech-service/custom-speech-overview.md) |
| 語音 | 自訂語音 | https://speech.microsoft.com/ | [快速入門](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>使用 DevOps 和 GitHub Actions 的持續整合和傳遞

Language Understanding 和語音服務提供由 Azure DevOps 和 GitHub 動作提供的持續整合和持續部署解決方案。 這些工具可用來自動化自訂模型的定型、測試和發行管理。 

* [自訂語音的 CI/CD](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [適用于 LUIS 的 CI/CD](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>內部內部部署容器 

許多認知服務都可以部署在容器中，以供內部部署存取及使用。 使用這些容器可讓您彈性地將認知服務帶到更接近您的資料，以符合法規、安全性或其他操作原因。 如需認知服務容器的完整清單，請參閱 [認知服務的內部部署容器](./cognitive-services-container-support.md)。

## <a name="next-steps"></a>後續步驟
<!--
* Learn more about low code development options for Cognitive Services -->
* [建立認知服務資源並開始建立](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
