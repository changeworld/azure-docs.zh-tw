---
title: 評論、工作流程和作業概念-內容仲裁
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解審核工具的核心概念;評論、工作流程和工作。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 69a66c49d64ab9ba589050e1f20399f7f193b76e
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912951"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>內容仲裁審核、工作流程和作業

內容仲裁結合了機器輔助仲裁與人為迴圈的功能，為真實世界的案例建立最佳仲裁流程。 它是透過以雲端為基礎的 [審核工具](https://contentmoderator.cognitive.microsoft.com)來完成。 在本指南中，您將瞭解審核工具的核心概念：審核、工作流程和工作。

## <a name="reviews"></a>評論

在評論中，會將內容上傳到審核工具，並顯示在 [ **審核** ] 索引標籤底下。從這裡開始，使用者可以修改套用的標籤，並視需要套用自己的自訂標記。 當使用者提交評論時，會將結果傳送至指定的回呼端點，並從網站移除內容。

![在瀏覽器的 [審核] 索引標籤上開啟審核工具網站](./Review-Tool-user-Guide/images/image-workflow-review.png)

請參閱 [審核工具指南](./review-tool-user-guide/review-moderated-images.md) 以開始建立評論，或參閱 [REST API 指南](./try-review-api-review.md) ，以瞭解如何以程式設計方式進行。

## <a name="workflows"></a>工作流程

工作流程是以雲端為基礎的自訂篩選內容。 工作流程可以連接到各種不同的服務，以不同的方式篩選內容，然後採取適當的動作。 使用「內容仲裁」連接器，工作流程可以自動套用仲裁標記，並使用提交的內容建立評論。

### <a name="view-workflows"></a>查看工作流程

若要查看您現有的工作流程，請移至 [審核工具](https://contentmoderator.cognitive.microsoft.com/)，然後選取 [ **設定**  >  **工作流程** ]。

![預設工作流程](images/default-workflow-listed.PNG)

工作流程可完整描述為 JSON 字串，使其可透過程式設計方式存取。 如果您針對工作流程選取 [ **編輯** ] 選項，然後選取 [ **json** ] 索引標籤，您會看到如下所示的 json 運算式：

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

若要瞭解如何以程式設計方式進行，請參閱「 [審核工具指南](./review-tool-user-guide/workflows.md) 」以開始建立和使用工作流程，或參閱 [REST API 指南](./try-review-api-workflow.md) 。

## <a name="jobs"></a>工作

仲裁作業可作為內容仲裁、工作流程和評論功能的一種包裝函式。 作業會使用內容仲裁影像仲裁 API 或文字仲裁 API 來掃描您的內容，然後針對指定的工作流程進行檢查。 根據工作流程的結果，它不一定會在 [審核工具](./review-tool-user-guide/human-in-the-loop.md)中建立內容的評論。 雖然您可以使用各自的 Api 來建立和設定評論和工作流程，但作業 API 可讓您取得整個程式的詳細報表， (可將其傳送至指定的回呼端點) 。

若要開始使用作業，請參閱 [REST API 指南](./try-review-api-job.md) 。

## <a name="next-steps"></a>下一步

* 試用[作業 API 主控台](try-review-api-job.md)，並使用 REST API 程式碼範例。 如果您已熟悉 Visual Studio 和 C#，也請參閱[作業 .NET 快速入門](moderation-jobs-quickstart-dotnet.md)。 
* 若為檢閱者，請開始使用[檢閱 API 主控台](try-review-api-review.md)，並使用 REST API 程式碼範例。 然後查看 [.net 快速入門](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)的 [評論] 區段。
* 若為影片檢閱者，請使用[影片檢閱快速入門](video-reviews-quickstart-dotnet.md)，並了解如何[在影片檢閱中新增文字記錄](video-transcript-reviews-quickstart-dotnet.md)。