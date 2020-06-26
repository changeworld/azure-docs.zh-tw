---
title: 快速入門 - 使用 Azure Resource Manager 範本建立預算
description: 說明如何使用 Azure Resource Manager 範本建立預算的快速入門
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 06/10/2020
ms.custom: subject-armqs
ms.openlocfilehash: dc37039d6777a77f9de247808329930f1621ee82
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686404"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>快速入門：使用 Azure Resource Manager 範本建立預算

成本管理中的預算可協助您進行規劃並促進組織責任歸屬。 透過預算，您可以說明您在特定期間所取用或訂閱的 Azure 服務。 它們可協助您通知其他人其費用的相關資訊以主動管理成本，並監視費用在經過一段時間的進展方式。 超過您所建立的預算閾值時，就會觸發通知。 您的資源都會不受到影響，而您的使用量並不會停止。 當您分析成本時，您可以使用預算來比較及追蹤費用。 本快速入門說明如何使用 Resource Manager 範本建立預算。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>必要條件

Azure Resource Manager 範本僅支援 Enterprise 合約 (EA) 的 Azure 訂用帳戶。 此範本不支援其他訂用帳戶類型。

若要建立及管理預算，您必須具有參與者權限。 您可以個別為 EA 訂用帳戶和資源群組建立預算。 不過，您無法為 EA 帳單帳戶建立預算。 針對 Azure EA 訂用帳戶，您必須具備檢視預算的讀取存取權。

建立預算之後，您至少需有 Azure 帳戶的讀取權限，才能檢視預算。

如果您有新的訂用帳戶，就無法立即建立預算或使用成本管理功能。 最多可能需要48小時的時間，才能使用所有的成本管理功能。

使用者和群組針對預算的每個訂用帳戶支援下列 Azure 權限或範圍。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 擁有者：可以建立、修改或刪除訂用帳戶的預算。
- 參與者和成本管理參與者 – 可以建立、修改或刪除他們自己的預算。 可以修改其他人所建立之預算的預算金額。
- 讀者和成本管理讀者 – 可以檢視他們有權限的預算。

如需成本管理資料的指派權限詳細資訊，請參閱[指派成本管理資料的存取權](assign-access-acm-data.md)。

## <a name="create-a-budget"></a>建立預算

### <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/create-budget)。

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

此範本中已定義一項 Azure 資源：

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets)：建立 Azure 預設。

### <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立預算。

   [![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. 選取或輸入下列值。

   [![Resource Manager 範本、建立預算、部署入口網站](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **訂用帳戶**：選取 Azure 訂用帳戶。
    * **資源群組**：選取 [新建] 並輸入資源群組的唯一名稱，然後按一下 [確定]，或選取現有的資源群組。
    * **位置**：選取位置。 例如，**美國中部**。
    * **預算名稱**：輸入預算的名稱。 其在資源群組內必須是唯一的。 只允許英數字元、底線及連字號字元。
    * **金額**：輸入要透過預算追蹤的總成本或使用量。
    * **預算類別**：選取預算的類別，也就是預算會追蹤 [成本] 或 [使用量]。
    * **時間粒紋**：輸入預算所涵蓋的時間。 允許的值為 [每月]、[每季] 或 [每年]。 預算會在時間粒紋結束時重設。
    * **開始日期**：輸入月份的第一天作為開始日期 (格式為 YYYY-MM-DD)。 未來開始日期不得超過今天算起的三個月。 您可以使用「時間粒紋」週期來指定過去開始日期。
    * **結束日期**：以 YYYY-MM-DD 格式輸入預算的結束日期。 如未提供，預設值會設定為開始日期算起的 10 年。
    * **運算子**：選取比較運算子。 可能的值為 EqualTo、GreaterThan 或 GreaterThanOrEqualTo。
    * **閾值**：輸入通知的閾值。 當成本超過閾值時，就會傳送通知。 該值一律是百分比，且必須介於 0 到 1000 之間。
    * **連絡人電子郵件**：輸入超出閾值時，預算通知要傳送至的電子郵件地址清單。 預期的格式為 `["user1@domain.com","user2@domain.com"]`。
    * **連絡人角色**：輸入超出閾值時，預算通知要傳送至的連絡人角色清單。 預設值為「擁有者」、「參與者」和「讀者」。 預期的格式為 `["Owner","Contributor","Reader"]`。
    * **連絡人群組**：輸入在超出閾值時，預算通知要傳送到的動作群組資源識別碼清單 (以完整資源 URI 的形式)。 其接受字串陣列。 預期的格式為 `["action group resource ID1","action group resource ID2"]`。 如果不想要使用動作群組，請輸入 `[]`。
    * **資源篩選**：輸入資源的篩選條件清單。 預期的格式為 `["Resource Filter Name1","Resource Filter Name2"]`。 如果您不想要套用篩選條件，請輸入 `[]`。 如果您輸入資源篩選條件，您也必須輸入 [計量篩選條件] 值。
    * **計量篩選條件**：輸入計量的篩選條件清單，這是具有 [使用量] 預算類別的預算所必備。 預期的格式為 `["Meter Filter Name1","Meter Filter Name2"]`。 如果您未輸入**資源篩選條件**，請輸入 `[]`。
    * **我同意上方所述的條款及條件**：選取。

3. 選取 [購買]。 成功部署預算之後，您會收到通知：

   ![Resource Manager 範本、預算、部署入口網站通知](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Azure 入口網站用於部署範本。 除了 Azure 入口網站以外，您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署範本，請參閱[部署範本](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="validate-the-deployment"></a>驗證部署

您可以使用 Azure 入口網站來確認是否已建立預算，方法是瀏覽至 [成本管理 + 帳單] > 選取範圍 > [預算]。 或者，使用下列 Azure CLI 或 Azure PowerShell 指令碼來檢視預算。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>清除資源

當您不再需要預算時，請使用下列其中一種方法將其刪除：

### <a name="azure-portal"></a>Azure 入口網站

瀏覽至 [成本管理 + 計費] > 選取計費範圍 > [預算] > 選取預算 > 然後選取 [刪除預算]。

### <a name="command-line"></a>命令列

您可以使用 Azure CLI 或 Azure PowerShell 來移除預算。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立部署的 Azure 預算。 若要深入了解 Azure 成本管理與計費及 Azure Resource Manager，請繼續閱讀下列文章。

- 請閱讀[Azure 成本管理與計費](../cost-management-billing-overview.md)概觀
- 在 Azure 入口網站中[建立預算](tutorial-acm-create-budgets.md)
- 深入了解 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
