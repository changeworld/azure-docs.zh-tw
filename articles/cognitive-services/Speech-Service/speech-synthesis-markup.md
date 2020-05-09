---
title: 語音合成標記語言（SSML）-語音服務
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
ms.openlocfilehash: b1c19ed556a55dec8c84686e80ec988bc593a7a2
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996037"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>使用語音合成標記語言（SSML）改善合成

語音合成標記語言（SSML）是以 XML 為基礎的標記語言，可讓開發人員指定如何使用文字轉換語音服務，將輸入文字轉換成合成語音。 相較于純文字，SSML 讓開發人員能夠微調文字到語音轉換輸出的音調、發音、說話速度、音量和更多。 一般標點符號（例如在句號之後暫停），或使用正確的聲調（當句子以問號結尾時）會自動處理。

SSML 的語音服務執行是以全球資訊網協會的[語音合成標記語言1.0 版](https://www.w3.org/TR/speech-synthesis)為基礎。

> [!IMPORTANT]
> 中文、日文和韓文字元的計費方式為兩個字元。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>標準、類神經和自訂語音

從標準和類神經語音中選擇，或為您的產品或品牌建立專屬的自訂語音。 75 + standard 語音提供45以上的語言和地區設定，而5類神經語音則提供四種語言和地區設定。 如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md)。

若要深入瞭解標準、類神經和自訂語音，請參閱[文字轉換語音的總覽](text-to-speech.md)。

## <a name="special-characters"></a>特殊字元

