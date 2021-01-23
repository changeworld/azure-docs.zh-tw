---
title: 規劃和管理 Azure SQL Database 的成本
description: 瞭解如何使用 Azure 入口網站中的成本分析來規劃和管理 Azure SQL Database 的成本。
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734625"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>規劃和管理 Azure SQL Database 的成本

本文說明如何規劃和管理 Azure SQL Database 的成本。 首先，請使用 Azure 定價計算機來新增 Azure 資源，並查看預估成本。 當您開始使用 Azure SQL Database 資源之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。Azure SQL Database 的成本只是您 Azure 帳單中的每月成本的一部分。 雖然本文說明如何規劃和管理 Azure SQL Database 的成本，但您必須支付 Azure 訂用帳戶中使用的所有 Azure 服務和資源的費用，包括任何協力廠商服務。


## <a name="prerequisites"></a>Prerequisites

成本分析支援大部分的 Azure 帳戶類型，但並非全部。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本資料，您至少需要 Azure 帳戶的讀取存取權。 

如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。


## <a name="sql-database-initial-cost-considerations"></a>SQL Database 初始成本考慮

使用 Azure SQL Database 時，需要考慮幾項節省成本的功能：


### <a name="vcore-or-dtu-purchasing-models"></a>vCore 或 DTU 購買模型 

Azure SQL Database 支援兩種購買模型： vCore 和 DTU。 購買模型的計費方式會有所不同，因此在規劃和考慮成本時，請務必瞭解最適合您工作負載的模型。 如需 vCore 和 DTU 購買模型的相關資訊，請參閱 [選擇 vCore 和 dtu 購買模型](purchasing-models.md)。


### <a name="provisioned-or-serverless"></a>已布建或無伺服器

在 vCore 購買模型中，Azure SQL Database 也支援兩種類型的計算層級：布建的輸送量和無伺服器。 每個計算層級的收費方式各異，因此請務必瞭解在規劃和考慮成本時，最適合您工作負載的方式。 如需詳細資訊，請參閱 [vCore 模型總覽-計算層級](service-tiers-vcore.md#compute-tiers)。

在以 vCore 為基礎的購買模型的已布建計算層中，您可以交換現有的授權以取得折扣費率。 如需詳細資訊，請參閱 [Azure Hybrid Benefit (AHB) ](../azure-hybrid-benefit.md)。

### <a name="elastic-pools"></a>彈性集區

若環境具有具有不同和無法預測之使用需求的多個資料庫，彈性集區可以節省成本，相較于布建相同數量的單一資料庫。 如需詳細資訊，請參閱 [彈性](elastic-pool-overview.md)集區。

## <a name="estimate-azure-sql-database-costs"></a>預估 Azure SQL Database 成本

使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) 來預估不同 Azure SQL Database 設定的成本。 下圖中的資訊和價格僅供範例之用：

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Azure SQL Database 定價計算機範例":::

您也可以估計不同的保留原則選項對成本有何影響。 下圖中的資訊和價格僅供範例之用：

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="適用于儲存體的 Azure SQL Database 定價計算機範例":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>瞭解 Azure SQL Database 的完整計費模型

Azure SQL Database 會在 Azure 基礎結構上執行，此基礎結構會在您部署新的資源時，與 Azure SQL Database 一起計算成本。 請務必瞭解，額外的基礎結構可能會產生成本。 當您對已部署的資源進行變更時，必須管理成本。 


無伺服器) 的 Azure SQL Database (會以可預測的每小時費率計費。 如果 SQL 資料庫使用時間不到一小時，則會使用選取的最高服務層級、在該小時內所套用的布建儲存體和 IO （不論使用方式或資料庫是否使用時間不到一小時），以每小時計費。


### <a name="using-monetary-credit-with-azure-sql-database"></a>搭配 Azure SQL Database 使用貨幣點數

您可以使用 Azure 預付金支付 Azure SQL Database 費用 (之前稱為預付金) 點數。 不過，您無法使用 Azure 預付點數來支付協力廠商產品和服務（包括來自 Azure Marketplace 的產品和服務）的費用。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 入口網站中檢閱預估成本

當您完成建立 Azure SQL Database 的程式時，您可以在計算層的設定期間看到估計成本。 

若要存取此畫面，請在 [**建立 SQL Database** ] 頁面的 [**基本**] 索引標籤上選取 [**設定資料庫**]。 下圖中的資訊和價格僅供範例之用：

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="顯示 Azure 入口網站中成本預估的範例":::



如果您的 Azure 訂用帳戶有消費限制，Azure 會讓您無法消費于您的點數金額。 當您建立和使用 Azure 資源時，會使用您的點數。 當您達到點數限制時，就會在該計費期間的剩餘時間內停用您所部署的資源。 您無法變更點數限制，但可以將它移除。 如需消費限制的詳細資訊，請參閱 [Azure 消費限制](../../cost-management-billing/manage/spending-limit.md)。

## <a name="monitor-costs"></a>監視成本

當您開始使用 Azure SQL Database 時，您可以在入口網站中看到估計成本。 使用下列步驟來檢查成本預估：

1. 登入 Azure 入口網站，然後流覽至您的 Azure SQL 資料庫資源群組。 您可以流覽至您的資料庫，然後選取 [**總覽**] 區段中的 [**資源群組**]，以找出資源群組。
1. 在功能表中，選取 [ **成本分析**]。
1. 查看 **累積成本** ，並將圖表設定在 **服務名稱** 的底部。 此圖表會顯示您目前 SQL Database 成本的預估。 若要縮小整頁 Azure SQL Database 的成本，請選取 [ **加入篩選** ]，然後選取 [ **Azure SQL Database**]。 下圖中的資訊和價格僅供範例之用：

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="顯示 Azure 入口網站中累積成本的範例":::

您可以從這裡自行探索成本。 如需有關不同成本分析設定的詳細資訊和詳細資訊，請參閱 [開始分析成本](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="create-budgets"></a>建立預算

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

您可以建立 [預算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而需要支付額外的費用。 如需建立預算時篩選選項的詳細資訊，請參閱 [群組和篩選選項](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要或其他人對成本進行額外的資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>管理和降低 Azure SQL Database 成本的其他方式

Azure SQL Database 也可讓您相應增加或減少資源，以根據您的應用程式需求來控制成本。 如需詳細資料，請參閱 [動態調整資料庫資源](scale-resources.md)。

將計算資源的保留承諾一到三年，以節省成本。 如需詳細資訊，請參閱為 [具有保留容量的資源節省成本](reserved-capacity-overview.md)。


## <a name="next-steps"></a>後續步驟

- 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 深入瞭解如何使用 [成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
- 瞭解如何避免非 [預期的成本](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 採用 [成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。