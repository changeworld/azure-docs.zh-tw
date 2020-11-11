---
title: 什麼是 Bing 圖像式搜尋 API？
titleSuffix: Azure Cognitive Services
description: Bing 圖像式搜尋可提供影像的相關詳細資料或深入解析，例如類似影像或購物來源。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 50db66d54993a4b5ee8afb4756be6b1f4387a1d0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369336"
---
# <a name="what-is-the-bing-visual-search-api"></a>什麼是 Bing 圖像式搜尋 API？

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing 圖像式搜尋 API 會傳回影像的見解。 您可以上傳影像，也可以提供影像的 URL。 見解是視覺上類似的影像、購物來源、包含影像的網頁等。 Bing 圖像式搜尋 API 所傳回的見解，類似於 Bing.com/images 上顯示的內容。 

如果您使用 [Bing 影像搜尋 API](../bing-image-search/overview.md)，則可對 Bing 圖像式搜尋使用來自該 API 搜尋結果的見解權杖，而非上傳影像。

> [!IMPORTANT]
> 如果您使用 Bing 影像搜尋 API 取得影像見解，請考慮切換至 Bing 圖像式搜尋 API，其可提供更完整的見解。

## <a name="insights"></a>深入解析

您可以使用 Bing 圖像式搜尋來探索下列見解：

| 見解                              | 描述 |
|--------------------------------------|-------------|
| 在視覺效果上類似的影像              | 外觀上類似於輸入影像的影像清單。 |
| 在視覺效果上類似的產品            | 外觀上類似於所示產品的產品。            |
| 購物來源                     | 可購買到輸入影像中所含項目的地點清單。            |
| 相關搜尋                     | 由他人建立或根據影像內容而建立的相關搜尋。            |
| 包含影像的網頁     | 包含輸入影像的網頁。            |
| 食譜                              | 一份網頁清單，這些網頁包含用來烹調輸入影像中所含菜餚的食譜。            |
| 實體                             | 知名的人員、地點和事物。 |

除了見解，Bing 圖像式搜尋也會傳回從輸入影像衍生的各種字詞 (亦即標記)。 標記可讓使用者瀏覽影像中出現的概念。 例如，如果輸入影像顯示的是知名運動員，則其中一個標記可能是運動員的名字，另外也可能有「運動」標記。 或者，如果輸入影像顯示的是蘋果派，則可能會有「蘋果派」、「甜派」和「甜點」等標記。

Bing 圖像式搜尋的結果也會包含影像中相關區域的週框。 例如，如果影像包含數個名人，則結果可能會為可辨識的每個名人加上週框。 或者，如果 Bing 在影像中辨識出某項產品或服飾，則結果可能會為可辨識的項目加上週框。

## <a name="workflow"></a>工作流程

Bing 圖像式搜尋 API 是一種 RESTful Web 服務，因此可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。 您可以對此服務使用 REST API 或 SDK。

1. 建立[認知服務帳戶](../cognitive-services-apis-create-account.md)來存取 Bing 搜尋 API。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。
2. 使用有效的搜尋查詢，將要求傳送至 API。
3. 剖析傳回的 JSON 訊息以處理 API 回應。

## <a name="next-steps"></a>後續步驟

首先，觀看 Bing 圖像式搜尋 API 的[互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)。
此示範將說明如何快速地自訂搜尋查詢，並翻找出 Web 上的影像。

若要快速開始使用您的第一個要求，請參閱快速入門：

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>另請參閱

* [影像 - 圖像式搜尋](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)參考會說明您在要求以影像為基礎的搜尋結果時所能使用端點、要求標頭、回應和查詢參數的定義和資訊。

* [Bing 搜尋 API 使用和顯示需求](../bing-web-search/use-display-requirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。

* 請瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/overview.md)以探索其他可用的 API。