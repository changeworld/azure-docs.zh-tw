---
title: 遷移知識庫 - Qna Maker
description: 遷移知識庫必須先從某個知識庫匯出，再匯入至另一個知識庫。
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 4148431ebb4de288ac517c7c006a925e36c5f460
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542916"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用匯出-匯入移轉知識庫

遷移是從現有的知識庫建立新知識庫的流程。 有幾個原因可以達到此目的：

* 備份與還原流程
* CI/CD 管線
* 移動區域

遷移知識庫需要從現有的知識庫匯出，然後再匯入到另一個知識庫。

## <a name="prerequisites"></a>必要條件

* 開始之前，請建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。
* 設定新的 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>從 QnA Maker 遷移知識庫
1. 登入 [QnA Maker 入口網站](https://qnamaker.ai)。
1. 選取您要遷移的原始知識庫。

1. 在 [ **設定** ] 頁面上，選取 [ **匯出知識庫** ] 以下載包含原始知識庫內容的 tsv 檔案-問題、答案、中繼資料、後續提示，以及從中解壓縮的資料來源名稱。

1. 從頂端功能表選取 [ **建立知識庫** ]，然後建立 _空白_ 的知識庫。 它是空的，因為當您建立它時，不會新增任何 Url 或檔案。 這些是在匯入步驟期間于建立後新增的。

    設定知識庫。 只設定新的知識庫名稱。 重複的名稱和特殊字元均受支援。

    請勿選取步驟4中的任何資料，因為當您匯入檔案時，將會覆寫這些值。

1. 在步驟5中，選取 [ **建立**]。

1. 在這個新知識庫中，開啟 [設定]**** 索引標籤，然後選取 [匯入知識庫]****。 這會匯入問題、答案、中繼資料、後續提示，並保留從中解壓縮的資料來源名稱。

   > [!div class="mx-imgBorder"]
   > [![匯入知識庫](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 使用 [測試] 面板來 [測試]**** 新知識庫。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。

1. **發佈** 知識庫並建立聊天機器人。 了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>以程式設計方式從 QnA Maker 遷移知識庫

您可以使用下列 REST Api，以程式設計方式提供遷移程式：

**匯出**

* [下載知識庫 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**匯入**

* [使用相同的知識庫識別碼取代 (重載 API) ](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [使用新的知識庫識別碼建立 API (載入) ](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>聊天記錄和變動
不會自動匯入不區分大小寫的變異形式 (同義字)。 使用 [V4 api](https://go.microsoft.com/fwlink/?linkid=2092179) 來移動新知識庫中的變更。

沒有任何方法可移轉聊天記錄，因為新的知識庫會使用 Application Insights 來儲存聊天記錄。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [編輯知識庫](../How-To/edit-knowledge-base.md)
