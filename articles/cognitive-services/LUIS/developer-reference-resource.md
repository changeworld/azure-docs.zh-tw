---
title: 開發人員資源 - 語言理解
description: SDK、REST API、CLI 可説明您開發程式設計語言的語言理解 （LUIS） 應用。 管理 Azure 資源和 LUIS 預測。
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457979"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>用於語言理解的 SDK、REST 和 CLI 開發人員資源 （LUIS）

SDK、REST API、CLI 可説明您開發程式設計語言的語言理解 （LUIS） 應用。 管理 Azure 資源和 LUIS 預測。

## <a name="azure-resource-management"></a>Azure 資源管理

使用 Azure 認知服務管理層創建、編輯、列出和刪除語言理解或認知服務資源。

根據該工具查找參考文檔：

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>語言理解創作和預測請求

語言理解服務是從需要創建的 Azure 資源訪問的。 有兩個資源：

* 使用**創作**資源進行培訓以創建、編輯、培訓和發佈。
* 使用運行時**的預測**發送使用者的文本並接收預測。

瞭解[V3 預測終結點](luis-migration-api-v3.md)。

使用[認知服務示例代碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code)來學習和使用最常見的任務。

### <a name="rest-specifications"></a>REST 規格

[LUIS REST 規範](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)以及所有[Azure REST 規範](https://github.com/Azure/azure-rest-api-specs)在 GitHub 上公開提供。

### <a name="rest-apis"></a>REST API

創作和預測終結點 API 均可從 REST API 獲得：

|類型|版本|
|--|--|
|編寫|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[預覽 V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|預測|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST 端點

LUIS 目前有兩種類型的終結點：

* 在訓練終結點上創作
* 對運行時終結點的查詢預測。

|目的|URL|
|--|--|
|在訓練終結點上創作|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 運行時 - 運行時終結點上的所有預測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 運行時 - 運行時終結點上的版本預測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 運行時 - 運行時終結點上的插槽預測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

下表說明上表中的參數 (以大括號 `{}` 表示)。

|參數|目的|
|--|--|
|`your-resource-name`|Azure 資源名稱|
|`q` 或 `query`|用戶端應用程式 (例如聊天機器人) 所傳來的語句文字|
|`version`|10 個字元版本名稱|
|`slot`| `production` 或 `staging`|

### <a name="language-based-sdks"></a>基於語言的 SDK

|語言 |參考文件|Package|範例|快速入門|
|--|--|--|--|--|
|C#|[編寫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[預測](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 創作](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 預測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.淨 SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[建立和管理應用程式](sdk-authoring.md?pivots=programming-language-csharp)<br>[查詢預測端點](sdk-query-prediction-endpoint.md)|
|Go|[創作和預測](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[使用 REST 進行創作和預測](luis-get-started-get-intent-from-rest.md)|
|Java|[創作和預測](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[馬文創作](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[馬文預測](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[創作和預測](luis-get-started-get-intent-from-rest.md)
|Node.js|[編寫](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 創作](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM 預測](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[使用 REST 進行創作和預測](luis-get-started-get-intent-from-rest.md)|
|Python|[創作和預測](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[編寫](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[編寫](sdk-authoring.md?pivots=programming-language-python)<br>[使用 REST 進行預測](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>容器

語言理解 （LUIS） 提供了一個[容器](luis-container-howto.md)，用於提供應用的本地版本和包含版本。

### <a name="export-and-import-formats"></a>匯出和導入格式

語言理解提供了以 JSON 格式`.LU`、（LUDown） 格式和"語言理解[LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)"容器的壓縮包來管理應用及其模型的功能。

可從 API 和 LUIS 門戶導入和匯出這些格式。 門戶提供導入和匯出作為應用清單和版本清單的一部分。

## <a name="other-tools-and-sdks"></a>其他工具和 SDK

機器人框架作為[SDK](https://github.com/Microsoft/botframework)以各種語言提供，並使用[Azure Bot 服務](https://dev.botframework.com/)作為服務提供。

Bot 框架提供了[多種工具](https://github.com/microsoft/botbuilder-tools)來説明理解語言，包括：

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - 使用標記檔構建 LUIS 語言理解模型
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - 創建和管理您的LUIS.ai應用程式
* [派單](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- 管理父應用和子應用
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - 自動生成支援 C#/Typescript 類，用於您的 LUIS 意圖和實體。
* [機器人框架模擬器](https://github.com/Microsoft/BotFramework-Emulator/releases)- 一個桌面應用程式，允許機器人開發人員測試和調試使用機器人框架 SDK 構建的機器人
* [機器人框架編輯器](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md)- 開發人員和多學科團隊的集成開發工具，用於構建機器人和 Microsoft 機器人框架的對話體驗
* [微軟/NLU。DevOps](https://github.com/microsoft/NLU.DevOps) - 支援 NLU 服務持續集成和部署的工具。

## <a name="next-steps"></a>後續步驟

* 瞭解常見的[HTTP 錯誤代碼](luis-reference-response-codes.md)
* 所有 API 和 SDK 的[參考文檔](https://docs.microsoft.com/azure/index)
* [機器人框架](https://github.com/Microsoft/botbuilder-dotnet)和[Azure 機器人服務](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [認知容器](../cognitive-services-container-support.md)
