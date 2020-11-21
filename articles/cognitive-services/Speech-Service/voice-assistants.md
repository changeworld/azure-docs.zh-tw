---
title: 語音助理-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用語音軟體發展工具組 (SDK) 的語音助理功能、功能及限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: aa885b1dcff59ec1b89d9a0bcf4ac5578cb5e8ea
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024903"
---
# <a name="what-is-a-voice-assistant"></a>什麼是語音助理？

使用語音服務的語音助理可讓開發人員為其應用程式和體驗建立自然、類似人類的對話式介面。

語音助理服務可在裝置和助理執行之間提供快速、可靠的互動，使用 (1) [Direct Line 語音](direct-line-speech.md) (透過 Azure bot service) 將語音功能新增至您的 bot，或 (2) 語音命令案例的自訂命令。

## <a name="choosing-an-assistant-solution"></a>選擇助理方案

建立語音助理的第一個步驟是決定應採取的動作。 語音服務提供多個互補的解決方案，可讓您製作助理互動。 您可以使用 Azure Bot Service 搭配 [Direct Line 語音](direct-line-speech.md) 通道，將語音輸入和語音輸出功能新增至您的彈性且多功能的 bot，或利用撰寫 [自訂命令](custom-commands.md) 應用程式以進行簡單的語音命令案例。

| 若要... | 然後考慮 .。。 | 例如... |
|-------------------|------------------|----------------|
|具有穩固技能整合和完整部署控制的開放式對話 | 具有 [Direct Line 語音](direct-line-speech.md) 通道的 Azure bot Service bot | <ul><li>「我需要前往西雅圖」</li><li>「我可以訂購哪種比薩？」</li></ul>
|使用簡化撰寫和裝載的語音命令或簡單的工作導向對話 | [自訂命令](custom-commands.md) | <ul><li>「開啟額外負荷燈」</li><li>「讓5度更暖」</li><li>其他[可用](https://speech.microsoft.com/customcommands)的範例</li></ul>

如果您還不確定想要處理的小幫手，建議您 [Direct Line 語音](direct-line-speech.md) 作為最佳的預設選項。 它提供了一組豐富的工具與撰寫輔助工具的整合，例如 [虛擬助理解決方案和企業範本](/azure/bot-service/bot-builder-enterprise-template-overview) ，以及用來建立一般模式並使用現有知識來源的 [QnA Maker 服務](../qnamaker/overview/overview.md) 。

[自訂命令](custom-commands.md) 可讓您輕鬆地建立針對語音優先互動體驗優化的豐富語音命令應用程式。 它提供統一的撰寫體驗、自動化的裝載模型，以及較低的複雜度，協助您專注于為語音命令的案例建立最佳解決方案。

   ![助理解決方案的比較](media/voice-assistants/assistant-solution-comparison.png "助理解決方案的比較")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>使用語音 SDK 建立語音助理的參考架構

   ![語音助理協調流程服務流程的概念圖](media/voice-assistants/overview.png "語音助理流程")

## <a name="core-features"></a>核心功能

無論您選擇 [Direct Line 語音](direct-line-speech.md) 或 [自訂命令](custom-commands.md) 來建立您的助理互動，您都可以使用一組豐富的自訂功能，自訂您的小幫手與品牌、產品和特質。

| 類別 | 功能 |
|----------|----------|
|[自訂關鍵字](./custom-keyword-basics.md) | 使用者可以使用自訂關鍵字（例如 "嗨 Contoso"）開始與助理進行交談。 應用程式會在語音 SDK 中使用自訂關鍵字引擎來進行這項設定， [您](./custom-keyword-basics.md)可以在此使用自訂關鍵字來設定。 語音助理可以使用服務端關鍵字驗證來改善關鍵字啟用 (與裝置) 的精確度。
|[語音轉換文字](speech-to-text.md) | 語音助理使用語音服務的 [語音轉換文字](speech-to-text.md) ，將即時音訊轉換成可辨識的文字。 在您的助理執行和用戶端應用程式中，都可以使用此文字轉譯。
|[文字轉換語音](text-to-speech.md) | 來自您助理的文字回應會使用語音服務的 [文字轉換語音](text-to-speech.md) 合成。 然後，您的用戶端應用程式可以使用此合成作為音訊串流。 Microsoft 提供的功能可讓您建立自己的自訂高品質類神經 TTS 語音，以對您的品牌提供語音。 若要深入瞭解，請 [洽詢我們](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>開始使用語音助理

我們提供的快速入門設計可讓您在10分鐘內執行程式碼。 下表包含依語言組織的語音助理快速入門清單。

* [快速入門：使用 Direct Line 語音建立自訂語音助理](quickstarts/voice-assistants.md)
* [快速入門：使用自訂命令建立語音命令應用程式](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>範例程式碼和教學課程

您可以在 GitHub 上取得用來建立語音助理的範例程式碼。 這些範例涵蓋了用戶端應用程式，可讓您以數種熱門的程式設計語言連接到您的助理。

* [GitHub 上的語音助理範例](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [教學課程：使用 Azure Bot Service 搭配 c # 語音 SDK 建立小幫手的語音](tutorial-voice-enable-your-bot-speech-sdk.md)
* [教學課程：使用簡單的語音命令來建立自訂命令應用程式](how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="customization"></a>自訂

使用 Azure 語音服務建立的語音助理可以使用完整的自訂選項範圍。

* [客製化的語音](how-to-custom-speech.md)
* [自訂語音](how-to-custom-voice.md)
* [自訂關鍵字](custom-keyword-overview.md)

> [!NOTE]
> 自訂選項會因語言/地區設定而異 (請參閱 [支援的語言](language-support.md)) 。

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](overview.md#try-the-speech-service-for-free)
* [深入瞭解自訂命令](custom-commands.md)
* [深入瞭解 Direct Line 語音](direct-line-speech.md)
* [取得語音 SDK](speech-sdk.md)