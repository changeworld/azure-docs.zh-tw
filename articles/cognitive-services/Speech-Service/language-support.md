---
title: 語言支援 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務支援多種語言進行語音到文本和文本到語音轉換，以及語音翻譯。 本文提供了按服務功能提供語言支援的完整清單。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d01ab60790311649e424a98d5a08c6af0bca90f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336045"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>語音服務的語言和語音支援

語言支援因語音服務功能而異。 下表匯總了[對語音到文本](#speech-to-text)、[文本到語音](#text-to-speech)和[語音翻譯](#speech-translation)服務產品的語言支援。

## <a name="speech-to-text"></a>語音轉文字

Microsoft 語音 SDK 和 REST API 都支援以下語言（地區設定）。 為了提高準確性，通過上傳"音訊+ 人工標記的腳本"或"相關文本：句子"，為語言的子集提供自訂。 發音自訂當前僅適用于`en-US`和`de-DE`。 在此處瞭解有關自訂的更多[詳細資訊](how-to-custom-speech.md)。

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Locale  | 語言                          | 支援 | 自訂                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | 阿拉伯文（阿聯酋）                      | 是       | 否                                                |
| `ar-BH` | 阿拉伯文（巴林），現代標準 | 是       | 語言模型                                    |
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
| `zh-HK` | 中文（粵語，傳統）  | 是       | 語言模型                                    |
| `zh-TW` | 中文 (繁體，國語)      | 是       | 語言模型                                    |

## <a name="text-to-speech"></a>文字轉換語音

Microsoft 語音 SDK 和 REST API 都支援這些語音，每個語音都支援特定語言和方言，由地區設定標識。

> [!IMPORTANT]
> 標準、自訂和神經語音的定價各不相同。 如需其他資訊，請瀏覽[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)頁面。

### <a name="neural-voices"></a>神經語音

神經文字轉換語音是由深度神經網路驅動的新類型語音合成。 使用神經語音時，合成語音與人類錄音幾乎無法區分。

神經語音可用於使與聊天機器人和語音助理的互動更加自然和吸引人，將電子書等數位文本轉換為有聲讀物，並增強車載導航系統。 具有類似人類的自然韻律和清楚的文字清晰度，神經語音大幅降低使用者與 AI 系統互動時的聆聽疲勞。

如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

| Locale  | 語言            | 性別 | 全服務名稱映射                                               | 短語音名稱        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | 德文 (德國)    | Female | "微軟伺服器語音文本到語音語音（de-DE，KatjaNeural）"     | "去DE-KatjaNeural"     |
| `en-US` | 英文 (美國)        | Female | "微軟伺服器語音文本到語音語音（en-美國，阿裡亞斯神經）"      | "美國-阿裡亞斯神經"      |
| `en-US` | 英文 (美國)        | Male   | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)"       | "美國-蓋伊神經"       |
| `it-IT` | 義大利文 (義大利)     | Female | 微軟伺服器語音文本到語音語音（IT，ElsaNeural）"      | "IT-ElsaNeural"      |
| `pt-BR` | 葡萄牙文 (巴西) | Female | 微軟伺服器語音文本到語音語音（pt-BR，法蘭西斯卡神經）" | "pt-BR-弗蘭西卡神經" |
| `zh-CN` | 中文 (普通話，簡體)  | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-曉曉神經"  |

> [!IMPORTANT]
> 語音`en-US-JessaNeural`已更改為`en-US-AriaNeural`。 如果您以前使用"傑薩"，則轉換成"阿裡婭"。

要瞭解如何配置和調整神經語音，請參閱[語音合成標記語言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!TIP]
> 您可以在語音合成請求中使用全服務名稱映射或短語音名稱。

### <a name="standard-voices"></a>標準語音

以超過 45 個語言和地區設定提供 75 個以上的標準語音，可讓您將文字轉換為合成語音。 如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