使用 SSML 時，請記住，特殊字元（例如引號、撇號和方括弧）必須經過轉義。 如需詳細資訊，請參閱[可延伸標記語言 (XML) （XML）1.0：附錄 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支援的 SSML 元素

每個 SSML 檔都是使用 SSML 元素（或標記）所建立。 這些元素可用來調整音調、韻律、音量等等。 下列各節將詳細說明每個專案的使用方式，以及當元素為必要或選擇性時。  

> [!IMPORTANT]
> 別忘了在屬性值前後使用雙引號。 格式正確且有效的 XML 的標準需要以雙引號括住屬性值。 例如， `<prosody volume="90">`是格式正確且有效的元素，但`<prosody volume=90>`不是。 SSML 可能無法辨識不是以引號括住的屬性值。

## <a name="create-an-ssml-document"></a>建立 SSML 檔

`speak`是根項目，而且是所有 SSML 檔的**必要**專案。 `speak`元素包含重要資訊，例如版本、語言和標記詞彙定義。

**語法**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `version` | 表示用來解讀檔標記的 SSML 規格版本。 目前的版本為1.0。 | 必要 |
| `xml:lang` | 指定根文檔的語言。 此值可包含小寫、兩個字母的語言代碼（例如`en`），或語言代碼和大寫國家/地區（例如`en-US`）。 | 必要 |
| `xmlns` | 指定檔的 URI，以定義 SSML 檔的標記詞彙（元素類型和屬性名稱）。 目前的 URI 是http://www.w3.org/2001/10/synthesis。 | 必要 |

## <a name="choose-a-voice-for-text-to-speech"></a>選擇文字轉換語音的語音

需要`voice`元素。 它是用來指定文字轉換語音所使用的語音。

**語法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `name` | 識別文字到語音轉換輸出所使用的語音。 如需支援的語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。 | 必要 |

**範例**

> [!NOTE]
> 這個範例會使用`en-US-AriaRUS`語音。 如需支援的語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多重音源

在`speak`元素內，您可以為文字到語音轉換輸出指定多個語音。 這些語音可以採用不同的語言。 針對每個聲音，文字必須包裝在`voice`元素中。 

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `name` | 識別文字到語音轉換輸出所使用的語音。 如需支援的語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。 | 必要 |

> [!IMPORTANT]
> 多個語音與「字邊界」功能不相容。 必須停用「字邊界」功能，才能使用多個語音。

### <a name="disable-word-boundary"></a>停用字邊界

視語音 SDK 語言而定，您會在`"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` `false` `SpeechConfig`物件的實例上將屬性設定為。

# <a name="c"></a>[C#](#tab/csharp)

如需詳細資訊， <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span></a>請參閱。

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C + +](#tab/cpp)

如需詳細資訊， <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span></a>請參閱。

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

如需詳細資訊， <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span></a>請參閱。

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

如需詳細資訊， <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span></a>請參閱。

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

如需詳細資訊， <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span></a>請參閱。

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

如需詳細資訊， <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span></a>請參閱。

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

如需詳細資訊， <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span></a>請參閱。

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
> 說話樣式的調整僅適用于類神經語音。

根據預設，文字轉換語音服務會針對標準和類神經語音使用中性說話樣式來合成文字。 使用神經語音時，您可以調整說話樣式，以使用`<mstts:express-as>`元素表達 cheerfulness、理解或情感。 這是語音服務特有的選擇性元素。

目前，這些類神經語音支援說話的樣式調整：
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`

變更會在句子層級套用，而樣式會因語音而有所不同。 如果樣式不受支援，服務將會以預設的中性說話樣式傳回語音。

**語法**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `style` | 指定說話的樣式。 目前，說話樣式是語音特有的。 | 如果要調整類神經語音的說話樣式，則為必要。 如果使用`mstts:express-as`，則必須提供 style。 如果提供了不正確值，則會忽略此元素。 |

使用此表格來判斷每個類神經語音支援哪些說話樣式。

| 語音                   | 樣式                     | 描述                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast"`        | 表達 narrating 新聞的正式和專業音調 |
|                         | `style="customerservice"` | 為客戶支援表示易記且有用的語氣  |
|                         | `style="chat"`            | 表示隨意且寬鬆的色調                         |
|                         | `style="cheerful"`        | 表達正面且滿意的語氣                         |
|                         | `style="empathetic"`      | 表達管也和認知的意義               |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | 表達 narrating 新聞的正式和專業音調 |
|                         | `style="customerservice"` | 為客戶支援表示易記且有用的語氣  |
|                         | `style="assistant"`       | 表達數位助理的暖和寬鬆音調    |
|                         | `style="lyrical"`         | 以 melodic 和感情的方式表達表情         |

**範例**

這個 SSML 程式碼片段說明如何`<mstts:express-as>`使用專案將說話風格變更為`cheerful`。

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

## <a name="add-or-remove-a-breakpause"></a>新增或移除中斷/暫停

使用元素`break`在單字之間插入暫停（或中斷），或防止文字轉換語音服務自動暫停。

> [!NOTE]
> 使用此元素可覆寫單字或片語的文字轉換語音（TTS）的預設行為（如果該單字或片語的合成語音非自然）。 設定`strength`為`none`以防止韻律中斷，這會由文字轉換語音服務自動插入。

**語法**

```xml
<break strength="string" />
<break time="string" />
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `strength` | 使用下列其中一個值，指定暫停的相對持續時間：<ul><li>無</li><li>x-弱式</li><li>不足</li><li>中（預設值）</li><li>強式</li><li>x-強式</li></ul> | 選用 |
| `time` | 指定暫停的絕對持續時間（以秒或毫秒為單位）。 有效值的範例包括`2s`和`500` | 選用 |

| 程度                      | 描述 |
|-------------------------------|-------------|
| 無; 如果未提供任何值，則為 | 0毫秒        |
| x-弱式                        | 250毫秒      |
| 不足                          | 500 毫秒      |
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

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p`和`s`元素分別用來表示段落和句子。 如果沒有這些元素，文字轉換語音服務會自動決定 SSML 檔的結構。

`p`元素可能包含文字和下列元素： `audio`、 `break`、 `phoneme`、 `prosody`、 `say-as`、 `sub` `mstts:express-as`、和。 `s`

`s`元素可能包含文字和`audio`下列元素：、 `break`、 `phoneme`、 `prosody`、 `say-as`、 `mstts:express-as`和。 `sub`

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

`ph`元素用於 SSML 檔中的語音發音。 `ph`元素只能包含文字，沒有其他元素。 一律提供人類可讀的語音做為回復。

語音字母由電話組成，其由字母、數位或字元組成，有時會組合。 每個電話都會說明語音的獨特聲音。 這與拉丁字母相較之下，其中任何字母可能代表多個說話的聲音。 請考慮字母 "c" 在「糖果」和「停止」單字中的不同發音，或在「內容」和「那些」單字中，字母組合「th」的不同發音。

**語法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `alphabet` | 指定合成`ph`屬性中字串的發音時，所要使用的語音字母。 指定字母的字串必須以小寫字母指定。 以下是您可以指定的可能字母。<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">國際語音字母<span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash; [語音服務拼音字母](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash;通用電話集合</li></ul><br>字母僅適用于元素中`phoneme`的。 | 選用 |
| `ph` | 包含電話的字串，指定`phoneme`元素中單字的發音。 如果指定的字串包含無法辨識的手機，文字轉換語音（TTS）服務會拒絕整個 SSML 檔，而且不會產生任何在檔中指定的語音輸出。 | 如果使用音素，則為必要。 |

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

有時文字轉換語音服務無法精確地發音單字。 例如，公司的名稱或醫療詞彙。 開發人員可以使用`phoneme`和`sub`標記定義在 SSML 中讀取單一實體的方式。 不過，如果您需要定義多個實體的讀取方式，您可以使用`lexicon`標記來建立自訂字典。

> [!NOTE]
> 自訂字典目前支援 UTF-8 編碼。 

**語法**

```XML
<lexicon uri="string"/>
```

**屬性**

| 屬性 | 描述                               | 必要/選用 |
|-----------|-------------------------------------------|---------------------|
| `uri`     | 外部另外檔的位址。 | 必要。           |

**使用量**

若要定義多個實體的讀取方式，您可以建立自訂的詞典，它會儲存為 .xml 或. 另外檔案。 以下是範例 .xml 檔案。

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

`lexicon`元素至少包含一個`lexeme`元素。 每`lexeme`個元素都包含至少`grapheme`一個專案，以及一個`grapheme`或`alias`多個`phoneme` 、和元素。 `grapheme`元素包含描述<a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography <span class="docon docon-navigate-external x-hidden-focus"> </span></a>的文字。 `alias`元素可用來指示縮略字或縮寫詞彙的發音。 `phoneme`元素會提供描述如何發音的`lexeme`文字。

請務必注意，您無法使用自訂字典直接設定單字的發音。 如果您需要設定的發音，請先提供，然後將`alias` `phoneme`與產生關聯`alias`。 例如：

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

> [!IMPORTANT]
> 使用`phoneme` .ipa 時，元素不能包含空白字元。

如需自訂字典檔案的詳細資訊，請參閱[發音字典規格（另外）版本 1.0](https://www.w3.org/TR/pronunciation-lexicon/)。

接下來，發佈您的自訂字典檔案。 雖然我們不會限制儲存此檔案的位置，但我們建議使用[Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

發行自訂字典之後，您就可以從 SSML 參考它。

> [!NOTE]
> `lexicon`元素必須在`voice`元素內。

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

使用此自訂字典時，"您" 將會以「方式」閱讀。 "Benigni" 將會使用提供的 .IPA "bɛ今天 ni ː nji" 讀取。  

**限制**
- 檔案大小：自訂字典檔案大小上限為 100 KB，如果超過此大小，合成要求將會失敗。
- 辭典快取重新整理：自訂字典會在第一次載入 TTS 服務時，以 URI 作為金鑰來進行快取。 具有相同 URI 的字典不會在15分鐘內重載，因此自訂的詞典變更必須等候15分鐘才會生效。

**語音服務拼音設定**

在上述範例中，我們使用國際語音字母（也稱為 .IPA 電話集）。 我們建議開發人員使用 .IPA，因為它是國際標準。 對於某些 .IPA 字元，它們會在以 Unicode 表示時具有 ' precomposed ' 和「分解」版本。 自訂字典僅支援分解的 unicodes。

考慮到 .ipa 並不容易記住，語音`en-US`服務會為七種語言（、 `fr-FR`、 `de-DE`、 `es-ES` `ja-JP` `zh-CN`、、和`zh-TW`）定義一組語音。

您可以使用`sapi`作為具有自訂字典之`alphabet`屬性的 back，如下所示：

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

如需詳細語音服務拼音字母的詳細資訊，請參閱[語音服務拼音設定](speech-ssml-phonetic-sets.md)。

## <a name="adjust-prosody"></a>調整韻律

`prosody`元素是用來指定文字到語音轉換輸出的音調、輪廓、範圍、速率、持續時間和音量的變更。 `prosody`元素可能包含文字和下列元素： `audio`、 `break`、 `p`、 `phoneme`、 `prosody`、 `say-as` `sub`、和。 `s`

由於韻律屬性的值可能會隨著寬範圍而有所不同，因此語音辨識器會將指派的值解釋為所選語音的實際韻律值應為的建議。 文字轉換語音服務會限制或替代不支援的值。 不支援值的範例是 1 MHz 或磁片區120。

**語法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `pitch` | 表示文字的基準間距。 您可以用下列方式表達音調：<ul><li>絕對值，以數位表示，後面接著 "Hz" （赫茲）。 例如，600 Hz。</li><li>以數位表示的相對值，前面加上 "+" 或 "-"，後面接著 "Hz" 或 "st"，以指定要變更音調的數量。 例如： + 80 Hz 或-2st。 "St" 表示變更單位是 semitone，這是標準 diatonic 尺規上的一半色調（半步驟）。</li><li>常數值：<ul><li>x-低</li><li>low</li><li>中</li><li>high</li><li>x-高</li><li>default</li></ul></li></ul>. | 選用 |
| `contour` |等高線現在支援類神經和標準語音。 等高線代表音調中的變更。 這些變更會在語音輸出中的指定時間位置以目標陣列表示。 每個目標都是由一組參數配對所定義。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每一組參數中的第一個值會指定音調變更的位置，以文字持續時間的百分比表示。 第二個值指定要增加或減少音調的數量，使用相對值或用於音調的列舉值（請參閱`pitch`）。 | 選用 |
| `range` | 值，表示文字的音調範圍。 您可以使用`range`相同的絕對值、相對值或用來描述`pitch`的列舉值來表示。 | 選用 |
| `rate` | 表示文字的說話速率。 您可以表達`rate`如下：<ul><li>相對值，以做為預設值之乘數的數位來表示。 例如，值*1*會導致速率不會變更。 *0.5*的值會產生速率的減半。 值為*3*會產生速率的增加三倍。</li><li>常數值：<ul><li>x-慢</li><li>slow</li><li>中</li><li>快速</li><li>x-快速</li><li>default</li></ul></li></ul> | 選用 |
| `duration` | 語音合成（TTS）服務讀取文字（以秒或毫秒為單位）時所經過的時間長度。 例如，2*秒*或*1800ms*。 | 選用 |
| `volume` | 表示說話語音的音量層級。 您可以將磁片區表示為：<ul><li>絕對值，以0.0 到100.0 範圍內的數位表示，從*quietest*到*loudest*。 例如，75。 預設值為100.0。</li><li>以數位表示的相對值，其前面加上 "+" 或 "-"，以指定要變更磁片區的數量。 例如，+ 10 或-5.5。</li><li>常數值：<ul><li>silent</li><li>x-軟</li><li>軟</li><li>中</li><li>很</li><li>x-大聲</li><li>default</li></ul></li></ul> | 選用 |

### <a name="change-speaking-rate"></a>改變說話速度

說話率可以套用至單字或句子層級的類神經語音和標準語音。 

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

磁片區變更可以套用至單字或句子層級的標準語音。 而磁片區變更只能套用至句子層級的類神經語音。

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

音調變更可以套用至單字或句子層級的標準語音。 而音調變更只能套用至句子層級的類神經語音。

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
## <a name="say-as-element"></a>假設為元素

`say-as`是選擇性元素，表示專案文字的內容類型（例如數位或日期）。 這會提供語音合成引擎關於如何朗讀文字的指引。

**語法**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `interpret-as` | 表示元素文字的內容類型。 如需類型清單，請參閱下表。 | 必要 |
| `format` | 針對可能有不明確格式的內容類型，提供元素文字精確格式的其他資訊。 SSML 會定義使用它們之內容類型的格式（請參閱下表）。 | 選用 |
| `detail` | 表示要讀出的詳細資料層級。 例如，此屬性可能會要求語音合成引擎發音標點符號。 沒有針對定義的`detail`標準值。 | 選用 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

以下是`interpret-as`和`format`屬性支援的內容類型。 只有在`format`設為日期`interpret-as`和時間時，才包含屬性。

| 解讀為 | format | 解譯 |
|--------------|--------|----------------|
| `address` | | 文字會以位址的形式讀出。 語音合成引擎 pronounces：<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />「我在150th 法院的美國華盛頓州 redmond」。 |
| `cardinal`, `number` | | 文字是以基本數位來讀出。 語音合成引擎 pronounces：<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />「有三種替代方案」。 |
| `characters`, `spell-out` | | 文字是以個別字母讀出（拼法）。 語音合成引擎 pronounces：<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />As "T E S T"。 |
| `date` | dmy、mdy、ymd、ydm、ym、my、md、dm、d、m、y | 文字會以日期說出。 `format`屬性會指定日期的格式（*d = day、m = month 和 y = year*）。 語音合成引擎 pronounces：<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />As 「今天是2016年10月的第十九個」。 |
| `digits`, `number_digit` | | 文字是以一系列的個別數位來讀出。 語音合成引擎 pronounces：<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />做為 "1 2 3 4 5 6 7 8 9"。 |
| `fraction` | | 文字會以小數的形式讀出。 語音合成引擎 pronounces：<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />做為「一種八分之的一英寸」。 |
| `ordinal` | | 文字會以序號的形式讀出。 語音合成引擎 pronounces：<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />做為「選取第三個選項」。 |
| `telephone` | | 文字會以電話號碼的形式讀出。 `format`屬性可以包含代表國家/地區代碼的數位。 例如，美國的 "1" 或義大利的 "39"。 語音合成引擎可能會使用這項資訊來引導其電話號碼的發音。 電話號碼也可能包含國家/地區代碼，若是如此，則會優先于中的國家（地區`format`）代碼。 語音合成引擎 pronounces：<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />As 「我的數位是區功能變數代碼 8 8 8 5 5 5 1 2 1 2」。 |
| `time` | hms12, hms24 | 文字會以一段時間讀出。 `format`屬性會指定是否使用12小時制（hms12）或24小時制（hms24）來指定時間。 使用冒號來分隔代表小時、分鐘和秒數的數位。 以下是有效的時間範例：12:35、1:14:32、08:15 和02:50:45。 語音合成引擎 pronounces：<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />「訓練離開在四個 M」。 |

**使用量**

`say-as`元素只能包含文字。

**範例**

語音合成引擎會將下列範例說為「您的第一個要求是在10月第十九個 20 10 的一個聊天室，並于下午 12 35 PM 提早抵達。」
 
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

`audio`是選擇性元素，可讓您將 MP3 音訊插入 SSML 檔中。 音訊元素的主體可能包含純文字或 SSML 標記，如果音訊檔無法使用或播放，就會說出來。 此外， `audio`元素可以包含文字和下列元素： `audio`、 `break`、 `p`、 `s`、 `phoneme`、 `prosody` `say-as`、和。 `sub`

SSML 檔中包含的任何音訊都必須符合下列需求：

* MP3 必須裝載在可存取網際網路的 HTTPS 端點上。 需要 HTTPS，而且裝載 MP3 檔案的網域必須提供有效、受信任的 TLS/SSL 憑證。
* MP3 必須是有效的 MP3 檔案（MPEG v2）。
* 位元速率必須是 48 kbps。
* 取樣速率必須是 16000 Hz。
* 單一回應中所有文字和音訊檔案的總時間總和不能超過90（90）秒。
* MP3 不得包含任何客戶特定或其他機密資訊。

**語法**

```xml
<audio src="string"/></audio>
```

**屬性**

| 屬性 | 描述                                   | 必要/選用                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | 指定音訊檔案的位置/URL。 | 如果在您的 SSML 檔中使用音訊元素，則為必要專案。 |

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

`mstts:backgroundaudio`元素可讓您將背景音訊新增至 SSML 檔（或混合具有文字轉換語音的音訊檔案）。 有`mstts:backgroundaudio`了，您可以在背景中迴圈音訊檔案、從文字到語音的開頭淡入，然後在文字轉換語音的結尾淡出。

如果提供的背景音訊短于文字轉換語音或淡出，則會迴圈。 如果超過文字轉換語音，則會在淡出完成時停止。

每一份 SSML 檔只能有一個背景音訊檔案。 不過，您可以在`audio`專案內散置`voice`標記，以在 SSML 檔中新增其他音訊。

**語法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `src` | 指定背景音訊檔案的位置/URL。 | 如果您在 SSML 檔中使用背景音訊，則為必要項。 |
| `volume` | 指定背景音訊檔案的磁片區。 **接受**的值`0` ： `100`包含（含）。 預設值為 `1`。 | 選用 |
| `fadein` | 指定背景音訊「淡入」的持續時間（以毫秒為單位）。 預設值為`0`，這相當於「不淡入」。 **接受**的值`0` ： `10000`包含（含）。  | 選用 |
| `fadeout` | 指定背景音訊的持續時間（以毫秒為單位）。 預設值為`0`，這相當於 [不淡出]。**接受**的值`0` ： `10000`包含（含）。  | 選用 |

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
