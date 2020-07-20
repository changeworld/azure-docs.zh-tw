---
title: 學習審查工具概念-內容仲裁
titleSuffix: Azure Cognitive Services
description: 瞭解內容仲裁審查工具，此網站會協調合併的 AI 和人工審核審核工作。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "76169512"
---
# <a name="content-moderator-review-tool"></a>內容仲裁審查工具

Azure 內容仲裁提供的服務可結合機器學習內容仲裁與人工審核，而[審核工具](https://contentmoderator.cognitive.microsoft.com)網站是方便使用的前端，可提供這些服務的詳細存取權。

![瀏覽器中的審核工具儀表板](./images/0-dashboard.png)

## <a name="what-it-does"></a>作用

[審核工具](https://contentmoderator.cognitive.microsoft.com)搭配電腦輔助審核 api 使用時，可讓您在內容仲裁程式中完成下列工作：

- 使用一組工具來以多種格式（文字、影像和影片）來審核內容。
- 在仲裁 API 結果出現時，自動建立人工[審核](../review-api.md#reviews)。
- 將內容審查指派或呈報給多個審查小組，並依內容類別別或經驗層級組織。
- 使用預設或自訂邏輯篩選（[工作流程](../review-api.md#workflows)）來排序和追蹤內容，而不需要撰寫任何程式碼。
- 除了內容仲裁 Api 之外，您還可以使用[連接器](./configure.md#connectors)來處理 Microsoft PhotoDNA、文字分析和臉部服務的內容。
- 建立您自己的連接器，為任何 API 或商務程式建立工作流程。
- 取得您內容審核程序的關鍵效能計量。

## <a name="review-tool-dashboard"></a>審查工具儀表板

在 [**儀表板**] 索引標籤上，您可以看到在工具中完成之內容審查的重要計量。 查看影像、文字和影片內容的總計、完整和暫止的評論數目。 您也可以查看已完成評論的使用者和小組的細目，以及已套用的審核標記。

![檢視儀表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>審查工具認證

當您註冊[審查工具](https://contentmoderator.cognitive.microsoft.com)時，系統會提示您為您的帳戶選取 Azure 區域。 這是因為[審查工具](https://contentmoderator.cognitive.microsoft.com)會為 Azure 內容仲裁服務產生免費試用金鑰;您將需要此金鑰，才能從 REST 呼叫或用戶端 SDK 存取任何服務。 您可以藉由選取 [**設定** > ] [**認證**] 來查看您的金鑰和 API 端點 URL。

![Content Moderator 認證](images/settings-6-credentials.png)

## <a name="next-steps"></a>後續步驟

請參閱[設定審核工具](./configure.md)，以瞭解如何存取審核工具資源和變更設定。