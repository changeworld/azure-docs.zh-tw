---
title: 將分析新增至 Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: Bing 統計資料可為 Bing 影像搜尋 API 提供分析。 Analytics 包括呼叫量、熱門查詢字串、地理分佈等等。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "68882790"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>將分析新增至 Bing 搜尋 Api

Bing 統計資料可為 Bing 搜尋 Api 提供分析。 這些分析包括呼叫量、熱門查詢字串、地理分佈等等。 您可以流覽至您的 Azure 資源，然後按一下 [**啟用 Bing 統計資料]**，以在[Azure 入口網站](https://ms.portal.azure.com)中啟用 Bing 統計資料。

> [!IMPORTANT]
> * Bing 統計資料不適用於免費試用訂用帳戶，或免費`F0`定價層的資源。
> * 您可能無法使用透過 Bing 統計資料儀表板提供的任何資料，來建立向第三方散發的應用程式。
> * 啟用 Bing 統計資料會稍微增加您的訂用帳戶速率。 如需詳細資訊，請參閱[定價](https://aka.ms/bingstatisticspricing)。


下圖顯示每個 Bing 搜尋 API 端點的可用分析。

![依端點支援矩陣的分佈情況](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>存取您的分析

Bing 會每隔24小時更新一次分析資料，最多可維護13個月的歷程記錄，讓您可以從[分析儀表板](https://bingapistatistics.com)存取。 請確定您已使用用來註冊 Bing 統計資料的相同 Microsoft 帳戶（MSA）登入。

> [!NOTE]  
> * 計量最多可能需要 24 小時才會呈現在儀表板上。 儀表板會顯示上次更新資料的時間與日期。  
> * 從啟用 Bing 統計資料增益集起，就可以取得計量。

## <a name="filter-the-data"></a>篩選資料

根據預設，圖表和圖表會顯示您有權存取的所有計量和資料。 您可以選取資源、市場、端點，以及感興趣的報告週期，以篩選圖表與圖形中所顯示的資料。 您可以變更下列篩選準則：

- **資源識別碼**：可識別 Azure 訂用帳戶的唯一資源識別碼。 如果您訂閱一個以上的 Bing 搜尋 API 層，此清單會包含多個識別碼。 預設會選取所有資源。  
  
- **市場**：結果來自的市場。 例如，en-US (英文，美國)。 預設會選取所有市場。 `en-WW`市場是 bing 使用的市場，如果電話未指定市場，而且 bing 無法判斷使用者的市場。  
  
- **端點**：Bing 搜尋 API 端點。 此清單包含您有付費訂用帳戶的所有端點。 預設會選取所有端點。  

- **時間範圍**：報告期間。 您可以指定：
  - **全部**：包含最多13個月的資料  
  - **過去24小時**：包含過去24小時內的分析  
  - **過去一周**：包含前七天的分析  
  - 上**個月**：包括過去30天內的分析  
  - **自訂日期範圍**：包含來自指定日期範圍的分析（如果有的話）  

## <a name="charts-and-graphs"></a>圖表和圖形

儀表板會顯示所選端點可用計量的圖表和圖形。 並非所有端點都能取得所有計量。 每個端點的圖表和圖形都是靜態的 (您無法選取要顯示的圖表和圖表)。 儀表板只會顯示有資料可用的圖表和圖形。

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

以下是可能的計量和端點限制。

- **呼叫量**：顯示在報告期間發出的呼叫次數。 如果報告期間為一天，圖表會顯示每小時發出的呼叫次數。 否則，圖表會顯示報告期間內每日發出的呼叫次數。  
  
  > [!NOTE]
  > 呼叫量可能會和帳單報告有所不同，帳單報告通常只包括成功的呼叫。

- **熱門查詢**：顯示報告期間內的熱門查詢，以及每個查詢的發生次數。 您可以設定顯示的查詢數目。 例如，您可以顯示前 25、50 或 75 個查詢。 熱門查詢不適用於下列端點：  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > 某些查詢詞彙可能隱藏起來以移除機密資訊，例如電子郵件、電話號碼、SSN 等。

- **地理分佈**：搜尋結果的來源市場。 例如， `en-us` （英文，美國）。 Bing 使用 `mkt` 查詢參數來判斷市場 (如果指定的話)。 否則，Bing 會使用訊號 (例如呼叫端的 IP 位址) 來判斷市場。

- **回應碼分佈**：在報告期間內所有呼叫的 HTTP 狀態碼。

- **呼叫來源分佈**：使用者使用的瀏覽器類型。 例如，Microsoft Edge、Chrome、Safari 及 FireFox。 從瀏覽器外部（例如 bot、Postman 或從主控台應用程式使用捲曲）所進行的呼叫，會在 [程式庫] 底下分組。 來源是使用要求的使用者代理程式標頭值來判斷的。 如果要求不包含使用者代理程式標頭，Bing 就會嘗試從其他訊號衍生來源。  

- **安全搜尋分佈**：安全搜尋值的分佈。 例如，關閉、中等或嚴格。 `safeSearch` 查詢參數會包含值 (如果指定的話)。 否則，Bing 會預設該值為中等。  

- **要求的散發**套件：您在`responseFilter`查詢參數中要求的 Web 搜尋 API 答案。  

- **傳回的答案分佈**：Web 搜尋 API 在回應中傳回的答案。

- **回應伺服器分佈**：處理 API 要求的應用程式伺服器。 可能的值為 Bing.com (針對從桌上型與膝上型電腦裝置提供的流量) 和 Bing.com-mobile (針對從行動裝置提供的流量)。 伺服器是使用要求的使用者代理程式標頭值來判斷的。 如果要求不包含使用者代理程式標頭，Bing 會嘗試從其他訊號衍生伺服器。

## <a name="next-steps"></a>後續步驟

* [Bing 搜尋 API 是什麼？](bing-api-comparison.md)
* [Bing 搜尋 API 使用和顯示需求](use-display-requirements.md)
