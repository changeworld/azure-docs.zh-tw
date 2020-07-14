---
title: 快速入門：使用 Azure 防火牆管理員來保護虛擬中樞 - Resource Manager 範本
description: 了解如何使用 Azure 防火牆管理員來保護您的虛擬中樞。
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 6641cc29025d39ddff33e706dd9b1b0da517b884
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563698"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---resource-manager-template"></a>快速入門：使用 Azure 防火牆管理員來保護您的虛擬中樞 - Resource Manager 範本

在本快速入門中，您可以使用 Resource Manager 範本，利用 Azure 防火牆管理員來保護您的虛擬中樞。 已部署的防火牆具有允許連線到 `www.microsoft.com` 的應用程式規則。 已部署兩部 Windows Server 2019 虛擬機器來測試防火牆。 一部跳躍伺服器用來連線到工作負載伺服器。 從工作負載伺服器，您只能連線到 `www.microsoft.com`。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如需 Azure 防火牆管理員的詳細資訊，請參閱[什麼是 Azure 防火牆管理員？](overview.md)。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-secured-virtual-hub"></a>建立安全虛擬中樞

此範本會使用 Azure 防火牆管理員建立受保護的虛擬中樞，以及支援此案例所需的資源。

### <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/fwm-docs-qs/)。

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>部署範本

將 Resource Manager 範本部署至 Azure：

1. 選取 [部署至 Azure] 以登入 Azure 並開啟範本。 此範本會建立 Azure 防火牆、虛擬 WAN 與虛擬中樞、網路基礎結構和兩部虛擬機器。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. 在入口網站的 [受保護虛擬中樞] 頁面上，輸入或選取下列值：
   - 訂用帳戶：從現有的訂用帳戶選取 
   - 資源群組：從現有的資源群組選取，或選取 [新建]，然後選取 [確定]。
   - 位置：選取位置
   - 管理員使用者名稱：輸入系統管理員使用者帳戶的使用者名稱 
   - 管理員密碼：輸入管理員密碼或金鑰

3. 選取 [檢閱 + 建立]，然後選取 [建立]。 部署可能需要 10 分鐘或更久的時間才能完成。

## <a name="validate-the-deployment"></a>驗證部署

現在，測試防火牆規則，以確認其運作符合預期。

1. 從 Azure 入口網站中，檢閱 **Workload-Srv** 虛擬機器的網路設定，並記下私人 IP 位址。
2. 將遠端桌面連線到 **Jump-Srv** 虛擬機器，然後登入。 登入之後，開啟對 **Workload-Srv** 私人 IP 位址的遠端桌面連線。

3. 開啟 Internet Explorer 並瀏覽至 `www.microsoft.com`。
4. 在 Internet Explorer 安全性警示上，選取 [確認] > [關閉]。

   您應該會看到 Microsoft 首頁。

5. 瀏覽至 `www.google.com`。

   您應該會遭到防火牆封鎖。

因此，現在您已確認防火牆規則正在運作：

* 您可以瀏覽至允許 FQDN 的防火牆規則，但不可瀏覽至任何其他的防火牆規則。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為防火牆建立的資源時，請刪除資源群組。 這會移除防火牆和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解安全性合作夥伴提供者](trusted-security-partners.md)
