---
title: 主權雲端-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用主權雲端
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "78228092"
---
# <a name="speech-services-with-sovereign-clouds"></a>具有主權雲端的語音服務

## <a name="azure-government-united-states"></a>Azure Government （美國）

只有美國聯邦、州、地方和部落政府及其合作夥伴可以使用由經過篩選的美國公民控制的作業來存取此專用實例。
- 區域： US Gov 維吉尼亞州
- Speechsdk.quickstart 中的 SR：*config。FromHost （"wss：//virginia.stt.speech.azure.us"，"\<您的金鑰\>"）;*
- Speechsdk.quickstart 中的 TTS： *config。FromHost （"HTTPs[]()：//virginia.tts.speech.azure.us"，\<"您的\>金鑰"）;*
- 驗證權杖 .. HTTPs[]()：//virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure 入口網站： https://portal.azure.us  
- 自訂語音入口網站：https://virginia.cris.azure.us/Home/CustomSpeech
- 可用的 Sku： S0
- 支援的功能：
  - 語音轉文字
  - 自訂語音（聲場/語言調適）
  - 文字轉語音
  - 語音翻譯工具
- 不支援的功能
  - 自訂語音
  - 文字轉換語音的類神經語音
- 支援的地區設定：支援下列語言的地區設定。
  - 阿拉伯文（ar-*）
  - 中文（zh-*）
  - 英文（en-*）
  - 法文（fr-*）
  - 德文（de-*）
  - Hindi
  - 韓文
  - 俄文
  - 西班牙文（es-*）

## <a name="microsoft-azure-china"></a>Microsoft Azure 中國

位於中國的 Azure 資料中心，可直接存取中國 Mobile、中國電訊、中國 Unicom 和其他主要電信公司骨幹網路，供中文使用者提供高速且穩定的區域網路存取體驗。
- 區域：中國東部2（上海）
- Speechsdk.quickstart 中的 SR： *config。FromHost （"wss：//chinaeast2.stt.speech.azure.cn"，"\<您的金鑰\>"）;*
- Speechsdk.quickstart 中的 TTS： *config。FromHost （"HTTPs[]()：//chinaeast2.tts.speech.azure.cn"，\<"您的\>金鑰"）;*
- 驗證權杖 .. HTTPs[]()：//chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure 入口網站： https://portal.azure.cn
- 自訂語音入口網站：https://speech.azure.cn/CustomSpeech
- 可用的 Sku： S0
- 支援的功能：
  - 語音轉文字
  - 自訂語音（聲場/語言調適）
  - 文字轉語音
  - 語音翻譯工具
- 不支援的功能
  - 自訂語音
  - 文字轉換語音的類神經語音
- 支援的地區設定：支援下列語言的地區設定。
  - 阿拉伯文（ar-*）
  - 中文（zh-*）
  - 英文（en-*）
  - 法文（fr-*）
  - 德文（de-*）
  - Hindi
  - 韓文
  - 俄文
  - 西班牙文（es-*）

