---
title: 規劃和管理 Azure Cosmos DB 的成本
description: 瞭解如何使用 Azure 入口網站中的成本分析來規劃和管理 Azure Cosmos DB 的成本。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: a73d155d20d09573ad5e1b2597678291aea4c92b
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980570"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>規劃和管理 Azure Cosmos DB 的成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文說明如何規劃和管理 Azure Cosmos DB 的成本。 首先，您會使用 Azure Cosmos DB 容量計算機來估計您的工作負載成本，然後再建立任何資源。 您稍後可以檢查預估成本，並開始建立您的資源。

當您開始使用 Azure Cosmos DB 資源之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。Azure Cosmos DB 的成本只是您 Azure 帳單中的每月成本的一部分。 雖然本文說明如何規劃和管理 Azure Cosmos DB 的成本，但您必須支付 Azure 訂用帳戶中使用的所有 Azure 服務和資源的費用，包括協力廠商服務。

## <a name="prerequisites"></a>必要條件

### <a name="provisioned-throughput-or-serverless"></a>布建的輸送量或無伺服器

Azure Cosmos DB 支援兩種類型的容量模式：布 [建的輸送量](set-throughput.md) 和 [無伺服器](serverless.md)。 您對 Azure Cosmos DB 使用量收費的方式在這兩種模式之間會有很大的差異，因此請務必選擇最適合您工作負載的方式。 請參閱 [如何選擇布建的輸送量和無伺服器](throughput-serverless.md) 文章，以取得有關如何進行這項選擇的指引和建議。

### <a name="cost-analysis"></a>成本分析

成本管理中的成本分析支援大部分的 Azure 帳戶類型，但並非全部。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本資料，您至少需要 Azure 帳戶的讀取存取權。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 之前，先預估布建的輸送量成本

如果您打算在布建的輸送量模式中使用 Azure Cosmos DB，請先使用 [Azure Cosmos DB 容量計算機](https://cosmos.azure.com/capacitycalculator/) 來估計成本，然後再于 Azure Cosmos 帳戶中建立資源。 容量計算機可讓您估計所需的輸送量和工作負載的成本。 將您的 Azure Cosmos 資料庫和容器設定為具有適當數量的已布建輸送量，或 [要求單位 (RU/秒) ](request-units.md)，以將成本和效能優化是不可或缺的工作負載。 您必須輸入詳細資料，例如 API 類型、區域數目、專案大小、每秒讀取/寫入要求數、儲存的資料總計，以取得成本預估。 若要深入瞭解容量計算機，請參閱 [估價](estimate-ru-with-capacity-planner.md) 文章。

下列螢幕擷取畫面顯示使用容量計算機的輸送量和成本估計：

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Azure Cosmos DB 容量計算機中的成本預估":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> 使用 Azure Cosmos DB 之前，先預估無伺服器成本

如果您打算在無伺服器模式中使用 Azure Cosmos DB，您需要預估每月可能耗用的 [要求單位](request-units.md) 和 GB 儲存體數量。 您可以評估每個月發出的資料庫作業數目，並將其數量乘以對應的 RU 成本，以估計所需的要求單位數量。 下表列出一般資料庫作業的估計 RU 費用：

| 作業 | 估計成本 | 注意 |
| --- | --- | --- |
| 建立項目 | 5 RU | 1 KB 專案中索引小於5個屬性的平均成本 |
| 更新項目 | 10 RU | 1 KB 專案中索引小於5個屬性的平均成本 |
| 依識別碼和分割區索引鍵讀取個別專案， (點讀取)  | 1 RU | 1 KB 專案的平均成本 |
| 刪除項目 | 5 RU | |
| 執行查詢 | 10 RU | 查詢的平均成本，可充分利用 [索引編制](index-overview.md) ，並傳回100結果或更少 |

> [!IMPORTANT] 
> 請注意上表中的附注。 若要更精確地估計作業的實際成本，您可以使用 [Azure Cosmos 模擬器](local-emulator.md) ，並 [測量作業的確切 RU 成本](find-request-unit-charge.md)。 雖然 Azure Cosmos 模擬器不支援無伺服器，但它會報告資料庫作業的標準 RU 費用，並且可用於此估計。

當您計算出每個月可能耗用的要求單位數和 GB 儲存體總數時，下列公式會傳回您的成本預估： **( [要求單位數目]/1000000 * $0.25) + ( [儲存空間] * $0.25)**。

> [!NOTE]
> 上述範例所示的成本僅供示範之用。 如需最新定價資訊，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/) 。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 入口網站中檢閱預估成本

