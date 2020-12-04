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
ms.custom: references_regions
ms.openlocfilehash: 9ef585d83c0606bedaf4c0a05c6c87fd52423b9a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575528"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>語音服務的語言和語音支援

語言支援會因語音服務功能而有所不同。 下表摘要說明[語音轉換文字](#speech-to-text)、[文字轉換語音](#text-to-speech)，以及 [語音翻譯](#speech-translation)服務供應項目的語言支援。

## <a name="speech-to-text"></a>語音轉文字

Microsoft Speech SDK 和 REST API 均支援下列語言 (地區設定)。 

為了提升正確性，請透過上傳 **音訊 + 人為標記文字記錄** 或 **相關的文字：句子**，為部分語言提供自訂。 若要深入了解自訂，請參閱[開始使用自訂語音](./custom-speech-overview.md)。

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Language                          | 地區設定 (BCP-47)  | 自訂                                   |
|-----------------------------------|--------|--------------------------------------------------|
|阿拉伯文 (巴林)，現代標準  |`ar-BH` | 語言模型                                   |
|阿拉伯文 (埃及)                     |`ar-EG` | 語言模型                                   |
|阿拉伯文 (伊拉克)                      |`ar-IQ` | 語言模型                                   |
|阿拉伯文 (以色列)                              |`ar-IL` | 語言模型                                                   |
|阿拉伯文 (約旦)                    |`ar-JO` | 語言模型                                   |
|阿拉伯文 (科威特)                    |`ar-KW` | 語言模型                                   |
|阿拉伯文 (黎巴嫩)                   |`ar-LB` | 語言模型                                   |
|阿拉伯文 (阿曼)                      |`ar-OM` | 語言模型                                   |
|阿拉伯文 (卡達)                     |`ar-QA` | 語言模型                                   |
|阿拉伯文 (沙烏地阿拉伯)              |`ar-SA` | 語言模型                                   |
|巴勒斯坦) 的阿拉伯文 (狀態            |`ar-PS` | 語言模型                                   |
|阿拉伯文 (敘利亞)                     |`ar-SY` | 語言模型                                   |
|阿拉伯文 (阿拉伯酋長國)       |`ar-AE` | 語言模型                                   |
|保加利亞文 (保加利亞)               |`bg-BG` | 語言模型                                   |
|加泰蘭文 (西班牙)                    |`ca-ES` | 語言模型                                   |
|中文 (廣東話，繁體)   |`zh-HK` | 語言模型                                   |
|中文 (中文，簡化)      |`zh-CN` | 原音模型<br>語言模型                 |
|中文 (繁體，國語)       |`zh-TW` | 語言模型                                   |
|克羅埃西亞文 (克羅埃西亞)                 |`hr-HR` | 語言模型                                   |
|捷克文 (捷克共和國)             |`cs-CZ` | 語言模型                                   |
|丹麥文 (丹麥)                   |`da-DK` | 語言模型                                   |
|荷蘭文 (荷蘭)                |`nl-NL` | 語言模型                                   |
|英文 (澳大利亞)                |`en-AU` | 原音模型<br>語言模型                 |
|英文 (加拿大)                   |`en-CA` | 原音模型<br>語言模型                 |
|英文 (香港特別行政區)                 |`en-HK` | 語言模型                                   |
|英文 (印度)                    |`en-IN` | 原音模型<br>語言模型                 |
|英文 (愛爾蘭)                  |`en-IE` | 語言模型                                   |
|英文 (紐西蘭)              |`en-NZ` | 原音模型<br>語言模型                 |
|英文 (奈及利亞)                           |`en-NG` | 語言模型                                                     |
|英文 (菲律賓)              |`en-PH` | 語言模型                                   |
|英文 (新加坡)                |`en-SG` | 語言模型                                   |
|英文 (南非)             |`en-ZA` | 語言模型                                   |
|英文 (英國)           |`en-GB` | 原音模型<br>語言模型<br>發音|
|英文 (美國)            |`en-US` | 原音模型<br>語言模型<br>發音|
|愛沙尼亞 (愛沙尼亞)                   |`et-EE` | 語言模型                                   |
|芬蘭文 (芬蘭)                  |`fi-FI` | 語言模型                                   |
|法文 (加拿大)                    |`fr-CA` | 原音模型<br>語言模型                 |
|法文 (法國)                    |`fr-FR` | 原音模型<br>語言模型<br>發音|
|德文 (德國)                   |`de-DE` | 原音模型<br>語言模型<br>發音|
|希臘文 (希臘)                     |`el-GR` | 語言模型                                   |
|古吉拉特文 (印度)                  |`gu-IN` | 語言模型                                   |
|印度文 (印度)                      |`hi-IN` | 原音模型<br>語言模型                 |
|匈牙利文 (匈牙利)                |`hu-HU` | 語言模型                                   |
|愛爾蘭 (愛爾蘭)                      |`ga-IE` | 語言模型                                   |
|義大利文 (義大利)                    |`it-IT` | 原音模型<br>語言模型<br>發音|
|日文 (日本)                   |`ja-JP` | 語言模型                                   |
|韓文 (韓國)                     |`ko-KR` | 語言模型                                   |
|拉脫維亞文 (拉脫維亞)                   |`lv-LV` | 語言模型                                   |
|立陶宛文 (立陶宛)             |`lt-LT` | 語言模型                                   |
|馬爾他 (馬爾他)                      |`mt-MT` | 語言模型                                   |
|馬拉提文 (印度)                    |`mr-IN` | 語言模型                                   |
|挪威文 (巴克摩，挪威)         |`nb-NO` | 語言模型                                   |
|波蘭文 (波蘭)                    |`pl-PL` | 語言模型                                   |
|葡萄牙文 (巴西)                |`pt-BR` | 原音模型<br>語言模型<br>發音|
|葡萄牙文 (葡萄牙)              |`pt-PT` | 語言模型                                   |
|羅馬尼亞文 (羅馬尼亞)                 |`ro-RO` | 語言模型                                   |
|俄文 (俄羅斯)                   |`ru-RU` | 原音模型<br>語言模型                 |
|斯洛伐克文 (斯洛伐克)                  |`sk-SK` | 語言模型                                   |
|斯洛維尼亞文 (斯洛維尼亞)               |`sl-SI` | 語言模型                                   |
|西班牙文 (阿根廷)                |`es-AR` | 語言模型                                   |
|西班牙文 (玻利維亞)                  |`es-BO` | 語言模型                                   |
|西班牙文 (智利)                    |`es-CL` | 語言模型                                   |
|西班牙文 (哥倫比亞)                 |`es-CO` | 語言模型                                   |
|西班牙文 (哥斯大黎加)               |`es-CR` | 語言模型                                   |
|西班牙文 (古巴)                      |`es-CU` | 語言模型                                   |
|西班牙文 (多明尼加)       |`es-DO` | 語言模型                                   |
|西班牙文 (厄瓜多)                  |`es-EC` | 語言模型                                   |
|西班牙文 (薩爾瓦多)              |`es-SV` | 語言模型                                   |
|西班牙文 (赤道幾內亞)             |`es-GQ` | 語言模型                                   |
|西班牙文 (瓜地馬拉)                |`es-GT` | 語言模型                                   |
|西班牙文 (宏都拉斯)                 |`es-HN` | 語言模型                                   |
|西班牙文 (墨西哥)                   |`es-MX` | 原音模型<br>語言模型                 |
|西班牙文 (尼加拉瓜)                |`es-NI` | 語言模型                                   |
|西班牙文 (巴拿馬)                   |`es-PA` | 語言模型                                   |
|西班牙文 (巴拉圭)                 |`es-PY` | 語言模型                                   |
|西班牙文 (秘魯)                     |`es-PE` | 語言模型                                   |
|西班牙文 (波多黎各)              |`es-PR` | 語言模型                                   |
|西班牙文 (西班牙)                    |`es-ES` | 原音模型<br>語言模型                 |
|西班牙文 (烏拉圭)                  |`es-UY` | 語言模型                                   |
|美國西班牙文 (USA)                       |`es-US` | 語言模型                                   |
|西班牙文 (委內瑞拉)                |`es-VE` | 語言模型                                   |
|瑞典文 (瑞典)                   |`sv-SE` | 語言模型                                   |
|坦米爾文 (印度)                      |`ta-IN` | 語言模型                                   |
|特拉古文 (印度)                     |`te-IN` | 語言模型                                   |
|泰文 (泰國)                    |`th-TH` | 語言模型                                   |
|土耳其文 (土耳其)                   |`tr-TR` | 語言模型                                   |

## <a name="text-to-speech"></a>文字轉換語音

Microsoft Speech SDK 和 REST API 均支援這些語音，且各支援依地區設定所識別的特定語言和方言。 您也可以透過[語音/清單 API](rest-text-to-speech.md#get-a-list-of-voices)，取得每個特定區域/端點所支援的語言和語音完整清單。 

> [!IMPORTANT]
> 標準、自訂和神經語音的定價各不相同。 如需其他資訊，請瀏覽[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)頁面。

### <a name="neural-voices"></a>神經語音

神經文字轉換語音是由深度神經網路驅動的新類型語音合成。 使用神經語音時，合成語音與人類錄音幾乎無法區分。

神經語音可用來讓與聊天機器人及語音小幫手的互動變得更加自然有趣；例如將數位文字 (例如電子書) 轉換成有聲書；以及增強車上導航系統。 具有類似人類的自然韻律和清楚的文字清晰度，神經語音大幅降低使用者與 AI 系統互動時的聆聽疲勞。

| Language | 地區設定 | 性別 | 語音名稱 | 樣式支援 |
|---|---|---|---|---|
| 阿拉伯文 (埃及) | `ar-EG` | Female | `ar-EG-SalmaNeural` | 一般 |
| 阿拉伯文 (沙烏地阿拉伯) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | 一般 |
| 保加利亞文 (Bulgary)  | `bg-BG` | Female | `bg-BG-KalinaNeural` <sup>新增</sup> | 一般 |
| 加泰蘭文 (西班牙) | `ca-ES` | Female | `ca-ES-AlbaNeural` | 一般 |
| 中文 (廣東話，繁體) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | 一般 |
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | 一般，[使用 SSML 可使用](speech-synthesis-markup.md#adjust-speaking-styles)的多個聲音樣式 |
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | 兒童語音，針對故事敘述進行最佳化 |
| 中文 (中文，簡化)  | `zh-CN` | Male   | `zh-CN-YunyangNeural` | 針對新聞閱讀優化<br /> [使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)提供的多個聲音樣式 |
| 中文 (中文，簡化)  | `zh-CN` | Male   | `zh-CN-YunyeNeural` | 針對故事敘述進行最佳化 |
| 中文 (繁體，國語) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | 一般 |
| 克羅埃西亞文 (克羅埃西亞) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` <sup>新增</sup> | 一般 |
| 捷克文 (捷克文)  | `cs-CZ` | Female | `cs-CZ-VlastaNeural` <sup>新增</sup>    | 一般 |
| 丹麥文 (丹麥) | `da-DK` | Female | `da-DK-ChristelNeural` | 一般 |
| 荷蘭文 (荷蘭) | `nl-NL` | Female | `nl-NL-ColetteNeural` | 一般 |
| 英文 (澳大利亞) | `en-AU` | Female | `en-AU-NatashaNeural` | 一般 |
| 英文 (澳大利亞) | `en-AU` | Male   | `en-AU-WilliamNeural` <sup>新增</sup>  | 一般 |
| 英文 (加拿大) | `en-CA` | Female | `en-CA-ClaraNeural` | 一般 |
| 英文 (印度) | `en-IN` | Female | `en-IN-NeerjaNeural` | 一般 |
| 英文 (愛爾蘭) | `en-IE` | Female | `en-IE-EmilyNeural` <sup>新增</sup> | 一般 |
| 英文 (英國) | `en-GB` | Female | `en-GB-LibbyNeural` | 一般 |
| 英文 (英國) | `en-GB` | Female | `en-GB-MiaNeural` | 一般 |
| 英文 (英國) | `en-GB` | Male | `en-GB-RyanNeural` <sup>新增</sup> | 一般 |
| 英文 (美國) | `en-US` | Female | `en-US-AriaNeural` | 一般，[使用 SSML 可使用](speech-synthesis-markup.md#adjust-speaking-styles)的多個聲音樣式 |
| 英文 (美國) | `en-US` | Male | `en-US-GuyNeural` | 一般 |
| 英文 (美國) | `en-US` | Female | `en-US-JennyNeural` <sup>新增</sup> | 一般，[使用 SSML 可使用](speech-synthesis-markup.md#adjust-speaking-styles)的多個聲音樣式 |
| 芬蘭文 (芬蘭) | `fi-FI` | Female | `fi-FI-NooraNeural` | 一般 |
| 法文 (加拿大) | `fr-CA` | Female | `fr-CA-SylvieNeural` | 一般 |
| 法文 (加拿大) | `fr-CA` | Male | `fr-CA-JeanNeural` <sup>新增</sup> | 一般 |
| 法文 (法國) | `fr-FR` | Female | `fr-FR-DeniseNeural` | 一般 |
| 法文 (法國) | `fr-FR` | Male | `fr-FR-HenriNeural` <sup>新增</sup> | 一般 |
| 法文 (瑞士) | `fr-CH` | Female | `fr-CH-ArianeNeural` <sup>新增</sup> | 一般 |
| 德文 (奧地利) | `de-AT` | Female | `de-AT-IngridNeural` <sup>新增</sup> | 一般 |
| 德文 (德國) | `de-DE` | Female | `de-DE-KatjaNeural` | 一般 |
| 德文 (德國) | `de-DE` | Male | `de-DE-ConradNeural` <sup>新增</sup> | 一般 |
| 德文 (瑞士) | `de-CH` | Female | `de-CH-LeniNeural` <sup>新增</sup> | 一般 |
| 希臘文 (希臘) | `el-GR` | Female | `el-GR-AthinaNeural` <sup>新增</sup> | 一般 |
| 希伯來文 (以色列) | `he-IL` | Female | `he-IL-HilaNeural` <sup>新增</sup> | 一般 |
| 印度文 (印度) | `hi-IN` | Female | `hi-IN-SwaraNeural` | 一般 |
| 匈牙利文 (匈牙利) | `hu-HU` | Female | `hu-HU-NoemiNeural` <sup>新增</sup> | 一般 |
| 印尼文 (印尼) | `id-ID` | Male | `id-ID-ArdiNeural` <sup>新增</sup> | 一般 |
| 義大利文 (義大利) | `it-IT` | Female | `it-IT-ElsaNeural` | 一般 |
| 義大利文 (義大利) | `it-IT` | Female | `it-IT-IsabellaNeural` <sup>新增</sup> | 一般 |
| 義大利文 (義大利) | `it-IT` | Male | `it-IT-DiegoNeural` <sup>新增</sup> | 一般 |
| 日文 (日本) | `ja-JP` | Female | `ja-JP-NanamiNeural` | 一般 |
| 日文 (日本) | `ja-JP` | Male | `ja-JP-KeitaNeural` <sup>新增</sup> | 一般 |
| 韓文 (韓國) | `ko-KR` | Female | `ko-KR-SunHiNeural` | 一般 |
| 韓文 (韓國) | `ko-KR` | Male | `ko-KR-InJoonNeural` <sup>新增</sup> | 一般 |
| 馬來文 (馬來西亞) | `ms-MY` | Female | `ms-MY-YasminNeural` <sup>新增</sup> | 一般 |
| 挪威文 (巴克摩，挪威) | `nb-NO` | Female | `nb-NO-IselinNeural` | 一般 |
| 波蘭文 (波蘭) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | 一般 |
| 葡萄牙文 (巴西) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | 一般，[使用 SSML 可使用](speech-synthesis-markup.md#adjust-speaking-styles)的多個聲音樣式 |
| 葡萄牙文 (巴西) | `pt-BR` | Male | `pt-BR-AntonioNeural` <sup>新增</sup> | 一般 |
| 葡萄牙文 (葡萄牙) | `pt-PT` | Female | `pt-PT-FernandaNeural` | 一般 |
| 羅馬尼亞文 (羅馬尼亞) | `ro-RO` | Female | `ro-RO-AlinaNeural` <sup>新增</sup> | 一般 |
| 俄文 (俄羅斯) | `ru-RU` | Female | `ru-RU-DariyaNeural` | 一般 |
| 斯洛伐克文 (斯洛伐克) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` <sup>新增</sup> | 一般 |
| 斯洛維尼亞文 (斯洛維尼亞) | `sl-SI` | Female | `sl-SI-PetraNeural` <sup>新增</sup> | 一般 |
| 西班牙文 (墨西哥) | `es-MX` | Female | `es-MX-DaliaNeural` | 一般 |
| 西班牙文 (墨西哥) | `es-MX` | Male | `es-MX-JorgeNeural` <sup>新增</sup> | 一般 |
| 西班牙文 (西班牙) | `es-ES` | Female | `es-ES-ElviraNeural` | 一般 |
| 西班牙文 (西班牙) | `es-ES` | Male | `es-ES-AlvaroNeural` <sup>新增</sup> | 一般 |
| 瑞典文 (瑞典) | `sv-SE` | Female | `sv-SE-HilleviNeural` | 一般 |
| 坦米爾文 (印度) | `ta-IN` | Female | `ta-IN-PallaviNeural` <sup>新增</sup> | 一般 |
| 特拉古文 (印度) | `te-IN` | Female | `te-IN-ShrutiNeural` <sup>新增</sup> | 一般 |
| 泰文 (泰國) | `th-TH` | Female | `th-TH-AcharaNeural` | 一般 |
| 泰文 (泰國) | `th-TH` | Female | `th-TH-PremwadeeNeural` <sup>新增</sup> | 一般 |
| 土耳其文 (土耳其) | `tr-TR` | Female | `tr-TR-EmelNeural` | 一般 |
| 越南文 (越南) | `vi-VN` <sup>新增</sup> | Female | `vi-VN-HoaiMyNeural` | 一般|

#### <a name="neural-voices-in-preview"></a>預覽中的類神經語音

以下神經語音處於公開預覽狀態。 

| Language                         | 地區設定  | 性別 | 語音名稱                             | 樣式支援 |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| 中文 (中文，簡化)  | `zh-CN` | Male   | `zh-CN-YunxiNeural` <sup>新增</sup> | 一般，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)時可使用多個樣式 |
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-XiaohanNeural` <sup>新增</sup> | 一般，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)時可使用多個樣式 |
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` <sup>新增</sup> | 一般，[使用 SSML 可使用](speech-synthesis-markup.md#adjust-speaking-styles)的多個角色扮演和樣式 |
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-XiaomoNeural` <sup>新增</sup> | 一般，[使用 SSML 可使用](speech-synthesis-markup.md#adjust-speaking-styles)的多個角色扮演和樣式 |
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-XiaoruiNeural` <sup>新增</sup> | 資深語音，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)提供多種樣式 |
| 愛沙尼亞文 (愛沙尼亞) | `et-EE` | Female | `et-EE-AnuNeural` <sup>新增</sup> | 一般 |
| 蓋爾語 (愛爾蘭)  | `ga-IE` | Female | `ga-IE-OrlaNeural` <sup>新增</sup> | 一般 |
| 立陶宛文 (立陶宛) | `lt-LT` | Female | `lt-LT-OnaNeural` <sup>新增</sup> | 一般 |
| 拉脫維亞文 (拉脫維亞) | `lv-LV` | Female | `lv-LV-EveritaNeural` <sup>新增</sup> | 一般 |
| 馬爾他文 (馬爾他) | `mt-MT` | Female | `mt-MT-GraceNeural` <sup>新增</sup> | 一般 |

> [!IMPORTANT]
> 公開預覽中的語音僅適用于三個服務區域：美國東部、西歐和東南亞。

如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

若要瞭解如何設定及調整類神經語音，例如說話樣式，請參閱 [語音合成標記語言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!IMPORTANT]
> `en-US-JessaNeural` 的語音已變更為 `en-US-AriaNeural`。 如果您之前使用過「Jessa」，請轉換為「Aria」。

> [!TIP]
> 您可以在語音合成要求中繼續使用完整的服務名稱對應，例如「Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)」。

### <a name="standard-voices"></a>標準語音

以超過 45 個語言和地區設定提供 75 個以上的標準語音，可讓您將文字轉換為合成語音。 如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

| Language | 地區設定 (BCP-47)  | 性別 | 語音名稱 |
|--|--|--|--|
| 阿拉伯文 (阿拉伯文 )  | `ar-EG` | Female | `ar-EG-Hoda`|
| 阿拉伯文 (沙烏地阿拉伯) | `ar-SA` | Male | `ar-SA-Naayf`|
| 保加利亞文 (保加利亞) | `bg-BG` | Male | `bg-BG-Ivan`|
| 加泰蘭文 (西班牙) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| 中文 (廣東話，繁體) | `zh-HK` | Male | `zh-HK-Danny`|
| 中文 (廣東話，繁體) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| 中文 (中文，簡化)  | `zh-CN` | Male | `zh-CN-Kangkang`|
| 中文 (中文，簡化)  | `zh-CN` | Female | `zh-CN-Yaoyao`|
| 中文 (繁體，國語) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| 中文 (繁體，國語) |  `zh-TW` | Female | `zh-TW-Yating`|
| 中文 (繁體，國語) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| 克羅埃西亞文 (克羅埃西亞) | `hr-HR` | Male | `hr-HR-Matej`|
| 捷克文 (捷克共和國) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| 丹麥文 (丹麥) | `da-DK` | Female | `da-DK-HelleRUS`|
| 荷蘭文 (荷蘭) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| 英文 (澳大利亞) | `en-AU` | Female | `en-AU-Catherine`|
| 英文 (澳大利亞) | `en-AU` | Female | `en-AU-HayleyRUS`|
| 英文 (加拿大) | `en-CA` | Female | `en-CA-HeatherRUS`|
| 英文 (加拿大) | `en-CA` | Female | `en-CA-Linda`|
| 英文 (印度) | `en-IN` | Female | `en-IN-Heera`|
| 英文 (印度) | `en-IN` | Female | `en-IN-PriyaRUS`|
| 英文 (印度) | `en-IN` | Male | `en-IN-Ravi`|
| 英文 (愛爾蘭) | `en-IE` | Male | `en-IE-Sean`|
| 英文 (英國) | `en-GB` | Male | `en-GB-George`|
| 英文 (英國) | `en-GB` | Female | `en-GB-HazelRUS`|
| 英文 (英國) | `en-GB` | Female | `en-GB-Susan`|
| 英文 (美國) | `en-US` | Male | `en-US-BenjaminRUS`|
| 英文 (美國) | `en-US` | Male | `en-US-GuyRUS`|
| 英文 (美國) | `en-US` | Female | `en-US-JessaRUS`|
| 英文 (美國) | `en-US` | Female | `en-US-ZiraRUS`|
| 芬蘭文 (芬蘭) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| 法文 (加拿大) | `fr-CA` | Female | `fr-CA-Caroline`|
| 法文 (加拿大) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| 法文 (法國) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| 法文 (法國) | `fr-FR` | Female | `fr-FR-Julie`|
| 法文 (法國) | `fr-FR` | Male | `fr-FR-Paul`|
| 法文 (瑞士) | `fr-CH` | Male | `fr-CH-Guillaume`|
| 德文 (奧地利) | `de-AT` | Male | `de-AT-Michael`|
| 德文 (德國) | `de-DE` | Female | `de-DE-HeddaRUS`|
| 德文 (德國) | `de-DE` | Male | `de-DE-Stefan`|
| 德文 (瑞士) | `de-CH` | Male | `de-CH-Karsten`|
| 希臘文 (希臘) | `el-GR` | Male | `el-GR-Stefanos`|
| 希伯來文 (以色列) | `he-IL` | Male | `he-IL-Asaf`|
| 印度文 (印度) | `hi-IN` | Male | `hi-IN-Hemant`|
| 印度文 (印度) | `hi-IN` | Female | `hi-IN-Kalpana`|
| 匈牙利文 (匈牙利) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| 印尼文 (印尼) | `id-ID` | Male | `id-ID-Andika`|
| 義大利文 (義大利) | `it-IT` | Male | `it-IT-Cosimo`|
| 義大利文 (義大利) | `it-IT` | Female | `it-IT-LuciaRUS`|
| 日文 (日本) | `ja-JP` | Female | `ja-JP-Ayumi`|
| 日文 (日本) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| 日文 (日本) | `ja-JP` | Male | `ja-JP-Ichiro`|
| 韓文 (韓國) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| 馬來文 (馬來西亞) | `ms-MY` | Male | `ms-MY-Rizwan`|
| 挪威文 (巴克摩，挪威) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| 波蘭文 (波蘭) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| 葡萄牙文 (巴西) | `pt-BR` | Male | `pt-BR-Daniel`|
| 葡萄牙文 (巴西) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| 葡萄牙文 (葡萄牙) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| 羅馬尼亞文 (羅馬尼亞) | `ro-RO` | Male | `ro-RO-Andrei`|
| 俄文 (俄羅斯) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| 俄文 (俄羅斯) | `ru-RU` | Female | `ru-RU-Irina`|
| 俄文 (俄羅斯) | `ru-RU` | Male | `ru-RU-Pavel`|
| 斯洛伐克文 (斯洛伐克) | `sk-SK` | Male | `sk-SK-Filip`|
| 斯洛維尼亞文 (斯洛維尼亞) | `sl-SI` | Male | `sl-SI-Lado`|
| 西班牙文 (墨西哥) | `es-MX` | Female | `es-MX-HildaRUS`|
| 西班牙文 (墨西哥) | `es-MX` | Male | `es-MX-Raul`|
| 西班牙文 (西班牙) | `es-ES` | Female | `es-ES-HelenaRUS`|
| 西班牙文 (西班牙) | `es-ES` | Female | `es-ES-Laura`|
| 西班牙文 (西班牙) | `es-ES` | Male | `es-ES-Pablo`|
| 瑞典文 (瑞典) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| 坦米爾文 (印度) | `ta-IN` | Male | `ta-IN-Valluvar`|
| 特拉古文 (印度) | `te-IN` | Female | `te-IN-Chitra`|
| 泰文 (泰國) | `th-TH` | Male | `th-TH-Pattara`|
| 土耳其文 (土耳其) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| 越南文 (越南) | `vi-VN` | Male | `vi-VN-An` |

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

| Language | 地區設定 (BCP-47)  | 文字相依驗證 | 與文字無關的驗證 | 與文字無關的識別 |
|----|----|----|----|----|
|英文 (美國)  |  en-US  |  是  |  是  |  是 |
|中文 (普通話，簡體) | zh-CN     |     n/a |     是 |     是|
|英文 (澳大利亞)     | en-AU     | n/a     | 是     | 是|
|英文 (加拿大)     | en-CA     | n/a |     是 |     是|
|英文 (英國)     | en-GB     | n/a     | 是     | 是|
|法文 (加拿大)     | fr-CA     | n/a     | 是 |     是|
|法文 (法國)     | fr-FR     | n/a     | 是     | 是|
|德文 (德國)     | de-DE     | n/a     | 是     | 是|
|義大利文 | it-IT     |     n/a     | 是 |     是|
|日文     | ja-JP | n/a     | 是     | 是|
|葡萄牙文 (巴西) | pt-BR |     n/a |     是 |     是|
|西班牙文 (墨西哥)     | es-MX     | n/a |     是 |     是|
|西班牙文 (西班牙)     | es-ES | n/a     | 是 |     是|

## <a name="next-steps"></a>後續步驟

* [建立 Azure 免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [了解如何以 C# 辨識語音](./get-started-speech-to-text.md?pivots=programming-language-chsarp) (英文)