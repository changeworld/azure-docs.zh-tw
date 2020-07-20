---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 8aeb6b964ab38a68a6d8681a4e5c93e1650c6a69
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171260"
---
使用適用於 Python 的 Language Understanding (LUIS) 預測用戶端程式庫，可以：

* 依位置取得預測
* 依版本取得預測

[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [預測執行階段套件 (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [ 範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Prerequisites

* Language Understanding (LUIS) 入口網站帳戶 - [建立免費帳戶](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* LUIS 應用程式識別碼 - 使用 `df67dcdb-c37d-46af-88e1-8b97951ca1c2` 的公用 IoT 應用程式識別碼。 快速入門程式碼中使用的使用者查詢是該應用程式所專屬。

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-runtime-key"></a>取得您的 Language Understanding (LUIS) 執行階段金鑰

藉由建立 LUIS 執行階段資源，取得您的[執行階段金鑰](../luis-how-to-azure-subscription.md)。 請保留您的金鑰和金鑰端點，以進行下一個步驟。

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>建立新的 Python 檔案

在您慣用的編輯器或 IDE 中，建立名為 `prediction_quickstart.py` 的新 Python 檔案。

### <a name="install-the-sdk"></a>安裝 SDK

在應用程式目錄中，使用下列命令安裝適用於 Python 的 Language Understanding (LUIS) 預測執行階段用戶端程式庫：

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>物件模型

Language Understanding (LUIS) 預測執行階段用戶端是向 Azure 進行驗證的 [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python)物件，其中包含您的資源金鑰。

建立用戶端之後，請使用此用戶端來存取功能，包括：

* [依預備或生產位置](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)進行預測
* 依[版本](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)進行預測

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的 Language Understanding (LUIS) 預測執行階段用戶端程式庫來執行下列動作：

* [依位置進行預測](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 `prediction_quickstart.py` 檔案。 新增下列相依性：

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>驗證用戶端

1. 為您自己的必要 LUIS 資訊建立變數：

    新增變數以管理從名為 `LUIS_RUNTIME_KEY` 的環境變數中提取的預測金鑰。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 方法會於稍後建立。

    建立用來保存資源名稱的變數 `LUIS_RUNTIME_ENDPOINT`。

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=AuthorizationVariables)]

1. 建立應用程式識別碼的變數，作為名為 `LUIS_APP_ID` 的環境變數。 將環境變數設定為公用 IoT 應用程式 **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** 。 建立變數以設定 `production` 發佈的位置。

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=OtherVariables)]


1. 使用您的金鑰建立認證物件，並使用該物件與您的端點建立 [LUISRuntimeClientConfiguration]https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() 物件。

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>從執行階段取得預測

新增下列方法，以建立對預測執行階段的要求。

使用者語句是 [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) 物件的一部分。

**[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** 方法需要數個參數，例如應用程式識別碼、位置名稱，以及用來完成要求的預測要求物件。 其他選項 (例如詳細資訊、顯示所有意圖和記錄) 是選擇性的。 要求會傳回 [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) 物件。

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>預測的主要程式碼

使用下列主要方法將變數和方法繫結在一起，以取得預測。

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>執行應用程式

使用 `python prediction_quickstart.py` 命令，從您的應用程式目錄執行應用程式。

```console
python prediction_quickstart.py
```

快速入門主控台會顯示輸出：

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>清除資源

在完成預測後，請刪除檔案及其子目錄，以清除本快速入門中的工作。
