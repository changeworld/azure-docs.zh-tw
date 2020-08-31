---
title: 教學課程 - 建立和管理 Azure 預算
description: 此教學課程可協助規劃和說明您取用之 Azure 服務的成本。
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 739d300faf77564891b2b783a3eb1cca50373015
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718348"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教學課程：建立和管理 Azure 預算

成本管理中的預算可協助您進行規劃並促進組織責任歸屬。 透過預算，您可以說明您在特定期間所取用或訂閱的 Azure 服務。 它們可協助您通知其他人其費用的相關資訊以主動管理成本，並監視費用在經過一段時間的進展方式。 當超過您所建立的預算閾值時，只會觸發通知。 您的資源都會不受到影響，而您的使用量並不會停止。 當您分析成本時，您可以使用預算來比較及追蹤費用。

成本和使用方式資料通常會在 20 小時內提供，而其後將會根據這些成本每 12-14 時評估一次預算。 達到預算閾值時，通常會在評估的一小時內收到電子郵件通知。

當您在未來選取到期日時，預算會在期間 (每月、每季或每年) 結束時自動重設相同的預算金額。 因為它們會使用相同的預算金額重設，所以，在預算的貨幣金額與未來期間不同時您需要另外建立預算。

此教學課程的範例會引導您針對 Azure Enterprise 合約 (EA) 訂用帳戶建立和編輯預算。

