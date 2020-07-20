---
title: 語音助理的常見問題
titleSuffix: Azure Cognitive Services
description: 使用自訂命令（預覽）或 Direct Line 語音訊道，取得語音助理最熱門問題的解答。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "74110354"
---
# <a name="voice-assistants-frequently-asked-questions"></a>語音助理的常見問題

如果您在本檔中找不到問題的解答，請參閱[其他支援選項](support.md)。

## <a name="general"></a>一般

**問：什麼是語音助理？**

**答：** 就像 Cortana 一樣，語音助理是接聽使用者說話語句的解決方案、分析那些語句的內容，並執行一或多個動作來回應語句的意圖，然後提供一項回應給經常包含說話元件的使用者。 這是與系統互動的「語音輸入」、「語音輸出」體驗。 語音助理作者會使用語音 SDK 中的來建立`DialogServiceConnector`裝置上的應用程式，以便與使用[自訂命令（預覽）](custom-commands.md)或 Bot Framework 的[Direct Line Speech](direct-line-speech.md)通道所建立的助理進行通訊。 這些助理可以使用自訂關鍵字、自訂語音和自訂語音，來提供針對您的品牌或產品量身打造的經驗。

**問：我應該使用自訂命令（預覽）或 Direct Line 語音嗎？有何不同？**

**答：** [自訂命令（預覽）](custom-commands.md)是較低複雜度的工具組，可輕鬆建立並裝載適合工作完成案例的小幫手。 [Direct Line Speech](direct-line-speech.md)提供更豐富、更精密的功能，可啟用健全的對話案例。 如需詳細資訊，請參閱[assistant 解決方案的比較](voice-assistants.md#choosing-an-assistant-solution)。

**問：如何開始使用？**

**答：** 開始建立自訂命令（預覽）應用程式或基本 Bot Framework bot 的最佳方式。

- [建立自訂命令（預覽）應用程式](quickstart-custom-speech-commands-create-new.md)
- [建立基本的 Bot Framework bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [將 bot 連線至 Direct Line 語音訊道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>偵錯

**問：我的通道秘密在哪裡？**

**答：** 如果您已使用 Direct Line 語音的預覽版本，或正在閱讀相關檔，您可能會預期會在 Direct Line 語音訊道註冊頁面上找到秘密金鑰。 語音 SDK 中`DialogServiceConfig`的 1.7 `FromBotSecret` factory 方法也需要此值。

最新版的 Direct Line Speech 可簡化從裝置聯繫 bot 的程式。 在 [通道註冊] 頁面上，頂端的下拉式會將您的 Direct Line 語音通道註冊與語音資源產生關聯。 一旦建立關聯之後，1.8 版語音 SDK 會`BotFrameworkConfig::FromSubscription`包含一個 factory 方法，其`DialogServiceConnector`會將設定為與您的訂用帳戶相關聯的 bot 連線。

如果您仍在將用戶端應用程式從1.7 版遷移至 1.8 `DialogServiceConfig::FromBotSecret`版，則可能會繼續使用非空白的非 null 值做為其通道秘密參數，例如先前使用的秘密。 使用與較新頻道註冊相關聯的語音訂用帳戶時，只會略過此功能。 請注意，此值_必須_為非 null 且不是空的，因為在服務端關聯相關之前，會在裝置上檢查這些值。

如需更詳細的指南，請參閱逐步解說通道註冊的[教學課程一節](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel)。

**問：我在連接時收到401錯誤，沒有任何作用。我知道我的語音訂用帳戶金鑰有效。這是怎麼回事？**

**答：** 在 Azure 入口網站上管理您的訂用帳戶時，請確定您使用的是**語音**資源（CognitiveServicesSpeechServices，"speech"），而_不_是**認知服務**資源（CognitiveServicesAllInOne，"All 認知服務"）。 此外，請檢查[語音服務區域的語音助理支援](regions.md#voice-assistants)。

![direct line speech 的正確訂用帳戶](media/voice-assistants/faq-supported-subscription.png "相容語音訂用帳戶的範例")

**問：我從 my `DialogServiceConnector`取得辨識文字，但我看到來自我的 bot 的「1011」錯誤和任何東西。因此?**

**答：** 此錯誤表示您的助理與語音助理服務之間發生通訊問題。

- 若為自訂命令（預覽），請確定您的自訂命令（預覽）應用程式已發佈
- 針對 Direct Line 語音，請確定您已將[bot 連線至 Direct Line 語音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)、將[串流通訊協定支援新增](https://aka.ms/botframework/addstreamingprotocolsupport)至您的 bot （具有相關的 Web 通訊端支援），然後檢查您的 bot 是否回應來自通道的連入要求。

**問：此程式碼仍然無法正常執行，並（或）在使用時收到不同`DialogServiceConnector`的錯誤。我該怎麼做？**

**答：** 以檔案為基礎的記錄提供更多詳細資料，並有助於加速支援要求。 若要啟用此功能，請參閱[如何使用檔案記錄](how-to-use-logging.md)。

## <a name="next-steps"></a>後續步驟

- [疑難排解](troubleshooting.md)
- [版本資訊](releasenotes.md)
