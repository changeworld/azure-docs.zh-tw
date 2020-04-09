---
title: 容器限制 - LUIS
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
ms.openlocfilehash: 7fe773b35c5aba31b2fea66bd2be7b2745eac3ee
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879236"
---
# <a name="language-understanding-luis-container-limitations"></a>語言理解 (LUIS) 容器限制

LUIS 容器有幾個值得注意的限制。 從不受支持的依賴項到支援的語言子集,本文詳細介紹了這些限制。

## <a name="supported-dependencies-for-latest-container"></a>`latest`容器支援相依項

最新的 LUIS 容器發佈於[//build/ 2019,](https://news.microsoft.com/build2019/)將支援:

* [新的預建置功能](luis-reference-prebuilt-domains.md):這些以企業為中心的域包括實體、範例陳述和模式。 擴展這些域供自己使用。

## <a name="unsupported-dependencies-for-latest-container"></a>`latest`容器不支援的相依項

要[匯出容器](luis-container-howto.md#export-packaged-app-from-luis),必須從 LUIS 應用中刪除不支援的依賴項。 當您嘗試匯出容器時,LUIS 門戶會報告需要刪除的這些不支援的功能。

您可以使用**不含**下列任何相依性的 LUIS 應用程式：

不支援的應用程式組態|詳細資料|
|--|--|
|不支援的容器文化特性| 荷蘭語`nl-NL`( )<br>日語`ja-JP`( )<br>德文僅支援[1.0.2 標籤器](luis-language-support.md#custom-tokenizer-versions)。|
|所有文化特性皆不支援的實體|適用於所有文化特性的 [KeyPhrase](luis-reference-prebuilt-keyphrase.md) 預先建置實體|
|英語`en-US`( ) 區域性的不支援實體|[GeographyV2](luis-reference-prebuilt-geographyV2.md) 預先建置的實體|
|語音預備|在容器中不支援外部相依性。|
|情感分析|在容器中不支援外部相依性。|
|Bing 拼字檢查|在容器中不支援外部相依性。|

## <a name="languages-supported"></a>支援的語言

LUIS 容器支援 LUIS 正確[支援的語言](luis-language-support.md#languages-supported)子集。 LUIS 容器能夠理解以下語言的話語:

| Language | Locale | 預建網域 | 預建實體 | 短語清單建議 | **[文字分析](../text-analytics/language-support.md)<br>(情感和<br>關鍵字)|
|--|--|:--:|:--:|:--:|:--:|
| 美式英文 | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[中文](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| 法文 (法國) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| 法文 (加拿大) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| 德文 |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| 義大利文 |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| 韓文 |`ko-KR` | ✔️ | ❌ | ❌ | 只*鍵短語* |
| 葡萄牙文 (巴西) |`pt-BR` | ✔️ | ✔️ | ✔️ | 並非所有的次文化特性 |
| 西班牙文 (西班牙) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| 西班牙文 (墨西哥)|`es-MX` | ❌ | ❌ |✔️|✔️|
| 土耳其文 | `tr-TR` |✔️| ❌ | ❌ | 僅*情緒* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]