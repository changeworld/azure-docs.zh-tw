---
title: 評論、工作流程和作業概念-內容仲裁
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解審查工具的核心概念;評論、工作流程和工作。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221539"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>內容仲裁審查、工作流程和工作

內容仲裁結合了機器輔助審核與人為迴圈的功能，為真實世界的案例建立最佳審核程式。 它會透過以雲端為基礎的[審查工具](https://contentmoderator.cognitive.microsoft.com)來執行此工作。 在本指南中，您將瞭解審查工具的核心概念：評論、工作流程和作業。

## <a name="reviews"></a>評論

在審查中，內容會上傳至審核工具，並出現在 [**審查**] 索引標籤底下。從這裡開始，使用者可以變更套用的標記，並視需要套用自己的自訂標記。 當使用者提交評論時，會將結果傳送至指定的回呼端點，並將內容從網站中移除。

![在瀏覽器的 [審核] 索引標籤中開啟審查工具網站](./Review-Tool-user-Guide/images/image-workflow-review.png)

若要瞭解如何以程式設計方式執行，請參閱[審查工具指南](./review-tool-user-guide/review-moderated-images.md)以開始建立評論，或參閱[REST API 指南](./try-review-api-review.md)。

## <a name="workflows"></a>工作流程

工作流程是以雲端為基礎的自訂篩選器內容。 工作流程可以連接到各種服務，以不同的方式篩選內容，然後採取適當的動作。 透過內容仲裁連接器，工作流程可以自動套用審核標記，並使用已提交的內容建立評論。

### <a name="view-workflows"></a>查看工作流程

若要查看現有的工作流程，請移至[審核工具](https://contentmoderator.cognitive.microsoft.com/)，然後選取 [**設定**] [ > **工作流程**]。

![預設工作流程](images/default-workflow-listed.PNG)

工作流程可以完整描述為 JSON 字串，讓它們可透過程式設計方式存取。 如果您選取工作流程的 [**編輯**] 選項，然後選取 [ **json** ] 索引標籤，您會看到如下所示的 json 運算式：

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

請參閱[審查工具指南](./review-tool-user-guide/workflows.md)，以開始建立和使用工作流程，或參閱[REST API 指南](./try-review-api-workflow.md)，以瞭解如何以程式設計方式執行此操作。

## <a name="jobs"></a>工作

審核作業可做為內容仲裁、工作流程和評論功能的一種包裝函式。 作業會使用內容仲裁影像審核 API 或文字仲裁 API 來掃描您的內容，然後針對指定的工作流程進行檢查。 根據工作流程結果，它不一定會在[審核工具](./review-tool-user-guide/human-in-the-loop.md)中建立內容的審查。 雖然評論和工作流程都可以使用其各自的 Api 來建立和設定，但作業 API 可讓您取得整個程式的詳細報告（可以傳送至指定的回呼端點）。

請參閱[REST API 指南](./try-review-api-job.md)，以開始使用作業。

## <a name="next-steps"></a>後續步驟

* 試用[作業 API 主控台](try-review-api-job.md)，並使用 REST API 程式碼範例。 如果您已熟悉 Visual Studio 和 C#，也請參閱[作業 .NET 快速入門](moderation-jobs-quickstart-dotnet.md)。 
* 若為檢閱者，請開始使用[檢閱 API 主控台](try-review-api-review.md)，並使用 REST API 程式碼範例。 然後參閱[.net 快速入門](dotnet-sdk-quickstart.md)的 [評論] 區段。
* 若為影片檢閱者，請使用[影片檢閱快速入門](video-reviews-quickstart-dotnet.md)，並了解如何[在影片檢閱中新增文字記錄](video-transcript-reviews-quickstart-dotnet.md)。
