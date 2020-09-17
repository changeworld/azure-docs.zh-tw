---
title: 語言支援 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務支援多種語言的語音轉換文字、文字轉換語音，以及語音翻譯。 本文依服務功能提供語言支援的完整清單。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 9a1745aa0e78dafca70ea90d4a5ac92f340312b3
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706927"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>語音服務的語言和語音支援

語言支援會因語音服務功能而有所不同。 下表摘要說明[語音轉換文字](#speech-to-text)、[文字轉換語音](#text-to-speech)，以及 [語音翻譯](#speech-translation)服務供應項目的語言支援。

## <a name="speech-to-text"></a>語音轉文字

Microsoft Speech SDK 和 REST API 均支援下列語言 (地區設定)。 

為了提升正確性，請透過上傳**音訊 + 人為標記文字記錄**或**相關的文字：句子**，為部分語言提供自訂。 若要深入了解自訂，請參閱[開始使用自訂語音](how-to-custom-speech.md)。

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Locale  | Language                          | 自訂                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | 阿拉伯文 (阿拉伯酋長國)      | 語言模型                                    |
| `ar-BH` | 阿拉伯文 (巴林)，現代標準 | 語言模型                                    |
| `ar-EG` | 阿拉伯文 (埃及)                    | 語言模型                                    |
| `ar-IL` | 阿拉伯文 (以色列)                   | 否                                                |
| `ar-IQ` | 阿拉伯文 (伊拉克)                     | 語言模型                                    |
| `ar-JO` | 阿拉伯文 (約旦)                   | 語言模型                                    |
| `ar-KW` | 阿拉伯文 (科威特)                   | 語言模型                                    |
| `ar-LB` | 阿拉伯文 (黎巴嫩)                  | 語言模型                                    |
| `ar-OM` | 阿拉伯文 (阿曼)                     | 語言模型                                    |
| `ar-PS` | 巴勒斯坦) 的阿拉伯文 (狀態       | 否                                                |
| `ar-QA` | 阿拉伯文 (卡達)                    | 語言模型                                    |
| `ar-SA` | 阿拉伯文 (沙烏地阿拉伯)             | 語言模型                                    |
| `ar-SY` | 阿拉伯文 (敘利亞)                    | 語言模型                                    |
| `bg-BG` | 保加利亞文 (保加利亞)              | 語言模型                                    |
| `ca-ES` | 加泰蘭文 (西班牙)                   | 語言模型                                    |
| `cs-CZ` | 捷克文 (捷克共和國)            | 語言模型                                    | 
| `da-DK` | 丹麥文 (丹麥)                  | 語言模型                                    |
| `de-DE` | 德文 (德國)                  | 原音模型<br>語言模型<br>發音 |
| `el-GR` | 希臘文 (希臘)                    | 語言模型                                    |
| `en-AU` | 英文 (澳大利亞)               | 原音模型<br>語言模型                  |
| `en-CA` | 英文 (加拿大)                  | 原音模型<br>語言模型                  |
| `en-GB` | 英文 (英國)          | 原音模型<br>語言模型<br>發音 |
| `en-HK` | 英文 (香港特別行政區)                | 語言模型                                    | 
| `en-IE` | 英文 (愛爾蘭)                 | 語言模型                                    | 
| `en-IN` | 英文 (印度)                   | 原音模型<br>語言模型                  |
| `en-NZ` | 英文 (紐西蘭)             | 原音模型<br>語言模型                  |
| `en-PH` | 英文 (菲律賓)             | 語言模型                                    | 
| `en-SG` | 英文 (新加坡)               | 語言模型                                    | 
| `en-US` | 英文 (美國)           | 原音模型<br>語言模型<br>發音 |
| `en-ZA` | 英文 (南非)            | 語言模型                                    | 
| `es-AR` | 西班牙文 (阿根廷)               | 語言模型                                    | 
| `es-BO` | 西班牙文 (玻利維亞)                 | 語言模型                                    | 
| `es-CL` | 西班牙文 (智利)                   | 語言模型                                    | 
| `es-CO` | 西班牙文 (哥倫比亞)                | 語言模型                                    | 
| `es-CR` | 西班牙文 (哥斯大黎加)              | 語言模型                                    | 
| `es-CU` | 西班牙文 (古巴)                     | 語言模型                                    | 
| `es-DO` | 西班牙文 (多明尼加)      | 語言模型                                    | 
| `es-EC` | 西班牙文 (厄瓜多)                 | 語言模型                                    | 
| `es-ES` | 西班牙文 (西班牙)                   | 原音模型<br>語言模型                  |
| `es-GT` | 西班牙文 (瓜地馬拉)               | 語言模型                                    | 
| `es-HN` | 西班牙文 (宏都拉斯)                | 語言模型                                    | 
| `es-MX` | 西班牙文 (墨西哥)                  | 原音模型<br>語言模型                  |
| `es-NI` | 西班牙文 (尼加拉瓜)               | 語言模型                                    | 
| `es-PA` | 西班牙文 (巴拿馬)                  | 語言模型                                    | 
| `es-PE` | 西班牙文 (秘魯)                    | 語言模型                                    | 
| `es-PR` | 西班牙文 (波多黎各)             | 語言模型                                    | 
| `es-PY` | 西班牙文 (巴拉圭)                | 語言模型                                    | 
| `es-SV` | 西班牙文 (薩爾瓦多)             | 語言模型                                    | 
| `es-US` | 美國西班牙文 (USA)                      | 語言模型                                    | 
| `es-UY` | 西班牙文 (烏拉圭)                 | 語言模型                                    | 
| `es-VE` | 西班牙文 (委內瑞拉)               | 語言模型                                    |
| `et-EE` | 愛沙尼亞 (愛沙尼亞)                  | 語言模型                                    | 
| `fi-FI` | 芬蘭文 (芬蘭)                 | 語言模型                                    |
| `fr-CA` | 法文 (加拿大)                   | 原音模型<br>語言模型                  |
| `fr-FR` | 法文 (法國)                   | 原音模型<br>語言模型<br>發音 |
| `ga-IE` | 愛爾蘭 (愛爾蘭)                     | 語言模型                                    |
| `gu-IN` | 古吉拉特文 (印度)                 | 語言模型                                    |
| `hi-IN` | 印度文 (印度)                     | 原音模型<br>語言模型                  |
| `hr-HR` | 克羅埃西亞文 (克羅埃西亞)                | 語言模型                                    |
| `hu-HU` | 匈牙利文 (匈牙利)               | 語言模型                                    | 
| `it-IT` | 義大利文 (義大利)                   | 原音模型<br>語言模型<br>發音 |
| `ja-JP` | 日文 (日本)                  | 語言模型                                    |
| `ko-KR` | 韓文 (韓國)                    | 語言模型                                    |
| `lt-LT` | 立陶宛文 (立陶宛)            | 語言模型                                    |
| `lv-LV` | 拉脫維亞文 (拉脫維亞)                  | 語言模型                                    |
| `mr-IN` | 馬拉提文 (印度)                   | 語言模型                                    |
| `mt-MT` | 馬爾他 (馬爾他)                     | 語言模型                                    |
| `nb-NO` | 挪威文 (巴克摩) (挪威)       | 語言模型                                    |
| `nl-NL` | 荷蘭文 (荷蘭)               | 語言模型                                    |
| `pl-PL` | 波蘭文 (波蘭)                   | 語言模型                                    |
| `pt-BR` | 葡萄牙文 (巴西)               | 原音模型<br>語言模型<br>發音 |
| `pt-PT` | 葡萄牙文 (葡萄牙)             | 語言模型                                    |
| `ro-RO` | 羅馬尼亞文 (羅馬尼亞)                | 語言模型                                    |
| `ru-RU` | 俄文 (俄羅斯)                  | 原音模型<br>語言模型                  |
| `sk-SK` | 斯洛伐克文 (斯洛伐克)                 | 語言模型                                    |
| `sl-SI` | 斯洛維尼亞文 (斯洛維尼亞)              | 語言模型                                    |
| `sv-SE` | 瑞典文 (瑞典)                  | 語言模型                                    |
| `ta-IN` | 坦米爾文 (印度)                     | 語言模型                                    |
| `te-IN` | 特拉古文 (印度)                    | 語言模型                                    |
| `th-TH` | 泰文 (泰國)                   | 語言模型                                    |
| `tr-TR` | 土耳其文 (土耳其)                  | 語言模型                                    |
| `zh-CN` | 中文 (中文，簡化)     | 原音模型<br>語言模型                  |
| `zh-HK` | 中文 (廣東話，繁體)  | 語言模型                                    |
| `zh-TW` | 中文 (繁體，國語)      | 語言模型                                    |

## <a name="text-to-speech"></a>文字轉換語音

Microsoft Speech SDK 和 REST API 均支援這些語音，且各支援依地區設定所識別的特定語言和方言。 您也可以透過[語音/清單 API](rest-text-to-speech.md#get-a-list-of-voices)，取得每個特定區域/端點所支援的語言和語音完整清單。 

> [!IMPORTANT]
> 標準、自訂和神經語音的定價各不相同。 如需其他資訊，請瀏覽[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)頁面。

### <a name="neural-voices"></a>神經語音

神經文字轉換語音是由深度神經網路驅動的新類型語音合成。 使用神經語音時，合成語音與人類錄音幾乎無法區分。

神經語音可用來讓與聊天機器人及語音小幫手的互動變得更加自然有趣；例如將數位文字 (例如電子書) 轉換成有聲書；以及增強車上導航系統。 具有類似人類的自然韻律和清楚的文字清晰度，神經語音大幅降低使用者與 AI 系統互動時的聆聽疲勞。

如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

|Locale  | Language            | 性別 | 語音名稱 | 樣式支援 |
|--|--|--|--|--|
| `ar-EG` | 阿拉伯文 (埃及)                  | Female | `ar-EG-SalmaNeural`      | 一般 |
| `ar-SA` | 阿拉伯文 (沙烏地阿拉伯)           | Female | `ar-SA-ZariyahNeural`    | 一般 |
| `ca-ES` | 加泰蘭文 (西班牙)                 | Female | `ca-ES-AlbaNeural`       | 一般 |
| `da-DK` | 丹麥文 (丹麥)                | Female | `da-DK-ChristelNeural`   | 一般 |
| `de-DE` | 德文 (德國)                | Female | `de-DE-KatjaNeural`      | 一般 |
| `en-AU` | 英文 (澳大利亞)             | Female | `en-AU-NatashaNeural`    | 一般 |
| `en-CA` | 英文 (加拿大)                | Female | `en-CA-ClaraNeural`      | 一般 |
| `en-GB` | 英文 (英國)                    | Female | `en-GB-LibbyNeural`      | 一般 |
|         |                                 | Female | `en-GB-MiaNeural`        | 一般 |
| `en-IN` | 英文 (印度)                 | Female | `en-IN-NeerjaNeural`     | 一般 |
| `en-US` | 英文 (美國)                    | Female | `en-US-AriaNeural`       | 一般，提供多種語音樣式 |
|         |                                 | Male   | `en-US-GuyNeural`        | 一般 |
| `es-ES` | 西班牙文 (西班牙)                 | Female | `es-ES-ElviraNeural`     | 一般 |
| `es-MX` | 西班牙文 (墨西哥)                | Female | `es-MX-DaliaNeural`      | 一般 |
| `fi-FI` | 芬蘭文 (芬蘭)               | Female | `fi-FI-NooraNeural`      | 一般 |
| `fr-CA` | 法文 (加拿大)                 | Female | `fr-CA-SylvieNeural`     | 一般 |
| `fr-FR` | 法文 (法國)                 | Female | `fr-FR-DeniseNeural`     | 一般 |
| `hi-IN` | 印度文 (印度)                   | Female | `hi-IN-SwaraNeural`      | 一般 |
| `it-IT` | 義大利文 (義大利)                 | Female | `it-IT-ElsaNeural`       | 一般 |
| `ja-JP` | 日文                        | Female | `ja-JP-NanamiNeural`     | 一般 |
| `ko-KR` | 韓文                          | Female | `ko-KR-SunHiNeural`      | 一般 |
| `nb-NO` | 挪威文                       | Female | `nb-NO-IselinNeural`     | 一般 |
| `nl-NL` | 荷蘭文 (Netherland)               | Female | `nl-NL-ColetteNeural`    | 一般 |
| `pl-PL` | 波蘭文 (波蘭)                 | Female | `pl-PL-ZofiaNeural`      | 一般 |
| `pt-BR` | 葡萄牙文 (巴西)             | Female | `pt-BR-FranciscaNeural`  | 一般，提供多種語音樣式 |
| `tr-TR` | 土耳其文                         | Female | `tr-TR-EmelNeural`       | 一般 |
| `pt-PT` | 葡萄牙文 (葡萄牙)           | Female | `pt-PT-FernandaNeural`   | 一般 |
| `ru-RU` | 俄文 (俄羅斯)                | Female | `ru-RU-DariyaNeural`     | 一般 |
| `sv-SE` | 瑞典文 (瑞典)                | Female | `sv-SE-HilleviNeural`    | 一般 |
| `th-TH` | 泰文 (泰國)                 | Female | `th-TH-AcharaNeural`     | 一般 |
| `zh-CN` | 中文 (中文，簡化)   | Female | `zh-CN-XiaoxiaoNeural`   | 一般，提供多種語音樣式 |
|         |                                 | Female | `zh-CN-XiaoyouNeural`    | 兒童語音，針對故事敘述進行最佳化 |
|         |                                 | Male   | `zh-CN-YunyangNeural`    | 針對新聞閱讀進行最佳化，提供多種語音樣式 |
|         |                                 | Male   | `zh-CN-YunyeNeural`      | 針對故事敘述進行最佳化 |
| `zh-HK` | 中文 (廣東話，繁體)   | Female | `zh-HK-HiuGaaiNeural`| 一般 |
| `zh-TW` | 中文 (繁體，國語)   | Female | `zh-TW-HsiaoYuNeural`    | 一般 |

> [!IMPORTANT]
> `en-US-JessaNeural` 的語音已變更為 `en-US-AriaNeural`。 如果您之前使用過「Jessa」，請轉換為「Aria」。

若要了解如何設定和調整類神經語音，請參閱[語音合成標記語言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!TIP]
> 您可以在語音合成要求中繼續使用完整的服務名稱對應，例如「Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)」。

### <a name="standard-voices"></a>標準語音

以超過 45 個語言和地區設定提供 75 個以上的標準語音，可讓您將文字轉換為合成語音。 如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

| Locale | Language | 性別 | 語音名稱 |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | 阿拉伯文 (埃及) | Female | "ar-EG-Hoda" |
| `ar-SA` | 阿拉伯文 (沙烏地阿拉伯) | Male | "ar-SA-Naayf" |
| `bg-BG` | 保加利亞文 | Male |  "bg-BG-Ivan" |
| `ca-ES` | 卡達隆尼亞文 | Female |  "ca-ES-HerenaRUS" |
| `cs-CZ` | 捷克文 | Male | "cs-CZ-Jakub" |
| `da-DK` | 丹麥文 | Female |  "da-DK-HelleRUS" |
| `de-AT` | 德文 (奧地利) | Male | "de-AT-Michael" |
| `de-CH` | 德文 (瑞士) | Male |  "de-CH-Karsten" |
| `de-DE` | 德文 (德國) | Female |  "de-DE-Hedda" |
|  |  | Female | "de-DE-HeddaRUS" |
|  |  | Male |  "de-DE-Stefan-Apollo" |
| `el-GR` | 希臘文 | Male | "el-GR-Stefanos" |
| `en-AU` | 英文 (澳大利亞) | Female |  "en-AU-Catherine" |
|  |  | Female |  "en-AU-HayleyRUS" |
| `en-CA` | 英文 (加拿大) | Female |  "en-CA-Linda" |
|  |  | Female |  "en-CA-HeatherRUS" |
| `en-GB` | 英文 (英國) | Female |  "en-GB-Susan-Apollo" |
|  |  | Female |  "en-GB-HazelRUS" |
|  |  | Male |  "en-GB-George-Apollo" |
| `en-IE` | 英文 (愛爾蘭) | Male | "en-IE-Sean" |
| `en-IN` | 英文 (印度) | Female | "en-IN-Heera-Apollo" |
|  |  | Female |  "en-IN-PriyaRUS" |
|  |  | Male |  "en-IN-Ravi-Apollo" |
| `en-US` | 英文 (美國) | Female |  "en-US-ZiraRUS" |
|  |  | Female | "en-US-AriaRUS" |
|  |  | Male | "en-US-BenjaminRUS" |
|  |  | Male |  "en-US-Guy24kRUS" |
| `es-ES` | 西班牙文 (西班牙) | Female |  "es-ES-Laura-Apollo" |
|  |  | Female | "es-ES-HelenaRUS" |
|  |  | Male | "es-ES-Pablo-Apollo" |
| `es-MX` | 西班牙文 (墨西哥) | Female |  "es-MX-HildaRUS" |
|  |  | Male | "es-MX-Raul-Apollo" |
| `fi-FI` | 芬蘭文 | Female | "fi-FI-HeidiRUS" |
| `fr-CA` | 法文 (加拿大) | Female | "fr-CA-Caroline" |
|  |  | Female | "fr-CA-HarmonieRUS" |
| `fr-CH` | 法文 (瑞士) | Male | "fr-CH-Guillaume" |
| `fr-FR` | 法文 (法國) | Female |  "fr-FR-Julie-Apollo" |
|  |  | Female |"fr-FR-HortenseRUS" |
|  |  | Male |  "fr-FR-Paul-Apollo" |
| `he-IL` | 希伯來文 (以色列) | Male |  "he-IL-Asaf" |
| `hi-IN` | 印度文 (印度) | Female | "hi-IN-Kalpana-Apollo" |
|  |  | Female |  "hi-IN-Kalpana" |
|  |  | Male |  "hi-IN-Hemant" |
| `hr-HR` | 克羅埃西亞文 | Male | "hr-HR-Matej" |
| `hu-HU` | 匈牙利文 | Male |  "hu-HU-Szabolcs" |
| `id-ID` | 印尼文 | Male | "id-ID-Andika" |
| `it-IT` | 義大利文 | Male |  "it-IT-Cosimo-Apollo" |
|  |  | Female |  "it-IT-LuciaRUS" |
| `ja-JP` | 日文 | Female |  "ja-JP-Ayumi-Apollo" |
|  |  | Male | "ja-JP-Ichiro-Apollo" |
|  |  | Female |  "ja-JP-HarukaRUS" |
| `ko-KR` | 韓文 | Female | "ko-KR-HeamiRUS" |
| `ms-MY` | 馬來文 | Male |  "ms-MY-Rizwan" |
| `nb-NO` | 挪威文 | Female |  "nb-NO-HuldaRUS" |
| `nl-NL` | 荷蘭文 | Female |  "nl-NL-HannaRUS" |
| `pl-PL` | 波蘭文 | Female |  "pl-PL-PaulinaRUS" |
| `pt-BR` | 葡萄牙文 (巴西) | Female | "pt-BR-HeloisaRUS" |
|  |  | Male |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | 葡萄牙文 (葡萄牙) | Female | "pt-PT-HeliaRUS" |
| `ro-RO` | 羅馬尼亞文 | Male | "ro-RO-Andrei" |
| `ru-RU` | 俄文 | Female |  "ru-RU-Irina-Apollo" |
|  |  | Male | "ru-RU-Pavel-Apollo" |
|  |  | Female |  ru-RU-EkaterinaRUS |
| `sk-SK` | 斯洛伐克文 | Male | "sk-SK-Filip" |
| `sl-SI` | 斯洛維尼亞文 | Male |  "sl-SI-Lado" |
| `sv-SE` | 瑞典文 | Female | "sv-SE-HedvigRUS" |
| `ta-IN` | 坦米爾文 (印度) | Male |  "ta-IN-Valluvar" |
| `te-IN` | 特拉古文 (印度) | Female |  "te-IN-Chitra" |
| `th-TH` | 泰文 | Male |  "th-TH-Pattara" |
| `tr-TR` | 土耳其文 (土耳其) | Female | "tr-TR-SedaRUS" |
| `vi-VN` | 越南文 | Male |  "vi-VN-An" |
| `zh-CN` | 中文 (中文，簡化)  | Female |  "zh-CN-HuihuiRUS" |
|  |  | Female | "zh-CN-Yaoyao-Apollo" |
|  |  | Male | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | 中文 (廣東話，繁體) | Female |  "zh-HK-Tracy-Apollo" |
|  |  | Female | "zh-HK-TracyRUS" |
|  |  | Male |  "zh-HK-Danny-Apollo" |
| `zh-TW` | 中文 (繁體，國語) | Female |  "zh-TW-Yating-Apollo" |
|  |  | Female | "zh-TW-HanHanRUS" |
|  |  | Male |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG 支援現代標準阿拉伯文 (MSA)。*

> [!IMPORTANT]
> `en-US-Jessa` 的語音已變更為 `en-US-Aria`。 如果您之前使用過「Jessa」，請轉換為「Aria」。

> [!TIP]
> 您可以在語音合成要求中繼續使用完整的服務名稱對應，例如「Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)」。

