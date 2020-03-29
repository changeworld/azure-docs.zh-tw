---
title: Azure 防火牆 SNAT 私人 IP 位址範圍
description: 您可以配置 IP 位址專用範圍，以便防火牆不會將 SNAT 流量連接到這些 IP 位址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064802"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 防火牆 SNAT 私人 IP 位址範圍

當目標 IP 位址位於[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的私人 IP 位址範圍內時，Azure 防火牆不會使用網路規則進行 SNAT。 應用程式規則始終使用[透明代理](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)應用，而不考慮目標 IP 位址。

如果您的組織對私人網路絡使用公共 IP 位址範圍，則 Azure 防火牆 SNAT 會將流量訪問 Azure 防火牆 Subnet 中防火牆私人 IP 位址之一。 但是，您可以將 Azure 防火牆配置為**不**將公共 IP 位址範圍 SNAT。

## <a name="configure-snat-private-ip-address-ranges"></a>配置 SNAT 私人 IP 位址範圍

可以使用 Azure PowerShell 指定防火牆不會 SNAT 的 IP 位址範圍。

### <a name="new-firewall"></a>新防火牆

對於新防火牆，Azure PowerShell 命令為：

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges 擴展為 Azure 防火牆上的當前預設值，同時將其他範圍添加到其中。

有關詳細資訊，請參閱新[A 防火牆](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)。

### <a name="existing-firewall"></a>現有防火牆

要配置現有防火牆，請使用以下 Azure PowerShell 命令：

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>範本

您可以將以下內容添加到該`additionalProperties`部分：

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。