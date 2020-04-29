---
title: 商業 Marketplace 分析中的合作夥伴中心訂單儀表板
description: 瞭解如何以圖形化且可下載的格式存取您 marketplace 供應專案訂單的分析報表。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 148b80a6843168b11e4feb3558f6bf08eaeb3ace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261256"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>商業 marketplace 分析中的訂單儀表板

本文提供 [合作夥伴中心] 中 [**訂單] 儀表板**的相關資訊。 此儀表板會以圖形化和可下載的格式顯示訂單的相關資訊。

若要存取合作夥伴中心分析工具中的 [**訂單] 儀表板**，請開啟 [商業 Marketplace] 底下的 [**[分析] 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。

## <a name="orders-dashboard"></a>訂單儀表板

[**分析**] 功能表的 [**訂單] 儀表板**會顯示所有 SaaS 供應專案的目前訂單。 您可以查看下列專案的圖形表示：

- [訂單摘要](#order-summary)
- [依地理位置的訂單](#orders-by-geography)
- [依供應專案排序](#orders-by-offers)
- [每個網站的訂單趨勢與每一基座](#orders-trend-per-site-versus-per-seat)
- [依 Sku 的訂單](#orders-by-skus)
- [訂單和基座趨勢](#orders-and-seats-trend)
- [Order details 資料表](#order-details-table)

> [!NOTE]
> 在 [Cloud Partner 入口網站（CPP）] 和 [合作夥伴中心] 的新商業 marketplace 方案中，分析報表的顯示方式有所差異。 其中一個特定的方式是 CPP 中的**賣方深入**解析具有 [**訂單 & 使用量**] 索引標籤，其中會顯示以使用量為基礎的供應專案和非使用量型供應專案的資料。 在合作夥伴中心，[**訂單**] 頁面具有 SaaS 供應專案的個別索引標籤。

## <a name="order-dashboard-details"></a>訂購儀表板詳細資料

本節將更詳細地說明分析報告。

### <a name="order-summary"></a>訂單摘要

[訂單摘要] 區段會顯示所有採購訂單的計數（不包括已取消的訂單）、已取消的訂單和基座。

[總訂單] 旁的百分比值代表所選日期範圍的成長量。

![合作夥伴中心分析訂單摘要](./media/order-summary.png)

- 向上指的綠色三角形表示正成長趨勢。
- 向下的紅色三角形表示相對於上個月的負面成長趨勢。
- 成長趨勢會以微橫條圖表示。 您可以將滑鼠停留在圖表內的資料行上方，以顯示每個月的值。
- 已取消的訂單是先前購買，然後在選取的日期範圍內取消的訂單計數。
- 基座是在選取的日期範圍內建立的基座計數。

### <a name="orders-by-geography"></a>依地理位置的訂單

[**依地理位置的訂單**] 熱度圖會顯示您在世界地圖上的訂單計數，並顯示根據客戶國家/地區所對應的基座。 此熱度圖的運作方式與**[客戶的地理位置熱度圖](./customer-dashboard.md#customer-by-geography)** 相同。

![合作夥伴中心依地理位置分析訂單](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>依供應專案排序

[**訂單依據] 提供**的環圈圖會根據供應專案名稱來組織訂單（包括已取消的訂單）。

- 主要的供應專案會顯示在圖形中，其餘的供應專案則會群組為「全部 Rest」。
- 您可以選取圖例中的特定供應專案，只顯示圖形中的供應專案。
- 將滑鼠停留在圖形中的配量時，會顯示與所有供應專案的訂單總數相比的訂單數目和百分比。
- Order **by 提供的趨勢**會顯示每月的成長趨勢。 月份資料行代表依供應專案名稱排列的訂單數目。 折線圖會顯示在 Z 軸上繪製的成長百分比趨勢。
- 您可以使用圖表頂端的滑杆，沿著 X 軸向右和向左滾動，並將焦點放在特定資料點。
- 您可以選取圖例上的特定專案來顯示趨勢圖表。
- 您也可以選擇顯示**已取消訂單**的趨勢和資料。 圖形的運作方式與**orders by**供應專案圖表相同。

### <a name="orders-trend-per-site-versus-per-seat"></a>每個網站的訂單趨勢與每一基座

[**每個網站與每個**基座間的環圈圖] 表示客戶購買的每個網站 saas 和每個基座 saas 訂單的細目（此圖表包含已取消的訂單）。 直條圖代表客戶購買的每個網站 SaaS 和每一基座 SaaS 訂單的趨勢（此圖表包含已取消的訂單）。

### <a name="orders-by-skus"></a>依 Sku 的訂單

[**依 Sku 排序**] 圖表代表您所有供應專案的庫存單位（SKU）層級的訂單趨勢（這包括已取消的訂單）。 環圈圖代表前五個 SKU 訂單的明細，而直條圖代表前五個 Sku 的訂單趨勢。

### <a name="orders-and-seats-trend"></a>訂單和基座趨勢

[**訂單] 和**[基座] 趨勢圖會以最高的訂單數顯示前50個供應專案。 這些會顯示在領導者面板上，並依照最高的訂單計數和訂單百分比來排序。

- **依 Sku 排序**：選取供應專案以查看圖表中前五個 sku 的訂單計數明細。
- **依 sku**的基座：前五個 sku 的每月基座趨勢。 如果您選取的供應專案不是每個基座供應專案，您將不會在此區域圖中看到任何資料。

### <a name="canceled-orders-by-offers"></a>依優惠的已取消訂單

[**已取消的訂單依據] 提供**圓形圖，會根據其供應專案名稱來組織所有已取消的訂單。 主要的供應專案會顯示在圖形中，其餘的供應專案則會分組為「全部 Rest」。 您可以在圖例中選取要顯示在圖形中的特定供應專案。

- 將滑鼠停留在圖形中的配量時，會顯示與所有供應專案的訂單總數相比，所選供應專案的訂單數目和百分比。
- 直條圖會顯示每月的趨勢。 資料行代表依供應專案名稱的已取消訂單數目。 您可以使用圖表頂端的滑杆，沿著 X 軸向右和向左滾動，並將焦點放在特定資料點。 您可以選取圖例上的特定專案來顯示趨勢圖表。

### <a name="order-details-table"></a>Order details 資料表

[訂單詳細資料] 資料表會顯示依取得日期排序之1000前訂單的編號清單。

- 方格中的每個資料行都是可排序的。
- 如果記錄計數小於1000，則可以將資料解壓縮至 TSV 檔案。
- 如果記錄數目超過1000，匯出的資料會在接下來的30天內，以非同步方式放置在下載頁面中。
- 篩選可以套用至**Order details 資料表**，只顯示您感興趣的資料。 資料可以依國家/地區、Azure 授權類型、Marketplace 授權類型、供應專案類型、訂單狀態、免費記錄、Marketplace 訂用帳戶識別碼、客戶識別碼和公司名稱進行篩選。

#### <a name="orders-page-filters"></a>訂單頁面篩選

這些篩選器會在頁面層級套用。

您可以選取多個篩選器來呈現您選擇要查看的準則圖表，以及要在**詳細訂單資料**方格/匯出中顯示的資料。 篩選會針對您在 [訂單] 頁面右上角選取的資料範圍，套用到解壓縮的資料上。

- 供應專案類型和供應專案名稱只會在選取的日期範圍內，針對您擁有訂單的優惠列出。 清單中的供應專案名稱會針對您在清單中選取的供應專案類型顯示。
- [套用的篩選] 會在每個選取的篩選器中顯示每個選取範圍內的計量總計。 選擇預設選取專案時，不會顯示套用的篩選。
- 如果已針對其中一個下拉式清單選取 [**全部**]，則會匯總所選取頁面中的所有計量。 例如： [供應專案類型] 篩選選項中的 [全部] 表示已選取所有供應專案類型。 這是下拉式清單的預設選項。 選取 [**全部**] 時，[套用的篩選] 顯示不會顯示任何專案。
- **多重值選取**：頁面中的所有度量都會針對下拉式清單下的所有選取專案進行匯總。 如果已進行多項選擇，套用的篩選會顯示所有選取專案的計數。 請參閱下圖以取得參考。

    ![合作夥伴中心分析具有套用至篩選之多個值的順序](./media/filters-applied.png)

- **單一值選取**：如果選取了某個值，套用的篩選會顯示一個已選取的篩選器計數。 如需參考，請參閱下方影像。

     ![合作夥伴中心使用套用至篩選的單一值來分析訂單](./media/filters-applied-single.png)

## <a name="next-steps"></a>後續步驟

- 如需合作夥伴中心商業 marketplace 中可用分析報告的總覽，請參閱[合作夥伴中心的商用 Marketplace 分析](./analytics.md)。
- 如需摘要說明您供應專案 marketplace 活動之匯總資料的圖形、趨勢和值，請參閱[商業 marketplace 分析中的摘要儀表板](./summary-dashboard.md)。
- 若為虛擬機器（VM）提供使用量和計量付費計量，請參閱[商業 marketplace 分析中的使用量儀表板](./usage-dashboard.md)。
- 如需客戶的詳細資訊，包括成長趨勢，請參閱[商業 marketplace 分析中的客戶儀表板](./customer-dashboard.md)。
- 如需過去30天內的下載要求清單，請參閱[商用 marketplace 分析中的下載儀表板](./downloads-dashboard.md)。
- 若要查看有關 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱[商業 Marketplace 分析中的評等和評論儀表板](./ratings-reviews.md)。
- 如需有關商業 marketplace 分析的常見問題，以及完整的資料詞彙字典，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。
