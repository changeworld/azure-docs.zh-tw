---
title: 商業 Marketplace 中合作夥伴中心分析的摘要儀表板
description: 從合作夥伴中心的摘要儀表板，了解如何存取會摘要說明市集活動的彙總資料圖表、趨勢和值。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 6b59da408ae7c88aaf8b9e7423fe955a861edf0f
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565512"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>商業市集分析中的摘要儀表板

本文提供合作夥伴中心內 [摘要] 儀表板的相關資訊。 此儀表板會顯示摘要說明您供應項目市集活動的彙總資料圖表、趨勢和值。

若要存取合作夥伴中心中的摘要儀表板，請在 [ **商業 Marketplace** ] 下選取 [ **[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **摘要** ]。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱 [商業市場分析術語和常見問題](./partner-center-portal/faq-terminology.md)。

## <a name="summary-dashboard"></a>摘要儀表板

摘要儀表板提供 Azure Marketplace 的總覽，並 Microsoft AppSource 提供的商務效能。 儀表板提供下列各項的廣泛總覽：

- 客戶的訂單
- 客戶
- 客戶的供應專案使用方式
- Azure Marketplace 和 AppSource 客戶的頁面造訪

## <a name="elements-of-the-summary-dashboard"></a>摘要儀表板的元素

下列幾節會說明如何使用摘要儀表板以及如何閱讀資料。

### <a name="month-range"></a>月份範圍

您可以在每個頁面的右上角找到月份範圍選取範圍。 選取以過去3、6或12個月為基礎的月份範圍，或選取最大持續時間為12個月的自訂月份範圍，以自訂 **摘要** 頁面圖形的輸出。 預設的月份範圍 (計算期間) 為六個月。

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="說明摘要儀表板上的每月範圍選項。":::

> [!NOTE]
> 視覺效果 widget 和匯出報表中的所有計量都會接受使用者所選取的計算週期。

### <a name="orders-widget"></a>Orders widget

[摘要] * * 儀表板上的 [訂單] 小工具會顯示您所有交易供應專案的目前訂單。 [訂單] widget 會顯示所選計算期間內， (不包括已取消的訂單) 的所有採購訂單的計數和趨勢。 百分比值 **Orders** 代表所選計算期間的成長量。

[![說明 [摘要] 儀表板上的 [訂單] widget。](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


您也可以選取小工具左下角的 [ **訂單儀表板** ] 連結，以移至 [訂單] 報表。

### <a name="customers-widget"></a>客戶 widget

**摘要** 儀表板的 [ **客戶** ] widget 會顯示已為所選計算期間取得供應專案的客戶總數。 [客戶] widget 會顯示作用中 (總數的計數和趨勢，包括新的和現有的) 客戶 (排除所選計算期間的流失客戶) 。 **客戶** 下的百分比值代表所選計算期間的成長量。

[![說明 [摘要] 儀表板上的 [客戶] widget。](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

您也可以在小工具的左下角選取 [ **客戶儀表板** ] 連結，以移至 [詳細客戶] 報表。

### <a name="usage-widget"></a>使用方式 widget

**摘要** 儀表板的 **使用量** 小工具代表所有 AZURE 虛擬機器 (VM) 供應專案的正規化和原始使用時數總計。 使用量 widget 會顯示所選計算期間的總使用時間的計數和趨勢。

[使用量摘要] 資料表會顯示客戶所購買所有供應項目的客戶使用量時數。

- 標準化使用量時數的定義是可負責 VM 核心數目的標準化使用時數 ([VM 核心數目] x [未處理使用時數])。 指定為「SHAREDCORE的 VM」使用 1/6 (或0.1666) 作為 [VM 核心數目] 乘數。
- 未經處理的使用時數會定義為 Vm 的時間量（以小時為單位）。

低於總使用時間的百分比值代表在選取的計算期間內的使用時間成長量。

[![說明 [摘要] 儀表板上的 [使用方法] widget。](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

您也可以在小工具的左下角選取 [ **使用量儀表板** ] 連結，以移至 [使用量] 報表。

### <a name="marketplace-insights"></a>Marketplace 見解

Marketplace 見解會顯示 Azure Marketplace 和 AppSource 中已造訪您供應專案頁面的使用者數目。 **頁面流覽計數** 會顯示商業 marketplace web 分析的摘要，可讓發行者針對商業 marketplace 線上商店上所列的個別產品詳細資料頁面，測量客戶參與： Microsoft AppSource 和 Azure Marketplace。 這個小工具會顯示所選計算期間內頁面造訪總數的計數和趨勢。

[![說明 [摘要] 儀表板上的 [頁面流覽計數] widget。](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

您也可以在小工具的左下角選取 [ **marketplace 見解儀表板** ] 連結，以移至 marketplace 見解報表。

### <a name="geographical-spread"></a>地理散佈

針對選取的計算期間，熱度圖會顯示 [地理位置] 維度的 [客戶]、[訂單] 和 [正規化使用時數] 總數。

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="說明 [摘要] 儀表板上的國家/地區散佈 widget。":::

請注意：

- 您可以移動地圖以檢視確切位置。
- 您可以放大特定位置。
- 熱度圖具有補充方格，可供您查看特定位置的客戶計數、訂單計數和標準化使用時數的詳細資料。
- 您可以在格線中搜尋並選取某個國家/地區，以縮放至地圖中的某個位置。 選取地圖中的 [ **首頁** ] 按鈕，還原為原始的視圖。

> [!TIP]
> 您可以使用任何小工具右上角的下載圖示來下載資料。 您可以藉由選取 [上移] 或 [下移] 圖示來提供每個 widget 的意見反應。

## <a name="next-steps"></a>後續步驟

- 如需商用市集中可用分析報告的總覽，請參閱 [合作夥伴中心中的「存取商用 marketplace 的分析報表](./partner-center-portal/analytics.md)」。
- 如需圖形化和可下載格式的訂單資訊，請參閱[商業市集分析中的訂單儀表板](orders-dashboard.md)。
- 針對虛擬機器 (VM) 供應項目使用量和計量帳單計量，請參閱[商業市集分析中的使用量儀表板](usage-dashboard.md)。
- 如需客戶的詳細資訊 (包括成長趨勢)，請參閱[商業市集分析中的客戶儀表板](customer-dashboard.md)。
- 如需過去 30 天內的下載要求清單，請參閱[商業市集分析中的下載儀表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱 [合作夥伴中心中的評量 & 評論分析儀表板](./partner-center-portal/ratings-reviews.md)。
- 如需有關商業市場分析以及完整的資料詞彙字典的常見問題，請參閱 [商業市場分析術語和常見問題](./partner-center-portal/faq-terminology.md)。
