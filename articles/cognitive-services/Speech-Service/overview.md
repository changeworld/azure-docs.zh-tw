---
title: 什麼是語音服務？
titleSuffix: Azure Cognitive Services
description: 語音服務會將語音轉文字、文字轉語音及語音翻譯整合至單一 Azure 訂用帳戶。 使用語音 SDK、語音裝置 SDK 或 REST API 將語音新增至您的應用程式、工具和裝置。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401030"
---
# <a name="what-is-the-speech-service"></a>什麼是語音服務？

語音服務會將語音轉文字、文字轉語音及語音翻譯整合至單一 Azure 訂用帳戶。 藉由[語音 SDK](speech-sdk-reference.md)、[語音裝置 SDK](https://aka.ms/sdsdk-quickstart) 或 [REST API](rest-apis.md)，可輕易地透過語音來啟用您的應用程式、工具和裝置。

> [!IMPORTANT]
> 語音服務已取代 Bing 語音 API 和翻譯工具語音。 如需移轉說明，請參閱 [操作指南] > [移轉]  。

語音服務由以下功能組成。 請使用此資料表中的連結，深入了解每項功能的常見使用案例，或瀏覽 API 參考。

| 服務 | 功能 | 描述 | SDK | REST |
|---------|---------|-------------|-----|------|
| [語音轉文字](speech-to-text.md) | 即時語音轉換文字 | 語音轉文字會即時地將音訊串流或本地檔案轉譯或翻譯成文字，以便您的應用程式、工具或裝置使用或顯示。 若搭配 [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) 使用語音轉文字，即可從轉譯的語音衍生使用者意圖，以及根據語音命令執行動作。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [批次語音轉換文字](batch-transcription.md) | 批次語音轉換文字可針對 Azure Blob 儲存體中的大量語音音訊資料，啟用非同步語音轉換文字轉譯。 除了將語音音訊轉換為文字之外，批次語音轉換文字也可以進行自動分段標記和情感分析。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| | [多裝置交談](multi-device-conversation.md) | 透過便利的轉譯和翻譯支援，在一個對話中連接多個裝置或用戶端以傳送以語音或文字為基礎的訊息| 是 | 否 |
| | [對話轉譯](conversation-transcription-service.md) | 啟用即時語音辨識、說話者識別和自動分段標記功能。 非常適合利用辨識說話者的能力來轉譯面對面會議。 | 是 | 否 |
| | [建立自訂語音模型](#customize-your-speech-experience) | 如果您在獨特的環境中使用語音轉文字進行辨識及轉譯，您可以建立並定型自訂原音、語言和發音模型，以處理環境噪音或業界專有詞彙。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| [文字轉換語音](text-to-speech.md) | 文字轉換語音 | 文字轉語音會使用[語音合成標記語言 (SSML)](speech-synthesis-markup.md) 將輸入文字轉換為仿真人的合成語音。 可選擇標準語音和類神經語音 (請參閱[語言支援](language-support.md))。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [建立自訂語音](#customize-your-speech-experience) | 建立您品牌或產品專有的自訂聲音音調。 | 否 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [語音翻譯](speech-translation.md) | 語音翻譯 | 語音翻譯可讓您在應用程式、工具和裝置上使用即時且多語言的語音翻譯。 此服務可用於語音轉語音及語音轉文字翻譯。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | 否 |
| [語音助理](voice-assistants.md) | 語音助理 | 使用語音服務的語音助理能賦予開發人員建立自然、擬人的對話介面，供其應用程式和體驗之用。 語音助理服務能在裝置和助理實作之間提供迅速且可靠的互動；該助理實作會使用 Bot Framework 的 Direct Line 語音頻道，或是整合的自訂命令 (預覽) 服務來完成工作。 | [是](voice-assistants.md) | 否 |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>試用語音服務

我們以最受歡迎的程式設計語言提供快速入門，目的是讓您能在 10 分鐘內執行程式碼。 此資料表包含每項功能最受歡迎的快速入門。 您可以使用左側導覽列來瀏覽其他語言及平台。

| 語音轉文字 (SDK) | 文字轉語音 (SDK) | 翻譯 (SDK) |
|----------------------|----------------------|-------------------|
| [辨識來自音訊檔案的語音](quickstarts/speech-to-text-from-file.md) | [將語音合成至音訊檔案](quickstarts/text-to-speech-audio-file.md) | [翻譯語音轉換文字](quickstarts/translate-speech-to-text.md) |
| [透過麥克風辨識語音](quickstarts/speech-to-text-from-microphone.md) | [將語音合成至喇叭](quickstarts/text-to-speech.md) | [將語音翻譯成多種目標語言](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [辨識儲存在 Blob 儲存體中的語音](quickstarts/from-blob.md) | [適用於長格式音訊的非同步合成](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [翻譯語音轉換語音](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> 語音轉換文字和文字轉換語音也都具備 REST 端點和相關聯的快速入門。

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

- [教學課程：使用語音 SDK 和 LUIS 從語音辨識意圖 (C#)](how-to-recognize-intents-from-speech-csharp.md)
- [教學課程：使用語音 SDK 和 C# 透過聲音啟用 Bot](tutorial-voice-enable-your-bot-speech-sdk.md)
- [教學課程：建置 Flask 應用程式以翻譯文字、分析情緒並將翻譯的文字合成語音，REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>取得範例程式碼

您可以在 GitHub 上取得語音服務的程式碼範例。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。 使用下列連結來檢視 SDK 和 REST 範例：

- [語音轉文字、文字轉語音及語音翻譯範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [語音助理範例 (SDK)](https://aka.ms/csspeech/samples) \(英文\)

## <a name="customize-your-speech-experience"></a>自訂語音體驗

語音服務可順利地與內建模型搭配使用，不過，您可以進一步自訂及調整體驗，以搭配您的產品或環境。 從原音模型調整到專屬於自身品牌的獨特聲音音調，都是自訂選項的範圍。

| 語音服務 | 平台 | 描述 |
| -------------- | -------- | ----------- |
| 語音轉文字 | [客製化的語音](https://aka.ms/customspeech) | 依據您的需求和可用的資料，自訂語音辨識模型。 克服像是語音模式、詞彙及背景雜音等語音辨識的阻礙。 |
| 文字轉語音 | [自訂語音](https://aka.ms/customvoice) | 使用您的談話資料，為您的文字轉語音應用程式建置可辨識且獨一無二的語音。 您可以藉由調整一組語音參數，來進一步微調語音輸出。 |

## <a name="reference-docs"></a>參考文件

- [語音 SDK](speech-sdk-reference.md)
- [語音裝置 SDK](speech-devices-sdk.md)
- [REST API：語音轉文字](rest-speech-to-text.md)
- [REST API：文字轉語音](rest-text-to-speech.md)
- [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
