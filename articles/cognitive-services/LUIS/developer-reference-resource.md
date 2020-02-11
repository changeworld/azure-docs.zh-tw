---
title: 開發人員資源-Language Understanding
description: Sdk、REST Api、CLI 可協助您以程式設計語言開發 Language Understanding （LUIS）應用程式。 管理您的 Azure 資源和 LUIS 預測。
ms.topic: reference
ms.date: 02/09/2020
ms.openlocfilehash: ed869b7022e43b8ecf8c1f05bb3c6f0919076818
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119973"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>適用于 Language Understanding 的 SDK、REST 和 CLI 開發人員資源（LUIS）

Sdk、REST Api、CLI 可協助您以程式設計語言開發 Language Understanding （LUIS）應用程式。 管理您的 Azure 資源和 LUIS 預測。 

## <a name="azure-resource-management"></a>Azure 資源管理

使用 Azure 認知服務管理層來建立、編輯、列出及刪除 Language Understanding 或認知服務資源。

尋找以工具為基礎的參考檔：

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding 撰寫和預測要求

Language Understanding 服務會從您需要建立的 Azure 資源進行存取。 有兩個資源：

* 使用**撰寫**資源進行訓練，以建立、編輯、定型和發佈。
* 使用執行時間的**預測**來傳送使用者的文字並接收預測。

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

使用[認知服務範例程式碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code)來學習和使用最常見的工作。

### <a name="rest-apis"></a>REST API

撰寫和預測端點 API 都可從 REST Api 取得：

|類型|版本|
|--|--|
|[撰寫中]|[2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[預覽 V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|預測|[2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>以語言為基礎的 Sdk

|語言 |參考文件|套件|Samples|快速入門|
|--|--|--|--|--|
|C#|[編寫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[預測](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 製作](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 預測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[建立和管理應用程式](sdk-authoring.md?pivots=programming-language-csharp)<br>[查詢預測端點](sdk-query-prediction-endpoint.md)|
|移至|[撰寫和預測](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[使用 REST 撰寫和預測](luis-get-started-get-intent-from-rest.md)|
|Java|[撰寫和預測](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 撰寫](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 預測](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[撰寫和預測](luis-get-started-get-intent-from-rest.md)
|Node.js|[編寫](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 撰寫](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM 預測](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[使用 REST 撰寫和預測](luis-get-started-get-intent-from-rest.md)|
|Python|[撰寫和預測](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[編寫](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[編寫](sdk-authoring.md?pivots=programming-language-python)<br>[使用 REST 進行預測](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>容器

Language Understanding （LUIS）會提供[容器](luis-container-howto.md)，以提供內部部署和應用程式的包含版本。

### <a name="export-and-import-formats"></a>匯出和匯入格式

Language Understanding 提供以 JSON 格式管理應用程式及其模型的功能，`.LU` （[LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)）格式，以及 Language Understanding 容器的壓縮封裝。

匯入和匯出這些格式可從 Api 和從 LUIS 入口網站取得。 入口網站會提供 [匯入] 和 [匯出] 作為 [應用程式清單和版本] 清單的一部分。

## <a name="other-tools-and-sdks"></a>其他工具和 Sdk

Bot framework 提供各種語言的[SDK](https://github.com/Microsoft/botframework) ，以及使用[Azure bot service](https://dev.botframework.com/)做為服務。

Bot framework 提供[數種工具](https://github.com/microsoft/botbuilder-tools)來協助 Language Understanding，包括：

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -使用 markdown 檔案建立 LUIS 語言理解模型
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -建立和管理您的 LUIS.ai 應用程式
* [分派](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-管理父系和子應用程式
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -自動為您C#的 LUIS 意圖和實體產生支援/Typescript 類別。
* [Bot framework 模擬器](https://github.com/Microsoft/BotFramework-Emulator/releases)-桌面應用程式，可讓 Bot 開發人員測試和偵測使用 BOT Framework SDK 建立的 bot


## <a name="next-steps"></a>後續步驟

* 瞭解常見的[HTTP 錯誤碼](luis-reference-response-codes.md)
* 所有 Api 和 Sdk 的[參考檔](https://docs.microsoft.com/azure/index#pivot=sdkstools)
* [Bot framework](https://github.com/Microsoft/botbuilder-dotnet)和[Azure bot 服務](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [認知容器](../cognitive-services-container-support.md)
