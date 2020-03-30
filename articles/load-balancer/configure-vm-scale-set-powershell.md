---
title: 使用現有 Azure 負載等化器配置虛擬機器規模集 - Azure PowerShell
description: 瞭解如何使用現有 Azure 負載等化器配置虛擬機器規模集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349999"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>使用 Azure PowerShell 配置使用現有 Azure 負載等化器的虛擬機器規模集

在本文中，您將瞭解如何使用現有的 Azure 負載等化器配置虛擬機器規模集。 

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。
- 將部署虛擬機器規模集的訂閱中的現有標準 sKU 負載等化器。
- 虛擬機器縮放集的 Azure 虛擬網路。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>登入 Azure CLI

登錄到 Azure。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>使用現有負載等化器部署虛擬機器規模集

將括弧中的值替換為配置中資源的名稱。

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

下面的示例部署虛擬機器規模集，該集具有：

- 名為**myVMSS 的**虛擬機器規模集
- Azure 負載等化器名為**myLoad 平衡器**
- 負載等化器後端池名為 **"我的後端池**"
- 名為**myVnet 的**Azure 虛擬網路
- 名為 **"我的子網"的子網**
- 名為 **"我的資源組"的資源組**

```azureppowershell-interactive

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
> 創建比例集後，無法為負載等化器的運行狀況探測使用的負載平衡規則修改後端埠。 要更改埠，可以通過更新 Azure 虛擬機器縮放集、更新埠然後再次配置運行狀況探測來刪除運行狀況探測。

## <a name="next-steps"></a>後續步驟

在本文中，您部署了一個虛擬機器縮放集與現有的 Azure 負載等化器。  要瞭解有關虛擬機器縮放集和負載等化器的更多詳細資訊，請參閱：

- [什麼是 Azure Load Balancer？](load-balancer-overview.md)
- [什麼是虛擬機器擴展集？](../virtual-machine-scale-sets/overview.md)
                                