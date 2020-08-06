---
title: 自訂關鍵字-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用語音軟體發展工具組 (SDK) 的自訂關鍵字功能、功能和限制。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: d7eea031e50a0f54a57d8a9c857d52ca137f3283
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760544"
---
# <a name="what-is-a-keyword"></a>什麼是關鍵字？

關鍵字是一種單字或短片語，可讓您的產品啟用語音。 例如，「嘿 Cortana」是 Cortana assistant 的關鍵字。 語音啟用可讓您的使用者透過直接說關鍵字來開始與您的產品互動，完全免費。 隨著您的產品持續接聽關鍵字，所有音訊都會在使用者的裝置上以本機方式處理，直到發生偵測，以確保其資料盡可能保持私用。

## <a name="core-features-of-custom-keyword"></a>自訂關鍵字的核心功能

透過自訂關鍵字的自訂、效能和整合功能，您可以量身打造語音啟用，以最符合您的產品願景和使用者需求。

| 特徵 | 描述 |
|----------|----------|
| 自訂關鍵字 | 關鍵字是您品牌的擴充功能，可強化您與客戶建立的權益。 語音 Studio 上的自訂關鍵字入口網站可讓您指定最能代表您品牌的任何單字或簡短片語。 您可以選擇適當的發音來進一步個人化關鍵字，這將由產生的關鍵字模型接受。
| 關鍵字驗證 | 在本機偵測到關鍵字的高度信賴度時，會將音訊傳送至雲端，以進一步確認使用者是否已說出關鍵字。 關鍵字驗證藉由減少不正確的本機偵測和保護使用者隱私權的影響，提供額外一層的安全性。
| 語音助理 & 語音 SDK 整合 | 您可以透過語音 SDK，輕鬆地在您的裝置或應用程式中整合自訂關鍵字在語音 Studio 上產生的關鍵字。 只要將 SDK 指向語音 Studio 所提供的關鍵字模型，您的產品就會啟用語音，並以關鍵字驗證做為後盾。 您可以藉由建立自己的[語音助理](voice-assistants.md)來完成產品的語音體驗。

## <a name="get-started-with-custom-keywords"></a>開始使用自訂關鍵字

* 如需基本使用方式和設計模式，請參閱[自訂關鍵字基本概念](custom-keyword-basics.md)。
* 如何[使用 c # 以語音 SDK 來啟動您的產品](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>選擇有效的關鍵字

建立有效的關鍵字對於確保您的裝置會一致且正確地回應非常重要。 自訂您的關鍵字是區分您的裝置並強化商標的有效方式。 當您選擇關鍵字時，請考慮下列指導方針：

> [!div class="checklist"]
> * 您的關鍵字應該是英文單字或片語。
> * 說出喚醒字所需的時間不應超過兩秒。
> * 由 4 到 7 個音節所構成的單字，是效果最佳的喚醒字。 例如，「嗨，Computer」是不錯的關鍵字。 只有 "Hey" 則不好。
> * 關鍵字應遵循常用的英文發音規則。
> * 遵循一般英文發音規則的獨特或甚至虛構的單字可減少誤判的情況。 例如，"computerama" 可能是不錯的關鍵字。
> * 請勿選擇一般單字。 例如，"eat" 和 "go" 是一般對話中常說的單字。 它們可能會不小心觸發您的裝置。
> * 請避免使用可能有其他發音的關鍵字。 使用者將必須知道讓裝置回應的「正確」發音。 例如，"509" 可能會讀為 "five zero nine"、"five oh nine" 或 "five hundred and nine"。 "R.E.I." 可能會讀為 "r-e-i" 或 "ray"。 "Live" 可能會讀為"/līv/" 或 "/liv/"。
> * 請勿使用特殊字元、符號或數字。 例如，「Go #」和「20 + 貓」可能是有問題的關鍵字。 不過，"go sharp" 或 "twenty plus cats" 是可用的喚醒字。 您仍然可以使用品牌中的符號，並使用行銷管道和文件來強調適當的發音。

> [!NOTE]
> 如果您選擇商標單字做為關鍵字，請確定您擁有該商標，或您具有商標擁有者的許可權，才能使用此字組。 Microsoft 對於您選擇的關鍵字可能會發生的任何法律問題概不負任何責任。

## <a name="see-samples-on-github"></a>請參閱 GitHub 上的範例

* [在通用 Windows 平臺上使用 C 來辨識使用語音 SDK 的關鍵字#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [在 Android 上使用 JAVA 以語音 SDK 辨識關鍵字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [深入瞭解語音助理](voice-assistants.md)
