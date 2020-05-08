---
title: Marketplace 深入解析-Microsoft 商業 marketplace
description: 存取 marketplace web 分析的摘要，可讓您在 Microsoft AppSource 和 Azure Marketplace 中測量客戶參與度。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: cecff7347189c5e5b964e10439297bce405810b7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857917"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>合作夥伴中心的 Marketplace 深入解析儀表板

本文提供合作夥伴中心內 Marketplace 深入解析儀表板的相關資訊。 此儀表板會顯示 marketplace web 分析的摘要，可讓發行者針對 marketplace 店面： Microsoft AppSource 和 Azure Marketplace 上所列的個別產品詳細資料頁面，測量客戶參與。

## <a name="marketplace-insights-dashboard"></a>Marketplace 見解儀表板

若要存取合作夥伴中心的**Marketplace 深入解析儀表板**，請開啟 [商業 Marketplace] 底下的 [**[分析]](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** 索引標籤。

您可以查看下列專案的圖形表示：  

- [Marketplace 深入解析摘要](#marketplace-insights-summary)
- [依地理位置造訪頁面](#page-visits-by-geography)  
- [頁面造訪與獨特的訪客趨勢](#page-visits-versus-unique-visitors-trend)
- [使用 CTAs 呼叫動作與唯一的訪客](#call-to-action-versus-unique-visitors-with-ctas)
- [頁面流覽和依優惠呼叫動作](#page-visits-and-calls-to-action-by-offers)
- [呼叫動作百分比趨勢](#call-to-action-percentage-trend)
- [頁面流覽和參考網域的動作呼叫](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace 深入解析詳細資料表格](#marketplace-insights-details-table)

>[!NOTE]
> 如需分析術語的詳細定義，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。

### <a name="insights-dashboard-layout"></a>Insights 儀表板版面配置

您可以透過各種不同的方式來查看 Marketplace 計量：

- 店面索引標籤
- 頁面篩選
- 日期篩選

**店面**索引標籤：您可以透過 [AppSource & Azure Marketplace] 索引標籤，分別查看供應專案的度量。 從右邊的 [供應專案] 下拉式清單中選取供應專案，以查看所選供應專案的度量視覺效果。 預設會選取所有供應專案。

![合作夥伴中心深入解析儀表板供應專案下拉式清單](./media/insights-offer-dropdown.png)

**深入解析頁面篩選**：這些篩選準則會套用至供應專案（產品詳細資料頁面）層級。 您可以針對想要查看的準則選取多個篩選準則。 此篩選準則適用于整個 Marketplace 深入解析區段，包括圖表和詳細資料。

![合作夥伴中心深入解析儀表板頁面篩選](./media/insights-page-filter.png)

- 供應專案名稱僅針對在選取的日期範圍內具有頁面流覽功能的優惠列出。  
- 針對每個篩選選項，預設選項為 [全部]
- [套用的篩選] 會顯示所做選擇的選取範圍計數。 預設的 [全部] 選取範圍不會顯示套用的篩選。

![已套用合作夥伴中心見解篩選](./media/insights-page-filter-two.png)

**深入解析日期篩選**：此篩選適用于整個 Marketplace 深入解析區段。 篩選可以包含預先決定的日期範圍或自訂的日期範圍。

![合作夥伴中心深入解析日期篩選](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace 深入解析摘要

[Marketplace 深入解析摘要] 區段會顯示**頁面**流覽次數、**動作呼叫**，以及所選日期範圍的**唯一訪客**。

### <a name="page-visits"></a>頁面流覽

此數位代表所選日期範圍的 [供應專案] 頁面（產品詳細資料頁面）上的相異使用者會話計數。 紅色/綠色百分比指標代表頁面流覽的成長百分比。 趨勢圖代表頁面造訪的月數。

### <a name="unique-visitors"></a>唯一的訪客

此數位代表在頁面篩選中選取的供應專案之所選日期範圍內的相異訪客計數。 造訪了一或多個產品詳細資料頁面的訪客，將計為一個唯一的訪客。

### <a name="call-to-action"></a>對動作的呼叫

此數位代表 [供應專案] 頁面（產品詳細資料頁面）上的動作按鈕按一下 [已完成] 的**呼叫**計數。 當您選取 [**立即取得**]、[**免費試用**]、[**聯絡我**] 和 [**試用**產品] 按鈕時，會計算**動作的呼叫**。

![合作夥伴中心深入解析對動作摘要的呼叫](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>依地理位置造訪頁面

下方的熱度圖會顯示**頁面**流覽次數、**動作呼叫**，以及**根據客戶國家/地區的唯一訪客**。 較高的頁面流覽是以較暗的地圖色彩表示，而較低的頁面流覽則是以較淺的地圖色彩表示

![合作夥伴中心深入解析地理分佈](./media/insights-geography.png)

熱度圖包含下列功能：

- 熱度圖有一個補充方格，可查看**頁面**流覽的詳細資料、對**動作的呼叫**，以及特定位置中的**唯一訪客**;如果慣用，您可以放大特定位置。  
- [**國家/地區**] 是指您的客戶在選取的日期範圍內報告頁面造訪的所有國家/地區計數。
- 您可以搜尋並選取方格中的國家/地區，以放大地圖中的位置。 藉由選取地圖上的 [**首頁**]，還原為原始的視圖。

## <a name="page-visits-versus-unique-visitors-trend"></a>頁面造訪與獨特的訪客趨勢

下列資料行代表每月頁面流覽次數，會顯示在 Y 軸上（位於圖表左邊的軸）。 趨勢線代表唯一訪客的每月趨勢，這會顯示在店面： Azure Marketplace 和 AppSource 中發佈的次要 Y 軸（圖表右側的軸）上。

![合作夥伴中心見解頁面造訪與獨特的訪客趨勢](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>使用 CTAs 呼叫動作與唯一的訪客

堆疊資料行代表對動作的每月呼叫（CTA），其依 CTA 類型（**立即取得**、**聯絡我**和**免費試用**）細分，並繪製在 Y 軸上（在頁面左側的軸）。 趨勢線代表具有 CTAs 之唯一訪客的每月趨勢，這會顯示在 [次要 Y 軸] （圖表右側的軸）上，您的供應專案是在 Azure Marketplace 和 AppSource 中發行。

![透過 CTAs 的合作夥伴中心深入解析呼叫動作與獨特的訪客](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>頁面流覽和依優惠呼叫動作

外部圓形圖根據您在 marketplace 中發佈並在篩選中選取的供應專案，呈現**頁面**流覽的明細。 內部圖表代表相同供應專案的**動作分解呼叫**。

![合作夥伴中心深入解析頁面流覽和依優惠呼叫動作](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>呼叫動作百分比趨勢

[**動作百分比] 趨勢的呼叫**會顯示 marketplace 中發佈之供應專案的 CTA 百分比。 CTA% = （CTAs/page 造訪） * 100。

![合作夥伴中心深入解析對動作百分比趨勢的呼叫](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>頁面流覽和參考網域的動作呼叫

下圖顯示前50個參考網域。 選取特定的參考定義域會顯示頁面流覽的每月趨勢，以及向右的圖表呼叫動作。

![合作夥伴中心深入解析頁面流覽和依參考領域和活動的動作呼叫](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace 深入解析詳細資料表格

下表提供頁面流覽的清單，以及您所選供應專案動作的呼叫（依日期排序）。

![合作夥伴中心深入解析詳細資料資料表](./media/insights-details-page.png)

- 如果記錄計數小於1000，則可以將資料解壓縮至 CSV 檔案。
- 如果記錄計數超過1000，匯出的資料會在接下來的30天內，以非同步方式放置在下載頁面中。
- 您可以套用篩選器來顯示您感興趣的資料。 資料可以依供應專案名稱和行銷活動名稱進行篩選。  

## <a name="next-steps"></a>後續步驟

- 如需合作夥伴中心商業 marketplace 中可用分析報告的總覽，請參閱[合作夥伴中心的商用 Marketplace 分析](./analytics.md)。
- 如需摘要說明您供應專案 marketplace 活動之匯總資料的圖形、趨勢和值，請參閱[商業 marketplace 分析中的摘要儀表板](./summary-dashboard.md)。
- 如需以圖形化和可下載格式顯示訂單的相關資訊，請參閱[商用 marketplace 分析中的訂單儀表板](./orders-dashboard.md)。
- 若為虛擬機器（VM）提供使用量和計量付費計量，請參閱[商業 marketplace 分析中的使用量儀表板](./usage-dashboard.md)。
- 如需客戶的詳細資訊，包括成長趨勢，請參閱[商業 marketplace 分析中的客戶儀表板](./customer-dashboard.md)。
- 如需過去30天內的下載要求清單，請參閱[商用 marketplace 分析中的下載儀表板](./downloads-dashboard.md)。
- 若要查看有關 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱[商業 Marketplace 分析中的評等和評論儀表板](./ratings-reviews.md)。
- 如需有關商業 marketplace 分析的常見問題，以及完整的資料詞彙字典，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。
