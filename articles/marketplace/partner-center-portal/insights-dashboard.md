---
title: Marketplace 見解-Microsoft 商業 marketplace、Microsoft AppSource 和 Azure Marketplace
description: 存取市集 Web 分析的摘要，其可供測量 Microsoft AppSource 和 Azure Marketplace 中的客戶參與度。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1a645a333db9b24005639f4adbb2913a2b887b66
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055663"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>合作夥伴中心的 [Marketplace 見解] 儀表板

本文提供合作夥伴中心內 [Marketplace 見解] 儀表板的相關資訊。 此儀表板會顯示 marketplace web 分析的摘要，可讓發行者針對商業 marketplace 線上商店上所列的個別產品詳細資料頁面，測量客戶參與： Microsoft AppSource 和 Azure Marketplace。

## <a name="marketplace-insights-dashboard"></a>Marketplace 見解儀表板

若要存取合作夥伴中心內的 [Marketplace 見解] 儀表板，請在 [商業市集] 下開啟 [[分析] 索引標籤](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)。

您可檢視下列項目的圖形表示：  

- [Marketplace 見解的摘要](#marketplace-insights-summary)
- [頁面瀏覽數 (依地理位置)](#page-visits-by-geography)  
- [頁面瀏覽數與非重複訪客數趨勢](#page-visits-versus-unique-visitors-trend)
- [使用 CTAs (CTA) 與唯一訪客的呼叫動作](#call-to-action-versus-unique-visitors-with-ctas)
- [頁面瀏覽數與動作呼叫 (依供應項目)](#page-visits-and-calls-to-action-by-offers)
- [動作呼叫百分比趨勢](#call-to-action-percentage-trend)
- [頁面瀏覽數與動作呼叫數 (依轉介網域)](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace 見解的詳細資料資料表](#marketplace-insights-details-table)

在合作夥伴中心中，使用者造訪 Azure Marketplace 或 AppSource 和報告之間的最大延遲為48小時。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱[商業市集分析的常見問題集與術語](./faq-terminology.md)。

### <a name="insights-dashboard-layout"></a>[見解] 儀表板版面配置

以各種不同的方式觀看商業市場計量：

- 線上商店索引標籤
- 頁面篩選
- 日期篩選

[**線上商店]** 索引標籤：您可以透過 [AppSource &] Azure Marketplace 索引標籤，分別查看供應專案的計量。 從右側的 [供應項目] 下拉式清單中選取供應項目，以查看所選供應項目的計量視覺效果。 依預設會選取所有供應項目。

![合作夥伴中心見解儀表板的 [供應項目] 下拉式清單](./media/insights-offer-dropdown.png)

**見解頁面篩選**：這些篩選會套用至供應項目 (產品詳細資料頁面) 層級。 您可針對想要檢視的準則選取多個篩選。 此篩選適用於整個 [Marketplace 見解] 區段，包括圖表和詳細資料。

![合作夥伴中心見解儀表板的頁面篩選](./media/insights-page-filter.png)

- [供應項目名稱] 僅針對在所選日期範圍內具有頁面瀏覽數的供應項目列出。  
- 針對每個篩選選項，預設選取項目為 [全部]
- 所套用篩選會顯示所做選擇的選取項目計數。 預設的 [全部] 選取項目不會顯示所套用篩選。

![合作夥伴中心見解的 [套用的篩選]](./media/insights-page-filter-two.png)

**見解日期篩選**：此篩選適用於整個 [Marketplace 見解] 區段。 篩選可包含預先決定或自訂的日期範圍。

![合作夥伴中心見解的日期篩選](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace 見解的摘要

[Marketplace 見解摘要] 區段會顯示所選日期範圍內的 **頁面**流覽次數、 **動作呼叫**和 **唯一訪客** 。

>[!NOTE]
>Marketplace 見解儀表板提供點選流資料，不應與潛在客戶目的地端點中產生的潛在客戶相互關聯。

### <a name="page-visits"></a>頁面瀏覽數

此數字代表所選日期範圍內，供應項目頁面 (產品詳細資料頁面) 上的相異使用者工作階段計數。 紅色/綠色百分比指標代表頁面瀏覽數的成長百分比。 趨勢圖代表每月的頁面瀏覽數計數。

### <a name="unique-visitors"></a>非重複訪客數

此數字代表頁面篩選中所選供應項目在所選日期範圍期間內的相異訪客計數。 瀏覽了一或多個產品詳細資料頁面的訪客，將計為一位非重複訪客。

### <a name="call-to-action"></a>對動作的呼叫

此數字代表在供應專案頁面 (產品詳細資料頁面) 上，已完成 [動作呼叫] 按鈕點選的計數。 當使用者選取 [**立即取得**]、[**免費試用**]、[**連絡人** **] 或 [試用**產品] 按鈕時，會計算**動作的呼叫**。

![合作夥伴中心見解的動作呼叫摘要](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>頁面瀏覽數 (依地理位置)

下方的熱度圖會顯示 [頁面瀏覽數]、[動作呼叫數] 以及 [根據客戶國家/地區的非重複訪客數] 等計數。 較高的頁面瀏覽數以較暗地圖色彩表示，而較低的頁面瀏覽數則以較淺地圖色彩表示。

![合作夥伴中心見解的地理分佈](./media/insights-geography.png)

熱度圖包含下列功能：

- 熱度圖有一個補充方格，可用來檢視特定位置中的 [頁面瀏覽數]、[動作呼叫數] 及 [非重複訪客數] 等詳細資料；如果想要的話，您可縮放到某個特定位置。  
- [分佈的國家/地區數] 是客戶已在所選日期範圍內回報頁面瀏覽數的所有國家/地區計數。
- 您可在方格中搜尋並選取某個國家/地區，以縮放到地圖中的該位置。 藉由選取地圖上的**首頁**，還原為原始檢視。

## <a name="page-visits-versus-unique-visitors-trend"></a>頁面瀏覽數與非重複訪客數趨勢

下列資料行代表每月頁面瀏覽數，其顯示在 Y 軸 (位於圖表左側的軸) 上。 趨勢線代表唯一訪客的每月趨勢，這會顯示在圖表右邊的次要 Y 軸 (軸) ，適用于您在線上商店中發佈的供應專案： Azure Marketplace 和 AppSource。

![合作夥伴中心見解的頁面瀏覽數與非重複訪客數趨勢](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>動作呼叫與具有 CTA 的非重複訪客數

堆疊資料行代表每月動作呼叫數 (CTA)，其會依 CTA 類型 ([立即取得] **，** 、[與我連絡] 及 [免費試用]) 細分，並繪製在 Y 軸 (位於頁面左側的軸) 上。 趨勢線則代表具有 CTA 的非重複訪客數每月趨勢，這會針對在 Azure Marketplace 和 AppSource 中發佈的供應項目顯示在次要 Y 軸 (位於圖表右側軸) 上。

![合作夥伴中心見解的動作呼叫與具有 CTA 的非重複訪客數](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>頁面瀏覽數與動作呼叫數 (依供應項目)

外部圓形圖根據您在市集中發佈並在篩選中所選供應項目來代表 [頁面瀏覽數] 的明細。 內部圖表則代表相同供應項目的 [動作呼叫數] 明細。

![合作夥伴中心見解的頁面瀏覽數與動作呼叫數 (依供應項目)](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>動作呼叫百分比趨勢

[動作呼叫百分比趨勢] 會呈現市集中所發佈供應項目的 CTA 百分比。 CTA % = (CTA/頁面瀏覽數) * 100。

![合作夥伴中心見解的動作呼叫百分比趨勢](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>頁面瀏覽數與動作呼叫數 (依轉介網域)

下圖呈現前 50 個轉介網域。 選取特定的轉介網域，就會在圖表右側顯示頁面瀏覽數與動作呼叫數的每月趨勢。

![合作夥伴中心見解的頁面瀏覽數與動作呼叫數 (依轉介網域和行銷活動)](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace 見解的詳細資料資料表

下表提供所選供應項目的頁面瀏覽數與動作呼叫數清單檢視 (依日期排序)。

![合作夥伴中心見解的詳細資料資料表](./media/insights-details-page.png)

- 如果記錄計數小於 1000，則可將資料擷取至 CSV 檔案。
- 如果記錄計數超過 1000，則匯出的資料會在接下來 30 天內以非同步方式放置在下載頁面中。
- 依供應專案名稱和活動名稱篩選資料，以顯示您感興趣的資料。

## <a name="next-steps"></a>後續步驟

- 如需合作夥伴中心商業市集中可用的分析報告概觀，請參閱[合作夥伴中心的商業市集分析](./analytics.md)。
- 如需摘要說明供應項目市集活動的彙總資料圖表、趨勢和值，請參閱[商業市集分析中的摘要儀表板](./summary-dashboard.md)。
- 如需圖形化和可下載格式的訂單資訊，請參閱[商業市集分析中的訂單儀表板](./orders-dashboard.md)。
- 針對虛擬機器 (VM) 供應項目使用量和計量帳單計量，請參閱[商業市集分析中的使用量儀表板](./usage-dashboard.md)。
- 如需客戶的詳細資訊 (包括成長趨勢)，請參閱[商業市集分析中的客戶儀表板](./customer-dashboard.md)。
- 如需過去 30 天內的下載要求清單，請參閱[商業市集分析中的下載儀表板](./downloads-dashboard.md)。
- 若要查看 Azure Marketplace 和 AppSource 上客戶對於供應項目的意見反應彙總檢視，請參閱[商業市集分析中的評等和評論儀表板](./ratings-reviews.md)。
- 如需商業市集分析的常見問題集以及完整的資料術語字典，請參閱[商業市集分析的常見問題集與術語](./faq-terminology.md)。
