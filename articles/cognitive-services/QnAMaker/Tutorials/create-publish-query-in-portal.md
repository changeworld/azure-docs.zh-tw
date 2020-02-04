---
title: 教學課程：在 QnA Maker 中建立、發佈及回答
description: 本教學課程說明如何從網頁型常見問題集建立具有問題與答案的新知識庫。 儲存、訓練和發佈知識庫。 發佈知識庫之後，使用 cURL 命令來傳送問題和接收答案。 接著建立 Bot，並以相同問題測試 Bot。
ms.topic: tutorial
ms.date: 01/28/2020
ms.openlocfilehash: 07a92b0f24aa95fc4a5188f54c7194fd342409fb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844136"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>教學課程：在 QnA Maker 入口網站中建立知識庫

從網頁型常見問題集建立具有問題與答案的新知識庫。 儲存、訓練和發佈知識庫。 發佈知識庫之後，使用 cURL 命令來傳送問題和接收答案。 接著建立 Bot，並以相同問題測試 Bot。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 QnA Maker 入口網站中建立知識庫。
> * 檢閱、儲存及訓練知識庫。
> * 發佈知識庫。
> * 使用 cURL 查詢知識庫。
> * 建立 Bot。


> [!NOTE]
> 本教學課程的程式設計版本可從 [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)連同完整的解決方案取得。

## <a name="prerequisites"></a>Prerequisites

本教學課程需要現有的 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。

## <a name="create-a-knowledge-base"></a>建立知識庫

1. 登入 [QnA Maker](https://www.qnamaker.ai) 入口網站。

1. 從頂端功能表中選取 [建立知識庫]  。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. 略過第一個步驟，因為您將使用現有的 QnA Maker 服務。

1. 選取現有設定：

    |設定|目的|
    |--|--|
    |Microsoft Azure 目錄識別碼|此識別碼會與您用來登入 Azure 入口網站和 QnA Maker 入口網站的帳戶相關聯。 |
    |Azure 訂用帳戶名稱|QnA Maker 資源建立所在的計費帳戶。|
    |Azure QnA 服務|您現有的 QnA Maker 資源。|
    |Language|服務中所有知識庫使用的語言。 請勿在單一資源中混用語言。 這會危害答案品質。|

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. 輸入您的知識庫名稱 `My Tutorial kb`。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. 將下列設定填入您的資料庫：

    |設定名稱|設定值|目的|
    |--|--|--|
    |啟用多重回合擷取|已檢查|允許後續追蹤提示。|
    |預設答案文字|`No good answer was found.`|在沒有問題的相符答案時傳回。|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |該 URL 上的常見問題集內容會格式化為後面附有答案的問題。 QnA Maker 可解譯此格式以擷取問題和相關聯的答案。|
    |檔案 |_本教學課程中未使用_|此設定會上傳問題和答案的檔案。 |
    |閒聊特質|易記|此設定可為常見問題和答案賦予友善而非正式的[特質](../Concepts/best-practices.md#chit-chat)。 這些問題和答案可於後續進行編輯。 |

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. 選取 [建立您的 KB]  以完成建立程序。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>檢閱、儲存及訓練知識庫

1. 檢閱問題和答案。 第一個頁面是 URL 中的問題和答案。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. 從資料表底部選取問題和答案的最後一個頁面。 此頁面會顯示閒聊特質中的問題和答案。

1. 在問題和答案清單上方的工具列中，選取 [檢視選項]  圖示，然後選取 [顯示中繼資料]  。 這會顯示每個問題和答案的中繼資料標記。 閒聊問題已經設定**編輯：閒聊**中繼資料。 此中繼資料會連同選取的答案傳回至用戶端應用程式。 用戶端應用程式 (例如聊天機器人) 可使用此經果篩選的中繼資料來決定其他處理或與使用者的互動。

    ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. 在頂端功能表列中選取 [儲存並訓練]  。

## <a name="publish-to-get-knowledge-base-endpoints"></a>發佈以取得知識庫端點

從頂端功能表中選取 [發佈]  按鈕。 在發佈頁面上，選取 [發佈]  。

![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

在發佈知識庫後，即會顯示端點。

![端點設定的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

請勿關閉此 [發佈]  頁面。 稍後在本教學課程中需要用來建立 Bot。

## <a name="use-curl-to-query-for-an-faq-answer"></a>使用 cURL 查詢常見問題集的答案

1. 選取 [Curl]  索引標籤。

    ![Curl 索引標籤的螢幕擷取畫面](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. 複製 [Curl]  索引標籤的文字，然後在支援 cURL 的終端機或命令列中加以執行。 授權標頭的值包含文字 `Endpoint`，而其後依序有尾端空格和索引鍵。

1. 將 `<Your question>` 取代為 `How large can my KB be?`。 此問題類似於 `How large a knowledge base can I create?`，但並非完全相同。 QnA Maker 會套用自然語言處理，以判斷兩個問題是否相同。

1. 執行 Curl 命令並接收 JSON 回應，包括分數和答案。

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }

    ```

    QnA Maker 對於 42.81% 這個分數有些許程度的信賴。

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>使用 cURL 查詢閒聊答案

1. 在支援 cURL 的終端機中，將 `How large can my KB be?` 取代為以 Bot 對話結尾的使用者陳述，例如 `Thank you`。

1. 執行 Curl 命令並接收 JSON 回應，包括分數和答案。

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }

    ```

    由於 `Thank you` 的問題完全符合閒聊問題，因此QnA Maker 完全信賴 100 這個分數。 QnA Maker 也會傳回所有相關問題，以及包含閒聊中繼資料標記的中繼資料屬性。

## <a name="use-curl-to-query-for-the-default-answer"></a>使用 cURL 查詢預設答案

QnA Maker 不確定答案的任何問題都會收到預設答案。 此答案可在 Azure 入口網站中設定。

1. 在支援 cURL 的終端機中，將 `Thank you` 取代為 `x`。

1. 執行 Curl 命令並接收 JSON 回應，包括分數和答案。

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```

    QnA Maker 會傳回代表沒有信心的 `0` 分。 它也會傳回預設答案。

## <a name="create-a-knowledge-base-bot"></a>建立知識庫 Bot

如需詳細資訊，請參閱[使用此知識庫建立 Bot](create-qna-bot.md)。

## <a name="clean-up-resources"></a>清除資源

當您完成知識庫 Bot 後，請藉由移除 `my-tutorial-rg` 資源群組，來移除在 Bot 程序中建立的所有 Azure 資源。

當您完成知識庫時，請在 QnA Maker 入口網站中選取 [我的知識庫]  。 然後選取知識庫 (**我的教學課程知識庫**)，並選取該資料列中最右邊的刪除圖示。

## <a name="next-steps"></a>後續步驟

如需支援檔案格式的詳細資訊，請參閱[支援的資料來源](../Concepts/content-types.md)。

深入了解閒聊[特質](../Concepts/best-practices.md#chit-chat)。

如需預設答案的詳細資訊，請參閱[找不到相符項目](../Concepts/confidence-score.md#no-match-found)。

> [!div class="nextstepaction"]
> [使用此知識庫建立聊天 Bot](create-qna-bot.md)