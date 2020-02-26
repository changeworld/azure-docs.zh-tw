---
title: 語言支援-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務支援多種語言的語音轉換文字和文字轉換語音，以及語音翻譯。 本文提供服務功能的完整語言支援清單。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 86224508b9f24906abce157b92a77c0a2f24d882
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605725"
---
# <a name="language-and-region-support-for-the-speech-service"></a>語音服務的語言和區域支援

語言支援會因語音服務功能而異。 下表摘要說明[語音轉換文字](#speech-to-text)、[文字到語音](#text-to-speech)[轉換和語音翻譯](#speech-translation)服務供應專案的語言支援。

## <a name="speech-to-text"></a>語音轉文字

Microsoft 語音 SDK 和 REST API 都支援下列語言（地區設定）。 若要改善正確性，請透過上傳音訊 + 人為標記的文字記錄或相關文字：句子，提供部分語言的自訂。 發音自訂目前僅適用于 `en-US` 和 `de-DE`。 [在這裡](how-to-custom-speech.md)深入瞭解自訂。

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

 Locale | Language | 支援 | 自訂
------|------------|-----------|-------------
`ar-AE` | 阿拉伯文（阿拉伯聯合大公國） | 是 | 否
`ar-BH` | 阿拉伯文 (巴林) | 是 | 語言模型
`ar-EG` | 阿拉伯文 (埃及)，現代標準 | 是 | 語言模型
`ar-KW` | 阿拉伯文 (科威特) | 是 | 否
`ar-QA` | 阿拉伯文 (卡達) | 是 | 否
`ar-SA` | 阿拉伯文 (沙烏地阿拉伯) | 是 | 否
`ca-ES` | 卡達隆尼亞文 | 是 | 語言模型
`da-DK` | 丹麥文 (丹麥) | 是 | 語言模型
`de-DE` | 德文 (德國) | 是 | 原音模型<br>語言模型<br>發音
`en-AU` | 英文 (澳大利亞) | 是 | 原音模型<br>語言模型
`en-CA` | 英文 (加拿大) | 是 | 原音模型<br>語言模型
`en-GB` | 英文 (英國) | 是 | 原音模型<br>語言模型<br>發音
`en-IN` | 英文 (印度) | 是 | 原音模型<br>語言模型
`en-NZ` | 英文 (紐西蘭) | 是 | 原音模型<br>語言模型
`en-US` | 英文 (美國) | 是 | 原音模型<br>語言模型<br>發音
`es-ES` | 西班牙文 (西班牙) | 是 | 原音模型<br>語言模型
`es-MX` | 西班牙文 (墨西哥) | 是 | 原音模型<br>語言模型
`fi-FI` | 芬蘭文 (芬蘭) | 是 | 語言模型
`fr-CA` | 法文 (加拿大) | 是 | 原音模型<br>語言模型
`fr-FR` | 法文 (法國) | 是 | 原音模型<br>語言模型<br>發音
`gu-IN` | 古吉拉特文（印度） | 是 | 語言模型
`hi-IN` | 印度文 (印度) | 是 | 原音模型<br>語言模型
`it-IT` | 義大利文 (義大利) | 是 | 原音模型<br>語言模型<br>發音
`ja-JP` | 日文 (日本) | 是 | 語言模型
`ko-KR` | 韓文 (韓國) | 是 | 語言模型
`mr-IN` | 馬拉提文 (印度) | 是 | 語言模型
`nb-NO` | 挪威文 (巴克摩) (挪威) | 是 | 語言模型
`nl-NL` | 荷蘭文 (荷蘭) | 是 | 語言模型
`pl-PL` | 波蘭文 (波蘭) | 是 | 語言模型
`pt-BR` | 葡萄牙文 (巴西) | 是 | 原音模型<br>語言模型<br>發音
`pt-PT` | 葡萄牙文 (葡萄牙) | 是 | 語言模型
`ru-RU` | 俄文 (俄羅斯) | 是 | 原音模型<br>語言模型
`sv-SE` | 瑞典文 (瑞典) | 是 | 語言模型
`ta-IN` | 坦米爾文 (印度) | 是 | 語言模型
`te-IN` | 特拉古文 (印度) | 是 | 否
`th-TH` | 泰文 (泰國) | 是 | 否
`tr-TR` | 土耳其文 (土耳其) | 是 | 否
`zh-CN` | 中文 (普通話，簡體) | 是 | 原音模型<br>語言模型
`zh-HK` | 中文（廣東話，繁體） | 是 | 語言模型
`zh-TW` | 中文 (繁體，國語) | 是 | 語言模型

## <a name="text-to-speech"></a>文字轉換語音

Microsoft 語音 SDK 和 REST Api 都支援這些語音，其中每個都支援地區設定所識別的特定語言和方言。

> [!IMPORTANT]
> 標準、自訂和神經語音的定價各不相同。 如需其他資訊，請瀏覽[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)頁面。

### <a name="neural-voices"></a>神經語音

神經文字轉換語音是由深度神經網路驅動的新類型語音合成。 使用神經語音時，合成語音與人類錄音幾乎無法區分。

神經語音可以用來與聊天機器人和語音助理互動，使其更自然且吸引人，將數位文字（例如電子書）轉換成有聲書;，並增強汽車內流覽系統。 具有類似人類的自然韻律和清楚的文字清晰度，神經語音大幅降低使用者與 AI 系統互動時的聆聽疲勞。

如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

Locale | Language | 性別 | 完整服務名稱對應 | 簡短的語音名稱
--------|----------|--------|---------|------------
`de-DE` | 德文 (德國) | Female | "Microsoft Server Speech 文字轉換語音 Voice （de，KatjaNeural）" | "de-KatjaNeural"
`en-US` | 英文 (美國) | Female | "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)" | 「en-us-JessaNeural」
`en-US` | 英文 (美國) | Male | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)" | 「en-us-GuyNeural」
`it-IT` | 義大利文 (義大利) | Female |"Microsoft Server Speech 文字轉換語音 Voice （it-IT，ElsaNeural）" | 「it-IT-ElsaNeural」
`pt-BR` | 葡萄牙文 (巴西) | Female | "Microsoft Server Speech 文字轉換語音 Voice （pt-BR，FranciscaNeural）" | "pt-BR-FranciscaNeural"
`zh-CN` | 中文 (大陸) | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