觀看[使用 Azure 入口網站將預算套用至訂用帳戶](https://www.youtube.com/watch?v=UrkHiUx19Po)影片，以了解如何在 Azure 中建立預算以監視支出。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中建立預算
> * 使用 PowerShell 建立和編輯預算
> * 使用 Azure Resource Manager 範本建立預算

## <a name="prerequisites"></a>必要條件

預算受下列類型的 Azure 帳戶類型和範圍支援：

- Azure 角色型存取控制 (Azure RBAC) 範圍
    - 管理群組
    - 訂用帳戶
- Enterprise 合約範圍
    - 計費帳戶
    - department
    - 註冊帳戶
- 個別合約
    - 計費帳戶
- Microsoft 客戶合約範圍
    - 計費帳戶
    - 帳單設定檔
    - 發票區段
    - 客戶
- AWS 範圍
    - 外部帳戶
    - 外部訂用帳戶


若要檢視預算，您至少需要 Azure 帳戶的讀取存取。

如果您有新的訂用帳戶，就無法立即建立預算或使用成本管理功能。 最多可能需要48小時的時間，才能使用所有的成本管理功能。

針對 Azure EA 訂用帳戶，您必須具備檢視預算的讀取存取權。 若要建立及管理預算，您必須具有參與者權限。

使用者和群組針對預算的每個訂用帳戶支援下列 Azure 權限或範圍。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 擁有者：可以建立、修改或刪除訂用帳戶的預算。
- 參與者和成本管理參與者 – 可以建立、修改或刪除他們自己的預算。 可以修改其他人所建立之預算的預算金額。
- 讀者和成本管理讀者 – 可以檢視他們有權限的預算。

如需成本管理資料的指派權限詳細資訊，請參閱[指派成本管理資料的存取權](../../cost-management/assign-access-acm-data.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

- 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 入口網站中建立預算

您可以針對每月、每季或每年期間建立 Azure 訂用帳戶預算。

若要建立或檢視預算，請在 Azure 入口網站中開啟所需的範圍，然後在功能表中選取 [預算]。 例如，瀏覽至 [訂用帳戶]、從清單中選取訂用帳戶，然後在功能表中選取 [預算]。 使用 [範圍] 框切換至 [預算] 中的不同範圍，例如管理群組。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

建立預算之後，會針對它們顯示您目前費用的簡單檢視。

選取 [新增]。

![此範例顯示已建立的預算清單](./media/tutorial-acm-create-budgets/budgets01.png)

在 [建立預算] 視窗中，確認顯示的範圍是正確的。 選擇您要新增的任何篩選條件。 篩選條件可讓您針對特定成本建立預算，例如訂用帳戶中的資源群組，或虛擬機器之類的服務。 任何可在成本分析中使用的篩選條件，也都可以套用至預算。

在識別範圍和篩選條件後，請輸入預算名稱。 然後，選擇每月、每季或每年的預算重設期間。 這個重設期間會決定預算所分析的時間範圍。 預算所評估的成本會在每個新期間開始時從零起算。 當您建立每季預算時，它的運作方式與與每月預算相同。 差異在於當季預算金額會平均分配於當季的三個月。 每年預算金額會平均分配於該日曆年度的 12 個月間。

如果您有隨用隨付、MSDN 或 Visual Studio 訂用帳戶，則您發票的計費期間可能與行事曆月份不同。 對於這些類型的訂用帳戶和資源群組，您可以建立與發票期間或行事曆月份一致的預算。 若要建立與發票期間一致的預算，請選取 [帳單月份]、[帳單季] 或 [帳單年份] 的重設期間。 若要建立與行事曆月份一致的預算，請選取 [每月]、[每季] 或 [每年] 的重設期間。

接下來，請識別預算失效並停止評估成本的到期日。

根據目前在預算中選擇的欄位，會有顯示的圖表可協助您選取要用於預算的閾值。 建議的預算以您在未來的期間可能產生的最高預測成本為準。 您可以變更預算金額。

![此範例顯示以每月成本資料建立的預算 ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

設定預算金額之後，請選取 [下一步] 以設定預算警示。 預算需要至少一個成本閾值 (預算的百分比) 與對應的電子郵件地址。 您可以選擇性地在單一預算中最多包含五個閾值與五個電子郵件地址。 達到預算限額時，通常會在評估的一小時內收到電子郵件通知。

如果您想要接收電子郵件，請將 azure-noreply@microsoft.com 新增至核准的寄件者清單，讓電子郵件不會進入您的垃圾郵件資料夾。 如需有關通知的詳細資訊，請參閱[使用成本警示](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md)。

在下列範例中，達到預算的 90% 時，就會產生電子郵件警示。 如果您使用預算 API 建立預算，您也可以將角色指派給其他人以接收警示。 目前不支援在 Azure 入口網站將角色指派給人員。 如需 Azure 預算 API 的詳細資訊，請參閱[預算 API](/rest/api/consumption/budgets)。

警示限制支援的範圍為您已提供的 0.01 到 1000% 預算限額。

![顯示警示條件的範例](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

建立預算之後，即會在成本分析中顯示它。 當您開始[分析成本和費用](../../cost-management/quick-acm-cost-analysis.md)時，首要步驟之一就是針對您的費用趨勢來檢視預算。

![成本分析中顯示的預算和支出範例](./media/tutorial-acm-create-budgets/cost-analysis.png)

在上述範例中，您已針對訂用帳戶建立預算。 您也可以針對資源群組建立預算。 如果您想要針對資源群組建立預算，請瀏覽至 [成本管理 + 計費]&gt;[訂用帳戶]&gt; 選取訂用帳戶 > [資源群組] > 選取資源群組 > [預算] > 然後**新增**預算。

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>針對合併的 Azure 與 AWS 成本建立預算

您可以透過指派管理群組給您的連接器和其合併和連結的帳戶，將您的 Azure 與 AWS 成本組成群組。 將您的 Azure 訂用帳戶指派給同一個管理群組。 然後為合併的成本建立預算。

1. 在成本管理中，選取 [預算]。
1. 選取 [新增]。
1. 選取 [變更範圍]，然後選取管理群組。
1. 繼續建立預算，直到完成為止。

## <a name="costs-in-budget-evaluations"></a>預算中的成本評估

現在，預算成本評估會包含保留執行個體和購買資料。 如果費用適用於您，則您可能會收到警示，因為費用已納入評估中。 建議您登入 [Azure 入口網站](https://portal.azure.com)，以確認預算閾值已正確設定而會考量新的成本。 Azure 計費的費用不會變更。 預算現在會根據您更完整的一組成本進行評估。 如果費用不適用於您，您的預算行為將維持不變。

如果您想要篩選新的成本，使預算僅根據第一方 Azure 使用量費用進行評估，請將下列篩選條件新增至您的預算：

- 發行者類型：Azure
- 費用類型：使用量

預算成本評估以實際成本為基礎。 其中不包含攤提。 如需預算中可用篩選選項的詳細資訊，請參閱[了解群組和篩選選項](group-filter.md)。

## <a name="trigger-an-action-group"></a>觸發動作群組

當您針對訂用帳戶或資源群組範圍建立或編輯預算時，您可以設定該預算以呼叫動作群組。 動作群組可在達到預算閾值時執行各種動作。 目前僅支援將動作群組用於訂用帳戶和資源群組範圍。 如需動作群組的詳細資訊，請參閱[在 Azure 入口網站中建立和管理動作群組](../../azure-monitor/platform/action-groups.md)。 如需以預算為基礎的自動化與動作群組搭配使用的詳細資訊，請參閱[使用 Azure 預算來管理成本](../manage/cost-management-budget-scenario.md)。

若要建立或更新動作群組，請在建立或編輯預算時選取 [管理動作群組]。

![建立預算以顯示管理動作群組的範例](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

接著，選取 [新增動作群組]，並建立動作群組。

![新增動作群組方塊的影像](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

建立動作群組後，請關閉此方塊以返回您的預算。

設定您的預算，以在達到個別閾值時使用您的動作群組。 最多可支援五個不同的閾值。

![此範例顯示如何選取警示條件的動作群組](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

下列範例顯示設定為 50%、75% 和 100% 的預算閾值。 每個閾值會分別設定為在指定的動作群組內觸發指定的動作。

![此範例顯示設定了不同動作群組和動作類型的警示條件](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

預算與動作群組的整合，僅適用於已停用一般警示結構描述的動作群組。 如需停用結構描述的詳細資訊，請參閱[如何啟用一般警示結構描述？](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>使用 PowerShell 建立和編輯預算

EA 客戶可以使用 Azure PowerShell 模組，以程式設計方式建立和編輯預算。  若要下載最新版 Azure PowerShell，請執行下列命令：

```azurepowershell-interactive
install-module -name Az
```

下列範例命令會建立預算。

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立預算

您可以使用 Azure Resource Manager 範本建立預算。 若要使用範本，請參閱[使用 Azure Resource Manager 範本建立預算](quick-create-budget-template.md)。

## <a name="clean-up-resources"></a>清除資源

如果您已建立預算，但不再需要用到，請檢視其詳細資料，並加以刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中建立預算
> * 使用 PowerShell 建立和編輯預算
> * 使用 Azure Resource Manager 範本建立預算

請前進到下一個教學課程，以建立成本管理資料的週期性匯出。

> [!div class="nextstepaction"]
> [建立和管理匯出的資料](tutorial-export-acm-data.md)
