---
title: 透過審核工具定義和使用內容工作流程-內容仲裁
titleSuffix: Azure Cognitive Services
description: 您可以使用 Azure 內容仲裁工作流程設計工具來定義自訂工作流程和閾值（以您的內容原則為基礎）。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "72754246"
---
# <a name="define-and-use-moderation-workflows"></a>定義和使用仲裁工作流程

在本指南中，您將瞭解如何在[審核工具](https://contentmoderator.cognitive.microsoft.com)網站上設定及使用[工作流程](../review-api.md#workflows)。 工作流程是以雲端為基礎的自訂篩選準則，可讓您更有效率地處理內容。 工作流程可以連接到各種不同的服務，以不同的方式篩選內容，然後採取適當的動作。 本指南說明如何使用預設包含的內容仲裁連接器 () 在一般仲裁案例中篩選內容並設定人工審核。

## <a name="create-a-new-workflow"></a>建立新的工作流程

移至 [內容仲裁審核工具](https://contentmoderator.cognitive.microsoft.com/) 並登入。 在 [設定]**** 索引標籤中，選取 [工作流程]****。

![[工作流程] 設定](images/2-workflows-0.png)

在下一個畫面中，選取 [ **新增工作流程**]。

![新增工作流程](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>指派名稱和描述

命名您的工作流程、輸入描述，然後選擇工作流程是否要處理影像或文字。

![工作流程名稱和描述](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>定義評估準則

在下一個畫面上，移至 **If** 區段。 在頂端的下拉式功能表中，選擇 [ **條件**]。 這可讓您設定工作流程將採取動作的條件。 如果您想要使用多個條件，請改為選擇 **組合** 。 

接下來，選取連接器。 這個範例會使用 **Content Moderator**。 根據您選擇的連接器，您將會取得不同的資料輸出選項。 請參閱審核工具設定指南的 [連接器](./configure.md#connectors) 一節，以瞭解如何設定其他連接器。

![選取工作流程連接器](images/image-workflow-connect-to.PNG)

選擇要使用的所需輸出，並設定要對其進行檢查的條件。

![定義工作流程的條件](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>定義動作

移至 [ **Then** ] 區段，您可以在其中選取動作。 下列範例會建立影像審核並指派標記。 （選擇性）您也可以將替代 (其他) 路徑，並為其設定動作。

![定義工作流程的動作](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>儲存工作流程

請記下工作流程名稱;您需要使用工作流程 API 來啟動仲裁工作的名稱 (請參閱以下) 。 最後，使用頁面頂端的 [ **儲存** ] 按鈕來儲存工作流程。

## <a name="test-the-workflow"></a>測試工作流程

現在您已定義自訂工作流程，請使用範例內容進行測試。 移至 [ **工作流程** ]，然後選取對應的 [ **執行工作流程** ] 按鈕。

![工作流程測試](images/image-workflow-execute.PNG)

將此 [範例映射](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) 儲存到您的本機磁片磁碟機。 然後選取 **[選擇檔案 (s) ** ，然後將影像上傳至工作流程。

![在影像上重迭引號的執行器](images/sample-text.jpg)

### <a name="track-progress"></a>追蹤進度

您可以在下一個快顯視窗中查看工作流程的進度。

![追蹤工作流程的執行](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>確認工作流程動作

移至 [正在**審核**] 下的 [**映射**] 索引標籤，並確認有新建立的影像審核。

![檢閱映像](images/image-workflow-review.PNG)

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何設定和使用內容仲裁 [審核工具](https://contentmoderator.cognitive.microsoft.com)中的審核工作流程。 接下來，請參閱 [REST API 指南](../try-review-api-workflow.md) ，以瞭解如何以程式設計方式建立工作流程。
