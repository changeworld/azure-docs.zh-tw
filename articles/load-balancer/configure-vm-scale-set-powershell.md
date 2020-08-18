---
title: 使用現有的 Azure Load Balancer Azure PowerShell 設定虛擬機器擴展集
description: 瞭解如何使用現有的 Azure Load Balancer 來設定虛擬機器擴展集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510057"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>使用 Azure PowerShell 來設定具有現有 Azure Load Balancer 的虛擬機器擴展集

在本文中，您將瞭解如何使用現有的 Azure Load Balancer 來設定虛擬機器擴展集。

## <a name="prerequisites"></a>先決條件

- Azure 訂用帳戶。
- 訂用帳戶中的現有標準 sku 負載平衡器，將在其中部署虛擬機器擴展集。
- 虛擬機器擴展集的 Azure 虛擬網路。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>登入 Azure CLI

登入 Azure。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>使用現有的負載平衡器部署虛擬機器擴展集

將括弧中的值取代為您設定中的資源名稱。

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

下列範例會使用下列方式部署虛擬機器擴展集：

- 名為 **>myvmss**的虛擬機器擴展集
- Azure Load Balancer 名為 **myLoadBalancer**
- 名為**myBackendPool**的負載平衡器後端集區
- 名為**myVnet**的 Azure 虛擬網路
- 名為 **>mysubnet**的子網
- 名為**myResourceGroup**的資源群組

```azurepowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> 建立擴展集之後，就無法針對負載平衡器健康情況探查所使用的負載平衡規則，修改後端埠。 若要變更通訊埠，您可以藉由更新 Azure 虛擬機器擴展集來移除健康情況探查、更新埠，然後再次設定健康情況探查。

## <a name="next-steps"></a>後續步驟

在本文中，您已部署具有現有 Azure Load Balancer 的虛擬機器擴展集。  若要深入瞭解虛擬機器擴展集與負載平衡器，請參閱：

- [什麼是 Azure Load Balancer？](load-balancer-overview.md)
- [什麼是虛擬機器擴展集？](../virtual-machine-scale-sets/overview.md)
