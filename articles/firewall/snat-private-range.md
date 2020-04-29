---
title: Azure 防火牆 SNAT 私人 IP 位址範圍
description: 您可以設定 IP 位址私人範圍，讓防火牆不會對那些 IP 位址進行 SNAT 流量。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064802"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 防火牆 SNAT 私人 IP 位址範圍

當目的地 IP 位址位於每個[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的私人 ip 位址範圍時，Azure 防火牆不會有網路規則的 SNAT。 應用程式規則一律會使用[透明 proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)來套用，而不論目的地 IP 位址為何。

如果您的組織使用私人網路的公用 IP 位址範圍，Azure 防火牆會 SNATs AzureFirewallSubnet 中其中一個防火牆私人 IP 位址的流量。 不過，您可以將 Azure 防火牆設定為**不**會 SNAT 公用 IP 位址範圍。

## <a name="configure-snat-private-ip-address-ranges"></a>設定 SNAT 私人 IP 位址範圍

您可以使用 Azure PowerShell 來指定防火牆不會 SNAT 的 IP 位址範圍。

### <a name="new-firewall"></a>新防火牆

針對新的防火牆，Azure PowerShell 命令為：

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges 會擴充至 Azure 防火牆上的目前預設值，而其他範圍則會加入其中。

如需詳細資訊，請參閱[AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)。

### <a name="existing-firewall"></a>現有的防火牆

若要設定現有的防火牆，請使用下列 Azure PowerShell 命令：

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>範本

您可以將下列內容新增至`additionalProperties`區段：

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。