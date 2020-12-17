---
title: 語音合成標記語言 (SSML) -語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音合成標記語言控制文字轉語音的發音和韻律。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5d11f343a55d30e5d14d6f4ae0ddb1a74d9c61fa
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631970"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>使用語音合成標記語言 (SSML) 改進合成

語音合成標記語言 (SSML) 是以 XML 為基礎的標記語言，可讓開發人員指定如何使用文字轉換語音服務，將輸入文字轉換為合成語音。 相較于純文字，SSML 可讓開發人員微調文字到語音轉換輸出的音調、發音、說話率、音量等等。 一般標點符號，例如在句號之後暫停，或在句子結尾為問號的時候，使用正確的聲調來自動處理。

SSML 的語音服務執行是以全球資訊網協會的 [語音合成標記語言版本 1.0](https://www.w3.org/TR/speech-synthesis)為基礎。

> [!IMPORTANT]
> 中文、日文和韓文字元會計算為兩個字元以供計費。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>標準、類神經和自訂語音

從標準和類神經語音中選擇，或為您的產品或品牌建立專屬的自訂語音。 75以上的標準語音提供45以上的語言和地區設定，而5種類神經語音提供四種語言和地區設定。 如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md)。

若要深入瞭解標準、神經和自訂語音，請參閱 [文字轉換語音總覽](text-to-speech.md)。


> [!NOTE]
> 您可以使用 [文字轉換語音頁面](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)來聽取不同樣式的語音，並簡報閱讀範例文字。


## <a name="special-characters"></a>特殊字元

