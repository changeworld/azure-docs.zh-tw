---
title: 商業 Marketplace 中合作夥伴中心分析的摘要儀表板
description: 瞭解如何從合作夥伴中心的摘要儀表板，存取匯總 marketplace 活動的圖形、趨勢和匯總資料值。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262395"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>商業 marketplace 分析中的摘要儀表板

本文提供 [合作夥伴中心] 中 [摘要] 儀表板的相關資訊。 此儀表板會針對您的供應專案，顯示摘要說明 marketplace 活動的圖表、趨勢和匯總資料值。

若要存取摘要儀表板，請開啟 [商業 Marketplace] 底下的 [**[分析] 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。

## <a name="summary-dashboard"></a>摘要儀表板

**摘要**儀表板會根據每個供應專案類型提供總覽。 **深入**解析會快速顯示重要資訊，並提供您的供應專案銷售活動的廣泛觀點。 您可以使用 [**摘要**] 儀表板將這些報表視覺化。 本文將深入探討下列每個元素的詳細資料：

- [日期範圍](#date-range)
- [摘要區段](#summary-section)
- [依地理位置的客戶](#customers-by-geography)
- [成長趨勢圖表](#growth-trend-charts)
- [客戶排行榜](#customer-leaderboard)
- [基座計數趨勢](#seat-count-trend)
- [免費試用 SaaS 訂單趨勢](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>摘要儀表板的元素

下列各節說明如何使用 [摘要] 儀表板，以及如何讀取資料。

### <a name="date-range"></a>日期範圍

您可以在每個頁面的右上角找到 [日期範圍] 選項。 選取以過去3、6或12個月為基礎的日期範圍，或選取最長持續時間為12個月的自訂日期範圍，以自訂**摘要**頁面圖形的輸出。 預設的日期範圍是六個月。

![合作夥伴中心分析儀表板](./media/analyze-dashboard.png)

### <a name="summary-section"></a>摘要區段

[摘要] 區段會顯示所選日期範圍內所建立之所有訂單的計數、取得的客戶和使用量。 部分當月會從這些計量的計算中排除。 例如：如果您已選取 [6M] 時間範圍，則會計算當月前六個月的使用時數。 如果選取了自訂日期範圍，計算中將會排除從當月起的部分金額。

![摘要儀表板中的成長趨勢](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>讀取摘要區段

- **訂單**：您目前已發佈之供應專案的所有採購訂單計數（排除已取消的訂單）。
- **客戶**：購買您的供應專案且至少有一個未取消訂單的所有客戶計數。
- **標準化的使用時間**：定義為以正規化的使用時數，以考慮 vm 核心數（[vm 核心數目] x [小時的原始使用量]）。 指定為 "SHAREDCORE" 的 Vm 會使用1/6 （或0.1666）作為 [VM 核心數目] 乘數。
- **基本使用時間**： vm 以小時為單位執行的時間量。 [**訂單總計**]、[**客戶總數**]、[**正規化使用時間**]、[**原始使用量小時**]、[**頁面**流覽] 和 [**動作呼叫**] 旁的百分比值代表所選日期範圍的使用量成長量（[上個月使用量–第一個月使用量]）/第一個月使用量）。 如上所述，目前月份的部分數量將會從此計量中排除。
- **成長趨勢**：如果您將滑鼠停留在圖表的資料行上，橫條圖會顯示每個月的值。
- **向上指的綠色三角形**：表示正成長趨勢。
- **指向向下的紅色三角形**：表示相對於上個月的負面成長趨勢。

### <a name="customers-by-geography"></a>依地理位置的客戶

[**依地理位置的客戶**] 熱度圖會在世界地圖上顯示客戶計數。

![在摘要儀表板中依地理位置的客戶](./media/summary-customers-by-geography.png)

- 您可以移動地圖以查看確切的位置。
- 您可以放大特定位置。
- 熱度圖有一個補充方格，可在特定位置查看客戶計數、訂單計數、正規化使用時數的詳細資料。
- 您可以搜尋並選取方格中的國家/地區，以縮放至地圖中的位置。 藉由按下地圖中的 [**首頁**] 按鈕，還原為原始的視圖。
- **新**客戶在選取的日期範圍內，第一次在當月購買了其中一個供應專案。

### <a name="growth-trend-charts"></a>成長趨勢圖表

您可以根據所**購買訂單**的成長（包括已取消的訂單）、已**取得的客戶**（包括遺失的客戶）和 [回報的**使用量**] 來查看趨勢（根據選取的日期範圍，依月份顯示月份）。 您可以選取圖表下方的連結，以進一步分析這些趨勢，流覽至個別的**訂單**、**使用量**、**客戶**或**Marketplace 深入**解析頁面。

Marketplace 供應專案**頁面流覽和呼叫動作**趨勢圖表會顯示在兩個索引標籤上的 Azure Marketplace 和 AppSource。

![頁面流覽和呼叫動作的摘要儀表板中的趨勢圖表](./media/summary-page-visits-and-cta.png)

[**依**供應專案的訂單] 圖表會根據供應專案名稱來組織您的訂單。

[**依銷售**通路的訂單] 圓形圖會在選取的日期範圍內，依據銷售通路來組織您的訂單（包括客戶所取消的訂單）。 「銷售通路」是客戶用來購買 Azure 的授權合約類型，這是雲端解決方案提供者（CSP）、企業版、透過轉銷商、轉型和隨用隨付。

**依**銷售通路的供應專案和**使用**方式圓形圖，會分別呈現依頂尖供應專案和銷售通路的使用量明細。 內部圓形圖代表原始使用量，而外部圓形圖則代表正規化的使用方式。

[Order **by marketplace 授權類型**] 和 [**依 marketplace 授權類型的使用量**] 圓形圖會依其各自的授權類型顯示訂單和使用量的細目。 授權類型包括：

- **透過 Azure 計費**：當您選擇透過此授權類型向 Microsoft 銷售您的供應專案時，Microsoft 會代表您帳單給客戶。 付款類型包含透過信用卡或企業發票的隨用隨付。
- 自備**授權**： Microsoft 不會向客戶收取這類 marketplace 供應專案的使用量。 此使用量在 marketplace 中列為 [**立即取得] （免費）** 。
- **免費**： Microsoft 不會向客戶收取這類 marketplace 供應專案的使用量。 此使用量在 marketplace 中列為**免費試用版**。
- **Microsoft as 轉銷商**：代表 microsoft 轉售商在**雲端解決方案提供者（CSP）計畫**中銷售的優惠。

### <a name="customer-leaderboard"></a>客戶排行榜

具有最高訂單數目的前50名客戶會顯示在*領導者面板*上，並以最高的訂單計數和訂單百分比來排序。

- 選取客戶以查看其設定檔詳細資料、依供應專案組織的訂單，或依 Azure 授權類型和定價通道組織的訂單。
- [**依訂單提供的產品**] 環圈圖會顯示前四個供應專案（依訂單計數）和剩餘的供應專案（以「Rest 全部」分組）。
- [**依供應專案的正規化使用量**] 環圈圖會以使用量呈現前五個優惠。

> [!NOTE]
> 只有在客戶已提供同意時，才會顯示客戶個人資訊。 如果您已使用**擁有**者角色許可權層級登入，就可以查看這項資訊。 具有「**參與者**」角色的使用者將無法再看到此資訊。 [深入瞭解使用者角色和許可權](./manage-account.md#define-user-roles-and-permissions)。

### <a name="seat-count-trend"></a>基座計數趨勢

[**每個基座/每個網站的訂單**] 圖表會顯示根據定價模式購買的所有訂單明細。 [基座**計數] 趨勢**圖表會顯示每個基座軟體即服務（SaaS）供應專案所購買的基座和訂單。

### <a name="free-trials-saas-orders-trend"></a>免費試用 SaaS 訂單趨勢

**免費試用 saas 訂單趨勢**圖表會以30天的試用期，顯示免費試用 saas 供應專案的訂單趨勢。

## <a name="next-steps"></a>後續步驟

- 如需合作夥伴中心商業 marketplace 中可用分析報告的總覽，請參閱[合作夥伴中心的商用 Marketplace 分析](./analytics.md)。
- 如需以圖形化和可下載格式顯示訂單的相關資訊，請參閱[商用 marketplace 分析中的訂單儀表板](./orders-dashboard.md)。
- 若為虛擬機器（VM）提供使用量和計量付費計量，請參閱[商業 marketplace 分析中的使用量儀表板](./usage-dashboard.md)。
- 如需客戶的詳細資訊，包括成長趨勢，請參閱[商業 marketplace 分析中的客戶儀表板](./customer-dashboard.md)。
- 如需過去30天內的下載要求清單，請參閱[商用 marketplace 分析中的下載儀表板](./downloads-dashboard.md)。
- 若要查看有關 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱[商業 Marketplace 分析中的評等和評論儀表板](./ratings-reviews.md)。
- 如需有關商業 marketplace 分析的常見問題，以及完整的資料詞彙字典，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。
