---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: 翻譯文本 API v2.0 的參考文檔。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242491"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> 這版 Translator Text API 已淘汱。 [查看翻譯器文本 API 版本 3 的文檔](v3-0-reference.md)。

翻譯器文本 API 的版本 2 可以無縫集成到您的應用、網站、工具或其他解決方案中，以提供多語言使用者體驗。 根據行業標準，您可以在任何硬體平臺和任何作業系統上使用它來執行語言翻譯和其他與語言相關的任務，如文本語言檢測和語音文本。 有關詳細資訊，請參閱[翻譯文本 API](../translator-info-overview.md)。

## <a name="getting-started"></a>開始使用
要訪問翻譯器文本 API，您需要[註冊 Microsoft Azure](../translator-text-how-to-signup.md)。

## <a name="authentication"></a>驗證 
對翻譯文本 API 的所有調用都需要一個訂閱金鑰進行身份驗證。 API 支援三種身份驗證方法：

- 訪問權杖。 使用訂閱金鑰通過向身份驗證服務發出 POST 請求來創建訪問權杖。 如需詳細資料，請參閱權杖服務文件。 使用`Authorization`標頭或`access_token`查詢參數將訪問權杖傳遞給轉換器服務。 存取權杖的有效時間為 10 分鐘。 每 10 分鐘獲取一個新訪問權杖，並在 10 分鐘內對重複請求使用相同的訪問權杖。
- 直接使用的訂閱金鑰。 將訂閱金鑰作為請求中包含的標頭中`Ocp-Apim-Subscription-Key`的值傳遞給翻譯人員文本 API。 直接使用訂閱金鑰時，不必調用權杖身份驗證服務來創建訪問權杖。
- [Azure 認知服務多服務多服務訂閱](https://azure.microsoft.com/pricing/details/cognitive-services/)。 此方法允許您使用單個金鑰組多個服務的請求進行身份驗證。
使用多服務金鑰時，需要將兩個身份驗證標頭包含在請求中。 第一個標頭傳遞金鑰。 第二個標頭指定與您的訂閱關聯的區域：
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

多服務文本 API 訂閱需要該區域。 您選擇的區域是使用多服務訂閱金鑰時可用於文本翻譯的唯一區域。 它需要與您在 Azure 門戶上註冊多服務訂閱時選擇的區域相同。

可用`australiaeast`區域是`brazilsouth``canadacentral``centralindia``centraluseuap``southcentralus``southeastasia``uksouth``westcentralus``westeurope``westus``westus2``eastus``eastus2``japaneast``northeurope`、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `eastasia`

訂閱金鑰和訪問權杖是應隱藏在視圖中的秘密。

## <a name="profanity-handling"></a>粗話處理
通常，翻譯服務將保留源中的褻瀆。 褻瀆的程度和使褻瀆詞的語境因文化而異。 因此，目的語言中的褻瀆程度可以增加或降低。

如果要防止翻譯中的褻瀆，即使它位於源文本中，也可以對支援該文本的方法使用褻瀆篩選選項。 該選項允許您選擇是希望看到褻瀆被刪除還是用適當的標記標記，還是要允許在目標中使用褻瀆。 的`ProfanityAction`已接受值為`NoAction`（預設`Marked`）、和`Deleted`。


|ProfanityAction    |動作 |示例源（日語）  |翻譯示例（英語）  |
|:--|:--|:--|:--|
|NoAction   |預設值。 與未設定此選項時相同。 粗話會從來源傳遞到目標。        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |褻瀆詞將被xml標記\<>褻瀆和\</褻瀆>所包圍。       |彼はジャッカスです。 |他是一個\<褻瀆>的混蛋\</褻瀆>。  |
|Deleted    |將會從輸出中移除不雅字眼，但不予取代。     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>從翻譯中排除內容
當您使用標記（如 HTML （`contentType=text/html`）））翻譯內容時，有時從翻譯中排除特定內容很有用。 您可以使用 `class=notranslate` 屬性，指定應該保留其原始語言的內容。 在下面的示例中，不會翻譯第一`div`個元素中的內容，但將翻譯第二`div`個元素中的內容。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>實作附註
將文字字串從某種語言翻譯成另一種語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

**傳回值：** 表示翻譯文本的字串。

如果您以前使用`AddTranslation`或`AddTranslationArray`輸入同一源句子的評級為 5 或更高的翻譯，`Translate`則僅返回系統可用的首選。 "同一源句"表示完全相同的（100% 匹配），但句子末尾的大小寫、空格、標記值和標點符號除外。 如果未存儲評級為 5 或以上，則返回的結果將由 Microsoft 翻譯人員自動翻譯。

### <a name="response-class-status-200"></a>回應類（狀態 200）

字串

回應內容類型：應用程式/xml

### <a name="parameters"></a>參數

|參數|值|描述    |參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid  |(空白)    |必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|text|(空白)   |必要。 表示要翻譯的文本的字串。 文本不能包含超過 10，000 個字元。|查詢|字串|
|從|(空白)   |選擇性。 表示要翻譯的文本的語言代碼的字串。 例如，en 代表「英文」。|查詢|字串|
|to|(空白) |必要。 表示要將文本轉換為的語言代碼的字串。|查詢|字串|
|ContentType|(空白)    |選擇性。 要翻譯文字的格式。 支援的格式為`text/plain`（預設）和`text/html`。 任何 HTML 元素都必須是格式良好的完整元素。|查詢|字串|
|category|(空白)   |選擇性。 包含翻譯的類別（域）的字串。 預設值為 `general`。|查詢|字串|
|授權|(空白)  |如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|


### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>實作附註
檢索多個源文本的翻譯。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`。

以下是請求正文的格式：

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

這些元素在`TranslateArrayRequest`中。


* `AppId`：必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`AppId`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。
* `From`：選擇性。 表示要翻譯的文本的語言代碼的字串。 如果此欄位留空，回應將包括自動語言檢測的結果。
* `Options`：選擇性。 包含`Options`以下值的物件。 它們都是可選的，預設為最常見的設置。 指定的項目必須以字母順序列出。
    - `Category`：包含翻譯的類別（域）的字串。 預設值為 `general`。
    - `ContentType`：要翻譯文字的格式。 支援的格式為`text/plain`（預設）、`text/xml`和`text/html`。 任何 HTML 元素都必須是格式良好的完整元素。
    - `ProfanityAction`： 指定如何處理褻瀆，如前面所述。 接受的值為`NoAction`（預設）、`Marked`和`Deleted`。
    - `State`：使用者狀態以説明關聯請求和回應。 相同的內容將在回應中返回。
    - `Uri`：依此 URI 篩選結果。 預設：`all`。
    - `User`：依此使用者篩選結果。 預設：`all`。
* `Texts`：必要。 包含要翻譯的文本的陣列。 所有字串必須使用同一語言。 要翻譯的所有文本總數不能超過 10，000 個字元。 陣列元素的最大數量為 2，000。
* `To`：必要。 表示要將文本轉換為的語言代碼的字串。

您可以省略可選元素。 作為 直接`TranslateArrayRequest`子級的元素必須按字母順序列出。

該方法`TranslateArray`接受`application/xml`或`text/xml` `Content-Type`。

**傳回值：**`TranslateArrayResponse` 陣列。 每個`TranslateArrayResponse`元素都有以下元素：

* `Error`：指示發生錯誤。 否則設定為 null。
* `OriginalSentenceLengths`： 一個整數陣列，用於指示源文本中每個句子的長度。 陣列長度，指出句子數目。
* `TranslatedText`：翻譯的文字。
* `TranslatedSentenceLengths`：一個整數陣列，用於指示翻譯文本中每個句子的長度。 陣列長度，指出句子數目。
* `State`：使用者狀態以説明關聯請求和回應。 返回與請求相同的內容。

以下是回應正文的格式：

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>回應類（狀態 200）
成功的回應包括前面所述格式的`TranslateArrayResponse`陣列陣列陣列。

字串

回應內容類型：應用程式/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|授權|(空白)  |如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼   |原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。 常見錯誤包括： <ul><li>陣列元素不能為空。</li><li>無效類別。</li><li>從語言無效。</li><li>語言無效。</li><li>請求包含的元素太多。</li><li>不支援 From 語言。</li><li>不支援"到"語言。</li><li>翻譯請求的資料太多。</li><li>HTML 格式不正確。</li><li>翻譯請求中傳遞的字串太多。</li></ul>|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>實作附註
檢索作為參數`languageCodes`傳入的語言的易記名稱，當地語系化為傳遞`locale`的語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

請求正文包括一個字串陣列，該陣列表示要檢索易記名稱的 ISO 639-1 語言代碼。 以下是範例：

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**傳回值：** 包含由翻譯服務支援的語言名稱的字串陣列，當地語系化為請求的語言。

### <a name="response-class-status-200"></a>回應類（狀態 200）
包含由翻譯服務支援的語言名稱的字串陣列，當地語系化為請求的語言。

字串

回應內容類型：應用程式/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|地區設定|(空白) |必要。 表示以下語言名稱之一的字串，用於當地語系化語言名稱： <ul><li>與語言關聯的 ISO 639 雙字母小寫區域性代碼和 ISO 3166 雙字母大寫子領域性代碼的組合。 <li>ISO 639 小寫區域性代碼本身。|查詢|字串|
|授權|(空白)  |如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>實作附註
獲取表示翻譯服務支援的語言的語言代碼的清單。  `Translate` 和 `TranslateArray` 可以翻譯這些語言的任兩種。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

**傳回值：** 包含翻譯服務支援的語言代碼的字串陣列。

### <a name="response-class-status-200"></a>回應類（狀態 200）
包含翻譯服務支援的語言代碼的字串陣列。

字串

回應內容類型：應用程式/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|授權|(空白)  |如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>實作附註
擷取提供語音合成功能的語言種類。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

**傳回值：** 包含翻譯服務支援語音合成的語言代碼的字串陣列。

### <a name="response-class-status-200"></a>回應類（狀態 200）
包含翻譯服務支援語音合成的語言代碼的字串陣列。

字串

回應內容類型：應用程式/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|授權|(空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|
 
### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401|憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>實作附註
返回以所需語言說出的傳入文本的 WAV 或 MP3 流。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

**傳回值：** 傳入文本的 WAV 或 MP3 流，以所需語言使用。

### <a name="response-class-status-200"></a>回應類（狀態 200）

BINARY

回應內容類型：應用程式/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|text|(空白)   |必要。 包含一個或多個要以指定語言為流說話的句子的字串。 文本不得超過 2，000 個字元。|查詢|字串|
|語言|(空白)   |必要。 表示講文本的語言的支援語言代碼的字串。 代碼必須是 方法`GetLanguagesForSpeak`返回的代碼之一。|查詢|字串|
|format|(空白)|選擇性。 指定內容類型 ID 的字串。 目前可以使用 `audio/wav` 和 `audio/mp3`。 預設值是 `audio/wav`。|查詢|字串|
|選項|(空白)    |選擇性。 指定合成語音屬性的字串：<ul><li>`MaxQuality`並`MinSize`指定音訊信號的品質。 `MaxQuality`提供最高品質。 `MinSize`提供最小的檔案大小。 預設值為`MinSize`。</li><li>`female`並`male`指定語音的所需性別。 預設值為 `female`。 使用分隔號 （<code>\|</code>） 包含多個選項。 例如，`MaxQuality|Male`。</li></li></ul>  |查詢|字串|
|授權|(空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>實作附註
標識文本部分的語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

**傳回值：** 包含文本的雙字元語言代碼的字串。

### <a name="response-class-status-200"></a>回應類（狀態 200）

字串

回應內容類型：應用程式/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)  |必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|text|(空白)|必要。 包含要標識其語言的文本的字串。 文本不得超過 10，000 個字元。|查詢|  字串|
|授權|(空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key  |(空白)    |如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>實作附註

標識字串陣列中的語言。 獨立檢測每個陣列元素的語言，並為數組的每一行返回結果。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

以下是請求正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文本不能超過 10，000 個字元。

**傳回值：** 包含輸入陣列中每行的雙字元語言代碼的字串陣列。

以下是回應正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>回應類（狀態 200）
`DetectArray`是成功的。 返回一個字串陣列，該字串陣列包含輸入陣列每行的雙字元語言代碼。

字串

回應內容類型：應用程式/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|授權|(空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。  授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>實作附註

> [!IMPORTANT]
> **棄用注釋：** 2018 年 1 月 31 日之後，此方法不會接受新的句子提交。 您會收到一條錯誤訊息。 請參閱有關協作翻譯框架 （CTF） 更改的公告。

將翻譯新增至翻譯記憶體。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>回應類（狀態 200）

字串

回應內容類型：應用程式：xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型   |
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|originalText|(空白)|必要。 包含要翻譯的文本的字串。 字串的最大長度為 1，000 個字元。|查詢|字串|
|translatedText|(空白) |必要。 包含翻譯成目的語言的文本的字串。 字串的最大長度為 2，000 個字元。|查詢|字串|
|從|(空白)   |必要。 表示文本原始語言的語言代碼的字串。 例如，英語和德語的 en。|查詢|字串|
|to|(空白)|必要。 表示要將文本轉換為的語言代碼的字串。|查詢|字串|
|rating|(空白) |選擇性。 表示字串品質評級的整數。 該值介於 -10 和 10 之間。 預設值是 1。|查詢|integer|
|ContentType|(空白)    |選擇性。 要翻譯文字的格式。 支援的格式為`text/plain`和`text/html`。 任何 HTML 元素都必須是格式良好的完整元素。    |查詢|字串|
|category|(空白)|選擇性。 包含翻譯的類別（域）的字串。 預設值為 `general`。|查詢|字串|
|user|(空白)|必要。 用於跟蹤提交建立者字串。|查詢|字串|
|uri|(空白)|選擇性。 包含翻譯內容位置的字串。|查詢|字串|
|授權|(空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。  授權權杖：`"Bearer" + " " + "access_token"`。  |頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|410|`AddTranslation` 不再受支援。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>實作附註

> [!IMPORTANT]
> **棄用注釋：** 2018 年 1 月 31 日之後，此方法不會接受新的句子提交。 您會收到一條錯誤訊息。 請參閱有關協作翻譯框架 （CTF） 更改的公告。

向翻譯記憶庫添加一系列翻譯。 此方法是 的`AddTranslation`陣列版本。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`。

以下是請求正文的格式：

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

這些元素在`AddtranslationsRequest`中。

* `AppId`：必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`AppId`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。
* `From`：必要。 包含來源語言的語言代碼的字串。 必須是 `GetLanguagesForTranslate` 方法所傳回的其中一種語言。
* `To`：必要。 包含目的語言的語言代碼的字串。 必須是 `GetLanguagesForTranslate` 方法所傳回的其中一種語言。
* `Translations`：必要。 要新增至翻譯記憶體的翻譯陣列。 每個翻譯必須包含`OriginalText``TranslatedText`和`Rating`。 每個`OriginalText`和最大大小`TranslatedText`為 1，000 個字元。 所有`OriginalText`和`TranslatedText`元素的總不能超過 10，000 個字元。 最大陣列項目數是 100。
* `Options`：必要。 一組`Category`選項，包括 、`ContentType``Uri`和`User`。 `User` 是必要的。 `Category``ContentType`，`Uri`是可選的。 指定的項目必須以字母順序列出。

### <a name="response-class-status-200"></a>回應類（狀態 200）
`AddTranslationArray`方法成功。 

2018 年 1 月 31 日之後，將不接受提交句子。 服務將會回應錯誤碼 410。

字串

回應內容類型：應用程式/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|授權|(空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。  授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|410    |`AddTranslation` 不再受支援。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>實作附註
將文本部分分解為句子，並返回包含每個句子長度的陣列。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

**傳回值：** 表示句子長度的整數陣列。 陣列的長度表示句子數。 這些值表示每個句子的長度。

### <a name="response-class-status-200"></a>回應類（狀態 200）
表示句子長度的整數陣列。 陣列的長度表示句子數。 這些值表示每個句子的長度。

integer

回應內容類型：應用程式/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)  |必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢| 字串|
|text|(空白)   |必要。 表示要拆分為句子的文本的字串。 文本的最大大小為 10，000 個字元。|查詢|字串|
|語言   |(空白)    |必要。 表示輸入文本的語言代碼的字串。|查詢|字串|
|授權|(空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。   |頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401|憑據無效。|
|500|伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>實作附註
從存放區和 MT 引擎中擷取指定語言組的翻譯陣列。 `GetTranslations`不同之處在于`Translate`它返回所有可用的翻譯。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

請求的主體包括可選`TranslationOptions`物件，該物件具有以下格式：

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

該`TranslateOptions`物件包含以下清單中的值。 它們都是可選的，預設為最常見的設置。 指定的項目必須以字母順序列出。

* `Category`：包含翻譯的類別（域）的字串。 預設值為 `general`。
* `ContentType`： 唯一支援的選項，預設值為`text/plain`。
* `IncludeMultipleMTAlternatives`： 布林標誌，用於指定是否應從 MT 引擎返回多個替代項。 有效值為`true``false`和（區分大小寫）。 預設值為`false`，它僅返回一個備選方案。 設置標誌，以便`true`創建人工替代方案，與協作翻譯框架 （CTF） 完全集成。 此功能通過從解碼器的*n-* 最佳清單中添加人工替代項，為 CTF 中沒有翻譯的句子添加返回替代項。
    - 評級。 評級應用如下： 
         - 最佳自動翻譯的評分為 5。
       - CTF 的備選方案反映了檢閱者的權威。 它們的範圍從 -10 到 +10。
       - 自動生成的 *（n*-最佳） 轉換替代項的評級為 0，匹配度為 100。
    - 備選方案數。 返回的備選方案數可以和 中`maxTranslations`指定的值一樣高，但可以更低。
    - 語言對。 此功能不適用於簡體中文和繁體中文之間的翻譯。這兩個方向。 它可用於 Microsoft 翻譯支援的所有其他語言對。
* `State`：使用者狀態以説明關聯請求和回應。 相同的內容將在回應中返回。
* `Uri`：依此 URI 篩選結果。 如果未設置任何值，則預設值為`all`。
* `User`：依此使用者篩選結果。 如果未設置任何值，則預設值為`all`。

要求 `Content-Type` 應該是 `text/xml`。

**傳回值：** 以下是回應的格式：

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

此回應包括包含`GetTranslationsResponse`以下值的元素：

* `Translations`： 找到的匹配項陣列，存儲在物件`TranslationMatch`中（如下節所述）。 翻譯可能包括原始文本的細微變體（模糊比對）。 翻譯將被排序：100%匹配第一，模糊比對下一個。
* `From`：如果方法未指定`From`語言，則此值將來自自動語言檢測。 否則，它將是指定的`From`語言。
* `State`：使用者狀態以説明關聯請求和回應。 包含`TranslateOptions`參數中提供的值。

該`TranslationMatch`物件由以下值組成：

* `Error`：如果特定輸入字串發生錯誤，則錯誤代碼。 否則，此欄位為空。
* `MatchDegree`指示輸入文本與存儲中的原始文本的匹配程度。 系統會比對輸入句子與存放區 (包括未完全相符的項目)。 返回的值範圍為 0 到 100，其中 0 沒有相似性，100 是一個精確、區分大小寫的匹配。
* `MatchedOriginalText`：此結果相符的原始文字。 僅當匹配的原始文本與輸入文本不同時，才會返回此值。 它用於返回模糊比對的源文本。 不會為 Microsoft 翻譯結果返回此值。
* `Rating`：指出制定品質決策之人員的授權單位。 機器翻譯結果的評分為 5。 匿名提供的翻譯通常具有從 1 到 4 的評分。 權威提供的翻譯通常有從 6 到 10 的評級。
* `Count`：已選取具有此評分之這個翻譯的次數。 自動轉換回應的值為 0。
* `TranslatedText`：翻譯的文字。

### <a name="response-class-status-200"></a>回應類（狀態 200）
以前`GetTranslationsResponse`描述的格式的物件。

字串

回應內容類型：應用程式/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`標頭，請保留該`appid`欄位為空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。|查詢|字串|
|text|(空白)|必要。 表示要翻譯的文本的字串。 文本的最大大小為 10，000 個字元。|查詢|字串|
|從|(空白)|必要。 表示要翻譯的文本的語言代碼的字串。|查詢|字串|
|to |(空白)    |必要。 表示要將文本轉換為的語言代碼的字串。|查詢|字串|
|maxTranslations|(空白)|必要。 表示要返回的最大轉換數的整數。|查詢|integer|
|授權| (空白)|如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。 授權權杖：`"Bearer" + " " + "access_token"`。|字串|  頁首|
|Ocp-Apim-Subscription-Key|(空白)  |如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請向我們提供請求的大致日期&時間以及回應標頭`X-MS-Trans-Info`中包含的請求 ID。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>實作附註
檢索多個源文本的多個翻譯候選項。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`。

以下是請求正文的格式：

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest`包括以下元素：

* `AppId`：必要。 如果使用標頭`Authorization`，則將`AppId`該欄位留空。 否則，請包含包含 的`"Bearer" + " " + "access_token"`字串。
* `From`：必要。 表示要翻譯的文本的語言代碼的字串。
* `MaxTranslations`：必要。 表示要返回的最大轉換數的整數。
* `Options`：選擇性。 包含`Options`以下值的物件。 它們都是可選的，預設為最常見的設置。 指定的項目必須以字母順序列出。
    - `Category`：包含翻譯的類別（域）的字串。 預設值為 `general`。
    - `ContentType`： 唯一支援的選項，預設值為`text/plain`。
    - `IncludeMultipleMTAlternatives`： 布林標誌，用於指定是否應從 MT 引擎返回多個替代項。 有效值為`true``false`和（區分大小寫）。 預設值為`false`，它僅返回一個備選方案。 設置標誌，使`true`人工翻譯替代方案與協作翻譯框架 （CTF） 完全集成。 此功能通過從解碼器的*n-* 最佳清單中添加人工替代項，從而支援返回 CTF 中沒有替代的句子的替代方法。
        - 評分 評級應用如下：
          - 最佳自動翻譯的評分為 5。
          - CTF 的備選方案反映了檢閱者的權威。 它們的範圍從 -10 到 +10。
          - 自動生成的 *（n*-最佳） 轉換替代項的評級為 0，匹配度為 100。
        - 備選方案數。 返回的備選方案數可以和 中`maxTranslations`指定的值一樣高，但可以更低。
        - 語言對。 此功能不適用於簡體中文和繁體中文之間的翻譯。這兩個方向。 它可用於 Microsoft 翻譯支援的所有其他語言對。
* `State`：使用者狀態以説明關聯請求和回應。 相同的內容將在回應中返回。
* `Uri`：依此 URI 篩選結果。 如果未設置任何值，則預設值為`all`。
* `User`：依此使用者篩選結果。 如果未設置任何值，則預設值為`all`。
* `Texts`：必要。 包含要翻譯的文本的陣列。 所有字串必須使用同一語言。 要翻譯的所有文本總數不能超過 10，000 個字元。 最大陣列項目數是 10。
* `To`：必要。 表示要將文本轉換為的語言代碼的字串。

您可以省略可選元素。 作為 直接`GetTranslationsArrayRequest`子級的元素必須按字母順序列出。

要求 `Content-Type` 應該是 `text/xml`。

**傳回值：** 以下是回應的格式：

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

每個`GetTranslationsResponse`元素包含以下值：

* `Translations`： 找到的匹配項陣列，存儲在物件`TranslationMatch`中（如下節所述）。 翻譯可能包括原始文本的細微變體（模糊比對）。 翻譯將被排序：100%匹配第一，模糊比對下一個。
* `From`：如果方法未指定`From`語言，則此值將來自自動語言檢測。 否則，它將是指定的`From`語言。
* `State`：使用者狀態以説明關聯請求和回應。 包含`TranslateOptions`參數中提供的值。

該`TranslationMatch`物件包含以下值：
* `Error`：如果特定輸入字串發生錯誤，則錯誤代碼。 否則，此欄位為空。
* `MatchDegree`指示輸入文本與存儲中的原始文本的匹配程度。 系統會比對輸入句子與存放區 (包括未完全相符的項目)。 返回的值範圍為 0 到 100，其中 0 沒有相似性，100 是一個精確、區分大小寫的匹配。
* `MatchedOriginalText`：此結果相符的原始文字。 僅當匹配的原始文本與輸入文本不同時，才會返回此值。 它用於返回模糊比對的源文本。 不會為 Microsoft 翻譯結果返回此值。
* `Rating`：指出制定品質決策之人員的授權單位。 機器翻譯結果的評分為 5。 匿名提供的翻譯通常具有從 1 到 4 的評分。 權威提供的翻譯通常具有從 6 到 10 的評級。
* `Count`：已選取具有此評分之這個翻譯的次數。 自動轉換回應的值為 0。
* `TranslatedText`：翻譯的文字。


### <a name="response-class-status-200"></a>回應類（狀態 200）

字串

回應內容類型：應用程式/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|授權  |(空白)    |如果欄位和標頭`appid`都留空`Ocp-Apim-Subscription-Key`，則為必填項。  授權權杖：`"Bearer" + " " + "access_token"`。|頁首|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果欄位和標頭`appid`都留空`Authorization`，則為必填項。|頁首|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |憑據無效。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期和時間，並將要求識別碼包括在回應標頭 `X-MS-Trans-Info` 中。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [遷移到翻譯文本 API v3](../migrate-to-v3.md)


