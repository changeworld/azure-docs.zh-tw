---
title: 商業 marketplace 分析中的使用量儀表板 |Azure Marketplace
description: 瞭解如何針對發佈至 Azure Marketplace 的供應專案存取所有使用量和計量付費計量。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 9f798751065d4889569f4b798ac864342f3e54ef
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934395"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>商業市集分析中的使用量儀表板

本文提供合作夥伴中心中使用方式儀表板的相關資訊。 此儀表板會顯示所有虛擬機器 (VM) 在三個不同的索引標籤中提供標準化的使用量、原始使用量和計量計費計量： VM 正規化使用量、VM 原始使用量和計量計費使用量。

若要存取合作夥伴中心中的 [使用量] 儀表板，請在 [**商業 Marketplace**] 底下選取 [**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **使用量**]。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱 [商業市場分析術語和常見問題](./analytics-faq.md)。

## <a name="usage-dashboard"></a>使用量儀表板

[**分析**] 功能表中的 [**使用量**] 儀表板會顯示您的所有軟體即服務 (SaaS) 優惠的目前訂單。 您可檢視下列項目的圖形表示：

- 使用趨勢
- 依優惠的標準化使用量
- 依其他維度的標準化使用量： VM 大小、銷售通路和供應專案類型
- 依地理位置的使用量
- 詳細使用量資料表
- 訂單頁面篩選

> [!NOTE]
> 使用事件產生和合作夥伴中心中報告的最大延遲為48小時。

## <a name="elements-of-the-usage-dashboard"></a>使用量儀表板的元素

下列各節說明如何使用 [使用量] 儀表板，以及如何讀取資料。

### <a name="month-range"></a>月份範圍

您可以在每個頁面的右上角找到月份範圍選取範圍。 選取以過去6或12個月為基礎的月份範圍，或選取最大持續時間為12個月的自訂月份範圍，以自訂 [ **使用量** ] 頁面圖形的輸出。 預設的月份範圍 (計算期間) 為六個月。

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="說明 [使用量] 儀表板上的月篩選。":::

### <a name="usage-trend"></a>使用趨勢

在本節中，您會找到客戶在選取的計算期間內取用之所有供應專案的總使用時間和趨勢。 計量和成長趨勢會以折線圖表示。 將滑鼠停留在圖表上的線條上，以顯示每個月的值。 Widget 中使用計量下方的百分比值代表所選計算期間的成長或拒絕量。

使用時數有兩種標記法： VM 標準化使用量和 VM 原始使用量。

- 標準化使用量時數的定義是可負責 VM 核心數目的標準化使用時數 ([VM 核心數目] x [未處理使用時數])。 指定為「SHAREDCORE的 VM」使用 1/6 (或0.1666) 作為 [VM 核心數目] 乘數。
- 未經處理的使用時數會定義為 Vm 的時間量（以小時為單位）。

