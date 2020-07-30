---
title: 判斷您應購買的 Azure 保留
description: 本文可協助您判斷所應購買的保留。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 9e5657211e640c741dbf1bf2b5473a3ea5e10487
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287299"
---
# <a name="determine-what-reservation-to-purchase"></a>決定您要購買的保留

除了 Azure Databricks 以外，所有保留都會以小時為單位套用。 您應根據一致的基礎使用量來購買保留。 有多種方式可以決定您要購買的項目，而本文可協助您判斷所應購買的保留。

若購買高於歷史使用量的容量，會導致保留的使用量過低。 您應該盡可能避免使用量過低。 未使用的保留容量挪移至下一個小時繼續執行。 超過保留數量的使用量會以較昂貴的隨用隨付費率計費。

## <a name="analyze-usage-data"></a>分析使用量資料

使用下列各節可協助您分析每日使用量資料，以判斷您的基準使用量和所應購買的保留。

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>分析使用量而據以購買 VM 保留執行個體

在購買前識別正確的 VM 大小。 例如，針對 ES 系列 VM 購買的保留並不適用於 E 系列 VM，反之亦然。

促銷系列 VM 不會獲得保留折扣，因此請將其從您的分析中移除。

若要縮小為合格 VM 使用量的範圍，請將下列篩選條件套用至您的使用量資料：

- 將 **MeterCategory** 篩選為 [虛擬機器]  。
- 從 **AdditionalInfo** 中取得 **ServiceType** 資訊。 這項資訊會建議正確的 VM 大小。 例如，標準 E32。
- 使用 **ResourceLocation** 欄位來判斷使用量資料中心。

請略過每天的使用量少於 24 小時的資源。

如果您想要在執行個體大小的系列層級進行分析，您可以從 [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv) 取得執行個體大小彈性值。 將值與您的資料結合，以進行分析。 如需執行個體大小彈性的詳細資訊，請參閱[利用保留的 VM 執行個體獲得虛擬機器大小彈性](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>分析 Azure Synapse Analytics 保留執行個體購買的使用量

保留容量適用於 Azure Synapse Analytics DWU 定價。 其不適用於 Azure Synapse Analytics 授權成本，或計算以外的任何成本。

若要縮小合格使用量的範圍，請對您的使用量資料套用下列篩選條件：


- 將 **MeterCategory** 篩選為 **SQL Database**。
- 將 **MeterName** 篩選為 [虛擬核心]  的。
- 針對所有在名稱中有 _Compute_ 的使用量記錄，篩選 **MeterSubCategory**。

從 **AdditionalInfo** 中取得 [虛擬核心]  值。 此值會指出已使用的虛擬核心數目。 數量是**虛擬核心**乘以已使用資料庫的時數。

這項資料會為您指出下列項目的一致使用量：

- 資料庫類型的組合。 例如，每個單一資料庫的受控執行個體或彈性集區。
- 服務層。 例如，一般用途或業務關鍵。
- 世代。 例如，第 5 代。
- 資源位置

### <a name="analysis-for-azure-synapse-analytics"></a>Azure Synapse Analytics 的分析

保留容量適用於 Azure Synapse Analytics DWU 使用量，可以 100 DWU 為單位累加購買。 若要縮小合格使用量的範圍，請對您的使用量資料套用下列篩選條件：

- 將 **MeterName** 篩選為 **100 DWU**。
- 將 [計量子類別]  篩選為 [計算最佳化第 2 代]  。

使用 [資源位置]  欄位來判斷 Azure Synapse Analytics 在某個區域中的使用量。

Azure Synapse Analytics 使用量可以在一天內相應增加和減少。 請洽詢負責管理 Azure Synapse Analytics 執行個體的小組，以了解基礎使用量。

移至 Azure 入口網站中的 [保留]，並以 100 DWU 的倍數購買 Azure Synapse Analytics 保留容量。

## <a name="reservation-purchase-recommendations"></a>保留購買建議

保留購買建議可藉由分析您過去 7、30 和 60 天的每小時使用量資料計算得出。 Azure 會設算您有保留時的成本，並將其與您長時間產生的實際隨用隨付成本進行比較。 對於您在時間範圍內使用的每個數量，都會執行此計算。 能夠節省最多成本的數量，即為建議值。

例如，您大部分的時間可能使用 500 個 VM，但有時使用量會遽增至 700 個 VM。 在此範例中，Azure 會分別就 500 和 700 個 VM 的數量計算可節省的成本。 由於 700 個 VM 的使用量是偶發的，因此建議計算會判定購買 500 個 VM 的保留可節省最多成本，而提供 500 的建議數量。

請注意下列幾點：

- 保留建議會使用適用於您的隨選使用費率來計算。
- 建議是針對個別大小而計算的，而非執行個體大小系列。
- 針對某個範圍建議的數量，會在您購買該範圍的保留當天減少。
    - 不過，跨範圍的保留數量建議可能需要多達 25 天才會更新。 例如，如果您根據共用範圍建議進行購買，則單一訂用帳戶範圍建議最多可能需要 25 天才會下調。

## <a name="recommendations-in-the-azure-portal"></a>Azure 入口網站中的建議

建議引擎所計算的保留購買會顯示在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs)的 [建議]  索引標籤上。 以下是範例影像。

![顯示建議的影像](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>成本管理 Power BI 應用程式中的建議

Enterprise 合約和 Microsoft 客戶合約客戶可以使用 VM RI 涵蓋範圍報表來取得 VM 和購買建議。 涵蓋範圍報表會顯示總使用量和保留執行個體所涵蓋的使用量。

1. 取得[成本管理應用程式](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)。
2. 根據您想要購買的範圍，移至 VM RI 涵蓋範圍報表 - 共用或單一範圍。
3. 選取區域、執行個體大小系列，以查看所選篩選條件的使用量、RI 涵蓋範圍和購買建議。

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor 中的建議

[Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview) 會提供保留購買建議。

- Advisor 只具有單一訂用帳戶範圍建議。
- Advisor 建議是使用 30 天的回顧期間來計算的。 預估的節省成本以 3 年的保留期限為單位。
- 如果您購買共用範圍的保留，Advisor 保留購買建議最多可能需要 30 天才會消失。

## <a name="recommendations-using-apis"></a>使用 API 的建議

使用[保留建議](/rest/api/consumption/reservationrecommendations/list) REST API 以程式設計方式檢視建議。

## <a name="next-steps"></a>後續步驟

- [管理 Azure 保留項目](manage-reserved-vm-instance.md)
- [了解採用隨用隨付費率的訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留項目內](reserved-instance-windows-software-costs.md)