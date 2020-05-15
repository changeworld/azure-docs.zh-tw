---
title: Azure PowerShell 範例 - 建立完整的虛擬機器擴展集
description: 此指令碼會建立執行 Windows Server 2016 的虛擬機器擴展集，其中可設定和建立個別資源。
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 2d4e252484515f43e53bdfbed58f7350d4d82cb3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200274"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>使用 PowerShell 建立完整的虛擬機器擴展集

此指令碼會建立執行 Windows Server 2016 的虛擬機器擴展集。 此時會設定和建立個別資源，而不是使用 [New-AzVmss 中提供於此處的內建資源建立選項](powershell-sample-create-simple-scale-set.md)。 執行指令碼之後，您可以透過 RDP 存取 VM 執行個體。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>清除部署
執行下列命令來移除資源群組、擴展集和所有相關資源。

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 此組態可使用於虛擬網路建立程序。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。 |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | 建立負載平衡器的前端 IP 組態。 |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | 建立負載平衡器的後端位址集區設定。 |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | 建立負載平衡器的輸入 NAT 規則組態。 |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | 建立負載平衡器。 |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | 建立負載平衡器的探查組態。 |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | 建立負載平衡器的規則組態。 |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | 使用提供的資訊更新負載平衡器。 |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | 建立擴展集 VM 執行個體的 IP 組態。 VM 執行個體會連線至負載平衡器後端集區、NAT 集區和虛擬網路子網路。 |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | 建立擴展集組態。 此組態會包含多項資訊，例如要建立的 VM 執行個體數目、VM SKU (大小)，以及升級原則模式等。 此組態可藉由其他 Cmdlet 來新增，並在擴展集建立期間使用。 |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | 定義要用於 VM 執行個體的映像，並將其新增至擴展集組態。 |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | 定義系統管理使用者名稱和密碼認證，以及 VM 的命名前置詞。 請將這些值新增至擴展集組態。 |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | 根據 IP 組態，將虛擬網路介面新增至 VM 執行個體。 請將這些值新增至擴展集組態。 |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | 根據擴展集組態中提供的資訊來建立擴展集。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟
如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure 虛擬機器擴展集文件](../powershell-samples.md)中找到其他的虛擬機器擴展集 PowerShell 指令碼範例。
