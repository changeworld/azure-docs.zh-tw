---
title: 非同步對話轉譯-語音服務
titleSuffix: Azure Cognitive Services
description: '瞭解如何使用語音服務來進行非同步對話轉譯。 僅適用于 JAVA 和 c #。'
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 65a6fd7e0e1e64a459a46c0f97a2afdbc03573a9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284199"
---
# <a name="asynchronous-conversation-transcription"></a>非同步對話轉譯

在本文中，非同步對話轉譯會使用**RemoteConversationTranscriptionClient** API 來示範。 如果您已設定對話轉譯來執行非同步轉譯，而且有 `conversationId` ，您可以 `conversationId` 使用**RemoteConversationTranscriptionClient** API 取得與相關聯的轉譯。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同步與即時 + 非同步

透過非同步轉譯，您可以串流處理對話音訊，但不需要即時傳回的轉譯。 相反地，在傳送音訊之後，請使用 `conversationId` 的 `Conversation` 來查詢非同步轉譯的狀態。 當非同步轉譯準備就緒時，您會取得 `RemoteConversationTranscriptionResult` 。

透過即時加上非同步，您可以即時取得轉譯，但也會使用 `conversationId` 來查詢（類似于非同步案例）來取得轉譯。

完成非同步轉譯需要兩個步驟。 第一個步驟是上傳音訊，選擇 [僅限非同步] 或 [即時加上非同步]。 第二個步驟是取得轉譯結果。

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
