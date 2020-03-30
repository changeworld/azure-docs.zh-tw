---
title: 使用 PowerShell 使用可用性區域部署 Azure 防火牆
description: 在本文中，您將瞭解如何使用 Azure PowerShell 部署具有可用性區域的 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195915"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>使用 Azure PowerShell 部署具有可用性區域的 Azure 防火牆

Azure 防火牆可在部署期間進行設定，以跨越多個可用性區域來增加可用性。

此功能支援以下方案：

- 您可以將可用性提高到 99.99%。 如需詳細資訊，請參閱 Azure 防火牆[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 選取兩個或多個可用性區域時，會提供 99.99% 運作時間 SLA。
- 您也可以單純基於鄰近性而將 Azure 防火牆關聯到特定區域，並使用服務標準的 99.95% SLA。

有關 Azure 防火牆可用性區域的詳細資訊，請參閱[什麼是 Azure 防火牆？](overview.md)

以下 Azure PowerShell 示例演示如何使用可用區域部署 Azure 防火牆。

## <a name="create-a-firewall-with-availability-zones"></a>使用可用區域創建防火牆

本示例在區域 1、2 和 3 中創建防火牆。

創建標準公共 IP 位址時，未指定特定區域。 預設情況下，這將創建區域冗余 IP 位址。 標準公共 IP 位址可以在所有區域或單個區域中配置。

請務必瞭解，因為區域 1 中不能有防火牆，在區域 2 中不能有 IP 位址。 但是，您可以在所有區域中具有區域 1 和 IP 位址中的防火牆，也可以在同一單個區域中具有防火牆和 IP 位址，以便進行接近。

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
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>後續步驟

- [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
