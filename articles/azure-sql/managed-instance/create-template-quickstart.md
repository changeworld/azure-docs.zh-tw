---
title: Azure Resource Manager：建立 Azure SQL 受控執行個體
description: 了解如何使用 Azure Resource Manager 範本建立 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: 01c6c37d31d41f88b370face372555536724adde
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256065"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-azure-resource-manager-template"></a>快速入門：使用 Azure Resource Manager 範本建立 Azure SQL 受控執行個體

本快速入門著重於部署 Resource Manager 範本來建立 Azure SQL 受控執行個體和 vNet 的程序。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

無。

## <a name="create-an-azure-sql-managed-instance"></a>建立 Azure SQL 受控執行個體

[Azure SQL 受控執行個體](sql-managed-instance-paas-overview.md)是智慧型、完全受控且可調整的雲端資料庫，可與 SQL Server 資料庫引擎達到幾乎 100% 的功能同位。

### <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

範本中定義了下列資源：

- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)



您可以在 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)中找到更多範本範例。

## <a name="deploy-the-template"></a>部署範本

從下列 PowerShell 程式碼區塊中選取 [試試看]，以開啟 Azure Cloud Shell。

> [!IMPORTANT]
> 部署受控執行個體是長時間執行的作業。 在子網路中部署第一個執行個體，所需時間通常比部署到具有現有受控執行個體的子網路還要久。 如需平均佈建時間，請參閱 [SQL 受控執行個體管理作業](sql-managed-instance-paas-overview.md#management-operations)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

* * *

## <a name="review-deployed-resources"></a>檢閱已部署的資源

瀏覽 [Azure 入口網站](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)，並確認受控執行個體是否在您選取的資源群組中。 由於建立受控執行個體可能需要一些時間，因此您可能需要在資源群組的 [概觀] 頁面上檢查**部署**連結。

- 如需說明如何從 Azure 虛擬機器連線至 SQL 受控執行個體的快速入門，請參閱[設定 Azure 虛擬機器連線](connect-vm-instance-configure.md)。
- 如需說明如何使用點對站連線從內部部署用戶端電腦連線至 SQL 受控執行個體的快速入門，請參閱[設定點對站連線](point-to-site-p2s-configure.md)。

## <a name="clean-up-resources"></a>清除資源

如果您想要前往[下一個步驟](#next-steps)，請保留受控執行個體，但請在完成任何其他教學課程之後，刪除受控執行個體和相關資源。 刪除受控執行個體之後，請參閱[在刪除受控執行個體後刪除子網路](virtual-cluster-delete.md)。


若要刪除資源群組：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定 Azure VM 以連線到 Azure SQL 受控執行個體](connect-vm-instance-configure.md)
