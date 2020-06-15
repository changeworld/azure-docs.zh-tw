---
title: 語言支援-Translator
titleSuffix: Azure Cognitive Services
description: 認知服務 Translator 支援下列使用類神經機器翻譯（NMT）之文字到文字翻譯的語言。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: swmachan
ms.openlocfilehash: 7ddf556be584603ce3e7cb4a97eb4cc9ff63ec68
ms.sourcegitcommit: a8928136b49362448e992a297db1072ee322b7fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84718439"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>文字和語音翻譯的語言和區域支援

使用翻譯工具，從60種以上的文字翻譯語言轉譯成和。 類神經機器翻譯（NMT）是適用于高品質 AI 電腦翻譯的新標準，並可在類神經系統可用時，使用翻譯工具 V3 作為預設值。

您也可以搭配自訂翻譯使用 Translator 來建立類神經翻譯系統，以瞭解您自己的企業和產業中使用的術語，並透過 Microsoft 語音服務將語音翻譯新增至您的應用程式。

[深入了解機器翻譯的運作方式](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>文字翻譯
您可以使用轉譯作業，或翻譯工具中提供的任何語言來翻譯文字。 此 API 也會使用「偵測」作業來提供語言偵測、使用直譯作業音譯，以及使用「字典查閱」和「字典範例」作業來進行雙語字典。 以下列出每個作業的可用語言。 

### <a name="translate"></a>Translate

翻譯工具支援文字轉換文字的下列語言。 

[視圖轉譯作業參考檔](reference/v3-0-translate.md)

|Language|  語言代碼|
|:-----|:-----:|
|南非荷蘭文| `af`|
|阿拉伯文|    `ar`    |
|孟加拉文|    `bn`    |
|波士尼亞文 (拉丁文)|   `bs`    |
|保加利亞文| `bg`    |
|粵語 (繁體中文)|   `yue`|
|卡達隆尼亞文|   `ca`    |
|簡體中文|    `zh-Hans`|
|繁體中文|   `zh-Hant`       |
|克羅埃西亞文|  `hr`    |
|捷克文| `cs`    |
|丹麥文|    `da`        |
|荷蘭文| `nl`|
|英文|   `en`    |
|愛沙尼亞文|  `et`    |
|斐濟文|    `fj`    |
|菲律賓文|  `fil`   |
|芬蘭文|   `fi`    |
|法文|    `fr`    |
|德文|    `de`    |
|希臘文| `el`    |
|古吉拉特文|  `gu`    |
|海地克裏奧爾文|    `ht`        |
|Hebrew |`he`   |
|Hindi| `hi`    |
|白苗文| `mww`   |
|匈牙利文| `hu`    |
|冰島文| `is`    |
|印尼文|    `id`    |
|愛爾蘭文 | `ga`|
|義大利文|   `it`    |
|日文|  `ja`    |
|坎那達文|`kn`|
|哈薩克文|`kk`|
|史瓦希里文| `sw`    |
|克林貢文|   `tlh-Latn`  |
|克林貢文 (plqaD)|   `tlh-Piqd`  |
|韓文 |`ko`   |
|拉脫維亞文|   `lv`    |
|立陶宛文|    `lt`    |
|馬達加斯加文|  `mg`    |
|馬來文| `ms`        |
|馬來亞拉姆文| `ml` |
|馬爾他文|   `mt`    |
|毛利文| `mi`  |
|馬拉地文| `mr`  |
|挪威文| `nb`    |
|波斯文|   `fa`    |
|波蘭文|    `pl`    |
|葡萄牙文 (巴西)|   `pt-br` |
|葡萄牙文 (葡萄牙)| `pt-pt` |
|旁遮普文|`pa`|
|奎雷塔洛歐多米文|   `otq`   |
|羅馬尼亞文|  `ro`    |
|俄文|   `ru`    |
|薩摩亞文|    `sm`    |
|塞爾維亞文 (斯拉夫)|    `sr-Cyrl`|
|塞爾維亞文 (拉丁)|   `sr-Latn`       |
|斯洛伐克文|    `sk`    |
|斯洛維尼亞文| `sl`    |
|西班牙文|   `es`    |
|瑞典文|   `sv`    |
|大溪地文|  `ty`    |
|坦米爾文| `ta`    |
|泰盧固文|    `te`    |
|泰文|  `th`    |
|東加文|    `to`    |
|土耳其文|   `tr`        |
|烏克蘭文| `uk`    |
|烏都文|  `ur`    |
|越南文|    `vi`    |
|威爾斯文| `cy`    |
|猶加敦馬雅文|  `yua`   |

> [!NOTE]
> 語言代碼 `pt` 會預設為 `pt-br` ，葡萄牙文（巴西）。

### <a name="detect"></a>Detect

Translator 會偵測下列用於轉譯和音譯的語言。

[View 偵測作業參考檔](reference/v3-0-detect.md)

|Language|  語言代碼|
|:-----|:-----:|
|南非荷蘭文| `af`|
|阿拉伯文|    `ar`    |
|保加利亞文| `bg`    |
|卡達隆尼亞文|   `ca`    |
|簡體中文|    `zh-Hans`|
|繁體中文|   `zh-Hant`       |
|克羅埃西亞文|  `hr`    |
|捷克文| `cs`    |
|丹麥文|    `da`        |
|荷蘭文| `nl`|
|英文|   `en`    |
|愛沙尼亞文|  `et`    |
|芬蘭文|   `fi`    |
|法文|    `fr`    |
|德文|    `de`    |
|希臘文| `el`    |
|古吉拉特文|  `gu`    |
|海地克裏奧爾文|    `ht`        |
|Hebrew |`he`   |
|Hindi| `hi`    |
|匈牙利文| `hu`    |
|冰島文| `is`    |
|印尼文|    `id`    |
|愛爾蘭文 | `ga`|
|義大利文|   `it`    |
|日文|  `ja`    |
|史瓦希里文| `sw`    |
|克林貢文|   `tlh-Latn`  |
|韓文 |`ko`   |
|拉脫維亞文|   `lv`    |
|立陶宛文|    `lt`    |
|馬來文| `ms`        |
|馬爾他文|   `mt`    |
|挪威文| `nb`    |
|波斯文|   `fa`    |
|波蘭文|    `pl`    |
|葡萄牙文 (巴西)|   `pt-br` |
|葡萄牙文 (葡萄牙)| `pt-pt` |
|羅馬尼亞文|  `ro`    |
|俄文|   `ru`    |
|塞爾維亞文 (斯拉夫)|    `sr-Cyrl`|
|塞爾維亞文 (拉丁)|   `sr-Latn`       |
|斯洛伐克文|    `sk`    |
|斯洛維尼亞文| `sl`    |
|西班牙文|   `es`    |
|瑞典文|   `sv`    |
|大溪地文|  `ty`    |
|泰文|  `th`    |
|土耳其文|   `tr`        |
|烏克蘭文| `uk`    |
|烏都文|  `ur`    |
|越南文|    `vi`    |
|威爾斯文| `cy`    |
|猶加敦馬雅文|  `yua`   |

### <a name="transliterate"></a>Transliterate

轉換方法支援以下語言。 在「至/自」列中，「<-->」表示該語言後方所列的兩種文字可相互轉換。 「-->」表示該語言只能從某種文字轉寫成另一種文字。

[View 直譯 operation 參考檔](reference/v3-0-translate.md)


| Language    | 語言代碼 | 指令碼 | 去/從 | 指令碼|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| 阿拉伯文 | `ar` | 阿拉伯文 `Arab` | <--> | 拉丁文 `Latn` |
|孟加拉文  | `bn` | 孟加拉文 `Beng` | <--> | 拉丁文 `Latn` |
| 簡體中文 | `zh-Hans` | 簡體中文 `Hans`| <--> | 拉丁文 `Latn` |
| 簡體中文 | `zh-Hans` | 簡體中文 `Hans`| <--> | 繁體中文 `Hant`|
| 繁體中文 | `zh-Hant` | 繁體中文 `Hant`| <--> | 拉丁文 `Latn` |
| 繁體中文 | `zh-Hant` | 繁體中文 `Hant`| <--> | 簡體中文 `Hans` |
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

### <a name="dictionary"></a>字典

字典支援使用查閱與範例方法來查詢下列語言的英文譯文，或英文的下列語言譯文。

查看[字典查閱](reference/v3-0-dictionary-lookup.md)和[字典範例](reference/v3-0-dictionary-examples.md)作業的參考檔。

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

### <a name="access-the-translator-language-list-programmatically"></a>以程式設計方式存取翻譯工具語言清單

您可以使用 [語言] 方法來抓取翻譯工具的支援語言清單。 您可以依特色、語言代碼，以及該語言的英文名稱或任何其他受支援語言的名稱，來檢視清單。 當我們提供新語言時，Microsoft Translator 服務會自動更新此清單。

[檢視語言作業參考文件](reference/v3-0-languages.md)

## <a name="customization"></a>自訂

下列語言可使用[自訂翻譯](https://aka.ms/CustomTranslator)自訂或英文。

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

## <a name="speech-translation"></a>語音翻譯
語音翻譯可透過認知服務語音服務使用 Translator 來取得。 請參閱[語音服務檔](https://docs.microsoft.com/azure/cognitive-services/speech-service/)以深入瞭解如何使用語音翻譯，以及如何查看所有[可用的語言選項](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support)。

### <a name="speech-to-text"></a>語音轉文字
將語音轉換成文字，以便轉譯成您選擇的文字語言。 語音轉換文字用於語音轉換文字翻譯，或用於與語音合成搭配使用時的語音轉換語音翻譯。

| Language    |
|:----------- |
|阿拉伯文|
|粵語 (繁體中文)|
|卡達隆尼亞文|
|簡體中文|
|繁體中文|
|丹麥文|
|荷蘭文|
|英文|
|芬蘭文|
|法文|
|德文|
|古吉拉特文|
|Hindi|
|義大利文|
|日文|
|韓文|
|馬拉地文|
|挪威文|
|波蘭文|
|葡萄牙文 (巴西)|
|葡萄牙文 (葡萄牙)|
|俄文|
|西班牙文|
|瑞典文|
|坦米爾文|
|泰盧固文|
|泰文|
|土耳其文|

### <a name="text-to-speech"></a>文字轉換語音
將文字轉換成語音。 文字轉換語音是用來新增可聽見的翻譯結果輸出，或在與語音轉換文字搭配使用時，用於語音轉換語音翻譯。 

| Language    |
|:----------- |
|阿拉伯文|
|保加利亞文|
|粵語 (繁體中文)|
|卡達隆尼亞文|
|簡體中文|
|繁體中文|
|克羅埃西亞文|
|捷克文|
|丹麥文|
|荷蘭文|
|英文|
|芬蘭文|
|法文|
|德文|
|希臘文|
|Hebrew|
|Hindi|
|匈牙利文|
|印尼文|
|義大利文|
|日文|
|韓文|
|馬來文|
|挪威文|
|波蘭文|
|葡萄牙文 (巴西)|
|葡萄牙文 (葡萄牙)|
|羅馬尼亞文|
|俄文|
|斯洛伐克文|
|斯洛維尼亞文|
|西班牙文|
|瑞典文|
|坦米爾文|
|泰盧固文|
|泰文|
|土耳其文|
|越南文|

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>查看 Microsoft Translator 網站上的語言清單

若要快速流覽語言，Microsoft Translator 網站會顯示翻譯工具針對文字翻譯和語音服務進行語音翻譯所支援的所有語言。 這份清單不含開發人員特定資訊，例如語言代碼。

[查看語言清單](https://www.microsoft.com/translator/languages.aspx) \(英文\)
