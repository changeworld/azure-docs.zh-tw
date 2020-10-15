---
title: 教學課程：使用 Azure Bot Service 建立常見問題 Bot
description: 在本教學課程中，使用 QnA Maker 和 Azure Bot Service 建立無程式碼的常見問題 Bot。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab78ac41be2f900d84e82c21e566f5250b2afd18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777402"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>教學課程：使用 Azure Bot Service 建立常見問題 Bot
使用 QnA Maker 和 Azure [Bot Service](https://azure.microsoft.com/en-us/services/bot-service/) 建立無程式碼的常見問題 Bot。

在本教學課程中，您會了解如何：

<!-- green checkmark -->
> [!div class="checklist"]
> * 將 QnA Maker 知識庫連結至 Azure Bot Service
> * 部署 Bot
> * 在網路聊天中測試 Bot
> * 在支援的通道中啟用 Bot

## <a name="create-and-publish-a-knowledge-base"></a>建立和發佈知識庫

依照[快速入門](../Quickstarts/create-publish-knowledge-base.md)建立知識庫。 成功發佈知識庫之後，系統會帶您前往下列頁面。

![發佈成功的螢幕擷取畫面](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>建立 Bot

發佈之後，您可以從 [發佈]**** 頁面建立 Bot：

* 您可以快速建立數個 Bot，讓全部 Bot 指向相同知識庫中個別 Bot 適用的不同區域或定價方案。
* 如果您想讓知識庫只有一個 Bot，可使用**在 Azure 入口網站中檢視 Bot** 連結，來檢視您目前的 Bot 清單。

當您變更知識庫並重新發佈時，不需要對 Bot 採取進一步的動作。 其已設定為與知識庫搭配使用，而且會與知識庫未來的所有變更搭配運作。 每次發佈知識庫後，與之連線的所有 Bot 就會自動更新。

1. 在 QnA Maker 入口網站的 [發佈]**** 頁面上，選取 [建立 Bot]****。 只有在發佈知識庫後，此按鈕才會出現。

    ![建立 Bot 的螢幕擷取畫面](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Azure 入口網站會在新的瀏覽器索引標籤中開啟，其中會包含 Azure Bot 服務的建立頁面。 設定 Azure Bot 服務。 Bot 與 QnA Maker 可共用 Web 應用程式服務方案，但不能共用 Web 應用程式。 這表示 Bot 的**應用程式名稱**必須與 QnA Maker 服務的應用程式名稱不同。

    * **建議事項**
        * 變更 Bot 控制代碼 - 如果不是唯一的。
        * 選取 SDK 語言。 建立 Bot 後，您可以將程式碼下載至您的本機開發環境，並繼續進行開發程序。
    * **禁止事項**
        * 建立 Bot 時，請勿在 Azure 入口網站中變更下列設定。 這些是為您現有知識庫預先填入的內容：
           * QnA 驗證金鑰
           * App Service 方案和位置


1. 建立 Bot 之後，請開啟 [Bot 服務]**** 資源。
1. 在 [Bot 管理] 下，選取 [在網路聊天中測試]。
1. 在 [輸入您的訊息]**** 聊天提示中，輸入：

    `Azure services?`

    聊天 Bot 會使用來自您知識庫的答案來回應。

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="在測試網頁聊天中輸入使用者查詢。":::
1. 在其他[支援的通道](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0&preserve-view=true)中使用 Bot。
