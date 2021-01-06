---
title: Microsoft 商業市場分析合作夥伴中心、Azure Marketplace 和 Microsoft AppSource 的客戶儀表板
description: 瞭解如何使用商業 marketplace 分析中的客戶儀表板，來存取客戶的相關資訊，包括成長趨勢。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c98b47507a7543002f00aae82370f5ea0043510d
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954272"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>商業 marketplace 分析中的客戶儀表板

本文提供合作夥伴中心中 [客戶] 儀表板的相關資訊。 此儀表板會顯示包括成長趨勢在內的客戶資訊，以圖形化及可下載的格式呈現。

若要存取合作夥伴中心中的客戶儀表板，請在 [**商業 Marketplace** ] 下選取 [**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **客戶**]。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱 [商業市場分析術語和常見問題](./analytics-faq.md)。

## <a name="customers-dashboard"></a>客戶儀表板

客戶儀表板會顯示已取得供應專案的客戶資料。 您可檢視以圖形表示的下列項目：

- 活躍和流失客戶的趨勢
- 客戶成長趨勢，包括現有、新的和流失的客戶
- 依訂單和使用量排列的客戶
- 客戶百分位數 
- 依訂單和使用量的客戶類型
- 客戶數 (依地理位置)
- 客戶詳細資料表格
- 客戶頁面篩選

> [!NOTE]
> 客戶取得和報告合作夥伴中心之間的最大延遲為48小時。

## <a name="elements-of-the-customers-dashboard"></a>客戶儀表板的元素

下列各節說明如何使用 [客戶] 儀表板，以及如何讀取資料。

### <a name="month-range"></a>月份範圍

您可以在每個頁面的右上角找到月份範圍選取範圍。 選取以過去6個月或12個月為基礎的月份範圍，或選取最大持續時間為12個月的自訂月份範圍，以自訂 [ **客戶** ] 頁面圖形的輸出。 預設的月份範圍 (計算期間) 為六個月。

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="說明 [客戶] 頁面上的月篩選。":::

> [!NOTE]
> 視覺效果 widget 和匯出報表中的所有計量都會接受使用者所選取的計算週期。

### <a name="active-and-churned-customers-trend"></a>活躍和流失客戶的趨勢

在本節中，您會發現您的客戶所選取計算期間的成長趨勢。 計量和成長趨勢會以折線圖表示，並藉由將滑鼠停留在圖表上的線條來顯示每個月的值。 Widget 中作用中 **客戶** 下方的百分比值代表所選計算期間的成長量。 例如，下列螢幕擷取畫面顯示所選計算期間的0.92% 成長。

