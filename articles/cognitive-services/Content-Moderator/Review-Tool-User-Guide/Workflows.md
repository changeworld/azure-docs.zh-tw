---
title: 透過審核工具定義及使用內容工作流程-內容仲裁
titleSuffix: Azure Cognitive Services
description: 您可以使用 Azure 內容仲裁工作流程設計工具，根據您的內容原則來定義自訂工作流程和閾值。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "72754246"
---
# <a name="define-and-use-moderation-workflows"></a>定義和使用仲裁工作流程

在本指南中，您將瞭解如何在[審核工具](https://contentmoderator.cognitive.microsoft.com)網站上設定和使用[工作流程](../review-api.md#workflows)。 工作流程是以雲端為基礎的自訂篩選器，您可以用來更有效率地處理內容。 工作流程可以連接到各種服務，以不同的方式篩選內容，然後採取適當的動作。 本指南說明如何使用內容仲裁連接器（預設包含）來篩選內容，並在一般審核案例中設定人工審核。

## <a name="create-a-new-workflow"></a>建立新的工作流程

前往 [[內容仲裁審查] 工具](https://contentmoderator.cognitive.microsoft.com/)並登入。 在 [設定]**** 索引標籤中，選取 [工作流程]****。

![[工作流程] 設定](images/2-workflows-0.png)

在下一個畫面上，選取 [**新增工作流程**]。

![新增工作流程](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>指派名稱和描述

為您的工作流程命名，輸入描述，然後選擇工作流程是否會處理影像或文字。

![工作流程名稱和描述](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>定義評估準則

在下一個畫面上，移至 [ **If** ] 區段。 在頂端的下拉式功能表中，選擇 [**條件**]。 這可讓您設定工作流程將採取動作的條件。 如果您想要使用多個條件，請改為選擇 [**組合**]。 

接下來，選取連接器。 這個範例會使用 **Content Moderator**。 視您選擇的連接器而定，您將會取得不同的資料輸出選項。 如要瞭解如何設定其他連接器，請參閱審查工具設定指南的[連接器](./configure.md#connectors)一節。

![選取工作流程連接器](images/image-workflow-connect-to.PNG)

選擇要使用的所需輸出，並設定要對其進行檢查的條件。

![定義工作流程的條件](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>定義動作

移至 [ **Then** ] 區段，您可以在其中選取動作。 下列範例會建立影像審核，並指派標記。 （選擇性）您可以新增替代（Else）路徑，並為其設定動作。

![定義工作流程的動作](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>儲存工作流程

請注意工作流程名稱;您需要名稱，才能使用工作流程 API 來啟動審核作業（請參閱下文）。 最後，使用頁面頂端的 [**儲存**] 按鈕來儲存工作流程。

## <a name="test-the-workflow"></a>測試工作流程

現在您已定義自訂工作流程，請使用範例內容進行測試。 移至 [**工作流程**]，然後選取對應的 [**執行工作流程**] 按鈕。

![工作流程測試](images/image-workflow-execute.PNG)

將此[範例影像](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)儲存到本機磁片磁碟機。 然後選取 **[選擇**檔案]，並將影像上傳至工作流程。

![在影像上加上引號的執行器](images/sample-text.jpg)

### <a name="track-progress"></a>追蹤進度

您可以在下一個快顯視窗中，查看工作流程的進度。

![追蹤工作流程的執行](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>確認工作流程動作

移至 [**預覽**] 底下的 [**影像**] 索引標籤，並確認有新建立的影像審核。

![檢閱映像](images/image-workflow-review.PNG)

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何從內容仲裁[審查工具](https://contentmoderator.cognitive.microsoft.com)設定及使用審核工作流程。 接下來，請參閱[REST API 指南](../try-review-api-workflow.md)，以瞭解如何以程式設計方式建立工作流程。
