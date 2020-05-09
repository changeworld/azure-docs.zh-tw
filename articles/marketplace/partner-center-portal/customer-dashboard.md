---
title: 合作夥伴中心的 Microsoft 商業 marketplace 分析客戶儀表板
description: 瞭解如何使用商業 marketplace 分析中的客戶儀表板，來存取客戶的相關資訊，包括成長趨勢。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 4d9dc0315a5c87ed67390e8edb23d44ee55f242b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732762"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>商業 marketplace 分析中的客戶儀表板

本文提供合作夥伴中心**客戶儀表板**的相關資訊。 此儀表板會顯示您客戶的相關資訊，包括成長趨勢，並以圖形化和可下載的格式呈現。

若要存取**客戶儀表板**，請開啟 [商業 Marketplace] 底下的 [**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**] 儀表板。

>[!NOTE]
> 如需分析術語的詳細定義，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。

## <a name="customer-dashboard"></a>客戶儀表板

[**分析**] 功能表的 [**客戶] 儀表板**會顯示已取得您供應專案之客戶的資料。 您可以查看下列專案的圖形表示：

- [客戶摘要](#customer-summary)
- [依地理位置的客戶](#customer-by-geography)
- [客戶趨勢](#customer-trends)
- [依訂單和使用量的客戶](#customers-by-orders-and-usage)
- [依 Sku 的客戶](#customers-by-skus)
- [訂單和使用量（依客戶類型）](#orders-and-usage-by-customer-type)
- [客戶詳細資料資料表](#customer-details-table)
- [客戶頁面篩選](#customer-page-filters)

### <a name="customer-summary"></a>客戶摘要

[客戶摘要] 區段會顯示所選日期範圍內的所有客戶計數，包括新的、現有的和變換。

- 客戶總數會定義為已購買供應專案，且至少有一個訂單尚未取消的所有客戶計數。
- 相較于上個月，客戶的成長百分比是以綠色或向下指標以紅色表示。
- 成長趨勢會以橫條圖表示，並將滑鼠停留在圖表的資料行上方，以顯示每個月的值。

有三種**客戶類型**：新的、現有的和變換。

- 新客戶在選取的月份內第一次取得了一或多個供應專案。
- 在選取的月份之前，現有的客戶已取得您的一或多個供應專案。
- 變換客戶已取消了先前購買的所有供應專案。

### <a name="customer-by-geography"></a>依地理位置的客戶

[**依地理位置的客戶**] 圖表會顯示在選取的日期範圍內取得的所有客戶和客戶計數，並根據客戶國家/地區進行對應。 地圖上的淺到暗色彩代表客戶計數的低到高值。 按一下資料表中的記錄，以放大國家/地區。

熱度圖會顯示客戶計數和% （依客戶國家/地區）。 您可以移動地圖以查看確切的位置，並放大至特定位置。 此地圖具有補充格線，可讓您依位置以及新加入至該位置的客戶來查看% 的客戶。

### <a name="customer-trends"></a>客戶趨勢

[**客戶趨勢**] 圖表會顯示所有客戶的計數，包括新的、現有的和變換，以及每月的成長趨勢。

- 折線圖代表整體客戶成長百分比。
- [月份] 資料行代表由新的、現有的和變換客戶堆疊的客戶計數。
- [變換客戶計數] 會顯示在 [Y 軸] 的負值方向上。
- 您可以選取特定的圖例專案，以顯示更詳細的視圖。 例如，從 [圖例] 選取 [新客戶]，只顯示新客戶。
- 您可以使用圖表頂端的滑杆，在 X 軸上向右或向左移動，並將焦點放在特定的資料點，以便更詳細地查看。
- 將滑鼠停留在圖表的資料行上，只會顯示該月份的詳細資料。

### <a name="customers-by-orders-and-usage"></a>依訂單和使用量的客戶

[**依訂單/使用量的客戶**] 圖表有三個索引標籤：「訂單」、「正規化使用」和「原始使用量」。 **最上層的客戶百分**位數會沿著 X 軸顯示，由其訂單數目決定。 Y 軸會顯示客戶的訂單計數。 [次要 y 軸（折線圖）] 會顯示訂單總數的累計百分比。 您可以將滑鼠停留在折線圖上的點上方，以顯示詳細資料。

如需範例，請參閱下圖以瞭解正規化的使用方式：客戶的前30個百分位數累積了87% 的標準化使用量。 客戶的30個百分位數只會貢獻1570000小時的使用量。

### <a name="customers-by-skus"></a>依 Sku 的客戶

**依 sku/使用量**圖表列出的客戶如下所述。

1. 領導者面板會針對依訂單計數排名的前50客戶提供詳細資料。 選取客戶之後，此領導者面板的第2、3和4節會顯示客戶的詳細資料。
2. 當發行者以擁有者角色登入時，客戶設定檔詳細資料就會顯示在此空間中。 如果發行者是以參與者角色登入，此區段中的詳細資料將無法使用。
3. [依 Sku 的訂單] 環圈圖會顯示為 Sku 購買的訂單明細。 會顯示具有最高訂單計數的前5個 Sku，而其餘的訂單則會在 [全部 rest] 之下分組。
4. [依 Sku 的基座] 環圈圖會顯示為 Sku 排序的基座細目。 系統會顯示具有最高基座的前5個 Sku，而其餘的訂單則會在 [全部 rest] 下分組。

### <a name="orders-and-usage-by-customer-type"></a>訂單和使用量（依客戶類型）

[**依客戶類型的訂單/使用量**] 圖表會顯示在新客戶與現有客戶之間分割的訂單數目、正規化使用量和原始使用量小時。根據選取的順序、正規化和原始使用量，分別在圖表中。

- 新客戶已在同一個日曆月份（y 軸）內取得您的一或多個供應專案，或第一次回報使用量。
- 現有的客戶先前已從您取得供應專案，或在行事曆月份（y 軸）之前回報使用量。

### <a name="customer-details-table"></a>客戶詳細資料資料表

[**客戶詳細資料**] 資料表會顯示依其第一次取得您供應專案的日期排序的前1000個客戶編碼清單。

- 只有在客戶已提供同意時，才會提供客戶個人資訊。 如果您已使用 [擁有者] 角色層級的許可權來登入，則只能看到這項資訊。 深入瞭解使用者角色和許可權。
- 方格中的每個資料行都是可排序的。
- 如果記錄計數小於1000，則可以將資料解壓縮至 TSV 檔案。
- 如果記錄數目超過1000，匯出的資料會在接下來的30天內以非同步方式放置在下載頁面中。
- 篩選器可以套用至資料表，只顯示您感興趣的資料。 資料可以依公司名稱、客戶識別碼、Marketplace 訂用帳戶識別碼、Azure 授權類型、取得的日期、遺失的日期、客戶電子郵件、客戶國家/地區/城市/Zip、客戶語言等進行篩選。

### <a name="customer-page-filters"></a>客戶頁面篩選

[**客戶] 頁面**篩選會套用至 [客戶] 頁面層級。 您可以選取多個篩選器，針對您選擇要查看的準則和您想要在 [詳細訂單資料] 方格/匯出中查看的資料轉譯圖表。 篩選會針對您在 [訂單] 頁面右上角選取的資料範圍，套用到解壓縮的資料上。

>[!NOTE]
> 客戶方格中每個欄位的詳細定義、頁面篩選和其可能的選擇，都位於[商業 Marketplace 分析的常見問題和術語](./faq-terminology.md)中。

## <a name="next-steps"></a>後續步驟

- 如需合作夥伴中心商業 marketplace 中可用分析報告的總覽，請參閱[合作夥伴中心的商用 Marketplace 分析](./analytics.md)。
- 如需摘要說明您供應專案 marketplace 活動之匯總資料的圖形、趨勢和值，請參閱[商業 marketplace 分析中的摘要儀表板](./summary-dashboard.md)。
- 如需以圖形化和可下載格式顯示訂單的相關資訊，請參閱[商用 marketplace 分析中的訂單儀表板](./orders-dashboard.md)。
- 若為虛擬機器（VM）提供使用量和計量付費計量，請參閱[商業 marketplace 分析中的使用量儀表板](./usage-dashboard.md)。
- 如需過去30天內的下載要求清單，請參閱[商用 marketplace 分析中的下載儀表板](./downloads-dashboard.md)。
- 若要查看有關 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱[商業 Marketplace 分析中的評等和評論儀表板](./ratings-reviews.md)。
- 如需有關商業 marketplace 分析的常見問題，以及完整的資料詞彙字典，請參閱[商業 marketplace 分析的常見問題和術語](./faq-terminology.md)。
