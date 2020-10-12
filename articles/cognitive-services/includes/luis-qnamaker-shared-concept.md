---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: ac0192da3afa76c3c21056f218f2b249c44d1c36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87133796"
---
認知服務提供兩種自然語言處理服務， [Language Understanding](../luis/what-is-luis.md) 和 [QnA Maker](../qnamaker/overview/overview.md)，各有不同的用途。 瞭解每個服務的使用時機，以及它們彼此如何補充。

自然語言處理 (NLP) 可讓您的用戶端應用程式（例如聊天機器人）與使用者一起使用自然語言。 使用者輸入句子或片語。 使用者的文字可能會有不佳的文法、拼寫和標點符號。 認知服務可以繼續執行使用者句子，並傳回聊天機器人所需的資訊來協助使用者。

## <a name="cognitive-services-with-nlp"></a>使用 NLP 的認知服務

Language Understanding (LUIS) 和 QnA Maker 提供 NLP。 用戶端應用程式會提交自然語言文字。 服務會取得文字、處理它，並傳回結果。

## <a name="when-to-use-each-service"></a>使用每個服務的時機

Language Understanding (LUIS) 和 QnA Maker 解決不同的問題。 LUIS 會決定使用者的文字 (稱為語句) ，而 QnA Maker 則會決定使用者的文字 (稱為查詢) 的答案。

為了挑選正確的服務，您必須瞭解來自用戶端應用程式的使用者文字，以及用戶端應用程式需要從認知服務取得的資訊。

如果聊天機器人收到文字 `How do I get to the Human Resources building on the Seattle North campus?` ，請使用下列圖表來瞭解每個服務如何搭配文字使用。

|服務|用戶端應用程式決定|
|--|--|
|LUIS|**判斷使用者** 對文字的意圖-服務不會傳回問題的答案。 例如，此文字會分類為符合 `FindLocation` 意圖。<br>|
|QnA Maker|從自訂知識庫傳回**問題的答案**。 例如，這項文字是由的靜態文字解答所組成的問題  `Get on the #9 bus and get off at Franklin street` 。|
|||

> [!div class="mx-imgBorder"]
> ![資訊圖以判斷何時使用 LUIS 和何時使用 QnA Maker](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>何時使用 LUIS？

當您需要知道聊天機器人在處理過程中使用該語句的意圖時，請使用 LUIS。 繼續進行範例文字， `How do I get to the Human Resources building on the Seattle North campus?` 一旦您知道使用者的意圖是要尋找一個位置，就可以傳遞語句 (的詳細資料，) 到另一項服務（例如運輸伺服器）來取得答案。

您不需要結合 LUIS 和 QnA Maker 來判斷意圖。

如果聊天機器人需要根據意圖和實體來處理文字 (使用 LUIS) ，以及使用 QnA Maker) 來尋找特定的靜態文字 (解答，您可能會結合這兩個服務。

## <a name="when-do-you-use-qna-maker"></a>何時使用 QnA Maker？

當您有答案的靜態知識庫時，請使用 QnA Maker。 此知識庫是依據需求所自訂的，且您已使用 PDF 和 URL 等文件建置完成。

繼續進行範例語句， `How do I get to the Human Resources building on the Seattle North campus?` 並將文字以查詢形式傳送至已發佈的 QnA Maker 服務，並獲得最佳答案。

您不需要結合 LUIS 和 QnA Maker 來判斷問題的答案。

如果聊天機器人需要根據意圖和實體來處理文字 (使用 LUIS) ，以及使用 QnA Maker) 尋找答案 (，您可能會結合這兩個服務來進行這種語句。

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>當知識庫未完成時，請使用這兩個服務

如果您正在建立 QnA Maker 知識庫，但知道主體網域正在變更 (例如及時資訊) ，您可以結合 LUIS 和 QnA Maker 服務。 這可讓您使用知識庫中的資訊，但也可以使用 LUIS 來判斷使用者的意圖。 一旦用戶端應用程式有目的，就可以要求來自另一個來源的相關資訊。

您的用戶端應用程式需要監視分數的 LUIS 和 QnA Maker 回應。 如果 QnA Maker 的分數低於某個任意臨界值，請使用從 LUIS 傳回的意圖和實體資訊，將資訊傳遞至協力廠商服務。

繼續進行範例文字， `How do I get to the Human Resources building on the Seattle North campus?` 假設 QnA Maker 傳回低信賴分數。 使用從 LUIS 傳回的意圖， `FindLocation` 以及任何已解壓縮的實體（例如 `Human Resources building` 和），將 `Seattle North campus` 此資訊傳送至對應或搜尋服務，以取得其他答案。

您可以向使用者呈現此協力廠商答案以進行驗證。 當您擁有使用者的核准之後，就可以回到 QnA Maker 加入資訊，以拓展您的知識。

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>當聊天機器人需要更多資訊時，請使用這兩種服務

如果聊天機器人所需的資訊超過任一項服務所提供的資訊，則若要繼續進行決策樹，請使用這兩個服務，並在用戶端應用程式中處理兩個回應。

使用 Bot framework **[分派 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 工具來協助建立處理這兩項服務的程式。 此工具會建立意圖的熱門 LUIS 應用程式，以在 LUIS 和 QnA Maker 之間分派為子應用程式。 [深入瞭解](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=cs) 如何整合 LUIS、QnA Maker 和 Bot framework。

使用 Bot builder 範例（以[c #](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch)或[Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)中**的分派 NLP**）來執行這種類型的聊天機器人。

## <a name="best-practices"></a>最佳作法

針對每個服務執行最佳作法：

* [LUIS](../luis/luis-concept-best-practices.md) 最佳作法
* [QnA Maker](../qnamaker/concepts/best-practices.md) 最佳作法

## <a name="see-also"></a>另請參閱

* [語言理解 (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [分派 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot framework 範例](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot 服務](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot 模擬器](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot framework 網路聊天](https://github.com/microsoft/BotFramework-WebChat)