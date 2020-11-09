---
title: Bing Web 搜尋 SDK 範例
titleSuffix: Azure Cognitive Services
description: 使用 Bing Web 搜尋 SDK 將搜尋功能新增至您的 Python、Node.js、C# 或 Java 應用程式。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: b31335df7c1dc48f82699aa3676561721474071d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076209"
---
# <a name="bing-web-search-sdk-samples"></a>Bing Web 搜尋 SDK 範例

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照[這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing Web 搜尋 SDK 可在 Python、Node.js、C# 和 Java 中使用。 GitHub 上有提供程式碼範例、必要條件和建置指示。 包含下列案例：

* 查詢單一字組，並輸出第一個網頁、影像、新聞文章和影片結果的名稱和 URL。
* 查詢片語，並驗證結果數目，然後輸出第一個結果的名稱和 URL。
* 在回應篩選設定為 `news` 的情況下查詢搜尋字詞，並輸出新聞結果的詳細資料。
* 使用 `answerCount` 和 `promote` 參數查詢搜尋字詞，然後輸出結果的詳細資料。

## <a name="sdks-and-libraries"></a>SDK 和程式庫

使用下列連結來存取適用於您慣用語言的 SDK。

* 開始使用 [Python 範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * 另請參閱 [Python 程式庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) ，以了解定義和相依性。
* 開始使用 [Node.js 範例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * 另請參閱 [Node.js Web 搜尋](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch)。
* 開始使用 [.NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * 另請參閱 [.NET 程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch)，以了解定義和相依性。
* 開始使用 [Java 範例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * 另請參閱 [Java 程式庫](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) ，以了解定義和相依性。
