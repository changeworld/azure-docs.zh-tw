---
title: 自訂技能示例（Python）
titleSuffix: Azure Cognitive Search
description: 對於 Python 開發人員，瞭解使用 Azure 函數和視覺化工作室構建自訂技能的工具和技術。 自訂技能包含使用者定義的模型或邏輯，您可以將這些模型或邏輯添加到 Azure 認知搜索中豐富 AI 的索引管道中。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210460"
---
# <a name="example-create-a-custom-skill-using-python"></a>示例：使用 Python 創建自訂技能

在此 Azure 認知搜索技能集示例中，您將學習如何使用 Python 和 Visual Studio 代碼創建 Web API 自訂技能。 該示例使用實現[自訂技能介面](cognitive-search-custom-skill-interface.md)的[Azure 函數](https://azure.microsoft.com/services/functions/)。

自訂技能在設計上很簡單（它串聯了兩個字串），因此您可以專注于用於 Python 中自訂技能開發的工具和技術。 使用簡單技能成功後，可以使用更複雜的方案進行分支。

## <a name="prerequisites"></a>Prerequisites

+ 查看[自訂技能介面](cognitive-search-custom-skill-interface.md)，深入瞭解自訂技能應實現的輸入/輸出介面。

+ 設置環境。 我們遵循[本教程端到端地](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01)使用 Visual Studio 代碼和 Python 擴展設置無伺服器 Azure 函數。 本教程將引導您完成以下工具和元件的安裝： 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [視覺工作室代碼](https://code.visualstudio.com/)
  + [視覺化工作室代碼的 Python 擴展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure 函數核心工具](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [適用於 Visual Studio Code 的 Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>建立 Azure 函式

此示例使用 Azure 函數演示託管 Web API 的概念，但可以採用其他方法。 只要您符合[認知技能的介面需求](cognitive-search-custom-skill-interface.md)，採取的方式並不重要。 不過，Azure Functions 能讓您輕鬆建立自訂技能。

### <a name="create-a-function-app"></a>建立函數應用程式

Visual Studio Code 中的 Azure Functions 專案範本會建立可發佈至 Azure 中函式應用程式的專案。 函式應用程式可讓您將多個函式群組為邏輯單位，以便您管理、部署和共用資源。

1. 在 Visual Studio Code 中按 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 `Azure Functions: Create new project...`。

1. 選擇您專案工作區的目錄位置，然後選擇 [選取]****。

    > [!NOTE]
    > 這些步驟主要設計為在工作區以外的地方完成。 因此，不要選擇屬於工作區的專案資料夾。

1. 為函數應用專案選擇語言。 對於本教程，請選擇**Python**。
1. 選擇 Python 版本（Azure 函數支援版本 3.7.5）
1. 為專案的第一個函數選擇範本。 選擇**HTTP 觸發器**以在新函數應用中創建 HTTP 觸發功能。
1. 提供函數名稱。 在這種情況下，讓我們使用**串聯** 
1. 選擇 **"功能**"作為授權級別。 這意味著我們將提供[一個函數鍵](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)來調用函數的 HTTP 終結點。 
1. 選擇要如何打開專案。 對於此步驟，選擇 **"添加到工作區"** 以在當前工作區中創建函數應用。

Visual Studio Code 會在新的工作區中建立函式應用程式專案。 此專案包含 [host.json](../azure-functions/functions-host-json.md) 和 [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 組態檔，以及任何特定語言的專案檔。 

在函數應用專案的 **"Concatenator"** 資料夾中也創建了新的 HTTP 觸發函數。 裡面將有一個名為"init__.py"\_\_的檔，內容包括：

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

現在，讓我們修改該代碼以遵循[自訂技能介面](cognitive-search-custom-skill-interface.md)。 修改包含以下內容的代碼：

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

**transform_value**方法對單個記錄執行操作。 您可以修改方法以滿足您的特定需求。 請記住執行任何必要的輸入驗證，並返回如果無法完成該記錄的操作時生成的任何錯誤和警告。

### <a name="debug-your-code-locally"></a>在本地調試代碼

視覺化工作室代碼使調試代碼變得容易。 按"F5"或轉到**調試**功能表並選擇 **"開始調試**"。

您可以通過在感興趣行上點擊"F9"來設置代碼上的任何中斷點。

開始調試後，函數將在本地運行。 您可以使用郵遞員或 Fiddler 等工具向本地主機發出請求。 請注意本地終結點在終端視窗中的位置。 

## <a name="publish-your-function"></a>發佈函數

當您對函數行為感到滿意時，可以發佈它。

1. 在 Visual Studio Code 中按 F1 以開啟命令選擇區。 在命令調色板中，搜索並選擇 **"部署到函數應用..."** 

1. 選擇要在其中部署應用程式的 Azure 訂閱。

1. 選擇 **= 在 Azure 中創建新功能應用**

1. 為函數應用輸入全域唯一名稱。

1. 選擇 Python 版本（Python 3.7.x 適用于此功能）。

1. 為新資源選擇位置（例如，美國西部 2）。

此時，將在 Azure 訂閱中創建必要的資源，以在 Azure 上託管新的 Azure 函數。 等待部署完成。 輸出視窗將顯示部署過程的狀態。

1. 在[Azure 門戶](https://portal.azure.com)中，導航到 **"所有資源"，** 並查找按其名稱發佈的函數。 如果將其命名為 **"串聯"，** 請選擇資源。

1. 按一下 **"</>獲取功能 URL"** 按鈕。 這將允許您複製 URL 來調用函數。

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

此示例應產生與以前在本地環境中運行函數時看到的結果相同的結果。

## <a name="connect-to-your-pipeline"></a>連線到您的管線

有了新的自訂技能之後，就可以將它加入您的技能集。 下面的示例演示如何調用技能將文檔的標題和作者串聯到我們稱之為merged_title_author的單個欄位中。 替換為`[your-function-url-here]`新的 Azure 函數的 URL。

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
恭喜！ 您已經創建了第一個自訂技能。 現在您可以遵循相同的模式，新增自己的自訂功能。 按一下以下連結以瞭解更多資訊。

+ [權力技能：自訂技能的寶庫](https://github.com/Azure-Samples/azure-search-power-skills)
+ [向 AI 擴充管道添加自訂技能](cognitive-search-custom-skill-interface.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應豐富型欄位](cognitive-search-output-field-mapping.md) (英文)
