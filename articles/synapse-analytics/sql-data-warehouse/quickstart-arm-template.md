---
title: 使用 Azure Resource Manager 範本建立 SQL 集區
description: 了解如何使用 Azure Resource Manager 範本來建立 Azure Synapse Analytics SQL 集區。
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 766a41b61f32804c7d7f59c946530f8e1a7b869a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296265"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>快速入門：使用 ARM 範本建立 Azure Synapse Analytics SQL 集區

此範本會建立已啟用透明資料加密的 Azure Synapse Analytics SQL 集區。 Synapse SQL 集區是已在 Azure Synapse 中正式推出的企業資料倉儲功能。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/)。

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json" highlight="57-97":::

範本會定義一項資源：

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立 Synapse SQL 集區。
   
   [![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. 輸入或更新下列值：

   * 訂用帳戶：選取 Azure 訂用帳戶。
   * **資源群組**：選取 [新建]，並輸入資源群組的唯一名稱，然後選取 [確定]。 新的資源群組將有助於清除資源。
   * **區域**：選取區域。  例如，**美國中部**。
   * **SQL Server 名稱**：接受預設名稱或輸入 SQL Server 的名稱。
   * **SQL 管理員登入**：輸入 SQL Server 的管理員使用者名稱。
   * **SQL 管理員密碼**：輸入 SQL Server 的管理員密碼。
   * **資料倉儲名稱**：輸入 SQL 集區名稱。
   * **透明資料加密**：接受預設值 (已啟用)。 
   * **服務等級目標**：接受預設值 (DW400c)。
   * **位置**：接受資源群組的預設位置。
   * **檢閱並建立**：選取。
   * **建立**：選取。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

您可以使用 Azure 入口網站來檢查已部署的資源，或使用 Azure CLI 或 Azure PowerShell 指令碼來列出已部署的資源。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>清除資源

不再需要時，請使用 Azure CLI 或 Azure PowerShell 刪除資源群組：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已透過使用 Azure Resource Manager 範本建立 Azure Synapse Analytics SQL 集區，並已驗證該部署。 若要深入了解 Azure Synapse Analytics 和 Azure Resource Manager，請繼續閱讀下列文章。

- 閱讀 [Azure Synapse Analytics 的概觀](sql-data-warehouse-overview-what-is.md)
- 深入了解 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [建立及部署第一個 Azure Resource Manager 範本](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
