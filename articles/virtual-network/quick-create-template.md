---
title: 快速入門：使用 Resource Manager 範本建立虛擬網路
titleSuffix: Azure Virtual Network
description: 了解如何使用 Resource Manager 範本建立 Azure 虛擬網路。
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217663"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>快速入門：建立虛擬網路 - Resource Manager 範本

在本快速入門中，您將了解如何使用 Azure Resource Manager 範本來建立具有兩個子網路的虛擬網路。 虛擬網路是私人網路在 Azure 中的基本建置組塊。 其可讓 Azure 資源 (例如 VM) 安全地彼此通訊，以及與網際網路通訊。


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

您也可以使用 [Azure 入口網站](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md) 或 [Azure CLI](quick-create-cli.md) 來完成本快速入門。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

範本中已定義下列 Azure 資源：
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)：建立 Azure 虛擬網路。
-  [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets) - 建立子網路。

## <a name="deploy-the-template"></a>部署範本

將 Resource Manager 範本部署至 Azure：

1. 選取 [部署至 Azure] 以登入 Azure 並開啟範本。 該範本會建立具有兩個子網路的虛擬網路。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. 在入口網站的 [建立具有兩個子網路的虛擬網路] 頁面上，輸入或選取下列值：
   - **資源群組**：選取 [新建]，輸入資源群組的名稱，然後選取 [確定]。
   - **虛擬網路名稱**：輸入新虛擬網路的名稱。
3. 選取 [檢閱 + 建立]，然後選取 [建立]。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

探索搭配虛擬網路所建立的資源。

若要了解範本中虛擬網路的 JSON 語法和屬性，請參閱 [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為虛擬網路建立的資源時，請刪除資源群組。 這會移除虛擬網路和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已部署具有兩個子網路的 Azure 虛擬網路。 若要深入了解 Azure 虛擬網路，請繼續進行虛擬網路的教學課程。

> [!div class="nextstepaction"]
> [篩選網路流量](tutorial-filter-network-traffic.md)