若要瞭解如何設定和調整類神經語音，請參閱[語音合成標記語言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!NOTE]
> 您可以在語音合成要求中使用完整的服務名稱對應或簡短的語音名稱。

### <a name="standard-voices"></a>標準語音

以超過 45 個語言和地區設定提供 75 個以上的標準語音，可讓您將文字轉換為合成語音。 如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

Locale | Language | 性別 | 完整服務名稱對應 | 簡短名稱
-------|----------|---------|----------|----------
<sup>1</sup>`ar-EG` | 阿拉伯文 (埃及) | Female | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" | "ar-例如-Hoda"
`ar-SA` | 阿拉伯文 (沙烏地阿拉伯) | Male | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
`bg-BG` | 保加利亞文 | Male | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)" | "bg-BG-Ivan"
`ca-ES` | 加泰蘭文 (西班牙) | Female | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | 「ca-ES-HerenaRUS」
`cs-CZ` | 捷克文 | Male | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub"
`da-DK` | 丹麥文 | Female | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-深色-HelleRUS"
`de-AT` | 德文 (奧地利) | Male | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | 「Michael」
`de-CH` | 德文 (瑞士) | Male | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)" | 「Karsten」
`de-DE` | 德文 (德國) | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "de-Hedda"
| | | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-HeddaRUS"
| | | Male | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | 「取消 Stefan-Apollo」
`el-GR` | 希臘文 | Male | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos"
`en-AU` | 英文 (澳大利亞) | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)" | "en-us-Catherine"
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "en-us-HayleyRUS"
`en-CA` | 英文 (加拿大) | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)" | 「en-CA-Linda」
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)" | 「en-CA-HeatherRUS」
`en-GB` | 英文 (英國) | Female | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-us-Susan-Apollo"
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Male | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)" | "George-Apollo"
`en-IE` | 英文 (愛爾蘭) | Male | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)" | 「en-IE-小紅」
`en-IN` | 英文 (印度) | Female | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)" | "en-us-Heera-Apollo"
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)" | 「PriyaRUS」
| | | Male | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "en-us-Ravi-Apollo"
`en-US` | 英文 (美國) | Female | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)" | 「en-us-ZiraRUS」
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)" | 「en-us-JessaRUS」
| | | Male | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)" | 「en-us-BenjaminRUS」
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" | 「en-us-Jessa24kRUS」
| | | Male | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)" | 「en-us-Guy24kRUS」
`es-ES` | 西班牙文 (西班牙) |Female | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-劉娜-Apollo"
| | | Female | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-HelenaRUS"
| | | Male | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-Pablo-Apollo"
`es-MX` | 西班牙文 (墨西哥) | Female | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Male | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | 芬蘭文 | Female | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | 「wi-fi-HeidiRUS」
`fr-CA` | 法文 (加拿大) |Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | 法文 (瑞士)| Male | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | 法文 (法國)| Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-fr-Julie-Apollo"
| | | Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-fr-HortenseRUS"
| | | Male | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-fr-Paul-Apollo"
`he-IL` | 希伯來文 (以色列) | Male| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)" | 「他-IL-Asaf」
`hi-IN` | 印度文 (印度) | Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)" | 「hi Kalpana-Apollo」
| | | Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)" | 「hi Kalpana」
| | | Male | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)" | 「hi Hemant」
`hr-HR` | 克羅埃西亞文 | Male | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)" | 「hr-HR-Matej」
`hu-HU` | 匈牙利文 | Male | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | 印尼文| Male | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)" | "id-ID-Andika"
`it-IT` | 義大利文 | Male | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" | "it-Cosimo-Apollo"
| | | Female | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)" | 「it-IT-LuciaRUS」
`ja-JP` | 日文 | Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-jp-Ayumi-Apollo"
| | | Male | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-jp-Ichiro-Apollo"
| | | Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-jp-HarukaRUS"
`ko-KR` | 韓文 | Female | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | 馬來文 | Male | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)" | "ms-MY-Rizwan"
`nb-NO` | 挪威文 | Female | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | 「nb-不 HuldaRUS」
`nl-NL` | 荷蘭文 | Female | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | 波蘭文 | Female | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | 葡萄牙文 (巴西) | Female | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Male |"Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
`pt-PT` | 葡萄牙文 (葡萄牙) | Female | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
`ro-RO` | 羅馬尼亞文 | Male | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)" | "ro-RO-Andrei"
`ru-RU` |俄文| Female | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | 「ru-RU-Irina-Apollo」
| | | Male | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | 「ru-RU-Pavel-Apollo」
| | | Female | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
`sk-SK` | 斯洛伐克文 | Male | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip"
`sl-SI` | 斯洛維尼亞文 | Male | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)" | "sl-SI-Lado"
`sv-SE` | 瑞典文 | Female | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
`ta-IN` | 坦米爾文 (印度) | Male | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)" | < Valluvar 中的 "ta"
`te-IN` | 特拉古文 (印度) | Female | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)" | "Chitra"
`th-TH` | 泰文 | Male | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | 「第一次 Pattara」
`tr-TR` | 土耳其文 (土耳其) | Female | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
`vi-VN` | 越南文 | Male | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)" | 「vi-VN-a」
`zh-CN` | 中文 (大陸) | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Male | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | 中文 (香港) | Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | 「zh-HK-Tracy-Apollo」
| | | Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Male | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | 「zh-HK-Danny-Apollo」
`zh-TW` | 中文 (台灣) | Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-幼圓-Yating-Apollo"
| | | Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-幼圓-HanHanRUS"
| | | Male | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-幼圓-Zhiwei-Apollo"

