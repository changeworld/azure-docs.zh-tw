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
ms.openlocfilehash: 5bd5ff0e49b125277255ec8e5c216583d75043a0
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381128"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>在您的應用程式中自動建議 Bing 搜尋字詞

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

若您提供使用者可在其中輸入其搜尋字詞的搜尋方塊，請使用 [Bing 自動建議 API](../bing-autosuggest/get-suggested-search-terms.md) 來改善使用經驗。 API 會根據部分搜尋字詞傳回建議的查詢字串，作為使用者類型。

在使用者輸入搜尋字詞之後，必須在設定 [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) (英文) 查詢參數之前進行 URL 編碼。 例如，如果使用者輸入 *sailing dinghies* ，請將 `q` 設定為 `sailing+dinghies` 或 `sailing%20dinghies`。

若查詢字詞包含拼字錯誤，搜尋回應會包含 [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) (英文) 物件。 物件會顯示 Bing 用於搜尋的原始拼字和已更正的拼字。

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

## <a name="see-also"></a>另請參閱  

* [Bing Web 搜尋 API 參考](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)