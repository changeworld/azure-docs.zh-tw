---
title: 使用 REST API 主控台定義審核工作流 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 可以使用 Azure 內容檢閱者審閱 API 根據內容策略定義自訂工作流和閾值。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754175"
---
# <a name="define-and-use-moderation-workflows-rest"></a>定義和使用審核工作流 （REST）

工作流是基於雲的自訂篩選器，可用於更高效地處理內容。 工作流可以連接到各種服務，以不同的方式篩選內容，然後採取適當的操作。 本指南介紹如何使用工作流 REST API（通過 API 主控台）創建和使用工作流。 瞭解 API 的結構後，可以輕鬆地將這些調用移植到任何與 REST 相容的平臺。

## <a name="prerequisites"></a>Prerequisites

- 在內容審閱人[審核工具](https://contentmoderator.cognitive.microsoft.com/)網站上登錄或創建帳戶。

## <a name="create-a-workflow"></a>建立工作流程

要創建或更新工作流，請轉到工作流**[- 創建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API 參考頁，然後選擇關鍵區域的按鈕（您可以在["審閱"工具](https://contentmoderator.cognitive.microsoft.com/)**的憑據**頁面上的終結點 URL 中找到該按鈕）。 這將啟動 API 主控台，您可以在其中輕鬆構造和運行 REST API 呼叫。

![[工作流程 - 建立或更新] 頁面的區域選取項目](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫參數

輸入**團隊**、**工作流名稱**和**Ocp-Apim-訂閱金鑰**的值：

- **團隊**：您在設置["審閱"工具](https://contentmoderator.cognitive.microsoft.com/)帳戶時創建的團隊 ID（在審閱工具的憑據螢幕上的 **"Id"** 欄位中找到）。
- **工作流名稱**：要添加的新工作流的名稱（或要更新現有工作流的現有名稱）。
- **Ocp-Apim-訂閱金鑰**：您的內容檢閱者金鑰。 您可以在["查看"工具](https://contentmoderator.cognitive.microsoft.com)的 **"設置"** 選項卡上找到此內容。

![[工作流程 - 建立或更新] 主控台查詢參數和標頭](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>輸入工作流定義

1. 編輯 **"請求正文**"框以輸入 JSON 請求，並包含**描述**和**類型**的詳細資訊`Text`（或`Image`）。
2. 對於**運算式**，複製預設工作流 JSON 運算式。 您的最終 JSON 字串應如下所示：

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
> 您可以使用此 API 為工作流定義簡單、複雜甚至嵌套運算式。 [工作流 - 創建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)文檔具有更複雜的邏輯示例。

### <a name="submit-your-request"></a>提交您的要求
  
選擇 **"發送**"。 如果作業成功，[Response status] \(回應狀態\)**** 就會是 `200 OK`，而 [Response content] \(回應內容\)**** 方塊則會顯示 `true`。

### <a name="examine-the-new-workflow"></a>檢查新工作流

在["審閱"工具](https://contentmoderator.cognitive.microsoft.com/)中，選擇 **"設置** > **工作流**"。 您的新工作流應顯示在清單中。

![工作流程的審核工具清單](images/workflow-console-new-workflow.PNG)

選擇工作流的 **"編輯"** 選項，然後轉到 **"設計器"** 選項卡。在這裡，您可以看到 JSON 邏輯的直觀表示形式。

![所選工作流程的 [設計工具] 索引標籤](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>取得工作流程詳細資料

若要檢索有關現有工作流的詳細資訊，請轉到工作流**[- 獲取](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API 參考頁，然後選擇區域的按鈕（管理金鑰的區域）。

![[工作流程 - 取得] 的區域選取項目](images/test-drive-region.png)

輸入上一節中所示的 REST 調用參數。 確保這次**工作流名稱**是現有工作流的名稱。

![取得查詢參數和標頭](images/workflow-get-default.PNG)

選擇 **"發送**"。 如果操作成功，**回應狀態**為`200 OK`，**回應內容**框以 JSON 格式顯示工作流，如下所示：

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