---
title: 語音合成標記語言 （SSML） - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音合成標記語言控制文字轉語音的發音和韻律。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7d5dd79399b15ade90173a55aeb71dacbc61fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80365819"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>使用語音合成標記語言 （SSML） 改進合成

語音合成標記語言 （SSML） 是一種基於 XML 的標記語言，它允許開發人員使用文本到語音服務指定輸入文本如何轉換為合成語音。 與純文字相比，SSML 允許開發人員微調音高、發音、說話速率、音量以及更多文本到語音輸出。 正常的標點符號，如句後暫停，或在句子以問號結尾時使用正確的語調。

SSML 的語音服務實現基於萬維網聯盟的[語音合成標記語言版本 1.0。](https://www.w3.org/TR/speech-synthesis)

> [!IMPORTANT]
> 中文、日語和韓語字元計為兩個字元進行計費。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>標準、神經和自訂語音

從標準和神經語音中進行選擇，或創建自己的自訂語音，這些語音是您的產品或品牌獨有的。 75+ 標準語音提供超過 45 種語言和地區設定，5 個神經語音提供四種語言和地區設定。 如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md)。

要瞭解有關標準語音、神經網路和自訂語音的詳細資訊，請參閱[文本到語音概述](text-to-speech.md)。

## <a name="special-characters"></a>特殊字元

