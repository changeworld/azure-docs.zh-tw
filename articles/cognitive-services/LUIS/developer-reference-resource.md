---
title: 開發人員資源-Language Understanding
description: Sdk、REST Api、CLI 可協助您以程式設計語言開發 Language Understanding (LUIS) 應用程式。 管理您的 Azure 資源和 LUIS 預測。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e4e43ead957bb6feca09fc8b2a8c16e37821c9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327165"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Language Understanding (LUIS) 的 SDK、REST 和 CLI 開發人員資源

Sdk、REST Api、CLI 可協助您以程式設計語言開發 Language Understanding (LUIS) 應用程式。 管理您的 Azure 資源和 LUIS 預測。

## <a name="azure-resource-management"></a>Azure 資源管理

使用「Azure 認知服務」管理層來建立、編輯、列出和刪除 Language Understanding 或認知服務資源。

根據工具尋找參考檔：

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding 撰寫和預測要求

Language Understanding 的服務會從您需要建立的 Azure 資源進行存取。 有兩個資源：

* 使用 **撰寫** 資源來進行訓練，以建立、編輯、定型和發佈。
* 使用執行時間的 **預測** 來傳送使用者的文字，並接收預測。

瞭解 [V3 預測端點](luis-migration-api-v3.md)。

使用 [認知服務範例程式碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code) 來學習及使用最常見的工作。

### <a name="rest-specifications"></a>REST 規格

[LUIS REST 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)以及所有[Azure REST 規格](https://github.com/Azure/azure-rest-api-specs)都可在 GitHub 上公開使用。

### <a name="rest-apis"></a>REST API

撰寫和預測端點 API 都可從 REST Api 取得：

|類型|版本|
|--|--|
|編寫|[2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[預覽 V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|預測|[2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST 端點

LUIS 目前有2種類型的端點：

* 在訓練端點上**撰寫**
* 執行時間端點的查詢 **預測** 。

|目的|URL|
|--|--|
|在定型端點上撰寫 V2|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|在訓練端點上撰寫 V3|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 預測-執行時間端點上的所有預測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 預測-執行時間端點上的版本預測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 預測-執行時間端點的位置預測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

下表說明上表中的參數 (以大括號 `{}` 表示)。

|參數|目的|
|--|--|
|`your-resource-name`|Azure 資源名稱|
|`q` 或 `query`|用戶端應用程式 (例如聊天機器人) 所傳來的語句文字|
|`version`|10個字元版本名稱|
|`slot`| `production` 或 `staging`|

### <a name="rest-query-string-parameters"></a>REST 查詢字串參數

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>應用程式結構描述

[應用程式架構](app-schema-definition.md)會以或格式匯入和匯出 `.json` `.lu` 。

### <a name="language-based-sdks"></a>以語言為基礎的 Sdk

|Language |參考文件|Package|快速入門|
|--|--|--|--|
|C#|[編寫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[預測](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 撰寫](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 預測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[編寫](azure-sdk-quickstart.md?pivots=programming-language-csharp)<br>[查詢預測](azure-sdk-quickstart.md?pivots=programming-language-csharp)|
|Go|[撰寫和預測](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[撰寫和預測](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 撰寫](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 預測](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[編寫](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 撰寫](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM 預測](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[編寫](azure-sdk-quickstart.md?pivots=programming-language-javascript)<br>[預測](azure-sdk-quickstart.md?pivots=programming-language-javascript)|
|Python|[撰寫和預測](azure-sdk-quickstart.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[編寫](azure-sdk-quickstart.md?pivots=programming-language-python)<br>[預測](azure-sdk-quickstart.md?pivots=programming-language-python)|


### <a name="containers"></a>容器

Language Understanding (LUIS) 提供 [容器](luis-container-howto.md) 來提供應用程式的內部部署和包含版本。

### <a name="export-and-import-formats"></a>匯出和匯入格式

Language Understanding 能夠以 JSON 格式管理您的應用程式和其模型、 `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) 格式，以及 Language Understanding 容器的壓縮套件。

匯入和匯出這些格式可從 Api 和 LUIS 入口網站取得。 入口網站會提供匯入和匯出作為應用程式清單和版本清單的一部分。

## <a name="workshops"></a>車間

* GitHub： (研討會) [對話-AI：使用 LUIS 的 NLU](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>持續整合工具

* GitHub： (預覽版) [使用 DevOps 實務開發 LUIS 應用程式](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub： [NLU。DevOps](https://github.com/microsoft/NLU.DevOps) -支援 NLU 服務持續整合和部署的工具。

## <a name="bot-framework-tools"></a>Bot Framework 工具

Bot framework 以多種語言提供的 [SDK](https://github.com/Microsoft/botframework) ，以及使用 [Azure bot service](https://dev.botframework.com/)的服務形式提供。

Bot framework 提供 [數種工具](https://github.com/microsoft/botbuilder-tools) 來協助 Language Understanding，包括：

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -使用 markdown 檔案建立 LUIS language 理解模型
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -建立及管理您的 LUIS.ai 應用程式
* [分派](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-管理父應用程式和子應用程式
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -自動產生 LUIS 意圖和實體的支援 c #/Typescript 類別。
* [Bot Framework 模擬器](https://github.com/Microsoft/BotFramework-Emulator/releases) -桌面應用程式，可讓 bot 開發人員測試及偵測使用 Bot Framework SDK 建立的 bot
* [Bot Framework 編輯器](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) -適用于開發人員的整合式開發工具，以及用來建立 bot 和對話式體驗的專業團隊 Microsoft Bot Framework
* [Bot Framework 範例](https://github.com/microsoft/botbuilder-samples) -#C、JavaScript、TypeScript 和 Python
## <a name="next-steps"></a>後續步驟

* 深入瞭解常見的 [HTTP 錯誤碼](luis-reference-response-codes.md)
* 所有 Api 和 Sdk 的[參考檔](https://docs.microsoft.com/azure/index)
* [Bot framework](https://github.com/Microsoft/botbuilder-dotnet) 和 [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [認知容器](../cognitive-services-container-support.md)
