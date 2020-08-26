---
title: Azure 成本管理中常見的成本分析用途
description: 本文說明如何在 Azure 成本管理中取得常見成本分析工作的結果。
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: c03bf5e52ae7f6b259c7b744e6033d760af23dd0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683669"
---
# <a name="common-cost-analysis-uses"></a>常見的成本分析用途

許多人都曾詢問過的問題，經常也是 Azure 成本管理使用者亟待解答的問題。 本文將逐步說明如何在成本管理中取得常見成本分析工作的結果。

## <a name="view-forecasted-costs"></a>檢視預測的成本

針對區域和堆疊直條圖，預測的成本會顯示在成本分析區域中。 預測是以您的歷程記錄資源使用量為基礎。 對您的資源使用所做的變更會影響預測的成本。

在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。

在預設檢視中，頂端圖表會有實際/分攤的成本和預測的成本區段。 圖表的純色會顯示您的實際/分攤成本。 陰影色彩則顯示預測成本。

[![預測成本](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>檢視依服務群組的預測成本

預設的檢視不會顯示服務的預測成本群組，因此您必須依選取項目來新增群組。

在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。

選取 [群組依據]  >  [服務名稱]。

此檢視會顯示針對每個服務群組的成本。 不會針對每個服務計算預測的成本。 它會針對您所有服務的**總計**預測。

[![群組的預測成本](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>檢視服務的預測成本

您可以檢視縮小至單一服務的預測成本。 例如，您可能只想要查看虛擬機器的預測成本。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 選取 [新增篩選]，然後選取 [服務名稱]。
1. 在 [選擇] 清單中，選取服務。 例如，選取 [虛擬機器]。

檢查選取項目的實際成本和預測的成本。

您可以新增更多自訂項目至此檢視。

1. 為 [計量] 新增第二個篩選，然後選取一個值，以根據您選取的服務名稱來篩選個別類型的計量。
1. 依**資源**群組，以查看產生成本的特定資源。 不會針對每個服務計算預測的成本。 它會針對您所有資源的**總計**預測。

[![服務的預測成本](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>同時檢視您的 Azure 和 AWS 成本  

若要同時檢視 Azure 和 AWS 成本，您可以在 Azure 中使用管理群組範圍。

1. 建立管理群組或選取現有的群組。
1. 將您需要的現有 Azure 訂用帳戶指派給管理群組。
1. 將*相同*的管理群組指派給連接器的連結帳戶。
1. 移至成本分析並選取 [累積的成本]。
1. 選取 [群組依據]  -  [提供者]。

## <a name="view-cost-breakdown-by-azure-service"></a>依 Azure 服務檢視成本明細

依 Azure 服務來檢視成本，可協助您進一步了解基礎結構中最耗費成本的部分。 例如，VM 計算成本可能很低。 不過，從 VM 發出的資訊量，可能會產生高額的網路成本。 您必須了解拉高 Azure 服務成本的主要因素，以期能視需要調整服務使用方式。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 選取 [依服務區分的成本]，然後依 [服務層級] 進行分組。
1. 將檢視變更為 [資料表]。

[![依 Azure 服務顯示的成本明細](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>檢閱成本分析中的發票費用

若要在 Azure 入口網站中檢視發票詳細資料，請瀏覽至與您正在分析之發票相關聯範圍的「成本分析」。 選取 [發票詳細資料] 檢視。 發票詳細資料會顯示發票上顯示的費用。

[![顯示發票詳細資料的範例](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

若要檢視發票詳細資料，您可以識別具有非預期成本的服務，並在成本分析中判斷哪些資源會直接與資源相關聯。 例如，如果想分析虛擬機器服務的費用，請瀏覽至**累計成本**檢視。 然後，將細微性設定為**每日**，並篩選**服務名稱：虛擬機器**的費用，並依**資源**將費用分組。

[![顯示虛擬機器累計成本的範例](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>依 Azure 資源檢視成本明細

您的服務是以 Azure 資源建置的。 根據資源檢閱成本，可協助您快速找出成本中的主要佔比項目。 如果服務有太昂貴的資源，請考慮進行變更以降低成本。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 選取 [依資源區分的成本]。
1. 將檢視變更為 [資料表]。

[![依 Azure 資源檢視成本明細](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>依選取的維度檢視成本明細

維度可讓您根據費用中顯示的各種中繼資料值來組織成本。 例如，您可以依位置將成本分組。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 選取 [群組依據] 篩選條件。  
    [![依項目選取群組](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. (選擇性) 您可以儲存檢視以供後續使用。
1. 按一下圖形下方的圓形圖，以檢視更詳細的資料。  
    [![依選取的維度檢視成本明細](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>每天或每月檢視成本

查看每日和每月的成本，可協助您進一步了解成本是否在某週或某年較高。 如果您在假期期間擁有更多客戶流量，是否會導致 Azure 成本相對應地增加？ 星期五的成本是否比星期一高？

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 將 [細微性] 設定為 [每月] 或 [每日]。

[![每天檢視成本](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>檢視您的 Spot VM 費用

Spot VM 可為可處理中斷的工作負載省下大量的成本。 工作負載會在未使用的 Azure 容量上執行。 由於工作負載可以隨時收回，所以 Spot VM 可獲得顯著的折扣。 請使用下列步驟來檢視 Spot VM 費用。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如，[成本管理 + 計費] > [成本管理] > [成本分析]。
2. 新增篩選條件，用於 [計價模式：Spot]。

![顯示 Spot VM 篩選條件的範例](./media/cost-analysis-common-uses/spot-vm-filter.png)

[計價模式] 維度也會用來視需要和保留費用以進行查看。

## <a name="view-your-reservation-charges"></a>檢視您的保留費用

保留執行個體可讓您使用 Azure 節省成本。 您可以利用保留，在一段時間內提前支付指定數量的資源費用。 成本分析會顯示您帳單上所顯示的費用。 費用會顯示為實際成本，或在您的保留期間內分攤。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如，[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 新增篩選條件，用於 [計價模式：保留]。
1. 在 [範圍] 底下和所顯示成本旁邊，按一下向下箭號符號，然後選取 [實際成本] 或 [分攤成本] 計量。

![選取成本計量](./media/cost-analysis-common-uses/metric-cost.png)

每個計量都會影響您保留費用的顯示方式。

**實際成本** - 顯示您帳單上所顯示的購買。 例如，如果您在 1 月花費 $1200 購買一年的保留，則成本分析會在 1 月份顯示保留的成本 $1200。 其不會顯示該年其他月份的保留成本。 如果您依 VM 將實際成本進行群組，則收到給定月份之保留權益的 VM，該月份將沒有成本。

**分攤成本** - 將保留購買分割顯示為保留期限期間的分攤成本。 使用上述相同的範例，如果您在 1 月花費 $1200 購買了一年的保留，成本分析就會顯示一年中每個月 $100 的成本。 如果您在此範例中依 VM 將成本進行群組，您就會看到歸屬於每個收到保留權益的 VM 成本。

## <a name="view-your-reservation-utilization"></a>檢視保留使用率

購買保留之後，請務必追蹤其使用率，以取得您所支付的費用。 例如，如果您一年購買 10 部 VM，且只使用其中 5 部，則基本上會浪費一半的購買。 有兩種不同的方式可評估您的使用率：

### <a name="view-unused-ri-costs-in-cost-analysis"></a>在成本分析中檢視未使用的 RI 成本

若要識別您的保留購買目前每個月浪費多少成本，請遵循下列步驟。

1. 在 Azure 入口網站中，導覽至套用保留之範圍內的成本分析。 例如，[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 新增篩選條件，用於 [計價模式：保留]。
1. 選取 [分攤成本] 檢視。
1. 將細微性設定為 [每月]。
1. 將時間週期設定為目前年份或保留期限。
1. 將圖表類型設定為 [資料行 (堆疊)]。
1. 依 [費用類型] 進行群組。
1. 檢閱 `unusedreservation` 值的結果。

[![顯示保留使用的範例](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>檢視保留的使用率

如需詳細指示，請參閱[將保留使用最佳化](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use)。

## <a name="view-costs-for-a-specific-tag"></a>檢視特定標記的成本

許多 Azure 使用者會將標記套用至成本中心或開發環境 (生產和測試) 之類的資源，以利進行費用的分類。 標記在成本分析中會顯示為維度。 您可以使用維度取得自訂標記分類的深入解析。

標記支援適用於將標記套用至資源「之後」所回報的使用量。 您無法追溯套用標記以彙總成本。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 針對您的標記選取 [群組依據]。

[![檢視特定標記的成本](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>下載使用情況詳細資料

您的使用情況詳細資料報表檔案 (CSV 格式) 會針對計入發票的所有費用提供相關明細。 您可以使用此報表作為比較依據，並進一步了解您的發票。 發票上計費的每項費用都會對應於使用情況報告中的明細費用。

1. 在 Azure 入口網站中，瀏覽至計費帳戶或訂用帳戶的 [使用方式和費用] 索引標籤。 例如：[成本管理 + 計費] > [計費] > [使用方式 + 費用]。
1. 選取要下載的明細項目，然後按一下下載符號。  
    [![下載使用方式和費用](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  選取要下載的使用方式檔案。  
    ![選擇要下載的使用方式檔案](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>檢視每月 EA 成本明細

您的 EA 註冊會累計到整個組織的成本中。 了解成本在一段時間內累計及開立發票的方式，可協助您與適當的專案關係人互動，以確保成本受到妥善的管理。

只有使用中的註冊才會顯示成本。 如果已將註冊 (非使用中) 轉移至新的註冊 (使用中)，則不會在成本管理中顯示先前的註冊費用。


1. 在 Azure 入口網站中，瀏覽至 [成本管理 + 計費] > [概觀]。
1. 按一下現行月份的 [明細]，並檢視您的預付金額度。  
    [![EA 成本概觀 - 明細摘要](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  按一下 [使用方式和費用] 索引標籤，然後在選擇的時間範圍內檢視先前月份的明細。  
    [![使用方式和費用索引標籤](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>依期間檢視每月註冊費用

使用註冊的每月成本圖形化檢視，可了解指定期間的成本趨勢和發票金額。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 選取您的註冊並設定註冊期間。
1. 將細微性設定為每月，然後將檢視設定為 [資料行 (堆疊)]。

您可以分組並篩選您的資料，以進行更詳細的分析。

[![依期限顯示的每月註冊費用](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>檢視 EA 註冊累積成本

檢視一段時間內的淨累積費用，以了解您的組織在指定期間內的整體支出。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費] > [成本管理] > [成本分析]。
1. 選取您的註冊，然後檢視目前的累積成本。

[![註冊累積成本](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>後續步驟
- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
- 閱讀[成本管理文件](../index.yml)。
