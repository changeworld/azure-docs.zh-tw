---
title: 在 Azure 虛擬網路中保留公用 IPv6 位址和位址範圍
titlesuffix: Azure Virtual Network
description: 瞭解如何在 Azure 虛擬網路中保留公用 IPv6 位址和位址範圍。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: eecfebc90c28b650af0cef4ee0e4ddc227af0e8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711488"
---
# <a name="reserve-public-ipv6-address-prefix"></a>保留公用 IPv6 位址首碼
Azure 虛擬網路 (VNet) 的 IPv6 可讓您在 Azure 中裝載應用程式，並在虛擬網路內以及網際網路之間建立 IPv6 和 IPv4 連線。 除了保留個別的 IPv6 位址，您還可以保留 Azure IPv6 位址的連續範圍（也稱為 IP 首碼）供您使用。 這篇文章說明如何使用 Azure PowerShell 和 CLI 建立 IPv6 公用 IP 位址和位址範圍。


## <a name="create-a-single-reserved-ipv6-public-ip"></a>建立單一保留的 IPv6 公用 IP

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

您可以使用 Azure PowerShell 搭配[get-azpublicipaddress](/powershell/module/az.network/new-azpublicipaddress) ，建立單一保留（靜態） IPV6 公用 IP 位址，如下所示：

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

 您可以使用[az network public ip create](/cli/azure/network/public-ip)來建立單一保留（靜態） IPV6 公用 ip 位址 Azure CLI，如下所示：
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>建立保留的 IPv6 首碼（範圍）

若要保留 IPv6 首碼，請將 IPv6 的 IP 位址系列新增至用來建立 IPv4 首碼的相同命令。 下列命令會建立大小/125 （8個 IPv6 位址）的前置詞。  

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

您可以使用 Azure CLI 搭配[az network public-ip create](/powershell/module/az.network/new-azpublicipprefix)來建立公用 IPv6 位址，如下所示：
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

您可以使用 Azure CLI 建立公用 IPv6 位址，如下所示：

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>從保留的 IPv6 首碼配置公用 IP 位址

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

 您可以在 `-PublicIpPrefix` 使用 Azure PowerShell 建立公用 ip 時新增引數，藉以從保留的首碼建立靜態 IPv6 公用 ip。 下列範例假設已建立前置詞，並將其儲存在名為的 PowerShell 變數中： *$MyOwnIPv 6prefix*。

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
 
下列範例假設前置詞已建立並儲存在名為： *IPv6PrefixWestUS*的 CLI 變數中。

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
- 深入瞭解[IPv6 位址首碼](ipv6-public-ip-address-prefix.md)。
- 深入瞭解[IPv6 位址](ipv6-overview.md)。
