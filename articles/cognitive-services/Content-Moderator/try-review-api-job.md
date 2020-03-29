---
title: 將審核作業與 REST API 主控台一起使用 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 使用檢閱 API 的作業操作來對 Azure Content Moderator 中的影像或文字內容起始端對端內容審核。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935960"
---
# <a name="define-and-use-moderation-jobs-rest"></a>定義和使用審核作業 （REST）

審核作業充當內容審核、工作流和審閱功能的包裝器。 本指南介紹如何使用作業 REST API 啟動和檢查內容審核作業。 瞭解 API 的結構後，可以輕鬆地將這些調用移植到任何與 REST 相容的平臺。

## <a name="prerequisites"></a>Prerequisites

- 在內容審閱人[審核工具](https://contentmoderator.cognitive.microsoft.com/)網站上登錄或創建帳戶。
- （可選）定義要與作業一起使用[的自訂工作流](./Review-Tool-User-Guide/Workflows.md);您還可以使用預設工作流。

## <a name="create-a-job"></a>建立作業

要創建審核作業，請轉到作業[- 創建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)API 參考頁，然後為訂閱區域選擇按鈕（您可以在["審閱"工具](https://contentmoderator.cognitive.microsoft.com/)的 **"憑據"** 頁上的終結點 URL 中找到該按鈕）。 這將啟動 API 主控台，您可以在其中輕鬆構造和運行 REST API 呼叫。

![[作業 - 建立] 頁面區域選取項目](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫參數

輸入以下值以構造 REST 調用：

- **團隊名稱**：您在設置[審閱工具](https://contentmoderator.cognitive.microsoft.com/)帳戶時創建的團隊 ID（在審閱工具的憑據螢幕上的 **"Id"** 欄位中找到）。
- **內容類型**：可以是"圖像"、"文本"或"視頻"。
- **內容 Id**：自訂識別碼字串。 這個字串會傳遞至 API 並透過回呼傳回。 它可用於將內部識別碼或中繼資料與審核作業的結果相關聯。
- **工作流名稱**：以前創建的工作流的名稱（或預設工作流的"預設"）。
- **回檔終結點**：（可選） 審核完成後接收回調資訊的 URL。
- **Ocp-Apim-訂閱金鑰**：您的內容檢閱者金鑰。 您可以在["查看"工具](https://contentmoderator.cognitive.microsoft.com)的 **"設置"** 選項卡上找到此內容。

### <a name="fill-in-the-request-body"></a>填寫請求正文

REST 調用的正文包含一個欄位"**內容值**"。 如果要調節文本，請粘貼原始文本內容，或者輸入圖像或視頻 URL（如果要調節圖像/視頻）。 您可以使用以下示例圖像 URL：[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![[作業 - 建立] 主控台查詢參數、標頭和要求本文方塊](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>提交您的要求

選擇 **"發送**"。 如果操作成功，**回應狀態**為`200 OK`，**回應內容**框將顯示作業的 ID。 複製此識別碼以在下列步驟中使用。

![[審查 - 建立] 主控台的 [回應內容] 方塊會顯示審查識別碼](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>取得作業狀態

要獲取正在運行的或已完成作業的狀態和詳細資訊，請轉到作業 -[獲取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3)API 參考頁，然後選擇您所在區域的按鈕（管理金鑰的區域）。

![作業 - 獲取區域選擇](images/test-drive-region.png)

輸入上一節中所示的 REST 調用參數。 對於此步驟 **，JobId**是創建作業時收到的唯一 ID 字串。 選擇 **"發送**"。 如果操作成功，**回應狀態**為`200 OK`，**回應內容**框以 JSON 格式顯示作業，如下所示：

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![作業 - 獲取 REST 呼叫回應](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>檢查新審查

如果您的內容作業導致創建了審閱，則可以在["審閱"工具](https://contentmoderator.cognitive.microsoft.com)中查看它。 選擇 **"查看** > **圖像**/**文本**/**視頻**"（具體取決於您使用的內容）。 內容應顯示，準備進行人工審閱。 在人工審閱人審核自動分配的標記和預測資料並提交最終審核決策後，作業 API 會將所有此資訊提交到指定的回檔終結點。

## <a name="next-steps"></a>後續步驟

在本指南中，您學習了如何使用 REST API 創建和查詢內容審核作業。 接下來，將作業集成到端到端審核方案中，如[電子商務審核](./ecommerce-retail-catalog-moderation.md)教程。