| Locale | 語言 | 性別 | 全服務名稱映射 | 簡短名稱 |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | 阿拉伯文 (埃及) | Female | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" | "ar-EG-霍達" |
| `ar-SA` | 阿拉伯文 (沙烏地阿拉伯) | Male | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)" | "ar-SA-奈夫" |
| `bg-BG` | 保加利亞文 | Male | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)" | "bg-BG-伊萬" |
| `ca-ES` | 加泰蘭文 (西班牙) | Female | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | "卡-埃斯-埃雷納魯斯" |
| `cs-CZ` | 捷克文 | Male | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub" |
| `da-DK` | 丹麥文 | Female | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "達-DK-海爾魯斯" |
| `de-AT` | 德文 (奧地利) | Male | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | "去AT-邁克爾" |
| `de-CH` | 德文 (瑞士) | Male | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)" | "去CH-卡斯滕" |
| `de-DE` | 德文 (德國) | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "DE-DE-赫德達" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "DE-DE-赫德達魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "德-德-斯特凡-阿波羅" |
| `el-GR` | 希臘文 | Male | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "埃爾-格-斯特凡諾斯" |
| `en-AU` | 英文 (澳大利亞) | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)" | "恩-AU-凱薩琳" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "EN-AU-海利魯斯" |
| `en-CA` | 英文 (加拿大) | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)" | "恩-卡林達" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)" | "en-CA-希瑟魯斯" |
| `en-GB` | 英文 (英國) | Female | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-GB-蘇珊-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)" | "en-GB-黑澤爾" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)" | "EN-GB-喬治-阿波羅" |
| `en-IE` | 英文 (愛爾蘭) | Male | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | 英文 (印度) | Female | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)" | "內海拉阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)" | "恩-恩-普裡裡亞斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "內拉維-阿波羅" |
| `en-US` | 英文 (美國) | Female | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)" | "美國-齊拉魯斯" |
|  |  | Female | "微軟伺服器語音文本到語音語音（en-美國，AriaRUS）" | "美國-阿裡阿魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)" | "美國-本傑明魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)" | "en-美國-蓋伊24kRUS" |
| `es-ES` | 西班牙文 (西班牙) | Female | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-蘿拉-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-ES-海倫納魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-巴勃羅-阿波羅" |
| `es-MX` | 西班牙文 (墨西哥) | Female | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-希爾達魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-勞爾-阿波羅" |
| `fi-FI` | 芬蘭文 | Female | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | "FI-FI-海蒂魯斯" |
| `fr-CA` | 法文 (加拿大) | Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)" | "fr-CA-卡洛琳" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)" | "f-CA-哈蒙尼魯斯" |
| `fr-CH` | 法文 (瑞士) | Male | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | 法文 (法國) | Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "FR-FR-朱莉-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-霍滕斯魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "FR-FR-保羅-阿波羅" |
| `he-IL` | 希伯來文 (以色列) | Male | "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)" | "他-IL-阿薩夫" |
| `hi-IN` | 印度文 (印度) | Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)" | "嗨-卡帕納-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)" | "嗨IN-卡爾帕納" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)" | "嗨，海曼特" |
| `hr-HR` | 克羅埃西亞文 | Male | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)" | "hr-HR-Matej" |
| `hu-HU` | 匈牙利文 | Male | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "胡-胡-薩博茨" |
| `id-ID` | 印尼文 | Male | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)" | "ID-ID-Andika" |
| `it-IT` | 義大利文 | Male | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" | "IT-Cosimo-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)" | "IT-LuciaRUS" |
| `ja-JP` | 日文 | Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-阿波羅" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-一郎阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-哈魯卡魯斯" |
| `ko-KR` | 韓文 | Female | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "科-KR-HeamiRUS" |
| `ms-MY` | 馬來文 | Male | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)" | "ms-MY-裡茲萬" |
| `nb-NO` | 挪威文 | Female | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | "nb-NO-胡爾達魯斯" |
| `nl-NL` | 荷蘭文 | Female | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-漢娜魯斯" |
| `pl-PL` | 波蘭文 | Female | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-保利納魯斯" |
| `pt-BR` | 葡萄牙文 (巴西) | Female | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-赫洛伊薩魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)" | "pt-BR-丹尼爾-阿波羅" |
| `pt-PT` | 葡萄牙文 (葡萄牙) | Female | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | 羅馬尼亞文 | Male | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)" | "RO-RO-安德列" |
| `ru-RU` | 俄文 | Female | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "魯魯-伊琳娜-阿波羅" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "魯魯-帕維爾-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | 魯魯-葉卡捷琳娜魯斯 |
| `sk-SK` | 斯洛伐克文 | Male | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-菲力浦" |
| `sl-SI` | 斯洛維尼亞文 | Male | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)" | "sl-SI-拉多" |
| `sv-SE` | 瑞典文 | Female | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-赫德維加" |
| `ta-IN` | 坦米爾文 (印度) | Male | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)" | "塔-因-瓦盧瓦爾" |
| `te-IN` | 特拉古文 (印度) | Female | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)" | "泰-因-奇特拉" |
| `th-TH` | 泰文 | Male | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | "Th-TH-Pattara" |
| `tr-TR` | 土耳其文 (土耳其) | Female | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-塞達魯斯" |
| `vi-VN` | 越南文 | Male | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)" | "vi-VN-安" |
| `zh-CN` | 中文 (普通話，簡體) | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-匯輝魯斯" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-耀耀-阿波羅" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-康康-阿波羅" |
| `zh-HK` | 中文（粵語，傳統） | Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "日-香港-特蕾西-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "日-香港-特蕾西魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "日-香港-丹尼-阿波羅" |
| `zh-TW` | 中文 (繁體，國語) | Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-亞丁-阿波羅" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-漢漢魯斯" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-志偉-阿波羅" |

