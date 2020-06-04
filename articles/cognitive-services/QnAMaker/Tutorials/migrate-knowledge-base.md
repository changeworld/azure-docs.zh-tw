---
title: 遷移知識庫 - Qna Maker
description: 遷移知識庫必須先從某個知識庫匯出，再匯入至另一個知識庫。
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: e2d6c1795b816d3b7ac046ea4055402b9107e20b
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343083"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用匯出-匯入移轉知識庫

「遷移」（process）是從現有的知識庫建立新知識庫的程式。 基於下列幾個原因，您可能會這麼做：

* 備份與還原程式
* CI/CD 管線
* 移動區域

若要遷移知識庫，您必須從現有的知識庫匯出，然後再匯入到另一個知識庫。

## <a name="prerequisites"></a>必要條件

* 開始之前，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 設定新的 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>從 QnA Maker 遷移知識庫
1. 登入[QnA Maker 入口網站](https://qnamaker.ai)。
1. 選取您想要遷移的來源知識庫。

1. 在 [**設定**] 頁面上，選取 [**匯出知識庫**] 以下載 tsv 檔案，其中包含您的來源知識庫內容-問題、解答、中繼資料、後續提示，以及從中解壓縮它們的資料來源名稱。

1. 從頂端功能表中選取 [**建立知識庫**]，然後建立_空白_的知識庫。 它是空的，因為當您建立它時，不會新增任何 Url 或檔案。 這些會在建立後於匯入步驟期間新增。

    設定知識庫。 僅設定新的知識庫名稱。 重複的名稱和特殊字元均受支援。

    請勿選取步驟4中的任何專案，因為當您匯入檔案時，將會覆寫這些值。

1. 在步驟5中，選取 [**建立**]。

1. 在這個新知識庫中，開啟 [設定]**** 索引標籤，然後選取 [匯入知識庫]****。 這會匯入問題、解答、中繼資料、後續提示，並保留從中解壓縮的資料來源名稱。

   > [!div class="mx-imgBorder"]
   > [![匯入知識庫](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 使用 [測試] 面板來 [測試]**** 新知識庫。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。

1. **發佈**知識庫並建立聊天機器人。 了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>以程式設計方式從 QnA Maker 遷移知識庫

您可以使用下列 REST Api，以程式設計方式取得遷移程式：

**匯出**

* [下載知識庫 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

[匯入]****

* [取代 API （以相同的知識庫識別碼重載）](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [建立 API （使用新的知識庫識別碼載入）](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>聊天記錄和變動
不會自動匯入不區分大小寫的變異形式 (同義字)。 您可以使用[V4 api](https://go.microsoft.com/fwlink/?linkid=2092179) ，在新的知識庫中移動變更。

沒有任何方法可移轉聊天記錄，因為新的知識庫會使用 Application Insights 來儲存聊天記錄。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [編輯知識庫](../How-To/edit-knowledge-base.md)
