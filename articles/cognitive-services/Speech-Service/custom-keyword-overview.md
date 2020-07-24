---
title: 自訂關鍵字-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用語音軟體發展工具組（SDK）之自訂關鍵字的功能、功能和限制。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 60bcdac0d7e19c424b007980294898638814d586
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044871"
---
# <a name="what-is-a-keyword"></a>什麼是關鍵字？

關鍵字是一種單字或短片語，可讓您的產品啟用語音。 例如，「嘿 Cortana」是 Cortana assistant 的關鍵字。 語音啟用可讓您的使用者透過直接說關鍵字來開始與您的產品互動，完全免費。 隨著您的產品持續接聽關鍵字，所有音訊都會在使用者的裝置上以本機方式處理，直到發生偵測，以確保其資料盡可能保持私用。

## <a name="core-features-of-custom-keyword"></a>自訂關鍵字的核心功能

透過自訂關鍵字的自訂、效能和整合功能，您可以量身打造語音啟用，以最符合您的產品願景和使用者需求。

| 功能 | 描述 |
|----------|----------|
| 自訂關鍵字 | 關鍵字是您品牌的擴充功能，可強化您與客戶建立的權益。 語音 Studio 上的自訂關鍵字入口網站可讓您指定最能代表您品牌的任何單字或簡短片語。 您可以選擇適當的發音來進一步個人化關鍵字，這將由產生的關鍵字模型接受。
| 關鍵字驗證 | 在本機偵測到關鍵字的高度信賴度時，會將音訊傳送至雲端，以進一步確認使用者是否已說出關鍵字。 關鍵字驗證藉由減少不正確的本機偵測和保護使用者隱私權的影響，提供額外一層的安全性。
| 語音助理 & 語音 SDK 整合 | 您可以透過語音 SDK，輕鬆地在您的裝置或應用程式中整合自訂關鍵字在語音 Studio 上產生的關鍵字。 只要將 SDK 指向語音 Studio 所提供的關鍵字模型，您的產品就會啟用語音，並以關鍵字驗證做為後盾。 您可以藉由建立自己的[語音助理](voice-assistants.md)來完成產品的語音體驗。

## <a name="get-started-with-custom-keywords"></a>開始使用自訂關鍵字

* 如需基本使用方式和設計模式，請參閱[自訂關鍵字基本概念](custom-keyword-basics.md)。
* 如何[使用 c # 以語音 SDK 來啟動您的產品](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="see-samples-on-github"></a>請參閱 GitHub 上的範例

* [在通用 Windows 平臺上使用 C 來辨識使用語音 SDK 的關鍵字#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [在 Android 上使用 JAVA 以語音 SDK 辨識關鍵字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [深入瞭解語音助理](voice-assistants.md)
