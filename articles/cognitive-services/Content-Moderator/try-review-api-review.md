---
title: 使用 REST API 主控台創建審核審核 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 使用 Azure 內容檢閱者審閱 API 創建圖像或文本審閱以進行人工審核。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757149"
---
# <a name="create-human-reviews-rest"></a>創建人工評論 （REST）

[評論](./review-api.md#reviews)存儲和顯示內容，供人工版主評估。 當使用者完成審核時，結果將發送到指定的回檔終結點。 在本指南中，您將瞭解如何使用通過 API 主控台查看 REST API 設置評論。 瞭解 API 的結構後，可以輕鬆地將這些調用移植到任何與 REST 相容的平臺。

## <a name="prerequisites"></a>Prerequisites

- 在內容審閱人[審核工具](https://contentmoderator.cognitive.microsoft.com/)網站上登錄或創建帳戶。

## <a name="create-a-review"></a>建立審核

要創建審核，請轉到**["審閱 - 創建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 參考頁"，然後為關鍵區域選擇按鈕（您可以在["審閱"工具](https://contentmoderator.cognitive.microsoft.com/)的 **"憑據"** 頁上的終結點 URL 中找到該按鈕）。 這將啟動 API 主控台，您可以在其中輕鬆構造和運行 REST API 呼叫。

![查看 - 獲取區域選擇](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫參數

輸入**團隊名稱**和**Ocp-Apim-訂閱金鑰**的值：

- **團隊名稱**：您在設置[審閱工具](https://contentmoderator.cognitive.microsoft.com/)帳戶時創建的團隊 ID（在審閱工具的憑據螢幕上的 **"Id"** 欄位中找到）。
- **Ocp-Apim-訂閱金鑰**：您的內容檢閱者金鑰。 您可以在["查看"工具](https://contentmoderator.cognitive.microsoft.com)的 **"設置"** 選項卡上找到此內容。

### <a name="enter-a-review-definition"></a>輸入審閱定義

編輯 **"請求正文**"框以輸入具有以下欄位的 JSON 請求：

- **中繼資料**：要返回到回檔終結點的自訂鍵值對。 如果該鍵是在["審閱"工具](https://contentmoderator.cognitive.microsoft.com)中定義的短代碼，則該鍵顯示為標記。
- **內容**：在圖像和視頻內容的情況下，這是指向內容的 URL 字串。 對於文本內容，這是實際的文本字串。
- **內容 Id**：自訂識別碼字串。 這個字串會傳遞至 API 並透過回呼傳回。 它可用於將內部識別碼或中繼資料與審核作業的結果相關聯。
- **回檔終結點**：（可選） 審核完成後接收回調資訊的 URL。

預設請求正文顯示您可以創建的不同類型的評論的示例：

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
  
選擇 **"發送**"。 如果操作成功，**回應狀態**為`200 OK`，**回應內容**框將顯示審閱的 ID。 複製此識別碼以在下列步驟中使用。

![[審查 - 建立] 主控台的 [回應內容] 方塊會顯示審查識別碼](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>檢查新審核

在["審閱"工具](https://contentmoderator.cognitive.microsoft.com)中，選擇 **"查看** > **圖像**/**文本**/**視頻**"（具體取決於您使用的內容）。 應顯示您上傳的內容，以便進行人工審閱。

![足球的審查工具影像](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>取得審核詳細資料

若要檢索有關現有審核的詳細資訊，請轉到["審核 - 獲取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2)API 參考頁"並選擇您所在區域的按鈕（管理金鑰的區域）。

![[工作流程 - 取得] 的區域選取項目](images/test-drive-region.png)

輸入上一節中所示的 REST 調用參數。 對於此步驟 **，reviewId**是創建審閱時收到的唯一 ID 字串。

![審查 - 建立主控台的取得結果](images/test-drive-review-3.PNG)
  
選擇 **"發送**"。 如果操作成功，**回應狀態**為`200 OK`，**回應內容**框以 JSON 格式顯示審閱詳細資訊，如下所示：

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

請注意回應中的以下欄位：

- **狀態**
- **檢閱者結果標籤**：如果人工審閱團隊手動添加了任何標記（顯示**創建 By**欄位），則會出現此標記。
- **中繼資料**：這顯示了在人工評審團隊進行更改之前最初在審核中添加的標記。

## <a name="next-steps"></a>後續步驟

在本指南中，您學習了如何使用 REST API 創建內容審核。 接下來，將評論集成到端到端審核方案中，如[電子商務審核](./ecommerce-retail-catalog-moderation.md)教程。