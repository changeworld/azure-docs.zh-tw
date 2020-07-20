---
title: 建立 DMS 的執行個體 (Azure Resource Manager 範本)
description: 了解如何使用 Azure Resource Manager 範本來建立資料庫移轉服務。
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: 9e0d537a948e960c90737cc3f367940a0ffca74c
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852455"
---
# <a name="create-instance-of-azure-database-migration-service-azure-resource-manager-template"></a>建立 Azure 資料庫移轉服務的執行個體 (Azure Resource Manager 範本)

使用此 Azure Resource Manager 範本來部署 Azure 資料庫移轉服務的執行個體。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>必要條件

Azure 資料庫移轉服務的 ARM 範本需要下列各項： 

- 最新版的 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 和/或 [PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7)。 
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/)。

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json" highlight="33-75":::

範本中定義了三個 Azure 資源： 

- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)：建立虛擬網路。 
- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)：建立子網路。 
- [Microsoft.DataMigration/services](/azure/templates/microsoft.datamigration/services):部署 Azure 資料庫移轉服務的執行個體。 

您可以在[快速入門範本資源庫](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration)中找到更多Azure 資料庫移轉服務範本。


## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立 Azure 資料庫移轉服務的執行個體。 

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. 選取或輸入下列值。

    * 訂用帳戶：選取 Azure 訂用帳戶。
    * **資源群組**：從下拉式清單中選取現有的資源群組，或選取 [新建] 來建立新的資源群組。 
    * **區域**：將部署資源的位置。
    * **服務名稱**：新移轉服務的名稱。
    * **位置**：資源群組的位置，保留預設值：`[resourceGroup().location]`。
    * **VNet 名稱**：新虛擬網路的名稱。
    * **子網路名稱**：與虛擬網路相關聯的新子網路名稱。



3. 選取 [檢閱 + 建立]。 成功部署 Azure 資料庫移轉服務的執行個體之後，您會收到通知。 


Azure 入口網站用於部署範本。 除了 Azure 入口網站以外，您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-powershell.md)。

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

如需部署 Azure 資料庫移轉服務的其他方式，請參閱： 
- [Azure 入口網站](quickstart-create-data-migration-service-portal.md)

若要深入了解，請參閱 [Azure 資料庫移轉服務的概觀](dms-overview.md)