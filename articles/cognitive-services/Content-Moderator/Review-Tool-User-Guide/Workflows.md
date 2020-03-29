---
title: 通過"審閱"工具定義和使用內容工作流 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 可以使用 Azure 內容檢閱者工作流設計器根據內容策略定義自訂工作流和閾值。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754246"
---
# <a name="define-and-use-moderation-workflows"></a>定義和使用審核工作流

在本指南中，您將瞭解如何在["審閱"工具](https://contentmoderator.cognitive.microsoft.com)網站上設置和使用[工作流](../review-api.md#workflows)。 工作流是基於雲的自訂篩選器，可用於更高效地處理內容。 工作流可以連接到各種服務，以不同的方式篩選內容，然後採取適當的操作。 本指南演示如何使用內容檢閱者連接器（預設情況下包含）在典型的審核方案中篩選內容和設置人工評論。

## <a name="create-a-new-workflow"></a>創建新工作流

轉到[內容檢閱者審閱工具](https://contentmoderator.cognitive.microsoft.com/)並登錄。 在 [設定]**** 索引標籤中，選取 [工作流程]****。

![[工作流程] 設定](images/2-workflows-0.png)

在下一個螢幕上，選擇 **"添加工作流**"。

![新增工作流程](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>指派名稱和描述

命名工作流，輸入說明，並選擇工作流是處理圖像還是文本。

![工作流程名稱和描述](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>定義評估標準

在下一個螢幕上，轉到 **"If"** 部分。 在頂部下拉式功能表中，選擇 **"條件**"。 這將允許您配置工作流將執行操作的條件。 如果要使用多個條件，請選擇 **"組合**"。 

接下來，選擇連接器。 這個範例會使用 **Content Moderator**。 根據您選擇的連接器，您將獲得不同的資料輸出選項。 請參閱"查看"工具設置指南中的["連接器](./configure.md#connectors)"部分，瞭解如何設置其他連接器。

![選擇工作流連接器](images/image-workflow-connect-to.PNG)

選擇要使用的輸出，並設置要檢查的條件。

![定義工作流程的條件](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>定義操作

轉到"**然後"** 部分，在其中選擇操作。 下面的示例創建圖像審閱並分配標記。 或者，您可以添加備用 （Else） 路徑並為其設置操作。

![定義工作流程的動作](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>保存工作流

請注意工作流名稱;您需要名稱才能使用工作流 API 啟動審核作業（見下文）。 最後，使用頁面頂部的 **"保存**"按鈕保存工作流。

## <a name="test-the-workflow"></a>測試工作流程

現在，您已經定義了自訂工作流，使用示例內容對其進行測試。 轉到**工作流**並選擇相應的**執行工作流**按鈕。

![工作流程測試](images/image-workflow-execute.PNG)

將[此示例圖像](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)保存到本地磁碟機。 然後**選擇"選擇檔"** 並將圖像上載到工作流。

![在圖像上疊加了報價的跑步者](images/sample-text.jpg)

### <a name="track-progress"></a>追蹤進度

您可以在下一個快顯視窗中查看工作流的進度。

![追蹤工作流程的執行](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>驗證工作流操作

轉到 **"審閱**"下的 **"圖像**"選項卡，並驗證是否有新創建的映射審閱。

![檢閱映像](images/image-workflow-review.PNG)

## <a name="next-steps"></a>後續步驟

在本指南中，您從內容審閱人[審閱工具](https://contentmoderator.cognitive.microsoft.com)中學習了如何設置和使用審核工作流。 接下來，請參閱[REST API 指南](../try-review-api-workflow.md)，瞭解如何以程式設計方式創建工作流。
