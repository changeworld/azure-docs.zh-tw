---
title: 配置 Azure 成本
description: 本文說明如何建立成本配置規則，將訂用帳戶、資源群組或標籤的成本分散給其他人。
author: bandersmsft
ms.author: banders
ms.date: 08/11/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 025b9b7e503f38a111bd158f17b7fbeec5b23579
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684978"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>建立和管理 Azure 成本配置規則 (預覽)

大型企業通常會集中管理 Azure 服務或資源，但會由不同的內部部門或業務單位使用。 一般來說，集中管理小組想要將共用服務的成本重新配置給內部部門，或主動使用服務的組織業務單位。 本文可協助您了解及使用 Azure 成本管理中的成本配置。

使用成本配置時，您可以將共用服務的成本從訂用帳戶、資源群組或標籤重新指派或分散至組織中的其他訂用帳戶、資源群組或標籤。 成本配置會將共用服務的成本轉移給另一個取用內部部門或業務單位所擁有的訂用帳戶、資源群組，或標記。 換句話說，成本配置有助於管理和顯示移轉時的「的成本責任」。

成本配置不會影響您的帳單發票。 計費責任不會變更。 成本配置的主要目的是協助您向其他人收取成本。 所有的退款流程都是在組織以外的 Azure 中進行。 成本配置可讓您藉由在重新指派或散發時顯示成本來支付費用。

配置的成本會顯示在成本分析中。 成本資訊會顯示為與建立成本配置規則時指定的目標訂用帳戶、資源群組或標記相關聯的其他專案。

> [!NOTE]
> Azure 成本管理的成本配置功能目前為公開預覽狀態。 成本管理中的某些功能可能不受支援，功能也可能有有限。

## <a name="prerequisites"></a>Prerequisites

- 成本配置目前僅支援具有 [Microsoft 客戶合約](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)或 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 的客戶。
- 若要建立或管理成本配置規則，您必須使用 [Enterprise 合約](../manage/understand-ea-roles.md)的企業系統管理員帳戶。 或者，您必須擁有 Microsoft 客戶合約的[計費帳戶](../manage/understand-mca-roles.md)。

## <a name="create-a-cost-allocation-rule"></a>建立成本配置規則

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。
2. 瀏覽至 [成本管理 + 計費] > [成本管理]。
3. 在 [設定] > [組態] 下，選取 [成本配置 (預覽)]。
4. 請確定已選取正確的 EA 註冊或計費帳戶。
5. 選取 [+新增]  。
6. 輸入成本配置規則名稱的描述性文字。

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="顯示建立規則名稱的範例" lightbox="./media/allocate-costs/rule-name.png" :::

規則的評估開始日期會用來產生預先填入的成本配置百分比。

1. 選取 [新增來源]，然後選取 [訂用帳戶]、[資源群組] 或 [標籤]，以選擇要分散的成本。
2. 選取 [新增目標]，然後選取會接收配置成本的 [訂用帳戶]、[資源群組] 或 [標籤]。
3. 如果您需要建立額外的成本配置規則，請重複此流程。

## <a name="configure-the-allocation-percentage"></a>設定配置百分比

設定配置百分比，以定義在指定的目標之間如何按比例分配成本。 您可以手動定義整數百分比來建立配置規則。 或者，您可以根據目前在指定目標中的計算、儲存體或網路使用量，按比例分割成本。

依據計算成本、儲存體成本或網路成本來分配成本時，會藉由評估選取的目標成本來衍生比例百分比。 成本會與目前計費月份的資源類型相關聯。

將成本分散到總成本時，會以目前計費月份所選目標的總和或總成本來分配比例百分比。

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="顯示配置百分比的範例" lightbox="./media/allocate-costs/cost-distribution.png" :::

一旦設定，預先定義的百分比就會固定不變。 所有進行中的配置都會使用這項資料。 只有在手動更新規則時，才會變更百分比。

