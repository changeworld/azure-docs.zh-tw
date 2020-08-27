---
title: 非同步對話轉譯-語音服務
titleSuffix: Azure Cognitive Services
description: '瞭解如何使用語音服務來使用非同步對話轉譯。 僅適用于 JAVA 和 c #。'
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934631"
---
# <a name="asynchronous-conversation-transcription"></a>非同步對話轉譯

在本文中，會使用 **RemoteConversationTranscriptionClient** API 來示範非同步對話轉譯。 如果您已設定對話轉譯來進行非同步轉譯並擁有 `conversationId` ，則可以 `conversationId` 使用 **RemoteConversationTranscriptionClient** API 來取得與該文字相關聯的轉譯。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同步與即時 + 非同步

使用非同步轉譯時，您可以串流對話音訊，但不需要即時傳回轉譯。 相反地，傳送音訊之後，請使用 `conversationId` 的 `Conversation` 來查詢非同步轉譯的狀態。 當非同步轉譯準備就緒時，您會得到 `RemoteConversationTranscriptionResult` 。

使用即時加上非同步時，您可以即時取得轉譯，但也可以使用與 `conversationId` 非同步案例) 類似的 (來查詢來取得轉譯。

完成非同步轉譯需要兩個步驟。 第一個步驟是上傳音訊，選擇非同步或即時加上非同步。 第二個步驟是取得轉譯結果。

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