[![說明使用量儀表板上的標準化使用量和原始使用量資料。](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>依優惠的標準化使用量

本節提供 Azure Marketplace 中以使用量為基礎的供應專案的總使用時數和趨勢。 依供應專案圖表的標準化使用量如下所述。

- **依提供的正規化使用方式** 會根據所選的計算期間，顯示前5個供應專案的正規化使用時數細目。 前五個供應專案會以圖形顯示，其餘部分則會在其餘的 [ **全部** ] 類別中分組。
- 堆疊直條圖會描述所選取日期範圍的每月成長趨勢。 月份資料行代表個別月份使用時數最高的供應項目的使用時數。 折線圖描述繪製在次要 Y 軸上的成長百分比趨勢。
- 您可以選取圖例中的特定供應項目，以在圖形中只顯示這些供應項目。

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="說明標準化的使用量會在使用量儀表板上提供資料。":::

您可以選取任何供應專案，以及最多三個 Sku 來查看供應專案和選定 Sku 的每月使用量趨勢。

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="說明使用量儀表板上的標準化使用量供應專案和 sku 資料。":::

### <a name="orders-by-offers-and-skus"></a>依優惠和 Sku 排序的訂單

依供應專案 **和 SKU 的訂單** 圖表會顯示所有供應專案的量值和趨勢。 請注意：

- 最高的供應專案會顯示在圖形中，其餘的供應專案則會分組為 **rest**。
- 您可以選取圖例中的特定供應項目，以在圖形中只顯示這些供應項目。
- 將滑鼠暫留在圖形中的配量上，會顯示與所有供應專案的訂單總數相較之下，該供應專案的訂單數目和百分比。
- **依供應項目趨勢排序的訂單** 會顯示每月成長趨勢。 月份資料行代表依供應項目名稱排序的訂單數目。 折線圖會顯示以 Z 軸繪製的成長百分比趨勢。

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="說明使用量儀表板上的訂單（依供應專案和 Sku 圖表）。":::

您可以選取任何供應專案，以及最多三個 Sku 來查看供應專案、Sku 和基座的月數趨勢。

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="說明使用量儀表板上的訂單（依供應專案和 Sku 圖表）。顯示供應專案趨勢、Sku 趨勢和基座趨勢。":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>依其他維度的標準化使用量： VM 大小、銷售通路和供應專案類型

維度有三個索引標籤： VM 大小、銷售通路和供應專案類型。 您可以針對每個維度查看使用計量和月間的趨勢。

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="說明 [使用量] 儀表板上的 [正規化使用量其他維度] 圖表。":::

### <a name="usage-by-geography"></a>依地理位置的使用量

針對選取的計算期間，熱度圖會顯示 [地理位置] 維度的總使用量。 地圖上由淺到深色彩代表由低到高的客戶計數值。 選取資料表中的一筆記錄，以放大國家/地區。

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="說明使用量儀表板上的標準化使用國家（地區）圖表。":::

請注意：

- 您可以移動地圖以檢視確切位置。
- 您可以放大特定位置。
- 熱度圖具有補充方格，可在特定位置查看客戶計數、訂單計數和標準化使用時數的詳細資料。
- 您可以在格線中搜尋並選取某個國家/地區，以縮放至地圖中的某個位置。 選取地圖中的 [ **首頁** ] 按鈕，還原為原始的視圖。

### <a name="usage-details-table"></a>使用量詳細資料表格

[ **使用量詳細資料** ] 資料表會顯示依使用量排序的前1000使用量記錄號碼清單。 請注意：

- 方格中的每個資料行皆可排序。
- 資料可以解壓縮至。TSV 或。如果記錄計數小於1000，則為 CSV 檔案。
- 如果記錄計數超過1000，則匯出資料將會以非同步方式放在將在未來30天內提供的下載頁面中。
- 將篩選套用至 **詳細的使用量資料** ，只顯示您感興趣的資料。 依國家/地區、銷售通路、Marketplace 授權類型、使用類型、供應專案名稱、供應專案類型、免費試用、Marketplace 訂用帳戶識別碼、客戶識別碼和公司名稱來篩選資料。

_**表1：資料詞彙的字典**_

| 資料行名稱 | 屬性名稱 | 定義 |
| ------------ | ------------- | ------------- |
| Marketplace 訂用帳戶識別碼 | Marketplace 訂用帳戶識別碼 | 與客戶用來購買商業 marketplace 供應專案的 Azure 訂用帳戶相關聯的唯一識別碼。 識別碼先前是 Azure 訂用帳戶 GUID。 |
| MonthStartDate | 月開始日期 | 月開始日期代表購買月份。 |
| 供應項目類型 | 供應項目類型 | 商業 marketplace 供應專案的類型。 |
| Azure 授權類型 | Azure 授權類型 | 客戶用來購買 Azure 的授權合約類型。 也稱為通道。 可能的值包括：<ui><li>雲端解決方案提供者</li><li>Enterprise</li><li>企業透過轉銷商</li><li>隨用隨付</li></ul> |
| Marketplace 授權類型 | Marketplace 授權類型 | 商業 marketplace 供應專案的計費方法。 可能的值包括：<ul><li>透過 Azure 計費</li><li>攜帶您自己的授權</li><li>免費</li><li>Microsoft 成為轉銷商</li></ul> |
| SKU | SKU | 與供應專案相關聯的方案。 |
| 客戶國家/地區 | Customer Country/Region | 客戶提供的國家/地區名稱。 國家/地區可能與客戶的 Azure 訂用帳戶中的國家/地區不同。 |
| 是預覽 SKU | 是預覽 SKU | 如果您已將 SKU 標記為「預覽」，則會顯示此值。 如果 SKU 已適當標記，則值為「是」，而且只有您所授權的 Azure 訂用帳戶可以部署和使用此映像。 如果 SKU 尚未識別為「預覽」，則值為「否」。 |
| SKU 計費類型 | SKU 計費類型 | 與供應專案中每個 SKU 相關聯的計費類型。 可能的值包括：<ul><li>免費</li><li>已支付</li></ul> |
| IsInternal | 已被取代 | 已被取代 |
| VM 大小 | 虛擬機器大小 | 針對以 VM 為基礎的供應專案類型，此實體表示與供應專案 SKU 相關聯的 VM 大小。 |
| 雲端執行個體名稱 | 雲端執行個體名稱 | 發生 VM 部署的 Microsoft Cloud。 |
| ServicePlanName | 已被取代 | 已淘汰 (與 SKU) 相同的定義 |
| 供應項目名稱 | 供應項目名稱 | 商業 marketplace 供應專案的名稱。 |
| DeploymentMethod | 已被取代 | 已淘汰 (與供應專案類型相同的定義) 
 |
| 客戶公司名稱 | 客戶公司名稱 | 客戶提供的公司名稱。 名稱可能與客戶的 Azure 訂用帳戶中的名稱不同。 |
| 使用日期 | 使用日期 | 以使用量為基礎的資產產生使用量事件的日期。 |
| IsMultisolution | 為 Multisolution | 表示供應專案是否為 Multisolution 供應專案類型。 |
| 是新的客戶 | 已被取代 | 已被取代 |
| 核心大小 | 核心大小 | 與以 VM 為基礎的供應專案相關聯的核心數目。 |
| 使用量類型 | 使用量類型 | 表示與供應專案相關聯的使用事件是否為下列其中一項：<ul><li>標準化使用量</li><li>原始使用量</li><li>使用計量</li></ul> |
| 試用結束日期 | 試用結束日期 | 此訂單將結束或已結束的日期。 |
| 客戶貨幣 (CC) | 客戶貨幣 | 客戶用來進行商業 marketplace 交易的貨幣。 |
| 價格 (CC) | 價格 | 以客戶貨幣顯示的 SKU 單價。 |
| 付款貨幣 (PC) | 付款貨幣 | 發行者會以發行者設定的貨幣，支付與資產相關聯的使用事件。 |
| 預估價格 (PC) | 預估價格 | 「發行者」所設定之貨幣的 SKU 單價。 |
| 使用量參考 | 使用量參考 | 一種串連的 GUID，用來連接商業 marketplace 分析) 中的使用量報表 (與付款交易報表。 使用方式參考會與 [付款] 交易報表中的 [訂單 Id] 和 [LineItemId] 連接。 |
| 使用量單位 | 使用量單位 | 與 SKU 相關聯的耗用量單位。 |
| Customer Id | 客戶識別碼 | 指派給客戶的唯一識別碼。 客戶可以有零或多個 Azure Marketplace 訂用帳戶。 |
| 計費帳戶識別碼 | 計費帳戶識別碼 | 產生帳單的帳戶識別碼。 將 **帳單帳戶識別碼** 對應至 **customerID** ，以連接您的付款交易報表與客戶、訂單和使用量報表。 |
| 使用量數量 | 使用量數量 | 由客戶部署的資產所耗用的總使用量單位。<br>這是以使用類型專案為基礎。 例如，如果使用類型是標準化使用方式，則使用數量是用於標準化使用量。 |
| NormalizedUsage | 標準化的使用量 | 客戶所部署資產所耗用的標準化使用量單位總數。<br>標準化使用量時數的定義是可負責 VM 核心數目的標準化使用時數 ([VM 核心數目] x [未處理使用時數])。 指定為「SHAREDCORE的 VM」使用 1/6 (或0.1666) 作為 [VM 核心數目] 乘數。 |
| MeteredUsage | 計量使用量 | 由客戶所部署的供應專案所設定之計量所耗用的總使用量單位。 |
| RawUsage | 未處理使用量 | 客戶所部署資產所耗用的原始使用量單位總數。<br>原始的使用時數會定義為 Vm 依使用量單位執行的時間量。 |
| 預估擴充費用 (CC) | 客戶貨幣的預估延長費用 | 表示與使用量相關聯的費用。 此資料行是價格 (CC) 和使用量數量的乘積。 |
| 預估擴充費用 (PC) | 付款金額的預估延長費用 | 表示與使用量相關聯的費用。 此資料行是 (電腦) 和使用量數量的預估價格乘積。 |
||||

### <a name="usage-page-filters"></a>使用方式頁面篩選

[ **使用量** ] 頁面篩選會套用至 [訂單] 頁面層級。 您可以選取一或多個篩選器，針對您選擇要查看的準則，以及您想要在 [使用訂單資料] 方格/匯出中看到的資料，轉譯圖表。 篩選準則會套用至針對您在 [使用方式] 頁面右上角選取的月份範圍所解壓縮的資料。

VM 原始使用量的 widget 和匯出報告類似于 VM 正規化使用，但有下列差異：

- 標準化使用量時數的定義是可負責 VM 核心數目的標準化使用時數 ([VM 核心數目] x [未處理使用時數])。 指定為「SHAREDCORE的 VM」使用 1/6 (或0.1666) 作為 [VM 核心數目] 乘數。
- 原始的使用時數會定義為 Vm 依使用量單位執行的時間量。

### <a name="metered-billing-usage"></a>計量付費使用量

[ **計量使用量** ] 索引標籤會顯示供應專案類型的使用量資訊，其中使用量是依每個計量維度來測量。 目前顯示的是 SaaS 供應項目類型超額部分。 此索引標籤會將 SaaS 計量付費使用量的超額趨勢以圖形表示：

- **依計量維度的超額趨勢**：顯示供應項目其所選計量維度的每月超額趨勢。 X 軸代表月份，而 Y 軸代表超額使用數量。 自訂計量的測量單位也會顯示在 Y 軸上。

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="說明使用量儀表板上的 VM 標準化使用量圖表。":::

- **依 sku 的超額趨勢**：依 sku/方案表示所選計量維度的使用量趨勢。 顯示所選供應專案使用量最高的前五個方案。

- **客戶的超額趨勢**：客戶領導者面板代表具有最高使用時間的客戶堆疊清單，並顯示在 _領導者面板_ 上，並依自訂計量的最高使用量進行排名。 在領導者面板中選取客戶，以查看所選計量維度的超額使用趨勢。

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="說明 [使用量] 儀表板上的 [依客戶的計量使用量] 圖表。":::

如果您有多個使用自訂計量的供應專案，計量付費使用量報表會根據其自訂計量維度顯示所有供應專案的使用量資訊。

> [!TIP]
> 您可以使用任何小工具右上角的下載圖示來下載資料。 您可以按一下 [向上] 或 [下移] 圖示，以提供每個 widget 的意見反應。

## <a name="next-steps"></a>後續步驟

- 如需商用市集中可用分析報告的總覽，請參閱 [合作夥伴中心中的「存取商用 marketplace 的分析報表](./partner-center-portal/analytics.md)」。
- 如需摘要說明供應項目市集活動的彙總資料圖表、趨勢和值，請參閱[商業市集分析中的摘要儀表板](./summary-dashboard.md)。
- 如需以圖形和可下載格式顯示訂單的詳細資訊，請參閱 [商業市場分析中的訂單儀表板](./orders-dashboard.md)
- 針對 (VM) 提供使用量和計量付費計量的虛擬機器，請參閱 [商用 marketplace 分析中的使用量儀表板](usage-dashboard.md)。
- 如需過去 30 天內的下載要求清單，請參閱[商業市集分析中的下載儀表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看 Azure Marketplace 和 Microsoft AppSource 上提供的客戶意見反應的匯總觀點，請參閱 [合作夥伴中心 & 審核分析儀表板中的評](./partner-center-portal/ratings-reviews.md)等。
- 如需有關商業市場分析以及完整的資料詞彙字典的常見問題，請參閱 [商業市場分析術語和常見問題](./analytics-faq.md)。
