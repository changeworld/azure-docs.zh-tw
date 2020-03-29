---
title: 語音到文本 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音到文本功能可即時轉錄音訊流到文本中。 您的應用程式、工具或設備可以使用此文本輸入、顯示和操作。 此服務可與文本到語音（語音合成）和語音翻譯功能無縫配合。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 2854f4e8d91164c8ae1f35761d6f605cae725245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052640"
---
# <a name="what-is-speech-to-text"></a>什麼是語音轉換文字？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

語音服務中的語音到文本（也稱為語音辨識）支援將音訊流即時轉錄到文本中。 您的應用程式、工具或設備可以使用此文本、顯示和對此文本執行操作作為命令輸入。 此服務由 Microsoft 用於 Cortana 和 Office 產品的相同識別技術提供支援。 它與<a href="./speech-translation.md" target="_blank">翻譯<span class="docon docon-navigate-external x-hidden-focus"></span></a>和<a href="./text-to-speech.md" target="_blank">文本到語音<span class="docon docon-navigate-external x-hidden-focus"></span>服務</a>產品無縫配合。 有關可用語音到文本語言的完整清單，請參閱[支援的語言](language-support.md#speech-to-text)。

語音到文本服務預設使用通用語言模型。 此模型使用 Microsoft 擁有的資料進行了培訓，並部署在雲中。 它是對話和聽寫方案的最佳選擇。 在獨特的環境中使用語音到文本進行識別和轉錄時，您可以創建並訓練自訂聲學、語言和發音模型。 自訂有助於解決環境噪音或行業特定的詞彙。

> [!NOTE]
> 必應演講于2019年10月15日退役。 如果您的應用程式、工具或產品正在使用必應語音 API，我們創建了指南以説明您遷移到語音服務。
> - [從必應語音遷移到語音服務](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>開始使用語音到文本

語音到文本服務可通過[語音 SDK](speech-sdk.md)獲得。 有多種常見方案可作為快速入門，使用各種語言和平臺：

 - [快速入門：通過麥克風輸入識別語音](quickstarts/speech-to-text-from-microphone.md)
 - [快速入門：從檔中識別語音](quickstarts/speech-to-text-from-file.md)
 - [快速入門：識別存儲在 blob 存儲中的語音](quickstarts/from-blob.md)

如果您更喜歡使用語音到文本 REST 服務，請參閱 REST [API](rest-speech-to-text.md)。

## <a name="tutorials-and-sample-code"></a>教程和示例代碼

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

- [教程：使用 C 使用語音 SDK 和 LUIS 識別語音意圖#](how-to-recognize-intents-from-speech-csharp.md)

在 GitHub 上提供了語音 SDK 的示例代碼。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。

- [語音到文本示例 （SDK）](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>自訂

除了標準的語音服務模型外，您還可以創建自訂模型。 自訂有助於克服語音辨識障礙，如說話風格、詞彙和背景雜音，請參閱[自訂語音](how-to-custom-speech.md)。 自訂選項因語言/地區設定而異，請參閱[支援的語言](supported-languages.md)以驗證支援。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>後續步驟

- [免費取得語音服務的訂用帳戶金鑰](get-started.md)
- [取得語音 SDK](speech-sdk.md)
