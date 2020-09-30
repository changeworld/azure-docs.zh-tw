---
title: 在 C# 中使用 REST 呼叫取得模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: 4e4a5115ee83030b5cf3bd8c047f7ccadf4c9e2c
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534553"
---
[參考文件](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-model-with-rest/model.py)

## <a name="prerequisites"></a>必要條件

* [Python 3.6](https://www.python.org/downloads/) 或更新版本。
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>建立披薩應用程式

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>以程式設計方式變更模型

1. 建立名為 `model.py` 的新檔案。 新增下列程式碼：

    [!code-python[Add example utterances to Language Understanding in python](~/cognitive-services-quickstart-code/python/LUIS/python-model-with-rest/model.py)]

1. 使用您自己的值取代以 `YOUR-` 開頭的值。

    |資訊|目的|
    |--|--|
    |`YOUR-APP-ID`| 您的 LUIS 應用程式識別碼。 |
    |`YOUR-AUTHORING-KEY`|您的 32 字元撰寫金鑰。|
    |`YOUR-AUTHORING-ENDPOINT`| 您的撰寫 URL 端點。 例如： `https://replace-with-your-resource-name.api.cognitive.microsoft.com/` 。 您已在建立資源時設定資源名稱。|

    在 LUIS 入口網站中，您可以在 [Azure 資源] 頁面上的 [管理] 區段中看到指派的金鑰和端點。 在相同的 [管理] 區段中，您可以在 [應用程式設定] 頁面上取得應用程式識別碼。

1. 在您建立檔案所在的相同目錄中使用命令提示字元，輸入下列命令以執行檔案：

    ```console
    python model.py
    ```

1. 檢閱撰寫回應：

    ```console
    Add the list of utterances:
    [{'value': {'ExampleId': 1137150691, 'UtteranceText': 'order a pizza'}, 'hasError': False}, {'value': {'ExampleId': 1137150692, 'UtteranceText': 'order a large pepperoni pizza'}, 'hasError': False}, {'value': {'ExampleId': 1137150693, 'UtteranceText': 'i want two large pepperoni pizzas on thin crust'}, 'hasError': False}]
    Request training:
    {'statusId': 9, 'status': 'Queued'}
    Request training status:
    [{'modelId': 'edb46abf-0000-41ab-beb2-a41a0fe1630f', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': 'a5030be2-616c-4648-bf2f-380fa9417d37', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': 'e4b6704b-1636-474c-9459-fe9ccbeba51c', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '031d3777-2a00-4a7a-9323-9a3280a30000', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '9250e7a1-06eb-4413-9432-ae132ed32583', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}]
    ```

    以下是針對可讀性格式化的輸出：

    ```json
    Add the list of utterances:
    [
      {
        'value': {
          'ExampleId': 1137150691,
          'UtteranceText': 'order a pizza'
        },
        'hasError': False
      },
      {
        'value': {
          'ExampleId': 1137150692,
          'UtteranceText': 'order a large pepperoni pizza'
        },
        'hasError': False
      },
      {
        'value': {
          'ExampleId': 1137150693,
          'UtteranceText': 'i want two large pepperoni pizzas on thin crust'
        },
        'hasError': False
      }
    ]

    Request training:
    {
      'statusId': 9,
      'status': 'Queued'
    }

    Request training status:
    [
      {
        'modelId': 'edb46abf-0000-41ab-beb2-a41a0fe1630f',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': 'a5030be2-616c-4648-bf2f-380fa9417d37',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': 'e4b6704b-1636-474c-9459-fe9ccbeba51c',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '031d3777-2a00-4a7a-9323-9a3280a30000',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '9250e7a1-06eb-4413-9432-ae132ed32583',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從檔案系統中刪除該檔案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的最佳做法](../luis-concept-best-practices.md)
