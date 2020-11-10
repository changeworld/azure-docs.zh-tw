---
title: 商業市集分析中的 Marketplace 深入解析儀表板
description: 存取合作夥伴中心中 marketplace web 分析的摘要，可讓您在 Microsoft AppSource 和 Azure Marketplace 中測量客戶參與度。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 479e42c2ea48ea56c0b2dd70752a3b1a330f7969
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414514"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>商業市集分析中的 Marketplace 深入解析儀表板

本文提供合作夥伴中心內 [Marketplace 見解] 儀表板的相關資訊。 此儀表板會顯示商業 marketplace web 分析的摘要，可讓發行者針對商業 marketplace 線上商店中所列的個別產品詳細資料頁面，測量客戶參與： Microsoft AppSource 和 Azure Marketplace。

若要存取合作夥伴中心中的 **Marketplace 見解** 儀表板，請在 [商業 Marketplace] 下，選取 [ **[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **marketplace 見解** ]。

如需分析術語的詳細定義，請參閱 [商業市場分析術語和常見問題](./partner-center-portal/faq-terminology.md)。

## <a name="marketplace-insights-dashboard"></a>Marketplace 見解儀表板

Marketplace 見解儀表板概述 Azure Marketplace 和 AppSource 提供的商務效能。 此儀表板提供下列各項的廣泛總覽：

- 頁面造訪趨勢
- 呼叫動作趨勢
- 頁面流覽和對供應專案、參考網域和活動識別碼的動作呼叫
- 依地理位置的 Marketplace 見解
- Marketplace 見解的詳細資料資料表

Marketplace 見解儀表板提供點選流資料，不應與潛在客戶目的地端點中產生的潛在客戶相互關聯。

> [!NOTE]
> 在合作夥伴中心中，使用者造訪 Azure Marketplace 或 AppSource 和報告之間的最大延遲為48小時。

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Marketplace 見解儀表板的元素

Marketplace 見解儀表板會在兩個不同的索引標籤中顯示 Azure Marketplace 和 AppSource 的 web 遙測詳細資料。 下列各節說明如何使用 Marketplace 見解儀表板，以及如何讀取資料。

### <a name="month-range"></a>月份範圍

您可以在每個頁面的右上角找到月份範圍選取範圍。 選取以過去6個月或12個月為基礎的月份範圍，或選取最大持續時間為12個月的自訂月份範圍，以自訂 **Marketplace 見解** 頁面圖形的輸出。 預設的月份範圍 (計算期間) 為六個月。

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="說明 Marketplace 見解儀表板上的月篩選。":::

> [!NOTE]
> 視覺效果 widget 和匯出報表中的所有計量都會接受使用者所選取的計算週期。

### <a name="page-visits-trends"></a>頁面造訪趨勢

「Marketplace 見解 **訪客** 」圖表會顯示所選計算期間的 _頁面造訪_ 和 _唯一訪客_ 計數。

**頁面** 流覽：此數位代表在 [供應專案清單] 頁面上的相異使用者會話計數， (產品詳細資料頁面) 選取的計算期間。 紅色和綠色百分比指標表示頁面流覽的成長百分比。 趨勢圖代表每月的頁面瀏覽數計數。

**唯一的訪客** ：此數位代表在 Azure Marketplace 和 AppSource 的供應專案 () 所選取計算期間內的相異訪客計數。 瀏覽了一或多個產品詳細資料頁面的訪客，將計為一位非重複訪客。

[![說明 Marketplace 見解儀表板上的訪客圖表。](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>呼叫動作趨勢

此數位代表 [供應專案清單] 頁面上的 [ **動作** ] 按鈕按一下 [已完成] 的計數 (產品詳細資料頁面) 。 當使用者選取 [ **立即取得** ]、[ **免費試用** ]、[ **連絡人** **] 或 [試用** 產品] 按鈕時，會計算 _動作的呼叫_ 。

[![說明在 Marketplace 見解儀表板上對動作圖表的呼叫。](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>頁面流覽和對供應專案、參考網域和活動識別碼的動作呼叫

**參考定義域** ：選取特定的參考定義域會顯示頁面流覽的每月趨勢，以及對右邊圖表的動作呼叫。

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="說明 Marketplace 見解儀表板上的參考定義域圖表。":::

供應 **專案：選取** 特定的供應專案，以查看頁面流覽的每月趨勢，以及對右邊圖表的動作呼叫。

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="說明 Marketplace 見解儀表板上的供應專案別名圖表。":::

**活動識別碼** ：選取特定的活動識別碼，您應該能夠瞭解行銷活動的成功與否。 針對每個活動，您應該能夠看到頁面流覽的每月趨勢，以及對右邊圖表的動作呼叫。

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="說明 Marketplace 見解儀表板上的行銷活動圖表。":::

### <a name="marketplace-insights-by-geography"></a>依地理位置的 Marketplace 見解

針對選取的計算期間，熱度圖會顯示頁面流覽次數、唯一的訪客，以及動作 (CTA) 的呼叫。 地圖上的淺色至深色色彩代表唯一訪客的低至高值。 選取資料表中的一筆記錄，以放大國家/地區。

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="說明 Marketplace 見解儀表板上的地理分散圖。":::

注意下列事項：

- 您可以移動地圖以檢視確切位置。
- 您可以放大特定位置。
- 熱度圖具有補充方格，可在特定位置查看客戶計數、訂單計數和標準化使用時數的詳細資料。
- 您可以在格線中搜尋並選取某個國家/地區，以縮放至地圖中的某個位置。 選取地圖中的 [ **首頁** ] 按鈕，還原為原始的視圖。

### <a name="marketplace-insights-details-table"></a>Marketplace 見解的詳細資料資料表

下表提供頁面流覽的清單視圖，以及您所選取供應專案依日期排序之頁面的動作呼叫。

- 資料可以解壓縮至。TSV 或。如果記錄計數小於1000，則為 CSV 檔案。
- 如果記錄計數超過1000，匯出的資料會以非同步方式放在未來30天的 [下載] 頁面中。
- 依供應專案名稱和活動名稱篩選資料，以顯示您感興趣的資料。

> [!TIP]
> 您可以使用任何小工具右上角的下載圖示來下載資料。 您可以按一下 [向上] 或 [下移] 圖示，以提供每個 widget 的意見反應。

## <a name="next-steps"></a>後續步驟

- 如需商用市集中可用分析報告的總覽，請參閱 [合作夥伴中心中的「存取商用 marketplace 的分析報表](./partner-center-portal/analytics.md)」。
- 如需以圖形和可下載格式顯示訂單的詳細資訊，請參閱 [商用 marketplace 分析中的訂單儀表板](./orders-dashboard.md)。
- 針對 (VM) 提供使用量和計量付費計量的虛擬機器，請參閱 [商用 marketplace 分析中的使用量儀表板](./usage-dashboard.md)。
- 如需客戶的詳細資訊 (包括成長趨勢)，請參閱[商業市集分析中的客戶儀表板](./customer-dashboard.md)。
- 如需過去 30 天內的下載要求清單，請參閱[商業市集分析中的下載儀表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱 [合作夥伴中心中的評量 & 評論分析儀表板](./partner-center-portal/ratings-reviews.md)。
- 如需有關商業市場分析以及完整的資料詞彙字典的常見問題，請參閱 [商業市場分析術語和常見問題](./partner-center-portal/faq-terminology.md)。
