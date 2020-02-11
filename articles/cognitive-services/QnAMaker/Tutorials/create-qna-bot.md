---
title: 教學課程：QnA Bot - Azure Bot 服務 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 本教學課程說明如何從 [發佈] 頁面建立現有知識庫的 QnA 聊天 Bot。 此 Bot 使用 Bot Framework SDK v4。 您不需要撰寫任何程式碼來建置 Bot，所有程式碼都已備妥。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: diberry
ms.openlocfilehash: a9afef07af1332910cc9e25f9d66db5930df3bd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901669"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>教學課程：使用 Azure Bot 服務 v4 建立 QnA Bot

從 [發佈]  頁面建立現有知識庫的 QnA 聊天 Bot。 此 Bot 使用 Bot Framework SDK v4。 您不需要撰寫任何程式碼來建置 Bot，所有程式碼都已備妥。

**在本教學課程中，您會了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 從現有的知識庫建立的 Azure Bot 服務
> * 與 Bot 聊天以確認程式碼可行

## <a name="prerequisites"></a>Prerequisites

您需要有已發佈的知識庫以供本教學課程使用。 如果您沒有，請依照[建立知識庫並回應](create-publish-query-in-portal.md)教學課程中的步驟，建立含有問題和解答的 QnA Maker 知識庫。

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>建立 QnA Bot

建立 Bot 作為知識庫的用戶端應用程式。

1. 在 QnA Maker 入口網站中，移至 [發佈]  頁面，並發佈您的知識庫。 選取 [建立 Bot]  。

    [![在 QnA Maker 入口網站中，移至 [發佈] 頁面，並發佈您的知識庫。選取 [建立 Bot]。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png#lightbox)

    Azure 入口網站隨即開啟，並顯示 Bot 建立組態。

1.  輸入用來建立 Bot 的設定：

    |設定|值|目的|
    |--|--|--|
    |Bot 控制代碼|`qna-maker-central-us-bot`|這是 Bot 的 Azure 資源名稱。|
    |訂用帳戶|請參閱用途。|選取您用來建立 QnA Maker 資源的相同訂用帳戶。|
    |資源群組|`docs`|用於所有 Bot 相關 Azure 資源的資源群組。|
    |Location|`west us`|Bot 的 Azure 資源位置。|
    |定價層|`F0`|Azure Bot 服務的免費層。|
    |應用程式名稱|`qna-maker-central-us-bot-app`|這是僅支援您的 Bot 的 Web 應用程式。 此應用程式名稱不應與您的 QnA Maker 服務已使用的相同。 不支援與任何其他資源共用 QnA Maker 的 Web 應用程式。|
    |SDK 語言|C#|這是 Bot Framework SDK 所使用的基礎程式設計語言。 您可以選擇 [C#](https://github.com/Microsoft/botbuilder-dotnet) 或 [Node.js](https://github.com/Microsoft/botbuilder-js)。|
    |QnA 驗證金鑰|**請勿變更**|此值會自動填入。|
    |App Service 方案/位置|**請勿變更**|在本教學課程中，位置並不重要。|
    |Application Insights|**請勿變更**|記錄會傳送至 Application Insights。|
    |Microsoft 應用程式識別碼|**請勿變更**|Active Directory 使用者和密碼是必填欄位。|

    > [!div class="mx-imgBorder"]
    > ![使用這些設定建立知識庫 Bot。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    等待幾分鐘，直到 Bot 建立程序通知回報作業成功。

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>與 Bot 聊天

1. 在 Azure 入口網站中，從通知開啟新的 Bot 資源。

    ![在 Azure 入口網站中，從通知開啟新的 Bot 資源。](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. 在 [Bot 管理]  中選取 [在網路聊天中測試]  ，然後輸入：`How large can my KB be?`。 Bot 將回應：


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![測試新的知識庫 Bot。](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    如需 Azure Bot 的詳細資訊，請參閱[使用 QnA Maker 回答問題](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>清除資源

當您完成本教學課程的 Bot 時，請在 Azure 入口網站中移除該 Bot。

如果您為 Bot 的資源建立了新的資源群組，請刪除該資源群組。

如果您未建立新的資源群組，則必須尋找與 Bot 相關聯的資源。 最簡單的方法是以 Bot 和 Bot 應用程式的名稱進行搜尋。 Bot 資源包括：

* App Service 方案
* 搜尋服務
* 認知服務
* 應用程式服務
* 或者，亦可包括 Application Insights 服務和 Application Insights 資料的儲存體


## <a name="related-to-qna-maker-bots"></a>QnA Maker Bot 相關資訊

* [醫療保健 Bot](https://docs.microsoft.com/HealthBot/qna_model_howto) 使用 QnA Maker 作為其[語言模型](https://docs.microsoft.com/HealthBot/qna_model_howto)之一。


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [概念：知識庫](../concepts/knowledge-base.md)

## <a name="see-also"></a>另請參閱

- [管理您的知識庫](https://qnamaker.ai)
- [在不同的頻道啟用您的 Bot](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
