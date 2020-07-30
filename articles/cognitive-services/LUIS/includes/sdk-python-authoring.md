---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: aa55a3550fbb4fcdcd97c26fca425b11ba589fcc
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369410"
---
使用適用於 Python 的 Language Understanding (LUIS) 撰寫用戶端程式庫可以：

* 建立應用程式。
* 新增意圖、實體和範例語句。
* 新增功能，例如片語清單。
* 定型和發佈應用程式。

[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [撰寫套件 (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Python 3.x](https://www.python.org/)。
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中[建立 Language Understanding 撰寫資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要您[建立](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)之資源的金鑰和端點，以將應用程式連線至 Language Understanding 撰寫。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。 您可以使用免費定價層 (`F0`) 來試用服務。

## <a name="setting-up"></a>設定

### <a name="install-the-python-library-for-luis"></a>安裝 Python LUIS 程式庫

在應用程式目錄中，使用下列命令安裝適用於 Python 的 Language Understanding (LUIS) 撰寫用戶端程式庫：

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>物件模型

Language Understanding (LUIS) 撰寫用戶端是向 Azure 進行驗證的 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)物件，其中包含您的撰寫金鑰。

建立用戶端之後，請使用此用戶端來存取功能，包括：

* 應用程式 - [建立](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-)、[刪除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-)、[發佈](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* 範例語句 - [依批次新增](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)、[依識別碼刪除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* 功能 - 管理[片語清單](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* 模型 - 管理[意圖](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-)和實體
* 模式 - 管理[模式](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* 定型 - [定型](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-)應用程式及輪詢[定型狀態](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [版本](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - 使用複製、匯出和刪除進行管理


## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的 Language Understanding (LUIS) 撰寫用戶端程式庫來執行下列工作：

* [建立應用程式](#create-a-luis-app)
* [新增實體](#create-entities-for-the-app)
* [新增對應方式](#create-intent-for-the-app)
* [新增範例語句](#add-example-utterance-to-intent)
* [定型應用程式](#train-the-app)
* [發佈應用程式](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您慣用的編輯器或 IDE 中，建立新的 Python 應用程式。 然後匯入下列程式庫。

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=Dependencies)]

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用您的金鑰建立 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 物件，並使用該物件與您的端點建立 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) 物件。

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>建立 LUIS 應用程式

1. 建立 LUIS 應用程式，並使其包含用來保存意圖、實體和範例語句的自然語言處理 (NLP) 模型。

1. 建立 [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) 物件用來建立應用程式的 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) 方法。 名稱和語言文化特性是必要屬性。

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>建立應用程式的意圖
LUIS 應用程式模型中的主要物件為意圖。 意圖會與使用者語句_意圖_的群組相對應。 使用者可以詢問問題，或發出預期會由 Bot (或其他用戶端應用程式) 提供特定回應的陳述。 意圖的範例包括預約航班、詢問目的地城市的天氣，以及詢問客戶服務的連絡人資訊。

使用具有唯一意圖名稱的 [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-)，然後傳遞應用程式識別碼、版本識別碼和新的意圖名稱。

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>建立應用程式的實體

雖然實體並非必要，但大部分的應用程式中都會有實體。 實體會從使用者語句中擷取滿足使用者意圖所需的資訊。 目前有數種類型的[預建](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-)和自訂實體，每一種分別有其本身的資料轉換物件 (DTO) 模型。  會新增至應用程式的常見預建實體包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md)、[ordinal](../luis-reference-prebuilt-ordinal.md)。

這個 **add_entities** 方法會建立一個具有兩個角色的 `Location` 簡單實體、一個 `Class` 簡單實體、一個 `Flight` 複合實體，並新增數個預建實體。

請務必了解，實體上不會標示意圖。 它們可以且通常會套用至許多意圖。 只有範例使用者語句會標示特定的單一意圖。

實體的建立方法是 [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) 類別的一部分。 每個實體類型都有本身的資料轉換物件 (DTO) 模型。

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>將範例語句新增至意圖

為了判斷語句的意圖及擷取實體，應用程式需要語句的範例。 這些範例必須以特定的單一意圖為目標，且應標示所有的自訂實體。 預建實體不需要標示。

藉由建立 [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) 物件的清單來新增範例語句，每個範例語句各有一個物件。 每個範例都應以實體名稱和實體值的名稱/值配對字典來標示所有實體。 實體值應與範例語句的文字中所顯示的完全相同。

使用應用程式識別碼、版本識別碼和範例清單來呼叫 [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)。 此呼叫會以結果清單來回應。 您必須檢查每個範例的結果，以確定範例已成功新增至模型。

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>進行應用程式定型

建立模型之後，必須針對此版本的模型將 LUIS 應用程式定型。 已定型的模型可用於[容器](../luis-container-howto.md)中，或[發佈](../luis-how-to-publish-app.md)至預備或產品位置。

[train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) 方法需要應用程式識別碼和版本識別碼。

非常小的模型 (如本快速入門所示) 會非常快速地進行定型。 針對生產層級的應用程式，在為應用程式定型時，必須進行 [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) 方法的輪詢呼叫，以確認定型成功的時間或是否成功。 其回應是 [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) 物件的清單，每個物件各有不同的狀態。 所有物件都必須成功，才會將定型視為完成。

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>發佈 Language Understanding 應用程式

使用 [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) 方法發佈 LUIS 應用程式。 這會將目前的定型版本發佈至端點上的指定位置。 您的用戶端應用程式會使用此端點來傳送使用者語句，以進行意圖和實體擷取的預測。

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `python` 命令執行應用程式。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清除資源

在完成預測後，請刪除檔案及其子目錄，以清除本快速入門中的工作。