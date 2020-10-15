---
title: 使用 Powershell 在負載平衡器中開啟應用程式連接埠
description: Azure PowerShell 指令碼範例 - 在 Azure Load Balancer 中開啟 Service Fabric 應用程式的連接埠。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 05/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4be9944a53aaf0c697d55ff567dee7f2fbb3ed85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87038081"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>在 Azure Load Balancer 中開啟應用程式連接埠

在 Azure 中執行的 Service Fabric 應用程式位於 Azure Load Balancer 後方。 這個範例指令碼會在 Azure Load Balancer 中開啟連接埠，以便 Service Fabric 應用程式可以與外部用戶端通訊。 視需要自訂參數。 如果您的叢集位於網路安全性群組中，也要[新增輸入網路安全性群組規則](service-fabric-powershell-add-nsg-rule.md)，以允許輸入流量。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如有需要，可隨同 [Service Fabric SDK](../service-fabric-get-started.md) 一起安裝 Service Fabric PowerShell 模組。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 取得 Azure 資源。  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | 取得 Azure Load Balancer。 |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | 將探查組態新增至負載平衡器。|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | 取得負載平衡器的探查組態。 |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | 將規則組態新增至負載平衡器。 |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | 設定負載平衡器的目標狀態。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。
