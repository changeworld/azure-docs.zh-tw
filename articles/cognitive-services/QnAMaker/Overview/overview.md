---
title: 什麼是 QnA Maker 服務？
titleSuffix: Azure Cognitive Services
description: QnA Maker 是一項雲端式 NLP 服務，可對您的資料輕鬆地建立自然對話層。 其可從資訊的自訂知識庫 (KB) 為指定的自然語言輸入尋找最適當的答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 7bac2546ac35f5f188a6490c4d9ecb2b86d7bb2d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843051"
---
# <a name="what-is-the-qna-maker-service"></a>什麼是 QnA Maker 服務？

QnA Maker 是一項雲端式自然語言處理 (NLP) 服務，可對您的資料輕鬆地建立自然對話層。 其可從資訊的自訂知識庫 (KB) 為指定的自然語言輸入尋找最適當的答案。

QnA Maker 的用戶端應用程式是任何可透過自然語言與使用者溝通以回答問題的對話應用程式。 用戶端應用程式的例子包括社群媒體應用程式、聊天機器人，以及具備語音功能的桌面應用程式。

## <a name="when-to-use-qna-maker"></a>QnA Maker 的使用時機

* **當您有靜態資訊時** - 當回答知識庫中有靜態資訊時，請使用 QnA Maker。 此知識庫是依據需求所自訂的，且您已使用 [PDF 和 URL](../concepts/content-types.md) 等文件建置好。
* **當您想要為要求、問題或命令提供相同的答案時** - 當不同使用者提交相同問題時，系統會傳回相同的答案。
* **當您想要根據中繼資訊來篩選靜態資訊時** - 新增[中繼資料](../how-to/metadata-generateanswer-usage.md)標籤，以提供與用戶端應用程式使用者和資訊相關的其他篩選選項。 常見的中繼資料資訊包括[閒聊](../how-to/chit-chat-knowledge-base.md)、內容類型或格式、內容用途和內容有效期限。
* **當您想要管理包含靜態資訊的聊天機器人對話時** - 您的知識庫會取得使用者的交談文字或命令，並做出回答。 如果答案是預先決定對話流程的一部分 (在您的知識庫中會以[多回合內容](../how-to/multiturn-conversation.md)來表示)，則聊天機器人可以輕鬆地提供此流程。

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>在聊天機器人中使用 QnA Maker 知識庫

在發佈 QnA Maker 知識庫後，用戶端應用程式會將問題傳送至知識庫端點，並以 JSON 回應的形式接收結果。 聊天機器人是 QnA Maker 的常見用戶端應用程式。

![向聊天機器人詢問問題並從知識庫內容取得答案](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|步驟|動作|
|:--|:--|
|1|用戶端應用程式傳送使用者的問題  (以自己的話語表達的文字)「如何以程式設計方式更新知識庫？」 到知識庫端點。|
|2|QnA Maker 使用訓練好的知識庫來提供正確解答，以及任何可用來精簡搜尋以獲得最佳解答的後續提示。 QnA Maker 會傳回 JSON 格式的回應。|
|3|用戶端應用程式使用 JSON 回應來決定如何繼續對話。 這些決定可能包括顯示最佳解答和呈現更多選擇來精簡搜尋以獲得最佳解答。 |
|||

## <a name="what-is-a-knowledge-base"></a>什麼是知識庫？

QnA Maker 會在問答集的知識庫中[匯入您的內容](../concepts/knowledge-base.md)。 匯入程序會擷取結構化和半結構化內容各部分之間關聯性的相關資訊，來表示問答集之間的關聯性。 您可以編輯這些問答集或新增問答集。

問答集的內容包括：
* 問題的所有替代形式
* 在搜尋期間用來篩選答案選擇的中繼資料標籤
* 後續提示，以繼續精簡搜尋

![問題和答案與中繼資料的範例](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

在發佈知識庫之後，用戶端應用程式會將使用者的問題傳送至您的端點。 QnA Maker 服務會處理問題，並以最佳答案回應。

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>不使用程式碼而在聊天機器人中進行建立、管理和發佈動作

QnA Maker 入口網站可提供完整的知識庫撰寫體驗。 您可以將文件 (以其目前的格式) 匯入到知識庫。 這些文件 (例如常見問題集、產品手冊、試算表或網頁) 會轉換成問答集。 系統會分析每個問答集來提供後續提示，且這些問答集各自都會與其他問答集連結。 最終的 Markdown  格式支援豐富的呈現方式，包括影像和連結。

知識庫編輯完成後，請將知識庫發佈至運作中的 [Azure Web 應用程式聊天機器人](https://azure.microsoft.com/services/bot-service/) (不必撰寫任何程式碼)。 在 [Azure 入口網站](https://portal.azure.com)中測試聊天機器人，或下載並繼續開發。

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>搜尋品質和排名可提供最佳答案

QnA Maker 的系統是分層排名方法。 資料會儲存在 Azure 搜尋服務中，這也會作為第一個排名層。 接著，會透過 QnA Maker 的 NLP 重新排名模型傳遞 Azure 搜尋服務的前幾個結果，以產生最終結果和信賴分數。

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker 可改善對話流程

QnA Maker 提供多回合提示和主動式學習，可協助您改善基本的問答集。

**多回合提示**讓您有機會連結問題和答案配對。 此連結可讓用戶端應用程式提供最佳答案，並提供更多問題來精簡搜尋以獲得最終答案。

知識庫從已發佈端點的使用者收到問題後，QnA Maker 會將**主動式學習**套用到這些真實世界的問題，以建議您變更知識庫來改善品質。

## <a name="development-lifecycle"></a>開發生命週期

QnA Maker 提供撰寫、定型及發佈功能以及共同作業權限來整合到完整的開發生命週期。

## <a name="how-do-i-start"></a>如何開始使用？

**步驟 1**：在 [Azure 入口網站](https://portal.azure.com)中建立 QnA Maker 資源。

**步驟 2**：在 [QnA Maker](https://www.qnamaker.ai) 入口網站中建立知識庫。 新增[檔案和 URL](../concepts/content-types.md) 以建立知識庫。

**步驟 3**：使用 [cURL 或 Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) 從自訂端點發佈知識庫並進行測試。

**步驟 4**：從您的用戶端應用程式，以程式設計方式呼叫您知識庫的端點。 用戶端應用程式會處理 JSON 回應，以向使用者顯示最佳答案。

## <a name="next-steps"></a>後續步驟
QnA Maker 可提供建置、管理和部署自訂知識庫所需的所有項目。

> [!div class="nextstepaction"]
> [檢閱最新的變更](../whats-new.md)
