---
title: Azure PowerShell 指令碼範例 - 建立 WAF 自訂規則
description: Azure PowerShell 指令碼範例 - 根據應用程式閘道自訂規則建立 Web 應用程式防火牆
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.openlocfilehash: 950f71c284268a9aa2773eb57213e266622d85bd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73509552"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>使用 Azure PowerShell 建立 WAF 自訂規則

此指令碼會建立可使用自訂規則的應用程式閘道 Web 應用程式防火牆。 如果要求標頭包含使用者代理程式 *evilbot*，則自訂規則會封鎖流量。

## <a name="prerequisites"></a>Prerequisites

### <a name="azure-powershell-module"></a>Azure PowerShell 模組

如果您選擇在本機安裝和使用 Azure PowerShell，此指令碼需要 Azure PowerShell 模組 2.1.0 版或更新版本。

1. 若要尋找版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。
2. 若要建立與 Azure 的連線，請執行 `Connect-AzAccount`。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、應用程式閘道和所有相關資源。

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 使用子網路組態建立虛擬網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立應用程式閘道的公用 IP 位址。 |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | 建立組態，該組態讓子網路與應用程式閘道產生關聯。 |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | 建立組態，該組態將公用 IP 位址指派給應用程式閘道。 |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | 指派要用來存取應用程式閘道的連接埠。 |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | 為應用程式閘道建立後端集區。 |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | 設定後端集區的設定。 |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | 建立接聽程式。 |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | 建立路由規則。 |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | 指定應用程式閘道的層級和容量。 |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | 建立應用程式閘道。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|建立應用程式閘道的自動調整組態。|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|建立防火牆條件的比對變數。|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|建立自訂規則的比對條件。|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|建立應用程式閘道防火牆原則的新自訂規則。|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|建立應用程式閘道防火牆原則。|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|建立應用程式閘道的 WAF 組態。|

## <a name="next-steps"></a>後續步驟

- 如需 WAF 自訂規則的詳細資訊，請參閱 [Web 應用程式防火牆的自訂規則](../ag/custom-waf-rules-overview.md)
- 如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。
