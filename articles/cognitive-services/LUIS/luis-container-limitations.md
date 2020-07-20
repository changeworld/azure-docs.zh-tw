---
title: 容器限制-LUIS
titleSuffix: Azure Cognitive Services
description: 支援的 LUIS 容器語言。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84672658"
---
# <a name="language-understanding-luis-container-limitations"></a>Language Understanding （LUIS）容器限制

LUIS 容器有一些值得注意的限制。 從不支援的相依性到支援的語言子集，本文會詳細說明這些限制。

## <a name="supported-dependencies-for-latest-container"></a>支援的容器相依性 `latest`

最新的 LUIS 容器支援：

* [新的預建網域](luis-reference-prebuilt-domains.md)：這些企業導向的網域包括實體、範例語句和模式。 擴充這些網域以供您自己使用。

## <a name="unsupported-dependencies-for-latest-container"></a>不支援的 `latest` 容器相依性

若要[匯出容器](luis-container-howto.md#export-packaged-app-from-luis)，您必須從 LUIS 應用程式移除不受支援的相依性。 當您嘗試匯出容器時，LUIS 入口網站會報告您需要移除的這些不支援的功能。

您可以使用**不含**下列任何相依性的 LUIS 應用程式：

不支援的應用程式組態|詳細資料|
|--|--|
|不支援的容器文化特性| `nl-NL`只有 1.0.2 tokenizer 支援荷蘭文（）、日文（ `ja-JP` ）和德文（ `de-DE` ）語言。 [1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions)|
|所有文化特性皆不支援的實體|適用於所有文化特性的 [KeyPhrase](luis-reference-prebuilt-keyphrase.md) 預先建置實體|
|英文（）文化特性不支援的實體 `en-US`|[GeographyV2](luis-reference-prebuilt-geographyV2.md) 預先建置的實體|
|語音預備|在容器中不支援外部相依性。|
|情感分析|在容器中不支援外部相依性。|
|Bing 拼字檢查|在容器中不支援外部相依性。|

## <a name="languages-supported"></a>支援的語言

LUIS 容器支援 LUIS 適當支援的[語言](luis-language-support.md#languages-supported)子集。 LUIS 容器能夠瞭解下列語言的語句：

| Language | 地區設定 | 預建網域 | 預建實體 | 片語清單建議 | **[文字分析](../text-analytics/language-support.md)<br>(情感和<br>關鍵字)|
|--|--|:--:|:--:|:--:|:--:|
| 英文 (美國) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| 阿拉伯文（預覽-現代化標準阿拉伯文） |`ar-AR`|❌|❌|❌|❌|
| *[中文](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| 法文 (法國) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| 法文 (加拿大) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| 德文 |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| 義大利文 |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| 韓文 |`ko-KR` | ✔️ | ❌ | ❌ | 僅限*關鍵字*組 |
| 馬拉地文 | `mr-IN`|❌|❌|❌|❌|
| 葡萄牙文 (巴西) |`pt-BR` | ✔️ | ✔️ | ✔️ | 並非所有的次文化特性 |
| 西班牙文 (西班牙) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| 西班牙文 (墨西哥)|`es-MX` | ❌ | ❌ |✔️|✔️|
| 坦米爾文 | `ta-IN`|❌|❌|❌|❌|
| 泰盧固文 | `te-IN`|❌|❌|❌|❌|
| 土耳其文 | `tr-TR` |✔️| ❌ | ❌ | 僅限*情感* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
