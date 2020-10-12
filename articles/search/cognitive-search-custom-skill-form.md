---
title: '表單辨識器自訂技能 (c # ) '
titleSuffix: Azure Cognitive Search
description: '瞭解如何使用 c # 和 Visual Studio 建立表單辨識器自訂技能。'
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 58f1c2621165a7074c04752832c6560b2fd3e423
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935427"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>範例：建立表單辨識器自訂技能

在此 Azure 認知搜尋技能集範例中，您將瞭解如何使用 c # 和 Visual Studio 來建立表單辨識器自訂技能。 表單辨識器會分析檔，並將索引鍵/值組和資料表資料解壓縮。 藉由將表單辨識器包裝在 [自訂技能介面](cognitive-search-custom-skill-interface.md)中，您可以將此功能新增為端對端擴充管線中的一個步驟。 然後，管線可以載入檔並進行其他轉換。

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (任何版本) 。
- 至少五個相同類型的表單。 您可以使用本指南所提供的範例資料。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>定型模型

在使用此技能之前，您必須先使用輸入表單來訓練表單辨識器模型。 請依照 [捲曲的快速入門](../cognitive-services/form-recognizer/quickstarts/curl-train-extract.md) ，瞭解如何將模型定型。 您可以使用該快速入門中提供的範例表單，也可以使用您自己的資料。 定型模型之後，請將其 ID 值複製到安全的位置。

## <a name="set-up-the-custom-skill"></a>設定自訂技能

本教學課程會使用[Azure 搜尋服務 Power 技能](https://github.com/Azure-Samples/azure-search-power-skills)GitHub 存放庫中的[AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm)專案。 將此存放庫複製到您的本機電腦，並流覽至 **視覺/AnalyzeForm/** 以存取專案。 然後，在 Visual Studio 中開啟_AnalyzeForm。_ 此專案會建立可滿足 [自訂技能介面](cognitive-search-custom-skill-interface.md) 並可用於 Azure 認知搜尋擴充的 Azure 函數資源。 它會將表單檔做為輸入，並以文字) 您指定的索引鍵/值組來輸出 (。

首先，加入專案層級的環境變數。 在左窗格中找出 **AnalyzeForm** 專案，以滑鼠右鍵按一下該專案，然後選取 [ **屬性**]。 在 [ **屬性** ] 視窗中，按一下 [ **調試** ] 索引標籤，然後尋找 [ **環境變數** ] 欄位。 按一下 [ **新增** ] 以新增下列變數：
* `FORMS_RECOGNIZER_ENDPOINT_URL` 將值設定為您的端點 URL。
* `FORMS_RECOGNIZER_API_KEY` 將值設定為您的訂用帳戶金鑰。
* `FORMS_RECOGNIZER_MODEL_ID` 將值設定為您定型之模型的識別碼。
* `FORMS_RECOGNIZER_RETRY_DELAY` 值設定為1000。 此值是程式在重試查詢之前等候的時間（以毫秒為單位）。
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` 值設定為100。 此值是程式在嘗試取得成功的回應時，會查詢服務的次數。

接下來，開啟 _AnalyzeForm.cs_ ，並尋找 `fieldMappings` 參考檔案 *field-mappings.js* 的變數。 這個檔案 (，而參考它的變數) 會定義您想要從表單解壓縮的索引鍵清單，以及每個索引鍵的自訂標籤。 例如，的值 `{ "Address:", "address" }, { "Invoice For:", "recipient" }` 表示腳本只會儲存所偵測到之和欄位的值 `Address:` `Invoice For:` ，而且會分別以和標記這些值 `"address"` `"recipient"` 。

最後，記下 `contentType` 變數。 此腳本會對 URL 所參考的遠端檔執行指定的表單辨識器模型，因此內容類型為 `application/json` 。 如果您想要在 HTTP 要求中包含其位元組資料流程來分析本機檔案，您必須將檔案的變更 `contentType` 為適當的 [MIME 類型](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) 。

## <a name="test-the-function-from-visual-studio"></a>從 Visual Studio 測試函式

在您編輯專案之後，請將其儲存，並將 **AnalyzeForm** 專案設定為 Visual Studio 中的啟始專案 (如果尚未設定) 。 然後按 **F5** 鍵，在您的本機環境中執行函式。 使用 REST 服務（如 [Postman](https://www.postman.com/) ）來呼叫函數。

### <a name="http-request"></a>HTTP 要求

您將會提出下列要求以呼叫函數。

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Request body

從下方的要求本文範本開始。

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

在此，您將需要提供表單的 URL，該表單的類型與您用來定型的表單相同。 基於測試目的，您可以使用其中一個訓練表單。 如果您已遵循捲曲快速入門，您的表單將會位於 Azure blob 儲存體帳戶中。 開啟 Azure 儲存體總管，找出表單檔案，在其上按一下滑鼠右鍵，然後選取 [ **取得共用存取**簽章]。 下一個對話方塊視窗將提供 URL 和 SAS 權杖。 `"formUrl"`分別在要求主體的和欄位中輸入這些字串 `"formSasToken"` 。

> [!div class="mx-imgBorder"]
> ![Azure 儲存體 explorer;已選取 pdf 檔](media/cognitive-search-skill-form/form-sas.png)

如果您想要分析不在 Azure blob 儲存體中的遠端檔，請在欄位中貼上其 URL， `"formUrl"` 並將 `"formSasToken"` 欄位保留空白。

> [!NOTE]
> 當技能整合在技能集中時，認知搜尋會提供 URL 和權杖。

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

當您對函式行為感到滿意時，就可以將它發行。

1. 在 Visual Studio 的 **方案總管** 中，以滑鼠右鍵按一下專案，然後選取 [ **發行**]。 選擇 [**建立新**  >  **發行**]。
1. 如果您尚未將 Visual Studio 連線到您的 Azure 帳戶，請選取 [新增帳戶...]****。
1. 遵循螢幕上的提示進行。 為您的 app service、Azure 訂用帳戶、資源群組、主控方案，以及您想要使用的儲存體帳戶指定唯一的名稱。 您可以建立新的資源群組、新的主控方案，以及新的儲存體帳戶（如果您還沒有的話）。 完成之後，選取 [建立]。
1. 部署完成之後，請注意網站 URL。 此 URL 是您在 Azure 中的函數應用程式位址。 將它儲存至暫存位置。
1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至資源群組，並尋找您發行的函式 `AnalyzeForm` 。 在 [管理]**** 區段下，應該會看到主機金鑰。 複製 *預設* 的主機金鑰，並將它儲存至暫存位置。

## <a name="connect-to-your-pipeline"></a>連線到您的管線

若要在認知搜尋管線中使用這項技術，您必須將技能定義新增至您的技能集。 下列 JSON 區塊是範例技能定義 (您應該更新輸入和輸出，以反映您的特定案例和技能集環境) 。 `AzureFunctionEndpointUrl`以您的函式 URL 取代，並 `AzureFunctionDefaultHostKey` 以您的主機金鑰取代。

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

在本指南中，您已從 Azure 表單辨識器服務建立自訂技能。 若要深入瞭解自訂技能，請參閱下列資源。 

* [Azure 搜尋服務的強大技能：自訂技能的儲存機制](https://github.com/Azure-Samples/azure-search-power-skills)
* [將自訂技能新增至 AI 擴充管線](cognitive-search-custom-skill-interface.md)
* [定義技能集](cognitive-search-defining-skillset.md)
* [建立技能集 (REST) ](/rest/api/searchservice/create-skillset)
* [對應擴充的欄位](cognitive-search-output-field-mapping.md)