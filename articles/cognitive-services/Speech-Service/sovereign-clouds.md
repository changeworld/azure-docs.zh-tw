---
title: 主權雲端-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用主權 Cloud
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: 63caac7871df9d999ca5a5f1b8c036ae7014231c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98061747"
---
# <a name="speech-services-in-sovereign-clouds"></a>主權雲端中的語音服務

## <a name="azure-government-united-states"></a>美國 Azure Government () 

僅適用于美國政府機構及其合作夥伴。 如 [需 Azure Government 的](../../azure-government/documentation-government-welcome.md) 詳細資訊，請參閱這裡 [。](../../azure-government/compare-azure-government-global-azure.md)

- **Azure 入口網站：**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **地區：**
  - US Gov 亞利桑那州
  - US Gov 維吉尼亞州
- **可用的定價層：**
  - Free (F0) 和 Standard (S0) 。 [在這裡](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)查看更多詳細資料
- **支援的功能：**
  - 語音轉文字
    - 自訂語音 (聲場模型 () 和語言模型 (LM) 適應性) 
      - [Speech Studio](https://speech.azure.us/) \(英文\)
  - 文字轉換語音
    - 標準語音
    - 類神經語音
  - 語音翻譯
- **不支援的功能：**
  - 自訂語音
- **支援的語言：**
  - 請參閱[這裡](language-support.md)的支援語言清單

### <a name="endpoint-information"></a>端點資訊

本章節包含使用語音 [SDK](speech-sdk.md)、 [語音轉換文字 REST API](rest-speech-to-text.md)和 [文字轉換語音 REST API](rest-text-to-speech.md)的語音服務端點資訊。

#### <a name="speech-services-rest-api"></a>語音服務 REST API

Azure Government 中的語音服務 REST API 端點具有下列格式：

|  REST API 類型/作業 | 端點格式 |
|--|--|
| 存取權杖 | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [語音轉換文字 REST API v3。0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [適用于短音訊的語音轉換文字 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [文字轉換語音 REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

取代 `<REGION_IDENTIFIER>` 為與此資料表中的訂用帳戶區域相符的識別碼：

|                     | 區域識別碼 |
|--|--|
| **US Gov 亞利桑那州**  | `usgovarizona` |
| **US Gov 維吉尼亞州** | `usgovvirginia` |

#### <a name="speech-sdk"></a>語音 SDK

針對主權雲端中的語音 SDK，您必須使用 `SpeechConfig` `--host` [speech CLI](spx-overview.md)類別或選項的「從主機」具現化。  (您也可以使用「從端點」具現化和 `--endpoint` 語音 CLI 選項) 。

`SpeechConfig` 類別應該具現化，如下所示：
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
語音 CLI 應使用如下 (請注意 `--host` 選項) ：
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
`subscriptionKey`以您的語音資源金鑰取代。 取代 `usGovHost` 為符合所需服務供應專案的運算式，以及您從此資料表中訂用帳戶的區域：

|  區域/服務供應專案 | 主機運算式 |
|--|--|
| **US Gov 亞利桑那州** | |
| 語音轉文字 | `wss://usgovarizona.stt.speech.azure.us` |
| 文字轉換語音 | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov 維吉尼亞州** | |
| 語音轉文字 | `wss://usgovvirginia.stt.speech.azure.us` |
| 文字轉換語音 | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure 中國

適用于在中國有商務存在的組織。 如需 Azure 中國的詳細資訊，請參閱 [。](/azure/china/overview-operations) 


- **Azure 入口網站：**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **地區：**
  - 中國東部 2
- **可用的定價層：**
  - Free (F0) 和 Standard (S0) 。 [在這裡](https://www.azure.cn/pricing/details/cognitive-services/index.html)查看更多詳細資料
- **支援的功能：**
  - 語音轉文字
    - 自訂語音 (聲場模型 () 和語言模型 (LM) 適應性) 
      - [Speech Studio](https://speech.azure.cn/) \(英文\)
  - 文字轉換語音
    - 標準語音
    - 類神經語音
  - 語音翻譯
- **不支援的功能：**
  - 自訂語音
- **支援的語言：**
  - 請參閱[這裡](language-support.md)的支援語言清單

### <a name="endpoint-information"></a>端點資訊

本章節包含使用語音 [SDK](speech-sdk.md)、 [語音轉換文字 REST API](rest-speech-to-text.md)和 [文字轉換語音 REST API](rest-text-to-speech.md)的語音服務端點資訊。

#### <a name="speech-services-rest-api"></a>語音服務 REST API

Azure 中國中的語音服務 REST API 端點具有下列格式：

|  REST API 類型/作業 | 端點格式 |
|--|--|
| 存取權杖 | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [語音轉換文字 REST API v3。0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [適用于短音訊的語音轉換文字 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [文字轉換語音 REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

取代 `<REGION_IDENTIFIER>` 為與此資料表中的訂用帳戶區域相符的識別碼：

|                     | 區域識別碼 |
|--|--|
| **中國東部 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>語音 SDK

針對主權雲端中的語音 SDK，您必須使用 `SpeechConfig` `--host` [speech CLI](spx-overview.md)類別或選項的「從主機」具現化。  (您也可以使用「從端點」具現化和 `--endpoint` 語音 CLI 選項) 。

`SpeechConfig` 類別應該具現化，如下所示：
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
語音 CLI 應使用如下 (請注意 `--host` 選項) ：
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
`subscriptionKey`以您的語音資源金鑰取代。 取代 `azCnHost` 為符合所需服務供應專案的運算式，以及您從此資料表中訂用帳戶的區域：

|  區域/服務供應專案 | 主機運算式 |
|--|--|
| **中國東部 2** | |
| 語音轉文字 | `wss://chinaeast2.stt.speech.azure.cn` |
| 文字轉換語音 | `https://chinaeast2.tts.speech.azure.cn` |