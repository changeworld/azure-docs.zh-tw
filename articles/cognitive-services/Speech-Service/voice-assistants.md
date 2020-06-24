---
title: 語音助理-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用語音軟體發展工具組（SDK）的語音助理的功能、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 18a9de8a2eaa2364e89e831db8dab5cbbb061c10
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85299240"
---
# <a name="what-is-a-voice-assistant"></a>什麼是語音助理？

語音助理使用語音服務，可讓開發人員為其應用程式和體驗建立自然、人類類似的對話介面。

語音助理服務可在裝置與助理執行之間提供快速、可靠的互動，使用（1） [Direct Line 語音](direct-line-speech.md)（透過 Azure Bot 服務）將語音功能新增至您的 bot，或（2）語音命令案例的自訂命令。

## <a name="choosing-an-assistant-solution"></a>選擇助理解決方案

建立語音助理的第一個步驟是決定應執行的動作。 語音服務提供多個互補的解決方案來製作您的助理互動。 您可以使用 Azure Bot Service 搭配[Direct Line Speech](direct-line-speech.md)通道，將語音輸入和語音輸出功能新增至彈性且多樣化的 bot，或利用撰寫[自訂命令](custom-commands.md)應用程式來進行直接語音命令案例的簡單性。

| 若要... | 然後考慮 .。。 | 例如 .。。 |
|-------------------|------------------|----------------|
|具有強大技能整合和完全部署控制的開放式交談 | 具有[Direct Line 語音](direct-line-speech.md)通道的 Azure bot Service bot | <ul><li>「我需要前往西雅圖」</li><li>「我可以訂購哪種比薩餅？」</li></ul>
|使用簡化的撰寫和裝載進行語音命令或簡單的工作導向交談 | [自訂命令](custom-commands.md) | <ul><li>「開啟額外負荷燈」</li><li>「讓它成為5度的暖」</li><li>[這裡提供](https://speech.microsoft.com/customcommands)其他範例</li></ul>

如果您還不確定想要協助程式處理的內容，建議[Direct Line 語音](direct-line-speech.md)做為最佳的預設選擇。 它可以與一組豐富的工具和撰寫協助工具整合，例如[虛擬小幫手解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)，以及用來建立通用模式和使用現有知識來源的[QnA Maker 服務](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview)。

[自訂命令](custom-commands.md)可讓您輕鬆地建立針對語音優先互動體驗優化的豐富語音命令應用程式。 它提供統一的撰寫體驗、自動裝載模型，以及相對較低的複雜性，協助您專注于為語音命令案例建立最佳解決方案。

   ![助理解決方案的比較](media/voice-assistants/assistant-solution-comparison.png "助理解決方案的比較")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>使用語音 SDK 建立語音助理的參考架構

   ![語音助理協調流程服務流程的概念圖](media/voice-assistants/overview.png "語音助理流程")

## <a name="core-features"></a>核心功能

無論您選擇[Direct Line 語音](direct-line-speech.md)或[自訂命令](custom-commands.md)來建立小幫手互動，都可以使用一組豐富的自訂功能，針對您的品牌、產品和特性自訂您的小幫手。

| 類別 | 特性 |
|----------|----------|
|[Custom 關鍵字](speech-devices-sdk-create-kws.md) | 使用者可以使用自訂關鍵字（例如 "嗨 Contoso"）來開始與助理交談。 應用程式會使用語音 SDK 中的自訂關鍵字引擎來執行這項工作，您可以使用[這裡產生](speech-devices-sdk-create-kws.md)的自訂關鍵字來設定。 語音助理可以使用服務端關鍵字驗證來改善關鍵字啟用的精確度（與裝置本身的比較）。
|[語音轉換文字](speech-to-text.md) | 語音助理會使用語音服務中的語音轉換文字[，](speech-to-text.md)將即時音訊轉換成已辨識的文字。 這項文字已轉譯，可供您的小幫手和用戶端應用程式使用。
|[文字轉換語音](text-to-speech.md) | 來自您小幫手的文字回應會使用語音服務中的[文字轉換語音](text-to-speech.md)進行合成。 然後，您的用戶端應用程式可以使用此合成作為音訊串流。 Microsoft 提供建立您自己的自訂高品質神經 TTS 語音的功能，讓您的品牌能夠發聲。 若要深入瞭解，請[洽詢我們](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>開始使用語音助理

我們提供的快速入門是為了讓您在10分鐘內執行程式碼而設計的。 此表格包含依語言組織的語音助理快速入門清單。

* [快速入門：使用 Direct Line 語音來建立自訂語音助理](quickstarts/voice-assistants.md)
* [快速入門：使用自訂命令建立語音命令應用程式](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>範例程式碼和教學課程

您可以在 GitHub 上取得用來建立語音助理的範例程式碼。 這些範例涵蓋了用來以數種熱門的程式設計語言連接到您的小幫手的用戶端應用程式。

* [GitHub 上的語音助理範例](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [教學課程：使用 Azure Bot Service 搭配 c # 語音 SDK 來啟用您的小幫手](tutorial-voice-enable-your-bot-speech-sdk.md)
* [教學課程：使用簡單的語音命令來建立自訂命令應用程式](how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="customization"></a>自訂

使用 Azure 語音服務建立的語音助理可以使用完整範圍的自訂選項。

* [客製化的語音](how-to-custom-speech.md)
* [自訂語音](how-to-custom-voice.md)
* [自訂關鍵字](custom-keyword-overview.md)

> [!NOTE]
> 自訂選項會因語言/地區設定而有所不同（請參閱[支援的語言](language-support.md)）。

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [深入瞭解自訂命令](custom-commands.md)
* [深入瞭解 Direct Line Speech](direct-line-speech.md)
* [取得語音 SDK](speech-sdk.md)