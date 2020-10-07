---
title: 快速入門：建立 Azure 防火牆和 IP 群組 - Resource Manager 範本
description: 在本快速入門中，了解如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure 防火牆和 IP 群組。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 7966cbe44fc8984cf3c3d9e2d08a11bf599ff158
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89079127"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>快速入門：建立 Azure 防火牆和 IP 群組 - ARM 範本

在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 搭配網路規則和應用程式規則中使用的範例 IP 群組，以部署 Azure 防火牆。 IP 群組是最上層資源，可讓您定義 IP 位址、範圍和子網定義，並將其群組為單一物件。 這適用於管理 Azure 防火牆規則中的 IP 位址。 您可手動輸入 IP 位址，或從檔案進行匯入。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

此範本會建立 Azure 防火牆和 IP 群組，以及支援 Azure 防火牆所需的資源。

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox)。

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. 在入口網站的 [使用 IpGroups 建立 AzureFirewall] 頁面上，輸入或選取下列值：
   - 訂用帳戶：從現有的訂用帳戶選取 
   - 資源群組：從現有的資源群組選取，或選取 [新建]，然後選取 [確定]。
   - 位置：選取位置
   - 虛擬網路名稱：輸入新虛擬網路 (VNet) 的名稱 
   - IP 群組名稱 1：輸入 IP 群組 1 的名稱 
   - IP 群組名稱 2：輸入 IP 群組 2 的名稱 
   - 管理員使用者名稱：輸入系統管理員使用者帳戶的使用者名稱 
   - 驗證：選取 sshPublicKey 或密碼 
   - 管理員密碼：輸入管理員密碼或金鑰

3. 選取 [我同意上方所述的條款及條件]，然後選取 [購買]。 部署可能需要 10 分鐘或更久的時間才能完成。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

在 Azure 入口網站中，檢閱已部署的資源，特別是使用 IP 群組的防火牆規則。

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP 群組。":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="IP 群組。":::

若要了解範本中防火牆的 JSON 語法和屬性，請參閱 [Microsoft.Network azureFirewalls 範本參考](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls)。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為防火牆建立的資源時，請刪除資源群組。 這會移除防火牆和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Azure 入口網站在混合式網路中部署及設定 Azure 防火牆](tutorial-hybrid-portal.md)