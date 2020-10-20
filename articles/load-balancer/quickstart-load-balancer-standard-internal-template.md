---
title: 快速入門：使用範本建立內部負載平衡器
description: 本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立內部 Azure 負載平衡器。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: aa68dad2f8f018a9f3f70f2f02fd5e989ccbad4e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047755"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>快速入門：使用 ARM 範本建立內部負載平衡器以平衡 VM 的負載

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立內部 Azure 負載平衡器。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先決條件：

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer)。

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

範本中已定義多個 Azure 資源：

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts):用於開機診斷的虛擬機器儲存體帳戶。
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets):虛擬機器的可用性設定組。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)：負載平衡器和虛擬機器的虛擬網路。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces)：虛擬機器的網路介面。
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers)：內部 Load Balancer。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines)：虛擬機器。

若要尋找更多有關 Azure Load Balancer 的範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署範本

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取左側面板中的 [資源群組]****。

3. 選取您在上一節中建立的資源群組。 預設的資源群組名稱為 **myResourceGroupLB**

4. 確認下列資源是在資源群組中建立的：

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="使用者 Azure 入口網站以確認資源的建立。" border="true":::

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組及其內含的所有資源。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>後續步驟

如需逐步教學課程，以引導您完成建立範本的流程，請參閱：

> [!div class="nextstepaction"]
> 建立及部署您的第一個 ARM 範本[