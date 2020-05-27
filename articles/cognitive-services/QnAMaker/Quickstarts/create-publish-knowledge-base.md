---
title: 快速入門：建立、定型和發佈知識庫-QnA Maker
description: 您可以從自己的內容 (例如常見問題集或產品手冊) 建立 QnA Maker 知識庫 (KB)。 本文包含從簡單的常見問題集網頁建立 QnA Maker 知識庫的範例，以回答問題 QnA Maker。
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: a35cbdea2d40e4b0df0ce0f0b36f1b6bf60dd42e
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871424"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>快速入門：建立、訓練及發佈您的 QnA Maker 知識庫

您可以從自己的內容 (例如常見問題集或產品手冊) 建立 QnA Maker 知識庫 (KB)。 本文包含從簡單的常見問題集網頁建立 QnA Maker 知識庫的範例，以回答問題 QnA Maker。

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> * 在 Azure 入口網站中所建立的 QnA Maker [資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 請記住您在建立資源時選取的 Azure Active Directory 識別碼、訂用帳戶、QnA 資源名稱。

## <a name="create-your-first-qna-maker-knowledge-base"></a>建立第一個 QnA Maker 知識庫

1. 利用您的 Azure 認證登入 [QnAMaker.ai](https://QnAMaker.ai) 入口網站。

1. 在 QnA Maker 入口網站中，選取 [建立知識庫]****。

1. 如果您已經有 QnA Maker 資源，請在 [建立]**** 頁面上略過 [步驟 1]****。

    如果您尚未建立資源，則請選取 [建立 QnA 服務]****。 系統會將您導向 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以在您的訂用帳戶中設定 QnA Maker 服務。 請記住您在建立資源時選取的 Azure Active Directory 識別碼、訂用帳戶、QnA 資源名稱。

    當您在 Azure 入口網站中建立好資源時，請回到 QnA Maker 入口網站、重新整理瀏覽器頁面，然後繼續 [步驟 2]****。

1. 在 [步驟 3]**** 中，選取您的 Active directory、訂用帳戶、服務 (資源)，以及在服務中所建立所有知識庫的語言。

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="選取 QnA Maker 服務知識庫的螢幕擷取畫面":::
    
1. 在 [步驟 3]**** 中，將知識庫命名為**我的範例 QnA KB**。

1. 在 [步驟 4]  中，使用下表進行設定：

    |設定|值|
    |--|--|
    |**啟用從 URL、.pdf 或 .docx 檔案進行多回合擷取。**|已檢查|
    |**預設答案文字**| `Quickstart - default answer not found.`|
    |**+ 新增 URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**閒聊**|選取 [Professional] |

1. 在**步驟 5**中，選取 [**建立您的 KB**]。

    擷取程序需要一點時間來讀取文件並找出問題和回答。

    在 QnA Maker 成功建立知識庫之後，[知識庫]**** 頁面隨即開啟。 您可以在此頁面上編輯知識庫的內容。

## <a name="add-a-new-question-and-answer-set"></a>新增問答集

1. 在 QnA Maker 入口網站的 [編輯]**** 頁面上，從操作工具列中選取 [+新增 QnA 配對]****。
1. 新增下列問題：

    `How many Azure services are used by a knowledge base?`

1. 新增使用 _markdown_ 設定格式的答案：

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="以文字型式新增問題與使用 markdown 設定格式的答案。":::

    markdown 符號 `*` 用於表示項目符號。 `\n` 用於表示新行。

    [編輯]**** 頁面會顯示 markdown。 當您稍後使用 [測試]**** 面板時，您將會看到 markdown 正確顯示。

## <a name="save-and-train"></a>儲存並定型

在右上方，選取 [儲存並定型]**** 來儲存您的編輯內容並將 QnA Maker 定型。 除非儲存編輯內容，否則不會保留。

## <a name="test-the-knowledge-base"></a>測試知識庫

1. 在 QnA Maker 入口網站的右上方，選取 [測試]**** 來測試您所做的變更是否已生效。
1. 在文字方塊中輸入範例使用者查詢。

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png" alt-text="在文字方塊中輸入範例使用者查詢。":::

1. 選取 [檢查]****，更詳細地檢查回應。 測試視窗是用來測試您對知識庫所做的變更，然後加以發佈。

1. 再次選取 [測試]**** 以關閉 [測試]**** 面板。

## <a name="publish-the-knowledge-base"></a>發佈知識庫

當您發佈知識庫時，您知識庫的內容會從 `test` 索引移到 Azure 搜尋服務中的 `prod` 索引。

![移動您的知識庫內容的螢幕擷取畫面](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. 在 QnA Maker 入口網站中，選取 [發佈]****。 若要接著確認，請選取頁面上的 [發佈]****。

    QnA Maker 服務現在已成功發佈。 您可以在您的應用程式或 Bot 程式碼中使用此端點。

    ![發佈成功的螢幕擷取畫面](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>建立 Bot

發佈之後，您可以從 [發佈]**** 頁面建立 Bot：

* 您可以快速建立數個 Bot，讓全部 Bot 指向相同知識庫中個別 Bot 適用的不同區域或定價方案。
* 如果您想讓知識庫只有一個 Bot，可使用**在 Azure 入口網站中檢視 Bot** 連結，來檢視您目前的 Bot 清單。

當您變更知識庫並重新發佈時，不需要對 Bot 採取進一步的動作。 其已設定為與知識庫搭配使用，而且會與知識庫未來的所有變更搭配運作。 每次發佈知識庫後，與之連線的所有 Bot 就會自動更新。

1. 在 QnA Maker 入口網站的 [發佈]**** 頁面上，選取 [建立 Bot]****。 只有在發佈知識庫後，此按鈕才會出現。

    ![建立 Bot 的螢幕擷取畫面](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Azure 入口網站會在新的瀏覽器索引標籤中開啟，其中會包含 Azure Bot 服務的建立頁面。 設定 Azure Bot 服務。 Bot 與 QnA Maker 可共用 Web 應用程式服務方案，但不能共用 Web 應用程式。 這表示 Bot 的**應用程式名稱**必須與 QnA Maker 服務的應用程式名稱不同。

    * **對**
        * 變更 Bot 控制代碼 - 如果不是唯一的。
        * 選取 SDK 語言。 建立 Bot 後，您可以將程式碼下載至您的本機開發環境，並繼續進行開發程序。
    * **禁止事項**
        * 建立 Bot 時，請勿在 Azure 入口網站中變更下列設定。 這些是為您現有知識庫預先填入的內容：
           * QnA 驗證金鑰
           * App Service 方案和位置


1. 建立 Bot 之後，請開啟 [Bot 服務]**** 資源。
1. 在 [ **Bot 管理**] 底下，選取 [**在網路聊天中測試**]。
1. 在 [輸入您的訊息]**** 聊天提示中，輸入：

    `Azure services?`

    聊天 Bot 會使用來自您知識庫的答案來回應。

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="在測試網頁聊天中輸入使用者查詢。":::

## <a name="what-did-you-accomplish"></a>您完成了哪些工作？

您已建立新的知識庫、將公用 URL 加入至知識庫、加入您自己的 QnA 組、定型、測試和發佈知識庫。

在發佈知識庫之後，您已建立 Bot 並測試 Bot。

這一切都是在幾分鐘內完成，而不需要撰寫任何程式碼或清除內容。

## <a name="clean-up-resources"></a>清除資源

如果您不想繼續進行下一個快速入門，請刪除 Azure 入口網站中的 QnA Maker 和 Bot Framework 資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增具有中繼資料的問題](add-question-metadata-portal.md)

如需詳細資訊：

* [ Markdown 格式](../reference-markdown-format.md)
* QnA Maker [資料來源](../concepts/knowledge-base.md)。


