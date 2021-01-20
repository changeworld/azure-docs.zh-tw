---
title: 規劃管理 Azure ExpressRoute 的成本
description: 瞭解如何使用 Azure 入口網站中的成本分析，來規劃和管理 Azure ExpressRoute 的成本。
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 9b74f6e82e39955554c13f6ce3490bc3c22c2b98
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600465"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>規劃和管理 Azure ExpressRoute 的成本

本文說明如何規劃和管理 ExpressRoute 的成本。 首先，您會使用 Azure 定價計算機來協助規劃 ExpressRoute 成本，然後再為服務新增任何資源以估計成本。 然後，當您新增 Azure 資源時，請檢查預估成本。 

在您開始使用 ExpressRoute 資源之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。 

請記住，ExpressRoute 的成本只是您 Azure 帳單中的每月成本的一部分。 雖然本文說明如何規劃和管理 ExpressRoute 的成本，但您必須支付 Azure 訂用帳戶中使用的所有 Azure 服務和資源的費用，包括協力廠商服務。

## <a name="prerequisites"></a>必要條件

成本管理中的成本分析支援大部分的 Azure 帳戶類型，但並非全部。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本資料，您至少需要 Azure 帳戶的讀取存取權。 

如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="local-vs-standard-vs-premium"></a>本機與標準與 Premium 的比較

ExpressRoute 有三個不同的線路 SKU： [*Local*](./expressroute-faqs.md#expressroute-local)、 *Standard* 和 [*Premium*](./expressroute-faqs.md#expressroute-premium)。 這三種 SKU 類型的計費方式會隨您的 ExpressRoute 使用量而有所不同。 使用本機 SKU 時，會自動向您收取無限制的資料方案。 使用 Standard 和 Premium SKU 時，您可以在計量付費或無限制的資料方案之間進行選擇。 除了使用「全球接觸」附加元件時，所有輸入資料都是免費的。 請務必瞭解哪些 SKU 類型和資料方案最適合您的工作負載，以充分發揮成本與預算的最大效益。

## <a name="estimate-costs"></a>預估成本

在您建立 ExpressRoute 線路之前，請使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) 來預估成本。 

1. 選取左側的 [ **網路**]，然後選取 [ **Azure ExpressRoute** ] 以開始。 

1. 根據您的對等互連位置，選取適當的 *區域* 。 請參閱 [ExpressRoute 連線提供者](./expressroute-locations-providers.md#partners) ，以在下拉式清單中選取適當的 *區域* 。 

1. 然後選取您想要評估的 *SKU*、 *線路速度* 和 *資料方案* 。 

1. 在 [ *其他輸出資料傳輸*] 中，輸入您在一個月的課程中可能使用的輸出資料量（GB）。 

1. 最後，您可以將「 *全球接觸」附加* 元件新增至估價。

下列螢幕擷取畫面顯示使用計算機的成本估計：

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 計算機中的 ExpressRoute 成本預估":::

如需詳細資訊，請參閱 [Azure ExpressRoute 定價](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="expressroute-gateway-estimated-cost"></a>ExpressRoute 閘道估計成本

如果您要使用 ExpressRoute 閘道將虛擬網路連結至 ExpressRoute 線路，請使用下列步驟來估計閘道使用量的成本。

1. 選取左側的 [ **網路**]，然後選取 [ **VPN 閘道** ] 以開始。 

1. 選取閘道的 *區域* ，然後將 *類型* 變更為 **ExpressRoute 閘道**。

1. 從下拉式清單中選取 *閘道類型* 。

1. 輸入 *閘道時數*。  (720 小時 = 30 天) 

## <a name="understand-the-full-billing-model-for-expressroute"></a>瞭解 ExpressRoute 的完整計費模型

ExpressRoute 會在 Azure 基礎結構上執行，此基礎結構會在您部署新的資源時，使用 ExpressRoute 來計算成本。 請務必瞭解，額外的基礎結構可能會產生成本。 當您對已部署的資源進行變更時，必須管理成本。 

### <a name="costs-that-typically-accrue-with-expressroute"></a>通常使用 ExpressRoute 累積的成本

當您建立 ExpressRoute 線路時，您可能會選擇建立 ExpressRoute 閘道，以將虛擬網路連結至線路。 閘道會依每小時費率計費，加上 ExpressRoute 電路的成本。 請參閱 [expressroute 定價](https://azure.microsoft.com/en-us/pricing/details/expressroute) 並選取 Expressroute 閘道，以查看不同閘道 sku 的費率。
 
### <a name="costs-might-accrue-after-resource-deletion"></a>資源刪除之後可能會累積成本

如果您在刪除 ExpressRoute 線路之後有 ExpressRoute 閘道，您仍需支付成本的費用，直到您將其刪除為止。

### <a name="using-azure-prepayment-credit"></a>使用 Azure 預付點數

您可以使用 Azure 預付金來支付 ExpressRoute 費用 (先前稱為預付金) 點數。 不過，您無法使用 Azure 預付點數來支付協力廠商產品和服務的費用，包括來自 Azure Marketplace 的產品和服務。

## <a name="monitor-costs"></a>監視成本

當您將 Azure 資源與 ExpressRoute 搭配使用時，會產生費用。 Azure 資源使用量單位成本會隨著時間間隔而改變 (秒、分鐘、小時和日) 或依單位使用量 (位元組、mb 等等。當 ExpressRoute 使用開始時，會立即 ) ，而您可以在 [成本分析](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到成本。

當您使用成本分析時，您會在圖表和資料表中，以不同的時間間隔來查看 ExpressRoute 電路成本。 某些範例是依日期、目前和先前月份，以及年度。 您也可以根據預算和預測成本來查看成本。 在一段時間內切換至更長的觀點，可協助您找出花費趨勢。 您會看到超支可能發生的位置。 如果您已建立預算，也可以輕鬆地查看其已超出的位置。

若要在成本分析中查看 ExpressRoute 成本：

1. 登入 Azure 入口網站。

1. 移至 [訂用帳戶 **]，從** 清單中選取訂用帳戶，然後選取功能表中的 [  **成本分析** ]。 選取 [ **範圍** ]，以切換至成本分析中的不同範圍。 依預設，服務的成本會顯示在第一個環圈圖中。

    當您一開始開啟成本分析時，就會顯示實際的每月費用。 以下是顯示所有每月使用量成本的範例。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="顯示訂用帳戶累計成本的範例":::
    

1.  若要縮小單一服務（例如 Expressroute）的成本，請選取 [ **新增篩選** ]，然後選取 [ **服務名稱**]。 然後選取 [ **ExpressRoute**]。

    以下範例顯示只是 ExpressRoute 的成本。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="顯示 ExpressRoute 累積成本的範例":::

在上述範例中，您會看到服務的目前成本。 依資源群組的 Azure 區域 (位置) 和 ExpressRoute 成本的成本也會顯示。 您可以從這裡自行探索成本。

## <a name="create-budgets-and-alerts"></a>建立預算與警示

您可以建立 [預算](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而需要支付額外的費用。 如需建立預算時篩選選項的詳細資訊，請參閱 [群組和篩選選項](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要或其他人對成本進行額外的資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 Azure ExpressRoute 的定價方式。 請參閱 [Azure ExpressRoute 總覽定價](https://azure.microsoft.com/en-us/pricing/details/expressroute/)。
- 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 深入瞭解如何使用 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
- 瞭解如何避免非 [預期的成本](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 採用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。
