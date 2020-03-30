---
title: 使用 PowerShell 部署具有多個公共 IP 位址的 Azure 防火牆
description: 在本文中，您將瞭解如何使用 Azure PowerShell 部署具有多個公共 IP 位址的 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ad54b60d8f15e36636f887015d97967740123669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195872"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>使用 Azure PowerShell 部署具有多個公用 IP 位址的 Azure 防火牆

此功能支援以下方案：

- **DNAT** - 您可以將多個標準連接埠執行個體轉譯到後端伺服器。 例如，如果您有兩個公用 IP 位址，您可以為這兩個 IP 位址轉譯 TCP 通訊埠 3389 (RDP)。
- **SNAT** - 其他連接埠可用於輸出 SNAT 連線，降低 SNAT 連接埠耗盡的可能性。 目前，Azure 防火牆會隨機選取來源公用 IP 位址以用於連線。 如果您的網路上有任何下游篩選，則您必須允許與防火牆相關聯的所有公用 IP 位址。
 
您可以透過 Azure 入口網站、Azure PowerShell、Azure CLI、REST 和範本使用具有多個公用 IP 位址的 Azure 防火牆。 可以部署具有多達 100 個公共 IP 位址的 Azure 防火牆。

以下 Azure PowerShell 示例演示如何配置、添加和刪除 Azure 防火牆的公共 IP 位址。

> [!NOTE]
> 無法從 Azure 防火牆公共 IP 位址配置頁中刪除第一個 ip 配置。 如果要修改 IP 位址，可以使用 Azure PowerShell。

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>創建具有兩個或多個公共 IP 位址的防火牆

本示例創建一個防火牆，該防火牆連接到具有兩個公共 IP 位址的虛擬網路*vnet。*

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

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>將公共 IP 位址添加到現有防火牆

在此示例中，公共 IP 位址*azFwPublicIp1*連接到防火牆。

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>從現有防火牆中刪除公共 IP 位址

在此示例中，公共 IP 位址*azFwPublicIp1*與防火牆分離。

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

* [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
