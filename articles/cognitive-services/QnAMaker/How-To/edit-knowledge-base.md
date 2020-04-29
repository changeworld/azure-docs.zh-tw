---
title: 編輯知識庫 - QnA Maker
description: QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756733"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>在您的知識庫中編輯 QnA 配對

QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。

QnA 組會從資料來源（例如檔案或 URL）新增，或加入做為編輯來源。 編輯來源表示已在 QnA 入口網站中手動新增 QnA 組。 所有 QnA 的配對都可供編輯。

## <a name="add-an-editorial-qna-pair"></a>新增編輯 QnA 組
1. 登入[QnA 入口網站](https://www.qnamaker.ai/)，然後選取要在其中新增 QnA 組的知識庫。
1. 在知識庫的 [**編輯**] 頁面上，選取 [新增**QnA**組] 以加入新的 QnA 配對。

1. 在 [新的 QnA 組] 資料列中，新增必要的**問題**和**答案**欄位。 其他欄位則是選擇性的。 所有欄位隨時都可以變更。

1. （選擇性）新增**替代**片語。 替代片語是任何形式的問題，與原始問題明顯不同，但應該提供相同的答案。

    當您的知識庫已發行，而且您已開啟[主動式學習](use-active-learning.md)時，QnA Maker 會收集可供您接受的替代片語選擇。 選取這些選項是為了增加預測精確度。

1. （選擇性）新增**中繼資料**。 若要查看中繼資料，請選取內容功能表中的 [**視圖選項**]。 中繼資料會針對用戶端應用程式（例如聊天機器人）提供的答案提供篩選準則。

1. （選擇性）新增**後續追蹤提示**。 後續提示會提供用戶端應用程式的其他交談路徑，以向使用者呈現。

1. 選取 [**儲存並定型**] 以查看包含新 QnA 組的預測。

## <a name="edit-a-qna-pair"></a>編輯 QnA 配對

不論原始資料來源為何，任何 QnA 組中的任何欄位都可以進行編輯。 某些欄位可能因為您目前的 [**視圖選項**] 設定（在內容工具列中找到）而無法顯示。

## <a name="delete-a-qna-pair"></a>刪除 QnA 組

若要刪除 QnA，請按一下 QnA 資料列最右側的 [刪除]**** 圖示。 這是永久性的作業。 該作業無法復原。 刪除集合之前，請考慮從 [**發行**] 頁面匯出您的知識庫。

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

藉由先選取 [**視圖選項**]，然後選取 [**顯示中繼資料**] 來新增元資料集。 這會顯示 [中繼資料] 資料行。 接下來，選取**+** [符號] 來新增元資料集。 這個集合是由一個索引鍵和一個值所組成。

## <a name="save-changes-to-the-qna-pairs"></a>儲存 QnA 配對的變更

在進行編輯後定期選取 [**儲存並定型**]，以避免遺失變更。

![新增中繼資料](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在知識庫上共同作業](./collaborate-knowledge-base.md)

* [管理 QnA Maker 所使用的 Azure 資源](set-up-qnamaker-service-azure.md)