當您開始使用 Azure 入口網站 Azure Cosmos DB 資源時，可以看到預估成本。 使用下列步驟來檢查成本預估：

1. 登入 Azure 入口網站，然後流覽至您的 Azure Cosmos 帳戶。
1. 移至 [ **總覽** ] 區段。
1. 檢查底部的 **成本** 圖表。 此圖表會在可設定的時間週期內，顯示您目前成本的預估：
1. 建立新的容器，例如圖形容器。
1. 輸入您的工作負載所需的輸送量，例如 400 RU/秒。 輸入輸送量值之後，您可以看到價格估計值，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Azure 入口網站中的成本預估":::

如果您的 Azure 訂用帳戶有消費限制，Azure 會讓您無法消費于您的點數金額。 當您建立和使用 Azure 資源時，會使用您的點數。 當您達到點數限制時，就會在該計費期間的剩餘時間內停用您所部署的資源。 您無法變更點數限制，但可以將它移除。 如需消費限制的詳細資訊，請參閱 [Azure 消費限制](https://docs.microsoft.com/azure/billing/billing-spending-limit)。

您可以使用 Azure Enterprise 合約預付金點數來支付 Azure Cosmos DB 費用。 不過，您無法使用預付金點數來支付協力廠商產品和服務的費用，包括來自 Azure Marketplace 的產品和服務。

## <a name="monitor-costs"></a>監視成本

當您搭配 Azure Cosmos DB 使用資源時，會產生成本。 資源使用量單位成本會隨著時間間隔而有所不同， (秒、分鐘、小時和日) 或依要求單位使用量而有所不同。 一旦 Azure Cosmos DB 開始使用，就會產生成本，而您可以在 Azure 入口網站的 [ [成本分析](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ] 窗格中看到成本。

當您使用成本分析時，可以在不同時間間隔的圖表和資料表中查看 Azure Cosmos DB 成本。 某些範例是依日、目前、上個月和年。 您也可以根據預算和預測成本來查看成本。 在一段時間內切換至更長的觀點，可協助您找出花費趨勢，並查看可能發生超支的位置。 如果您已建立預算，也可以輕鬆地查看其超出的位置。 

若要查看成本分析中的 Azure Cosmos DB 成本：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 開啟 Azure 入口網站中的範圍，然後選取功能表中的 [ **成本分析** ]。 例如，移至 [訂用帳戶 **]，從** 清單中選取訂用帳戶，然後選取功能表中的 [  **成本分析** ]。 選取 [ **範圍** ]，以切換至成本分析中的不同範圍。

1. 依預設，所有服務的成本都會顯示在第一個環圈圖中。 選取標示為 "Azure Cosmos DB" 的圖表區域。

1. 若要縮小單一服務（例如 Azure Cosmos DB）的成本，請選取 [ **新增篩選** ]，然後選取 [ **服務名稱**]。 然後，從清單中選擇 [ **Azure Cosmos DB** ]。 以下範例顯示只 Azure Cosmos DB 的成本：
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="使用成本分析窗格來監視成本":::

在上述範例中，您會看到二月的目前 Azure Cosmos DB 成本。這些圖表也包含依位置和依資源群組 Azure Cosmos DB 的成本。

## <a name="create-budgets"></a>建立預算

您可以建立 [預算](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而需要支付額外的費用。 如需建立預算時篩選選項的詳細資訊，請參閱 [群組和篩選選項](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要或其他人對成本進行額外的資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以深入瞭解定價在 Azure Cosmos DB 中的運作方式：

* [Azure Cosmos DB 中的計價模式](how-pricing-works.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](./optimize-cost-reads-writes.md)
* [在 Azure Cosmos DB 中最佳化儲存體成本](optimize-cost-storage.md)
* 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
* 深入瞭解如何使用 [成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
* 瞭解如何避免非 [預期的成本](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
* 採用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。
