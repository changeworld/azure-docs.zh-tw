---
title: Web 分析
description: 此文章提供有關如何學習並使用 Web 分析來有效讓您的業務成長的指示。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416273"
---
<a name="web-analytics"></a>Web 分析
=============

此文章提供有關如何學習並使用 Web 分析來有效讓您的業務成長的指示。 目前此 [見解] 索引標籤可供任何 AppSource 供應項目使用。

既然您已經建置並發行您的供應項目，接下來的步驟是追蹤並測量其成效。 我們隨著 **Web 分析** 加入查看您的每個供應項目在 Marketplace 上之表現的功能。 若要開始進行，請瀏覽到 Cloud Partner 入口網站左邊的 [見解] 頁面，以查看新的 [分析] 索引標籤。

![[Web 分析] 頁面](./media/si-getting-started/WebAnalytics1.png)

您將會看到您發行者識別碼的豐富儀表板，這是使用 Microsoft Power BI 所建置，而且可讓您查看每個供應項目的資料 (每天更新)。

<a name="microsoft-campaigns"></a>**Microsoft 行銷活動**
-----------------------

若要推廣您的供應項目並追蹤其成長，我們已啟用在 Cloud Partner 入口網站上使用 **Microsoft 行銷活動**的功能。 行銷活動是 Marketplace 新支援的功能，可讓您追蹤讓客戶連到您應用程式詳細資料頁面的不同管道。

### <a name="how-to-make-a-campaign"></a>**如何執行行銷活動**

描述行銷活動最簡單的方式是您新增自訂文字/詞彙到可連到您在 Marketplace 中之應用程式詳細資料頁面的 URL。 Google、Bing、LinkedIn 與許多其他網站都鼓勵您建置廣告，從其網站連線到您想要的網站。

一般而言，這些動作的目的是要協助將新客戶吸引到您的產品，而且測量您每個管道之表現是很重要的。 這是行銷活動進入點。

有兩種方式可產生您自己的行銷活動。

1. 將查詢參數 **mktcmpid** 新增到 URL，此 URL 說明行銷活動是什麼，以及這些客戶來自哪些頁面/事件。

例如，您可以使用：```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (進階)：在 URL 中使用我們其中一個支援的一般行銷活動識別碼。 我們想要能適應您需要使用的額外 ref 標記，因此我們支援自動識別這些額外標記的慣例：
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

例如，您可以使用：```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

您可以選擇結合多個這些行銷活動識別碼，以進一步指出為行銷活動帶來流量的多個來源，例如客戶來自何處 (電子郵件、部落格、社交媒體來源等)。

例如：

1. 電子報推薦者：```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. LinkedIn 推薦者：```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**確定行銷活動傳遞到您的所有頁面**

您的行銷活動可能會有中繼頁面，您將流量導向該頁面，然後該頁面接著將客戶導向 Marketplace。 務必將您的初始行銷活動識別碼傳遞到您傳送到 Marketplace 的最終 URL。

範例如下：

1. 行銷員工向 Google 購買廣告以將流量導向公司的登陸頁面 ```https://contoso.com```。 登陸頁面有「嘗試我的產品」連結，該連結會連到 ```https://appsource.com```。
2. 使用者按一下廣告並登陸此公司登陸頁面。
    1.  轉介 URL = google.com
    2.  登陸頁面 URL = ```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. 使用者按一「嘗試我的產品」連結並連到 AppSource。
    1. 轉介 URL =  ```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. 登入頁面網**址 (\_確保此\_URL 已新增到此網址的 utm 市場活動來源和 utm 源**) |```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>如何評估行銷活動是否成功
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**依行銷活動的頁面瀏覽數**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

這是依行銷活動來源的每日頁面瀏覽數細項。

### <a name="conversion-rate-by-campaign"></a>**依行銷活動的轉換率**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

與我們顯示您整個供應項目的轉換率相同，您可以在此圖表中看到您的不同行銷活動成效細項。 此圖表可以協助您找出較高轉換率行銷活動的來源。

### <a name="distribution-by-campaign"></a>**依行銷活動的分佈情況**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

與我們如何看待您客戶的網域相同，此圖表可讓您查看您每個行銷活動資料的分佈情況 (關於使用者如何來到 Marketplace)。 \_NoCampaign 表示當客戶瀏覽到 Marketplace 時，其 URL 中沒有行銷活動識別碼。

<a name="next-steps"></a>**後續步驟**
--------------

既然您已經有能力追蹤您供應項目的成效，我們想要鼓勵您建立您自己的行銷活動。

若您有問題/功能要求，請透過位於右上角的 [意見反應] 來分享。

![Cloud Partner 入口網站中的 [意見反應]](./media/si-getting-started/WebAnalytics5.png)