1. 在 [預先填入百分比] 清單中，選取下列其中一個選項。
    - **平均分散** - 每個目標會接收總成本的平均百分比比例。
    - **總成本** - 根據其總成本，建立與目標成正比的比率。 比率用來從選取的來源分散成本。
    - **計算成本** - 根據其 Azure 計算成本 ([Microsoft.Compute](https://docs.microsoft.com/azure/templates/microsoft.compute/allversions) 命名空間中的資源類型)，建立與目標的比例。比率用來從選取的來源分散成本。
    - **儲存體成本** - 根據其 Azure 儲存體成本 ([Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) 命名空間中的資源類型) 建立與目標的比例。 比率用來從選取的來源分散成本。
    - **網路成本** - 根據其 Azure 網路成本 ([Microsoft.Network](https://docs.microsoft.com/azure/templates/microsoft.network/allversions) 命名空間中的資源類型) 建立與目標的比例。 比率用來從選取的來源分散成本。
    - **自訂** - 允許手動指定整數百分比。 指定的總合必須等於 100%。
1. 設定規則後，請選取 [建立]。

配置規則會開始處理。 當規則處於作用中狀態時，所有選取的來源成本都會配置給指定的目標。

> [!NOTE] 
> 新的規則處理最多可能需要兩個小時才會完成並處於作用中狀態。

## <a name="verify-the-cost-allocation-rule"></a>驗證成本配置規則

當成本配置規則處於作用中狀態時，所選來源的成本會分散到指定的配置目標。 使用下列資訊來確認已適當地將成本配置給目標。

### <a name="view-cost-allocation-for-a-subscription"></a>檢視訂用帳戶的成本配置

您會在成本分析中，看到配置規則的影響。 在 Azure 入口網站中，前往[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 選取清單中的訂用帳戶，這是使用中的成本配置規則目標。 然後在功能表中選取 [成本分析]。 在成本分析中，選取 [群組依據] 然後選取 [成本配置]。 結果檢視會顯示訂用帳戶所產生的快速成本明細。 系統也會顯示配置給訂用帳戶的成本，如下圖所示。

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="顯示成本明細的範例" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>檢視資源群組的成本配置

使用類似的流程，對資源群組的成本配置規則所造成的影響。 在 Azure 入口網站中，移至[資源群組](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)。 選取清單中的資源群組，這是使用中的成本配置規則目標。 然後在功能表中選取 [成本分析]。 在成本分析中，選取 [群組依據] 然後選取 [成本配置]。 檢視會顯示資源群組所產生的快速成本明細。 同時也會顯示配置給資源群組的成本。

### <a name="view-cost-allocation-for-tags"></a>檢視標籤的成本配置

在 Azure 入口網站中，瀏覽至**成本管理 + 帳單** > **成本管理** > **成本分析**。 在成本分析中，選取 [新增篩選]。 選取 [標籤]，選擇標籤金鑰，以及為其配置成本的標籤值。

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="顯示已標記項目成本的範例" lightbox="./media/allocate-costs/tagged-costs.png" :::

## <a name="edit-an-existing-cost-allocation-rule"></a>編輯現有的成本配置規則

您可以編輯成本配置規則來變更來源或目標，或更新計算、儲存體或網路選項的預先填入百分比。 以您建立規則的相同方式來編輯。 修改現有的規則最多可能需要兩小時來重新處理。

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

下列各節包含關於成本配置的常見問題。

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>在公開預覽中，成本配置目前的限制為何？
<a name="limitations"></a>

目前，成本分析、預算和預測檢視在成本管理中支援成本配置。 配置的成本也會顯示在訂用帳戶清單和訂用帳戶概觀頁面上。

成本配置公開預覽目前不支援下列項目：

- 排定的[匯出](tutorial-export-acm-data.md)
- [使用量詳細資料](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API 公開的資料
- 計費訂用帳戶區域
- [成本管理 Power BI 應用程式](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop 連接器](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>成本會分解成預算和預測的檢視嗎？
<a name="budgets-forecast"></a>

是。 配置的成本會納入預算和預測並加以支援。 預算和預測檢視會顯示配置的成本，如成本分配規則所設定。

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>如果刪除成本配置規則，會發生什麼事？
<a name="delete-rule"></a>

刪除成本配置規則時，會移除配置給目標的所有開放和目前計費月份成本。 如果已擁有數個月的成本配置規則，過去幾個月的配置資料就會保留為配置規則原先設定的狀態。

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>為什麼註冊管理員或計費帳戶管理員需要建立成本配置規則？
<a name="why-admin"></a>

成本配置規則會在註冊範圍 (Enterprise 合約) 或計費帳戶範圍 (Microsoft 客戶合約) 中建立。 需要計費管理員權限才能在這些範圍進行變更。

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>為什麼來源和目標依規則限制為 25 個？
<a name="source-target-rule-limit"></a>

限制是預覽限制，可確保良好的成本配置效能和可擴縮性。 當成本配置轉換為正式發行 (GA) 時，限制可能會增加或移除。

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>如果成本配置規則 (來源/目標) 重疊，會發生什麼事？
<a name="rule-overlap"></a>

不建議具有重疊來源或重疊目標的規則。 成本配置規則會依其建立日期順序套用，因此，如果有任何成本配置規則與最早建立日期的配置規則重疊，將會優先使用。

## <a name="next-steps"></a>後續步驟

- 使用[成本配置 Rest API](https://go.microsoft.com/fwlink/?linkid=2135004) 建立或更新配置規則
- 深入了解[如何使用 Azure 成本管理來最佳化您的雲端投資](cost-mgt-best-practices.md)