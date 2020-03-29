---
title: 學習複習工具概念 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 瞭解內容審閱人審閱工具，這是一個協調聯合 AI 和人工審核審核工作的網站。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169512"
---
# <a name="content-moderator-review-tool"></a>內容檢閱者審閱工具

Azure 內容審閱人提供的服務是將機器學習內容審核與人工審閱相結合，[而"審閱"工具](https://contentmoderator.cognitive.microsoft.com)網站是方便使用的前端，提供對這些服務的詳細訪問。

![瀏覽器中的審閱工具儀表板](./images/0-dashboard.png)

## <a name="what-it-does"></a>作用

與機器輔助的審核 API 結合使用時，"[審閱"工具](https://contentmoderator.cognitive.microsoft.com)允許您在內容審核過程中完成以下任務：

- 使用一組工具來調節多種格式（文本、圖像和視頻）的內容。
- 當審核 API 結果出來時，自動創建人工[評審](../review-api.md#reviews)。
- 將內容評審分配或上報給多個審核團隊，按內容類別別或體驗級別組織。
- 使用預設或自訂邏輯篩選器 （[工作流](../review-api.md#workflows)） 對內容進行排序和跟蹤，而無需編寫任何代碼。
- 使用[連接器](./configure.md#connectors)處理內容與 Microsoft PhotoDNA、文本分析和人臉服務以及內容審閱人 API。
- 構建您自己的連接器，為任何 API 或業務流程創建工作流。
- 取得您內容審核程序的關鍵效能計量。

## <a name="review-tool-dashboard"></a>查看工具儀表板

在 **"儀表板"** 選項卡上，您可以看到工具中完成的內容評審的關鍵指標。 查看圖像、文本和視頻內容的總、完整和掛起評論數。 您還可以查看已完成審核的使用者和團隊以及已應用的審核標記的細分。

![檢視儀表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>查看工具憑據

使用["審閱"工具](https://contentmoderator.cognitive.microsoft.com)註冊時，系統將提示您為帳戶選擇 Azure 區域。 這是因為["審閱"工具](https://contentmoderator.cognitive.microsoft.com)為 Azure 內容檢閱者服務生成免費試用金鑰;因此，審核工具為 Azure 內容檢閱者服務生成免費試用金鑰。您需要此金鑰才能從 REST 調用或用戶端 SDK 訪問任何服務。 您可以通過選擇 **"設置** > **憑據**"來查看金鑰和 API 終結點 URL。

![Content Moderator 認證](images/settings-6-credentials.png)

## <a name="next-steps"></a>後續步驟

請參閱[配置審閱工具](./configure.md)以瞭解如何訪問審閱工具資源和更改設置。