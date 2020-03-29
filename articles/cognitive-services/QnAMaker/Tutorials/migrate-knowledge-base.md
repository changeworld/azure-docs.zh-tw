---
title: 遷移知識庫 - Qna Maker
description: 遷移知識庫必須先從某個知識庫匯出，再匯入至另一個知識庫。
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258085"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用匯出-匯入移轉知識庫

遷移是從現有知識庫創建新知識庫的過程。 這樣做可能有幾個原因：

* 備份和恢復過程
* CI/CD 管道
* 移動區域

遷移知識庫需要從現有知識庫匯出，然後導入到另一個知識庫。

## <a name="prerequisites"></a>Prerequisites

* 開始之前，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 設定新的 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>從 QnA Maker 遷移知識庫
1. 登入 [QnA Maker 入口網站](https://qnamaker.ai)。
1. 選擇要遷移的原點知識庫。

1. 在 **"設置"** 頁上，選擇 **"匯出知識庫**"以下載包含源知識庫內容的 .tsv 檔 - 問題、答案、中繼資料、後續提示以及從中提取它們的資料來源名稱。

1. 選擇從頂部功能表**創建知識庫**，然後創建_空_知識庫。 它是空的，因為當您創建它時，您不打算添加任何 URL 或檔。 這些在導入步驟期間在創建後添加。

    配置知識庫。 僅設置新的知識庫名稱。 重複的名稱和特殊字元均受支援。

    不要從步驟 4 中選擇任何內容，因為導入檔時這些值將被覆蓋。

1. 在步驟 5 中，選擇 **"創建**"。

1. 在這個新知識庫中，開啟 [設定]**** 索引標籤，然後選取 [匯入知識庫]****。 這將導入問題、答案、中繼資料、後續提示，並保留從中提取這些問題的資料來源名稱。

   > [!div class="mx-imgBorder"]
   > [![匯入知識庫](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 使用 [測試] 面板來 [測試]**** 新知識庫。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。

1. **發佈**知識庫並創建聊天機器人。 了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>以程式設計方式從 QnA 製造商遷移知識庫

遷移過程使用以下 REST API 以程式設計方式可用：

**匯出**

* [下載知識庫 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

[匯入]****

* [替換 API（使用相同的知識庫 ID 重新載入）](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [創建 API（使用新的知識庫 ID 載入）](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>聊天記錄和變動
不會自動匯入不區分大小寫的變異形式 (同義字)。 使用[V4 API](https://go.microsoft.com/fwlink/?linkid=2092179)在新知識庫中移動更改。

沒有任何方法可移轉聊天記錄，因為新的知識庫會使用 Application Insights 來儲存聊天記錄。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [編輯知識庫](../How-To/edit-knowledge-base.md)
