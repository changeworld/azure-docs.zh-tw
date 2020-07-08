---
title: 使用 PowerShell 部署具有多個公用 IP 位址的 Azure 防火牆
description: 在本文中，您將瞭解如何使用 Azure PowerShell 來部署具有多個公用 IP 位址的 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610671"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>使用 Azure PowerShell 部署具有多個公用 IP 位址的 Azure 防火牆

這項功能可實現下列案例：

- **DNAT** - 您可以將多個標準連接埠執行個體轉譯到後端伺服器。 例如，如果您有兩個公用 IP 位址，您可以為這兩個 IP 位址轉譯 TCP 通訊埠 3389 (RDP)。
- **SNAT** - 其他連接埠可用於輸出 SNAT 連線，降低 SNAT 連接埠耗盡的可能性。 目前，Azure 防火牆會隨機選取來源公用 IP 位址以用於連線。 如果您的網路上有任何下游篩選，則您必須允許與防火牆相關聯的所有公用 IP 位址。 請考慮使用[公用 IP 位址首碼](../virtual-network/public-ip-address-prefix.md)來簡化此設定。
 
您可以透過 Azure 入口網站、Azure PowerShell、Azure CLI、REST 和範本使用具有多個公用 IP 位址的 Azure 防火牆。 您可以部署最多250個公用 IP 位址的 Azure 防火牆。

下列 Azure PowerShell 範例會示範如何設定、新增和移除 Azure 防火牆的公用 IP 位址。

> [!NOTE]
> 您無法從 Azure 防火牆公用 IP 位址設定頁面移除第一個 ipConfiguration。 如果您想要修改 IP 位址，您可以使用 Azure PowerShell。

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>建立具有兩個或多個公用 IP 位址的防火牆

此範例會建立連結至具有兩個公用 IP 位址之虛擬網路*vnet*的防火牆。

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

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>將公用 IP 位址新增至現有的防火牆

在此範例中，公用 IP 位址*azFwPublicIp1*會附加至防火牆。

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>從現有的防火牆移除公用 IP 位址

在此範例中，公用 IP 位址*azFwPublicIp1*會從防火牆卸離。

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>後續步驟

* [快速入門：阵立具有多個公用 IP 位址的 Azure 防火牆 - Resource Manager 範本](quick-create-multiple-ip-template.md)
