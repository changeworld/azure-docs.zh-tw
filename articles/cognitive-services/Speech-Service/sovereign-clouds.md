---
title: 主權雲端-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用主權 Cloud
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78228092"
---
# <a name="speech-services-with-sovereign-clouds"></a>使用主權 cloud 的語音服務

## <a name="azure-government-united-states"></a>美國 Azure Government () 

只有美國聯邦、州、地方和部落政府及其合作夥伴能夠存取這個專用實例，以及由經篩選的美國公民控制的作業。
- 區域： US Gov 維吉尼亞州
- >speechsdk.quickstart 中的 SR：*config。FromHost ( "wss://virginia.stt.speech.azure.us"，" \<your-key\> " ) ;*
- >speechsdk.quickstart 中的 TTS： *config。FromHost ( "HTTPs： []() //virginia.tts.speech.azure.us"，" \<your-key\> " ) ;*
- 驗證權杖： HTTPs： []() //virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure 入口網站： https://portal.azure.us  
- 自訂語音入口網站： https://virginia.cris.azure.us/Home/CustomSpeech
- 可用的 Sku： S0
- 支援的功能：
  - 語音轉文字
  - 自訂語音 (聲場/語言調整) 
  - 文字轉語音
  - 語音翻譯
- 不支援的功能
  - 自訂語音
  - 文字轉換語音的神經語音
- 支援的地區設定：支援下列語言的地區設定。
  - 阿拉伯文 (ar-* ) 
  - 中文 (zh-* ) 
  - 英文 (en-*)
  - 法文 (fr-* ) 
  - 德文 (de-* ) 
  - Hindi
  - 韓文
  - 俄文
  - 西班牙文 (es-* ) 

## <a name="microsoft-azure-china"></a>Microsoft Azure 中國

位於中國，可直接存取中國行動裝置、中國電信、中國 Unicom 和其他主要電訊廠商骨幹網路的 Azure 資料中心，可供中文使用者提供高速且穩定的區域網路存取體驗。
- 區域：中國東部 2 (上海) 
- >speechsdk.quickstart 中的 SR： *config。FromHost ( "wss://chinaeast2.stt.speech.azure.cn"，" \<your-key\> " ) ;*
- >speechsdk.quickstart 中的 TTS：  *config。FromHost ( "HTTPs： []() //chinaeast2.tts.speech.azure.cn"，" \<your-key\> " ) ;*
- 驗證權杖： HTTPs： []() //chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure 入口網站： https://portal.azure.cn
- 自訂語音入口網站： https://speech.azure.cn/CustomSpeech
- 可用的 Sku： S0
- 支援的功能：
  - 語音轉文字
  - 自訂語音 (聲場/語言調整) 
  - 文字轉語音
  - 語音翻譯
- 不支援的功能
  - 自訂語音
  - 文字轉換語音的神經語音
- 支援的地區設定：支援下列語言的地區設定。
  - 阿拉伯文 (ar-* ) 
  - 中文 (zh-* ) 
  - 英文 (en-*)
  - 法文 (fr-* ) 
  - 德文 (de-* ) 
  - Hindi
  - 韓文
  - 俄文
  - 西班牙文 (es-* ) 

