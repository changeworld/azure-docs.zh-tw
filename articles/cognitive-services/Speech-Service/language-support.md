---
title: 語言支援-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務支援多種語言的語音轉換文字和文字轉換語音，以及語音翻譯。 本文提供服務功能的完整語言支援清單。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 3fe1b999fbdc03157778a1329e05e8c342183528
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587369"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>語音服務的語言和語音支援

語言支援會因語音服務功能而異。 下表摘要說明[語音轉換文字](#speech-to-text)、[文字到語音](#text-to-speech)[轉換和語音翻譯](#speech-translation)服務供應專案的語言支援。

## <a name="speech-to-text"></a>語音轉文字

Microsoft 語音 SDK 和 REST API 都支援下列語言（地區設定）。 若要改善正確性，請透過上傳音訊 + 人為標記的文字記錄或相關文字：句子，提供部分語言的自訂。 發音自訂目前僅適用于 `en-US` 和 `de-DE` 。 [在這裡](how-to-custom-speech.md)深入瞭解自訂。

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| 地區設定  | 語言                          | 支援 | 自訂                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | 阿拉伯文（阿拉伯聯合大公國）                      | 是       | 否                                                |
| `ar-BH` | 阿拉伯文（巴林），現代化標準 | 是       | 語言模型                                    |
| `ar-EG` | 阿拉伯文 (埃及)                    | 是       | 語言模型                                    |
| `ar-KW` | 阿拉伯文 (科威特)                   | 是       | 否                                                |
| `ar-QA` | 阿拉伯文 (卡達)                    | 是       | 否                                                |
| `ar-SA` | 阿拉伯文 (沙烏地阿拉伯)             | 是       | 否                                                |
| `ar-SY` | 阿拉伯文 (敘利亞)                    | 是       | 語言模型                                    |
| `ca-ES` | 卡達隆尼亞文                           | 是       | 語言模型                                    |
| `da-DK` | 丹麥文 (丹麥)                  | 是       | 語言模型                                    |
| `de-DE` | 德文 (德國)                  | 是       | 原音模型<br>語言模型<br>發音 |
| `en-AU` | 英文 (澳大利亞)               | 是       | 原音模型<br>語言模型                  |
| `en-CA` | 英文 (加拿大)                  | 是       | 原音模型<br>語言模型                  |
| `en-GB` | 英文 (英國)          | 是       | 原音模型<br>語言模型<br>發音 |
| `en-IN` | 英文 (印度)                   | 是       | 原音模型<br>語言模型                  |
| `en-NZ` | 英文 (紐西蘭)             | 是       | 原音模型<br>語言模型                  |
| `en-US` | 英文 (美國)           | 是       | 原音模型<br>語言模型<br>發音 |
| `es-ES` | 西班牙文 (西班牙)                   | 是       | 原音模型<br>語言模型                  |
| `es-MX` | 西班牙文 (墨西哥)                  | 是       | 原音模型<br>語言模型                  |
| `fi-FI` | 芬蘭文 (芬蘭)                 | 是       | 語言模型                                    |
| `fr-CA` | 法文 (加拿大)                   | 是       | 原音模型<br>語言模型                  |
| `fr-FR` | 法文 (法國)                   | 是       | 原音模型<br>語言模型<br>發音 |
| `gu-IN` | 古吉拉特文（印度）                 | 是       | 語言模型                                    |
| `hi-IN` | 印度文 (印度)                     | 是       | 原音模型<br>語言模型                  |
| `it-IT` | 義大利文 (義大利)                   | 是       | 原音模型<br>語言模型<br>發音 |
| `ja-JP` | 日文 (日本)                  | 是       | 語言模型                                    |
| `ko-KR` | 韓文 (韓國)                    | 是       | 語言模型                                    |
| `mr-IN` | 馬拉提文 (印度)                   | 是       | 語言模型                                    |
| `nb-NO` | 挪威文 (巴克摩) (挪威)       | 是       | 語言模型                                    |
| `nl-NL` | 荷蘭文 (荷蘭)               | 是       | 語言模型                                    |
| `pl-PL` | 波蘭文 (波蘭)                   | 是       | 語言模型                                    |
| `pt-BR` | 葡萄牙文 (巴西)               | 是       | 原音模型<br>語言模型<br>發音 |
| `pt-PT` | 葡萄牙文 (葡萄牙)             | 是       | 語言模型                                    |
| `ru-RU` | 俄文 (俄羅斯)                  | 是       | 原音模型<br>語言模型                  |
| `sv-SE` | 瑞典文 (瑞典)                  | 是       | 語言模型                                    |
| `ta-IN` | 坦米爾文 (印度)                     | 是       | 語言模型                                    |
| `te-IN` | 特拉古文 (印度)                    | 是       | 否                                                |
| `th-TH` | 泰文 (泰國)                   | 是       | 否                                                |
| `tr-TR` | 土耳其文 (土耳其)                  | 是       | 否                                                |
| `zh-CN` | 中文 (普通話，簡體)    | 是       | 原音模型<br>語言模型                  |
| `zh-HK` | 中文（廣東話，繁體）  | 是       | 語言模型                                    |
| `zh-TW` | 中文 (繁體，國語)      | 是       | 語言模型                                    |

## <a name="text-to-speech"></a>文字轉換語音

Microsoft 語音 SDK 和 REST Api 都支援這些語音，其中每個都支援地區設定所識別的特定語言和方言。 您也可以透過[語音/清單 API](rest-text-to-speech.md#get-a-list-of-voices)，取得每個特定區域/端點所支援的語言和語音的完整清單。 

> [!IMPORTANT]
> 標準、自訂和神經語音的定價各不相同。 如需其他資訊，請瀏覽[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)頁面。

### <a name="neural-voices"></a>神經語音

神經文字轉換語音是由深度神經網路驅動的新類型語音合成。 使用神經語音時，合成語音與人類錄音幾乎無法區分。

神經語音可以用來與聊天機器人和語音助理互動，使其更自然且吸引人，將數位文字（例如電子書）轉換成有聲書;，並增強汽車內流覽系統。 具有類似人類的自然韻律和清楚的文字清晰度，神經語音大幅降低使用者與 AI 系統互動時的聆聽疲勞。

如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

|地區設定  | 語言            | 性別 | 語音名稱  | Bi 語言支援 | 樣式支援 |
|--|--|--|--|--|--|
| `de-DE` | 德文 (德國)    | Female | "de-KatjaNeural" | 是。 英文 (美國) | 一般 |
| `en-AU` | 英文 (澳大利亞) | Female |  "en-us-NatashaNeural" | 否 | 一般 |
| `en-CA` | 英文 (加拿大)    | Female |  「en-CA-ClaraNeural」| 否 | 一般 |
| `en-GB` | 英文 (英國)        | Female |  "en-GB-LibbyNeural"| 否 | 一般 |
|  |      | Female |  "en-GB-MiaNeural" | 否 | 一般 |
| `en-US` | 英文 (美國)        | Female |  「en-us-AriaNeural」| 否 | 一般，可以使用多個語音樣式 |
|  |      | Male   | 「en-us-GuyNeural」 | 否 | 一般 |
| `es-ES` | 西班牙文 (西班牙)     | Female |  "es-ElviraNeural"| 是。 英文 (美國) | 一般 |
| `es-MX` | 西班牙文 (墨西哥)    | Female |  "es-MX-DaliaNeural" | 是。 英文 (美國) | 一般 |
| `fr-CA` | 法文 (加拿大)     | Female |  "fr-CA-SylvieNeural" | 是。 英文 (美國) | 一般 |
| `fr-FR` | 法文 (法國)     | Female | "fr-fr-DeniseNeural"  | 是。 英文 (美國) | 一般 |
| `it-IT` | 義大利文 (義大利)     | Female |  「it-IT-ElsaNeural」  | 是。 英文 (美國) | 一般 |
| `ja-JP` | 日文            | Female |  "ja-jp-NanamiNeural" | 是。 英文 (美國) | 一般 |
| `ko-KR` | 韓文              | Female |  "ko-KR-SunHiNeural" | 是。 英文 (美國) | 一般 |
| `nb-NO` | 挪威文           | Female | 「nb-不 IselinNeural」 | 否 | 一般 |
| `pt-BR` | 葡萄牙文 (巴西) | Female |  "pt-BR-FranciscaNeural" | 否 | 一般 |
| `tr-TR` | 土耳其文             | Female | "tr-TR-EmelNeural" | 否 | 一般 |
| `zh-CN` | 中文 (普通話，簡體)  | Female |  "zh-CN-XiaoxiaoNeural"  | 是。 英文 (美國) | 一般，可以使用多個語音樣式 |
|  |  | Female |  "zh-CN-XiaoyouNeural"  | 是。 英文 (美國) | 兒童心聲，針對案例 narrating 優化 |
|  |  | Male |  "zh-CN-YunyangNeural"  | 是。 英文 (美國) | 已針對新聞閱讀優化，提供多種語音樣式 |
|  |  | Male |  "zh-CN-YunyeNeural"  | 否 | 已針對案例 narrating 優化 |

> [!IMPORTANT]
> `en-US-JessaNeural`語音已變更為 `en-US-AriaNeural` 。 如果您之前使用 "Jessa"，請將轉換成「Aria」。

若要瞭解如何設定和調整類神經語音，請參閱[語音合成標記語言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!TIP]
> 您可以在語音合成要求中繼續使用完整的服務名稱對應，例如 "Microsoft Server Speech 文字轉換語音 Voice （en-us，AriaNeural）"。

### <a name="standard-voices"></a>標準語音

以超過 45 個語言和地區設定提供 75 個以上的標準語音，可讓您將文字轉換為合成語音。 如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

| 地區設定 | 語言 | 性別 | 語音名稱 |
|--|--|--|--|
| <sup>sha-1</sup>`ar-EG` | 阿拉伯文 (埃及) | Female | "ar-例如-Hoda" |
| `ar-SA` | 阿拉伯文 (沙烏地阿拉伯) | Male | "ar-SA-Naayf" |
| `bg-BG` | 保加利亞文 | Male |  "bg-BG-Ivan" |
| `ca-ES` | 加泰蘭文 (西班牙) | Female |  「ca-ES-HerenaRUS」 |
| `cs-CZ` | 捷克文 | Male | "cs-CZ-Jakub" |
| `da-DK` | 丹麥文 | Female |  "da-深色-HelleRUS" |
| `de-AT` | 德文 (奧地利) | Male | 「Michael」 |
| `de-CH` | 德文 (瑞士) | Male |  「Karsten」 |
| `de-DE` | 德文 (德國) | Female |  "de-Hedda" |
|  |  | Female | "de-HeddaRUS" |
|  |  | Male |  「取消 Stefan-Apollo」 |
| `el-GR` | 希臘文 | Male | "el-GR-Stefanos" |
| `en-AU` | 英文 (澳大利亞) | Female |  "en-us-Catherine" |
|  |  | Female |  "en-us-HayleyRUS" |
| `en-CA` | 英文 (加拿大) | Female |  「en-CA-Linda」 |
|  |  | Female |  「en-CA-HeatherRUS」 |
| `en-GB` | 英文 (英國) | Female |  "en-us-Susan-Apollo" |
|  |  | Female |  "en-GB-HazelRUS" |
|  |  | Male |  "George-Apollo" |
| `en-IE` | 英文 (愛爾蘭) | Male | 「en-IE-小紅」 |
| `en-IN` | 英文 (印度) | Female | "en-us-Heera-Apollo" |
|  |  | Female |  「PriyaRUS」 |
|  |  | Male |  "en-us-Ravi-Apollo" |
| `en-US` | 英文 (美國) | Female |  「en-us-ZiraRUS」 |
|  |  | Female | 「en-us-AriaRUS」 |
|  |  | Male | 「en-us-BenjaminRUS」 |
|  |  | Male |  「en-us-Guy24kRUS」 |
| `es-ES` | 西班牙文 (西班牙) | Female |  "es-劉娜-Apollo" |
|  |  | Female | "es-HelenaRUS" |
|  |  | Male | "es-Pablo-Apollo" |
| `es-MX` | 西班牙文 (墨西哥) | Female |  "es-MX-HildaRUS" |
|  |  | Male | "es-MX-Raul-Apollo" |
| `fi-FI` | 芬蘭文 | Female | 「wi-fi-HeidiRUS」 |
| `fr-CA` | 法文 (加拿大) | Female | "fr-CA-Caroline" |
|  |  | Female | "fr-CA-HarmonieRUS" |
| `fr-CH` | 法文 (瑞士) | Male | "fr-CH-Guillaume" |
| `fr-FR` | 法文 (法國) | Female |  "fr-fr-Julie-Apollo" |
|  |  | Female |"fr-fr-HortenseRUS" |
|  |  | Male |  "fr-fr-Paul-Apollo" |
| `he-IL` | 希伯來文 (以色列) | Male |  「他-IL-Asaf」 |
| `hi-IN` | 印度文 (印度) | Female | 「hi Kalpana-Apollo」 |
|  |  | Female |  「hi Kalpana」 |
|  |  | Male |  「hi Hemant」 |
| `hr-HR` | 克羅埃西亞文 | Male | 「hr-HR-Matej」 |
| `hu-HU` | 匈牙利文 | Male |  "hu-HU-Szabolcs" |
| `id-ID` | 印尼文 | Male | "id-ID-Andika" |
| `it-IT` | 義大利文 | Male |  "it-Cosimo-Apollo" |
|  |  | Female |  「it-IT-LuciaRUS」 |
| `ja-JP` | 日文 | Female |  "ja-jp-Ayumi-Apollo" |
|  |  | Male | "ja-jp-Ichiro-Apollo" |
|  |  | Female |  "ja-jp-HarukaRUS" |
| `ko-KR` | 韓文 | Female | "ko-KR-HeamiRUS" |
| `ms-MY` | 馬來文 | Male |  "ms-MY-Rizwan" |
| `nb-NO` | 挪威文 | Female |  「nb-不 HuldaRUS」 |
| `nl-NL` | 荷蘭文 | Female |  "nl-NL-HannaRUS" |
| `pl-PL` | 波蘭文 | Female |  "pl-PL-PaulinaRUS" |
| `pt-BR` | 葡萄牙文 (巴西) | Female | "pt-BR-HeloisaRUS" |
|  |  | Male |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | 葡萄牙文 (葡萄牙) | Female | "pt-PT-HeliaRUS" |
| `ro-RO` | 羅馬尼亞文 | Male | "ro-RO-Andrei" |
| `ru-RU` | 俄文 | Female |  「ru-RU-Irina-Apollo」 |
|  |  | Male | 「ru-RU-Pavel-Apollo」 |
|  |  | Female |  ru-RU-EkaterinaRUS |
| `sk-SK` | 斯洛伐克文 | Male | "sk-SK-Filip" |
| `sl-SI` | 斯洛維尼亞文 | Male |  "sl-SI-Lado" |
| `sv-SE` | 瑞典文 | Female | "sv-SE-HedvigRUS" |
| `ta-IN` | 坦米爾文 (印度) | Male |  < Valluvar 中的 "ta" |
| `te-IN` | 特拉古文 (印度) | Female |  "Chitra" |
| `th-TH` | 泰文 | Male |  「第一次 Pattara」 |
| `tr-TR` | 土耳其文 (土耳其) | Female | "tr-TR-SedaRUS" |
| `vi-VN` | 越南文 | Male |  「vi-VN-a」 |
| `zh-CN` | 中文 (普通話，簡體) | Female |  "zh-CN-HuihuiRUS" |
|  |  | Female | "zh-CN-Yaoyao-Apollo" |
|  |  | Male | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | 中文（廣東話，繁體） | Female |  「zh-HK-Tracy-Apollo」 |
|  |  | Female | "zh-HK-TracyRUS" |
|  |  | Male |  「zh-HK-Danny-Apollo」 |
| `zh-TW` | 中文 (繁體，國語) | Female |  "zh-幼圓-Yating-Apollo" |
|  |  | Female | "zh-幼圓-HanHanRUS" |
|  |  | Male |  "zh-幼圓-Zhiwei-Apollo" |

**1** *ar-例如，支援新式標準阿拉伯文（MSA）。*

> [!IMPORTANT]
> `en-US-Jessa`語音已變更為 `en-US-Aria` 。 如果您之前使用 "Jessa"，請將轉換成「Aria」。

> [!TIP]
> 您可以在語音合成要求中繼續使用完整的服務名稱對應，例如 "Microsoft Server Speech 文字轉換語音 Voice （en-us，AriaRUS）"。

### <a name="customization"></a>自訂

語音自訂適用于 `de-DE` 、 `en-GB` 、 `en-IN` 、 `en-US` 、、、、 `es-MX` `fr-FR` `it-IT` `pt-BR` 和 `zh-CN` 。 選取適當的地區設定，以符合您必須訓練自訂語音模型的定型資料。 例如，如果您的記錄資料是以英式輔的英文來說，請選取 `en-GB` 。

> [!NOTE]
> 除了中文-英文 bi 語言，我們不支援自訂語音中的 bi 語言模型定型。 如果您想要訓練中文語音（也可以說英文），請選取 [中文-英文雙語]。 所有地區設定的語音訓練都是以 2000 + 語句的資料集開始，除了 `en-US` 和以外， `zh-CN` 您可以從任何規模的定型資料開始。

## <a name="speech-translation"></a>語音翻譯

**語音翻譯** API 支援語音轉換語音和語音轉換文字翻譯的不同語言。 來源語言一律必須來自語音轉換文字語言表。 可用的目標語言取決於翻譯目標是語音還是文字。 您可以將傳入的語音翻譯為 [60 多種語言](https://www.microsoft.com/translator/business/languages/)。 語言子集可用於[語音合成](language-support.md#text-languages)。

### <a name="text-languages"></a>文字語言

| 文字語言           | 語言代碼 |
|:------------------------|:-------------:|
| 南非荷蘭文               | `af`          |
| 阿拉伯文                  | `ar`          |
| 孟加拉文                  | `bn`          |
| 波士尼亞文 (拉丁文)         | `bs`          |
| 保加利亞文               | `bg`          |
| 粵語 (繁體中文) | `yue`         |
| 卡達隆尼亞文                 | `ca`          |
| 簡體中文      | `zh-Hans`     |
| 繁體中文     | `zh-Hant`     |
| 克羅埃西亞文                | `hr`          |
| 捷克文                   | `cs`          |
| 丹麥文                  | `da`          |
| 荷蘭文                   | `nl`          |
| 英文                 | `en`          |
| 愛沙尼亞文                | `et`          |
| 斐濟文                  | `fj`          |
| 菲律賓文                | `fil`         |
| 芬蘭文                 | `fi`          |
| 法文                  | `fr`          |
| 德文                  | `de`          |
| 希臘文                   | `el`          |
| 古吉拉特文                | `gu`          |
| 海地克裏奧爾文          | `ht`          |
| 希伯來文                  | `he`          |
| Hindi                   | `hi`          |
| 白苗文               | `mww`         |
| 匈牙利文               | `hu`          |
| 印尼文              | `id`          |
| 愛爾蘭文                   | `ga`          |
| 義大利文                 | `it`          |
| 日文                | `ja`          |
| 坎那達文                 | `kn`          |
| 史瓦希里文               | `sw`          |
| 克林貢文                 | `tlh`         |
| 克林貢文 (plqaD)         | `tlh-Qaak`    |
| 韓文                  | `ko`          |
| 拉脫維亞文                 | `lv`          |
| 立陶宛文              | `lt`          |
| 馬達加斯加文                | `mg`          |
| 馬來文                   | `ms`          |
| 馬來亞拉姆文               | `ml`          |
| 馬爾他文                 | `mt`          |
| 毛利文                   | `mi`          |
| 馬拉地文                 | `mr`          |
| 挪威文               | `nb`          |
| 波斯文                 | `fa`          |
| 波蘭文                  | `pl`          |
| 葡萄牙文 (巴西)     | `pt-br`       |
| 葡萄牙文 (葡萄牙)   | `pt-pt`       |
| 旁遮普文                 | `pa`          |
| 奎雷塔洛歐多米文         | `otq`         |
| 羅馬尼亞文                | `ro`          |
| 俄文                 | `ru`          |
| 薩摩亞文                  | `sm`          |
| 塞爾維亞文 (斯拉夫)      | `sr-Cyrl`     |
| 塞爾維亞文 (拉丁)         | `sr-Latn`     |
| 斯洛伐克文                  | `sk`          |
| 斯洛維尼亞文               | `sl`          |
| 西班牙文                 | `es`          |
| 瑞典文                 | `sv`          |
| 大溪地文                | `ty`          |
| 坦米爾文                   | `ta`          |
| 泰盧固文                  | `te`          |
| 泰文                    | `th`          |
| 東加文                  | `to`          |
| 土耳其文                 | `tr`          |
| 烏克蘭文               | `uk`          |
| 烏都文                    | `ur`          |
| 越南文              | `vi`          |
| 威爾斯文                   | `cy`          |
| 猶加敦馬雅文            | `yua`         |

## <a name="next-steps"></a>後續步驟

* [取得您的語音服務試用版訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp) (英文)
