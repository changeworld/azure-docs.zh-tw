---
title: 規劃管理 Azure Synapse Analytics 成本
description: 瞭解如何使用 Azure 入口網站中的成本分析來規劃和管理 Azure Synapse Analytics 的成本。
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: c7a0be6f1d402cc994532ab4bc5a5d0ea39bc8b7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599043"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>規劃和管理 Azure Synapse Analytics 的成本

本文說明如何規劃和管理 Azure Synapse Analytics 的成本。 首先，您會使用 Azure 定價計算機來協助規劃 Azure Synapse 成本，然後再為服務新增任何資源以預估成本。 接下來，當您新增 Azure Synapse 資源時，請檢查預估成本。

在您開始使用 Azure Synapse 資源之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。Azure Synapse 的成本只是您 Azure 帳單中每月成本的一部分。 雖然本文說明如何規劃和管理 Azure Synapse 的成本，但您必須支付 Azure 訂用帳戶中使用的所有 Azure 服務和資源的費用，包括協力廠商服務。

## <a name="prerequisites"></a>必要條件

成本管理中的成本分析支援大部分的 Azure 帳戶類型，但並非全部。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本資料，您至少需要 Azure 帳戶的讀取存取權。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 前先預估成本

在您新增 Azure Synapse Analytics 之前，請使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) 來預估成本。

Azure Synapse 有各種不同費用的資源，如下列成本預估中所示。 

![顯示 Azure 定價計算機中預估成本的範例](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>瞭解 Azure Synapse Analytics 的完整計費模型

當您部署新的資源時，azure Synapse 會在 Azure 基礎結構上執行，以在 Azure Synapse 中進行累算成本。 請務必瞭解，額外的基礎結構可能會產生成本。 當您對已部署的資源進行變更時，必須管理成本。 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>通常會隨 Azure Synapse Analytics 累積的成本

當您建立 Azure Synapse 的資源時，也會建立其他 Azure 服務的資源。 包括：

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>資源刪除之後可能會累積成本

刪除 Azure Synapse 資源之後，下列資源可能會繼續存在。 它們會繼續累積成本，直到您將其刪除為止。

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>使用 azure Synapse 的 Azure 預付款點數 

您可以使用 Azure 預付金支付 Azure Synapse 費用 (先前稱為預付金) 點數。 不過，您無法使用 Azure 預付點數來支付協力廠商產品和服務（包括來自 Azure Marketplace 的產品和服務）的費用。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 入口網站中檢閱預估成本

當您為 Azure Synapse Analytics 建立資源時，您會看到估計成本。 工作區有一個使用工作區建立的無伺服器 SQL 集區。 在您執行查詢之前，無伺服器 SQL 集區不會產生費用。 您必須在工作區中建立其他資源（例如專用的 SQL 集區和無伺服器 Apache Spark 集區）。

若要建立 <ResourceName> 並查看預估價格：

1. 流覽至 Azure 入口網站中的服務。
2. 建立資源。
3. 查看摘要中顯示的預估價格。
4. 完成資源的建立。

![在建立資源時顯示預估成本的範例](./media/plan-manage-costs/create-workspace-cost.png)


如果您的 Azure 訂用帳戶有消費限制，Azure 會讓您無法消費于您的點數金額。 當您建立和使用 Azure 資源時，會使用您的點數。 當您達到點數限制時，就會在該計費期間的剩餘時間內停用您所部署的資源。 您無法變更點數限制，但可以將它移除。 如需消費限制的詳細資訊，請參閱 [Azure 消費限制](../cost-management-billing/manage/spending-limit.md)。

## <a name="monitor-costs"></a>監視成本

當您使用 Azure Synapse 資源時，會產生費用。 Azure 資源使用量單位成本會隨著時間間隔而改變 (秒、分鐘、小時和日) 或依單位使用量 (位元組、mb 等等 ) 。當您開始使用 Azure Synapse 中的資源時，就會產生費用，而您可以在 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到成本。

當您使用成本分析時，可以在不同時間間隔的圖表和資料表中，查看 Azure Synapse Analytics 的成本。 某些範例是依日期、目前和先前月份，以及年度。 您也可以根據預算和預測成本來查看成本。 在一段時間內切換至更長的觀點，可協助您找出花費趨勢。 您會看到超支可能發生的位置。 如果您已建立預算，也可以輕鬆地查看其已超出的位置。

若要在成本分析中查看 Azure Synapse 成本：

1. 登入 Azure 入口網站。
2. 在 Azure 入口網站中開啟訂用帳戶或資源群組的範圍，然後選取功能表中的 [ **成本分析** ]。 例如，移至 [訂用帳戶 **]，從** 清單中選取訂用帳戶，然後選取功能表中的 [  **成本分析** ]。 選取 [ **範圍** ]，以切換至成本分析中的不同範圍。
3. 依預設，服務的成本會顯示在第一個環圈圖中。 選取標示為 Azure Synapse 的圖表區域。

當您一開始開啟成本分析時，就會顯示實際的每月費用。 以下是顯示所有每月使用量成本的範例。

![顯示訂用帳戶累計成本的範例](./media/plan-manage-costs/actual-monthly-costs.png)

- 若要縮小單一服務（例如 Azure Synapse）的成本，請選取 [ **新增篩選** ]，然後選取 [ **服務名稱**]。 然後，選取 [ **Azure Synapse Analytics**]。

以下範例只顯示 Azure Synapse 的成本。

![顯示 ServiceName 累計成本的範例](./media/plan-manage-costs/filtered-monthly-costs.png)

在上述範例中，您會看到服務的目前成本。 依資源群組的 Azure 區域 (位置) 和 Azure Synapse 成本的成本也會顯示。 您可以從這裡自行探索成本。

## <a name="create-budgets"></a>建立預算

您可以建立 [預算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而需要支付額外的費用。 如需建立預算時篩選選項的詳細資訊，請參閱 [群組和篩選選項](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要或其他人對成本進行額外的資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>其他管理和降低 Azure Synapse 成本的方式 

### <a name="serverless-sql-pool"></a>無伺服器 SQL 集區

若要深入瞭解無伺服器 SQL 集區的成本，請參閱 [Azure Synapse Analytics 中無伺服器 sql 集區的成本管理](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>專用的 SQL 集區

您可以在資源未使用時暫停資源，藉以控制專用 SQL 集區的成本。 例如，如果您在夜間和週末不會使用資料庫，可以在這段時間暫停，並且在白天時繼續。 如需詳細資訊，請參閱透過 [Azure 入口網站暫停和繼續專用 SQL 集區中的計算](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>資料整合-管線與資料流程 

若要深入瞭解資料整合成本，請參閱 [規劃和管理 Azure Data Factory 的成本](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>後續步驟

- 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 深入瞭解如何使用 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
- 瞭解如何避免非 [預期的成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 採用 [成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。
- 瞭解[Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)的規劃和管理成本