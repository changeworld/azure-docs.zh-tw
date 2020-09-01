---
title: Azure PowerShell 腳本範例-透過網路虛擬裝置路由傳送流量
description: Azure PowerShell 指令碼範例 - 透過防火牆網路虛擬設備來路由傳送流量。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cd7e23ec3b562d50413c5c70841b57cb8182773d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074656"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>透過網路虛擬設備來路由傳送流量

此指令碼範例會建立一個具有前端和後端子網路的虛擬網路。 它也會建立一個已啟用 IP 轉送功能的 VM，以在兩個子網路之間路由傳送流量。 執行此指令碼之後，您可以將網路軟體 (例如防火牆應用程式) 部署到 VM。

您可以視需要使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```
## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立資源群組、虛擬網路及網路安全性群組。 下表中的每個命令都會連結至命令特定的文件。

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

您可以在 [Azure 網路概觀文件](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)中找到其他網路 PowerShell 指令碼範例。
