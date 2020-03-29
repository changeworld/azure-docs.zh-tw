---
title: 評論、工作流和作業概念 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解"審閱"工具的核心概念;評論、工作流和作業。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221539"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>內容審核審核、工作流和作業

內容管理員將機器輔助審核與"迴圈中人"功能相結合，為實際方案創建最佳的審核過程。 它通過基於雲的[審閱工具](https://contentmoderator.cognitive.microsoft.com)來完成此情況。 在本指南中，您將瞭解"審閱"工具的核心概念：評論、工作流和作業。

## <a name="reviews"></a>評論

在審閱中，內容將上載到"審閱"工具，並顯示在 **"審閱**"選項卡下。從這裡，使用者可以更改應用的標記，並根據需要應用自己的自訂標記。 當使用者提交審核時，結果將發送到指定的回檔終結點，並且內容將從網站中刪除。

![在"審閱"選項卡上，在瀏覽器中打開的工具網站](./Review-Tool-user-Guide/images/image-workflow-review.png)

請參閱[Review 工具指南](./review-tool-user-guide/review-moderated-images.md)以開始創建評論，或查看[REST API 指南](./try-review-api-review.md)，瞭解如何以程式設計方式執行此操作。

## <a name="workflows"></a>工作流程

工作流是內容的基於雲的自訂篩選器。 工作流可以連接到各種服務，以不同的方式篩選內容，然後採取適當的操作。 使用內容檢閱者連接器，工作流可以自動應用審核標記並創建包含已提交內容的評論。

### <a name="view-workflows"></a>查看工作流

要查看現有工作流，請轉到["審閱"工具](https://contentmoderator.cognitive.microsoft.com/)並選擇 **"設置** > **工作流**"。

![預設工作流程](images/default-workflow-listed.PNG)

工作流可以完全描述為 JSON 字串，這使得它們可以通過程式設計方式訪問。 如果為工作流選擇 **"編輯"** 選項，然後選擇 **"JSON"** 選項卡，您將看到一個 JSON 運算式，如下所示：

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

請參閱[Review 工具指南](./review-tool-user-guide/workflows.md)以開始創建和使用工作流，或者請參閱 REST [API 指南](./try-review-api-workflow.md)，瞭解如何以程式設計方式執行此操作。

## <a name="jobs"></a>工作

審核作業充當內容審核、工作流和審閱功能的包裝器。 作業使用內容檢閱者圖像審核 API 或文本審閱 API 掃描內容，然後根據指定的工作流進行檢查。 根據工作流結果，它可能會或可能不會為[審閱工具](./review-tool-user-guide/human-in-the-loop.md)中的內容創建審閱。 雖然可以使用各自的 API 創建和配置審核和工作流，但作業 API 允許您獲取整個過程的詳細報告（可發送到指定的回檔終結點）。

請參閱[REST API 指南](./try-review-api-job.md)以開始使用作業。

## <a name="next-steps"></a>後續步驟

* 試用[作業 API 主控台](try-review-api-job.md)，並使用 REST API 程式碼範例。 如果您已熟悉 Visual Studio 和 C#，也請參閱[作業 .NET 快速入門](moderation-jobs-quickstart-dotnet.md)。 
* 若為檢閱者，請開始使用[檢閱 API 主控台](try-review-api-review.md)，並使用 REST API 程式碼範例。 然後查看[.NET 快速入門](dotnet-sdk-quickstart.md)的評論部分。
* 若為影片檢閱者，請使用[影片檢閱快速入門](video-reviews-quickstart-dotnet.md)，並了解如何[在影片檢閱中新增文字記錄](video-transcript-reviews-quickstart-dotnet.md)。
