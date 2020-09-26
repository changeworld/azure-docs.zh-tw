---
title: 使用 Bing 拼字檢查 API
titleSuffix: Azure Cognitive Services
description: 深入瞭解 Bing 拼寫檢查模式、設定，以及與 API 相關的其他資訊。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a17ed0f13e352fb185d20d9dc2d7bfffa74ac957
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334764"
---
# <a name="using-the-bing-spell-check-api"></a>使用 Bing 拼字檢查 API

請使用本文以了解如何使用 Bing 拼字檢查 API 來對內容執行文法與拼字檢查。 大部分的拼字檢查工具都依賴以字典為基礎的規則集，但 Bing 拼字檢查工具卻是利用機器學習和統計機器翻譯來對內容提供正確的校正。 

## <a name="spell-check-modes"></a>拼字檢查模式

此 API 支援兩種校訂模式：`Proof` 和 `Spell`。  請看看[此處](https://azure.microsoft.com/services/cognitive-services/spell-check/)的範例。

### <a name="proof---for-documents"></a>Proof - 適用於文件 

預設模式為 `Proof`。 `Proof` 拼字模式可提供最全面的檢查，並可新增大小寫、基本標點符號及其他功能，以利建立文件。 但此模式僅適用於 en-US (美式英文)、es-ES (西班牙文)、pt-BR (葡萄牙文) 市場 (附註：西班牙文和葡萄牙文僅限於 Beta 版)。 對於其他所有的市場，請將 mode 查詢參數設定為 Spell。 

> [!NOTE]
> 查詢文字的長度若超過 4096，則會先截斷為 4096 個字元再進行處理。 

### <a name="spell----for-web-searchesqueries"></a>Spell - 適用於 Web 搜尋/查詢

`Spell` 會較積極地處理而傳回較佳的搜尋結果。 `Spell` 模式可找出大部分的拼字錯誤，但有可能找不到 `Proof` 可挑出的某些文法錯誤，例如大小寫和重複的文字。

> [!NOTE]
> * 以下是支援的查詢長度上限。 如果查詢超過長度上限，查詢及其結果將不會改變。
>    * 下列語言代碼的限制為 130 個字元：en、de、es、fr、pl、pt、sv、ru、nl、nb、tr-tr、it、zh、ko。 
>    * 其他語言的限制為 65 個字元。
> * 拼字模式不支援查詢中的方括號字元 ( `[` 和 `]`)，這可能會導致不一致的結果。 建議您在使用拼字模式時，從查詢中移除這些字元。

## <a name="market-setting"></a>市場設定

[市場代碼](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes)應該與 `mkt` 查詢參數一起在要求中指定。 否則，API 會使用以要求的 IP 位址為基礎的預設市場。


## <a name="http-post-and-get-support"></a>HTTP POST 與 GET 支援

此 API 支援 HTTP POST 或 HTTP GET。 應使用何者，取決於您要校訂的文字長度。 如果字串一律少於 1,500 個字元，您應使用 GET。 但若您要支援高達 10,000 個字元的字串，則應使用 POST。 文字字串可包含任何有效的 UTF-8 字元。

下列範例顯示要對文字字串檢查拼字和文法的 POST 要求。 為求完整性，此範例加入了 [mode](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) 查詢參數 (原本會排除此參數，因為 `mode` 預設為 Proof)。 [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) 查詢參數包含要校訂的字串。
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

如果您使用 HTTP GET，您應在 URL 的查詢字串中加入 `text` 查詢參數
  
以下顯示前一個要求的回應。 回應包含 [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck) 物件。 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
[FlaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) 欄位列出此 API 在[文字](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text)字串中找到的拼字和文法錯誤。 `token` 欄位包含要被取代的文字。 您可以在 `offset` 欄位中使用以零為起始的位移，尋找 `text` 字串中的權杖。 然後，您應將該位置的文字取代為 `suggestion` 欄位中的文字。 

如果 `type` 欄位為 RepeatedToken，您仍應將權杖取代為 `suggestion`，但您也可能需要移除尾端的空格。

## <a name="throttling-requests"></a>節流要求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>後續步驟

- [什麼是 Bing 拼字檢查 API？](../overview.md)
- [Bing 拼字檢查 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
