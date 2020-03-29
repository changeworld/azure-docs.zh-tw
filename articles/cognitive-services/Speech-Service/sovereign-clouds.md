---
title: 主權雲 - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用主權雲
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228092"
---
# <a name="speech-services-with-sovereign-clouds"></a>具有主權雲的語音服務

## <a name="azure-government-united-states"></a>Azure 政府（美國）

只有美國聯邦、州、地方和部落政府及其合作夥伴才能訪問此專用實例，其操作由經過篩選的美國公民控制。
- 地區：美國弗吉尼亞州州長
- 語音 SDK 中的 SR：*配置。從Host（"wss：//virginia.stt.語音.azure.us"，"\<你的鍵\>"）;*
- 語音 SDK 中的 TTS：*配置。從Host（"HTTPs：//virginia.tts.speech.azure.us"，"[]()\<你的鑰匙\>"）;*
- 身份驗證權杖： HTTPs[]()：//virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure 入口網站： https://portal.azure.us  
- 自訂語音門戶：https://virginia.cris.azure.us/Home/CustomSpeech
- 可用 SKU： S0
- 支援的功能：
  - 語音轉文字
  - 自訂語音（聲學/語言自我調整）
  - 文字轉語音
  - 語音翻譯
- 不支援的功能
  - 自訂語音
  - 文本到語音的神經語音
- 支援地區設定：支援以下語言的地區設定。
  - 阿拉伯文（ar-*）
  - 中文（zh-*）
  - 英語（en-*）
  - 法語 （fr-*）
  - 德語（除*）
  - Hindi
  - 韓文
  - 俄文
  - 西班牙文（es-*）

## <a name="microsoft-azure-china"></a>微軟 Azure 中國

位於中國，一個Azure資料中心，可直接接入中國移動、中國電信、中國聯通等主要運營商骨幹網，為中國使用者提供高速穩定的本地網路接入體驗。
- 地區：華東2（上海）
- 語音 SDK 中的 SR：*配置。從Host（"ws：//chinaeast2.stt.語音.azure.cn"，"\<你的鍵\>"）;*
- 語音 SDK 中的 TTS：*配置。從Host（"HTTPs：//chinaeast2.tts.speech.azure.cn"，"[]()\<你的鑰匙\>"）;*
- 身份驗證權杖： HTTPs[]()：//chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure 入口網站： https://portal.azure.cn
- 自訂語音門戶：https://speech.azure.cn/CustomSpeech
- 可用 SKU： S0
- 支援的功能：
  - 語音轉文字
  - 自訂語音（聲學/語言自我調整）
  - 文字轉語音
  - 語音翻譯
- 不支援的功能
  - 自訂語音
  - 文本到語音的神經語音
- 支援地區設定：支援以下語言的地區設定。
  - 阿拉伯文（ar-*）
  - 中文（zh-*）
  - 英語（en-*）
  - 法語 （fr-*）
  - 德語（除*）
  - Hindi
  - 韓文
  - 俄文
  - 西班牙文（es-*）

