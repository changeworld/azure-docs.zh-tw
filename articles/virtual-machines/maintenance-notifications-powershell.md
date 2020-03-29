---
title: 使用 PowerShell 獲取 Azure VM 的維護通知
description: 查看在 Azure 中運行的虛擬機器的維護通知，並使用 PowerShell 啟動自助服務維護。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916077"
---
# <a name="handling-planned-maintenance-using-powershell"></a>使用 PowerShell 處理計畫維護

**本文適用于同時運行 Linux 和 Windows 的虛擬機器。**

您可以使用 Azure PowerShell 查看 VM 何時計畫[進行維護](maintenance-notifications.md)。 使用 `-status` 參數時，可從 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) Cmdlet 取得預定進行的維修作業的相關資訊。
  
只在有預定進行的維修作業時，才會傳回維修資訊。 如果沒有排定會影響 VM 的維護，則 Cmdlet 不會傳回任何維護資訊。 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

下列是 MaintenanceRedeployStatus 下傳回的內容： 

| 值 | 描述   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指出您目前是否可以在 VM 上開始維修 |
| PreMaintenanceWindowStartTime         | 維修自助期間的開始，此時您可以在 VM 上起始維修 |
| PreMaintenanceWindowEndTime           | 維修自助期間的結束，此時您可以在 VM 上起始維修 |
| MaintenanceWindowStartTime            | 排定維護開始，此時 Azure 會在您的虛擬機器上起始維護 |
| MaintenanceWindowEndTime              | 排定維護期間結束，此時 Azure 會在您的虛擬機器上停止維護 |
| LastOperationResultCode               | 前次嘗試在 VM 上起始維修的結果 |



您也可以使用 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) 而不指定 VM 來取得資源群組中所有 VM 的維修狀態。
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

下面的 PowerShell 示例採用訂閱 ID 並返回計畫維護的 VM 清單。

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>使用 PowerShell 在 VM 上開始維修

使用來自上一節函式的資訊，如果 **IsCustomerInitiatedMaintenanceAllowed** 設為 true，以下命令會在 VM 上開始維修。

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>傳統部署

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

如果您仍有使用傳統部署模型部署的舊版 VM，可以使用 PowerShell 查詢 VM 並起始維護。

若要取得 VM 的維護狀態，請鍵入：

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

若要在傳統 VM 上啟動維護，請鍵入：

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>後續步驟

您還可以使用[Azure CLI](maintenance-notifications-cli.md)或[門戶](maintenance-notifications-portal.md)處理計畫維護。
