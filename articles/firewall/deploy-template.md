---
title: 快速入門：使用可用性區域建立 Azure 防火牆 - Resource Manager 範本
description: 使用範本來部署 Azure 防火牆。 此虛擬網路具有一個 VNet 和三個子網路。 部署兩部 Windows Server 虛擬機器：跳躍箱 (jump box) 和伺服器。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 104e2e9f6593d95caf77c0796f8b9465eebcfcee
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705124"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---arm-template"></a>快速入門：使用可用性區域部署 Azure 防火牆 - ARM 範本

在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 在三個可用性區域中部署 Azure 防火牆。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

此範本會建立具有防火牆的測試網路環境。 此網路具有一個虛擬網路 (VNet) 和三個子網路：AzureFirewallSubnet、ServersSubnet 和 *JumpboxSubnet*。 ServersSubnet 和 JumpboxSubnet 子網路各有一個雙核心的 Windows Server 虛擬機器。

防火牆位於 AzureFirewallSubnet 中並具有應用程式規則集合，集合內有一個規則允許存取 `www.microsoft.com`。

使用者定義的路由會將來自 ServersSubnet 子網路的網路流量指向通過已套用防火牆規則的防火牆。

如需 Azure 防火牆的詳細資訊，請參閱[使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

此範本會建立具有可用性區域的 Azure 防火牆，以及支援 Azure 防火牆所需的資源。

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox)。

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>部署範本

將 ARM 範本部署到 Azure：

1. 選取 [部署至 Azure] 以登入 Azure 並開啟範本。 此範本會建立 Azure 防火牆、網路基礎結構和兩部虛擬機器。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. 在入口網站的 [以區域建立 AzureFirewall 的沙箱設定] 頁面上，輸入或選取下列值：
   - **資源群組**：選取 [新建]，輸入資源群組的名稱，然後選取 [確定]。 
   - **虛擬網路名稱**：輸入新 VNet 的名稱。
   - **管理使用者名稱**：輸入管理使用者帳戶的使用者名稱。
   - **管理員密碼**：輸入管理員密碼。

3. 讀取條款及條件，然後選取 [我同意上方所述的條款及條件]，然後選取 [購買]。 部署可能需要 10 分鐘或更久的時間才能完成。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

探索使用防火牆所建立的資源。

若要了解範本中防火牆的 JSON 語法和屬性，請參閱 [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="clean-up-resources"></a>清除資源

當您不再需要這些資源時，您可以執行 `Remove-AzResourceGroup` PowerShell 命令來移除資源群組、防火牆和所有相關資源。 若要移除名為 MyResourceGroup 的資源群組，請執行：

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

如果您打算繼續進行防火牆監視教學課程，請勿移除資源群組和防火牆。 

## <a name="next-steps"></a>後續步驟

接下來，您可以監視 Azure 防火牆記錄。

> [!div class="nextstepaction"]
> [教學課程：監視 Azure 防火牆記錄](tutorial-diagnostics.md)