---
title: 表單辨識器自訂技能 (C#)
titleSuffix: Azure Cognitive Search
description: 瞭解如何使用 C# 和可視化工作室創建表單識別器自定義技能。
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274571"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>範例:建立表單識別器自訂技能

在此 Azure 認知搜尋技能集範例中,您將瞭解如何使用 C# 和 Visual Studio 創建表單識別器自定義技能。 表單識別器分析文檔並提取鍵/值對和表數據。 通過將表單辨識器包裝到[自訂技能介面](cognitive-search-custom-skill-interface.md)中,可以將此功能添加為端擴充管道中的步驟。 然後,管道可以載入文件並執行其他轉換。

## <a name="prerequisites"></a>Prerequisites

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (任何版本)。
- 至少五種相同類型的形式。 您可以使用本指南提供的範例資料。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>定型模型

在使用此技能之前,您需要使用輸入表單訓練表單識別器模型。 按照[cURL 快速入門](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract)瞭解如何訓練模型。 您可以使用該快速入門中提供的範例表單,也可以使用您自己的資料。 訓練模型后,將其ID值複製到安全位置。

## <a name="set-up-the-custom-skill"></a>設定自訂技能

本教學在[Azure 搜尋電源技能](https://github.com/Azure-Samples/azure-search-power-skills)GitHub 儲存庫中使用[分析窗體](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm)專案。 將此儲存庫克隆到本地電腦,並導航到**Vision/AnalyticsForm/** 以訪問專案。 然後在視覺工作室中打開_AnalyticsForm.csproj。_ 此項目創建一個 Azure 函數資源,該資源滿足[自定義技能介面](cognitive-search-custom-skill-interface.md),並可用於 Azure 認知搜索擴充。 它將形式文件作為輸入,並輸出(作為文本)您指定的鍵/值對。

首先,添加專案級環境變數。 在左側窗格中尋找**AnalyticsForm**專案,右鍵單擊它並選擇 **「屬性**」。。 在「**屬性」** 視窗中,按下 **「調試」** 選項卡,然後查找 **「環境變數」** 欄位。 點選 **「新增**」可新增以下變數:
* `FORMS_RECOGNIZER_ENDPOINT_URL`將值設置為終結點 URL。
* `FORMS_RECOGNIZER_API_KEY`將值設置為訂閱金鑰。
* `FORMS_RECOGNIZER_MODEL_ID`將值設定為您訓練的模型的 ID。
* `FORMS_RECOGNIZER_RETRY_DELAY`值設置為 1000。 此值是程式在重試查詢之前將等待的時間(以毫秒為單位)。
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`將值設置為 100。 此值是程式在嘗試成功回應時查詢服務的次數。

接下來,打開_AnalyzeForm.cs_並查找`fieldMappings`引用*欄位映射.json*檔的變數。 此檔(以及引用它的變數)定義要從窗體中提取的鍵清單以及每個鍵的自定義標籤。 例如`{ "Address:", "address" }, { "Invoice For:", "recipient" }`,值表示腳本將僅`Address:`保存檢測到的值`Invoice For:`和 欄位,並且它將`"address"`分別`"recipient"`用和標記這些值。

最後,請注意變數`contentType`。 此文稿在網址的遠端文件執行給定的表單辨識器模型,因此內容類型為`application/json`。 如果要透過 HTTP 要求包含其位元組流來分析本地檔案,則需要將改變`contentType`為檔案的相應[MIME 類型](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types)。

## <a name="test-the-function-from-visual-studio"></a>從 Visual Studio 測試函式

編輯專案後,請保存該專案,並將**AnalyticsForm**專案設置為 Visual Studio 中的啟動專案(如果尚未設置)。 然後按**F5**在本地環境中運行該函數。 使用 REST 服務(如[Postman)](https://www.postman.com/)呼叫函數。

### <a name="http-request"></a>HTTP 要求

您將發出以下請求來呼叫函數。

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Request body

從下面的請求正文範本開始。

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

在這裡,您需要提供與您訓練的表單類型相同的窗體的網址。 出於測試目的,您可以使用其中一個培訓表單。 如果遵循 cURL 快速啟動,則表單將位於 Azure Blob 儲存帳戶中。 打開 Azure 儲存資源管理器,尋找表單檔,右鍵單擊它,然後選擇 **「獲取共享存取簽名**」 。。 下一個對話框視窗將提供 URL 和 SAS 權杖。 分別在請求正文`"formUrl"`和`"formSasToken"`欄位中輸入這些字串。

> [!div class="mx-imgBorder"]
> ![Azure 儲存資源管理員;已選擇 pdf 文件](media/cognitive-search-skill-form/form-sas.png)

如果要分析不在 Azure Blob 儲存中的遠端文件,請將其`"formUrl"`URL 貼上到`"formSasToken"`欄位中,並將該欄位留空。

> [!NOTE]
> 當技能整合到技能集中時,URL和權杖將由認知搜尋提供。

### <a name="response"></a>回應

您應該會看到類似於以下範例的回應：

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>將函式發佈至 Azure

當您對函數行為感到滿意時,可以發佈它。

1. 在可視化工作室**中的解決方案資源管理器**中,右鍵單擊專案並選擇 **「發布**」。 選擇 **「創建新** > **發佈**」。
1. 如果您尚未將 Visual Studio 連線到您的 Azure 帳戶，請選取 [新增帳戶...]****。
1. 遵循螢幕上的提示進行。 為應用服務、Azure 訂閱、資源組、託管計劃和要使用的存儲帳戶指定唯一名稱。 如果尚未創建新的資源組、新的託管計劃和新的存儲帳戶,則可以創建新的資源組、新的託管計劃和新的存儲帳戶。 完成後,選擇 **"創建**"。
1. 部署完成後,請注意網站網址。 此 URL 是 Azure 中函數應用的位址。 將其保存到臨時位置。
1. 在[Azure 門戶](https://portal.azure.com)中,導航到資源組,並`AnalyzeForm`查找發佈的 函數。 在 [管理]**** 區段下，應該會看到主機金鑰。 複製*預設*主機金鑰並將其儲存到臨時位置。

## <a name="connect-to-your-pipeline"></a>連線到您的管線

要在認知搜尋管道中使用此技能,您需要向技能集添加技能定義。 以下 JSON 塊是一個範例技能定義(應更新輸入和輸出以反映您的特定方案和技能集環境)。 替換為`AzureFunctionEndpointUrl`函數網址,然後`AzureFunctionDefaultHostKey`替換為 主機鍵。

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

在本指南中,您從 Azure 窗體識別器服務創建了自定義技能。 要瞭解有關自定義技能的更多資訊,請參閱以下資源。 

* [Azure 搜尋電源技能:自訂技能的儲存庫](https://github.com/*zure-Samples/azure-search-power-skills)
* [加入 AI 擴充導管新增自訂技能](cognitive-search-custom-skill-interface.md)
* [定義技能集](cognitive-search-defining-skillset.md)
* [建立技能集 (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [映射富集欄位](cognitive-search-output-field-mapping.md)
