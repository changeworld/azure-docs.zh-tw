---
title: 使用 REST API 主控台定義仲裁工作流程-內容仲裁
titleSuffix: Azure Cognitive Services
description: 您可以使用 Azure 內容仲裁者審核 Api，根據您的內容原則來定義自訂工作流程和閾值。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "72754175"
---
# <a name="define-and-use-moderation-workflows-rest"></a>定義和使用仲裁工作流程（REST）

工作流程是以雲端為基礎的自訂篩選器，您可以用來更有效率地處理內容。 工作流程可以連接到各種服務，以不同的方式篩選內容，然後採取適當的動作。 本指南說明如何使用工作流程 REST Api （透過 API 主控台）來建立和使用工作流程。 一旦您瞭解 Api 的結構之後，就可以輕鬆地將這些呼叫移植到任何與 REST 相容的平臺。

## <a name="prerequisites"></a>先決條件

- 在內容仲裁[審查工具](https://contentmoderator.cognitive.microsoft.com/)網站上登入或建立帳戶。

## <a name="create-a-workflow"></a>建立工作流程

若要建立或更新工作流程，請移至 [**[工作流程-建立或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API 參考] 頁面，然後選取您的金鑰區域的按鈕（您可以在[審核工具](https://contentmoderator.cognitive.microsoft.com/)的 [**認證**] 頁面上的 [端點 URL] 中找到此資訊）。 這會啟動 API 主控台，您可以在其中輕鬆地建立和執行 REST API 呼叫。

![[工作流程 - 建立或更新] 頁面的區域選取項目](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫參數

輸入 [ **team**]、[ **workflowname**] 和 [ **Ocp-Apim-訂**用帳戶金鑰] 的值：

- **team**：您設定[審核工具](https://contentmoderator.cognitive.microsoft.com/)帳戶時所建立的小組識別碼（可在審核工具的 [認證] 畫面上的 [**識別碼**] 欄位中找到）。
- **workflowname**：要新增的新工作流程名稱（如果您想要更新現有的工作流程，則為現有的名稱）。
- **Ocp-Apim-訂**用帳戶金鑰：您的內容仲裁金鑰。 您可以在[審核工具](https://contentmoderator.cognitive.microsoft.com)的 [**設定**] 索引標籤上找到此資訊。

![[工作流程 - 建立或更新] 主控台查詢參數和標頭](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>輸入工作流程定義

1. 編輯 [**要求**本文] 方塊以輸入 JSON 要求，其中包含**描述**和**類型**的詳細`Image`資料`Text`（或）。
2. 針對 [**運算式**]，複製預設工作流程 JSON 運算式。 最後的 JSON 字串看起來應該像這樣：

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> 您可以使用此 API，為您的工作流程定義簡單、複雜且甚至是嵌套的運算式。 [工作流程-建立或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)檔包含更複雜邏輯的範例。

### <a name="submit-your-request"></a>提交您的要求
  
選取 [**傳送**]。 如果作業成功，[Response status] \(回應狀態\)**** 就會是 `200 OK`，而 [Response content] \(回應內容\)**** 方塊則會顯示 `true`。

### <a name="examine-the-new-workflow"></a>檢查新的工作流程

在[審核工具](https://contentmoderator.cognitive.microsoft.com/)中，選取 **[設定** > ] [**工作流程**]。 您的新工作流程應該會出現在清單中。

![工作流程的審核工具清單](images/workflow-console-new-workflow.PNG)

選取工作流程的 [**編輯**] 選項，然後移至 [**設計**工具] 索引標籤。在這裡，您可以看到以直覺方式呈現的 JSON 邏輯。

![所選工作流程的 [設計工具] 索引標籤](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>取得工作流程詳細資料

若要取得現有工作流程的詳細資料，請移至 [**[工作流程-取得](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API 參考] 頁面，然後選取您的區域（您的金鑰管理所在的區域）的按鈕。

![[工作流程 - 取得] 的區域選取項目](images/test-drive-region.png)

輸入 REST 呼叫參數，如上一節所示。 請確定這次**workflowname**是現有工作流程的名稱。

![取得查詢參數和標頭](images/workflow-get-default.PNG)

選取 [**傳送**]。 如果作業成功，**回應狀態**會是， `200 OK`而 [**回應內容**] 方塊會以 JSON 格式顯示工作流程，如下所示：

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>後續步驟

- 了解如何搭配[內容審核作業](try-review-api-job.md)使用工作流程。