[![說明 [客戶] 頁面上的 [客戶] widget。](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

有三種 _客戶類型_：新客戶、舊客戶和流失的客戶。

- 新客戶是在選取的月份內，第一次取得一或多個供應項目的客戶。
- 舊客戶是在選取的月份前，取得一或多個供應項目的客戶。
- 所流失客戶是取消之前所購買全部供應項目的客戶。 流失客戶會在趨勢 widget 的負軸中表示。

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>客戶成長趨勢，包括現有、新的和流失的客戶

在本節中，您會找到所有客戶的趨勢和計數，包括新的、現有的和流失，以及每月的成長趨勢。

- 折線圖表示整體客戶成長的百分比。
- [月份] 資料行代表由新舊客戶及所流失客戶堆疊而成的客戶計數。
- 流失客戶計數會以 X 軸的負方向顯示。
- 您可以選取特定圖例專案，以顯示更詳細的視圖。 例如，選取 [圖例] 中的 [新客戶] 只顯示新客戶。
- 將滑鼠停留在圖表中的資料行上方，只會顯示該月份的詳細資料。

[![說明 [客戶] 頁面上的 [客戶趨勢] widget。](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>依訂單/使用量的客戶

**客戶的訂單/使用量** 圖表有三個索引標籤：訂單、標準化使用量和原始使用量。 選取 [ **訂單** ] 索引標籤，以顯示訂單詳細資料。

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="在 [客戶] 頁面上，說明 [客戶的訂單] 和 [使用量] widget 的 [訂單] 索引標籤。":::

請注意：

- 領導者面板會顯示依訂單計數排名的客戶詳細資料。 選取客戶之後，客戶詳細資料會顯示在連續的「詳細資料」、「依 Sku 排序」和「依基座的 Sku」區段中。
- 當發行者以擁有者角色登入時，此空間中會顯示客戶設定檔詳細資料。 如果發行者是以參與者角色登入，此區段中的詳細資料將無法使用。
- **Orders By sku** 環圈圖會顯示針對方案購買的訂單明細。 系統會顯示具有最高訂單計數的前五個計畫，而其餘訂單會在 [ **全部 rest**] 下分組。
- [ **依** 基座的 sku] 環圈圖會顯示針對方案訂購的基座明細。 系統會顯示具有最高基座的前五個方案，而其餘訂單則會在 [ **全部 rest**] 下分組。

您也可以選取 [ **標準化使用量** ] 或 [ **原始使用量** ] 索引標籤，以查看使用量詳細資料。

- 領導者面板會顯示依使用量時數排名的客戶詳細資料。 選取客戶之後，客戶的詳細資料會顯示在連續的「詳細資料」、「依供應專案的正規化使用量」和「依虛擬機器 (VM 的標準化使用量) 大小 (s) 」一節。
- 當發行者以擁有者角色登入時，客戶設定檔詳細資料就會顯示在此位置。 如果發行者是以參與者角色登入，即無法取得此區段中的詳細資料。
- **標準化的使用量（依提供** 環圈圖）會顯示供應專案所使用的使用量細目。 會顯示具有最高使用量計數的前五個計畫，而其餘的供應專案則會在 [ **全部 rest**] 下分組。
- **依 Vm 大小 (s 的標準化使用量)** 環圈圖會顯示不同 vm 大小 (s) 所耗用的使用量細目。 會顯示具有最高標準化使用量的前五個 VM 大小，而其餘的使用量則會在 [ **全部 rest**] 下分組。

### <a name="top-customers-percentile"></a>前幾個客戶百分位數

**排名最高的客戶百分位數** 圖表有三個索引標籤：「訂單」、「標準化使用量」和「原始使用量」。 _排名最高的客戶百分位數_ 會沿著 X 軸顯示，由訂單數目決定。 Y 軸顯示客戶的訂單計數。 次要 Y 軸 (折線圖) 會顯示總訂單數的累計百分比。 您可將滑鼠暫留在折線圖的點上，以顯示詳細資料。

[![說明 [客戶] 頁面上 [最上層客戶百分位數] widget 的 [訂單] 索引標籤。](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>依訂單和使用量的客戶類型

**依客戶類型排列的訂單/使用量** 圖會分別根據圖中所選訂單、標準化使用量和未處理使用量來顯示按新客戶與舊客戶其訂單數目、標準化使用量和未處理使用量的時數份額。

此圖表顯示下列各項：

- 新客戶是在同一行事曆月份內 (Y 軸)，第一次取得一或多個供應項目的客戶。
- 舊客戶是在此行事曆月份 (Y 軸) 前，已取得供應項目或回報使用量的客戶。

[![說明 [客戶] 頁面上 [訂單依據客戶類型] widget 的 [訂單] 索引標籤。](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>客戶數 (依地理位置)

針對選取的計算期間，熱度圖會顯示客戶總數，以及新增的客戶對地理位置維度的百分比。 地圖上由淺到深色彩代表由低到高的客戶計數值。 選取資料表中的一筆記錄，以放大國家或地區。

[![說明 [客戶] 頁面上 [依地理位置] widget 的 [訂單] 索引標籤。](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

請注意：

- 您可以移動地圖以檢視確切位置。
- 您可以放大特定位置。
- 熱度圖有一個補充格線，可供您檢視特定位置的客戶計數、訂單計數、標準化使用時數詳細資料。
- 您可以在格線中搜尋並選取某個國家/地區，以縮放至地圖中的某個位置。 選取地圖中的 [ **首頁** ] 按鈕，還原為原始的視圖。

### <a name="customer-details-table"></a>客戶詳細資料表

[ **客戶詳細資料** ] 資料表會顯示排名最高的1000客戶清單，依他們第一次取得其中一個供應專案的日期排序。 您可以藉由選取 [詳細資料] 功能區中的展開圖示來展開區段。

請注意：

- 客戶的個人資訊必須經過客戶同意才會顯示。 如果您已使用「擁有者」角色層級的許可權登入，就只能查看這項資訊。
- 方格中的每個資料行皆可排序。
- 資料可以解壓縮至。CSV 或。如果記錄計數小於1000，則為 TSV 檔案。
- 如果記錄數目超過1000，匯出的資料會以非同步方式放在未來30天的 [下載] 頁面中。
- 將篩選套用至資料表，只顯示您感興趣的資料。 依公司名稱、客戶識別碼、Marketplace 訂用帳戶識別碼、Azure 授權類型、取得日期、遺失日期、客戶電子郵件、客戶國家/地區/州/城市/郵遞區號、客戶語言等來篩選資料。
- 當受保護的客戶購買供應項目時，將會遮罩 (************) [Customer Detailed Data] \(客戶詳細資料\) 中的資訊。
- 客戶維度詳細資料（例如公司名稱、客戶名稱和客戶電子郵件）是組織識別碼層級，而不是 Azure Marketplace 或 Microsoft AppSource 交易層級。

_**表1：資料詞彙的字典**_

| 資料行名稱 | 屬性名稱 | 定義 |
| ------------ | ------------- | ------------- |
| Marketplace 訂用帳戶識別碼 | Marketplace 訂用帳戶識別碼 | 與客戶用來購買商業 marketplace 供應專案的 Azure 訂用帳戶相關聯的唯一識別碼。 針對基礎結構供應專案，這是客戶的 Azure 訂用帳戶 GUID。 針對 SaaS 供應專案，這會顯示為零，因為 SaaS 購買不需要 Azure 訂用帳戶。 |
| DateAcquired | 取得日期 | 客戶購買任何您所發佈之供應專案的第一天。 |
| DateLost | 損失日期 | 客戶最近一次購買的供應專案最後一個日期。 |
| Provider Name | Provider Name | 與 Microsoft 和客戶之間的關係相關的提供者名稱。 如果客戶是透過轉銷商的企業，就會是轉銷商。 如果涉及雲端解決方案提供者 (CSP)，就會是 CSP。 |
| 提供者電子郵件 | 提供者電子郵件 | 與 Microsoft 和客戶之間的關係相關之提供者的電子郵件地址。 如果客戶是透過轉銷商的企業，就會是轉銷商。 如果涉及雲端解決方案提供者 (CSP)，就會是 CSP。 |
| FirstName | 客戶名字 | 客戶所提供的名字。 名稱可能與客戶的 Azure 訂用帳戶中提供的名稱不同。 |
| LastName | 客戶姓氏 | 客戶所提供的姓氏。 名稱可能與客戶的 Azure 訂用帳戶中提供的名稱不同。 |
| 電子郵件 | 客戶電子郵件 | 客戶提供的電子郵件地址。 電子郵件可能與客戶的 Azure 訂用帳戶中的電子郵件地址不同。 |
| 客戶公司名稱 | 客戶公司名稱 | 客戶提供的公司名稱。 名稱可能與客戶的 Azure 訂用帳戶中的城市不同。 |
| CustomerCity | 客戶縣/市 | 客戶提供的城市名稱。 城市可能與客戶的 Azure 訂用帳戶中的城市不同。 |
| 客戶郵遞區號 | 客戶郵遞區號 | 客戶提供的郵遞區號。 程式碼可能與客戶的 Azure 訂用帳戶中提供的郵遞區號不同。 |
| CustomerCommunicationCulture | 客戶通訊語言 | 客戶慣用於通訊的語言。 |
| CustomerCountryRegion | Customer Country/Region | 客戶提供的國家/地區名稱。 國家/地區可能與客戶的 Azure 訂用帳戶中的國家/地區不同。 |
| AzureLicenseType | Azure 授權類型 | 客戶用來購買 Azure 的授權合約類型。 也稱為 _通道_。 可能的值包括：<ul><li>雲端解決方案提供者</li><li>Enterprise</li><li>企業透過轉銷商</li><li>隨用隨付</li></ul> |
| PromotionalCustomers | 已選擇加入促銷連絡人 | 此值可讓您知道客戶是否主動選擇加入發行者的促銷連絡人。 目前，我們沒有向客戶顯示該選項，因此我們一律指出 [否]。 部署這項功能之後，我們將會據以開始更新。 |
| CustomerState | 客戶州 | 客戶所提供的居住州。 狀態可能與客戶的 Azure 訂用帳戶中提供的狀態不同。 |
| CommerceRootCustomer | 商務根客戶 | 一個計費帳戶識別碼可以與多個客戶識別碼相關聯。<br>計費帳戶識別碼和客戶識別碼的其中一個組合，可以與多個商業 marketplace 訂用帳戶相關聯。<br>商務根客戶表示訂用帳戶的客戶名稱。 |
| Customer Id | 客戶識別碼 | 指派給客戶的唯一識別碼。 客戶可以有零或多個 Azure Marketplace 訂用帳戶。 |
| 計費帳戶識別碼 | 計費帳戶識別碼 | 產生帳單的帳戶識別碼。 將 **帳單帳戶識別碼** 對應至 **customerID** ，以連接您的付款交易報表與客戶、訂單和使用量報表。 |
||||

### <a name="customers-page-filters"></a>客戶頁面篩選

客戶頁面篩選會套用至客戶頁面層級。 您可以選取多個篩選器，以針對您選擇要查看的準則，以及您想要在「詳細訂單資料」方格/匯出中看到的資料，轉譯圖表。 篩選準則會套用至針對您在 [客戶] 頁面右上角選取的月份範圍所解壓縮的資料。

> [!TIP]
> 您可以使用任何小工具右上角的下載圖示來下載資料。 您可以按一下 [向上] 或 [下移] 圖示，以提供每個 widget 的意見反應。

## <a name="next-steps"></a>後續步驟

- 如需商用市集中可用分析報告的總覽，請參閱 [合作夥伴中心中的「存取商用 marketplace 的分析報表](./partner-center-portal/analytics.md)」。
- 如需摘要說明供應項目市集活動的彙總資料圖表、趨勢和值，請參閱[商業市集分析中的摘要儀表板](./summary-dashboard.md)。
- 如需以圖形和可下載格式顯示訂單的詳細資訊，請參閱 [商用 marketplace 分析中的訂單儀表板](./orders-dashboard.md)。
- 針對 (VM) 提供使用量和計量付費計量的虛擬機器，請參閱 [商用 marketplace 分析中的使用量儀表板](./usage-dashboard.md)。
- 如需過去 30 天內的下載要求清單，請參閱[商業市集分析中的下載儀表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看 Azure Marketplace 和 Microsoft AppSource 上提供的客戶意見反應的匯總觀點，請參閱 [合作夥伴中心 & 審核分析儀表板中的評](./partner-center-portal/ratings-reviews.md)等。
- 如需有關商業市場分析以及完整的資料詞彙字典的常見問題，請參閱 [商業市場分析術語和常見問題](./analytics-faq.md)。