---
title: 語音助理 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音軟體發展工具組 （SDK） 的語音助理的功能、功能和限制概述。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369891"
---
# <a name="what-is-a-voice-assistant"></a>什麼是語音助理？

使用語音服務的語音助理使開發人員能夠為其應用程式和體驗創建自然、類似人類的對話介面。

語音助理服務在設備和輔助實現之間提供快速、可靠的交互，該實現使用 （1） Bot Framework 的直線語音通道或 （2） 集成的自訂命令（預覽）服務來完成任務。

應用程式使用語音軟體發展工具組 （SDK） 連接到語音助理服務。

   ![語音助理業務流程的概念圖](media/voice-assistants/overview.png "語音助理流")

## <a name="choosing-an-assistant-solution"></a>選擇助理解決方案

創建語音助理的第一步是決定它應該做什麼。 語音服務提供多個互補解決方案，用於製作您的助理交互。 無論您是想要機器人框架的[直線語音](direct-line-speech.md)通道提供的靈活性和多功能性，還是希望為簡單方案提供[自訂命令（預覽）](custom-commands.md)的簡單性，選擇正確的工具都將讓您入門。

| 若要... | 然後考慮... | 例如... |
|-------------------|------------------|----------------|
|具有強大技能集成和全面部署控制的開放式對話 | 機器人框架的[直線語音](direct-line-speech.md)通道 | <ul><li>"我需要去西雅圖"</li><li>"我可以點什麼樣的披薩？</li></ul>
|命令和控制或面向任務的對話，簡化創作和託管 | [自訂命令（預覽）](custom-commands.md) | <ul><li>"打開頂燈"</li><li>"讓它溫暖5度"</ul>

如果您還不確定您希望您的助手處理什麼，我們建議您將[直接線上語音](direct-line-speech.md)作為最佳預設選擇。 它提供了與一組豐富的工具和創作輔助工具（如[虛擬助理解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)以及[QnA Maker 服務](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview)）的集成，以構建通用模式並使用現有知識源。

[自訂命令（預覽版）](custom-commands.md)提供了專為自然語言命令和控制方案量身定制的簡化的創作和託管體驗。

   ![助理解決方案的比較](media/voice-assistants/assistant-solution-comparison.png "助理解決方案的比較")

## <a name="core-features"></a>核心功能

無論您選擇["直線語音"](direct-line-speech.md)還是["自訂命令（預覽"）](custom-commands.md)來創建助理交互，您都可以使用一組豐富的自訂功能來自訂您的助手，以滿足您的品牌、產品和個性。

| 類別 | 特性 |
|----------|----------|
|[Custom 關鍵字](speech-devices-sdk-create-kws.md) | 使用者可以使用自訂關鍵字（如"嘿 Contoso"）開始與助手對話。 應用使用語音 SDK 中的自訂關鍵字引擎進行此用，該引擎可以使用自訂關鍵字進行配置[，您可以在此處生成](speech-devices-sdk-create-kws.md)該關鍵字。 語音助理可以使用服務端關鍵字驗證來提高關鍵字啟動的準確性（與單獨使用設備相比）。
|[語音到文本](speech-to-text.md) | 語音助理使用語音服務[中的語音到文本](speech-to-text.md)將即時音訊轉換為識別的文本。 此文本可供助理實現和用戶端應用程式使用，因為它已轉錄。
|[將文字轉換成語音](text-to-speech.md) | 使用語音服務中的["文本到語音"](text-to-speech.md)來合成助手的文本回應。 然後，此合成將作為音訊流提供給用戶端應用程式。 Microsoft 提供構建您自己的自訂、高品質神經 TTS 語音的能力，為您的品牌提供語音。 要瞭解更多資訊，[請聯繫我們](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>使用語音助理開始

我們提供快速入門，旨在讓您在 10 分鐘內運行代碼。 此表包括按語言組織的語音助理快速入門清單。

| 快速入門 | Platform | 應用程式開發介面參考 |
|------------|----------|---------------|
| C#，UWP | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [瀏覽](https://aka.ms/csspeech/javaref) |
| Java | Android | [瀏覽](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

在 GitHub 上提供了用於創建語音助理的示例代碼。 這些示例涵蓋用戶端應用程式，用於以幾種流行的程式設計語言連接到您的助手。

* [語音助理範例 (SDK)](https://aka.ms/csspeech/samples) \(英文\)
* [教程：語音啟用您的助手與語音 SDK， C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>教學課程

有關如何[使用語音 SDK 和直撥語音通道使助手語音啟用的](tutorial-voice-enable-your-bot-speech-sdk.md)教程。

## <a name="customization"></a>自訂

使用語音服務構建的語音助手可以使用用於[語音到文本](speech-to-text.md)、[文本到語音](text-to-speech.md)和[自訂關鍵字選擇](speech-devices-sdk-create-kws.md)的完整自訂選項。

> [!NOTE]
> 自訂選項因語言/地區設定而異（請參閱[支援的語言](supported-languages.md)）。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure 機器人服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [瞭解有關自訂命令（預覽）的更多](custom-commands.md)
* [瞭解有關直線語音的更多內容](direct-line-speech.md)
