---
title: 翻譯工具文字 API V3.0 參考
titleSuffix: Azure Cognitive Services
description: 翻譯工具文字 API V3.0 參考文件。 第 3 版翻譯工具文字 API 提供最新 JSON 型 Web API。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 4/2/2020
ms.author: swmachan
ms.openlocfilehash: fcbaabac0961f1269a929fb4a56f81ac282bae29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619153"
---
# <a name="translator-text-api-v30"></a>Microsoft Translator Text API v3.0

## <a name="whats-new"></a>新功能

第 3 版翻譯工具文字 API 提供最新 JSON 型 Web API。 其藉由將現有功能合併成較少的作業來提升可用性和效能，並提供新功能。

 * 轉換功能，可將某種語言的文字從某個字集轉換成另一個字集。
 * 透過一個要求即可翻譯成多種語言。
 * 透過一個要求即可進行語言偵測、翻譯和轉換。
 * 字典查找術語的替代翻譯,查找顯示上下文中使用的術語的回譯和示例。
 * 包含更多資訊的語言偵測結果。

## <a name="base-urls"></a>基底 URL

Microsoft Translator 透過多個資料中心位置來提供服務。 目前,它們位於 10[個 Azure 地理位置](https://azure.microsoft.com/global-infrastructure/regions):

* **美洲:** 美國東部、美國中南部、美國中西部和美國西部 2 
* **亞太地區:** 韓國、日本東部、東南亞和澳大利亞東部
* **歐洲:** 北歐和西歐

對於 Microsoft Translator Text API 的要求大多會由最接近要求發起來源的資料中心負責處理。 如果資料中心發生故障,請求可能會路由到 Azure 地理區之外。

要強制由特定的 Azure 地理位置處理請求,請將 API 請求中的全域終結點更改為所需的區域終結點:

|描述|Azure 地理|基底 URL|
|:--|:--|:--|
|Azure|全球(非區域)|   api.cognitive.microsofttranslator.com|
|Azure|美國|   api-nam.cognitive.microsofttranslator.com|
|Azure|歐洲|  api-eur.cognitive.microsofttranslator.com|
|Azure|亞太地區|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>驗證

訂閱 Azure 認知服務中的翻譯文本 API 或[認知服務多服務](https://azure.microsoft.com/pricing/details/cognitive-services/),並使用訂閱密鑰(在 Azure 門戶中可用)進行身份驗證。 

有三個標頭可供用來驗證您的訂用帳戶。 下表描述了每種功能的使用方式:

|headers|描述|
|:----|:----|
|Ocp-Apim-Subscription-Key|如果您要傳遞祕密金鑰，請使用認知服務訂用帳戶**。<br/>此值是您 Translator Text API 訂用帳戶的 Azure 祕密金鑰。|
|授權|如果您要傳遞驗證權杖，請使用認知服務訂用帳戶**。<br/>此值是持有人權杖：`Bearer <token>`。|
|Ocp-Apim-Subscription-Region|*與認知服務多服務和區域翻譯資源一起使用。*<br/>該值是多服務或區域轉換器資源的區域。 使用全域轉換器資源時,此值是可選的。|

###  <a name="secret-key"></a>祕密金鑰
第一個選項是使用 `Ocp-Apim-Subscription-Key` 標頭來進行驗證。 將`Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>`標頭添加到請求中。

#### <a name="authenticating-with-a-global-resource"></a>使用全域資源進行認證

使用[全域轉換器資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)時,需要包含一個標頭來調用轉換器 API。

|headers|描述|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 此值是您 Translator Text API 訂用帳戶的 Azure 祕密金鑰。|

下面是使用全域轉換器資源呼叫轉換器 API 的範例請求

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>使用區域資源進行認證

當您使用[區域轉換器資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)時。
需要調用轉換器 API 需要 2 個標頭。

|headers|描述|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 此值是您 Translator Text API 訂用帳戶的 Azure 祕密金鑰。|
|Ocp-Apim-Subscription-Region| 該值是轉換器資源的區域。 |

下面是使用區域轉換器資源呼叫翻譯 API 的範例請求

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>使用多服務資源進行認證

使用認知服務的多服務資源時。 這可讓您使用單一祕密金鑰來驗證多個服務的要求。 

使用多服務密鑰時,必須隨請求包含兩個身份驗證標頭。 需要調用轉換器 API 需要 2 個標頭。

|headers|描述|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 該值是多服務資源的 Azure 密鑰。|
|Ocp-Apim-Subscription-Region| 該值是多服務資源的區域。 |

多服務文本 API 訂閱需要區域。 您選擇的區域是使用多服務訂閱密鑰時可用於文本翻譯的唯一區域,並且必須與通過 Azure 門戶註冊多服務訂閱時選擇的區域相同。

可用區域`australiaeast``brazilsouth``canadacentral``centralindia`是`centralus``westus``westus2``southafricanorth``japanwest``koreacentral``northcentralus``northeurope``eastasia`、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `southcentralus` `southeastasia` `uksouth` `westcentralus` `eastus` `eastus2` `francecentral` `japaneast` `westeurope` `centraluseuap`

如果您在查詢字串中使用參數 `Subscription-Key` 傳遞祕密金鑰，則必須使用查詢參數 `Subscription-Region` 來指定區域。

### <a name="authenticating-with-an-access-token"></a>使用存取權杖進行認證
或者，您可以用秘密金鑰交換存取權杖。 此權杖會隨附在每個要求中作為 `Authorization` 標頭。 若要取得授權權杖，請對下列 URL 提出 `POST` 要求：

| 資源類型     | 驗證服務 URL                                |
|-----------------|-----------------------------------------------------------|
| 全域          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| 區域或多服務 | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

以下要求範例示範如何取得具有祕密金鑰的權杖：

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

成功的要求會以純文字形式在回應本文中傳回已編碼的存取權杖。 有效的權杖會傳遞至翻譯工具服務，以作為授權的持有人權杖。

```http
Authorization: Bearer <Base64-access_token>
```

驗證權杖的有效時間為 10 分鐘。 對轉換器 API 進行多次呼叫時,應重複使用權杖。 但是,如果程式在較長時間內向轉換器 API 發出請求,則程式必須定期請求新的訪問權杖(例如,每 8 分鐘一次)。

## <a name="virtual-network-support"></a>虛擬網路支援

翻譯服務現在`WestUS2``EastUS``SouthCentralUS``WestUS``Central US EUAP``global`可在有限區域(、、、、、、、、、、、、、 ) 中使用虛擬網路功能。 要啟用虛擬網路,請參閱[設定 Azure 認知服務虛擬網路](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)。 

打開此功能後,必須使用自定義終結點調用轉換器 API。 不能使用全域轉換器終結點("api.cognitive.microsofttranslator.com"),也不能使用訪問令牌進行身份驗證。

創建[翻譯資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)後,可以找到自定義終結點。

|headers|描述|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 此值是您 Translator Text API 訂用帳戶的 Azure 祕密金鑰。|
|Ocp-Apim-Subscription-Region| 該值是轉換器資源的區域。 如果資源是`global`|

下面是使用自訂的終結點呼叫轉換器 API 的範例請求

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Errors

標準錯誤回應是名稱/值組為 `error` 的 JSON 物件。 此值也可以是具有下列屬性的 JSON 物件：

  * `code`：伺服器定義的錯誤碼。
  * `message`：以人類可閱讀形式表示錯誤的字串。

例如，持有免費試用訂用帳戶的客戶會在免費配額用完時，收到下列錯誤：

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
錯誤碼是 6 位數的數字，其中結合了 3 位數的 HTTP 狀態碼，後面接著將錯誤進一步分類的 3 位數數字。 常見的錯誤碼包括：

| 程式碼 | 描述 |
|:----|:-----|
| 400000| 其中一個要求輸入無效。|
| 400001| "scope" 參數無效。|
| 400002| "category" 參數無效。|
| 400003| 語言指定名稱遺漏或無效。|
| 400004| 目標指令碼指定名稱 ("To script") 遺漏或無效。|
| 400005| 輸入文字遺漏或無效。|
| 400006| 語言與指令碼的組合無效。|
| 400018| 來源指令碼指定名稱 ("From script") 遺漏或無效。|
| 400019| 不支援其中一種指定語言。|
| 400020| 輸入文字陣列中的其中一個元素無效。|
| 400021| API 版本參數遺漏或無效。|
| 400023| 其中一個指定的語言組無效。|
| 400035| 來源語言 ("From" 欄位) 無效。|
| 400036| 目標語言 ("To" 欄位) 無效。|
| 400042| 其中一個指定的選項 ("Options" 欄位) 無效。|
| 400043| 用戶端追蹤識別碼 ID (ClientTraceId 欄位或 X-ClientTranceId 標頭) 遺漏或無效。|
| 400050| 輸入文字太長。 檢視[要求限制](../request-limits.md)。|
| 400064| "translation" 參數遺漏或無效。|
| 400070| 目標指令碼 (ToScript 參數) 數目與目標語言 (To 參數) 數目不符。|
| 400071| 值不是有效的 TextType 值。|
| 400072| 輸入文字陣列的元素太多。|
| 400073| 指令碼參數無效。|
| 400074| 要求本文不是有效的 JSON。|
| 400075| 語言組與類別組合無效。|
| 400077| 已超過要求大小上限。 檢視[要求限制](../request-limits.md)。|
| 400079| 所要求用來在來源與目標語言之間進行翻譯的自訂系統不存在。|
| 400080| 語言或腳本不支援音譯。|
| 401000| 要求未獲授權，因為認證遺漏或無效。|
| 401015| 「提供的認證是 Speech API 的認證。 此要求需要的是「文字 API」的認證。 使用翻譯文本 API 的訂閱。|
| 403000| 不允許此作業。|
| 403001| 不允許此作業，因為訂用帳戶已超出其可用配額。|
| 405000| 要求方法不是所要求資源支援的方法。|
| 408001| 正在編寫所要求的翻譯系統。 請稍待數分鐘後重試。|
| 408002| 請求超時等待傳入流。 用戶端未在伺服器準備等待的時間內生成請求。 用戶端可以重複請求,以後可以隨時修改。|
| 415000| Content-Type 標頭遺漏或無效。|
| 429000、429001、429002| 伺服器拒絕請求,因為客戶端已超過請求限制。|
| 500000| 發生意外錯誤。 如果錯誤持續存在，請回報錯誤並提供錯誤的日期/時間、來自回應標頭 X-RequestId 的要求識別碼，以及來自要求標頭 X-ClientTraceId 的用戶端識別碼。|
| 503000| 服務暫時無法使用。 請再試一次。 如果錯誤持續存在，請回報錯誤並提供錯誤的日期/時間、來自回應標頭 X-RequestId 的要求識別碼，以及來自要求標頭 X-ClientTraceId 的用戶端識別碼。|

## <a name="metrics"></a>計量 
指標允許您在 Azure 門戶中查看翻譯人員使用方式和可用性資訊,在指標部分下,如以下螢幕截圖所示。 有關詳細資訊,請參閱[資料和平台指標](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

![轉換器指標](../media/translatormetrics.png)

此表列出了可用指標,說明如何使用這些指標來監視翻譯 API 調用。

| 計量 | 描述 |
|:----|:-----|
| TotalCalls| API 呼叫總數。|
| TotalTokenCalls| 使用身份驗證權杖透過權杖服務呼叫的 API 總數。|
| SuccessfulCalls| 成功的呼叫數。|
| TotalErrors| 具有錯誤回應的呼叫數。|
| BlockedCalls| 超過速率或配額限制的呼叫數目。|
| ServerErrors| 具有伺服器內部錯誤的呼叫數 (5XX)。|
| ClientErrors| 用戶端錯誤 (4XX) 的調用數。|
| Latency| 以毫秒為單位完成請求的持續時間。|
| CharactersTranslated| 傳入文字要求中的字元總數。|
