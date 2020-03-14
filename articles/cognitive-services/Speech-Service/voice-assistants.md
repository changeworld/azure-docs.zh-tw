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
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369891"
---
# <a name="what-is-a-voice-assistant"></a>什麼是語音助理？

語音助理使用語音服務，可讓開發人員為其應用程式和體驗建立自然、人類類似的對話介面。

語音助理服務可讓您在裝置與小幫手執行之間快速可靠地進行互動，使用（1） Bot Framework 的 Direct Line 語音通道，或（2）整合式自訂命令（預覽）服務來完成工作。

應用程式會使用語音軟體發展工具組（SDK）連接到語音助理服務。

   ![語音助理協調流程服務流程的概念圖](media/voice-assistants/overview.png "語音助理流程")

## <a name="choosing-an-assistant-solution"></a>選擇助理解決方案

建立語音助理的第一個步驟是決定應執行的動作。 語音服務提供多個互補的解決方案來製作您的助理互動。 無論您是否想要有 Bot Framework 的[Direct Line 語音](direct-line-speech.md)通道所提供的彈性和多功能性，或簡單的[自訂命令（預覽）](custom-commands.md) ，都可以選擇適當的工具，讓您開始使用。

| 若要... | 然後考慮 。 | 例如 。 |
|-------------------|------------------|----------------|
|具有強大技能整合和完全部署控制的開放式交談 | Bot Framework 的[Direct Line 語音](direct-line-speech.md)頻道 | <ul><li>「我需要前往西雅圖」</li><li>「我可以訂購哪種比薩餅？」</li></ul>
|透過簡化的撰寫和裝載來進行命令與控制或工作導向的交談 | [自訂命令（預覽）](custom-commands.md) | <ul><li>「開啟額外負荷燈」</li><li>「讓它成為5度的暖」</ul>

如果您還不確定想要協助程式處理的內容，建議[Direct Line 語音](direct-line-speech.md)做為最佳的預設選擇。 它可以與一組豐富的工具和撰寫協助工具整合，例如[虛擬小幫手解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)，以及用來建立通用模式和使用現有知識來源的[QnA Maker 服務](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview)。

[自訂命令（預覽）](custom-commands.md)提供簡化的撰寫和裝載體驗，特別針對自然語言命令和控制項案例量身打造。

   ![助理解決方案的比較](media/voice-assistants/assistant-solution-comparison.png "助理解決方案的比較")

## <a name="core-features"></a>核心功能

無論您選擇[Direct Line 語音](direct-line-speech.md)或[自訂命令（預覽）](custom-commands.md)來建立助理互動，都可以使用一組豐富的自訂功能，針對您的品牌、產品和特性自訂您的小幫手。

| 類別 | 特性 |
|----------|----------|
|[Custom 關鍵字](speech-devices-sdk-create-kws.md) | 使用者可以使用自訂關鍵字（例如 "嗨 Contoso"）來開始與助理交談。 應用程式會使用語音 SDK 中的自訂關鍵字引擎來執行這項工作，您可以使用[這裡產生](speech-devices-sdk-create-kws.md)的自訂關鍵字來設定。 語音助理可以使用服務端關鍵字驗證來改善關鍵字啟用的精確度（與裝置本身的比較）。
|[語音轉換文字](speech-to-text.md) | 語音助理會使用語音服務中的語音轉換文字[，](speech-to-text.md)將即時音訊轉換成已辨識的文字。 這項文字已轉譯，可供您的小幫手和用戶端應用程式使用。
|[文字轉換語音](text-to-speech.md) | 來自您小幫手的文字回應會使用語音服務中的[文字轉換語音](text-to-speech.md)進行合成。 然後，您的用戶端應用程式可以使用此合成作為音訊串流。 Microsoft 提供建立您自己的自訂高品質神經 TTS 語音的功能，讓您的品牌能夠發聲。 若要深入瞭解，請[洽詢我們](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>開始使用語音助理

我們提供的快速入門是為了讓您在10分鐘內執行程式碼而設計的。 此表格包含依語言組織的語音助理快速入門清單。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| C#，UWP | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [瀏覽](https://aka.ms/csspeech/javaref) |
| Java | Android | [瀏覽](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上取得用來建立語音助理的範例程式碼。 這些範例涵蓋了用來以數種熱門的程式設計語言連接到您的小幫手的用戶端應用程式。

* [語音助理範例 (SDK)](https://aka.ms/csspeech/samples) \(英文\)
* [教學課程：使用語音 SDK 語音啟用您的助理C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>教學課程

本教學課程說明如何[使用語音 SDK 和 Direct Line 語音通道](tutorial-voice-enable-your-bot-speech-sdk.md)，為您的小幫手提供語音。

## <a name="customization"></a>自訂

使用語音服務所建立的語音助理可以使用適用于[語音轉換文字](speech-to-text.md)、[文字轉換語音](text-to-speech.md)及[自訂關鍵字選取](speech-devices-sdk-create-kws.md)範圍的完整自訂選項。

> [!NOTE]
> 自訂選項會因語言/地區設定而有所不同（請參閱[支援的語言](supported-languages.md)）。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [深入瞭解自訂命令（預覽）](custom-commands.md)
* [深入瞭解 Direct Line Speech](direct-line-speech.md)
