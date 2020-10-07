---
title: 快速入門：阵立具有多個公用 IP 位址的 Azure 防火牆 - Resource Manager 範本
description: 在本快速入門中，了解如何使用 Azure Resource Manager 範本 (ARM 範本) 建立具有多個公用 IP 位址的 Azure 防火牆和 IP 群組。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 282ce16c926c61008d0ebd70106b161425a15c9e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89079076"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---arm-template"></a>快速入門：建立具有多個公用 IP 位址的 Azure 防火牆 - ARM 範本

在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 部署具有多個公用 IP 位址的 Azure 防火牆和 IP 群組。 已部署的防火牆具有 NAT 規則集合規則，可允許 RDP 連線到兩部 Windows Server 2019 虛擬機器。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如需具有多個公用 IP 位址之 Azure 防火牆的詳細資訊，請參閱 [使用 Azure PowerShell 部署具有多個公用 IP 位址的 Azure 防火牆](deploy-multi-public-ip-powershell.md)。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

此範本會建立具有兩個公用 IP 位址的 Azure 防火牆，以及支援 Azure 防火牆所需的資源。

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/fw-docs-qs)。

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>部署範本

將 ARM 範本部署到 Azure：

1. 選取 [部署至 Azure] 以登入 Azure 並開啟範本。 此範本會建立 Azure 防火牆、網路基礎結構和兩部虛擬機器。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. 在入口網站的 [建立具有多個公用 IP 位址的 Azure 防火牆] 頁面上，輸入或選取下列值：
   - 訂用帳戶：從現有的訂用帳戶選取 
   - 資源群組：從現有的資源群組選取，或選取 [新建]，然後選取 [確定]。
   - 位置：選取位置
   - 管理員使用者名稱：輸入系統管理員使用者帳戶的使用者名稱 
   - 管理員密碼：輸入管理員密碼或金鑰

3. 選取 [我同意上方所述的條款及條件]，然後選取 [購買]。 部署可能需要 10 分鐘或更久的時間才能完成。

## <a name="validate-the-deployment"></a>驗證部署

在 Azure 入口網站中，檢閱已部署的資源。 請注意防火牆的公用 IP 位址。  

使用遠端桌面連線連線到防火牆公用 IP 位址。 成功的連線會示範允許連線到後端伺服器的防火牆 NAT 規則。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為防火牆建立的資源時，請刪除資源群組。 這會移除防火牆和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Azure 入口網站在混合式網路中部署及設定 Azure 防火牆](tutorial-hybrid-portal.md)