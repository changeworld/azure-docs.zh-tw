---
title: 商業 Marketplace 分析中的合作夥伴中心 Orders 儀表板、Microsoft AppSource 和 Azure Marketplace
description: 瞭解如何以圖形和可下載的格式存取您的商業 marketplace 供應專案訂單相關分析報表。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 943f1361a2a9d6bf8d891f8b4f2137b8738f0836
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450761"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>商業市集分析中的訂單儀表板

本文提供合作夥伴中心內訂單儀表板的相關資訊。 此儀表板會顯示訂單的相關資訊，包括以圖形和可下載格式呈現的成長趨勢。

若要存取合作夥伴中心的 [訂單] 儀表板，請在 [**商業 Marketplace**] 下，選取 [**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **訂單**]。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱 [商業市場分析術語和常見問題](./analytics-faq.md)。

## <a name="orders-dashboard"></a>訂單儀表板

[訂單] 儀表板會顯示您的所有軟體即服務 (SaaS) 優惠的目前訂單。 您可檢視下列項目的圖形表示：

- 訂單趨勢
- 每一基座和網站趨勢的訂單
- 依優惠和 Sku 排序的訂單
- 依地理位置排序訂單
- 詳細訂單資料表
- 訂單頁面篩選

> [!NOTE]
> 客戶取得和報告合作夥伴中心之間的最大延遲為48小時。

## <a name="elements-of-the-orders-dashboard"></a>Orders 儀表板的元素

下列各節說明如何使用 Orders 儀表板，以及如何讀取資料。

### <a name="month-range"></a>月份範圍

您可以在每個頁面的右上角找到月份範圍選取範圍。 選取以過去6或12個月為基礎的月份範圍，或選取最大持續時間為12個月的自訂月份範圍，以自訂 [ **訂單** ] 頁面圖形的輸出。 預設的月份範圍 (計算期間) 為六個月。

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="說明 [訂單] 儀表板上的月篩選。":::

> [!NOTE]
> 視覺效果 widget 和匯出報表中的所有計量都會接受使用者所選取的計算週期。

### <a name="orders-trend"></a>訂單趨勢

在本節中，您會發現 [ **Orders** ] 圖表會顯示所選計算期間內的作用中和已取消訂單的趨勢。 計量和成長趨勢會以折線圖表示，並藉由將滑鼠停留在圖表上的線條來顯示每個月的值。 Widget 中 Orders 計量下方的百分比值代表所選計算期間的成長或拒絕量。

有兩個 Orders 計數器：作用 _中和已__取消_。

- 作用 **中等於客戶** 目前在選取的日期範圍內所使用的訂單數目。
- 「**已取消**」等於先前在選取的日期範圍內所購買並取消的訂單數目。

