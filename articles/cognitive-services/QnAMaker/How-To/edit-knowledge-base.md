---
title: 編輯知識庫 - QnA Maker
description: QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 3940f060209ea5ba55957a3673020656d45de9e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776539"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>在知識庫中編輯 QnA 組

QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。

QnA 組是從資料來源（例如檔案或 URL）新增，或新增為編輯來源。 編輯來源表示已在 QnA 入口網站中手動新增 QnA 配對。 所有 QnA 配對都可供編輯。

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>新增編輯 QnA 配對

1. 登入 [QnA 入口網站](https://www.qnamaker.ai/)，然後選取要新增 QnA 配對的知識庫。
1. 在知識庫的 [ **編輯** ] 頁面上，選取 [新增 **QnA** 組] 以加入新的 QnA 配對。

    > [!div class="mx-imgBorder"]
    > ![加入 QnA 組](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. 在 [新的 QnA 組] 資料列中，加入必要的問題和解答欄位。 其他欄位則是選擇性的。 所有欄位都可以隨時變更。

1. （選擇性）新增 **[替代](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** 片語。 替代片語是問題的任何形式，與原始問題截然不同，但應該提供相同的答案。

    當您的知識庫已發行，且您已開啟使用中的 [學習](use-active-learning.md) 時，QnA Maker 會收集您要接受的替代片語選擇。 系統會選取這些選項來提高預測精確度。

1. （選擇性）新增 **[中繼資料](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)**。 若要查看中繼資料，請在內容功能表中選取 [ **視圖選項** ]。 中繼資料會提供用戶端應用程式（例如聊天機器人）所提供答案的篩選器。

1. （選擇性）新增 **[後續提示](multiturn-conversation.md)**。 後續提示會提供用戶端應用程式的其他交談路徑，以呈現給使用者。

1. 選取 [ **儲存並定型** ] 以查看預測，包括新的 QnA 配對。

## <a name="rich-text-editing-for-answer"></a>適用于解答的 Rich text 編輯

您可以從簡單的工具列 markdown 樣式，讓答案文字進行 Rich text 編輯。

1. 選取答案的文字區域，rich text 編輯器工具列會顯示在 QnA 組的資料列上。

    > [!div class="mx-imgBorder"]
    > ![Rich text 編輯器的螢幕擷取畫面，其中包含 QnA 配對資料列的問題和答案。](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    當您的使用者從 bot 看到任何文字時，就會正確地顯示任何已在答案中的文字。

1. 編輯文字。 從 rich text 編輯工具列選取格式化功能，或使用切換功能來切換至 markdown 語法。

    > [!div class="mx-imgBorder"]
    > ![您可以使用 rtf 編輯器來撰寫文字並將其格式化，並以 markdown 的形式儲存。](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Rich text 編輯器功能|鍵盤快速鍵|
    |--|--|
    |在 rich text 編輯器與 markdown 之間切換。 `</>`|CTRL+M|
    |大膽。 **B**|CTR + LB|
    |斜體，以斜體** _I_表示**|CTRL+I|
    |未排序清單||
    |已排序清單||
    |段落樣式||
    |影像-新增可從公用 URL 取得的影像。|CTRL+G|
    |新增公開可用 URL 的連結。|CTRL+K|
    |圖釋-從選取的圖釋新增。|CTRL+E|
    |Advanced 功能表-復原|CTRL+Z|
    |Advanced 功能表-重做|CTRL+Y|

1. 使用 rtf 工具列中的影像圖示，將影像新增至答案。 就地編輯器需要可公開存取的影像 URL 和影像的替代文字。


    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面顯示具有可公開存取影像 URL 的就地編輯器，以及所輸入影像的替代文字。](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. 將連結新增至 URL，方法是選取答案中的文字，然後選取工具列中的連結圖示，或選取工具列中的連結圖示，然後輸入新的文字和 URL。

    > [!div class="mx-imgBorder"]
    > ![使用 rich text 編輯器加入可公開存取的影像及其替代文字。](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>編輯 QnA 配對

任何 QnA 組中的任何欄位都可以編輯，不論原始資料來源為何。 由於您目前的 **視圖選項** 設定（在內容工具列中找到），某些欄位可能無法顯示。

## <a name="delete-a-qna-pair"></a>刪除 QnA 組

若要刪除 QnA，請按一下 QnA 資料列最右側的 [刪除]**** 圖示。 這是永久性的作業。 該作業無法復原。 請考慮先從 [發佈]**** 頁面匯出知識庫，再刪除 QnA 組。

![刪除 QnA 組](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>尋找 QnA 配對識別碼

如果您需要尋找 QnA 組識別碼，可以在兩個地方找到它：

* 將滑鼠停留在您感興趣之 QnA 配對資料列上的刪除圖示上。 停留文字包含 QnA 組識別碼。
* 匯出知識庫。 知識庫中的每個 QnA 配對都包含 QnA 配對識別碼。

## <a name="add-alternate-questions"></a>新增替代問題

將替代問題新增至現有 QnA 組來改善與使用者查詢相符的可能性。

![新增替代問題](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>連結 QnA 組

[後續的提示](multiturn-conversation.md)會提供連結 QnA 配對。 這是在知識庫層級管理的 QnA 組之間的邏輯連線。 您可以在 QnA Maker 入口網站中編輯後續提示。

您無法在答案的中繼資料中連結 QnA 組。

## <a name="add-metadata"></a>新增中繼資料

先選取 [ **視圖選項**]，然後選取 [ **顯示中繼資料**]，以加入中繼資料配對。 這會顯示 [中繼資料] 資料行。 接下來，選取 **+** 要新增中繼資料配對的正負號。 這組組合是由一個索引鍵和一個值所組成。

在 QnA Maker 入口網站快速入門中，深入瞭解中繼資料的中繼資料：
* [製作 - 將中繼資料新增至 QnA 配對](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [查詢預測 - 依中繼資料篩選答案](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>儲存 QnA 配對的變更

進行編輯之後，定期選取 [ **儲存並定型** ] 以避免遺失變更。

![新增中繼資料](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>何時使用 rich text 編輯與 markdown

您可以使用 [格式化] 工具列來快速選取並格式化文字，以方便您使用[文字編輯](#add-an-editorial-qna-set)答案。

當您需要自動產生內容以建立要匯入做為 CI/CD 管線一部分或進行[批次測試](../Quickstarts/batch-testing.md)的知識庫時， [Markdown](../reference-markdown-format.md)是較好的工具。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在知識庫上共同作業](./collaborate-knowledge-base.md)

* [管理 QnA Maker 所使用的 Azure 資源](set-up-qnamaker-service-azure.md)
