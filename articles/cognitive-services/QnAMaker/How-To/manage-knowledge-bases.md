---
title: 管理知識庫 - QnA 製造商
description: QnA Maker 允許您通過提供對知識庫設置和內容的訪問來管理知識庫。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 57839125011016daed5f0b3d441a83e8db488198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071233"
---
# <a name="create-knowledge-base-and-manage-settings"></a>創建知識庫和管理設置

QnA Maker 允許您通過提供對知識庫設置和資料來源的訪問來管理知識庫。

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * 如果沒有 Azure 訂閱，請先[創建一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"
> * 在 Azure 門戶中創建的[QnA Maker 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 請記住您在建立資源時選取的 Azure Active Directory 識別碼、訂用帳戶、QnA 資源名稱。

## <a name="create-a-knowledge-base"></a>建立知識庫

1. 利用您的 Azure 認證登入 [QnAMaker.ai](https://QnAMaker.ai) 入口網站。

1. 在 QnA Maker 入口網站中，選取 [建立知識庫]****。

1. 如果您已經有 QnA Maker 資源，請在 [建立]**** 頁面上略過 [步驟 1]****。

    如果您尚未建立資源，則請選取 [建立 QnA 服務]****。 系統會將您導向 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以在您的訂用帳戶中設定 QnA Maker 服務。 請記住您在建立資源時選取的 Azure Active Directory 識別碼、訂用帳戶、QnA 資源名稱。

    當您在 Azure 入口網站中建立好資源時，請回到 QnA Maker 入口網站、重新整理瀏覽器頁面，然後繼續 [步驟 2]****。

1. 在 [步驟 3]**** 中，選取您的 Active directory、訂用帳戶、服務 (資源)，以及在服務中所建立所有知識庫的語言。

   ![選取 QnA Maker 服務知識庫的螢幕擷取畫面](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 在**步驟 3**中，命名`My Sample QnA KB`您的知識庫。

1. 在 [步驟 4]**** 中，使用下表進行設定：

    |設定|值|
    |--|--|
    |**啟用從 URL、.pdf 或 .docx 檔案進行多回合擷取。**|已檢查|
    |**預設答案文字**| `Quickstart - default answer not found.`|
    |**+ 新增 URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**閒聊**|選取 [Professional]****|

1. 在 [步驟 5]**** 中，選取 [建立知識庫]****。

    擷取程序需要一點時間來讀取文件並找出問題和回答。

    在 QnA Maker 成功建立知識庫之後，[知識庫]**** 頁面隨即開啟。 您可以在此頁面上編輯知識庫的內容。

## <a name="edit-knowledge-base"></a>編輯知識庫

1.  在上方導覽列中選取 [我的知識庫]****。

       您可以看到您建立或與您分享的所有服務，這些服務均按照**上次修改**日期的遞減順序排列。

       ![我的知識庫](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. 選取特定的知識庫進行編輯。

1.  選取 [設定]****。 下面的清單包含您可以更改的欄位。

       |目標|動作|
       |--|--|
       |新增 URL|您可以藉由按一下 [管理知識庫] -> [+ 新增 URL]**** 連結來新增 URL，以便將新的常見問題集內容新增至知識庫。|
       |刪除 URL|您可以選取刪除圖示 (即垃圾桶)，以刪除現有 URL。|
       |刷新內容|如果您想要讓知識庫搜耙現有 URL 的最新內容，請選取 [重新整理]**** 核取方塊。 這將使用最新的 URL 內容更新一次知識庫。 這不是設置定期更新計畫。|
       |新增檔案|您可以藉由選取 [管理知識庫]**** 和 [+ 新增檔案]****，將受支援的檔案文件新增為知識庫的一部分|
    |匯入|您還可以通過選擇 **"導入知識庫"** 按鈕導入任何現有知識庫。 |
    |更新|知識庫的更新取決於當建立與知識庫相關聯的 QnA Maker 服務時，所使用的**管理定價層**。 如有必要，還可以從 Azure 門戶更新管理層。

  1. 完成對知識庫的更改後，選擇頁面右上角的 **"保存"並訓練**，以便保留更改。

       ![儲存並訓練](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >如果您在選取 [儲存並訓練]**** 之前就離開此頁面，所有變更都將遺失。



## <a name="manage-large-knowledge-bases"></a>管理大型知識庫

* **資料來源組**：QnA 按從中提取它們的資料來源分組。 您可以展開或摺疊資料來源。

    ![使用 QnA Maker 資料來源列來摺疊和展開資料來源問題和答案](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **搜索知識庫**：您可以通過在知識庫表頂部的文字方塊中鍵入來搜索知識庫。 按一下輸入來搜尋問題、答案或中繼資料內容。 按一下 X 圖示移除搜尋篩選條件。

    ![使用問題和答案上方的 QnA Maker 搜尋方塊，將檢視縮小為僅限符合篩選條件的項目](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **分頁**：快速流覽資料來源以管理大型知識庫

    ![使用問題和答案上方的 QnA Maker 分頁功能來瀏覽問題和答案頁面](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>刪除知識庫

刪除知識庫 (KB) 是永久性的作業。 該作業無法復原。 刪除知識庫之前，您應該從 QnA Maker 入口網站的 [設定]**** 頁面將知識庫匯出。

如果您與協作者共用知識庫，*（協作-知識-base.md），然後將其刪除，則每個人都無法訪問知識庫。

## <a name="next-steps"></a>後續步驟

瞭解如何管理資源中所有知識庫[的語言](language-knowledge-base.md)。

* 編輯 QnA 集
* 管理 QnA 製造商使用的 Azure 資源