[![說明 [訂單] 儀表板上的 [訂單] 小工具，顯示作用中和已取消訂單的趨勢。](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>依基座和網站趨勢排列的訂單

依基座 **和以網站為基礎** 的折線圖，每一網站 saas 的計量和趨勢，以及客戶所購買的每一基座 saas 訂單， (此圖表包含已取消的訂單) 。

[![說明 [訂單] 儀表板上的 [訂單] 小工具，其會顯示每個基座和網站趨勢的訂單。](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

SaaS 供應專案可以在每個方案中使用兩種定價模型的其中一種：一般費率 (以網站為基礎的) 或依使用者 (基座) 。

- 一般 **費率**：以單一每月或每年的原價費率，啟用對您供應專案的存取。 有時稱為網站型定價。
- **每位使用者**：啟用您供應專案的存取權，其價格取決於可以存取供應專案或佔用基座的使用者數目。 使用這個以使用量為基礎的模型，您可以設定方案支援的最小和最大使用者數目。 您可以建立多個方案，以根據使用者數目來設定不同的價格點。 這些欄位為選擇性。 如果未選取此項，則會將使用者數目視為沒有限制 (的最小值為1，最多可支援您的服務可支援) 。 這些欄位可作為方案更新的一部分進行編輯。
- **計量付費**：以一般費率定價為基礎。 使用此定價模型，您可以選擇性地定義計量方案，以使用 marketplace 計量服務 API 來向客戶收取非固定費率所涵蓋使用量的費用。

如需基座、網站和計量付費計費的詳細資訊，請參閱 [如何為商業市場規劃 SaaS 供應](plan-saas-offer.md)專案。

### <a name="orders-by-offers-and-skus"></a>依優惠和 Sku 排序的訂單

依供應專案和 SKU 的訂單圖表會顯示所有供應專案的量值和趨勢：

- 最高的供應專案會顯示在圖形中，其餘的供應專案則會分組為 **rest**。
- 您可以選取圖例中的特定供應專案，只在圖形中顯示該供應專案和相關聯的 Sku。
- 將滑鼠暫留在圖形中的配量上，會顯示與所有供應專案的訂單總數相較之下，該供應專案的訂單數目和百分比。
- **依供應項目趨勢排序的訂單** 會顯示每月成長趨勢。 月份資料行代表依供應項目名稱排序的訂單數目。 折線圖會顯示在 Z 軸上繪製的成長百分比趨勢。

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="說明 [訂單] 儀表板上的 [依供應專案排列順序] 圖表。":::

您可以選取任何供應專案，以及最多三個 Sku 來查看供應專案、Sku 和基座的月數趨勢。

### <a name="orders-by-geography"></a>依地理位置排序訂單

針對選取的計算期間，熱度圖會顯示訂單的總數目，以及新加入的訂單與地理位置的成長百分比。  地圖上由淺到深色彩代表由低到高的客戶計數值。 選取資料表中的一筆記錄，以放大特定國家或地區。

[![說明 [訂單] 儀表板上的地理分散圖。](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

請注意：

- 您可以移動地圖以檢視確切位置。
- 您可以放大特定位置。
- 熱度圖具有補充方格，可供您查看特定位置的客戶計數、訂單計數和標準化使用時數的詳細資料。
- 您可以在格線中搜尋並選取某個國家/地區，以縮放至地圖中的某個位置。 選取地圖中的 [ **首頁** ] 按鈕，還原為原始的視圖。

### <a name="orders-details-table"></a>Orders 詳細資料表格

[訂單詳細資料] 資料表會顯示依取得日期排序的1000前幾筆訂單的編號清單。

- 方格中的每個資料行皆可排序。
- 資料可以解壓縮至。CSV 或。如果記錄計數小於1000，則為 TSV 檔案。
- 如果記錄數超過1000，匯出的資料會以非同步方式放在未來30天的 [下載] 頁面中。
- 將篩選套用至 [ **訂單詳細** 資料] 資料表，只顯示您感興趣的資料。 依國家/地區、Azure 授權類型、商業 marketplace 授權類型、供應專案類型、訂單狀態、免費線索、商業 marketplace 訂用帳戶識別碼、客戶識別碼和公司名稱進行篩選。
- 因為 SaaS 供應專案是透過 Azure Marketplace 或 Microsoft AppSource 購買，所以不需要 Azure 訂用帳戶，Marketplace 訂用帳戶識別碼會以00000000-0000-0000-0000-000000000000 形式出現在 [ **詳細訂單資料** ] 區段中。
- 當受保護的客戶購買訂單時，會將 [ **訂單詳細資料** ] 中的資訊遮罩 ( * * * * * * * * * * * ) 。

**_表1：資料的字典_* _

| 資料行名稱 | 屬性名稱 | 定義 |
| ------------ | ------------- | ------------- |
| Marketplace 訂用帳戶識別碼 | Marketplace 訂用帳戶識別碼 | 與客戶用來購買商業 marketplace 供應專案的 Azure 訂用帳戶相關聯的唯一識別碼。 識別碼先前是 Azure 訂用帳戶 GUID。 |
| MonthStartDate | 月開始日期 | 月開始日期代表購買月份。 |
| 供應項目類型 | 供應項目類型 | 商業 marketplace 供應專案的類型。 |
| Azure 授權類型 | Azure 授權類型 | 客戶用來購買 Azure 的授權合約類型。 也稱為「通道」。 可能的值包括：<ul><li>雲端解決方案提供者</li><li>企業</li><li>企業透過轉銷商</li><li>隨用隨付</li></ul> |
| Marketplace 授權類型 | Marketplace 授權類型 | 商業 marketplace 供應專案的計費方法。 不同的值如下：<ul><li>透過 Azure 計費</li><li>攜帶您自己的授權</li><li>免費</li><li>Microsoft 成為轉銷商</li></ul> |
| SKU | SKU | 與供應專案相關聯的方案 |
| 客戶國家/地區 | Customer Country/Region | 客戶提供的國家/地區名稱。 國家/地區可能與客戶的 Azure 訂用帳戶中的國家/地區不同。 |
| 是預覽 SKU | 是預覽 SKU | 此值可讓您知道您是否已將 SKU 標記為「預覽」。 如果 SKU 已適當標記，則值為「是」，而且只有您所授權的 Azure 訂用帳戶可以部署和使用此映像。 如果 SKU 尚未識別為「預覽」，則值為「否」。 |
| 訂單識別碼 | 訂單識別碼 | 商業 marketplace 服務客戶訂單的唯一識別碼。 Virtual Machine 用法型供應項目不會與訂單有關聯。 |
| Order Quantity | Order Quantity | 與使用中訂單的訂單識別碼相關聯的資產數目 |
| 雲端執行個體名稱 | 雲端執行個體名稱 | 發生 VM 部署的 Microsoft Cloud。 |
| 是新的客戶 | 是新的客戶 | 此值會識別新客戶第一次是否取得一或多個供應專案。 如果是在「取得日期」的同一個日曆月份內，值會是「是」。 如果客戶已在報告的日曆月份之前購買任何您的供應項目，值將會是「否」。 |
| 訂單狀態 | 訂單狀態 | 上次重新整理資料時，商業 marketplace 訂單的狀態。 |
| 訂單取消日期 | 訂單取消日期 | 商業 marketplace 訂單的取消日期。 |
| 客戶公司名稱 | 客戶公司名稱 | 客戶提供的公司名稱。 名稱可能與客戶的 Azure 訂用帳戶中的城市不同。 |
| 訂單購買日期 | 訂單購買日期 | 商業 marketplace 訂單的建立日期。 |
| 供應項目名稱 | 供應項目名稱 | 商業 marketplace 供應專案的名稱。 |
| 試用結束日期 | 試用結束日期 | 此訂單將結束或已結束的日期。 |
| Customer Id | 客戶識別碼 | 指派給客戶的唯一識別碼。 客戶可以有零或多個 Azure Marketplace 訂用帳戶。 |
| 計費帳戶識別碼 | 計費帳戶識別碼 | 產生帳單的帳戶識別碼。 Map _ *計費帳戶識別碼** 至 **customerID** ，以連接您的付款交易報表與客戶、訂單和使用量報表。 |
| AssetCount | 資產計數 | 與訂單識別碼相關聯的資產數目。 |
||||

### <a name="orders-page-filters"></a>訂單頁面篩選

[ **訂單** ] 頁面篩選會套用至 [訂單] 頁面層級。 您可以選取一或多個篩選器，以針對您選擇要查看的準則，以及您想要在 [詳細訂單資料] 方格/匯出中看到的資料，來呈現圖表。 篩選準則會套用至針對您在 [訂單] 頁面右上角選取的月份範圍所解壓縮的資料。

> [!TIP]
> 您可以使用任何小工具右上角的下載圖示來下載資料。 您可以按一下 [向上] 或 [下移] 圖示，以提供每個 widget 的意見反應。

## <a name="next-steps"></a>後續步驟

- 如需商用市集中可用分析報告的總覽，請參閱 [合作夥伴中心中的「存取商用 marketplace 的分析報表](./partner-center-portal/analytics.md)」。
- 如需摘要說明供應項目市集活動的彙總資料圖表、趨勢和值，請參閱[商業市集分析中的摘要儀表板](./summary-dashboard.md)。
- 如需圖形化和可下載格式的訂單資訊，請參閱[商業市集分析中的訂單儀表板](orders-dashboard.md)。
- 針對虛擬機器 (VM) 供應項目使用量和計量帳單計量，請參閱[商業市集分析中的使用量儀表板](./usage-dashboard.md)。
- 如需過去 30 天內的下載要求清單，請參閱[商業市集分析中的下載儀表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱 [合作夥伴中心中的評量 & 評論分析儀表板](./partner-center-portal/ratings-reviews.md)。
- 如需有關商業市場分析以及完整的資料詞彙字典的常見問題，請參閱 [商業市場分析術語和常見問題](./analytics-faq.md)。