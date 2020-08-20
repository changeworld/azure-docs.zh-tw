---
title: 語音轉換文字-語音服務
titleSuffix: Azure Cognitive Services
description: 語音轉換文字功能可將音訊串流的即時轉譯為文字。 您的應用程式、工具或裝置可以針對此文字輸入取用、顯示及採取動作。 這種服務可與文字轉換語音 (語音合成) 和語音翻譯功能緊密搭配運作。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 90a5ec8c58865c3aa1b210db51b9ffeb7169d641
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640407"
---
# <a name="what-is-speech-to-text"></a>什麼是語音轉換文字？

語音服務的語音轉換文字（也稱為語音辨識）可讓您即時將音訊串流轉譯為文字。 您的應用程式、工具或裝置可以對此文字使用、顯示和採取動作作為命令輸入。 這項服務是由 Microsoft 用於 Cortana 和 Office 產品的相同辨識技術所提供。 它可順暢地與<a href="./speech-translation.md" target="_blank">翻譯 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>和<a href="./text-to-speech.md" target="_blank">文字轉換語音 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>服務供應專案搭配使用。 如需可用語音轉換文字語言的完整清單，請參閱 [支援的語言](language-support.md#speech-to-text)。

語音轉換文字服務預設為使用通用語言模型。 此模型已使用 Microsoft 所擁有的資料進行定型，並已部署在雲端中。 最適合對話和聽寫案例。 在獨特的環境中使用語音轉換文字進行辨識和轉譯時，您可以建立及定型自訂聲場、語言和發音模型。 自訂有助於解決環境雜訊或產業特定詞彙。

使用額外的參考文字做為輸入時，語音轉換文字服務也可讓 [發音評定](rest-speech-to-text.md#pronunciation-assessment-parameters) 功能評估語音發音，並為說話者提供有關說話音訊精確度和流暢度的意見反應。 使用發音評定，語言學習工具可實務、取得即時意見反應，並改善其發音，讓他們可以安心地說話並提供意見。 教育工作者可以使用此功能來即時評估多個喇叭的發音。 此功能目前支援美式英文，並且與專家所進行的語音評量相互關聯。

> [!NOTE]
> Bing 語音已于2019年10月15日解除委任。 如果您的應用程式、工具或產品使用 Bing 語音 Api，我們已建立可協助您遷移至語音服務的指南。
> - [從 Bing 語音遷移至語音服務](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started-with-speech-to-text"></a>開始使用語音轉文字

語音轉換文字服務可透過 [語音 SDK](speech-sdk.md)取得。 在各種語言和平臺中，有幾個常見的案例可作為快速入門：

 - [快速入門：使用麥克風輸入辨識語音](quickstarts/speech-to-text-from-microphone.md)
 - [快速入門：從檔案辨識語音](quickstarts/speech-to-text-from-file.md)
 - [快速入門：辨識儲存在 blob 儲存體中的語音](quickstarts/from-blob.md)

如果您想要使用語音轉換文字 REST 服務，請參閱 [REST api](rest-speech-to-text.md)。

 - [快速入門：使用參考輸入的發音評定](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>教學課程和範例程式碼

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

- [教學課程：使用 C 以語音 SDK 和 LUIS 從語音辨識意圖#](how-to-recognize-intents-from-speech-csharp.md)

您可以在 GitHub 上取得適用于語音 SDK 的範例程式碼。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。

- [ (SDK) 的語音轉換文字範例 ](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [ (REST) 的發音評量範例 ](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>自訂

除了標準語音服務模型之外，您還可以建立自訂模型。 自訂有助於克服語音辨識的阻礙，例如說話風格、詞彙和背景雜音，請參閱 [自訂語音](how-to-custom-speech.md)。 自訂選項會因語言/地區設定而異，請參閱 [支援的語言](supported-languages.md) 以驗證支援。

## <a name="batch-transcription"></a>批次轉譯

批次轉譯是一組 REST API 作業，可讓您在儲存體中轉譯大量音訊。 您可以使用共用存取簽章來指向音訊檔案 (SAS) URI，並以非同步方式接收轉譯結果。 如需如何使用批次轉譯 API 的詳細資訊，請參閱 how [to](batch-transcription.md) 。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>後續步驟

- [免費取得語音服務的訂用帳戶金鑰](get-started.md)
- [取得語音 SDK](speech-sdk.md)
