---
title: 翻譯工具轉譯方法
titleSuffix: Azure Cognitive Services
description: 瞭解 Azure 認知服務翻譯工具翻譯方法翻譯文字的參數、標頭和主體訊息。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 15f20bab91a4feab056a9f8cd6b012792f036be2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016542"
---
# <a name="translator-30-translate"></a>Translator 3.0：轉譯

翻譯文字。

## <a name="request-url"></a>要求 URL

將 `POST` 要求傳送至：

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>要求參數

在查詢字串上傳遞的要求參數為：

### <a name="required-parameters"></a>必要參數

<table width="100%">
  <th width="20%">查詢參數</th>
  <th>描述</th>
  <tr>
    <td>api-version</td>
    <td><em>必要參數</em>。<br/>用戶端要求的 API 版本。 值必須為 <code>3.0</code>。</td>
  </tr>
  <tr>
    <td>to</td>
    <td><em>必要參數</em>。<br/>指定輸出文字的語言。 目標語言必須是 <code>translation</code> 範圍內包含的<a href="./v3-0-languages.md">支援語言</a>之一。 例如，使用 <code>to=de</code> 翻譯為德文。<br/>在查詢字串中重複參數，可能會同時翻譯為多種語言。 例如，使用 <code>to=de&to=it</code> 翻譯為德文和義大利文。</td>
  </tr>
</table>

### <a name="optional-parameters"></a>選擇性參數

<table width="100%">
  <th width="20%">查詢參數</th>
  <th>描述</th>
  <tr>
    <td>從</td>
    <td><em>選擇性參數</em>。<br/>指定輸入文字的語言。 使用 <code>translation</code> 範圍查閱<a href="./v3-0-languages.md">支援語言</a>，以尋找可用於翻譯的來源語言。 若未指定 <code>from</code> 參數，則會套用自動語言偵測來判斷來源語言。 <br/><br/><code>from</code>使用<a href="/azure/cognitive-services/translator/dynamic-dictionary">動態字典</a>功能時，您必須使用參數，而不是自動偵測。</td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>選擇性參數</em>。<br/>定義要翻譯的文字是純文字還是 HTML 文字。 任何 HTML 都需要是格式正確的完整項目。 可能的值為： <code>plain</code> (預設) 或 <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>選擇性參數</em>。<br/>字串，指定翻譯的分類 (定義域)。 此參數用來從使用 <a href="../customization.md">Custom Translator</a> 所建置的自訂系統取得翻譯。 將自訂翻譯 <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">專案詳細資料</a> 中的分類識別碼新增至這個參數，以使用您已部署的自訂系統。 預設值為：<code>general</code>。</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>選擇性參數</em>。<br/>指定如何處理翻譯中的粗話。 可能的值為：<code>NoAction</code> (預設)、<code>Marked</code> 或 <code>Deleted</code>。 若要了解處理粗話的方式，請參閱<a href="#handle-profanity">粗話處理</a>。</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>選擇性參數</em>。<br/>指定如何在翻譯中標示粗話。 可能的值為：<code>Asterisk</code> (預設) 或 <code>Tag</code>。 若要了解處理粗話的方式，請參閱<a href="#handle-profanity">粗話處理</a>。</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>選擇性參數</em>。<br/>指定是否包括從來源文字到翻譯文字的對齊方式投影。 可能的值為：<code>true</code> 或 <code>false</code> (預設)。 </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>選擇性參數</em>。<br/>指定是否包括輸入文字和翻譯文字的句子界限。 可能的值為：<code>true</code> 或 <code>false</code> (預設)。</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>選擇性參數</em>。<br/>指定無法識別輸入文字語言時的後援語言。 省略 <code>from</code> 參數時，會套用語言自動偵測。 若偵測失敗，則會採用 <code>suggestedFrom</code> 語言。</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>選擇性參數</em>。<br/>指定輸入文字的指令碼。</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>選擇性參數</em>。<br/>指定翻譯文字的指令碼。</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>選擇性參數</em>。<br/>指出服務可在自訂系統不存在時，回復為一般系統。 可能的值為：<code>true</code> (預設) 或 <code>false</code>。<br/><br/><code>allowFallback=false</code> 會指出翻譯只應該使用針對要求所指定的 <code>category</code> 而進行訓練的系統。 如果從語言 X 翻譯為語言 Y 需要透過樞紐語言 E 來鏈結，則鏈結中的所有系統 (X->E 和 E->Y) 都必須是自訂的，且具有相同類別。 如果沒有找到具有特定類別的系統，則要求會傳回 400 狀態碼。 <code>allowFallback=true</code> 會指出服務可在自訂系統不存在時，回復為一般系統。
