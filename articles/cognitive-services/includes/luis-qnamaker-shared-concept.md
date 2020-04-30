---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 02610e647e2138cbf52f86c22107feec2d61273b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604948"
---
認知服務提供兩種自然語言處理服務， [Language Understanding](../luis/what-is-luis.md)和[QnA Maker](../qnamaker/overview/overview.md)，各有不同的用途。 瞭解每個服務的使用時機，以及它們彼此的補充方式。

自然語言處理（NLP）可讓您的用戶端應用程式（例如聊天機器人）與您的使用者一起使用自然語言。 使用者輸入句子或片語。 使用者的文字可能會有不佳的文法、拼寫及標點符號。 認知服務仍然可以透過使用者句子來執行，並傳回聊天機器人協助使用者所需的資訊。

## <a name="cognitive-services-with-nlp"></a>使用 NLP 認知服務

Language Understanding （LUIS）和 QnA Maker 提供 NLP。 用戶端應用程式會提交自然語言文字。 服務會接受文字並加以處理，然後傳回結果。

## <a name="when-to-use-each-service"></a>每項服務的使用時機

Language Understanding （LUIS）和 QnA Maker 解決不同的問題。 LUIS 會決定使用者文字的意圖（稱為語句），而 QnA Maker 會決定使用者文字的答案（稱為「查詢」）。

為了挑選正確的服務，您必須瞭解來自用戶端應用程式的使用者文字，以及用戶端應用程式需要從認知服務取得哪些資訊。

如果您的聊天機器人收到文字`How do I get to the Human Resources building on the Seattle North campus?`，請使用下圖來瞭解每個服務如何與文字搭配使用。

|Service|用戶端應用程式決定|
|--|--|
|LUIS|**判斷使用者**的文字意圖-服務不會傳回問題的答案。 例如，此文字會分類為符合`FindLocation`意圖。<br>|
|QnA Maker|從自訂知識庫傳回**問題的答案**。 例如，這項`Get on the #9 bus and get off at Franklin street`文字是由的靜態文字答案所決定的問題。|
|||

## <a name="when-do-you-use-luis"></a>何時使用 LUIS？

當您需要知道語句在聊天機器人中的處理過程中的意圖時，請使用 LUIS。 繼續進行範例文字， `How do I get to the Human Resources building on the Seattle North campus?`一旦您知道使用者的意圖是尋找某個位置，您可以將語句的詳細資料（包含實體）傳遞給另一個服務（例如運輸伺服器），以取得答案。

您不需要結合 LUIS 和 QnA Maker 來判斷意圖。

如果聊天機器人需要根據意圖和實體（使用 LUIS）來處理文字，以及尋找特定的靜態文字答案（使用 QnA Maker），您可能會結合這兩個服務來進行此語句。

## <a name="when-do-you-use-qna-maker"></a>何時使用 QnA Maker？

當您有答案的靜態知識庫時，請使用 QnA Maker。 此知識庫是依據需求所自訂的，且您已使用 PDF 和 URL 等文件建置好。

繼續進行範例語句， `How do I get to the Human Resources building on the Seattle North campus?`將文字當做查詢傳送至您發佈的 QnA Maker 服務，並獲得最佳解答。

您不需要結合 LUIS 和 QnA Maker 來判斷問題的答案。

如果聊天機器人需要根據意圖和實體（使用 LUIS）來處理文字，以及尋找答案（使用 QnA Maker），您可能會合並這兩個服務。

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>當您的知識庫不完整時，請使用這兩項服務

如果您要建立 QnA Maker 知識庫，但是知道主體網域正在變更（例如及時資訊），您可以結合 LUIS 和 QnA Maker 服務。 這可讓您使用知識庫中的資訊，但也可以使用 LUIS 來判斷使用者的意圖。 一旦用戶端應用程式有意圖，它就可以向另一個來源要求相關資訊。

您的用戶端應用程式必須同時監視 LUIS 和 QnA Maker 分數的回應。 如果 QnA Maker 的分數低於部分任意臨界值，請使用從 LUIS 傳回的意圖和實體資訊，將資訊傳遞給協力廠商服務。

繼續進行範例文字， `How do I get to the Human Resources building on the Seattle North campus?`，假設 QnA Maker 傳回的信賴分數較低。 使用從 LUIS 傳回的意圖， `FindLocation`以及任何已解壓縮的實體（ `Human Resources building`例如`Seattle North campus`和），將此資訊傳送給對應或搜尋服務，以取得另一個答案。

您可以向使用者呈現此協力廠商答案以進行驗證。 一旦您有使用者的核准，您可以回到 QnA Maker 新增資訊，以拓展您的知識。

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>當您的聊天機器人需要詳細資訊時，請使用這兩項服務

如果您的聊天機器人需要的資訊比服務提供的還多，若要繼續進行決策樹，請使用這兩個服務，並在用戶端應用程式中處理這兩個回應。

使用 Bot framework**[分派 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 工具，協助建立處理這兩項服務的程式。 此工具會建立意圖的熱門 LUIS 應用程式，以在 LUIS 和 QnA Maker 之間分派作為子應用程式。

在[c #](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch)或[Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)中使用 Bot builder 範例**NLP with 分派**，以執行這種類型的聊天機器人。

## <a name="best-practices"></a>最佳作法

針對每個服務執行最佳做法：

* [LUIS](../luis/luis-concept-best-practices.md)最佳作法
* [QnA Maker](../qnamaker/concepts/best-practices.md)最佳做法

## <a name="see-also"></a>請參閱

* [語言理解 (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [分派 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot framework 範例](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot 服務](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot 模擬器](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot framework 網路聊天](https://github.com/microsoft/BotFramework-WebChat)