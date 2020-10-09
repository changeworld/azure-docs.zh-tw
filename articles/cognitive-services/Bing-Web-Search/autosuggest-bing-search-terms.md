---
title: 自動建議搜尋字詞 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: 將 Bing Web 搜尋 API 與 Bing 自動建議 API 配對，為使用者提供增強的搜尋體驗。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "66384868"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>在您的應用程式中自動建議 Bing 搜尋字詞

若您提供使用者可在其中輸入其搜尋字詞的搜尋方塊，請使用 [Bing 自動建議 API](../bing-autosuggest/get-suggested-search-terms.md) 來改善使用經驗。 API 會根據部分搜尋字詞傳回建議的查詢字串，作為使用者類型。

在使用者輸入搜尋字詞之後，必須在設定 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) (英文) 查詢參數之前進行 URL 編碼。 例如，如果使用者輸入 *sailing dinghies*，請將 `q` 設定為 `sailing+dinghies` 或 `sailing%20dinghies`。

若查詢字詞包含拼字錯誤，搜尋回應會包含 [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) (英文) 物件。 物件會顯示 Bing 用於搜尋的原始拼字和已更正的拼字。

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

您可以使用此資訊讓使用者知道，您在顯示搜尋結果時已修改他們的查詢字串。

![查詢內容 UX 範例](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>後續步驟  

* 檢閱範例 [Bing Web 搜尋 API 回應](search-responses.md)。  

## <a name="see-also"></a>請參閱  

* [Bing Web 搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
