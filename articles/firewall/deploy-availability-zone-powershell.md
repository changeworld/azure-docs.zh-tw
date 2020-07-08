---
title: 使用 PowerShell 部署具有可用性區域的 Azure 防火牆
description: 在本文中，您將瞭解如何使用 Azure PowerShell 來部署具有可用性區域的 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: e051f57d27c0eea585c63dca5e124e0846752be5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602477"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>使用 Azure PowerShell 部署具有可用性區域的 Azure 防火牆

Azure 防火牆可在部署期間進行設定，以跨越多個可用性區域來增加可用性。

這項功能可實現下列案例：

- 您可以增加99.99% 執行時間的可用性。 如需詳細資訊，請參閱 Azure 防火牆[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 選取兩個或多個可用性區域時，會提供 99.99% 運作時間 SLA。
- 您也可以單純基於鄰近性而將 Azure 防火牆關聯到特定區域，並使用服務標準的 99.95% SLA。

如需 Azure 防火牆可用性區域的詳細資訊，請參閱[什麼是 Azure 防火牆？](overview.md)

下列 Azure PowerShell 範例會示範如何使用可用性區域部署 Azure 防火牆。

## <a name="create-a-firewall-with-availability-zones"></a>建立具有可用性區域的防火牆

這個範例會在區域1、2和3中建立防火牆。

建立標準公用 IP 位址時，不會指定特定區域。 這會根據預設建立區域冗余 IP 位址。 標準公用 IP 位址可以在所有區域或單一區域中進行設定。

請務必知道，因為您在區域1中不能有防火牆，而在區域2中不能有 IP 位址。 但是，您可以在 [區域 1] 和 [所有區域中的 IP 位址] 中擁有防火牆，或在相同的單一區域中擁有防火牆和 IP 位址，以進行鄰近性目的。

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>後續步驟

- [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
