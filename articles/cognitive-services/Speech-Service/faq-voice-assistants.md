---
title: 語音助理常見問題
titleSuffix: Azure Cognitive Services
description: 使用自訂命令（預覽）或直撥語音通道獲取有關語音助理的最常見問題的答案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110354"
---
# <a name="voice-assistants-frequently-asked-questions"></a>語音助理常見問題

如果您在本文檔中找不到問題的答案，請查看[其他支援選項](support.md)。

## <a name="general"></a>一般

**問：什麼是語音助理？**

**答：** 與 Cortana 一樣，語音助理是一種解決方案，用於偵聽使用者的語音陳述，分析這些話語的內容以表達意義，執行一個或多個操作以回應話語的意圖，然後向通常包含語音元件的使用者提供回應。 它是與系統交互的"語音、語音"體驗。 語音助理作者使用語音 SDK`DialogServiceConnector`中創建設備應用程式，以便與使用[自訂命令（預覽）](custom-commands.md)或自動框架[的直線語音](direct-line-speech.md)通道創建的助手進行通信。 這些助手可以使用自訂關鍵字、自訂語音和自訂語音來提供根據您的品牌或產品量身定制的體驗。

**問：我應該使用自訂命令（預覽）還是直接行語音？有什麼區別？**

**答：**[自訂命令（預覽）](custom-commands.md)是一組較低複雜性的工具，用於輕鬆創建和託管非常適合任務完成方案的助理。 [直接線上語音](direct-line-speech.md)提供更豐富、更複雜的功能，可實現可靠的對話方案。 有關詳細資訊[，請參閱助理解決方案的比較](voice-assistants.md#choosing-an-assistant-solution)。

**問：如何開始使用？**

**答：** 從創建自訂命令（預覽）應用程式或基本機器人框架機器人開始的最佳方式。

- [創建自訂命令（預覽）應用程式](quickstart-custom-speech-commands-create-new.md)
- [創建基本機器人框架機器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [將自動程式連接到直線語音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>偵錯

**問：我的頻道機密在哪裡？**

**答：** 如果您已使用"直行語音"的預覽版本，或者正在閱讀相關文檔，則可能需要在"直線語音"頻道註冊頁面上找到一個機密金鑰。 語音 SDK 中的`DialogServiceConfig`v1.7 工廠方法`FromBotSecret`也期望此值。

最新版本的"直線語音"簡化了從設備聯繫機器人的過程。 在頻道註冊頁面上，頂部的下拉將您的直接線路語音訊道註冊與語音資源關聯。 一旦關聯，v1.8 語音 SDK`BotFrameworkConfig::FromSubscription`包括一個工廠方法`DialogServiceConnector`，該方法將配置 以與訂閱關聯的機器人聯繫。

如果仍在將用戶端應用程式從 v1.7 遷移到 v1.8，`DialogServiceConfig::FromBotSecret`則可能會繼續使用非空、非空的值來處理其通道機密參數，例如您使用的前一個機密。 使用與較新的通道註冊關聯的語音訂閱時，將忽略它。 請注意，該值_必須_為非空和非空值，因為在服務端關聯相關之前，將在設備上檢查這些值。

有關更詳細的指南，請參閱流覽頻道註冊的[教程部分](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel)。

**問：連接時出現 401 錯誤，因此沒有任何問題有效。我知道我的語音訂閱金鑰是有效的。這是怎麼回事？**

**答：** 在 Azure 門戶上管理訂閱時，請確保使用的是**語音**資源（Microsoft.CognitiveServices語音服務，"語音"），_而不是_**認知服務**資源（Microsoft.CognitiveServicesAllInOne，"所有認知服務"）。 此外，請檢查[語音助理語音服務區域支援](regions.md#voice-assistants)。

![正確訂閱直線語音](media/voice-assistants/faq-supported-subscription.png "相容語音訂閱的示例")

**問：我從 中收到識別文本`DialogServiceConnector`，但我看到"1011"錯誤，從我的機器人中什麼也看不到。為什麼？**

**答：** 此錯誤表示您的助理和語音助理服務之間的通信問題。

- 對於自訂命令（預覽），請確保發佈自訂命令（預覽）應用程式
- 對於直接線路語音，請確保將[自動程式連接到直接線路語音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)，[將流式處理協定支援添加到](https://aka.ms/botframework/addstreamingprotocolsupport)您的自動程式（具有相關的 Web 通訊端支援），然後檢查您的自動程式是否回應來自該通道的傳入請求。

**問：此代碼仍然不起作用，並且/或者我在使用 時`DialogServiceConnector`收到不同的錯誤。我該怎麼辦？**

**答：** 基於檔的日誌記錄提供了更多的詳細資訊，並有助於加快支援請求。 要啟用此功能，請參閱[如何使用檔日誌記錄](how-to-use-logging.md)。

## <a name="next-steps"></a>後續步驟

- [疑難排解](troubleshooting.md)
- [版本資訊](releasenotes.md)