**1** *ar-EG 支援現代標準阿拉伯文 （MSA）。*

> [!IMPORTANT]
> 語音`en-US-Jessa`已更改為`en-US-Aria`。 如果您以前使用"傑薩"，則轉換成"阿裡婭"。

> [!TIP]
> 您可以在語音合成請求中使用全服務名稱映射或短語音名稱。

### <a name="customization"></a>自訂

語音`de-DE`自訂可用於 、、、、、、、、、、`zh-CN``en-US``es-MX``fr-FR``it-IT``pt-BR``en-GB``en-IN`和 。 選擇與訓練自訂語音模型時必須的訓練資料相匹配的正確地區設定。 例如，如果您錄製的資料以英語講有英國口音，請選擇`en-GB`。

> [!NOTE]
> 我們不支援自訂語音的雙語模型培訓，但中英文雙語種除外。 如果你想訓練一個能說英語的漢語語音，請選擇"中英文雙語"。 所有地區設定中的語音培訓從 2，000 多個話語的資料集開始，但 和`en-US``zh-CN`位置可以從任何規模的訓練資料開始。

## <a name="speech-translation"></a>語音翻譯

**語音翻譯** API 支援語音轉換語音和語音轉換文字翻譯的不同語言。 來源語言必須始終來自"語音到文本"語言表。 可用的目標語言取決於翻譯目標是語音還是文字。 您可以將傳入的語音翻譯為 [60 多種語言](https://www.microsoft.com/translator/business/languages/)。 語言的子集可用於[語音合成](language-support.md#text-languages)。

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
| 克林貢文                 | `tlh`         |
| 克林貢文 (plqaD)         | `tlh-Qaak`    |
| 韓文                  | `ko`          |
| 拉脫維亞文                 | `lv`          |
| 立陶宛文              | `lt`          |
| 馬達加斯加文                | `mg`          |
| 馬來文                   | `ms`          |
| 馬來亞拉姆文               | `ml`          |
| 馬爾他文                 | `mt`          |
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

* [獲取語音服務試用訂閱](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp) (英文)
