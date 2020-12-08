---
title: 使用 Azure Resource Manager 範本建立專用 SQL 集區 (先前稱為 SQL DW)
description: 了解如何使用 Azure Resource Manager 範本來建立 Azure Synapse Analytics SQL 集區。
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 70adb7409c44a79345a192df173a1a073cc9b7dd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460739"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>快速入門：使用 ARM 範本建立 Azure Synapse Analytics 專用 SQL 集區 (先前稱為 SQL DW)

此 Azure Resource Manager 範本 (ARM 範本) 會建立已啟用透明資料加密的專用 SQL 集區 (先前稱為 SQL DW)。 專用 SQL 集區 (先前稱為 SQL DW) 是已在 Azure Synapse 中正式發行的企業資料倉儲功能。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/)。

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

範本會定義一項資源：

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立專用 SQL 集區 (先前稱為 SQL DW)。
   
   [![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. 輸入或更新下列值：

   * 訂用帳戶：選取 Azure 訂用帳戶。
   * **資源群組**：選取 [新建]，並輸入資源群組的唯一名稱，然後選取 [確定]。 新的資源群組將有助於清除資源。
   * **區域**：選取區域。  例如，**美國中部**。
   * **SQL Server 名稱**：接受預設名稱或輸入 SQL Server 的名稱。
   * **SQL 管理員登入**：輸入 SQL Server 的管理員使用者名稱。
   * **SQL 管理員密碼**：輸入 SQL Server 的管理員密碼。
   * **資料倉儲名稱**：輸入專用 SQL 集區名稱。
   * **透明資料加密**：接受預設值 (已啟用)。 
   * **服務等級目標**：接受預設值 (DW400c)。
   * **位置**：接受資源群組的預設位置。
   * **檢閱並建立**：選取。
   * **建立**：選取。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

您可以使用 Azure 入口網站來檢查已部署的資源，或使用 Azure CLI 或 Azure PowerShell 指令碼來列出已部署的資源。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
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

在此快速入門中，您已使用 ARM 範本建立專用 SQL 集區 (先前稱為 SQL DW)，並已驗證部署。 若要深入了解 Azure Synapse Analytics 和 Azure Resource Manager，請參閱下列文章。

- 閱讀 [Azure Synapse Analytics 的概觀](sql-data-warehouse-overview-what-is.md)
- 深入了解 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [建立及部署您的第一個 ARM 範本](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