**1** *ar-例如，支援新式標準阿拉伯文（MSA）。*

> [!NOTE]
> 您可以在語音合成要求中使用完整的服務名稱對應或簡短的語音名稱。

### <a name="customization"></a>自訂

語音自訂適用于 `de-DE`、`en-GB`、`en-IN`、`en-US`、`es-MX`、`fr-FR`、`it-IT`、`pt-BR`和 `zh-CN`。 選取適當的地區設定，以符合您必須訓練自訂語音模型的定型資料。 例如，如果您的記錄資料是以英式輔的英文來說，請選取 [`en-GB`]。

> [!NOTE]
> 除了中文-英文 bi 語言，我們不支援自訂語音中的 bi 語言模型定型。 如果您想要訓練中文語音（也可以說英文），請選取 [中文-英文雙語]。 所有地區設定的語音訓練都是以 2000 + 語句的資料集開始，除了 `en-US` 和 `zh-CN`，您可以從任何規模的定型資料開始。

## <a name="speech-translation"></a>語音翻譯

**語音翻譯** API 支援語音轉換語音和語音轉換文字翻譯的不同語言。 來源語言一律必須來自語音轉換文字語言表。 可用的目標語言取決於翻譯目標是語音還是文字。 您可以將傳入的語音翻譯為 [60 多種語言](https://www.microsoft.com/translator/business/languages/)。 語言子集可用於[語音合成](language-support.md#text-languages)。

### <a name="text-languages"></a>文字語言

| 文字語言    | 語言代碼 |
|:----------- |:-------------:|
| 南非荷蘭文      | `af`          |
| 阿拉伯文       | `ar`          |
| 孟加拉文      | `bn`          |
| 波士尼亞文 (拉丁文)      | `bs`          |
| 保加利亞文      | `bg`          |
| 粵語 (繁體中文)      | `yue`          |
| 卡達隆尼亞文      | `ca`          |
| 簡體中文      | `zh-Hans`          |
| 繁體中文      | `zh-Hant`          |
| 克羅埃西亞文      | `hr`          |
| 捷克文      | `cs`          |
| 丹麥文      | `da`          |
| 荷蘭文      | `nl`          |
| 英文      | `en`          |
| 愛沙尼亞文      | `et`          |
| 斐濟文      | `fj`          |
| 菲律賓文      | `fil`          |
| 芬蘭文      | `fi`          |
| 法文      | `fr`          |
| 德文      | `de`          |
| 希臘文      | `el`          |
| 海地克裏奧爾文      | `ht`          |
| Hebrew      | `he`          |
| Hindi      | `hi`          |
| 白苗文      | `mww`          |
| 匈牙利文      | `hu`          |
| 印尼文      | `id`          |
| 愛爾蘭文      | `ga`          |
| 義大利文      | `it`          |
| 日文      | `ja`          |
| 坎那達文      | `kn`          |
| 史瓦希里文      | `sw`          |
| 克林貢文      | `tlh`          |
| 克林貢文 (plqaD)      | `tlh-Qaak`          |
| 韓文      | `ko`          |
| 拉脫維亞文      | `lv`          |
| 立陶宛文      | `lt`          |
| 馬達加斯加文      | `mg`          |
| 馬來文      | `ms`          |
| 馬來亞拉姆文      | `ml`          |
| 馬爾他文      | `mt`          |
| 挪威文      | `nb`          |
| 波斯文      | `fa`          |
| 波蘭文      | `pl`          |
| 葡萄牙文 (巴西)      | `pt-br`          |
| 葡萄牙文 (葡萄牙)      | `pt-pt`          |
| 旁遮普文      | `pa`          |
| 奎雷塔洛歐多米文      | `otq`          |
| 羅馬尼亞文      | `ro`          |
| 俄文      | `ru`          |
| 薩摩亞文      | `sm`          |
| 塞爾維亞文 (斯拉夫)      | `sr-Cyrl`          |
| 塞爾維亞文 (拉丁)      | `sr-Latn`          |
| 斯洛伐克文     | `sk`          |
| 斯洛維尼亞文      | `sl`          |
| 西班牙文      | `es`          |
| 瑞典文      | `sv`          |
| 大溪地文      | `ty`          |
| 坦米爾文      | `ta`          |
| 泰盧固文      | `te`          |
| 泰文      | `th`          |
| 東加文      | `to`          |
| 土耳其文      | `tr`          |
| 烏克蘭文      | `uk`          |
| 烏都文      | `ur`          |
| 越南文      | `vi`          |
| 威爾斯文      | `cy`          |
| 猶加敦馬雅文      | `yua`          |


## <a name="next-steps"></a>後續步驟

* [取得您的語音服務試用版訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp) (英文)
