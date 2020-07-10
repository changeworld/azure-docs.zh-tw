---
title: 使用 Azure PowerShell 部署具有多個公用 IP 位址的 Azure 防火牆
description: 部署具有多個公用 IP 位址的防火牆，以保護虛擬中樞
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 87af7f0f9b446fb3a54a600f61409c2cfc1a2494
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189401"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>部署具有多個公用 IP 位址的 Azure 防火牆

如果您想要使用 Azure 防火牆來保護虛擬中樞，您可以使用 Azure PowerShell 來部署具有多個公用 IP 位址的防火牆。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>部署防火牆

使用下列 Azure PowerShell 範例來部署具有多個公用 IP 位址的 Azure 防火牆，以保護虛擬中樞。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="delete-a-public-ip-address"></a>刪除公用 IP 位址

您可以使用 Azure PowerShell 從 Azure 防火牆刪除公用 IP 位址。 下列範例會從防火牆刪除一個公用 IP 位址。 其開頭為三個公用 IP 位址。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>後續步驟

[什麼是安全虛擬中樞？](secured-virtual-hub.md)