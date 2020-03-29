---
title: 直線語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音軟體發展工具組 （SDK） 使用"直線語音"的語音助理的功能、功能和限制概述。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 714cdefa7100160a510d1922a2547ae53692d76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367800"
---
# <a name="what-is-direct-line-speech"></a>什麼是 Direct Line Speech？

**直接線上語音**是一個強大的端到端解決方案，用於創建靈活、可擴展的語音助手。 它由機器人框架及其直線語音通道提供支援，該通道針對與機器人的語音轉換、語音交互進行了優化。

[語音助理](voice-assistants.md)會傾聽使用者並採取行動進行回應，通常還會回話。 他們使用[語音到文本](speech-to-text.md)來轉錄使用者的演講，然後對文本的自然語言理解採取行動。 此操作通常包括使用[文本到語音](text-to-speech.md)生成的助手的語音輸出。

直接線上語音為語音助理提供了最高級別的自訂和複雜。 它專為具有任務完成或命令和控制使用的開放式、自然或混合的會話方案而設計。 這種高度的靈活性具有更大的複雜性，使用自然語言輸入將範圍限定為定義良好的任務的方案可能需要考慮[自訂命令（預覽），](custom-commands.md)以簡化解決方案體驗。

## <a name="getting-started-with-direct-line-speech"></a>開始使用直線語音

使用"直線語音"創建語音助手的第一個步驟是[獲取語音訂閱金鑰](get-started.md)、創建與該訂閱關聯的新自動程式以及將自動程式連接到"直線語音"通道。

   ![直線語音編排服務流的概念圖](media/voice-assistants/overview-directlinespeech.png "語音通道流")

有關使用"直線語音"創建簡單語音助手的完整分步指南，請參閱[使用語音 SDK 和直線語音通道啟用語音的機器人教程](tutorial-voice-enable-your-bot-speech-sdk.md)。

我們還提供快速入門，旨在讓您快速運行代碼和學習 API。 此表包括按語言和平臺組織的語音助理快速入門的清單。

| 快速入門 | Platform | 應用程式開發介面參考 |
|------------|----------|---------------|
| C#，UWP | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [瀏覽](https://aka.ms/csspeech/javaref) |
| Java | Android | [瀏覽](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

在 GitHub 上提供了用於創建語音助理的示例代碼。 這些示例涵蓋用戶端應用程式，用於以幾種流行的程式設計語言連接到您的助手。

* [語音助理範例 (SDK)](https://aka.ms/csspeech/samples) \(英文\)
* [教程：語音啟用您的助手與語音 SDK， C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>自訂

使用語音服務構建的語音助手可以使用用於[語音到文本](speech-to-text.md)、[文本到語音](text-to-speech.md)和[自訂關鍵字選擇](speech-devices-sdk-create-kws.md)的完整自訂選項。

> [!NOTE]
> 自訂選項因語言/地區設定而異（請參閱[支援的語言](supported-languages.md)）。

直接線路語音及其語音助理的相關功能是[虛擬助理解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的理想補充。 儘管 Direct Line 語音可以與任何相容的機器人配合使用，但這些資源為高品質的對話體驗提供了可重用的基線，以及快速入門的常見支援技能和模型。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure 機器人服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [建立及部署基本 Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [獲取虛擬助理解決方案和企業範本](https://github.com/Microsoft/AI)
