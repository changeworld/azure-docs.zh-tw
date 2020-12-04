---
title: 在 QnA Maker 入口網站中新增問題和答案
description: 本文說明如何利用中繼資料新增成對的問與答，讓使用者可以找到其問題的正確答案。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 53c0afa1cdb8c9920875b7ba694339107714bd54
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462207"
---
# <a name="add-questions-and-answer-with-qna-maker-portal"></a>使用 QnA Maker 入口網站新增問題和答案

建立知識庫之後，請使用中繼資料新增成對的問與答 (QnA)，以篩選出答案。 下表中的問題與 Azure 服務限制有關，但每個問題都與不同的 Azure 搜尋服務有關。

<a name="qna-table"></a>

|成對|問題|Answer|中繼資料|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

將中繼資料新增至 QnA 配對之後，用戶端應用程式可以：

* 要求僅符合特定中繼資料的答案。
* 接收所有答案，但根據每個答案的中繼資料來對答案進行後置處理。


## <a name="prerequisites"></a>必要條件

* 完成[先前的快速入門](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>登入 QnA Maker 入口網站

1. 登入 [QnA Maker 入口網站](https://www.qnamaker.ai)。

1. 選取來自[先前快速入門](./create-publish-knowledge-base.md)的現有知識庫。

## <a name="add-additional-alternatively-phrased-questions"></a>新增其他採用不同說法的問題

目前的知識庫具有 QnA Maker 疑難排解 QnA 配對。 在建立程序期間，將 URL 新增至知識庫時，便已建立了這些成對的問與答。

匯入此 URL 時，只會建立一個有單一答案的問題。 在此程序中，請新增其他問題。

1. 在 [編輯] 頁面上，使用成對問與答上方的搜尋文字方塊，尋找問題 `How large a knowledge base can I create?`

1. 在 [問題] 資料行中，選取 [+ 新增不同說法]，然後新增下表所提供的每個新說法。

    |不同說法|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. 選取 **儲存並訓練** 來重新訓練知識庫。

1. 選取 [測試]，然後輸入接近其中一個新的不同說法，但用語不是完全相同的問題：

    `What GB size can a knowledge base be?`

    正確答案會以 Markdown 格式傳回：

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`

    如果您在傳回的答案下選取 [檢查]，則可以看到更多符合問題，但信賴度不同的答案。

    請勿新增每個可能的不同說法組合。 當您開啟 QnA Maker 的[主動式學習](../how-to/improve-knowledge-base.md)時，這會尋找最能讓知識庫符合使用者需求的不同說法。

1. 再次選取 [測試] 來關閉測試視窗。

## <a name="add-metadata-to-filter-the-answers"></a>新增中繼資料以篩選答案

將中繼資料新增至成對的問與答，可讓用戶端應用程式要求經過篩選的答案。 系統會先套用此篩選條件，再套用[第一個和第二個順位排定程式](../concepts/query-knowledge-base.md#ranker-process)。

1. 從[此快速入門的第一個表格](#qna-table)中，新增第二個成對的問與答，但不包含中繼資料，然後繼續進行下列步驟。

1. 選取 [檢視選項]，然後選取 [顯示中繼資料]。

1. 針對您剛才新增的 QnA 配對，選取 [新增中繼資料標籤]，然後新增 `service` 的名稱和 `search` 的值。 其看起來像這樣：`service:search`。

1. 使用 `link_in_answer` 的名稱和 `false` 的值新增其他中繼資料標籤。 其看起來像這樣：`link_in_answer:false`。

1. 在資料表中搜尋第一個答案：`How large a knowledge base can I create?`。

1. 為相同的兩個中繼資料標籤新增中繼資料組合：

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    現在，您有兩個具有相同中繼資料標籤，但值不同的問題。

1. 選取 **儲存並訓練** 來重新訓練知識庫。

1. 選取頂端功能表中的 [發佈]，以移至 [發佈] 頁面。
1. 選取 [發佈] 按鈕，將目前的知識庫發佈至端點。
1. 知識庫發佈完成後，請繼續進行下一個快速入門，以了解如何從知識庫產生答案。

## <a name="what-did-you-accomplish"></a>您完成了哪些工作？

您已編輯知識庫以支援更多問題，並提供名稱/值組以支援在搜尋期間於一或多個答案傳回後，篩選出最佳答案或進行後續處理。

## <a name="clean-up-resources"></a>清除資源

如果您不想繼續進行下一個快速入門，請刪除 Azure 入口網站中的 QnA Maker 和 Bot Framework 資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Postman 或 cURL 來獲得答案](get-answer-from-knowledge-base-using-url-tool.md)
