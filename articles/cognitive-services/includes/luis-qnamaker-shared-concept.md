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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604948"
---
認知服務提供兩種自然語言處理服務,[語言理解和](../luis/what-is-luis.md)[QnA製作器](../qnamaker/overview/overview.md),每個服務都有不同的目的。 了解何時使用每項服務,以及它們如何相互稱讚。

自然語言處理 (NLP) 允許用戶端應用程式(如聊天機器人)使用自然語言與使用者一起工作。 用戶輸入句子或短語。 使用者的文本可能語法、拼寫和標點符號較差。 認知服務無論如何都可以處理使用者句子,返回聊天機器人需要幫助使用者的資訊。

## <a name="cognitive-services-with-nlp"></a>使用 NLP 的認知服務

語言理解 (LUIS) 和 QnA 製造商提供 NLP。 用戶端應用程式提交自然語言文本。 服務獲取文本,處理文本並返回結果。

## <a name="when-to-use-each-service"></a>何時使用每個服務

語言理解 (LUIS) 和 QnA 製造商可解決不同的問題。 LUIS 確定使用者文本的意圖(稱為話語),而 QnA Maker 確定用戶文本的答案(稱為查詢)。

為了選擇正確的服務,您需要瞭解來自用戶端應用程式的使用者文本,以及用戶端應用程式需要從認知服務獲得哪些資訊。

如果您的聊天機器人收到文本`How do I get to the Human Resources building on the Seattle North campus?`,請使用下面的圖表來瞭解每個服務如何與文字一起工作。

|服務|用戶端應用程式確定|
|--|--|
|LUIS|**確定使用者的文本意圖**- 服務不會返回問題的答案。 例如, 這個文字會被歸類別為`FindLocation`符合意圖 。<br>|
|QnA Maker|從自訂知識庫中**傳回問題的答案**。 例如,此文字會確定為具有靜態文字答案的問題`Get on the #9 bus and get off at Franklin street`。|
|||

## <a name="when-do-you-use-luis"></a>你什麼時候使用LUIS?

當您需要知道話語的意圖作為聊天機器人過程中進程的一部分時,請使用 LUIS。 繼續示例文本,`How do I get to the Human Resources building on the Seattle North campus?`一旦知道使用者的意圖是查找位置,就可以將有關話語的詳細資訊(與實體一起拉出)傳遞給另一個服務(如運輸伺服器),以獲得答案。

您無需將 LUIS 和 QnA 製造商組合以確定意圖。

如果聊天機器人需要根據意圖和實體(使用 LUIS)處理文本,以及查找特定的靜態文本答案(使用 QnA Maker),則可以為此語音組合這兩個服務。

## <a name="when-do-you-use-qna-maker"></a>何時使用 QnA 製造商?

當您有靜態答案知識庫時,請使用 QnA Maker。 此知識庫是依據需求所自訂的，且您已使用 PDF 和 URL 等文件建置好。

繼續範例陳述,`How do I get to the Human Resources building on the Seattle North campus?`將文字作為查詢發送到已發布的 QnA Maker 服務,並接收最佳答案。

您無需將 LUIS 和 QnA Maker 組合在一起,以確定問題的答案。

如果聊天機器人需要根據意圖和實體(使用 LUIS)處理文本並找到答案(使用 QnA Maker),則可以為此語音組合這兩個服務。

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>知識庫不完整時使用這兩種服務

如果您正在構建 QnA Maker 知識庫,但知道主題域正在發生變化(例如及時資訊),則可以將 LUIS 和 QnA Maker 服務結合起來。 這允許您使用知識庫中的資訊,但也可以使用 LUIS 來確定用戶的意圖。 一旦用戶端應用程式具有意圖,它可以請求來自其他來源的相關信息。

用戶端應用程式需要同時監視 LUIS 和 QnA Maker 的評分回應。 如果來自 QnA Maker 的分數低於某個任意閾值,請使用從 LUIS 返回的意圖和實體資訊將資訊傳遞給第三方服務。

繼續範例文字,`How do I get to the Human Resources building on the Seattle North campus?`假設 QnA Maker 傳回低置信度分數。 使用從 LUIS`FindLocation`返回的意圖以及任何提取的實體`Human Resources building`(`Seattle North campus`如和 )將此資訊發送到映射或搜索服務以查找其他答案。

您可以將此第三方答案呈現給用戶進行驗證。 獲得使用者批准後,您可以返回 QnA Maker 添加資訊以培養您的知識。

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>當您的聊天機器人需要更多資訊時,請使用這兩種服務

如果您的聊天機器人需要的資訊比任一服務提供的資訊都多,要繼續通過決策樹,請使用兩個服務並處理用戶端應用程式中的兩個回應。

使用 Bot 框架**[調度 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 工具幫助建構一個流程以同時使用這兩種服務。 此工具將建構一個頂級的 LUIS 應用,該應用在 LUIS 和 QnA Maker 之間作為子應用調度。

使用 Bot 生成器範例**NLP 和調度**,在[C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch)或[Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)中實現這種類型的聊天機器人。

## <a name="best-practices"></a>最佳作法

為每個服務實施最佳實務:

* [LUIS](../luis/luis-concept-best-practices.md)最佳實務
* [QnA 製造商](../qnamaker/concepts/best-practices.md)最佳實作

## <a name="see-also"></a>另請參閱

* [語言理解 (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [調度 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [機器人框架範例](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure 機器人服務](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure 機器人模擬器](https://github.com/Microsoft/BotFramework-Emulator)
* [機器人框架網路聊天](https://github.com/microsoft/BotFramework-WebChat)