---
title: 使用仲裁作業搭配 REST API 主控台-內容仲裁
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "72935960"
---
# <a name="define-and-use-moderation-jobs-rest"></a>定義和使用審核作業（REST）

審核作業可做為內容仲裁、工作流程和評論功能的一種包裝函式。 本指南說明如何使用作業 REST Api 來起始及檢查內容審核作業。 一旦您瞭解 Api 的結構之後，就可以輕鬆地將這些呼叫移植到任何與 REST 相容的平臺。

## <a name="prerequisites"></a>先決條件

- 在內容仲裁[審查工具](https://contentmoderator.cognitive.microsoft.com/)網站上登入或建立帳戶。
- 選擇性定義要與您的作業搭配使用[的自訂工作流程](./Review-Tool-User-Guide/Workflows.md);您也可以使用預設工作流程。

## <a name="create-a-job"></a>建立作業

若要建立審核作業，請移至 [[作業-建立](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)API 參考] 頁面，然後選取訂用帳戶區域的按鈕（您可以在 [[審核] 工具](https://contentmoderator.cognitive.microsoft.com/)的 [**認證**] 頁面上的 [端點 URL] 中找到此資訊）。 這會啟動 API 主控台，您可以在其中輕鬆地建立和執行 REST API 呼叫。

![[作業 - 建立] 頁面區域選取項目](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫參數

輸入下列值以建立 REST 呼叫：

- **teamName**：當您設定[審核工具](https://contentmoderator.cognitive.microsoft.com/)帳戶時所建立的小組識別碼（可在審核工具的 [認證] 畫面上的 [**識別碼**] 欄位中找到）。
- **ContentType**：這可以是「影像」、「文字」或「影片」。
- **ContentId**：自訂識別碼字串。 這個字串會傳遞至 API 並透過回呼傳回。 這適用于將內部識別碼或中繼資料與審核作業的結果產生關聯。
- **Workflowname**：您先前建立的工作流程名稱（或預設工作流程的預設值）。
- **CallbackEndpoint**：（選擇性）完成審核時，用來接收回呼資訊的 URL。
- **Ocp-Apim-訂**用帳戶金鑰：您的內容仲裁金鑰。 您可以在[審核工具](https://contentmoderator.cognitive.microsoft.com)的 [**設定**] 索引標籤上找到此資訊。

### <a name="fill-in-the-request-body"></a>填寫要求本文

REST 呼叫的主體包含一個欄位**ContentValue**。 如果您要仲裁文字，請貼上原始文字內容; 如果您要仲裁影像/影片，請輸入影像或影片 URL。 您可以使用下列範例影像 URL：[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![[作業 - 建立] 主控台查詢參數、標頭和要求本文方塊](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>提交您的要求

選取 [**傳送**]。 如果作業成功，**回應狀態**會是， `200 OK`而 [**回應內容**] 方塊會顯示工作的識別碼。 複製此識別碼以在下列步驟中使用。

![[審查 - 建立] 主控台的 [回應內容] 方塊會顯示審查識別碼](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>取得作業狀態

若要取得執行中或已完成工作的狀態和詳細資料，請移至 [[作業-取得](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3)API 參考] 頁面，然後選取您的區域（您的金鑰管理所在的區域）的按鈕。

![作業-取得區域選取範圍](images/test-drive-region.png)

輸入 REST 呼叫參數，如上一節所示。 在此步驟中， **JobId**是您在建立作業時所收到的唯一識別碼字串。 選取 [**傳送**]。 如果作業成功，**回應狀態**會是， `200 OK`而 [**回應內容**] 方塊會以 JSON 格式顯示工作，如下所示：

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

![作業-取得 REST 呼叫回應](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>檢查新的評論

如果您的內容作業導致建立審查，您可以在[審核工具](https://contentmoderator.cognitive.microsoft.com)中加以查看。 選取 [**審查** > **影像**/**文字**/] [**影片**] （視您使用的內容而定）。 內容應該會出現，準備好進行人工審核。 在人力仲裁者審查自動指派的標記和預測資料並提交最終審核決策之後，作業 API 會將所有這項資訊提交至指定的回呼端點端點。

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何使用 REST API 來建立及查詢內容仲裁作業。 接下來，將作業整合到端對端仲裁案例，例如[電子商務審核](./ecommerce-retail-catalog-moderation.md)教學課程。