使用 SSML 時，請記住，必須將特殊字元（例如引號、單引號和括弧）換成書簽。 如需詳細資訊，請參閱 [可延伸標記語言 (XML)  (XML) 1.0：附錄 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支援的 SSML 元素

每個 SSML 檔會使用 SSML 元素 (或標記) 來建立。 這些元素可用來調整音調、韻律、音量等等。 下列各節將詳細說明每個元素的使用方式，以及何時需要或選擇性專案。  

> [!IMPORTANT]
> 別忘了在屬性值前後加上雙引號。 格式正確、有效 XML 的標準需要將屬性值括在雙引號中。 例如，是格式正確 `<prosody volume="90">` 且有效的元素，但卻 `<prosody volume=90>` 不是。 SSML 可能無法辨識不在引號中的屬性值。

## <a name="create-an-ssml-document"></a>建立 SSML 檔

`speak` 是根項目，而且是所有 SSML 檔的 **必要** 專案。 `speak`元素包含重要的資訊，例如版本、語言和標記詞彙定義。

**語法**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `version` | 指出用來解讀檔標記之 SSML 規格的版本。 目前的版本為1.0。 | 必要 |
| `xml:lang` | 指定根文檔的語言。 此值可能包含小寫、兩個字母的語言代碼 (例如 `en`) ，或語言代碼和大寫國家/地區 (例如 `en-US`) 。 | 必要 |
| `xmlns` | 指定檔的 URI，該檔會定義 SSML 檔的專案類型和屬性名稱)  (的標記詞彙。 目前的 URI 為 http://www.w3.org/2001/10/synthesis 。 | 必要 |

## <a name="choose-a-voice-for-text-to-speech"></a>選擇文字轉換語音的語音

`voice`需要元素。 它是用來指定用於文字轉換語音的語音。

**語法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `name` | 識別用於文字到語音轉換輸出的語音。 如需支援的語音的完整清單，請參閱 [語言支援](language-support.md#text-to-speech)。 | 必要 |

**範例**

> [!NOTE]
> 這個範例會使用 `en-US-AriaRUS` 語音。 如需支援的語音的完整清單，請參閱 [語言支援](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多重音源

在 `speak` 元素中，您可以為文字到語音轉換輸出指定多個語音。 這些語音可採用不同的語言。 針對每個語音，文字必須包裝在元素中 `voice` 。 

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `name` | 識別用於文字到語音轉換輸出的語音。 如需支援的語音的完整清單，請參閱 [語言支援](language-support.md#text-to-speech)。 | 必要 |

> [!IMPORTANT]
> 多個語音與「字邊界」功能不相容。 必須停用字邊界功能，才能使用多個語音。

### <a name="disable-word-boundary"></a>停用字邊界

根據語音 SDK 語言，您會將 `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` 屬性設定為 `false` 物件實例上的 `SpeechConfig` 。

# <a name="c"></a>[C#](#tab/csharp)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>調整說話樣式

> [!IMPORTANT]
> 說話樣式的調整只會使用類神經語音。

根據預設，文字轉換語音服務會使用標準和類神經語音的中性說話樣式來會合成文字。 使用類神經語音，您可以調整說話樣式來表達不同的表情（例如歡欣鼓舞、理解和平靜），或使用元素將語音用於不同的案例，例如客戶服務、newscasting 和語音助理 `mstts:express-as` 。 這是語音服務唯一的選擇性元素。

目前，下列類神經語音支援說話樣式調整：
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` (預覽)
* `zh-CN-XiaohanNeural` (預覽)
* `zh-CN-XiaomoNeural` (預覽)
* `zh-CN-XiaoxuanNeural` (預覽)
* `zh-CN-XiaoruiNeural` (預覽)

說話樣式的濃度可以進一步變更，以更符合您的使用案例。 您可以指定更強或更柔和的樣式， `styledegree` 讓語音更具表達性或 subdued。 

目前，下列類神經語音支援說話樣式調整：
* `zh-CN-XiaoxiaoNeural`

除了調整說話樣式和樣式的角度之外，您也可以調整 `role` 參數，讓聲音模仿不同的年齡和性別。 例如，男性聲音可以提高音調，並將聲調變更為模仿女性心聲。

目前，下列類神經語音支援角色扮演調整：
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

上述變更會在句子層級套用，而樣式和角色播放則會因語音而異。 如果不支援樣式或角色扮演，服務將會以預設的中性說話方式傳回語音。 您可以透過 [語音清單 API](rest-text-to-speech.md#get-a-list-of-voices) 或透過無程式碼的 [音訊內容建立](https://aka.ms/audiocontentcreation) 平臺，查看每個語音所支援的樣式和角色播放。

**語法**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> 目前 `styledegree` 只支援 zh-CN-XiaoxiaoNeural。 `role` 只支援 zh-CN-XiaomoNeural 和 zh-CN-XiaoxuanNeural。

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `style` | 指定說出的樣式。 目前，說話樣式是語音特定的。 | 如果為類神經語音調整說話樣式，則為必要。 如果使用 `mstts:express-as` ，則必須提供樣式。 如果提供的值無效，則會忽略這個元素。 |
| `styledegree` | 指定說話樣式的濃度。 **接受的值**：0.01 到2（含）。 預設值為1，表示預先定義的樣式濃度。 最小單位為0.01，這會造成目標樣式稍微傾向。 值為2會導致預設樣式濃度加倍。  | 選擇性 (目前 `styledegree` 僅支援 zh-CN-XiaoxiaoNeural。 ) |
| `role` | 指定說話角色的播放。 語音會扮演不同的年齡和性別。  | 選擇性 (目前 `role` 僅支援 zh-cn-XiaomoNeural 和 zh-cn-XiaoxuanNeural。 ) |

您可以使用此資料表來判斷每個神經語音支援哪些說話樣式。

| 語音                   | 樣式                     | 描述                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | 針對新聞傳遞提供正式且自信的授權語氣 |
|                         | `style="newscast-casual"` | 表示一般新聞傳遞的多功能和休閒語氣        |
|                         | `style="customerservice"` | 為客戶支援表達易記且有用的語氣  |
|                         | `style="chat"`            | 表示隨意且寬鬆的色調                         |
|                         | `style="cheerful"`        | 表達正面且快樂的語氣                         |
|                         | `style="empathetic"`      | 表達關懷和理解的意義               |
| `en-US-JennyNeural`     | `style="customerservice"` | 為客戶支援表達易記且有用的語氣  |
|                         | `style="chat"`            | 表示隨意且寬鬆的色調                         |
|                         | `style="assistant"`       | 表達數位助理的暖和寬鬆語氣    |
|                         | `style="newscast"`        | 表示一般新聞傳遞的多功能和休閒語氣   |
| `en-US-GuyNeural`       | `style="newscast"`        | 表示 narrating 新聞的正式和專業語氣 |
| `pt-BR-FranciscaNeural` | `style="calm"`            | 在說話時，表示酷炫、收集和組合的態度。 相較于其他類型的語音，音調、音調、韻律的一致性更加一致。                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | 表示 narrating 新聞的正式和專業語氣 |
|                         | `style="customerservice"` | 為客戶支援表達易記且有用的語氣  |
|                         | `style="assistant"`       | 表達數位助理的暖和寬鬆語氣    |
|                         | `style="chat"`            | 表示閒聊的休閒和寬鬆語氣-聊天           |
|                         | `style="calm"`            | 在說話時，表示酷炫、收集和組合的態度。 相較于其他類型的語音，音調、音調、韻律的一致性更加一致。                                |
|                         | `style="cheerful"`        | 以更高的音調和關切這個領域能源表示樂觀和熱心語氣                         |
|                         | `style="sad"`             | 以較高的音調、較低的濃度和較低的關切這個領域能源來表示 sorrowful 的色調。 這種表情的常見指標會在語音期間 whimpers 或哭泣。            |
|                         | `style="angry"`           | 以較低的音調、更高的強度和更高的關切這個領域能源來表示生氣和感到苦惱的語氣。 說話者處於 irate、displeased 和感冒的狀態。       |
|                         | `style="fearful"`         | 以較高的音調、更高的關切這個領域能源及更快的速率表示害怕和緊張語氣。 喇叭處於 tenseness 和 uneasiness 的狀態。                          |
|                         | `style="disgruntled"`     | 表示 disdainful 和抱怨語氣。 此表情的語音會顯示不悅和藐視。              |
|                         | `style="serious"`         | 表示嚴格和命令的色調。 說話者通常會 stiffer，而且會有公司的步調更寬鬆。          |
|                         | `style="affectionate"`    | 以更高的音調和關切這個領域能源來表示暖和 affectionate 的色調。 說話者的狀態是吸引接聽程式的注意力。 說話者的「個人化」通常是可愛本質。          |     
|                         | `style="gentle"`          | 以較低的音調和關切這個領域能源來表達輕微、親切、愉快的語氣         |   
|                         | `style="lyrical"`         | 以 melodic 和感情因素存在的方式表達表情         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | 為客戶支援表達易記且有用的語氣  | 
| `zh-CN-YunyeNeural`     | `style="calm"`            | 在說話時，表示酷炫、收集和組合的態度。 相較于其他類型的語音，音調、音調、韻律的一致性更加一致。    | 
|                         | `style="cheerful"`        | 以更高的音調和關切這個領域能源表示樂觀和熱心語氣                         |
|                         | `style="sad"`             | 以較高的音調、較低的濃度和較低的關切這個領域能源來表示 sorrowful 的色調。 這種表情的常見指標會在語音期間 whimpers 或哭泣。            |
|                         | `style="angry"`           | 以較低的音調、更高的強度和更高的關切這個領域能源來表示生氣和感到苦惱的語氣。 說話者處於 irate、displeased 和感冒的狀態。       |
|                         | `style="fearful"`         | 以較高的音調、更高的關切這個領域能源及更快的速率表示害怕和緊張語氣。 喇叭處於 tenseness 和 uneasiness 的狀態。                          |
|                         | `style="disgruntled"`     | 表示 disdainful 和抱怨語氣。 此表情的語音會顯示不悅和藐視。              |
|                         | `style="serious"`         | 表示嚴格和命令的色調。 說話者通常會 stiffer，而且會有公司的步調更寬鬆。          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | 以更高的音調和關切這個領域能源表示樂觀和熱心語氣                         |
|                         | `style="sad"`             | 以較高的音調、較低的濃度和較低的關切這個領域能源來表示 sorrowful 的色調。 這種表情的常見指標會在語音期間 whimpers 或哭泣。            |
|                         | `style="angry"`           | 以較低的音調、更高的強度和更高的關切這個領域能源來表示生氣和感到苦惱的語氣。 說話者處於 irate、displeased 和感冒的狀態。       |
|                         | `style="fearful"`         | 以較高的音調、更高的關切這個領域能源及更快的速率表示害怕和緊張語氣。 喇叭處於 tenseness 和 uneasiness 的狀態。                          |
|                         | `style="disgruntled"`     | 表示 disdainful 和抱怨語氣。 此表情的語音會顯示不悅和藐視。              |
|                         | `style="serious"`         | 表示嚴格和命令的色調。 說話者通常會 stiffer，而且會有公司的步調更寬鬆。    |
|                         | `style="depressed"`       | 以較低的音調和能源來表示 melancholic 和 despondent 語氣    |
|                         | `style="embarrassed"`     | 在說話者感覺不舒服時，表達不確定和遲疑的語氣   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | 以更高的音調和關切這個領域能源表示樂觀和熱心語氣                         |
|                         | `style="sad"`             | 以較高的音調、較低的濃度和較低的關切這個領域能源來表示 sorrowful 的色調。 這種表情的常見指標會在語音期間 whimpers 或哭泣。            |
|                         | `style="angry"`           | 以較低的音調、更高的強度和更高的關切這個領域能源來表示生氣和感到苦惱的語氣。 說話者處於 irate、displeased 和感冒的狀態。       |
|                         | `style="fearful"`         | 以較高的音調、更高的關切這個領域能源及更快的速率表示害怕和緊張語氣。 喇叭處於 tenseness 和 uneasiness 的狀態。                          |
|                         | `style="disgruntled"`     | 表示 disdainful 和抱怨語氣。 此表情的語音會顯示不悅和藐視。              |
|                         | `style="serious"`         | 表示嚴格和命令的色調。 說話者通常會 stiffer，而且會有公司的步調更寬鬆。    |
|                         | `style="embarrassed"`     | 在說話者感覺不舒服時，表達不確定和遲疑的語氣   |
|                         | `style="affectionate"`    | 以更高的音調和關切這個領域能源來表示暖和 affectionate 的色調。 說話者的狀態是吸引接聽程式的注意力。 說話者的「個人化」通常是可愛本質。          |     
|                         | `style="gentle"`          | 以較低的音調和關切這個領域能源來表達輕微、親切、愉快的語氣         |   
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | 以更高的音調和關切這個領域能源表示樂觀和熱心語氣                         |
|                         | `style="angry"`           | 以較低的音調、更高的強度和更高的關切這個領域能源來表示生氣和感到苦惱的語氣。 說話者處於 irate、displeased 和感冒的狀態。       |
|                         | `style="fearful"`         | 以較高的音調、更高的關切這個領域能源及更快的速率表示害怕和緊張語氣。 喇叭處於 tenseness 和 uneasiness 的狀態。                          |
|                         | `style="disgruntled"`     | 表示 disdainful 和抱怨語氣。 此表情的語音會顯示不悅和藐視。              |
|                         | `style="serious"`         | 表示嚴格和命令的色調。 說話者通常會 stiffer，而且會有公司的步調更寬鬆。    |
|                         | `style="depressed"`       | 以較低的音調和能源來表示 melancholic 和 despondent 語氣    |
|                         | `style="gentle"`          | 以較低的音調和關切這個領域能源來表達輕微、親切、愉快的語氣         |  
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | 以更高的音調和關切這個領域能源表示樂觀和熱心語氣                         |
|                         | `style="angry"`           | 以較低的音調、更高的強度和更高的關切這個領域能源來表示生氣和感到苦惱的語氣。 說話者處於 irate、displeased 和感冒的狀態。       |
|                         | `style="fearful"`         | 以較高的音調、更高的關切這個領域能源及更快的速率表示害怕和緊張語氣。 喇叭處於 tenseness 和 uneasiness 的狀態。                          |
|                         | `style="disgruntled"`     | 表示 disdainful 和抱怨語氣。 此表情的語音會顯示不悅和藐視。              |
|                         | `style="serious"`         | 表示嚴格和命令的色調。 說話者通常會 stiffer，而且會有公司的步調更寬鬆。    |
|                         | `style="depressed"`       | 以較低的音調和能源來表示 melancholic 和 despondent 語氣    |
|                         | `style="gentle"`          | 以較低的音調和關切這個領域能源來表達輕微、親切、愉快的語氣         |   
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | 以較高的音調、較低的濃度和較低的關切這個領域能源來表示 sorrowful 的色調。 這種表情的常見指標會在語音期間 whimpers 或哭泣。            |
|                         | `style="angry"`           | 以較低的音調、更高的強度和更高的關切這個領域能源來表示生氣和感到苦惱的語氣。 說話者處於 irate、displeased 和感冒的狀態。       |
|                         | `style="fearful"`         | 以較高的音調、更高的關切這個領域能源及更快的速率表示害怕和緊張語氣。 喇叭處於 tenseness 和 uneasiness 的狀態。                          |

您可以使用此資料表來判斷每個神經語音支援哪些角色。

| 語音                   | 角色                       | 描述                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | 語音 imitates 給年輕成人女性。                 |
|                         | `role="OlderAdultMale"`    | 語音 imitates 到較舊的成人男性。                   |
|                         | `role="Girl"`              | Imitates 至女孩的聲音。                               |
|                         | `role="Boy"`               | 將語音 imitates 到男孩。                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | 語音 imitates 給年輕成人女性。                 |
|                         | `role="OlderAdultFemale"`  | 語音 imitates 到較舊的成人女性。                 |
|                         | `role="OlderAdultMale"`    | 語音 imitates 到較舊的成人男性。                   |

**範例**

這個 SSML 程式碼片段說明如何 `<mstts:express-as>` 使用元素來將說話樣式變更為 `cheerful` 。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

這個 SSML 程式碼片段說明如何 `styledegree` 使用屬性來變更 XiaoxiaoNeural 說話樣式的濃度。
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

這個 SSML 程式碼片段說明如何 `role` 使用屬性來變更 XiaomoNeural 的角色扮演。
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>新增或移除中斷/暫停

您 `break` 可以使用元素來插入暫停 (或在文字之間中斷) ，或防止文字轉換語音服務自動暫停。

> [!NOTE]
> 您可以使用此專案來覆寫單字或片語的文字轉換語音 (TTS) 的預設行為（如果該單字或片語的合成語音非自然）。 設定 `strength` 為 `none` 以防止韻律中斷，這會由文字轉換語音服務自動插入。

**語法**

```xml
<break strength="string" />
<break time="string" />
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `strength` | 使用下列其中一個值，指定暫停的相對持續時間：<ul><li>無</li><li>x-弱式</li><li>弱</li><li>中型 (預設) </li><li>強式</li><li>x-強式</li></ul> | 選擇性 |
| `time` | 指定暫停的絕對持續時間（以秒或毫秒為單位），此值應設為小於5000毫秒。 有效值的範例包括 `2s` 和 `500ms` | 選擇性 |

| 強度                      | Description |
|-------------------------------|-------------|
| 無，或如果未提供任何值 | 0毫秒        |
| x-弱式                        | 250 毫秒      |
| 弱                          | 500 毫秒      |
| 中                        | 750 毫秒      |
| 強式                        | 1000毫秒     |
| x-強式                      | 1250毫秒     |

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>新增無聲

您 `mstts:silence` 可以使用元素來插入在文字之前或之後暫停，或在2個連續的句子之間插入。 

> [!NOTE]
>和之間的 `mstts:silence` 差異 `break` 是 `break` 可以加入至文字中的任何位置，但無回應只適用于輸入文字的開頭或結尾，或2個相鄰句子的界限。  


**語法**

```xml
<mstts:silence  type="string"  value="string"/>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `type` | 指定要新增無聲的位置： <ul><li>前置-在文字開頭 </li><li>尾端–在文字結尾 </li><li>Sentenceboundary –相鄰句子之間 </li></ul> | 必要 |
| `Value` | 指定暫停的絕對持續時間（以秒或毫秒為單位），此值應設為小於5000毫秒。 有效值的範例包括 `2s` 和 `500ms` | 必要 |

**範例** 在此範例中， `mtts:silence` 是用來在兩個句子之間新增200毫秒的靜音。
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">  
<voice name="en-US-AriaNeural"> 
<mstts:silence  type="Sentenceboundary" value="200ms"/> 
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way. 
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time. 
</voice> 
</speak> 
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p` 和 `s` 元素分別用於表示段落和句子。 如果沒有這些元素，則文字轉換語音服務會自動判斷 SSML 檔的結構。

`p`元素可包含文字和下列元素： `audio` 、 `break` 、 `phoneme` 、 `prosody` 、 `say-as` 、 `sub` 、 `mstts:express-as` 和 `s` 。

`s`元素可包含文字和下列元素： `audio` 、 `break` 、 `phoneme` 、 `prosody` 、 `say-as` 、 `mstts:express-as` 和 `sub` 。

**語法**

```XML
<p></p>
<s></s>
```

**範例**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素來改善發音

專案 `ph` 是用來表示 SSML 檔中的語音發音。 `ph`元素只能包含文字，而不能包含其他元素。 一律提供人們看得懂的語音作為回復。

語音字母是由電話組成，也就是由字母、數位或字元組成，有時會組合。 每個電話都描述語音的獨特音效。 這與拉丁字母相反，其中任何字母可能代表多個說話音效。 請考慮字母 "c" 的不同發音（以「糖果」和「停止」文字），或在「內容」和「那些」字組的不同發音中加上字母組合「th」。

> [!NOTE]
> 這5個語音不支援音素標籤 (et-AnuNeural、ga-IE-OrlaNeural、lt-OnaNeural、lv-LV-EveritaNeural 和 mt-GarceNeural) 目前。

**語法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `alphabet` | 指定當合成屬性中字串的發音時，所要使用的拼音字母 `ph` 。 指定字母的字串必須以小寫字母來指定。 以下是您可以指定的可能字母。<ul><li>`ipa`&ndash;<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">國際注音字母 <span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash;[語音服務拼音字母](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash;<a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">通用電話組</a></li></ul><br>字母只適用于 `phoneme` 元素中的。 | 選擇性 |
| `ph` | 字串，包含指定元素中單字發音的電話 `phoneme` 。 如果指定的字串包含無法辨識的電話，則文字轉換語音 (TTS) 服務會拒絕整個 SSML 檔，並且不會產生檔中指定的任何語音輸出。 | 如果使用音素，則為必要。 |

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>使用自訂字典改善發音

有時候，文字轉換語音服務無法精確地發音單字。 例如，公司的名稱或醫療詞彙。 開發人員可以使用和標記定義在 SSML 中讀取單一實體的方式 `phoneme` `sub` 。 但是，如果您需要定義如何讀取多個實體，您可以使用標記來建立自訂字典 `lexicon` 。

> [!NOTE]
> 自訂字典目前支援 UTF-8 編碼。 

> [!NOTE]
> 自訂字典不支援這些5個語音 (et-AnuNeural、ga-IE-OrlaNeural、lt-OnaNeural、lv-LV-EveritaNeural 和 mt-GarceNeural) 目前。


**語法**

```XML
<lexicon uri="string"/>
```

**屬性**

| 屬性 | 描述                               | 必要/選用 |
|-----------|-------------------------------------------|---------------------|
| `uri`     | 外部另外檔的位址。 | 必要。           |

**使用方式**

若要定義如何讀取多個實體，您可以建立自訂的詞典，它會儲存為 .xml 或另外檔案。 以下是範例 .xml 檔案。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

`lexicon`元素至少包含一個 `lexeme` 元素。 每個 `lexeme` 元素都至少包含一個專案， `grapheme` 以及一個或多個 `grapheme` 、 `alias` 和 `phoneme` 元素。 `grapheme`元素包含描述<a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography <span class="docon docon-navigate-external x-hidden-focus"></span> </a>的文字。 `alias`元素用來表示縮寫或縮寫詞彙的發音。 專案 `phoneme` 會提供描述發音方式的文字 `lexeme` 。

請務必注意，您無法使用自訂字典直接設定片語的發音。 如果您需要設定縮寫或縮寫詞彙的發音，請先提供 `alias` ，然後將 `phoneme` 與關聯 `alias` 。 例如：

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>ScotlandMV</alias> 
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme> 
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

您也可以直接提供所需的 `alias` 縮寫或縮寫字詞。 例如：
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>Scotland Media Wave</alias> 
  </lexeme>
```

> [!IMPORTANT]
> `phoneme`使用 IPA 時，元素不能包含空白字元。

如需有關自訂字典檔案的詳細資訊，請參閱 [ (另外) 1.0 版的發音詞典規格](https://www.w3.org/TR/pronunciation-lexicon/)。

接下來，發行您的自訂字典檔案。 雖然我們不會限制儲存此檔案的位置，但我們建議使用 [Azure Blob 儲存體](../../storage/blobs/storage-quickstart-blobs-portal.md)。

發佈自訂字典之後，您就可以從 SSML 參考它。

> [!NOTE]
> `lexicon`元素必須在專案內 `voice` 。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

使用這個自訂的詞典時，"您" 將會以「單向」的方式讀取。 將會使用所提供的 IPA "bɛˈ ni ː nji" 來讀取 "Benigni"。  

**限制**
- 檔案大小：自訂字典檔案大小上限為 100 KB，如果超出此大小，合成要求將會失敗。
- 詞典快取重新整理：自訂字典首次載入時，將會以 URI 作為索引鍵來進行快取。 具有相同 URI 的詞典將不會在15分鐘內重載，因此自訂的詞典變更必須等待最多15分鐘才會生效。

**語音服務拼音組**

在上述範例中，我們使用國際注音字母，也稱為 IPA phone 組。 我們建議開發人員使用 IPA，因為它是國際標準。 針對某些 IPA 字元，當使用 Unicode 表示時，它們會有 ' precomposed ' 和 ' 分解 ' 版本。 自訂字典只支援分解的 unicodes。

考慮到 IPA 並不容易記住，語音服務會定義七種語言的語音， (、、 `en-US` 、、、 `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` 和 `zh-TW`) 。

您可以使用 `sapi` 做為 `alphabet` 具有自訂字典之屬性的銷售額，如下所示：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

如需詳細的語音服務拼音字母的詳細資訊，請參閱 [語音服務拼音設定](speech-ssml-phonetic-sets.md)。

## <a name="adjust-prosody"></a>調整韻律

`prosody`元素可用來指定文字到語音轉換輸出的音調、輪廓、範圍、速率、持續時間和音量的變更。 `prosody`元素可包含文字和下列元素： `audio` 、 `break` 、 `p` 、 `phoneme` 、 `prosody` 、 `say-as` 、 `sub` 和 `s` 。

因為韻律屬性值可能會隨著廣泛的範圍而改變，所以語音辨識器會將指派的值解釋為所選語音之實際韻律值的建議。 文字轉換語音服務會限制或替代不支援的值。 不支援值的範例是 1 MHz 或磁片區120。

**語法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `pitch` | 表示文字的基準間距。 您可以將推銷表達為：<ul><li>絕對值，以數位開頭，後面接著 "Hz" (赫茲) 。 例如： `<prosody pitch="600Hz">some text</prosody>` 。</li><li>相對值，以前面加上 "+" 或 "-"，後面接著 "Hz" 或 "st" 的數位表示，以指定要變更音調的數量。 例如：`<prosody pitch="+80Hz">some text</prosody>` 或 `<prosody pitch="-2st">some text</prosody>`。 "St" 表示變更單位是 semitone，也就是標準 diatonic 小數位數的一半步驟) 的一半 (。</li><li>常數值：<ul><li>x-低</li><li>low</li><li>中</li><li>high</li><li>x-高</li><li>預設</li></ul></li></ul> | 選擇性 |
| `contour` |等高線現在支援類神經和標準語音。 等高線表示音調變化。 這些變更會在語音輸出中指定的時間位置，以目標陣列的形式表示。 每個目標都是由一組參數配對所定義。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每一組參數中的第一個值會指定音調變更的位置，以文字持續時間的百分比表示。 第二個值指定要提高或減少間距的數量，使用相對值的相對值或列舉值 (請參閱 `pitch`) 。 | 選擇性 |
| `range` | 值，表示文字的音調範圍。 您可以 `range` 使用相同的絕對值、相對值或用來描述的列舉值來表示 `pitch` 。 | 選擇性 |
| `rate` | 指出文字的說話率。 您可以表達 `rate` ：<ul><li>相對值，以做為預設值乘數的數位來表示。 例如，值為 *1* 會導致費率沒有變更。 值為 *0.5* 時，會產生速率減半。 值為 *3* 時，會產生速率增加三倍。</li><li>常數值：<ul><li>x-慢</li><li>slow</li><li>中</li><li>快速</li><li>x-快速</li><li>預設</li></ul></li></ul> | 選擇性 |
| `duration` | 當語音合成 (TTS) 服務讀取文字（以秒或毫秒為單位）時，應該經過的時間長度。 例如，2 *或* *1800ms*。 持續時間僅支援標準語音。| 選擇性 |
| `volume` | 表示說話聲音的音量層級。 您可以將磁片區表達為：<ul><li>絕對值，以0.0 到100.0 之間的數位表示，從 *quietest* 到 *loudest*。 例如，75。 預設值為100.0。</li><li>相對值，以前面加上 "+" 或 "-" 的數位表示，以指定要變更磁片區的數量。 例如，+ 10 或-5.5。</li><li>常數值：<ul><li>silent</li><li>x-軟</li><li>軟</li><li>中</li><li>大聲</li><li>x-朗讀</li><li>預設</li></ul></li></ul> | 選擇性 |

### <a name="change-speaking-rate"></a>改變說話速度

說出的速率可以套用至單字或句子層級的神經語音和標準語音。 

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>變更音量

磁片區變更可以套用至單字或句子層級的標準語音。 但是，磁片區變更只能套用至句子層級的類神經語音。

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>變更音高

音調變更可以套用至單字或句子層級的標準語音。 但是，只有在句子層級的神經語音才能套用音調變更。

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>變更音高結構

> [!IMPORTANT]
> 類神經語音現在支援音調輪廓變更。

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room? 
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>口述元素

`say-as` 這是一個選擇性元素，表示內容類型 (例如專案文字的數位或日期) 。 這會針對語音合成引擎提供有關如何將文字發音的指引。

**語法**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `interpret-as` | 指出元素文字的內容類型。 如需類型清單，請參閱下表。 | 必要 |
| `format` | 針對可能具有不明確格式的內容類型，提供有關專案文字精確格式的其他資訊。 SSML 會定義使用這些內容類型的格式 (請參閱下表) 。 | 選擇性 |
| `detail` | 指出要說出的詳細資料層級。 例如，這個屬性可能會要求語音合成引擎發音標點符號。 未定義任何標準值 `detail` 。 | 選擇性 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

以下是和屬性支援的內容類型 `interpret-as` `format` 。 `format`只有當 `interpret-as` 設為日期和時間時，才會包含屬性。

| 解讀為 | format | 解讀 |
|--------------|--------|----------------|
| `address` | | 文字是以位址的形式讀出。 語音合成引擎 pronounces：<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />「我在150th 法庭華盛頓州 redmond」。 |
| `cardinal`, `number` | | 文字會以基本數位的形式讀出。 語音合成引擎 pronounces：<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />「有三個替代方案」。 |
| `characters`, `spell-out` | | 文字會以個別字母的形式讀出 (拼寫) 。 語音合成引擎 pronounces：<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />As "T E S T." |
| `date` | dmy、mdy、ymd、ydm、ym、my、md、dm、d、m、y | 文字會以日期的形式讀出。 `format`屬性會指定日期格式 (*d = day、m = month 和 y = year*) 。 語音合成引擎 pronounces：<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />「今天是10月第十九個2016」。 |
| `digits`, `number_digit` | | 文字是以個別數位的序列來讀出。 語音合成引擎 pronounces：<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />為 "1 2 3 4 5 6 7 8 9"。 |
| `fraction` | | 文字會以小數的形式讀出。 語音合成引擎 pronounces：<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />「三個八位的三個」。 |
| `ordinal` | | 文字是以序數的形式讀出。 語音合成引擎 pronounces：<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />作為 [選取第三個選項]。 |
| `telephone` | | 文字是以電話號碼讀出。 `format`屬性可包含代表國家（地區）代碼的數位。 例如，美國的 "1" 或義大利的 "39"。 語音合成引擎可以使用這項資訊來引導其電話號碼的發音。 電話號碼也可能包含國家/地區代碼，如果是，則會優先于中的國家/地區代碼 `format` 。 語音合成引擎 pronounces：<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />「我的數位是區碼 8 8 8 5 5 5 1 2 1 2」。 |
| `time` | hms12, hms24 | 文字會以時間的形式讀出。 `format`屬性（attribute）會指定使用12小時制指定時間 (hms12) 或24小時制 (hms24) 。 使用冒號分隔代表小時、分鐘和秒數的數位。 以下是有效的時間範例：12:35、1:14:32、08:15 和02:50:45。 語音合成引擎 pronounces：<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />作為「訓練離開，共四個 M」。 |

**使用方式**

`say-as`元素只能包含文字。

**範例**

語音合成引擎會以下列範例的形式演講：「您的第一個要求是在10月第十九個 20 10 的一個房間內，提早抵達下午 12 35。」
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>新增錄製的音訊

`audio` 是選擇性元素，可讓您將 MP3 音訊插入 SSML 檔中。 音訊元素的主體可能包含純文字或 SSML 標記（如果音訊檔案無法使用或播放的話）。 此外，專案 `audio` 可以包含文字和下列元素： `audio` 、 `break` 、 `p` 、 `s` 、 `phoneme` 、 `prosody` 、 `say-as` 和 `sub` 。

SSML 檔中包含的任何音訊都必須符合下列需求：

* MP3 必須裝載在可存取網際網路的 HTTPS 端點上。 需要 HTTPS，而且裝載 MP3 檔案的網域必須出示有效且受信任的 TLS/SSL 憑證。
* MP3 必須是有效的 MP3 檔 (MPEG v2) 。
* 位元速率必須為 48 kbps。
* 取樣率必須為 16000 Hz。
* 單一回應中所有文字和音訊檔案的合併總時間不能超過 90 (90) 秒。
* MP3 不得包含任何客戶特定或其他機密資訊。

**語法**

```xml
<audio src="string"/></audio>
```

**屬性**

| 屬性 | 描述                                   | 必要/選用                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | 指定音訊檔案的位置/URL。 | 如果在 SSML 檔中使用音訊元素，則為必要專案。 |

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>新增背景音訊

`mstts:backgroundaudio`元素可讓您將背景音訊新增至 SSML 檔 (或混合音訊檔案與文字轉換語音的) 。 `mstts:backgroundaudio`您可以使用在背景中迴圈音訊檔案、在文字轉換語音的開頭淡化，以及將文字轉換成語音的結尾淡出。

如果所提供的背景音訊短于文字轉換語音，或淡出，則會迴圈。 如果它的長度超過文字轉換語音，就會在淡化完成時停止。

每個 SSML 檔只允許一個背景音訊檔案。 不過，您可以在專案 `audio` 內散置標記， `voice` 以將其他音訊新增至 SSML 檔。

**語法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `src` | 指定背景音訊檔案的位置/URL。 | 如果在 SSML 檔中使用背景音訊，則為必要項。 |
| `volume` | 指定背景音訊檔案的磁片區。 **接受的值**： `0` 至 `100` 內含。 預設值是 `1`。 | 選擇性 |
| `fadein` | 指定背景音訊「淡入」的持續時間（以毫秒為單位）。 預設值為 `0` ，相當於不淡入。 **接受的值**： `0` 至 `10000` 內含。  | 選擇性 |
| `fadeout` | 指定背景音訊淡出的持續時間（以毫秒為單位）。 預設值為 `0` ，相當於不淡出。 **接受的值**： `0` 至 `10000` 內含。  | 選擇性 |

**範例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>下一步

* [語言支援：語音、地區設定、語言](language-support.md)
