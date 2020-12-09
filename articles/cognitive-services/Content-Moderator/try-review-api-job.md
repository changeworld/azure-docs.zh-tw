---
title: 使用 REST API 主控台的仲裁作業-內容仲裁
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
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905128"
---
# <a name="define-and-use-moderation-jobs-api-console"></a> (API 主控台定義和使用仲裁作業) 

仲裁作業可作為內容仲裁、工作流程和評論功能的一種包裝函式。 本指南說明如何使用作業 REST Api 來起始和檢查內容仲裁作業。 瞭解 Api 的結構之後，您就可以輕鬆地將這些呼叫移植到任何與 REST 相容的平臺。

## <a name="prerequisites"></a>必要條件

- 在內容仲裁者 [審核工具](https://contentmoderator.cognitive.microsoft.com/) 網站上登入或建立帳戶。
-  (選擇性) 定義要與作業搭配使用 [的自訂工作流程](./Review-Tool-User-Guide/Workflows.md) ;您也可以使用預設工作流程。

## <a name="create-a-job"></a>建立作業

若要建立仲裁作業，請移至 [ [作業-建立](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API 參考] 頁面，然後選取訂用帳戶區域的按鈕。 您可以在 [審核工具](https://contentmoderator.cognitive.microsoft.com/)的 [**認證**] 頁面上的端點 URL 中找到您的區域。 這會啟動 API 主控台，您可以在其中輕鬆地建立及執行 REST API 呼叫。

![[作業 - 建立] 頁面區域選取項目](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫參數

輸入下列值來建立 REST 呼叫：

- **teamName**：當您設定 [審核工具](https://contentmoderator.cognitive.microsoft.com/) 帳戶時所建立的小組識別碼 (在審核工具的認證畫面) 的 [ **識別碼** ] 欄位中找到。
- **ContentType**：這可以是「影像」、「文字」或「影片」。
- **ContentId**：自訂識別碼字串。 這個字串會傳遞至 API 並透過回呼傳回。 它適用于將內部識別碼或中繼資料與審核作業的結果產生關聯。
- **Workflowname**：您先前建立 (或預設工作流程) 預設值的工作流程名稱。
- **>callbackendpoint**： (選擇性) 在完成審核時接收回呼資訊的 URL。
- **Ocp Apim-訂用帳戶-金鑰**：您的內容仲裁金鑰。 您可以在 [審核工具](https://contentmoderator.cognitive.microsoft.com)的 [**設定**] 索引標籤上找到此金鑰。

### <a name="fill-in-the-request-body"></a>填寫要求本文

REST 呼叫的主體包含一個欄位 **ContentValue**。 如果您要仲裁文字，請貼上原始文字內容，或如果您要仲裁影像或影片，請輸入影像或影片 URL。 您可以使用下列範例影像 URL： [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![[作業 - 建立] 主控台查詢參數、標頭和要求本文方塊](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>提交您的要求

選取 [傳送]。 如果作業成功，則 **回應狀態** 為 `200 OK` ，而且 [ **回應內容** ] 方塊會顯示作業的識別碼。 複製此識別碼以在下列步驟中使用。

![[審查 - 建立] 主控台的 [回應內容] 方塊會顯示審查識別碼](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>取得作業狀態

若要取得執行中或已完成作業的狀態和詳細資料，請移至 [ [作業-取得](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API 參考] 頁面，然後選取您區域 (您的金鑰管理所在區域的按鈕) 。

![作業-取得區域選取專案](images/test-drive-region.png)

輸入 REST 呼叫參數，如上一節所示。 在這個步驟中， **JobId** 是您建立作業時所收到的唯一識別碼字串。 選取 [傳送]。 如果作業成功，則 **回應狀態** 為 `200 OK` ，而且 [ **回應內容** ] 方塊會以 JSON 格式顯示作業，如下所示：

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

### <a name="examine-the-new-reviews"></a>檢查新的評論 (s) 

如果您的內容作業導致建立評論，您可以在 [審核工具](https://contentmoderator.cognitive.microsoft.com)中加以查看。 選取 [**審核**  >  **影像** / **文字**] / **影片** (取決於您) 使用的內容。 內容應該會出現，可供人工審核。 在人力審核者審查自動指派的標記和預測資料並提交最終審核決策之後，作業 API 會將所有資訊提交至指定的回呼端點。

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何使用 REST API 來建立和查詢內容仲裁作業。 接下來，將作業整合至端對端仲裁案例，例如 [電子商務審核](./ecommerce-retail-catalog-moderation.md) 教學課程。