</td>
  </tr>
</table> 

要求標頭包括：

<table width="100%">
  <th width="20%">標題</th>
  <th>描述</th>
  <tr>
    <td>驗證標頭</td>
    <td>必要的要求標頭。<br/>請參閱<a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">可用的驗證選項</a>。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>必要的要求標頭。<br/>指定承載的內容類型。<br/> 接受的值為 <code>application/json; charset=UTF-8</code>。</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>必要的要求標頭。<br/>要求本文的長度。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>選擇項</em>。<br/>用於識別唯一要求的 GUID，由用戶端產生。 若您使用名為 <code>ClientTraceId</code> 的查詢參數在查詢字串中包含追蹤識別碼，您就可以省略此標頭。</td>
  </tr>
</table> 

## <a name="request-body"></a>Request body

要求的本文是 JSON 陣列。 每個陣列項目都是字串屬性名為 `Text` 的 JSON 物件，其代表要翻譯的字串。

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

適用下列限制：

* 陣列最多可以有 100 個項目。
* 要求中包含的完整文字不能超過10000個字元，包括空格。

## <a name="response-body"></a>回應本文

成功的回應是輸入陣列的每個字串各有一個結果的 JSON 陣列。 結果物件包含下列屬性：

  * `detectedLanguage`：物件，透過下列屬性描述偵測到的語言：

      * `language`：字串，代表偵測到語言的代碼。

      * `score`：浮點值，表示結果的信賴度。 分數介於 0 與 1 之間，分數低表示信賴度偏低。

    只有在要求自動偵測語言時，`detectedLanguage` 屬性才會存在於結果物件中。

  * `translations`：翻譯結果的陣列。 陣列大小符合透過 `to` 查詢參數指定的目標語言數。 陣列中的每個項目都包括：

    * `to`：字串，代表目標語言的語言代碼。

    * `text`：字串，提供翻譯文字。

    * `transliteration`：物件，提供 `toScript` 參數所指定指令碼中的翻譯文字。

      * `script`：字串，指定目標指令碼。   

      * `text`：字串，提供目標指令碼中的翻譯文字。

    若未進行音譯，則不會包括 `transliteration` 物件。

    * `alignment`：單一字串屬性名為 `proj` 的物件，以將輸入文字對應至翻譯文字。 只有在要求參數 `includeAlignment` 是 `true` 時，才會提供對齊方式資訊。 對齊方式會以下列格式的字串值傳回：`[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`。  冒號區隔開始與結束索引、虛線區隔語言，而空格區隔字組。 一個單字可能會與另一種語言中的零個、一個或多個單字對齊，而對齊的單字可能不連續。 沒有對齊方式資訊可用時，對齊方式項目會空白。 如需範例和限制，請參閱[取得對齊資訊](#obtain-alignment-information)。

    * `sentLen`：物件，傳回輸入和輸出文字中的句子界限。

      * `srcSentLen`：整數陣列，代表輸入文字中句子的長度。 陣列長度就是句子數目，而值是每個句子的長度。

      * `transSentLen`：整數陣列，代表翻譯文字中句子的長度。 陣列長度就是句子數目，而值是每個句子的長度。

    只有在要求參數 `includeSentenceLength` 是 `true` 時，才會包括句子界限。

  * `sourceText`：具有名為 `text` 之單一字串屬性的物件，以提供來源語言之預設指令碼中的輸入文字。 只有在以不是語言一般指令碼的指令碼表示輸入時，才會有 `sourceText` 屬性。 例如，若輸入是以拉丁文指令碼寫入的阿拉伯文，則 `sourceText.text` 會是轉換成阿拉伯文指令碼的相同阿拉伯文文字。

[範例](#examples)一節提供 JSON 回應的範例。

## <a name="response-headers"></a>回應標頭

<table width="100%">
  <th width="20%">標題</th>
  <th>描述</th>
    <tr>
    <td>X-RequestId</td>
    <td>服務產生的值，用於識別要求。 作為疑難排解之用。</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>指出要求進行翻譯的每個 'to' 語言翻譯所使用的系統類型。 此值是以逗號分隔的字串清單。 每個字串代表一種類型：<br/><ul><li>自訂 - 要求中包含自訂系統，且在翻譯期間使用了至少一個自訂系統。</li><li>小組 - 所有其他要求</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>回應狀態碼

以下是要求傳回的可能 HTTP 狀態碼。 

<table width="100%">
  <th width="20%">狀態碼</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>成功。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>其中一個查詢參數遺失或無效。 請先修正要求參數再重試。</td>
  </tr>
  <tr>
    <td>401</td>
    <td>無法驗證要求。 請確認認證已指定且有效。</td>
  </tr>
  <tr>
    <td>403</td>
    <td>要求未經授權。 請查看詳細錯誤訊息。 這通常表示試用訂用帳戶提供的所有免費翻譯都已用完。</td>
  </tr>
  <tr>
    <td>408</td>
    <td>無法完成要求，因為資源有所遺漏。 請查看詳細錯誤訊息。 當使用自訂 <code>category</code> 時，這通常表示自訂翻譯系統尚無法用來處理要求。 要求應該在一段等待期間 (例如 1 分鐘) 之後重試。</td>
  </tr>
  <tr>
    <td>429</td>
    <td>伺服器已拒絕要求，因為用戶端已超過要求限制。</td>
  </tr>
  <tr>
    <td>500</td>
    <td>發生意外錯誤。 若錯誤仍然存在，請回報：失敗的日期和時間、來自回應標頭 <code>X-RequestId</code> 的要求識別碼，以及來自要求標頭 <code>X-ClientTraceId</code> 的用戶端識別碼。</td>
  </tr>
  <tr>
    <td>503</td>
    <td>暫時無法使用伺服器。 重試要求。 若錯誤仍然存在，請回報：失敗的日期和時間、來自回應標頭 <code>X-RequestId</code> 的要求識別碼，以及來自要求標頭 <code>X-ClientTraceId</code> 的用戶端識別碼。</td>
  </tr>
</table> 

如果發生錯誤，要求也會傳回 JSON 錯誤回應。 錯誤碼是 6 位數的數字，其中結合了 3 位數的 HTTP 狀態碼，後面接著將錯誤進一步分類的 3 位數數字。 您可以在 [V3 Translator](./v3-0-reference.md#errors)的 [參考] 頁面上找到常見的錯誤碼。 

## <a name="examples"></a>範例

### <a name="translate-a-single-input"></a>翻譯單一輸入

此範例示範如何將單一句子從英文翻譯成簡體中文。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

回應本文如下：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` 陣列包括一個項目，以提供輸入中單一文字片段的翻譯。

### <a name="translate-a-single-input-with-language-auto-detection"></a>使用語言自動偵測翻譯單一輸入

此範例示範如何將單一句子從英文翻譯成簡體中文。 要求未指定輸入語言。 改為使用來源語言的自動偵測。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

回應本文如下：

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
回應與前一個範例的回應類似。 因為要求語言自動偵測，所以回應也會包括針對輸入文字偵測到之語言的相關資訊。 語言自動偵測的運作方式較長的輸入文字。

### <a name="translate-with-transliteration"></a>含轉換的翻譯

讓我們新增轉換以擴充前一個範例。 下列要求會要求以拉丁文指令碼撰寫的中文翻譯。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

回應本文如下：

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

翻譯結果現在包括 `transliteration` 屬性，以使用拉丁文字元來提供翻譯文字。

### <a name="translate-multiple-pieces-of-text"></a>翻譯文字的多個片段

一次翻譯多個字串就像是在要求本文中指定字串陣列一樣。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

回應會以與要求中完全相同的順序，包含所有文字片段的轉譯。
回應本文如下：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>翻譯為多種語言

本範例示範如何使用一個要求將相同輸入翻譯為數種語言。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

回應本文如下：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>處理粗話

Translator 服務通常會在翻譯中保留存在於來源的粗話。 粗話程度以及產生不雅字眼的內容在文化特性之間會不同，因此目標語言中的粗話程度可能會加大或減少。

若您想要在翻譯中避免粗話，則不論來源文字中是否有粗話，都可以使用粗話篩選選項。 此選項可讓您選擇是否要查看刪除的粗話、是否要使用適當的標籤標示粗話 (可讓您選擇新增您自己的後續處理)，或您不想要採取任何動作。 接受的 `ProfanityAction` 值為 `Deleted`、`Marked` 和 `NoAction` (預設)。

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>動作</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>這是預設行為。 粗話會從來源傳遞到目標。<br/><br/>
    <strong>範例來源 (日文)</strong>：彼はジャッカスです。<br/>
    <strong>範例翻譯 (英文)</strong>：He is a jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>將會從輸出中移除不雅字眼，但不予取代。<br/><br/>
    <strong>範例來源 (日文)</strong>：彼はジャッカスです。<br/>
    <strong>範例翻譯 (英文)</strong>：He is a.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>不雅字眼會取代為輸出中的標記。 標記取決於 <code>ProfanityMarker</code> 參數。<br/><br/>
對於 <code>ProfanityMarker=Asterisk</code>，不雅字眼會取代為 <code>***</code>：<br/>
    <strong>範例來源 (日文)</strong>：彼はジャッカスです。<br/>
    <strong>範例轉譯 (英文) </strong>：他是 \* \* \* 。<br/><br/>
對於 <code>ProfanityMarker=Tag</code>，不雅字眼會括上 XML 標籤 &lt;profanity&gt; 和 &lt;/profanity&gt;：<br/>
    <strong>範例來源 (日文)</strong>：彼はジャッカスです。<br/>
    <strong>範例翻譯 (英文)</strong>：He is a &lt;profanity&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

例如：

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
這會傳回：

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

相較於：

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

這個最後一個要求會傳回：

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>翻譯包含標記的內容，並決定翻譯內容

通常會翻譯包含標記的內容，例如 HTML 頁面的內容或 XML 文件的內容。 翻譯具有標籤的內容時，請包括 `textType=html` 查詢參數。 此外，排除特定內容不翻譯有時可能會十分有用。 您可以使用 `class=notranslate` 屬性，指定應該保留其原始語言的內容。 在下列範例中，不會翻譯第一個 `div` 項目內的內容，但會翻譯第二個 `div` 項目中的內容。

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

以下是要說明的範例要求。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

回應如下：

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>取得對齊方式資訊

系統會以下列格式的字串值，傳回來源每個單字的對齊方式。 每個單字的資訊會以空格分隔，包括中文這類非以空格分隔的語言 (指令碼)：

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

範例對齊方式字串："0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21"。

換句話說，冒號會分隔開始與結束索引、虛線會分隔語言，而空格則分隔單字。 一個單字可能會與另一種語言中的零個、一個或多個單字對齊，而對齊的單字可能不連續。 沒有對齊方式資訊可用時，Alignment 元素便會空白。 在該情況下，方法不會傳回任何錯誤。

若要收到對齊方式資訊，請在查詢字串上指定 `includeAlignment=true`。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

回應如下：

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

對齊方式資訊開始於 `0:2-0:1`，表示來源文字中的前三個字元 (`The`) 對應至翻譯文字中的前兩個字元 (`La`)。

#### <a name="limitations"></a>限制
取得對齊資訊是一項實驗性功能，我們已針對可能的片語對應，使用原型設計研究和體驗。 未來可能會選擇停止支援。 以下是不支援對齊的一些值得注意的限制：

* HTML 格式的文字未提供對齊，亦即 textType = html
* 只會傳回一部分語言組的對齊方式：
  - 除了繁體中文、廣東話 (傳統) 或塞爾維亞文 (斯拉夫文) 以外的其他任何語言的英文。
  - 從日文到韓文，或從韓文到日文。
  - 從日文到簡體中文，以及簡體中文至日文。 
  - 從簡體中文到中文繁體，繁體中文到簡體中文。 
* 若句子是已定義的翻譯，則您收不到對齊方式。 已定義的翻譯範例是 "This is a test"、"I love you" 和其他高頻率出現句子。
* 當您套用任何方法來防止轉譯[（如下所述）](../prevent-translation.md)時，將無法使用對齊

### <a name="obtain-sentence-boundaries"></a>取得句子界限

若要收到來源文字和翻譯文字中句子長度的相關資訊，請在查詢字串上指定 `includeSentenceLength=true`。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

回應如下：

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>使用動態字典翻譯

如果已經知道想要套用至單字或片語的翻譯，則可以在要求內以標記來提供。 動態字典只有適當的名詞才安全，例如個人名稱和產品名稱。

要提供的標記會使用下列語法。

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

例如，請考慮使用英文句子 "The word wordomatic is a dictionary entry." 若要在翻譯中保留 _wordomatic_ 這個字，則會傳送要求：

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

結果如下：

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

此功能的運作方式與 `textType=text` 或 `textType=html` 相同。 應該謹慎使用此功能。 自訂翻譯的適當且最適合方式是使用 Custom Translator。 Custom Translator 會完全利用內容和統計機率。 如果您已經或能夠建立定型資料，以便在上下文中顯示您的單字或片語，則會得到更好的結果。 [深入瞭解自訂翻譯](../customization.md)。