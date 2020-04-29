---
title: 使用 REST API 主控台建立仲裁審查-內容仲裁
titleSuffix: Azure Cognitive Services
description: 使用 Azure 內容仲裁審核 Api 來建立影像或文字審查，以進行人工審核。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "72757149"
---
# <a name="create-human-reviews-rest"></a>建立人力審查（REST）

[審查](./review-api.md#reviews)儲存和顯示內容，供人力仲裁者評估。 當使用者完成審查時，會將結果傳送至指定的回呼端點。 在本指南中，您將瞭解如何透過 API 主控台使用審查 REST Api 來設定評論。 一旦您瞭解 Api 的結構之後，就可以輕鬆地將這些呼叫移植到任何與 REST 相容的平臺。

## <a name="prerequisites"></a>先決條件

- 在內容仲裁[審查工具](https://contentmoderator.cognitive.microsoft.com/)網站上登入或建立帳戶。

## <a name="create-a-review"></a>建立審核

若要建立審查，請移至 [**[審查-建立](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 參考] 頁面，然後選取金鑰區域的按鈕（您可以在 [[審核] 工具](https://contentmoderator.cognitive.microsoft.com/)的 [**認證**] 頁面上的 [端點 URL] 中找到此資訊）。 這會啟動 API 主控台，您可以在其中輕鬆地建立和執行 REST API 呼叫。

![審查-取得區域選取專案](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫參數

輸入**teamName**的值，以及**Apim-訂**用帳戶金鑰：

- **teamName**：當您設定[審核工具](https://contentmoderator.cognitive.microsoft.com/)帳戶時所建立的小組識別碼（可在審核工具的 [認證] 畫面上的 [**識別碼**] 欄位中找到）。
- **Ocp-Apim-訂**用帳戶金鑰：您的內容仲裁金鑰。 您可以在[審核工具](https://contentmoderator.cognitive.microsoft.com)的 [**設定**] 索引標籤上找到此資訊。

### <a name="enter-a-review-definition"></a>輸入審查定義

編輯 [**要求**本文] 方塊，以輸入具有下欄欄位的 JSON 要求：

- **中繼資料**：要傳回給回呼端點的自訂索引鍵/值組。 如果金鑰是在[審核工具](https://contentmoderator.cognitive.microsoft.com)中定義的簡短程式碼，則會顯示為標記。
- **內容**：在影像和影片內容的案例中，這是指向內容的 URL 字串。 對於文字內容，這是實際的文字字串。
- **ContentId**：自訂識別碼字串。 這個字串會傳遞至 API 並透過回呼傳回。 這適用于將內部識別碼或中繼資料與審核作業的結果產生關聯。
- **CallbackEndpoint**：（選擇性）完成審核時，用來接收回呼資訊的 URL。

預設的要求本文會顯示您可以建立的不同評論類型的範例：

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>提交您的要求
  
選取 [**傳送**]。 如果作業成功，**回應狀態**會是， `200 OK`而 [**回應內容**] 方塊會顯示審核的識別碼。 複製此識別碼以在下列步驟中使用。

![[審查 - 建立] 主控台的 [回應內容] 方塊會顯示審查識別碼](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>檢查新的評論

在[審核工具](https://contentmoderator.cognitive.microsoft.com)中，選取 **[審查** > **影像**/**文字**/] [**影片**] （視您使用的內容而定）。 您上傳的內容應該會出現，準備好進行人工審核。

![足球的審查工具影像](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>取得審核詳細資料

若要取得有關現有評論的詳細資料，請移至 [[審查-取得](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2)API 參考] 頁面，然後選取您的區域（您的金鑰管理所在的區域）的按鈕。

![[工作流程 - 取得] 的區域選取項目](images/test-drive-region.png)

輸入 REST 呼叫參數，如上一節所示。 在此步驟中， **reviewId**是您在建立審核時所收到的唯一識別碼字串。

![審查 - 建立主控台的取得結果](images/test-drive-review-3.PNG)
  
選取 [**傳送**]。 如果作業成功，**回應狀態**會是， `200 OK`而 [**回應內容**] 方塊會以 JSON 格式顯示審核詳細資料，如下所示：

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

請記下回應中的下欄欄位：

- **status**
- **reviewerResultTags**：如果人工審核小組已手動新增任何標記（顯示**createdBy**欄位），就會出現此情況。
- **中繼資料**：這會顯示在人工審核小組進行變更之前，一開始在審查中新增的標記。

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何使用 REST API 來建立內容仲裁審核。 接下來，將審查整合到端對端仲裁案例，例如[電子商務審核](./ecommerce-retail-catalog-moderation.md)教學課程。