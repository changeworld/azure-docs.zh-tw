---
title: 編輯知識庫 - QnA Maker
description: QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 52aaaf630806ed6c71101860a1286f88a23ec3fa
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054017"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>在您的知識庫中編輯 QnA 配對

QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。

QnA 組會從資料來源（例如檔案或 URL）新增，或加入做為編輯來源。 編輯來源表示已在 QnA 入口網站中手動新增 QnA 組。 所有 QnA 的配對都可供編輯。

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>新增編輯 QnA 組

1. 登入[QnA 入口網站](https://www.qnamaker.ai/)，然後選取要在其中新增 QnA 組的知識庫。
1. 在知識庫的 [**編輯**] 頁面上，選取 [新增**QnA**組] 以加入新的 QnA 配對。

    > [!div class="mx-imgBorder"]
    > ![加入 QnA 組](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. 在 [新的 QnA 組] 資料列中，新增必要的問題和答案欄位。 其他欄位則是選擇性的。 所有欄位隨時都可以變更。

1. （選擇性）新增**[替代](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** 片語。 替代片語是任何形式的問題，與原始問題明顯不同，但應該提供相同的答案。

    當您的知識庫已發行，而且您已開啟[主動式學習](use-active-learning.md)時，QnA Maker 會收集可供您接受的替代片語選擇。 選取這些選項是為了增加預測精確度。

1. （選擇性）新增**[中繼資料](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)**。 若要查看中繼資料，請選取內容功能表中的 [**視圖選項**]。 中繼資料會針對用戶端應用程式（例如聊天機器人）提供的答案提供篩選準則。

1. （選擇性）新增**[後續追蹤提示](multiturn-conversation.md)**。 後續提示會提供用戶端應用程式的其他交談路徑，以向使用者呈現。

1. 選取 [**儲存並定型**] 以查看包含新 QnA 組的預測。

## <a name="rich-text-editing-for-answer"></a>適用于解答的 Rich text 編輯

您可以透過簡單的工具列，對您的答案文字進行 rtf 編輯，提供 markdown 的樣式。

1. 選取答案的文字區域，rich text 編輯器工具列會顯示在 [QnA 組] 資料列上。

    > [!div class="mx-imgBorder"]
    > ![Rich 文字編輯器的螢幕擷取畫面，其中包含 QnA 組資料列的問題和答案。](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    任何已在答案中的文字都會正確顯示，因為您的使用者會從 bot 看到它。

1. 編輯文字。 從 rtf 編輯工具列選取 [格式化功能]，或使用切換功能來切換到 markdown 語法。

    > [!div class="mx-imgBorder"]
    > ![使用 rtf 編輯器來撰寫和格式化文字，並將它儲存為 markdown。](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |豐富文字編輯器功能|鍵盤快速鍵|
    |--|--|
    |在 rtf 文字編輯器和 markdown 之間切換。 `</>`|CTRL+M|
    |黑. **B**|CTR + LB|
    |斜體，以斜體** _I_表示**|CTRL+I|
    |未排序清單||
    |已排序清單||
    |段落樣式||
    |影像-新增可從公用 URL 取得的映射。|CTRL+G|
    |將連結新增至可公開使用的 URL。|CTRL+K|
    |圖釋-從選取的圖釋新增。|CTRL+E|
    |[高級] 功能表-[復原]|CTRL+Z|
    |[高級] 功能表-重做|CTRL+Y|

1. 使用 rtf 文字工具列中的影像圖示，將影像新增至答案。 就地編輯器需要可公開存取的影像 URL 和影像的替代文字。


    > [!div class="mx-imgBorder"]
    > ![使用 rtf 編輯器新增可公開存取的影像和其替代文字。](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. 新增 URL 的連結，方法是選取答案中的文字，然後選取工具列中的 [連結] 圖示，或選取工具列中的 [連結] 圖示，然後輸入新的文字和 URL。

    > [!div class="mx-imgBorder"]
    > ![使用 rtf 編輯器新增可公開存取的影像和其替代文字。](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>編輯 QnA 配對

不論原始資料來源為何，任何 QnA 組中的任何欄位都可以進行編輯。 某些欄位可能因為您目前的 [**視圖選項**] 設定（在內容工具列中找到）而無法顯示。

## <a name="delete-a-qna-pair"></a>刪除 QnA 組

若要刪除 QnA，請按一下 QnA 資料列最右側的 [刪除]**** 圖示。 這是永久性的作業。 該作業無法復原。 請考慮先從 [發佈]**** 頁面匯出知識庫，再刪除 QnA 組。

![刪除 QnA 組](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>尋找 QnA 配對識別碼

如果您需要找到 QnA 組識別碼，可以在兩個地方找到它：

* 將滑鼠停留在您感興趣之 QnA 組資料列上的 [刪除] 圖示上。 暫留文字包含 QnA 配對識別碼。
* 匯出知識庫。 知識庫中的每個 QnA 組都會包含 QnA 配對識別碼。

## <a name="add-alternate-questions"></a>新增替代問題

將替代問題新增至現有 QnA 組來改善與使用者查詢相符的可能性。

![新增替代問題](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>連結 QnA 配對

[後續的提示](multiturn-conversation.md)會提供連結 QnA 配對。 這是在知識庫層級管理的 QnA 組之間的邏輯連接。 您可以在 QnA Maker 入口網站中編輯後續追蹤提示。

您無法在答案的中繼資料中連結 QnA 配對。

## <a name="add-metadata"></a>新增中繼資料

藉由先選取 [**視圖選項**]，然後選取 [**顯示中繼資料**] 來新增中繼資料組。 這會顯示 [中繼資料] 資料行。 接下來，選取 **+** 正負號來新增中繼資料組。 此配對包含一個索引鍵和一個值。

在 QnA Maker 入口網站的中繼資料快速入門中深入瞭解中繼資料：
* [撰寫-將中繼資料新增至 QnA 配對](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [查詢預測-依中繼資料篩選答案](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>儲存 QnA 配對的變更

在進行編輯後定期選取 [**儲存並定型**]，以避免遺失變更。

![新增中繼資料](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>何時使用 rich text 編輯與 markdown

使用者可以使用 [格式化] 工具列來快速選取和格式化文字，以提供豐富的答案[文字編輯](#add-an-editorial-qna-set)。

當您需要自動產生內容來建立要匯入做為 CI/CD 管線一部分或用於[批次測試](../Quickstarts/batch-testing.md)的知識庫時， [Markdown](../reference-markdown-format.md)是較好的工具。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在知識庫上共同作業](./collaborate-knowledge-base.md)

* [管理 QnA Maker 所使用的 Azure 資源](set-up-qnamaker-service-azure.md)
