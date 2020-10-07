---
title: 透過 NVA 路由傳送流量 - Azure PowerShell 指令碼範例
description: Azure PowerShell 指令碼範例 - 透過防火牆 NVA 路由傳送流量。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19bebb5c1817882dcb641a284332926e51a8dd2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313970"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>透過網路虛擬設備路由流量的指令碼範例

此指令碼範例會建立一個具有前端和後端子網路的虛擬網路。 它也會建立一個已啟用 IP 轉送功能的 VM，以在兩個子網路之間路由傳送流量。 執行此指令碼之後，您可以將網路軟體 (例如防火牆應用程式) 部署到 VM。

您可以從 Azure [Cloud Shell](https://shell.azure.com/powershell) 或從本機的 PowerShell 安裝來執行指令碼。 如果您在本機使用 PowerShell，此指令碼需要使用 Az PowerShell 模組 5.4.1 版或更新版本。 若要尋找已安裝的版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、VM 和所有相關資源：

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立資源群組、虛擬網路及網路安全性群組。 下表中的每個命令都會連結至命令特定的文件：

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | 建立用來存放所有資源的資源群組。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立 Azure 虛擬網路和前端子網路。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立後端和 DMZ 子網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址以從網際網路存取 VM。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 建立虛擬網路介面並為它啟用 IP 轉送功能。 |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組 (NSG)。 |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | 建立允許對 VM 進行 HTTP 和 HTTPS 連接埠輸入的 NSG 規則。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| 將 NSG 和路由表與子網路建立關聯。 |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| 為所有路由建立一個路由表。 |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| 建立路由，以透過 VM 在子網路與網際網路之間路由傳送流量。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 建立虛擬機器，並將 NIC 連結到此虛擬機器。 此命令也會指定要使用的虛擬機器映像和系統管理認證。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | 刪除資源群組及其包含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/azure/)。

可以在[虛擬網路 PowerShell 範例](../powershell-samples.md)中找到其他的虛擬網路 PowerShell 指令碼範例。
