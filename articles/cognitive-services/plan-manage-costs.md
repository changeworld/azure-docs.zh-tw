---
title: 規劃管理 Azure 認知服務的成本
description: 瞭解如何使用 Azure 入口網站中的成本分析，來規劃和管理 Azure 認知服務的成本。
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 925a092eb67fa459213a37df0fc6b3f7a1b8a0fb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602353"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>規劃和管理 Azure 認知服務的成本

本文說明如何規劃和管理 Azure 認知服務的成本。 首先，您會使用 Azure 定價計算機來協助規劃認知服務成本，然後再為服務新增任何資源以預估成本。 接下來，當您新增 Azure 資源時，請檢查預估成本。 在您開始使用認知服務資源 (例如語音、電腦視覺、LUIS、文字分析 ) 、轉譯程式等）之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。認知服務的成本只是您 Azure 帳單中每月成本的一部分。 雖然本文說明如何規劃和管理認知服務的成本，但您必須支付 Azure 訂用帳戶中使用的所有 Azure 服務和資源的費用，包括協力廠商服務。

## <a name="prerequisites"></a>必要條件

成本管理中的成本分析支援大部分的 Azure 帳戶類型，但並非全部。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本資料，您至少需要 Azure 帳戶的讀取存取權。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>使用認知服務之前估計成本

在您新增認知服務之前，請使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) 來預估成本。

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="適用于認知服務的 Azure 定價計算機" border="true":::

當您將新的資源新增至您的工作區時，請返回此計算機並在此新增相同的資源，以更新您的成本預估。

如需詳細資訊，請參閱 [Azure 認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/)。

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>瞭解認知服務的完整計費模式

認知服務會在 Azure 基礎結構上執行，此基礎結構會在您部署新資源時 [累算成本](https://azure.microsoft.com/pricing/details/cognitive-services/) 。 請務必瞭解，額外的基礎結構可能會產生成本。 當您對已部署的資源進行變更時，必須管理成本。 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>通常會使用認知服務來累積的成本

一般而言，在您部署 Azure 資源之後，成本取決於您對端點所進行的定價層和 API 呼叫。 如果您正在使用的服務具有承諾用量層，則在您的階層中進行分配的呼叫可能會產生超額費用。

使用這些服務時，可能會產生額外的成本：

#### <a name="qna-maker"></a>QnA Maker

當您為 QnA Maker 建立資源時，也可以建立其他 Azure 服務的資源。 包括：

- [執行時間的 Azure App Service () ](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure 認知搜尋 (資料) ](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>資源刪除之後可能會產生的成本

#### <a name="qna-maker"></a>QnA Maker

刪除 QnA Maker 資源之後，可能會繼續存在下列資源。 它們會繼續累積成本，直到您將其刪除為止。

- [執行時間的 Azure App Service () ](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure 認知搜尋 (資料) ](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>使用認知服務的 Azure 預付款點數

您可以使用 Azure 預付金支付認知服務費用， (先前稱為預付金) 點數。 不過，您無法使用 Azure 預付點數來支付協力廠商產品和服務（包括來自 Azure Marketplace 的產品和服務）的費用。

## <a name="create-budgets"></a>建立預算

您可以建立 [預算](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而需要支付額外的費用。 如需建立預算時篩選選項的詳細資訊，請參閱 [群組和篩選選項](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要或其他人對成本進行額外的資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>後續步驟

- 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 深入瞭解如何使用 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
- 瞭解如何避免非 [預期的成本](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 採用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。
