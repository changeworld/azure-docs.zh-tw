---
title: 自訂關鍵字-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用語音軟體發展工具組 (SDK) 的自訂關鍵字功能、功能及限制。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356599"
---
# <a name="what-is-a-keyword"></a>什麼是關鍵字？

關鍵字是可讓您的產品啟用語音的單字或簡短片語。 例如，"嗨 Cortana" 是 Cortana assistant 的關鍵字。 語音啟用可讓您的使用者藉由直接說出關鍵字來開始與您的產品互動。 當您的產品持續接聽關鍵字時，系統會在使用者的裝置本機上處理所有音訊，直到發生偵測，以確保其資料保持私密。

## <a name="core-features-of-custom-keyword"></a>自訂關鍵字的核心功能

使用自訂關鍵字的自訂、效能和整合功能，您可以量身打造語音啟用，以最符合您產品的願景和使用者的需求。

| 功能 | 說明 |
|----------|----------|
| 關鍵字自訂 | 作為品牌的擴充功能，關鍵字會強化您與客戶建立的權益。 語音 Studio 上的自訂關鍵字入口網站可讓您指定最能代表您品牌的任何單字或簡短片語。 您可以藉由選擇正確的發音（會由產生的關鍵字模型接受）來進一步個人化關鍵字。
| 關鍵字驗證 | 如果在本機偵測到關鍵字有高度信賴度，則會將音訊傳送至雲端，以進一步確認使用者是否已說出關鍵字。 關鍵字驗證藉由減少不正確的本機偵測和保護使用者隱私權的影響，來提供額外的安全性層級。
| 語音助理 & 語音 SDK 整合 | 您可以透過語音 SDK，輕鬆地在您的裝置或應用程式中整合在語音 Studio 上自訂關鍵字產生的關鍵字。 只要將 SDK 指向語音 Studio 所提供的關鍵字模型，您的產品就會啟用語音，並受到關鍵字驗證的支援。 您可以建立自己的 [語音助理](voice-assistants.md)來完成產品的語音體驗。

## <a name="get-started-with-custom-keywords"></a>開始使用自訂關鍵字

* 請參閱基本使用方式和設計模式的 [自訂關鍵字基本概念](custom-keyword-basics.md) 。
* 如何 [使用 c # 透過語音 SDK 來語音啟用您的產品](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>選擇有效的關鍵字

為了確保您的裝置能以一致且正確的回應，建立有效的關鍵字是很重要的。 自訂關鍵字是用來區分您的裝置和強化品牌的有效方式。 當您選擇關鍵字時，請考慮下列指導方針：

> [!div class="checklist"]
> * 關鍵字應該是英文單字或片語。
> * 說出喚醒字所需的時間不應超過兩秒。
> * 由 4 到 7 個音節所構成的單字，是效果最佳的喚醒字。 例如，"嗨，Computer" 是不錯的關鍵字。 只有 "Hey" 則不好。
> * 關鍵字應遵循常見的英文發音規則。
> * 遵循一般英文發音規則的獨特或甚至虛構的單字可減少誤判的情況。 例如，"computerama" 可能是不錯的關鍵字。
> * 請勿選擇一般單字。 例如，"eat" 和 "go" 是一般對話中常說的單字。 它們可能會不小心觸發您的裝置。
> * 請避免使用可能有替代發音的關鍵字。 使用者將必須知道讓裝置回應的「正確」發音。 例如，"509" 可能會讀為 "five zero nine"、"five oh nine" 或 "five hundred and nine"。 "R.E.I." 可能會讀為 "r-e-i" 或 "ray"。 "Live" 可能會讀為"/līv/" 或 "/liv/"。
> * 請勿使用特殊字元、符號或數字。 例如，「Go #」和「20 + 貓」可能是有問題的關鍵字。 不過，"go sharp" 或 "twenty plus cats" 是可用的喚醒字。 您仍然可以使用品牌中的符號，並使用行銷管道和文件來強調適當的發音。

> [!NOTE]
> 如果您選擇商標單字作為關鍵字，請確定您擁有該商標或您擁有商標擁有者的許可，才能使用該字組。 Microsoft 不負責您選擇的關鍵字可能產生的任何法律問題。

## <a name="see-samples-on-github"></a>請參閱 GitHub 上的範例

* [使用 C 在通用 Windows 平臺上以語音 SDK 辨識關鍵字#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [使用 JAVA 在 Android 上以語音 SDK 辨識關鍵字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](overview.md#try-the-speech-service-for-free)
* [取得語音 SDK](speech-sdk.md)
* [深入瞭解語音助理](voice-assistants.md)
