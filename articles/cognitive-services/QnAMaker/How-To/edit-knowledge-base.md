---
title: 編輯知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b5ee7f60eab0349378767473c9c80f035a65c9a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221463"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>在 QnA Maker 中編輯知識庫

QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>編輯您的知識庫的內容

1.  在上方導覽列中選取 [我的知識庫]。 

    您可以看到您建立或與您分享的所有服務，這些服務均按照**上次修改**日期的遞減順序排列。

    ![我的知識庫](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. 選取特定的知識庫進行編輯。
 
1. 選取 [Settings] \(設定)。 您可以在這裡編輯必要的欄位：[服務名稱]。
  
    |目標|動作|
    |--|--|
    |新增 URL|您可以藉由按一下 [管理知識庫] -> [+ 新增 URL] 連結來新增 URL，以便將新的常見問題集內容新增至知識庫。|
    |刪除 URL|您可以選取刪除圖示 (即垃圾桶)，以刪除現有 URL。|
    |重新整理內容|如果您想要讓知識庫搜耙現有 URL 的最新內容，請選取 [重新整理] 核取方塊。 這會以最新的 URL 內容一次更新知識庫。 這不會設定週期性更新排程。|
    |新增檔案|您可以藉由選取 [管理知識庫] 和 [+ 新增檔案]，將受支援的檔案文件新增為知識庫的一部分|
    |匯入|您也可以選取 [匯**入知識庫**] 按鈕，匯入任何現有的知識庫。 |
    |更新|知識庫的更新取決於當建立與知識庫相關聯的 QnA Maker 服務時，所使用的**管理定價層**。 如有需要，您也可以從 Azure 入口網站更新管理層。

1. 變更知識庫完成之後，選取頁面右上角的 [儲存並訓練] 以維持變更。    

    ![儲存並訓練](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >如果您在選取 [儲存並訓練] 之前就離開此頁面，所有變更都將遺失。

## <a name="add-a-qna-pair"></a>加入 QnA 組

在 [**編輯**] 頁面上，選取 [**加入 QnA**組]，將新的資料列加入至知識庫資料表。

![加入 QnA 組](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>刪除 QnA 組

若要刪除 QnA，請按一下 QnA 資料列最右側的 [刪除] 圖示。 這是永久性的作業。 該作業無法復原。 請考慮先從 [發佈] 頁面匯出知識庫，再刪除 QnA 組。 

![刪除 QnA 組](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>新增替代問題

將替代問題新增至現有 QnA 組來改善與使用者查詢相符的可能性。

![新增替代問題](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>新增中繼資料

藉由先選取 [**視圖選項**]，然後選取 [**顯示中繼資料**] 來新增中繼資料組。 這會顯示 [中繼資料] 資料行。 接下來，選取 **+** 正負號來新增中繼資料組。 此配對包含一個索引鍵和一個值。

![新增中繼資料](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> 務必在進行編輯後定期儲存和訓練知識庫，以避免遺失變更。

## <a name="manage-large-knowledge-bases"></a>管理大型知識庫

* **資料來源群組**： qna 會依其解壓縮的來來源資料源分組。 您可以展開或摺疊資料來源。

    ![使用 QnA Maker 資料來源列來摺疊和展開資料來源問題和答案](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **搜尋知識庫**：您可以在知識庫資料表頂端的文字方塊中輸入來搜尋知識庫。 按一下輸入來搜尋問題、答案或中繼資料內容。 按一下 X 圖示移除搜尋篩選條件。

    ![使用問題和答案上方的 QnA Maker 搜尋方塊，將檢視縮小為僅限符合篩選條件的項目](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **分頁**：快速地移至資料來源以管理大型知識庫

    ![使用問題和答案上方的 QnA Maker 分頁功能來瀏覽問題和答案頁面](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>刪除知識庫

刪除知識庫 (KB) 是永久性的作業。 該作業無法復原。 刪除知識庫之前，您應該從 QnA Maker 入口網站的 [設定] 頁面將知識庫匯出。 

如果您與[共同作業者](collaborate-knowledge-base.md)共用知識庫然後刪除它，則每個人都會無法存取 KB。 

## <a name="delete-azure-resources"></a>刪除 Azure 資源 

如果您刪除任何用於 QnA Maker 知識庫的 Azure 資源，知識庫將無法再運作。 在刪除任何資源之前，請務必先從 [設定] 頁面匯出知識庫。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在知識庫上共同作業](./collaborate-knowledge-base.md)
