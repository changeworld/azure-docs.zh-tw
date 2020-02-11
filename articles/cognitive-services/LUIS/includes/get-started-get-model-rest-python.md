---
title: 在 C# 中使用 REST 呼叫取得模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: a21834cec456ded82c15dc916cc4991a196ea22c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966818"
---
## <a name="prerequisites"></a>Prerequisites

* Azure Language Understanding - 撰寫資源的 32 字元金鑰和撰寫端點 URL。 使用 [Azure 入口網站](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或 [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) 建立。
* 匯入來自 cognitive-services-language-understanding GitHub 存放庫的 [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) 應用程式。
* 已匯入 TravelAgent 應用程式的 LUIS 應用程式識別碼。 應用程式識別碼會顯示在應用程式儀表板中。
* 應用程式中用來接收表達的版本識別碼。 預設識別碼為 "0.1"。
* [Python 3.6](https://www.python.org/downloads/) 或更新版本。
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>以程式設計方式變更模型

使用 GO 將機器學習的實體 [API](https://aka.ms/luis-apim-v3-authoring) 新增至應用程式。

1. 建立名為 `model.py` 的新檔案。 新增下列程式碼：

    ```python
    ########### Python 3.6 #############
    import requests

    # 32 character Authoring key
    LUIS_authoringKey  = "YOUR-KEY"

    LUIS_APP_ID = "YOUR-APP-ID"

    # Authoring endpoint, example: your-resource-name.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"

    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/train'

    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}

    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())

    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())

    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())

    addUtterances()
    train()
    trainStatus()
    ```

1. 使用您自己的值取代以 `YOUR-` 開頭的值。

    |資訊|目的|
    |--|--|
    |`YOUR-KEY`|您的 32 字元撰寫金鑰。|
    |`YOUR-ENDPOINT`| 您的撰寫 URL 端點。 例如： `replace-with-your-resource-name.api.cognitive.microsoft.com` 。 您已在建立資源時設定資源名稱。|
    |`YOUR-APP-ID`| 您的 LUIS 應用程式識別碼。 |

    在 LUIS 入口網站中，您可以在 [Azure 資源]  頁面上的 [管理] 區段中看到指派的金鑰和端點。 在相同的 [管理] 區段中，您可以在 [應用程式設定]  頁面上取得應用程式識別碼。

1. 在您建立檔案所在的相同目錄中使用命令提示字元，輸入下列命令以執行檔案：

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從檔案系統中刪除該檔案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的最佳做法](../luis-concept-best-practices.md)
