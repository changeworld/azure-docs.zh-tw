---
title: 使用 Azure PowerShell 建立及設定 Azure DDoS 保護方案
description: 瞭解如何使用 Azure PowerShell 來建立 DDoS 保護計劃
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 49fa0c849a6b2eab0647922a711c50b4fe762584
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095657"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立及設定 Azure DDoS 保護 Standard

使用 Azure PowerShell Azure DDoS 保護 Standard 入門。 

DDoS 保護計劃會定義一組跨訂用帳戶且已啟用標準 DDoS 保護的虛擬網路。 您可以為組織設定一個 DDoS 保護計劃，然後將來自多個訂用帳戶的虛擬網路連結至該相同計劃。 

在本快速入門中，您將建立 DDoS 保護計劃，並將它連結至虛擬網路。 

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure PowerShell 或 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>建立 DDoS 保護計劃

在 Azure 中，您可以將相關資源配置到資源群組。 您可以使用現有的資源群組，或建立一個新的群組。

若要建立資源群組，請使用 [New->new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)。 在此範例中，我們會將資源群組命名為 _MyResourceGroup_ ，並使用 _美國東部_ 位置：

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

現在，建立名為 _MyDdosProtectionPlan_ 的 DDoS 保護計劃：

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>為虛擬網路啟用 DDoS

### <a name="enable-ddos-for-a-new-virtual-network"></a>為新虛擬網路啟用 DDoS

您可以在建立虛擬網路時啟用 DDoS 保護。 在此範例中，我們會將虛擬網路命名為 _MyVnet_ ： 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>為現有的虛擬網路啟用 DDoS 保護

建立 DDoS 保護計劃時，您可以建立現有虛擬網路的關聯：

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>驗證和測試

首先，請查看 DDoS 保護計劃的詳細資料：

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

確認命令會傳回您 DDoS 保護計劃的正確詳細資料。

## <a name="clean-up-resources"></a>清除資源

您可以保留資源以供下一個教學課程之用。 如果不再需要，請刪除 _MyResourceGroup_ 資源群組。 當您刪除資源群組時，也會刪除 DDoS 保護計劃及其所有相關資源。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

停用虛擬網路的 DDoS 保護： 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

如果您想要刪除 DDoS 保護計劃，您必須先中斷所有虛擬網路與其的關聯。

## <a name="next-steps"></a>後續步驟

若要瞭解如何為您的 DDoS 保護計劃查看及設定遙測，請繼續進行教學課程。

> [!div class="nextstepaction"]
> [檢視和設定 DDoS 保護遙測](telemetry-monitoring-alerting.md)