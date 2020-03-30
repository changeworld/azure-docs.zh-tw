---
title: 在 Azure 虛擬網路中保留公共 IPv6 位址和位址範圍
titlesuffix: Azure Virtual Network
description: 瞭解如何在 Azure 虛擬網路中保留公共 IPv6 位址和位址範圍。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72595077"
---
# <a name="reserve-public-ipv6-address-prefix"></a>保留公共 IPv6 位址首碼
Azure 虛擬網路 （VNet） 的 IPv6 使您能夠在 Azure 中託管應用程式，在虛擬網路中以及從 Internet 中使用 IPv6 和 IPv4 連接。 除了保留單個 IPv6 位址外，還可以保留連續範圍的 Azure IPv6 位址（稱為 IP 首碼）供您使用。 本文介紹如何使用 Azure PowerShell 和 CLI 創建 IPv6 公共 IP 位址和位址範圍。

> [!Important]
> Azure 虛擬網路的 IPv6 當前處於公共預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-single-reserved-ipv6-public-ip"></a>創建單個保留的 IPv6 公共 IP

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

您可以使用 Azure PowerShell 使用[新 AzPublicIp 位址](/powershell/module/az.network/new-azpublicipaddress)創建單個保留（靜態）IPv6 公共 IP 位址，如下所示：

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>使用 Azure CLI

 您可以創建單個保留（靜態）IPv6 公共 IP 位址 Azure CLI，使用[az 網路公共 ip 創建](/cli/azure/network/public-ip)，如下所示：
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>創建保留的 IPv6 首碼（範圍）

要保留 IPv6 首碼，將 IPv6 的 IP 位址系列添加到用於創建 IPv4 首碼的同一命令中。 以下命令創建大小 /125 （8 IPv6 位址） 的首碼。  

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

可以使用 Azure CLI 創建公共 IPv6 位址，並使用[az 網路公共 ip 創建](/powershell/module/az.network/new-azpublicipprefix)，如下所示：
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>使用 Azure CLI

您可以使用 Azure CLI 創建公共 IPv6 位址，如下所示：

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>從保留的 IPv6 首碼分配公共 IP 位址

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

 在使用 Azure PowerShell 創建公共 IP 時，`-PublicIpPrefix`通過添加參數，從保留的首碼創建靜態 IPv6 公共 IP。 以下示例假定首碼已創建並存儲在名為 *：$myOwnIPv6Prefix*的 PowerShell 變數中。

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>使用 Azure CLI
 
下面的示例假定首碼已創建並存儲在名為 *：IPv6PrefixWestUS*的 CLI 變數中。

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>後續步驟
- 瞭解有關[IPv6 位址首碼](ipv6-public-ip-address-prefix.md)的更多。
- 瞭解有關[IPv6 位址的更多。](ipv6-overview.md)
