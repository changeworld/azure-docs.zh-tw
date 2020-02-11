---
title: 自訂技能範例（Python）
titleSuffix: Azure Cognitive Search
description: 針對 Python 開發人員，瞭解使用 Azure Functions 和 Visual Studio 建立自訂技能的工具和技術。 自訂技能包含使用者定義的模型或邏輯，您可以將其新增至 Azure 認知搜尋中的 AI 擴充索引管線。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e4f27e582e6b920e9de068fe2c3270af19ad8a17
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122415"
---
# <a name="example-create-a-custom-skill-using-python"></a>範例：使用 Python 建立自訂技能

在此 Azure 認知搜尋技能集範例中，您將瞭解如何使用 Python 和 Visual Studio Code 建立 Web API 的自訂技能。 此範例會使用可執行[自訂技能介面](cognitive-search-custom-skill-interface.md)的[Azure](https://azure.microsoft.com/services/functions/)函式。

自訂技能的設計很簡單（它會串連兩個字串），因此您可以專注于用於 Python 中自訂技能開發的工具和技術。 當您成功完成簡單的技能之後，您可以使用更複雜的案例來進行分支。

## <a name="prerequisites"></a>必要條件

+ 請參閱[自訂技能介面](cognitive-search-custom-skill-interface.md)，以取得自訂技能應實行的輸入/輸出介面簡介。

+ 設定您的環境。 我們已遵循[本教學課程的端對端](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01)，以使用 Visual Studio Code 和 Python 擴充功能來設定無伺服器 Azure 函式。 本教學課程會引導您安裝下列工具和元件： 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [適用於 Visual Studio Code 的 Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-python.python) \(英文\)
  + [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [適用於 Visual Studio Code 的 Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>建立 Azure 函式

這個範例會使用 Azure 函式來示範裝載 Web API 的概念，但其他方法也是可行的。 只要您符合[認知技能的介面需求](cognitive-search-custom-skill-interface.md)，採取的方式並不重要。 不過，Azure Functions 能讓您輕鬆建立自訂技能。

### <a name="create-a-function-app"></a>建立函數應用程式

Visual Studio Code 中的 Azure Functions 專案範本會建立可發佈至 Azure 中函式應用程式的專案。 函式應用程式可讓您將多個函式群組為邏輯單位，以便您管理、部署和共用資源。

1. 在 Visual Studio Code 中按 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 `Azure Functions: Create new project...`。

1. 選擇您專案工作區的目錄位置，然後選擇 [選取]。

    > [!NOTE]
    > 這些步驟主要設計為在工作區以外的地方完成。 基於這個理由，請勿選取屬於工作區的專案資料夾。

1. 選取函數應用程式專案的語言。 在本教學課程中，選取 [ **Python**]。
1. 選取 Python 版本，（Azure Functions 支援版本3.7.5）
1. 為您專案的第一個函式選取範本。 選取 [ **HTTP 觸發**程式]，在新的函數應用程式中建立 HTTP 觸發的函式。
1. 提供函數名稱。 在此情況下，讓我們使用**Concatenator** 
1. 選取 [**函數**] 做為授權層級。 這表示我們將提供[函數金鑰](../azure-functions/functions-bindings-http-webhook.md#authorization-keys)來呼叫函式的 HTTP 端點。 
1. 選取您想要開啟專案的方式。 在此步驟中，選取 [**新增至工作區**] 以在目前的工作區中建立函數應用程式。

Visual Studio Code 會在新的工作區中建立函式應用程式專案。 此專案包含 [host.json](../azure-functions/functions-host-json.md) 和 [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 組態檔，以及任何特定語言的專案檔。 

也會在函式應用程式專案的**Concatenator**資料夾中建立新的 HTTP 觸發函式。 裡面會有一個名為 "\_\_init__. .py" 的檔案，其中包含下列內容：

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

現在，讓我們修改該程式碼，使其遵循[自訂技能介面](cognitive-search-custom-skill-interface.md)）。 使用下列內容修改程式碼：

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

**Transform_value**方法會對單一記錄執行作業。 您可以修改方法，以符合您的特定需求。 請記得執行任何必要的輸入驗證，如果無法完成該記錄的作業，則傳回產生的任何錯誤和警告。

### <a name="debug-your-code-locally"></a>在本機偵錯工具代碼

Visual Studio Code 可讓您輕鬆地對程式碼進行偵錯工具。 按 ' F5 ' 或移至 [**調試**] 功能表，然後選取 [**開始調試**]。

您可以在相關的那一行上叫用 ' F9 '，以在程式碼上設定任何中斷點。

一旦您開始進行偵錯工具，您的函式將會在本機執行。 您可以使用 Postman 或 Fiddler 之類的工具，向 localhost 發出要求。 請注意您的本機端點在終端機視窗中的位置。 

## <a name="publish-your-function"></a>發佈您的函式

當您對函式行為感到滿意時，就可以發行它。

1. 在 Visual Studio Code 中按 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 [**部署至函數應用程式 ...** ]。 

1. 選取您想要在其中部署應用程式的 Azure 訂用帳戶。

1. **在 Azure 中選取 + 建立新的函數應用程式**

1. 輸入函數應用程式的全域唯一名稱。

1. 選取 Python 版本（Python 3.7. x 適用于此功能）。

1. 選取新資源的位置（例如，美國西部2）。

此時，將會在您的 Azure 訂用帳戶中建立所需的資源，以在 Azure 上裝載新的 Azure Function。 等待部署完成。 [輸出] 視窗會顯示部署流程的狀態。

1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至 **所有資源**，然後尋找您依其名稱發佈的函式。 如果您將它命名為**Concatenator**，請選取資源。

1. 按一下 [ **</> 取得函數 URL** ] 按鈕。 這可讓您複製 URL 以呼叫函數。

## <a name="test-the-function-in-azure"></a>在 Azure 測試函式

有了預設主機金鑰之後，請如下列所示測試您的函式：

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>要求本文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

在本機環境中執行此函式時，此範例應該會產生與您先前看到的相同結果。

## <a name="connect-to-your-pipeline"></a>連線到您的管線

有了新的自訂技能之後，就可以將它加入您的技能集。 下列範例示範如何呼叫技能，將檔的標題和作者串連到我們呼叫 merged_title_author 的單一欄位中。 將 `[your-function-url-here]` 取代為新 Azure 函式的 URL。

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>後續步驟
恭喜！ 您已建立您的第一個自訂技能。 現在您可以遵循相同的模式，新增自己的自訂功能。 若要深入瞭解，請按一下下列連結。

+ [電力技能：自訂技能的存放庫](https://github.com/Azure-Samples/azure-search-power-skills)
+ [將自訂技能新增至 AI 擴充管線](cognitive-search-custom-skill-interface.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應豐富型欄位](cognitive-search-output-field-mapping.md) (英文)
