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
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420552"
---
# <a name="reserve-public-ipv6-address-prefix"></a>保留公共 IPv6 位址前置字串
Azure 虛擬網路 (VNet) 的 IPv6 使您能夠在 Azure 中託管應用程式,在虛擬網路中以及從 Internet 中使用 IPv6 和 IPv4 連接。 除了保留單個 IPv6 位址外,還可以保留連續範圍的 Azure IPv6 位址(稱為 IP 首碼)供您使用。 本文介紹如何使用 Azure PowerShell 和 CLI 創建 IPv6 公共 IP 位址和位址範圍。


## <a name="create-a-single-reserved-ipv6-public-ip"></a>建立單一個保留的 IPv6 公共 IP

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

您可以使用 Azure PowerShell 使用[新的 AzPublicIp 位址](/powershell/module/az.network/new-azpublicipaddress)建立單一次保留(靜態)IPv6 公共 IP 位址,如下所示:

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

 您可以建立單一次保留(靜態)IPv6 公共 IP 位址 Azure CLI,使用[az 網路公共 ip 建立](/cli/azure/network/public-ip),如下所示:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>建立保留的 IPv6 首數(ran)

要保留 IPv6 首碼,將 IPv6 的 IP 位址系列添加到用於創建 IPv4 首碼的同一指令中。 以下命令建立大小 /125 (8 IPv6 位址) 的前置碼。  

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

可以使用 Azure CLI 建立公共 IPv6 位址,並使用[az 網路公共 ip 創建](/powershell/module/az.network/new-azpublicipprefix),如下所示:
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

您可以使用 Azure CLI 建立公共 IPv6 位址,如下所示:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>從保留的 IPv6 首元配置公共 IP 位址

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

 使用 Azure PowerShell 建立公共`-PublicIpPrefix`IP 時, 透過新增參數,從保留的前置碼建立靜態 IPv6 公共 IP。 以下範例假定前置碼已建立並儲存在名為 *:$myOwnIPv6Prefix*的 PowerShell 變數中。

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
 
下面的範例假定前置碼已建立並儲存在名為 *:IPv6PrefixWestUS*的 CLI 變數中。

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
- 瞭解有關[IPv6 位址前置字](ipv6-public-ip-address-prefix.md)的更多。
- 瞭解有關[IPv6 位址的更多。](ipv6-overview.md)
