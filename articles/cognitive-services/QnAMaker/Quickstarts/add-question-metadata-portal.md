---
title: 快速入門：在 QnA Maker 入口網站中新增問題和答案
titleSuffix: Azure Cognitive Services
description: 本快速入門說明如何新增問答集與中繼資料，讓使用者可以找到其問題的正確答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 664d6006ab78f91a8ed0e199cf78fae9512efd73
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843034"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>快速入門：使用 QnA Maker 入口網站新增問題和答案

建立知識庫之後，請新增問答集與中繼資料，讓使用者可以找到其問題的正確答案。

正確答案只會有一個，但客戶可能會用許多方式來詢問問題，而這些問題全都指向某個答案。

例如，下表中的問題與 Azure 服務限制有關，但每個問題都與不同的 Azure 服務有關。

<a name="qna-table"></a>


|設定|問題|Answer|中繼資料|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

將中繼資料新增至問答集之後，用戶端應用程式可以：

* 要求僅符合特定中繼資料的答案。
* 接收所有答案，但根據每個答案的中繼資料來對答案進行後置處理。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* QnA Maker 服務
* 已在該 QnA Maker 服務中建立知識庫

上述兩者都已在[第一個快速入門](../how-to/create-knowledge-base.md)中完成建立。

## <a name="sign-in-to-the-qna-maker-portal"></a>登入 QnA Maker 入口網站

1. 登入 [QnA Maker 入口網站](https://www.qnamaker.ai)。

1. 選取現有知識庫。 如果您沒有知識庫，請回到[先前的快速入門](../how-to/create-knowledge-base.md)，並完成建立知識庫的步驟。

## <a name="add-additional-alternatively-phrased-questions"></a>新增其他採用不同說法的問題

目前的知識庫來自[先前的快速入門](../how-to/create-knowledge-base.md)，具有 QnA Maker 疑難排解問答集。 在建立程序進行期間將 URL 新增至知識庫時，便已建立這些問答集。

匯入此 URL 時，只會建立一個有單一答案的問題。

在此程序中，請新增其他問題。

1. 在 [編輯]  頁面上，使用問答集上方的搜尋文字方塊來尋找問題 `How large a knowledge base can I create?`

1. 在 [問題]  資料行中，選取 [+ 新增不同說法]  ，然後新增下表所提供的每個新說法。

    |不同說法|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. 選取 **儲存並訓練** 來重新訓練知識庫。

1. 選取 [測試]  ，然後輸入接近其中一個新的不同說法，但用語不是完全相同的問題：

    `What GB size can a knowledge base be?`

    正確答案會以 Markdown 格式傳回：`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    如果您在傳回的答案下選取 [檢查]  ，則可以看到更多符合問題，但信賴度不同的答案。

    請勿新增每個可能的不同說法組合。 開啟 QnA Maker 的[主動式學習](../how-to/improve-knowledge-base.md)，這會尋找最能讓知識庫符合使用者需求的不同說法。

1. 再次選取 [測試]  來關閉測試視窗。

## <a name="add-metadata-to-filter-the-answers"></a>新增中繼資料以篩選答案

將中繼資料新增至問答集可讓用戶端應用程式要求經過篩選的答案。 系統會先套用此篩選條件，再套用[第一個和第二個順位排定程式](../concepts/query-knowledge-base.md#ranker-process)。

1. 從[此快速入門的第一個表格](#qna-table)中新增第二個問答集，但不要包含中繼資料，然後繼續進行下列步驟。

1. 選取 [檢視選項]  ，然後選取 [顯示中繼資料]  。

1. 針對您剛剛新增的問答集，選取 [新增中繼資料標籤]  ，然後新增 `service` 的名稱和 `search` 的值，也就是 `service:search`。

1. 使用 `link_in_answer` 的名稱和 `false` 的值新增其他中繼資料標籤，也就是 `link_in_answer:false`。

1. 在資料表中搜尋第一個答案：`How large a knowledge base can I create?`。
1. 為相同的兩個中繼資料標籤新增中繼資料組合：

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    現在，您有兩個具有相同中繼資料標籤，但值不同的問題。

1. 選取 **儲存並訓練** 來重新訓練知識庫。

1. 選取頂端功能表中的 [發佈]  ，以移至 [發佈] 頁面。
1. 選取 [發佈]  按鈕，將目前的知識庫發佈至可查詢的端點。
1. 發佈知識庫之後，選取 [Curl]  索引標籤來查看可用來從知識庫產生答案的 cURL 命令範例。
1. 將命令複製到記事本或其他可編輯的環境，以便編輯命令。 針對您自己的資源名稱、知識庫識別碼和端點金鑰進行編輯：

    |Replace|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    請注意，問題只有一個字 `size`，這可以傳回任一問答集。 `strictFilters` 陣列會辨別回應，而將回應減少到只剩 `qna_maker` 答案。

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. 回應只會包含符合篩選準則的答案。

    下列 cURL 回應已經過格式化，以方便您閱讀：

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    如果有不符合搜尋詞彙但符合篩選條件的問答集，則不會傳回。 相反地，會傳回一般性答案 `No good match found in KB.`。

    請務必讓中繼資料的名稱和值組合保持在必要限制內。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Postman 或 cURL 來獲得答案](get-answer-from-knowledge-base-using-url-tool.md)