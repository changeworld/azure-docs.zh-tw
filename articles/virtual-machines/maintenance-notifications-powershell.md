---
title: 使用 PowerShell 取得 Azure Vm 的維護通知
description: 查看在 Azure 中執行之虛擬機器的維護通知，並使用 PowerShell 啟動自助維護。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916077"
---
# <a name="handling-planned-maintenance-using-powershell"></a>使用 PowerShell 處理預定的維護

**本文適用于同時執行 Linux 和 Windows 的虛擬機器。**

您可以使用 Azure PowerShell 來查看 Vm 何時排程進行[維護](maintenance-notifications.md)。 使用 [ 參數時，可從 ](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)Get-AzVM`-status` Cmdlet 取得預定進行的維修作業的相關資訊。
  
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

下列 PowerShell 範例會採用您的訂用帳戶識別碼，並傳回已排程進行維護的 Vm 清單。

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

您也可以使用[Azure CLI](maintenance-notifications-cli.md)或[入口網站](maintenance-notifications-portal.md)來處理預定的維護。
