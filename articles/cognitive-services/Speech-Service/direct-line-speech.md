---
title: Direct Line 語音-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用 Direct Line 語音搭配語音軟體發展工具組 (SDK) 的語音助理功能、功能及限制。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 2b0bb997ecea55d25748152bab7bb6963d67657f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357636"
---
# <a name="what-is-direct-line-speech"></a>什麼是 Direct Line Speech？

**Direct Line 語音** 是一套強大的端對端解決方案，可讓您建立彈性且可擴充的語音助理。 它是由 Bot Framework 和其 Direct Line 語音通道所支援，其已針對語音輸入、語音輸出與 bot 的互動進行優化。

[語音助理](voice-assistants.md) 會接聽使用者，並採取動作來回應，通常會說回來。 他們使用 [語音轉換文字](speech-to-text.md) 來轉譯使用者的語音，然後對文字的自然語言理解採取行動。 此動作通常會包含從 [文字轉換語音](text-to-speech.md)所產生之助理的語音輸出。

Direct Line Speech 為語音助理提供最高層級的自訂和複雜的功能。 它是專為使用工作完成或命令和控制所使用的兩個已結束、自然或混合式的對話案例所設計。 這種高度的彈性帶來更複雜的情況，而且範圍限定在使用自然語言輸入之妥善定義工作的案例，可能會想要考慮使用 [自訂命令 (預覽) ](custom-commands.md) ，以簡化解決方案體驗。

## <a name="getting-started-with-direct-line-speech"></a>開始使用 Direct Line 語音

使用 Direct Line 語音建立語音助理的第一個步驟是 [取得語音訂](overview.md#try-the-speech-service-for-free)用帳戶金鑰、建立與該訂用帳戶相關聯的新 bot，然後將 bot 連線到 Direct Line 語音通道。

   ![Direct Line 語音協調流程服務流程的概念圖](media/voice-assistants/overview-directlinespeech.png "語音通道流程")

如需使用 Direct Line 語音建立簡單語音助理的完整逐步指南，請參閱 [使用語音 SDK 和 Direct Line 語音通道來啟用 bot 的語音教學](tutorial-voice-enable-your-bot-speech-sdk.md)課程。

我們也提供快速入門，旨在讓您快速執行程式碼和學習 Api。 下表包含依語言和平臺組織的語音助理快速入門清單。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| C #、UWP | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [瀏覽](https://aka.ms/csspeech/javaref) |
| Java | Android | [瀏覽](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上取得用來建立語音助理的範例程式碼。 這些範例涵蓋了用戶端應用程式，可讓您以數種熱門的程式設計語言連接到您的助理。

* [語音助理範例 (SDK)](https://aka.ms/csspeech/samples) \(英文\)
* [教學課程：使用語音 SDK （C）來啟用您的助理#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>自訂

使用「語音服務」建立的語音助理可以使用適用于 [語音轉換文字](speech-to-text.md)、 [文字轉換語音](text-to-speech.md)和 [自訂關鍵字選取](speech-devices-sdk-create-kws.md)的完整自訂選項範圍。

> [!NOTE]
> 自訂選項會因語言/地區設定而異 (請參閱 [支援的語言](supported-languages.md)) 。

語音助理 Direct Line 語音和其相關聯的功能是 [虛擬助理解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的絕佳補充。 雖然 Direct Line 語音可以與任何相容的 bot 搭配使用，但這些資源為高品質的對話式體驗提供可重複使用的基準，以及快速入門的一般支援技能和模型。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](overview.md#try-the-speech-service-for-free)
* [取得語音 SDK](speech-sdk.md)
* [建立及部署基本 Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [取得虛擬助理解決方案和企業範本](https://github.com/Microsoft/AI)
