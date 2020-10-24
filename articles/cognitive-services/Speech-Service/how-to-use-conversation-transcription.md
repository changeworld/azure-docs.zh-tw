---
title: 即時對話轉譯快速入門-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何搭配語音 SDK 使用即時對話轉譯。 對話轉譯可讓您透過將音訊串流處理至語音服務，來轉譯會議和其他交談，以新增、移除及識別多位參與者的能力。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 6cb338e8b7baa45e1c84f59a5730a9a500e71a79
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486770"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>開始使用即時對話轉譯

語音 SDK 的 **ConversationTranscriber** API 可讓您透過使用或將音訊串流處理至語音服務，來轉譯會議和其他交談，以新增、移除及識別多個 `PullStream` 參與者 `PushStream` 。 您會先使用 REST API 建立每個參與者的語音簽章，然後使用語音簽名搭配 SDK 來轉譯交談。 如需詳細資訊，請參閱對話轉譯 [總覽](conversation-transcription.md) 。

## <a name="limitations"></a>限制

* 僅適用于下列訂用帳戶區域： `centralus` 、 `eastasia` 、 `eastus` 、 `westeurope`
* 需要具有播放參考資料流的7個 mic 迴圈多重麥克風陣列。 麥克風陣列應符合 [我們的規格](https://aka.ms/sdsdk-microphone)。
* [語音裝置 SDK](speech-devices-sdk.md)提供適合的裝置，以及示範對話轉譯的範例應用程式。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](overview.md#try-the-speech-service-for-free)。

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [非同步對話](how-to-async-conversation-transcription.md) 
>  轉譯[ROOBO 裝置範例程式碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
> [Azure Kinect 開發工具組範例程式碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)
