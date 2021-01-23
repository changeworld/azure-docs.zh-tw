---
title: 規劃和管理 Azure API 管理的成本
description: 瞭解如何使用 Azure 入口網站中的成本分析，來規劃和管理 Azure API 管理的成本。
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: e171e642440b7c6c99353169e426a722885f1bcf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725932"
---
# <a name="plan-and-manage-costs-for-api-management"></a>規劃和管理 API 管理的成本

本文說明如何規劃和管理 Azure API 管理的成本。 首先，您會使用 Azure 定價計算機來協助規劃 API 管理成本，然後再為服務新增任何資源以估計成本。 在您開始使用 API 管理資源之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。 

API 管理的成本只是您的 Azure 帳單中的每月成本的一部分。 雖然本文說明如何規劃和管理 API 管理的成本，但您必須針對 Azure 訂用帳戶中使用的所有 Azure 服務和資源（包括協力廠商服務）支付費用。

## <a name="prerequisites"></a>Prerequisites

成本管理中的成本分析支援大部分的 Azure 帳戶類型，但並非全部。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本資料，您至少需要 Azure 帳戶的讀取存取權。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-api-management"></a>使用 API 管理之前預估成本

在您新增 API 管理之前，請先使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) 來預估成本。 

1. 搜尋 *API 管理*，或選取 [**整合**  >  **api 管理**]。
1. 選取 [ **View** ] 以新增「API 管理」服務實例的預設成本預估值。

> [!NOTE]
> 此範例中顯示的成本僅供示範之用。 如需最新定價資訊，請參閱 [API 管理定價](https://azure.microsoft.com/pricing/details/api-management/) 。

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="估計開發人員層的成本":::

* 預設成本預估是以 **開發人員**[服務層級](api-management-features.md)中有1個 [容量單位](api-management-capacity.md)的 API 管理服務實例為基礎。 開發人員層適用于非生產環境的使用案例和評估。 它不支援服務等級協定。

* 若要估計額外容量單位或不同服務層級的成本，請從 [**單位****和階層**] 下拉式清單中選取其他選項。

* 視功能可用性和服務層級而定，可能需要額外費用才能使用 [自我裝載的閘道](self-hosted-gateway-overview.md)。

如需其他定價和功能詳細資料，請參閱：

* [API 管理定價](https://azure.microsoft.com/pricing/details/api-management/)
* [Azure API 管理層的功能式比較](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>透過 API 管理使用貨幣點數

您可以使用您的 Azure 預付款 (先前稱為預付金) 來支付 API 管理費用。 不過，您無法使用 Azure 預付點數來支付協力廠商產品和服務的費用，包括來自 Azure Marketplace 的產品和服務。

## <a name="monitor-costs"></a>監視成本

當您使用 Azure 資源搭配 API 管理時，會產生費用。 Azure 資源使用量單位成本會隨著時間間隔而改變 (秒、分鐘、小時和日) 或依單位使用量 (位元組、mb 等) 。 一旦開始使用 API 管理，就會產生成本，而您可以在 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到成本。

當您使用成本分析時，您會在圖表和資料表中查看不同時間間隔的 API 管理成本。 某些範例是依日期、目前和先前月份，以及年度。 您也可以根據預算和預測成本來查看成本。 在一段時間內切換至更長的觀點，可協助您找出花費趨勢。 您會看到超支可能發生的位置。 如果您已建立預算，也可以輕鬆地查看其已超出的位置。

> [!NOTE]
> 此範例中顯示的成本僅供示範之用。 您的成本會依資源使用量和目前定價而有所不同。

若要在成本分析中查看 API 管理成本：

1. 登入 [Azure 入口網站](https://azure.microsoft.com)。
1. 開啟 [ **成本管理 + 計費** ] 視窗，從功能表中選取 [ **成本管理** ]，然後選取 **計費範圍**。 例如，從清單中選取訂用帳戶。
1. 從功能表選取 [ **成本管理** ]，然後選取 [ **成本分析**]。
1. 依預設，所有服務的每月成本都會顯示在第一個環圈圖中。 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="訂用帳戶的每月費用":::

1. 若要縮小單一服務（例如 API 管理）的成本，請選取 [ **新增篩選** ]，然後選取 [ **服務名稱**]。 然後，選取 [ **API 管理**]。

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="顯示 API 管理累計成本的範例":::

在上述範例中，您會看到服務的目前成本。 依資源群組的 Azure 區域 (位置) 和 API 管理成本的成本也會顯示。 您可以從這裡自行探索成本。

## <a name="create-budgets"></a>建立預算

您可以建立 [預算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而需要支付額外的費用。 如需建立預算時篩選選項的詳細資訊，請參閱 [群組和篩選選項](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要其他人對成本進行額外的資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>管理和降低 API 管理成本的其他方式

### <a name="choose-tier"></a>選擇階層

請參閱 [AZURE API 管理層的功能式比較](api-management-features.md) ，以協助決定哪一個服務層級適用于您的案例。 不同的服務層級支援針對各種使用案例設計的特性和功能組合（具有不同的成本）。 隨時[升級](upgrade-and-scale.md)至不同的服務層級。

* 取用 **服務層** 提供輕量、無伺服器的選項，不會產生任何固定成本。 系統會根據對服務的 API 呼叫數目，以超過特定閾值的費率向您收費。 容量也會根據服務的負載自動調整。
* 其他 API 管理層會產生每月成本，並為評估和生產工作負載提供更高的輸送量和更豐富的功能集。

### <a name="scale-using-capacity-units"></a>使用容量單位進行調整

除了取用服務層級，API 管理支援藉由新增或移除 [*容量單位*](api-management-capacity.md)來進行調整。 隨著 API 管理實例的負載增加，增加容量單位的成本可能比升級至更高的服務層級更經濟實惠。 單位數目上限取決於服務層級。

每個容量單位都有特定的要求處理功能，取決於服務的層級。 例如，基本層的單位有大約每秒1000要求的估計最大輸送量。 

當您新增或移除單位時，依比例調整容量和成本。 例如，標準層的兩個單位提供大約每秒2000要求的預估輸送量。 實際的輸送量可能會因要求或回應的大小、連線模式、提出要求的用戶端數目，以及其他因素而有所不同。

[監視](api-management-howto-use-azure-monitor.md) api 管理實例的容量計量，以協助決定是否要調整或升級 api 管理實例以容納更多負載。

## <a name="next-steps"></a>後續步驟

- 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 深入瞭解如何使用 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
- 瞭解如何避免非 [預期的成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 採用 [成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。
- 深入瞭解 API 管理 [容量](api-management-capacity.md)。
- 請參閱使用[Azure 入口網站](upgrade-and-scale.md)來調整及升級 API 管理的步驟，[並瞭解自動調整規模。](api-management-howto-autoscale.md)