### <a name="customization"></a>自訂

語音自訂適用於 `de-DE`、`en-GB`、`en-IN`、`en-US`、`es-MX`、`fr-FR`、`it-IT`、`pt-BR` 和 `zh-CN`。 選取適當的地區設定，以符合您必須訓練自訂語音模型的訓練資料。 例如，如果您擁有的錄音資料是英式英文，請選取 [`en-GB`]。

> [!NOTE]
> 除了中文-英文雙語之外，我們不支援自訂語音中的雙語模型訓練。 如果您想要訓練中文語音 (也可以說英文)，請選取 [中文-英文雙語]。 所有地區設定中的語音訓練，通常要有 2000 句以上的語句資料集，除了 `en-US` 和 `zh-CN` 以外，這兩種語言可以從任何規模的訓練資料開始。

## <a name="speech-translation"></a>語音翻譯

**語音翻譯** API 支援語音轉換語音和語音轉換文字翻譯的不同語言。 來源語言一律必須來自語音轉換文字的語言表格。 可用的目標語言取決於翻譯目標是語音還是文字。 您可以將傳入的語音翻譯為 [60 多種語言](https://www.microsoft.com/translator/business/languages/)。 其中一些語言提供[語音合成](language-support.md#text-languages)功能。

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
| Hebrew                  | `he`          |
| Hindi                   | `hi`          |
| 白苗文               | `mww`         |
| 匈牙利文               | `hu`          |
| 印尼文              | `id`          |
| 愛爾蘭文                   | `ga`          |
| 義大利文                 | `it`          |
| 日文                | `ja`          |
| 坎那達文                 | `kn`          |
| 史瓦希里文               | `sw`          |
| 克林貢文                 | `tlh-Latn`    |
| 克林貢文 (plqaD)         | `tlh-Piqd`    |
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

## <a name="speaker-recognition"></a>說話者辨識

請參閱下表以了解各種說話者辨識 API 支援的語言。 如需說話者辨識的其他詳細資訊，請參閱 [概觀](speaker-recognition-overview.md)。

| Locale | Language | 文字相依驗證 | 與文字無關的驗證 | 與文字無關的識別 |
|----|----|----|----|----|
| zh-TW | 英文 (美國) | 是 | 是 | 是 |
|zh-CN    |中文 (普通話，簡體)|    n/a|    是|    是|
|de-DE    |德文 (德國)    |n/a    |是    |是|
|en-GB    |英文 (英國)    |n/a    |是    |是|
|fr-FR    |法文 (法國)    |n/a    |是    |是|
|en-AU    |英文 (澳大利亞)    |n/a    |是    |是|
|en-CA    |英文 (加拿大)    |n/a|    是|    是|
|fr-CA    |法文 (加拿大)    |n/a    |是|    是|
|it-IT    |義大利文|    n/a    |是|    是|
|es-ES|    西班牙文 (西班牙)    |n/a    |是|    是|
|es-MX    |西班牙文 (墨西哥)    |n/a|    是|    是|
|ja-JP|    日文    |n/a    |是    |是|
|pt-BR|    葡萄牙文 (巴西)|    n/a|    是|    是|

## <a name="next-steps"></a>後續步驟

* [建立 Azure 免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp) (英文)