使用 SSML 時，請記住必須轉義特殊字元，如引號、撇號和括弧。 有關詳細資訊，請參閱[可擴展標記語言 （XML） 1.0：附錄 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支援的 SSML 元素

每個 SSML 文檔都是使用 SSML 元素（或標記）創建的。 這些元素用於調整音高、原聲、體積等。 以下各節詳細介紹了如何使用每個元素，以及何時需要元素或可選元素。  

> [!IMPORTANT]
> 不要忘記在屬性值周圍使用雙引號。 格式良好、有效的 XML 標準要求屬性值以雙引號括起來。 例如，`<prosody volume="90">`是格式良好的有效元素，但不是`<prosody volume=90>`。 SSML 可能無法識別不在引號中的屬性值。

## <a name="create-an-ssml-document"></a>創建 SSML 文檔

`speak`是根項目，是所有 SSML 文檔**所必需的**。 該`speak`元素包含重要資訊，如版本、語言和標記詞彙定義。

**語法**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `version` | 指示用於解釋文檔標記的 SSML 規範的版本。 當前版本為 1.0。 | 必要 |
| `xml:lang` | 指定根文檔的語言。 該值可能包含小寫、雙字母語言代碼（例如`en`），或者語言代碼和大寫國家/區域（例如。 `en-US` | 必要 |
| `xmlns` | 指定定義 SSML 文檔的標記詞彙表（元素類型和屬性名稱）的文檔的 URI。 當前 URIhttp://www.w3.org/2001/10/synthesis為 。 | 必要 |

## <a name="choose-a-voice-for-text-to-speech"></a>為文本到語音選擇語音

該`voice`元素是必需的。 它用於指定用於文本到語音的語音。

**語法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `name` | 標識用於文本到語音輸出的語音。 有關支援語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。 | 必要 |

**範例**

> [!NOTE]
> 此示例使用語音`en-US-AriaRUS`。 有關支援語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多重音源

在元素`speak`中，可以為文本到語音輸出指定多個語音。 這些聲音可以有不同的語言。 對於每個語音，文本必須包裝在一個`voice`元素中。 

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `name` | 標識用於文本到語音輸出的語音。 有關支援語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。 | 必要 |

> [!IMPORTANT]
> 多個語音與單詞邊界功能不相容。 需要禁用單詞邊界特徵才能使用多個語音。

### <a name="disable-word-boundary"></a>禁用單詞邊界

根據語音 SDK 語言，您將在`"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"``false``SpeechConfig`物件的實例上將屬性設置為。

# <a name="c"></a>[C#](#tab/csharp)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank">`SetProperty`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank">`SetProperty`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[JAVA](#tab/java)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank">`setProperty`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank">`set_property_by_name`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank">`setProperty`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[目標C](#tab/objectivec)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">`setPropertyTo`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">`setPropertyTo`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

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

## <a name="adjust-speaking-styles"></a>調整說話風格

> [!IMPORTANT]
> 說話風格的調整只能與神經聲音一起工作。

預設情況下，文本到語音服務使用中性語音樣式為標準語音和神經語音合成文本。 通過神經語音，您可以調整說話風格，以表達快樂、同情或情緒元素`<mstts:express-as>`。 這是語音服務獨有的可選元素。

目前，支援這些神經語音的說話風格調整：
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `pt-BR-FranciscaNeural`

更改在句子級別應用，樣式因語音而異。 如果不支援樣式，服務將以預設中性發言樣式返回語音。

**語法**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `style` | 指定說話樣式。 目前，說話風格是特定于語音的。 | 如果調整神經語音的說話風格，則需要。 如果使用`mstts:express-as`，則必須提供樣式。 如果提供了無效值，則將忽略此元素。 |

使用此表可確定每個神經語音支援哪些語音樣式。

| 語音 | Style | 描述 |
|-------|------|-------------|
| `en-US-AriaNeural` | `style="newscast"` | 表達正式和專業的語氣，講述新聞 |
| | `style="customerservice"` | 為客戶支援表達友好且有用的語氣 |
| | `style="chat"` | 表達一個隨意和輕鬆的語氣 |
| | `style="cheerful"` | 表達積極和快樂的語氣 |
| | `style="empathetic"` | 表達關懷和理解感 |
| `zh-CN-XiaoxiaoNeural` | `style="newscast"` | 表達正式和專業的語氣，講述新聞 |
| | `style="customerservice"` | 為客戶支援表達友好且有用的語氣 |
| | `style="assistant"` | 為數字助理表達溫暖而輕鬆的語氣  |
| | `style="lyrical"` | 以旋律和感傷的方式表達情感 |
| `pt-BR-FranciscaNeural` | `style="cheerful"` | 表達積極和快樂的語氣 |

**範例**

此 SSML 程式碼片段`<mstts:express-as>`說明了如何使用元素將說話樣式更改為`cheerful`。

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

## <a name="add-or-remove-a-breakpause"></a>添加或刪除中斷/暫停

使用`break`元素在單詞之間插入暫停（或中斷），或防止文本到語音服務自動添加的暫停。

> [!NOTE]
> 如果單詞或短語的合成語音聽起來不自然，則使用此元素可以覆蓋單詞或短語的文本到語音 （TTS） 的預設行為。 設置為`strength``none`防止由文本到語音轉換服務自動插入的正體中斷。

**語法**

```xml
<break strength="string" />
<break time="string" />
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `strength` | 使用以下值之一指定暫停的相對持續時間：<ul><li>無</li><li>x 弱</li><li>弱</li><li>中等（預設）</li><li>強式</li><li>x 強</li></ul> | 選用 |
| `time` | 指定暫停的絕對持續時間（以秒或毫秒為單位）。 有效值的示例為`2s`和`500` | 選用 |

| 強度 | 描述 |
|----------|-------------|
| 無，或者沒有提供值 | 0 毫秒 |
| x 弱 | 250 毫秒 |
| 弱 | 500 毫秒 |
| 中 | 750 毫秒 |
| 強式 | 1000 毫秒 |
| x 強 | 1250 毫秒 |


**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p`元素`s`分別用於表示段落和句子。 如果沒有這些元素，文本到語音轉換服務會自動確定 SSML 文檔的結構。

該`p`元素可能包含`audio`文本和以下元素： `break`、 、 `phoneme` `prosody`、 `say-as` `sub`、 `mstts:express-as`、 `s`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、

該`s``audio`元素可能包含文本和以下元素： `break`、 、 `phoneme` `prosody`、 `say-as` `mstts:express-as`、 `sub`、 、 、 、 、 、 和 。

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

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音語提高發音

該`ph`元素用於 SSML 文檔中的語音發音。 元素`ph`只能包含文本，不包含其他元素。 始終提供人類可讀的語音作為回退。

語音字母表由電話組成，由字母、數位或字元組成，有時組合在一起。 每部手機都描述了獨特的語音。 這與拉丁字母不同，拉丁字母表顯示任意字母可以表示多個語音。 考慮字母"c"在單詞"糖果"和"停止"的不同發音，或字母組合"th"在單詞"事物"和"那些"的不同發音。

**語法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `alphabet` | 指定在合成`ph`屬性中字串的發音時要使用的拼音字母表。 指定字母的字串必須以小寫字母指定。 以下是您可以指定的可能字母表。<ul><li>`ipa`&ndash;<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">國際拼音字母<span class="docon docon-navigate-external x-hidden-focus"></span>表</a></li><li>`sapi`&ndash;[語音服務拼音字母表](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash;通用電話組</li></ul><br>字母表僅適用于`phoneme`元素中的 。 | 選用 |
| `ph` | 包含在元素中指定單詞發音的手機的`phoneme`字串。 如果指定的字串包含無法識別的電話，則文本到語音轉換 （TTS） 服務將拒絕整個 SSML 文檔，並且不會生成文檔中指定的語音輸出。 | 使用音名時需要。 |

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

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>使用自訂字典提高發音

有時 TTS 不能準確發音一個單詞，例如，公司或外國名稱。 開發人員可以使用`phoneme`和`sub`標記在 SSML 中定義這些實體的讀取，或者通過使用`lexicon`標記引用自訂字典檔的讀取來定義多個實體的讀取。

**語法**

```XML
<lexicon uri="string"/>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `uri` | 外部 PLS 文檔的位址。 | 必要。 |

**使用**

第 1 步：定義自訂字典 

您可以按自訂字典項清單定義實體的讀取，這些自訂字典項存儲為 .xml 或 .pls 檔。

**範例**

```xml
<?xml version="1.0" encoding="UTF-16"?>
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

每個`lexeme`元素都是一個詞典項。 `grapheme`包含描述 的`lexeme`正交器的文本。 讀出表單可以作為`alias`提供。 電話字串可以在元素中`phoneme`提供。

該`lexicon`元素至少包含一個`lexeme`元素。 每個`lexeme`元素至少包含一`grapheme`個元素和一個或多個`grapheme`，`alais`以及`phoneme`元素。 元素`grapheme`包含描述<a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">地勢<span class="docon docon-navigate-external x-hidden-focus"></span></a>的文本。 元素`alias`用於表示首字母縮略詞或縮寫詞的發音。 該`phoneme`元素提供描述如何`lexeme`發音的文本。

有關自訂字典檔的詳細資訊，請參閱 W3C 網站上的[發音詞典規範 （PLS） 版本 1.0。](https://www.w3.org/TR/pronunciation-lexicon/)

步驟 2：上傳在步驟 1 中創建的自訂字典檔連線，您可以將其存儲在任何位置，我們建議您將其存儲在 Microsoft Azure 中，例如[Azure Blob 存儲](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

第 3 步：請參閱 SSML 中的自訂字典檔

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"順便說一下"將改為"BTW"。 "Benigni"將閱讀提供IPA"b_ni_nji"。  

**限制**
- 檔案大小：自訂字典檔案大小最大限制為 100KB，如果超出此大小，合成請求將失敗。
- Lexicon 緩存刷新：自訂字典將在首次載入時以 URI 作為 TTS 服務上的金鑰進行緩存。 具有相同 URI 的詞典不會在 15 分鐘內重新載入，因此自訂字典更改最多需要等待 15 分鐘才能生效。

**語音服務語音集**

在上面的示例中，我們使用的是國際拼音字母表，也稱為 IPA 電話組。 我們建議開發人員使用 IPA，因為它是國際標準。 考慮到 IPA 不容易記住，語音服務為七種語言（、、、、、、、、、、`en-US``fr-FR``de-DE``es-ES``ja-JP``zh-CN`和`zh-TW`）定義了拼音集。

您可以使用 作為`sapi``alphabet`屬性的 vale，具有自訂字典，如下所示：

```xml
<?xml version="1.0" encoding="UTF-16"?>
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

有關詳細的語音服務拼音字母的詳細資訊，請參閱[語音服務拼音集](speech-ssml-phonetic-sets.md)。

## <a name="adjust-prosody"></a>調整生育

該`prosody`元素用於指定文本到語音輸出的間距、輪廓、範圍、速率、持續時間和音量的更改。 該`prosody`元素可能包含`audio`文本和以下元素： `break`、 、 `p` `phoneme`、 `prosody` `say-as`、 `sub`、 `s`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、

由於原體屬性值可能因範圍而異，因此語音辨識器將分配的值解釋為所選語音的實際原體值應是什麼的建議。 文本到語音轉換服務限制或替換不支援的值。 不支援的值的示例包括 1 MHz 的間距或 120 的音量。

**語法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `pitch` | 指示文本的基線間距。 您可以表示間距：<ul><li>絕對值，以數位表示，後跟"Hz"（赫茲）。 例如，600 Hz。</li><li>相對值，以數位表示，前面是"+"或"-"，後跟"Hz"或"st"，指定要更改間距的量。 例如：+80 Hz 或 -2st。 "st"表示更改單元為半色調，即標準音調刻度上的半音（半步）。</li><li>常量值：<ul><li>x-低</li><li>low</li><li>中</li><li>high</li><li>x-高</li><li>default</li></ul></li></ul>. | 選用 |
| `contour` | 神經語音不支援輪廓。 輪廓表示間距的變化。 這些更改在語音輸出中指定的時間位置表示為目標陣列。 每個目標由參數對集定義。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每組參數中的第一個值指定間距變化的位置，作為文本持續時間的百分比。 第二個值使用相對值或枚舉值指定要提高或降低間距的金額（請參閱`pitch`）。 | 選用 |
| `range` | 表示文本間距範圍的值。 您可以使用用於描述`range``pitch`的相同絕對值、相對值或枚舉值進行表示。 | 選用 |
| `rate` | 指示文本的講量。 您可以表示： `rate`<ul><li>作為預設乘數的數位表示的相對值。 例如，值*1*會導致速率沒有變化。 值*0.5*會導致速率減半。 值為*3*會導致速率的三次。</li><li>常量值：<ul><li>x慢速</li><li>slow</li><li>中</li><li>快速</li><li>x 快速</li><li>default</li></ul></li></ul> | 選用 |
| `duration` | 語音合成 （TTS） 服務讀取文本時應經過的時間段（以秒或毫秒為單位）。 例如 *，2 或* *1800ms*。 | 選用 |
| `volume` | 指示說話語音的音量級別。 您可以表示音量為：<ul><li>絕對值，以 0.0 到 100.0 的範圍的數位表示，從*最安靜*到*最響亮*。 例如，75。 預設值為 100.0。</li><li>一個相對值，以數位表示，前面是"+"或"-"，指定要更改卷的金額。 例如，+10 或 -5.5。</li><li>常量值：<ul><li>silent</li><li>x 軟</li><li>軟</li><li>中</li><li>大聲</li><li>X-大聲</li><li>default</li></ul></li></ul> | 選用 |

### <a name="change-speaking-rate"></a>改變說話速度

發言率可以應用於單詞或句子級別的標準語音。 而說話率只能應用於句子級別的神經聲音。

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>變更音量

音量更改可以應用於單詞或句子級別的標準語音。 而音量變化只能應用於句子級別的神經語音。

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

間距變化可以應用於單詞或句子級別的標準語音。 而音調變化只能應用於句子級別的神經聲音。

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
> 神經語音不支援音調輪廓變化。

**範例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>說-作為元素

`say-as`是指示元素文本的內容類型（如數位或日期）的可選元素。 這為語音合成引擎提供了有關如何發音文本的指導。

**語法**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `interpret-as` | 指示元素文本的內容類型。 有關類型清單，請參閱下表。 | 必要 |
| `format` | 提供有關元素文本的精確格式設置（可能具有不明確格式的內容類型）的其他資訊。 SSML 為使用它們的內容類型定義格式（請參見下表）。 | 選用 |
| `detail` | 指示要說出的細節級別。 例如，此屬性可能要求語音合成引擎發音標點符號。 沒有為`detail`定義標準值。 | 選用 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

以下是`interpret-as`和`format`屬性的支援內容類型。 僅當設置為`format`日期和時間時`interpret-as`，才包括該屬性。

| 解釋為 | format | 解譯 |
|--------------|--------|----------------|
| `address` | | 文本以位址表示。 語音合成引擎為：<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />作為"我在東北第150法院，雷德蒙德華盛頓。 |
| `cardinal`, `number` | | 文本以基數表示。 語音合成引擎為：<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />作為"有三種選擇"。 |
| `characters`, `spell-out` | | 文本以單個字母（拼寫出來）表示。 語音合成引擎為：<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />作為"T E E S T"。 |
| `date` | dmy， mdy， ymd， ydm， ym， 我的， md， dm， d， m， y | 文本以日期表示。 該`format`屬性指定日期的格式 *（d=day、m=月和 y=年*）。 語音合成引擎為：<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />正如"今天是十九二十六十六年"。 |
| `digits`, `number_digit` | | 文本以單個數位序清單示。 語音合成引擎為：<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />如"1 2 3 4 5 6 7 8 9"。 |
| `fraction` | | 文本以小數表示。 語音合成引擎為：<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />作為"八分之三一英寸"。 |
| `ordinal` | | 文本以帶形式編號表示。 語音合成引擎為：<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />作為"選擇第三個選項"。 |
| `telephone` | | 文本以電話號碼表示。 該`format`屬性可能包含表示國家/地區代碼的數位。 例如，美國的"1"或義大利的"39"。 語音合成引擎可以使用此資訊來指導其電話號碼的發音。 電話號碼還可以包括國家/地區代碼，如果是，則優先于 中的`format`國家/地區代碼。 語音合成引擎為：<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />作為"我的號碼是區號八八八五五五一二一二。 |
| `time` | hms12， hms24 | 文本作為時間發言。 該`format`屬性指定時間是使用 12 小時時鐘 （hms12） 還是 24 小時制 （hms24） 指定。 使用冒號分隔表示小時、分鐘和秒的數位。 以下是有效的時間示例：12：35、1：14：32、08：15 和 02：50：45。 語音合成引擎為：<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />當"火車在四A M處開動" |

**使用**

元素`say-as`可能僅包含文本。

**範例**

語音合成引擎講了以下示例："您的第一個請求是在 10 月 19 日 20 點 10 分提前到達 1235 PM 的一個房間。
 
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

## <a name="add-recorded-audio"></a>添加錄製的音訊

`audio`是允許您將 MP3 音訊插入到 SSML 文檔中的可選元素。 如果音訊檔不可用或無法播放，音訊元素的正文可能包含純文字或 SSML 標記。 此外，該`audio`元素可以包含文本`audio`和以下元素： 、 `break` `p`、 `s` `phoneme`、 `prosody` `say-as`、 `sub`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、

SSML 文檔中包含的任何音訊都必須符合以下要求：

* MP3 必須託管在可 Internet 訪問的 HTTPS 終結點上。 需要 HTTPS，託管 MP3 檔的域必須出示有效的、受信任的 TLS/SSL 憑證。
* MP3 必須是有效的 MP3 檔（MPEG v2）。
* 位元速率必須為 48 kbps。
* 取樣速率必須為 16，000 Hz。
* 單個回應中所有文本和音訊檔的總總時間不得超過九十 （90） 秒。
* MP3 不得包含任何特定于客戶的資訊或其他敏感資訊。

**語法**

```xml
<audio src="string"/></audio>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `src` | 指定音訊檔的位置/URL。 | 如果在 SSML 文檔中使用音訊元素，則需要。 |

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

## <a name="add-background-audio"></a>添加背景音訊

該`mstts:backgroundaudio`元素允許您向 SSML 文檔添加背景音訊（或將音訊檔與文本到語音混合）。 使用`mstts:backgroundaudio`可以在後臺迴圈音訊檔，在文本到語音的開頭淡入，在文本到語音的末尾淡出。

如果提供的背景音訊短于文本到語音轉換或淡出，它將迴圈。 如果時間比文本到語音轉換長，則淡出完成後將停止。

每個 SSML 文檔只允許一個後臺音訊檔。 但是，您可以在元素中穿插`audio`標記，`voice`以便向 SSML 文檔添加其他音訊。

**語法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `src` | 指定背景音訊檔的位置/URL。 | 如果在 SSML 文檔中使用背景音訊，則需要。 |
| `volume` | 指定背景音訊檔的音量。 **接受的值** `0` ：`100`到包容性。 預設值是 `1`。 | 選用 |
| `fadein` | 指定背景音訊"淡入"的持續時間為毫秒。 預設值為`0`，這相當於不淡入。 **接受的值** `0` ：`10000`到包容性。  | 選用 |
| `fadeout` | 指定背景音訊淡出（以毫秒為單位）的持續時間。 預設值為`0`，這相當於不淡出。**接受的值** `0` ：`10000`到包容性。  | 選用 |

**範例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>後續步驟

* [語言支援：語音、地區設定、語言](language-support.md)
