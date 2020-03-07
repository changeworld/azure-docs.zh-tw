---
title: 語音轉換文字-語音服務
titleSuffix: Azure Cognitive Services
description: 語音轉換文字功能可將音訊串流的即時轉譯為文字。 您的應用程式、工具或裝置可以對此文字輸入使用、顯示和採取動作。 此服務可與文字轉換語音（語音合成），以及語音翻譯功能緊密搭配運作。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388310"
---
# <a name="what-is-speech-to-text"></a>什麼是語音轉換文字？

語音服務的語音轉換文字（也稱為語音辨識）可將音訊串流的即時轉譯為文字。 您的應用程式、工具或裝置可以針對此文字使用、顯示和採取動作作為命令輸入。 這項服務是由 Microsoft 用於 Cortana 和 Office 產品的相同辨識技術所支援。 它可順暢地與<a href="./speech-translation.md" target="_blank">翻譯<span class="docon docon-navigate-external x-hidden-focus"></span> </a>和<a href="./text-to-speech.md" target="_blank">文字到語音<span class="docon docon-navigate-external x-hidden-focus"></span> </a>轉換服務供應專案搭配運作。 如需可用語音轉換文字語言的完整清單，請參閱[支援的語言](language-support.md#speech-to-text)。

語音轉換文字服務預設會使用通用語言模型。 此模型已使用 Microsoft 所擁有的資料進行定型，並已部署在雲端中。 這對於對話式和聽寫案例而言是最理想的。 在獨特的環境中使用語音轉換文字進行辨識和轉譯時，您可以建立及定型自訂聲場、語言和發音模型。 自訂有助於解決環境雜訊或產業特定詞彙。

> [!NOTE]
> Bing 語音已于2019年10月15日解除委任。 如果您的應用程式、工具或產品使用 Bing 語音 Api 或自訂語音，我們建立了指南，協助您遷移至語音服務。
> - [從 Bing 語音遷移至語音服務](how-to-migrate-from-bing-speech.md)
> - [從自訂語音遷移至語音服務](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>開始使用語音轉換文字

語音轉換文字服務可透過[語音 SDK](speech-sdk.md)取得。 在各種不同的語言和平臺中，有幾個常見的案例可做為快速入門：

 - [快速入門：使用麥克風輸入來辨識語音](quickstarts/speech-to-text-from-microphone.md)
 - [快速入門：從檔案辨識語音](quickstarts/speech-to-text-from-file.md)
 - [快速入門：辨識儲存在 blob 儲存體中的語音](quickstarts/from-blob.md)

如果您想要使用語音轉換文字 REST 服務，請參閱[Rest api](rest-speech-to-text.md)。

## <a name="tutorials-and-sample-code"></a>教學課程和範例程式碼

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

- [教學課程：使用語音 SDK 和 LUIS 從語音辨識意圖C#](how-to-recognize-intents-from-speech-csharp.md)

語音 SDK 的範例程式碼可在 GitHub 上取得。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。

- [語音轉換文字範例（SDK）](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>自訂

除了標準語音服務模型之外，您還可以建立自訂模型。 自訂有助於克服語音辨識的阻礙，例如說話風格、詞彙和背景雜音，請參閱[自訂語音](how-to-custom-speech.md)。 自訂選項會因語言/地區設定而異，請參閱[支援的語言](supported-languages.md)以確認支援。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>後續步驟

- [免費取得語音服務的訂用帳戶金鑰](get-started.md)
- [取得語音 SDK](speech-sdk.md)
