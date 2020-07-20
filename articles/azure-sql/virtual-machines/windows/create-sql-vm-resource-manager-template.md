---
title: 建立 SQL Server VM (Azure Resource Manager 範本)
description: 了解如何使用 Azure Resource Manager 範本在 Azure 虛擬機器 (VM) 上建立 SQL Server。
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 8b165f640548f28e5d94e5a791c0fe8545df4d78
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852473"
---
# <a name="create-sql-server-vm-azure-resource-manager-template"></a>建立 SQL Server VM (Azure Resource Manager 範本)

使用此 Azure Resource Manager 範本在 Azure 虛擬機器 (VM) 上部署 SQL Server。 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>必要條件

SQL Server VM 的 ARM 範本需要下列各項：

- 最新版的 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 和/或 [PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7)。 
- 預先設定且備妥[虛擬網路](../../../virtual-network/quick-create-portal.md) 和[子網路](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)的[資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)。
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/)。

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json" highlight="169-310":::

範本中定義了五個 Azure 資源： 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses)：建立公用 IP 位址。 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)：建立網路安全性群組。 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)：設定網路介面。 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)：在 Azure 中建立虛擬機器。 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines)：向 SQL VM 資源提供者註冊虛擬機器。 

如需更多「Azure VM 上的 SQL Server」範本，請參閱[快速入門範本資源庫](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine)。


## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 範本會建立虛擬機器，而其中會安裝所需的 SQL Server 版本，並向 SQL VM 資源提供者註冊。 

   [![部署至 Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. 選取或輸入下列值。

    * 訂用帳戶：選取 Azure 訂用帳戶。
    * **資源群組**：為 SQL Server VM 準備的資源群組。 
    * **區域**：選取區域。  例如，**美國中部**。
    * **虛擬機器名稱**：輸入 SQL Server 虛擬機器的名稱。 
    * **虛擬機器大小**：從下拉式選單中，為您的虛擬機器選擇適當的大小。
    * **現有虛擬網路名稱**：輸入為 SQL Server VM 準備的虛擬網路名稱。 
    * **現有 Vnet 資源群組**：輸入其中已備妥虛擬網路的資源群組。 
    * **現有子網路名稱**：備妥的子網路名稱。 
    * **映像供應項目**：選擇最符合您業務需求的 SQL Server 和 Windows Server 映像。 
    * **SQL SKU**：選擇最符合您業務需求的 SQL Server SKU 版本。 
    * **管理使用者名稱**：虛擬機器管理員的使用者名稱。 
    * **管理員密碼**：VM 管理帳戶所使用的密碼。 
    * **儲存體工作負載類型**：最符合您業務的工作負載儲存體類型。 
    * **SQL 資料磁碟計數**：SQL Server 用在資料檔案上的磁碟數目。  
    * **資料路徑**：SQL Server 資料檔案的路徑。 
    * **SQL 記錄磁碟計數**：SQL Server 用在記錄檔上的磁碟數目。 
    * **記錄路徑**：SQL Server 記錄檔的路徑。 
    * **位置**：所有資源的位置，此值應維持 `[resourceGroup().location]` 的預設值。 

3. 選取 [檢閱 + 建立]。 成功部署 SQL Server VM 之後，您會收到通知。

Azure 入口網站用於部署範本。 除了 Azure 入口網站以外，您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

您可以使用 Azure CLI 來檢查已部署的資源。 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

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

如需逐步教學課程，以引導您完成建立範本的流程，請參閱：

> [!div class="nextstepaction"]
> [教學課程：建立及部署第一個 Azure Resource Manager 範本](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

如需部署 SQL Server VM 的其他方式，請參閱： 
- [Azure 入口網站](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

若要深入了解，請參閱 [Azure VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)。
