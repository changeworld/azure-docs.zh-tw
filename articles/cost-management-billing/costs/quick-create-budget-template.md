---
title: 快速入門 - 使用 Azure Resource Manager 範本建立預算
description: 說明如何使用 Azure Resource Manager 範本建立預算的快速入門
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 7d93bd757a39247302a6bc09009a1a814425c32f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745377"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>快速入門：使用 ARM 範本建立預算

成本管理中的預算可協助您進行規劃並促進組織責任歸屬。 透過預算，您可以說明您在特定期間所取用或訂閱的 Azure 服務。 它們可協助您通知其他人其費用的相關資訊以主動管理成本，並監視費用在經過一段時間的進展方式。 超過您所建立的預算閾值時，就會觸發通知。 您的資源都會不受到影響，而您的使用量並不會停止。 當您分析成本時，您可以使用預算來比較及追蹤費用。 本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立預算。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果您有新的訂用帳戶，就無法立即建立預算或使用成本管理功能。 最多可能需要48小時的時間，才能使用所有的成本管理功能。

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

針對 Azure EA 訂用帳戶，您必須具備檢視預算的讀取存取權。 若要建立及管理預算，您必須具有參與者權限。

使用者和群組針對預算的每個訂用帳戶支援下列 Azure 權限或範圍。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 擁有者：可以建立、修改或刪除訂用帳戶的預算。
- 參與者和成本管理參與者 – 可以建立、修改或刪除他們自己的預算。 可以修改其他人所建立之預算的預算金額。
- 讀者和成本管理讀者 – 可以檢視他們有權限的預算。

如需成本管理資料的指派權限詳細資訊，請參閱[指派成本管理資料的存取權](assign-access-acm-data.md)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/create-budget)。

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

此範本中已定義一項 Azure 資源：

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets)：建立 Azure 預設。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立預算。

   [![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. 選取或輸入下列值。

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Resource Manager 範本、建立預算、部署入口網站" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **訂用帳戶** ：選取 Azure 訂用帳戶。
    * **資源群組** ：如有必要，請選取現有的資源群組或 **建立新的資源群組** 。
    * **區域** ：選取 Azure 區域。 例如， **美國中部** 。
    * **預算名稱** ：輸入預算的名稱。 其在資源群組內必須是唯一的。 只允許英數字元、底線及連字號字元。
    * **金額** ：輸入要透過預算追蹤的成本總金額。
    * **時間粒紋** ：輸入預算所涵蓋的時間。 允許的值為 [每月]、[每季] 或 [每年]。 預算會在時間粒紋結束時重設。
    * **開始日期** ：輸入月份的第一天作為開始日期 (格式為 YYYY-MM-DD)。 未來開始日期不得超過今天算起的三個月。 您可以使用「時間粒紋」週期來指定過去開始日期。
    * **結束日期** ：以 YYYY-MM-DD 格式輸入預算的結束日期。 
    * **第一個閾值** ：輸入第一個通知的閾值。 當成本超過閾值時，就會傳送通知。 該值一律是百分比，且必須介於 0 到 1000 之間。
    * **第二個閾值** ：輸入第二個通知的閾值。 當成本超過閾值時，就會傳送通知。 該值一律是百分比，且必須介於 0 到 1000 之間。
    * **連絡人角色** ：輸入超出閾值時，預算通知要傳送至的連絡人角色清單。 預設值為「擁有者」、「參與者」和「讀者」。 預期的格式為 `["Owner","Contributor","Reader"]`。
    * **連絡人電子郵件** ：輸入超出閾值時，預算通知要傳送至的電子郵件地址清單。 預期的格式為 `["user1@domain.com","user2@domain.com"]`。
    * **連絡人群組** ：輸入在超出閾值時，預算通知要傳送到的動作群組資源識別碼清單 (以完整資源 URI 的形式)。 其接受字串陣列。 預期的格式為 `["action group resource ID1","action group resource ID2"]`。 如果不想要使用動作群組，請輸入 `[]`。
    * **資源群組篩選值** ：輸入要篩選的資源群組名稱清單。 預期的格式為 `["Resource Group Name1","Resource Group Name2"]`。 如果您不想要套用篩選條件，請輸入 `[]`。 
    * **計量類別篩選值** ：輸入 Azure 服務計量類別的清單。 預期的格式為 `["Meter Category1","Meter Category2"]`。 如果您不想要套用篩選條件，請輸入 `[]`。
   
3. 根據您的 Azure 訂用帳戶類型，執行下列其中一個動作：
   - 選取 [檢閱 + 建立]。
   - 檢閱條款及條件、選取 [我同意上方所述的條款及條件]，然後選取 [購買]。

4. 如果已選取 [檢閱 + 建立]，則會驗證您的範本。 選取 [建立]。  

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
