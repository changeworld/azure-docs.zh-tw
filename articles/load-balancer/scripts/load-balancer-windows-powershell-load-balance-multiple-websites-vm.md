---
title: 平衡多個網站的負載 - Azure PowerShell - Azure Load Balancer
description: 此 Azure PowerShell 指令碼範例示範如何將多個網站負載平衡至相同的虛擬機器
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.openlocfilehash: b7beb5186bd039d7098883915663e8a3286fb99d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76274607"
---
# <a name="azure-powershell-script-example-load-balance-multiple-websites"></a>Azure PowerShell 指令碼範例：平衡多個網站的負載

此 Azure PowerShell 指令碼範例使用可用性設定組成員的兩個虛擬機器 (VM) 建立虛擬網路。 負載平衡器會將兩個不同 IP 位址的流量傳送至這兩個 VM。 執行指令碼之後，您可以將 Web 伺服器軟體部署至 VM 並主控多個網站 (各有自己的 IP 位址)。

您可以視需要使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令建立資源群組、虛擬網路、負載平衡器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | 建立 Azure 可用性設定組以提供高可用性。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 此組態可使用於虛擬網路建立程序。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。 |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | 建立負載平衡器的前端 IP 設定。 |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | 建立負載平衡器的後端位址集區設定。 |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | 建立 NLB 探查。 NLB 探查可用來監視 NLB 集合中的每部 VM。 如有任何 VM 變得無法存取，就不會將流量路由至該 VM。 |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | 建立 NLB 規則。 在此範例中，會為連接埠 80 建立規則。 當 HTTP 流量抵達 NLB 時，就會經由連接埠 80 將其路由傳送至 NLB 集合的其中一部 VM。 |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | 建立負載平衡器。 |
| [New-AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | 定義虛擬網路介面的進階功能。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 建立網路介面。 |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | 建立 VM 組態。 此組態包括 VM 名稱、作業系統和系統管理認證等資訊。 建立 VM 時會使用此組態。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 建立虛擬機器。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/azure/overview)。

您可以在 [Azure 網路概觀文件](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)中找到其他網路 PowerShell 指令碼範例。
