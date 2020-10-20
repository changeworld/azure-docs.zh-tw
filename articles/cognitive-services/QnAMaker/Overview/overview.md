---
title: 什麼是 QnA Maker 服務？
description: QnA Maker 是一項雲端式 NLP 服務，可對您的資料輕鬆地建立自然對話層。 其可從資訊的自訂知識庫 (KB) 為指定的自然語言輸入尋找最適當的答案。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 05/26/2020
ms.custom: cog-serv-seo-aug-2020
keywords: qna maker, 低程式碼聊天機器人, 多回合交談
ms.openlocfilehash: 6a5ea51086e3ab532966c9cea9eb866334494bba
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874419"
---
# <a name="what-is-qna-maker"></a>什麼是 QnA Maker？

QnA Maker 是一項雲端式自然語言處理 (NLP) 服務，可讓您透過您的資料建立自然對話層。 其用來從資訊的自訂知識庫 (KB) 為任何輸入尋找最適當的答案。

QnA Maker 通常用來建立交談式用戶端應用程式，其中包括社交媒體應用程式、聊天機器人，以及具備語音功能的傳統型應用程式。

## <a name="when-to-use-qna-maker"></a>QnA Maker 的使用時機

* **當您有靜態資訊時** - 當回答知識庫中有靜態資訊時，請使用 QnA Maker。 此知識庫是依據需求所自訂的，且您已使用 [PDF 和 URL](../concepts/content-types.md) 等文件建置好。
* **當您想要為要求、問題或命令提供相同的答案時** - 當不同使用者提交相同問題時，系統會傳回相同的答案。
* **當您想要根據中繼資訊來篩選靜態資訊時** - 新增[中繼資料](../how-to/metadata-generateanswer-usage.md)標籤，以提供與用戶端應用程式使用者和資訊相關的其他篩選選項。 常見的中繼資料資訊包括[閒聊](../how-to/chit-chat-knowledge-base.md)、內容類型或格式、內容用途和內容有效期限。
* **當您想要管理包含靜態資訊的聊天機器人對話時** - 您的知識庫會取得使用者的交談文字或命令，並做出回答。 如果答案是預先決定對話流程的一部分 (在您的知識庫中會以[多回合內容](../how-to/multiturn-conversation.md)來表示)，則聊天機器人可以輕鬆地提供此流程。

## <a name="what-is-a-knowledge-base"></a>什麼是知識庫？

QnA Maker 會在問答組的知識庫中[匯入您的內容](../concepts/knowledge-base.md)。 匯入程序會擷取結構化和半結構化內容各部分之間關聯性的相關資訊，來表示問答組之間的關聯性。 您可以編輯這些問答組或新增問答組。

問答組的內容包括：
* 問題的所有替代形式
* 在搜尋期間用來篩選答案選擇的中繼資料標籤
* 後續提示，以繼續精簡搜尋

![問題和答案與中繼資料的範例](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

在發佈知識庫之後，用戶端應用程式會將使用者的問題傳送至您的端點。 QnA Maker 服務會處理問題，並以最佳答案回應。

## <a name="create-a-chat-bot-programmatically"></a>以程式設計方式建立聊天機器人

在發佈 QnA Maker 知識庫後，用戶端應用程式會將問題傳送至知識庫端點，並以 JSON 回應的形式接收結果。 聊天機器人是 QnA Maker 的常見用戶端應用程式。

![向聊天機器人詢問問題並從知識庫內容取得答案](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|步驟|動作|
|:--|:--|
|1|用戶端應用程式傳送使用者的問題 (以自己的話語表達的文字)「如何以程式設計方式更新知識庫？」 到知識庫端點。|
|2|QnA Maker 使用訓練好的知識庫來提供正確解答，以及任何可用來精簡搜尋以獲得最佳解答的後續提示。 QnA Maker 會傳回 JSON 格式的回應。|
|3|用戶端應用程式使用 JSON 回應來決定如何繼續對話。 這些決定可能包括顯示最佳解答和呈現更多選擇來精簡搜尋以獲得最佳解答。 |
|||

## <a name="build-low-code-chat-bots"></a>建立低程式碼聊天機器人

QnA Maker 入口網站可提供完整的知識庫撰寫體驗。 您可以將文件 (以其目前的格式) 匯入到知識庫。 這些文件 (例如常見問題集、產品手冊、試算表或網頁) 會轉換成問答組。 系統會分析每個問答組來提供後續提示，且這些問答組各自都會與其他問答組連結。 最終的 Markdown 格式支援豐富的呈現方式，包括影像和連結。

知識庫編輯完成後，請將知識庫發佈至運作中的 [Azure Web 應用程式聊天機器人](https://azure.microsoft.com/services/bot-service/) (不必撰寫任何程式碼)。 在 [Azure 入口網站](https://portal.azure.com)中測試聊天機器人，或下載聊天機器人並繼續開發。

## <a name="high-quality-responses-with-layered-ranking"></a>具有排名層的高品質回應

QnA Maker 的系統是分層排名方法。 資料會儲存在 Azure 搜尋服務中，這也會作為第一個排名層。 接著，會透過 QnA Maker 的 NLP 重新排名模型傳遞 Azure 搜尋服務的前幾個結果，以產生最終結果和信賴分數。

## <a name="multi-turn-conversations"></a>多回合交談

QnA Maker 提供多回合提示和主動式學習，可協助您改善基本的問答組。

**多回合提示**讓您有機會連結問題和答案配對。 此連結可讓用戶端應用程式提供最佳答案，並提供更多問題來精簡搜尋以獲得最終答案。

知識庫從已發佈端點的使用者收到問題後，QnA Maker 會將**主動式學習**套用到這些真實世界的問題，以建議您變更知識庫來改善品質。

## <a name="development-lifecycle"></a>開發生命週期

QnA Maker 提供撰寫、定型及發佈功能以及共同作業權限來整合到完整的開發生命週期。

> [!div class="mx-imgBorder"]
> ![開發週期的概念影像](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>完成快速入門

我們會以最熱門的程式設計語言提供快速入門，目的是教您基本的設計模式，並讓您能在 10 分鐘內執行程式碼。 請參閱下列清單，以取得每項功能的快速入門。

* [開始使用 QnA Maker 用戶端程式庫](../quickstarts/quickstart-sdk.md)
* [開始使用 QnA Maker 入口網站](../quickstarts/create-publish-knowledge-base.md)
* [開始使用 QnA Maker REST API](../quickstarts/quickstart-rest-curl.md)


## <a name="next-steps"></a>後續步驟
QnA Maker 可提供建置、管理和部署自訂知識庫所需的所有項目。

> [!div class="nextstepaction"]
> [檢閱最新的變更](../whats-new.md)
