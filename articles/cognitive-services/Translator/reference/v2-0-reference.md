---
title: Translator 2.0 版
titleSuffix: Azure Cognitive Services
description: Translator 2.0 版的參考檔。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 7fa148579e7525933d388b8a93c9a3476f473cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83588610"
---
# <a name="translator-v20"></a>Translator 2.0 版

> [!IMPORTANT]
> 此版本的翻譯工具已淘汰。 請[參閱第3版翻譯工具的檔](v3-0-reference.md)。

第2版的翻譯工具可以緊密整合到您的應用程式、網站、工具或其他解決方案，以提供多語言使用者體驗。 您可以將它用於任何硬體平臺和任何作業系統，以根據產業標準來執行語言轉譯和其他與語言相關的工作，例如文字語言偵測和文字轉換語音。 如需詳細資訊，請參閱 [Translator](../translator-info-overview.md)。

## <a name="getting-started"></a>開始使用
若要存取翻譯工具，您必須 [註冊 Microsoft Azure](../translator-text-how-to-signup.md)。

## <a name="authentication"></a>驗證 
對翻譯工具的所有呼叫都需要訂用帳戶金鑰才能進行驗證。 API 支援三種驗證方法：

- 存取權杖。 使用訂用帳戶金鑰來建立存取權杖，方法是向驗證服務提出 POST 要求。 如需詳細資料，請參閱權杖服務文件。 使用 `Authorization` 標頭或查詢參數，將存取權杖傳遞至翻譯工具服務 `access_token` 。 存取權杖的有效時間為 10 分鐘。 每隔10分鐘取得新的存取權杖，並在10分鐘內針對重複的要求繼續使用相同的存取權杖。
- 直接使用的訂用帳戶金鑰。 將您的訂用帳戶金鑰作為要求隨附的標頭中的值傳遞 `Ocp-Apim-Subscription-Key` 至翻譯工具。 當您直接使用訂用帳戶金鑰時，您不需要呼叫權杖驗證服務來建立存取權杖。
- [Azure 認知服務的多服務訂](https://azure.microsoft.com/pricing/details/cognitive-services/)用帳戶。 此方法可讓您使用單一秘密金鑰來驗證多個服務的要求。
當您使用多服務秘密金鑰時，您需要在要求中包含兩個驗證標頭。 第一個標頭會傳遞秘密金鑰。 第二個標頭會指定與您的訂用帳戶相關聯的區域：
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

多服務文字 API 訂用帳戶需要區域。 當您使用多服務訂用帳戶金鑰時，您選取的區域是唯一可用來進行文字翻譯的區域。 當您在 Azure 入口網站上註冊您的多服務訂用帳戶時，它必須是您所選取的相同區域。

可用的區域包括 `australiaeast` 、 `brazilsouth` 、、、、、、、、、、、、、、 `canadacentral` `centralindia` `centraluseuap` `eastasia` `eastus` `eastus2` `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` 和 `westus2` 。

您的訂用帳戶金鑰和存取權杖是應該隱藏的秘密。

## <a name="profanity-handling"></a>粗話處理
一般而言，翻譯工具服務會保留來源中沒有的不雅內容。 不雅內容的程度和不雅單字的內容會根據文化特性而有所不同。 因此，目的語言中的不雅內容的程度可能會增加或減少。

如果您想要防止翻譯中的不雅內容（即使是在來源文字中），您可以針對支援的方法使用不雅內容篩選選項。 此選項可讓您選擇要查看是否有不雅內容，或以適當的標記標記，或是否要允許目標中的不雅內容。 接受的值 `ProfanityAction` 為 `NoAction` (預設) 、 `Marked` 和 `Deleted` 。


|ProfanityAction    |動作 | (日文) 的範例來源  |英文) 範例轉譯 (  |
|:--|:--|:--|:--|
|NoAction   |預設值。 與未設定此選項時相同。 粗話會從來源傳遞到目標。        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |不雅字眼會以 XML 標籤 \<profanity> 和 \</profanity> 括住。       |彼はジャッカスです。 |He is a \<profanity>jackass\</profanity>.  |
|已刪除    |將會從輸出中移除不雅字眼，但不予取代。     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>從翻譯中排除內容
當您翻譯具有標記的內容（例如 HTML (`contentType=text/html`) ）時，從翻譯中排除特定內容有時會很有用。 您可以使用 `class=notranslate` 屬性，指定應該保留其原始語言的內容。 在下列範例中，不會轉譯第一個元素中的內容 `div` ，但會轉譯第二個專案中的內容 `div` 。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>實作附註
將文字字串從某種語言翻譯成另一種語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

傳回**值：** 表示已翻譯文字的字串。

如果您先前使用 `AddTranslation` 或 `AddTranslationArray` 針對相同的來源句子輸入具有5或更高評等的翻譯，則 `Translate` 只會傳回您系統可用的最上層選擇。 「相同的來源句子」表示) 的相同 (100% 的相符項，但不包括大小寫、空白字元、標記值和標點符號（在句子結尾）。 如果未以5或以上的評等來儲存評等，則傳回的結果會是由 Microsoft Translator 自動轉譯。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 

字串

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|說明    |參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid  |(空白)    |必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|text|(空白)   |必要。 字串，表示要轉譯的文字。 文字不可包含超過10000個字元。|查詢|字串|
|從|(空白)   |選擇性。 字串，表示所要翻譯文字的語言代碼。 例如，en 代表「英文」。|查詢|字串|
|to|(空白) |必要。 字串，代表翻譯文字的語言代碼。|查詢|字串|
|ContentType|(空白)    |選擇性。 要翻譯文字的格式。 支援的格式 `text/plain` (預設) 和  `text/html` 。 任何 HTML 專案都必須是格式正確且完整的元素。|查詢|字串|
|category|(空白)   |選擇性。 字串，包含翻譯 (網域) 的分類。 預設為 `general`。|查詢|字串|
|授權|(空白)  |如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|


### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>實作附註
抓取多個來源文字的翻譯。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`。

以下是要求主體的格式：

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

這些元素位於 `TranslateArrayRequest` ：


* `AppId`：必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `AppId` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。
* `From`:選擇性。 字串，表示所要翻譯文字的語言代碼。 如果此欄位保留空白，回應將會包含自動語言偵測的結果。
* `Options`:選擇性。 `Options`包含下列值的物件。 它們都是選擇性的，且預設為最常見的設定。 指定的項目必須以字母順序列出。
    - `Category`：包含翻譯 (網域) 類別的字串。 預設為 `general`。
    - `ContentType`：要翻譯文字的格式。 支援的格式 `text/plain` (預設) 、 `text/xml` 和 `text/html` 。 任何 HTML 專案都必須是格式正確且完整的元素。
    - `ProfanityAction`：指定如何處理粗話，如先前所述。 接受的值 `NoAction` (預設值) 、 `Marked` 和 `Deleted` 。
    - `State`：可協助將要求和回應相互關聯的使用者狀態。 回應中會傳回相同的內容。
    - `Uri`：依此 URI 篩選結果。 預設：`all`。
    - `User`：依此使用者篩選結果。 預設：`all`。
* `Texts`：必要。 陣列，其中包含翻譯的文字。 所有字串都必須採用相同的語言。 所有要轉譯的文字總計不能超過10000個字元。 陣列元素的最大數目是2000。
* `To`：必要。 字串，代表翻譯文字的語言代碼。

您可以省略選擇性元素。 直接子系的專案 `TranslateArrayRequest` 必須以字母順序列出。

`TranslateArray`方法會接受 `application/xml` 或 `text/xml` `Content-Type` 。

**傳回值：**`TranslateArrayResponse` 陣列。 每個 `TranslateArrayResponse` 都有下列元素：

* `Error`：指出發生錯誤時的錯誤。 否則設定為 null。
* `OriginalSentenceLengths`：整數陣列，指出來源文字中每個句子的長度。 陣列長度，指出句子數目。
* `TranslatedText`：翻譯的文字。
* `TranslatedSentenceLengths`：整數陣列，指出翻譯文字中每個句子的長度。 陣列長度，指出句子數目。
* `State`：可協助將要求和回應相互關聯的使用者狀態。 傳回與要求相同的內容。

以下是回應主體的格式：

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

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
成功的回應包含陣列的陣列 `TranslateArrayResponse` ，此陣列的格式稍早所述。

字串

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|授權|(空白)  |如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼   |原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。 常見錯誤包括： <ul><li>陣列元素不可為空白。</li><li>不正確分類。</li><li>從語言無效。</li><li>至語言無效。</li><li>要求包含太多元素。</li><li>不支援 From 語言。</li><li>不支援 To 語言。</li><li>轉譯要求的資料太多。</li><li>HTML 的格式不正確。</li><li>轉譯要求中傳遞了太多字串。</li></ul>|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>實作附註
抓取以參數形式傳入之語言的易記名稱 `languageCodes` ，並當地語系化為傳遞的 `locale` 語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

要求本文包含字串陣列，代表要取得其易記名稱的 ISO 639-1 語言代碼。 以下為範例：

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

傳回**值：** 字串陣列，其中包含 Translator 服務所支援的語言名稱，當地語系化為所要求的語言。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
字串陣列，其中包含 Translator 服務所支援且當地語系化為所要求語言的語言名稱。

字串

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|locale|(空白) |必要。 表示下列其中一項的字串，用來將語言名稱當地語系化： <ul><li>與語言相關聯的 ISO 639 2 字母小寫文化特性代碼和 ISO 3166 2 字母大寫子文化特性代碼的組合。 <li>ISO 639 小寫文化特性代碼本身。|查詢|字串|
|授權|(空白)  |如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>實作附註
取得表示轉譯服務所支援語言的語言代碼清單。  `Translate` 和 `TranslateArray` 可以翻譯這些語言的任兩種。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

傳回**值：** 字串陣列，包含 Translator 服務所支援的語言代碼。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
字串陣列，包含 Translator 服務所支援的語言代碼。

字串

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|授權|(空白)  |如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>實作附註
擷取提供語音合成功能的語言種類。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

傳回**值：** 字串陣列，包含 Translator 服務所支援的語音合成語言代碼。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
字串陣列，包含 Translator 服務所支援的語音合成語言代碼。

字串

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|授權|(空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|
 
### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401|不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>實作附註
傳回以所需語言說出之傳入文字的 WAV 或 MP3 串流。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

傳回**值：** 以所需語言說出之傳入文字的 WAV 或 MP3 串流。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 

BINARY

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|text|(空白)   |必要。 字串，包含一或多個要以指定語言讀出之資料流程的句子。 文字不能超過2000個字元。|查詢|字串|
|語言|(空白)   |必要。 字串，表示用來說出文字之語言的支援語言代碼。 程式碼必須是方法所傳回的其中一個代碼 `GetLanguagesForSpeak` 。|查詢|字串|
|format|(空白)|選擇性。 指定內容類型識別碼的字串。 目前可以使用 `audio/wav` 和 `audio/mp3`。 預設值為 `audio/wav`。|查詢|字串|
|選項|(空白)    |選擇性。 指定合成語音屬性的字串：<ul><li>`MaxQuality` 並 `MinSize` 指定音訊信號的品質。 `MaxQuality` 提供最高品質。 `MinSize` 提供最小的檔案大小。 預設值為  `MinSize` 。</li><li>`female` 並 `male` 指定所需的聲音性別。 預設為 `female`。 使用垂直線 (<code>\|</code>) 包含多個選項。 例如，`MaxQuality|Male`。</li></li></ul>  |查詢|字串|
|授權|(空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>實作附註
識別一段文字的語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

傳回**值：** 字串，包含文字的雙字元語言代碼。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 

字串

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)  |必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|text|(空白)|必要。 包含要識別其語言之文字的字串。 文字不能超過10000個字元。|查詢|  字串|
|授權|(空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key  |(空白)    |如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>實作附註

識別字串陣列中的語言。 獨立偵測每個個別陣列元素的語言，並傳回陣列中每個資料列的結果。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

以下是要求主體的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文字不能超過10000個字元。

傳回**值：** 字串陣列，包含輸入陣列中的每個資料列都有兩個字元的語言代碼。

以下是回應主體的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
`DetectArray` 成功。 傳回字串陣列，其中包含輸入陣列的每個資料列都有兩個字元的語言代碼。

字串

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|授權|(空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。  授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>實作附註

> [!IMPORTANT]
> 淘汰**附注：** 2018年1月31日之後，此方法將不會接受新的句子提交。 您將會收到錯誤訊息。 請參閱有關共同翻譯架構 (CTF) 之變更的公告。

將翻譯新增至翻譯記憶體。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 

字串

回應內容類型： application： xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型   |
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|originalText|(空白)|必要。 字串，包含要轉譯的文字。 字串的最大長度是1000個字元。|查詢|字串|
|translatedText|(空白) |必要。 包含翻譯成目的語言之文字的字串。 字串的最大長度是2000個字元。|查詢|字串|
|從|(空白)   |必要。 字串，表示文字原始語言的語言代碼。 例如，en 代表英文，取消德文。|查詢|字串|
|to|(空白)|必要。 字串，表示要將文字轉譯成之語言的語言代碼。|查詢|字串|
|rating|(空白) |選擇性。 表示字串品質評等的整數。 值介於-10 到10之間。 預設值為 1。|查詢|整數|
|ContentType|(空白)    |選擇性。 要翻譯文字的格式。 支援的格式為 `text/plain` 和 `text/html` 。 任何 HTML 專案都必須是格式正確且完整的元素。    |查詢|字串|
|category|(空白)|選擇性。 字串，包含翻譯 (網域) 的分類。 預設為 `general`。|查詢|字串|
|user|(空白)|必要。 用來追蹤提交的建立者的字串。|查詢|字串|
|uri|(空白)|選擇性。 字串，包含翻譯的內容位置。|查詢|字串|
|授權|(空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。  授權權杖：`"Bearer" + " " + "access_token"`。  |header|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|410|`AddTranslation` 不再受支援。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>實作附註

> [!IMPORTANT]
> 淘汰**附注：** 2018年1月31日之後，此方法將不會接受新的句子提交。 您將會收到錯誤訊息。 請參閱有關共同翻譯架構 (CTF) 之變更的公告。

將翻譯的陣列加入至翻譯記憶體。 這個方法是的陣列版本 `AddTranslation` 。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`。

以下是要求主體的格式：

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

這些元素位於 `AddtranslationsRequest` ：

* `AppId`：必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `AppId` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。
* `From`：必要。 包含來源語言語言代碼的字串。 必須是 `GetLanguagesForTranslate` 方法所傳回的其中一種語言。
* `To`：必要。 字串，包含目的語言的語言代碼。 必須是 `GetLanguagesForTranslate` 方法所傳回的其中一種語言。
* `Translations`：必要。 要新增至翻譯記憶體的翻譯陣列。 每個翻譯都必須包含 `OriginalText` 、 `TranslatedText` 和 `Rating` 。 每個和的大小 `OriginalText` 上限 `TranslatedText` 為1000個字元。 All `OriginalText` 和 elements 的總計 `TranslatedText` 不能超過10000個字元。 最大陣列項目數是 100。
* `Options`：必要。 一組選項，包括 `Category` 、 `ContentType` 、 `Uri` 和 `User` 。 `User` 是必要的。 `Category`、 `ContentType` 和 `Uri` 是選擇性的。 指定的項目必須以字母順序列出。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
`AddTranslationArray` 方法成功。 

2018年1月31日之後，將不會接受句子提交。 服務將會回應錯誤碼 410。

字串

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|授權|(空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。  授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|410    |`AddTranslation` 不再受支援。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>實作附註
將文字區段分成句子，並傳回陣列，其中包含每個句子的長度。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

傳回**值：** 整數的陣列，表示句子的長度。 陣列的長度代表句子數目。 值代表每個句子的長度。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
整數的陣列，表示句子的長度。 陣列的長度代表句子數目。 值代表每個句子的長度。

整數

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)  |必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢| 字串|
|text|(空白)   |必要。 表示要分割成句子之文字的字串。 文字的大小上限為10000個字元。|查詢|字串|
|語言   |(空白)    |必要。 字串，表示輸入文字的語言代碼。|查詢|字串|
|授權|(空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。   |header|字串|
|Ocp-Apim-Subscription-Key|(空白)|如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401|不正確認證。|
|500|伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>實作附註
從存放區和 MT 引擎中擷取指定語言組的翻譯陣列。 `GetTranslations` 與不同 `Translate` ，它會傳回所有可用的翻譯。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

要求的本文包含選用 `TranslationOptions` 物件，其格式如下：

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

此 `TranslateOptions` 物件包含下列清單中的值。 它們都是選擇性的，且預設為最常見的設定。 指定的項目必須以字母順序列出。

* `Category`：包含翻譯 (網域) 類別的字串。 預設為 `general`。
* `ContentType`：唯一支援的選項以及預設值為 `text/plain` 。
* `IncludeMultipleMTAlternatives`：布林值旗標，指定是否應該從 MT 引擎傳回一個以上的替代選項。 有效的值為 `true` ，且 `false` (區分大小寫的) 。 預設值是 `false` ，它只會傳回一個替代方法。 設定旗標可 `true` 讓您建立人工替代方案，與共同作業轉譯架構 (CTF) 完全整合在一起。 這項功能可讓您藉由在解碼器的 *n*個最佳清單中新增人工替代專案，來傳回在 CTF 中沒有翻譯的句子替代專案。
    - 評級。 分級的套用方式如下： 
         - 最佳自動翻譯的評分為 5。
       - CTF 中的替代方案會反映審核者的授權單位。 範圍從-10 到 + 10。
       - 自動產生的 (*n*最佳) 轉譯替代專案的評等為0，而相符程度為100。
    - 替代專案的數目。 傳回的替代專案數目可以高達中指定的值 `maxTranslations` ，但可以較低。
    - 語言組。 這項功能不適用於簡體中文與繁體中文之間的翻譯。 它適用于 Microsoft Translator 支援的所有其他語言配對。
* `State`：可協助將要求和回應相互關聯的使用者狀態。 回應中會傳回相同的內容。
* `Uri`：依此 URI 篩選結果。 如果未設定任何值，則預設值為 `all` 。
* `User`：依此使用者篩選結果。 如果未設定任何值，則預設值為 `all` 。

要求 `Content-Type` 應該是 `text/xml`。

傳回**值：** 以下是回應的格式：

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

此回應包含 `GetTranslationsResponse` 包含下列值的元素：

* `Translations`：找到的相符專案陣列， `TranslationMatch` (下一節) 所述的物件中儲存。 翻譯可能包含原始文字的微小變化 (模糊比對) 。 將會排序翻譯：先符合100% 的相符專案，然後再模糊符合。
* `From`：如果方法未指定 `From` 語言，則此值會來自自動語言偵測。 否則，它會是指定的 `From` 語言。
* `State`：可協助將要求和回應相互關聯的使用者狀態。 包含參數中提供的值 `TranslateOptions` 。

此 `TranslationMatch` 物件包含下列值：

* `Error`：若特定輸入字串發生錯誤，則為錯誤碼。 否則，此欄位會是空的。
* `MatchDegree`：指出輸入文字與存放區中找到的原始文字緊密相符的程度。 系統會比對輸入句子與存放區 (包括未完全相符的項目)。 傳回的值範圍從0到100，其中0是沒有相似性，100是完全區分大小寫的相符項。
* `MatchedOriginalText`：此結果相符的原始文字。 只有當相符的原始文字與輸入文字不同時，才會傳回這個值。 它是用來傳回模糊相符的原始程式文字。 Microsoft Translator 結果不會傳回此值。
* `Rating`：指出制定品質決策之人員的授權單位。 電腦轉譯結果的評等為5。 匿名提供的翻譯一般會有1到4的評等。 授權提供的翻譯一般會有6到10之間的評等。
* `Count`：已選取具有此評分之這個翻譯的次數。 自動轉譯回應的值為0。
* `TranslatedText`：翻譯的文字。

### <a name="response-class-status-200"></a>回應類別 (狀態 200) 
`GetTranslationsResponse`先前所述格式的物件。

字串

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 標頭，請將 `appid` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。|查詢|字串|
|text|(空白)|必要。 字串，表示要轉譯的文字。 文字的大小上限為10000個字元。|查詢|字串|
|從|(空白)|必要。 字串，表示所要翻譯文字的語言代碼。|查詢|字串|
|to |(空白)    |必要。 字串，表示要將文字轉譯成之語言的語言代碼。|查詢|字串|
|maxTranslations|(空白)|必要。 整數，表示要傳回的翻譯數目上限。|查詢|整數|
|授權| (空白)|如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。 授權權杖：`"Bearer" + " " + "access_token"`。|字串|  header|
|Ocp-Apim-Subscription-Key|(空白)  |如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期 & 時間，以及回應標頭中包含的要求識別碼 `X-MS-Trans-Info` 。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>實作附註
抓取多個來源文字的多個翻譯候選項目。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`。

以下是要求主體的格式：

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

`GetTranslationsArrayRequest` 包含下列元素：

* `AppId`：必要。 如果 `Authorization` 使用標頭，請將 `AppId` 欄位保留空白。 否則，請包含包含的字串 `"Bearer" + " " + "access_token"` 。
* `From`：必要。 字串，表示所要翻譯文字的語言代碼。
* `MaxTranslations`：必要。 整數，表示要傳回的翻譯數目上限。
* `Options`:選擇性。 `Options`包含下列值的物件。 它們都是選擇性的，且預設為最常見的設定。 指定的項目必須以字母順序列出。
    - `Category`：包含翻譯 (網域) 類別的字串。 預設為 `general`。
    - `ContentType`：唯一支援的選項以及預設值為 `text/plain` 。
    - `IncludeMultipleMTAlternatives`：布林值旗標，指定是否應該從 MT 引擎傳回一個以上的替代選項。 有效的值為 `true` ，且 `false` (區分大小寫的) 。 預設值是 `false` ，它只會傳回一個替代方法。 將旗標設定為可 `true` 在翻譯中產生人工替代方案，並與共同作業翻譯架構 (CTF) 完全整合。 這項功能可讓您藉由在解碼器的 *n*個最佳清單中新增人工替代專案，來傳回在 CTF 中沒有替代專案的替代句子替代專案。
        - 分級的套用方式如下：
          - 最佳自動翻譯的評分為 5。
          - CTF 中的替代方案會反映審核者的授權單位。 範圍從-10 到 + 10。
          - 自動產生的 (*n*最佳) 轉譯替代專案的評等為0，而相符程度為100。
        - 替代專案的數目。 傳回的替代專案數目可以高達中指定的值 `maxTranslations` ，但可以較低。
        - 語言組。 這項功能不適用於簡體中文與繁體中文之間的翻譯。 它適用于 Microsoft Translator 支援的所有其他語言配對。
* `State`：可協助將要求和回應相互關聯的使用者狀態。 回應中會傳回相同的內容。
* `Uri`：依此 URI 篩選結果。 如果未設定任何值，則預設值為 `all` 。
* `User`：依此使用者篩選結果。 如果未設定任何值，則預設值為 `all` 。
* `Texts`：必要。 陣列，其中包含翻譯的文字。 所有字串都必須採用相同的語言。 所有要轉譯的文字總計不能超過10000個字元。 最大陣列項目數是 10。
* `To`：必要。 字串，表示要將文字轉譯成之語言的語言代碼。

您可以省略選擇性元素。 直接子系的專案 `GetTranslationsArrayRequest` 必須以字母順序列出。

要求 `Content-Type` 應該是 `text/xml`。

傳回**值：** 以下是回應的格式：

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

每個 `GetTranslationsResponse` 元素都包含下列值：

* `Translations`：找到的相符專案陣列， `TranslationMatch` (下一節) 所述的物件中儲存。 翻譯可能包含原始文字的微小變化 (模糊比對) 。 將會排序翻譯：先符合100% 的相符專案，然後再模糊符合。
* `From`：如果方法未指定 `From` 語言，則此值會來自自動語言偵測。 否則，它會是指定的 `From` 語言。
* `State`：可協助將要求和回應相互關聯的使用者狀態。 包含參數中提供的值 `TranslateOptions` 。

此 `TranslationMatch` 物件包含下列值：
* `Error`：若特定輸入字串發生錯誤，則為錯誤碼。 否則，此欄位會是空的。
* `MatchDegree`：指出輸入文字與存放區中找到的原始文字緊密相符的程度。 系統會比對輸入句子與存放區 (包括未完全相符的項目)。 傳回的值範圍從0到100，其中0是沒有相似性，100是完全區分大小寫的相符項。
* `MatchedOriginalText`：此結果相符的原始文字。 只有當相符的原始文字與輸入文字不同時，才會傳回這個值。 它是用來傳回模糊相符的原始程式文字。 Microsoft Translator 結果不會傳回此值。
* `Rating`：指出制定品質決策之人員的授權單位。 電腦轉譯結果的評等為5。 匿名提供的翻譯一般會有1到4的評等。 授權提供的翻譯一般會有6到10之間的評等。
* `Count`：已選取具有此評分之這個翻譯的次數。 自動轉譯回應的值為0。
* `TranslatedText`：翻譯的文字。


### <a name="response-class-status-200"></a>回應類別 (狀態 200) 

字串

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|授權  |(空白)    |如果 `appid` 欄位和 `Ocp-Apim-Subscription-Key` 標頭都保持空白，則為必要項。  授權權杖：`"Bearer" + " " + "access_token"`。|header|字串|
|Ocp-Apim-Subscription-Key|(空白)  |如果 `appid` 欄位和 `Authorization` 標頭都保持空白，則為必要項。|header|字串|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|原因|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |不正確認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期和時間，並將要求識別碼包括在回應標頭 `X-MS-Trans-Info` 中。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [遷移至 Translator v3](../migrate-to-v3.md)


