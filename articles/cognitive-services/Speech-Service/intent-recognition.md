---
title: 意圖辨識總覽-語音服務
titleSuffix: Azure Cognitive Services
description: 意圖辨識可讓您辨識預先定義的使用者目標。 本文概述意圖辨識服務的優點和功能。
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: 意圖辨識
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018696"
---
# <a name="what-is-intent-recognition"></a>什麼是意圖辨識？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

在此總覽中，您將瞭解意圖辨識的優點和功能。 認知服務語音 SDK 會與 Language Understanding Intelligent Service (LUIS) 整合以提供意圖辨識。 意圖是使用者想要做的事情：訂機票、查看天氣或打電話。
使用意圖辨識時，您的應用程式、工具和裝置可以根據您在 LUIS 中定義的選項，判斷使用者希望起始或執行的動作。

## <a name="luis-key-required"></a>需要 LUIS 金鑰

* LUIS 會與語音服務整合以從語音辨識意圖。 您不需要語音服務訂用帳戶，只要有 LUIS 即可。
* 語音意圖辨識已與 SDK 整合。 您可以搭配語音服務使用 LUIS 金鑰。
* 透過語音 SDK 的意圖辨識是 [在 LUIS 支援的區域子集上提供](./regions.md#intent-recognition)。

## <a name="get-started"></a>開始使用

請參閱 [快速入門](quickstarts/intent-recognition.md) 以開始使用意圖辨識。

## <a name="sample-code"></a>範例程式碼

意圖辨識的範例程式碼：

* [快速入門：使用預先建置的家庭自動化應用程式](../luis/luis-get-started-create-app.md)
* [使用適用於 C# 的語音 SDK 從語音辨識意圖](./how-to-recognize-intents-from-speech-csharp.md)
* [使用 C 中 Unity 的意圖辨識和其他語音服務#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [使用適用于 Python 的語音 SDK 來辨識意圖](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [在 Windows 上使用適用于 c + + 的語音 SDK 的意圖辨識和其他語音服務](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [在 Windows 或 Linux 上使用適用于 JAVA 的語音 SDK 的意圖辨識和其他語音服務](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [在網頁瀏覽器上使用適用于 JavaScript 的語音 SDK 的意圖辨識和其他語音服務](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](./speech-sdk.md)

## <a name="next-steps"></a>下一步

* 完成意圖辨識 [快速入門](quickstarts/intent-recognition.md)
* [免費取得語音服務的訂用帳戶金鑰](overview.md#try-the-speech-service-for-free)
* [取得語音 SDK](speech-sdk.md)