---
title: 使用 Azure PowerShell 管理虛擬機器擴展集
description: 管理虛擬機器擴展集 (例如如何啟動和停止執行個體，或變更擴展集容量) 的一般 Azure PowerShell Cmdlet。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 68b5aa21f861009dd78f48428fa0ffdc5b5ae3a3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124863"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>使用 Azure PowerShell 管理虛擬機器擴展集

在虛擬機器擴展集生命週期期間，您可能需要執行一或多個管理工作。 此外，您可以建立指令碼來自動化各種生命週期工作。 本文詳述一些可讓您執行這些工作的一般 Azure PowerShell Cmdlet。

如果您需要建立虛擬機器擴展集，可以[使用 Azure PowerShell 建立擴展集](quick-create-powershell.md)。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>檢視擴展集的相關資訊
若要檢視擴展集的整體資訊，請使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)。 下列範例取得 myResourceGroup 資源群組中 myScaleSet 擴展集的相關資訊。 輸入您自己的名稱，如下所示：

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>檢視擴展集中的 VM
若要檢視擴展集中的 VM 執行個體清單，請使用 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)。 下列範例列出 myScaleSet 擴展集和 myResourceGroup 資源群組中的所有虛擬機器執行個體。 針對這些名稱提供您自己的值：

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

若要檢視特定 VM 執行個體的其他資訊，請將 `-InstanceId` 參數新增至 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)，並指定要檢視的執行個體。 下列範例檢視 myScaleSet 擴展集和 myResourceGroup 資源群組中 VM 執行個體 *0* 的相關資訊。 輸入您自己的名稱，如下所示：

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>變更擴展集的容量
上述命令顯示您擴展集和 VM 執行個體的相關資訊。 若要增加或減少擴展集中的執行個體數目，您可以變更容量。 擴展集會自動建立或移除所需的 VM 數目，然後設定接收應用程式流量的 VM。

首先，使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 建立擴展集物件，然後指定 `sku.capacity` 的新值。 若要套用容量變更，請使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)。 下列範例將 *myResourceGroup* 資源群組中的 *myScaleSet* 更新為 *5* 個執行個體的容量。 提供您自己的值，如下所示︰

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

如果需要幾分鐘的時間才能更新您擴展集的容量。 如果您減少擴展集的容量，則會先移除具有最高執行個體識別碼的 VM。


## <a name="stop-and-start-vms-in-a-scale-set"></a>停止和啟動擴展集中的 VM
若要停止擴展集中的一或多個 VM，請使用 [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss)。 `-InstanceId` 參數可讓您指定停止一或多個 VM。 如果您未指定執行個體識別碼，則會停止擴展集中的所有 VM。 若要停止多個 VM，請以逗號分隔每個執行個體識別碼。

下列範例停止 myScaleSet 擴展集和 myResourceGroup 資源群組中的執行個體 *0*。 提供您自己的值，如下所示︰

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

預設會解除配置已停止的 VM，而且不會產生計算費用。 如果您想要在停止時保留處於佈建狀態的 VM，請將 `-StayProvisioned` 參數新增至先前的命令。 保持佈建的已停止 VM 會產生一般計算費用。


### <a name="start-vms-in-a-scale-set"></a>啟動擴展集中的 VM
若要啟動擴展集中的一或多個 VM，請使用 [Start-AzVmss](/powershell/module/az.compute/start-azvmss)。 `-InstanceId` 參數可讓您指定啟動一或多個 VM。 如果您未指定執行個體識別碼，則會啟動擴展集中的所有 VM。 若要啟動多個 VM，請以逗號分隔每個執行個體識別碼。

下列範例啟動 myScaleSet 擴展集和 myResourceGroup 資源群組中的執行個體 *0*。 提供您自己的值，如下所示︰

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>重新啟動擴展集中的 VM
若要重新啟動擴展集中的一或多部 VM，請使用 [Restart-AzVmss](/powershell/module/az.compute/restart-azvmss)。 `-InstanceId` 參數可讓您指定重新啟動一或多個 VM。 如果您未指定執行個體識別碼，則會重新啟動擴展集中的所有 VM。 若要重新啟動多個 VM，請以逗號分隔每個執行個體識別碼。

下列範例重新啟動 myScaleSet 擴展集和 myResourceGroup 資源群組中的執行個體 *0*。 提供您自己的值，如下所示︰

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>移除擴展集中的 VM
若要移除擴展集中的一或多個 VM，請使用 [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss)。 `-InstanceId` 參數可讓您指定移除一或多個 VM。 如果您未指定執行個體識別碼，則會移除擴展集中的所有 VM。 若要移除多個 VM，請以逗號分隔每個執行個體識別碼。

下列範例移除 myScaleSet 擴展集和 myResourceGroup 資源群組中的執行個體 *0*。 提供您自己的值，如下所示︰

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>後續步驟
擴展集的其他一般工作包含如何[部署應用程式](virtual-machine-scale-sets-deploy-app.md)以及[升級 VM 執行個體](virtual-machine-scale-sets-upgrade-scale-set.md)。 您也可以使用 Azure PowerShell [設定自動調整規則](virtual-machine-scale-sets-autoscale-overview.md)。
