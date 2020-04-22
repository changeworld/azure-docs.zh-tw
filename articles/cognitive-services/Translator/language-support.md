---
title: 語言支援 - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 翻譯器文字 API 支援使用神經機翻譯 (NMT) 進行文本到文字翻譯的以下語言。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 0ecde5acb7dc57ed9e5802c1589d5813a9206643
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684835"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>翻譯工具文字 API 支援的語言和區域

翻譯工具文字 API 支援以下語言的文字轉文字翻譯。 類神經機器翻譯 (NMT) 是高品質 AI 技術支援機器翻譯的新標準，在具備類神經系統時，若使用翻譯工具文字 API 第 3 版，該新標準將為預設值。

[深入了解機器翻譯的運作方式](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>翻譯

**V2 Translator API**

> [!NOTE]
> V2 於 2018 年 4 月 30 日棄用。 請將應用程式遷移到 V3,以便利用 V3 中獨有功能。

* 僅統計:沒有神經網路可用於此語言。
* 神經可用:有一個神經系統可用。 使用 `category=generalnn` 參數來存取類神經系統。
* 神經預設值:神經是默認翻譯系統。 使用 `category=smt` 參數存取搭配 Microsoft Translator Hub 使用的統計系統。
* 僅限神經:僅提供神經翻譯。

**V3 Translator API** V3 Translator API 預設為類神經架構，而統計系統僅適用於沒有類神經系統存在時。

> [!NOTE]
> 目前,自定義翻譯器中提供了神經語言的子集,並且我們正在逐步添加其他語言。 [檢視目前在自訂翻譯器中提供的語言](#customization)。

|Language|  語言代碼|  V3 API|
|:-----|:-----:|:-----|
|南非荷蘭文| `af`|   類神經|
|阿拉伯文|    `ar`    |   類神經|
|孟加拉文|    `bn`    |   類神經|
|波士尼亞文 (拉丁文)|   `bs`    |   類神經|
|保加利亞文| `bg`    |   類神經|
|粵語 (繁體中文)|   `yue`|  統計|
|卡達隆尼亞文|   `ca`    |   統計|
|簡體中文|    `zh-Hans`|類神經|
|繁體中文|   `zh-Hant`       |類神經|
|克羅埃西亞文|  `hr`    |類神經|
|捷克文| `cs`    |   類神經|
|丹麥文|    `da`        |類神經|
|荷蘭文| `nl`|   類神經|
|英文|   `en`    |   類神經|
|愛沙尼亞文|  `et`    |   類神經|
|斐濟文|    `fj`    |   統計|
|菲律賓文|  `fil`   |   統計|
|芬蘭文|   `fi`    |   類神經|
|法文|    `fr`    |   類神經|
|德文|    `de`    |   類神經|
|希臘文| `el`    |   類神經|
|古吉拉特文|  `gu`    |   類神經|
|海地克裏奧爾文|    `ht`        |統計|
|Hebrew |`he`   |類神經
|Hindi| `hi`    |   類神經|
|白苗文| `mww`   |   統計|
|匈牙利文| `hu`    |   類神經|
|冰島文| `is`    |   類神經|
|印尼文|    `id`    |   統計|
|愛爾蘭文 | `ga`| 類神經
|義大利文|   `it`    |   類神經|
|日文|  `ja`    |   類神經|
|坎那達文|`kn`| 類神經
|史瓦希里文| `sw`    |   統計|
|克林貢文|   `tlh`   |   統計|
|克林貢文 (plqaD)|   `tlh-Qaak`  |   統計|
|韓文 |`ko`   |   類神經|
|拉脫維亞文|   `lv`    |   類神經|
|立陶宛文|    `lt`    |   類神經|
|馬達加斯加文|  `mg`    |   統計|
|馬來文| `ms`        |統計|
|馬來亞拉姆文| `ml` | 類神經
|馬爾他文|   `mt`    |   統計|
|毛利文| `mi`  | 類神經|
|馬拉地文| `mr`  | 類神經|
|挪威文| `nb`    |   類神經|
|波斯文|   `fa`    |   類神經|
|波蘭文|    `pl`    |   類神經|
|葡萄牙文 (巴西)|   `pt-br` |   類神經|
|葡萄牙文 (葡萄牙)| `pt-pt` | 類神經
|旁遮普文|`pa`|類神經
|奎雷塔洛歐多米文|   `otq`   |   統計|
|羅馬尼亞文|  `ro`    |   類神經|
|俄文|   `ru`    |   類神經|
|薩摩亞文|    `sm`    |   統計|
|塞爾維亞文 (斯拉夫)|    `sr-Cyrl`|  統計|
|塞爾維亞文 (拉丁)|   `sr-Latn`       |統計|
|斯洛伐克文|    `sk`    |   類神經|
|斯洛維尼亞文| `sl`    |   類神經|
|西班牙文|   `es`    |   類神經|
|瑞典文|   `sv`    |類神經|
|大溪地文|  `ty`    |統計|
|坦米爾文| `ta`    |   類神經|
|泰盧固文|    `te`    |   類神經|
|泰文|  `th`    |   類神經|
|東加文|    `to`    |   統計|
|土耳其文|   `tr`        |類神經|
|烏克蘭文| `uk`    |   類神經|
|烏都文|  `ur`    |   統計|
|越南文|    `vi`    |   類神經|
|威爾斯文| `cy`    |   類神經|
|猶加敦馬雅文|  `yua`   |   統計|

> [!NOTE]
> 語言代碼`pt`將預設`pt-br`為 葡萄牙文(巴西)。

## <a name="transliteration"></a>轉換

轉換方法支援以下語言。 在「至/自」列中，「<-->」表示該語言後方所列的兩種文字可相互轉換。 「-->」表示該語言只能從某種文字轉寫成另一種文字。

| Language    | 語言代碼 | 指令碼 | 至/自 | 指令碼|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| 阿拉伯文 | `ar` | 阿拉伯文 `Arab` | <--> | 拉丁文 `Latn` |
|孟加拉文  | `bn` | 孟加拉文 `Beng` | <--> | 拉丁文 `Latn` |
| 中文 (簡體) | `zh-Hans` | 簡體中文 `Hans`| <--> | 拉丁文 `Latn` |
| 中文 (簡體) | `zh-Hans` | 簡體中文 `Hans`| <--> | 繁體中文 `Hant`|
| 中文 (繁體) | `zh-Hant` | 繁體中文 `Hant`| <--> | 拉丁文 `Latn` |
| 中文 (繁體) | `zh-Hant` | 繁體中文 `Hant`| <--> | 簡體中文 `Hans` |
| 古吉拉特文 | `gu`  | 古吉拉特文 `Gujr` | --> | 拉丁文 `Latn` |
| Hebrew | `he` | 希伯來文 `Hebr` | <--> | 拉丁文 `Latn` |
| Hindi | `hi` | 梵文字母 `Deva` | <--> | 拉丁文 `Latn` |
| 日文 | `ja` | 日文 `Jpan` | <--> | 拉丁文 `Latn` |
| 坎那達文 | `kn` | 坎那達文 `Knda` | --> | 拉丁文 `Latn` |
| 馬來亞拉姆文 | `ml` | 馬來亞拉姆文 `Mlym` | --> | 拉丁文 `Latn` |
| 馬拉地文 | `mr` | 梵文字母 `Deva` | --> | 拉丁文 `Latn` |
| 歐利亞文 | `or` | 歐利亞文 `Orya` | <--> | 拉丁文 `Latn` |
| 旁遮普文 | `pa` | 古木基文 `Guru`  | <--> | 拉丁文 `Latn`  |
| 塞爾維亞文 (斯拉夫) | `sr-Cyrl` | 斯拉夫文 `Cyrl`  | --> | 拉丁文 `Latn` |
| 塞爾維亞文 (拉丁) | `sr-Latn` | 拉丁文 `Latn` | --> | 斯拉夫文 `Cyrl`|
| 坦米爾文 | `ta` | 坦米爾文 `Taml` | --> | 拉丁文 `Latn` |
| 泰盧固文 | `te` | 泰盧固文 `Telu` | --> | 拉丁文 `Latn` |
| 泰文 | `th` | 泰文 `Thai` | --> | 拉丁文 `Latn` |

## <a name="dictionary"></a>字典

字典支援使用查閱與範例方法來查詢下列語言的英文譯文，或英文的下列語言譯文。

| Language    | 語言代碼 |
|:----------- |:-------------:|
| 南非荷蘭文      | `af`          |
| 阿拉伯文       | `ar`          |
| 孟加拉文      | `bn`          |
| 波士尼亞文 (拉丁文)      | `bs`          |
| 保加利亞文      | `bg`          |
| 卡達隆尼亞文      | `ca`          |
| 簡體中文      | `zh-Hans`          |
| 克羅埃西亞文      | `hr`          |
| 捷克文      | `cs`          |
| 丹麥文      | `da`          |
| 荷蘭文      | `nl`          |
| 愛沙尼亞文      | `et`          |
| 芬蘭文      | `fi`          |
| 法文      | `fr`          |
| 德文      | `de`          |
| 希臘文      | `el`          |
| 海地克裏奧爾文      | `ht`          |
| Hebrew      | `he`          |
| Hindi      | `hi`          |
| 白苗文      | `mww`          |
| 匈牙利文      | `hu`          |
| 冰島文    | `is`  |
| 印尼文      | `id`          |
| 義大利文      | `it`          |
| 日文      | `ja`          |
| 史瓦希里文      | `sw`          |
| 克林貢文      | `tlh`          |
| 韓文      | `ko`          |
| 拉脫維亞文      | `lv`          |
| 立陶宛文      | `lt`          |
| 馬來文      | `ms`          |
| 馬爾他文      | `mt`          |
| 挪威文      | `nb`          |
| 波斯文      | `fa`          |
| 波蘭文      | `pl`          |
| 葡萄牙文 (巴西)     | `pt-br`          |
| 羅馬尼亞文      | `ro`          |
| 俄文      | `ru`          |
| 塞爾維亞文 (拉丁)      | `sr-Latn`          |
| 斯洛伐克文     | `sk`          |
| 斯洛維尼亞文      | `sl`          |
| 西班牙文      | `es`          |
| 瑞典文      | `sv`          |
| 坦米爾文      | `ta`          |
| 泰文      | `th`          |
| 土耳其文      | `tr`          |
| 烏克蘭文      | `uk`          |
| 烏都文      | `ur`          |
| 越南文      | `vi`          |
| 威爾斯文      | `cy`          |

## <a name="detect"></a>Detect

翻譯文字 API 檢測所有可用於翻譯和音譯的語言。


## <a name="access-the-translator-text-api-language-list-programmatically"></a>以程式設計方式存取翻譯工具文字 API 的語言清單

您可以使用語言方法擷取翻譯工具文字 API v3.0 的支援語言清單。 您可以依特色、語言代碼，以及該語言的英文名稱或任何其他受支援語言的名稱，來檢視清單。 當我們提供新語言時，Microsoft Translator 服務會自動更新此清單。

[檢視語言作業參考文件](reference/v3-0-languages.md)

## <a name="customization"></a>自訂

以下語言可用於自訂[翻譯](https://aka.ms/CustomTranslator)器的英語或英語自定義。

| Language    | 語言代碼 |
|:----------- |:-------------:|
| 阿拉伯文       | `ar`          |
| 孟加拉文      | `bn`          |
| 波士尼亞文 (拉丁文)      | `bs`          |
| 保加利亞文      | `bg`          |
| 簡體中文      | `zh-Hans`          |
|繁體中文|   `zh-Hant`   |
| 克羅埃西亞文      | `hr`          |
| 捷克文      | `cs`          |
| 丹麥文      | `da`          |
| 荷蘭文      | `nl`          |
| 英文    | `en`     |
| 愛沙尼亞文      | `et`          |
| 芬蘭文      | `fi`          |
| 法文      | `fr`          |
| 德文      | `de`          |
| 希臘文      | `el`          |
| Hebrew      | `he`          |
| Hindi      | `hi`          |
| 匈牙利文      | `hu`          |
| 冰島文 | `is` |
| 印尼文|   `id`    |
| 愛爾蘭文 | `ga`  |
| 義大利文      | `it`          |
| 日文      | `ja`          |
| 史瓦希里文|    `sw`    |
| 韓文      | `ko`          |
| 拉脫維亞文      | `lv`          |
| 立陶宛文      | `lt`          |
| 馬達加斯加文| `mg`    |
| 毛利文| `mi`  |
| 挪威文      | `nb`          |
| 波斯文      | `fa`          |
| 波蘭文      | `pl`          |
| 葡萄牙文 (巴西) | `pt-br` |
| 羅馬尼亞文      | `ro`          |
| 俄文      | `ru`          |
| 薩摩亞文|   `sm`    |
| 塞爾維亞文 (拉丁)      | `sr-Latn`          |
| 斯洛伐克文     | `sk`          |
| 斯洛維尼亞文      | `sl`          |
| 西班牙文      | `es`          |
| 瑞典文      | `sv`          |
| 泰文      | `th`          |
| 土耳其文      | `tr`          |
| 烏克蘭文      | `uk`          |
| 越南文      | `vi`          |
| 威爾斯文 | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>存取 Microsoft Translator 網站上的清單

如需快速一覽所有語言，Microsoft Translator 網站顯示了翻譯工具文字與語音 API 支援的所有語言。 這份清單不含開發人員特定資訊，例如語言代碼。

[查看語言清單](https://www.microsoft.com/translator/languages.aspx) \(英文\)
