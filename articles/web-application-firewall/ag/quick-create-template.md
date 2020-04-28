---
title: 快速入門：在應用程式閘道上建立 Azure WAF v2 - Resource Manager 範本
titleSuffix: Azure Application Gateway
description: 了解如何使用 Resource Manager 範本在 Azure 應用程式閘道上建立 Web 應用程式防火牆 v2。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 6759071e73adfd3af4ac780da6db3a0e6e967ea1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617982"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>快速入門：在應用程式閘道上建立 Azure WAF v2 - Resource Manager 範本

在本快速入門中，您會使用 Resource Manager 範本在應用程式閘道上建立 Web 應用程式防火牆 v2。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-web-application-firewall"></a>建立 Web 應用程式防火牆

此範本會在 Azure 應用程式閘道上建立簡單的 Web 應用程式防火牆 v2。 這包括公用 IP 前端 IP 位址、HTTP 設定、連接埠 80 上具有基本接聽程式的規則，以及後端集區。 系統會建立具有自訂規則的 WAF 原則，以根據 IP 位址相符類型來封鎖後端集區的流量。

### <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)：一個用於應用程式閘道，兩個用於虛擬機器。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：兩個虛擬機器
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：兩個用於虛擬機器
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions)：可設定 IIS 和網頁

### <a name="deploy-the-template"></a>部署範本

將 Resource Manager 範本部署至 Azure：

1. 選取 [部署至 Azure]  以登入 Azure 並開啟範本。 此範本會在執行 IIS 的後端集區中建立應用程式閘道、網路基礎結構和兩個虛擬機器。

   [![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. 選取或建立資源群組。
3. 選取 [我同意上方所述的條款及條件]  ，然後選取 [購買]  。 部署可能需要 10 分鐘或更久的時間才能完成。

## <a name="validate-the-deployment"></a>驗證部署

雖然不需要 IIS 即可建立應用程式閘道，但仍會安裝在後端伺服器上，以確認 Azure 是否已在應用程式閘道上成功建立 WAF v2。 

使用 IIS 測試應用程式閘道：

1. 在 [概觀]  頁面上尋找應用程式閘道的公用 IP 位址。![記錄應用程式閘道公用 IP 位址](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) 或者，您可以選取 [所有資源]  ，並在搜尋方塊中輸入 *myAGPublicIPAddress*，然後在搜尋結果中加以選取。 Azure 會在 [概觀]  頁面上顯示公用 IP 位址。
2. 將公用 IP 位址複製並貼到您瀏覽器的網址列，以瀏覽該 IP 位址。
3. 檢查回應。 **403 禁止** 回應會確認已成功建立 WAF，並封鎖與後端集區的連線。
4. 將自訂規則變更為 [允許流量]  。
  執行下列 Azure PowerShell 指令碼，並取代您的資源組名：
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   多次重新整理瀏覽器後，您應該會看到 myVM1 和 myVM2 的連線。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為應用程式閘道建立的資源時，請刪除資源群組。 這會移除應用程式閘道和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Azure 入口網站建立包含 Web 應用程式防火牆的應用程式閘道](application-gateway-web-application-firewall-portal.md)