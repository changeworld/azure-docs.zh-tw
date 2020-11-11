---
title: 什麼是 Bing 自動建議？
titleSuffix: Azure Cognitive Services
description: Bing 自動建議 API 會根據搜尋方塊中的部分查詢字串，傳回建議的查詢清單。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: a0187a5d2be6b2f93897fc358e26ad095e5a70f7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364576"
---
# <a name="what-is-bing-autosuggest"></a>什麼是 Bing 自動建議？

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

如果應用程式將查詢傳送給任何 Bing 搜尋 API，您就可以使用 Bing 自動建議 API 來改善使用者的搜尋體驗。 Bing 自動建議 API 會根據搜尋方塊中的部分查詢字串，傳回建議的查詢清單。 隨著您在搜尋方塊中輸入字元，下拉式清單中便會顯示建議。

## <a name="bing-autosuggest-api-features"></a>Bing 自動建議 API 功能

| 功能                                                                                                                                                                                 | 描述                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [即時建議搜尋字詞](concepts/get-suggestions.md) | 使用自動建議 API 隨著使用者的輸入顯示建議的搜尋字詞，以改善您的應用程式體驗。 |

## <a name="workflow"></a>工作流程

Bing 自動建議 API 是一種 RESTful Web 服務，可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。

1. 建立具備 Bing 搜尋 API 存取權的[認知服務 API 帳戶](../cognitive-services-apis-create-account.md)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。
2. 在每次使用者於應用程式的搜尋方塊中鍵入新字元時，傳送要求給這個 API。
3. 剖析傳回的 JSON 訊息以處理 API 回應。

一般來說，您會在每次使用者於應用程式的搜尋方塊中鍵入新字元時，呼叫這個 API。 隨著更多字元的輸入，這個 API 會傳回更相關的建議搜尋查詢。 例如，比起 `sail`，API 為單一 `s` 傳回的建議可能會有較低的相關性。

下列範例顯示下拉式搜尋方塊與來自 Bing 自動建議 API 的建議查詢字詞。

![自動建議下拉式搜尋方塊清單](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

當使用者從下拉式清單中選取建議時，您可以使用其中一個 Bing 搜尋 API 以該建議開始進行搜尋，或直接移至 [Bing 搜尋結果] 頁面。

## <a name="next-steps"></a>後續步驟

若要快速開始使用您的第一個要求，請參閱[建立您的第一個查詢](quickstarts/csharp.md)。

請熟悉 [Bing 自動建議 API v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) 參考。 此參考包含端點、標頭、您用來要求建議查詢字詞的查詢參數，以及回應物件定義的清單。

請瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/overview.md)以探索其他可用的 API。


了解如何使用 [Bing Web 搜尋 API](../bing-web-search/overview.md) 來搜尋 Web，以及如何探索其他 [Bing 搜尋 API](../bing-web-search/index.yml)。

務必閱讀 [Bing 使用和顯示需求](../bing-web-search/use-display-requirements.md)，您才不會違反任何有關使用搜尋結果的規則。