---
title: 使用 PowerShell 進行 Azure 虛擬機器的維護控制
description: 瞭解如何使用維護控制和 PowerShell 來控制將維護套用至 Azure Vm 的時機。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: efd35cfe2660f4597ec0c95dc29bcb4b839da680
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306934"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>使用維護控制和 Azure PowerShell 來控制更新

維護控制可讓您決定何時要將更新套用至隔離的 Vm 和 Azure 專用主機。 本主題涵蓋維護控制的 Azure PowerShell 選項。 如需使用維護控制、其限制和其他管理選項之優點的詳細資訊，請參閱 [使用維護控制管理平臺更新](maintenance-control.md)。
 
## <a name="enable-the-powershell-module"></a>啟用 PowerShell 模組

請確定 `PowerShellGet` 是最新的。    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

安裝 `Az.Maintenance` PowerShell 模組。     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

如果您是在本機進行安裝，請務必以系統管理員身分開啟 PowerShell 提示字元。

系統可能也會要求您確認是否要從未 *受信任*的存放庫進行安裝。 輸入 `Y` 或選取 **[是]，以** 安裝模組。


## <a name="create-a-maintenance-configuration"></a>建立維護設定

建立資源群組作為您設定的容器。 在此範例中，會在*eastus*中建立名為*myMaintenanceRG*的資源群組。 如果您已經有想要使用的資源群組，您可以略過此部分，並將資源組名取代為其餘範例中所擁有的資源組名。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

使用 [AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 建立維護設定。 這個範例會建立一個名為 *myconfig.xml* 的維護設定，範圍設定為主機。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

使用 `-MaintenanceScope host` 可確保使用維護設定來控制主機的更新。

如果您嘗試使用相同的名稱建立設定，但在不同的位置，您將會收到錯誤。 設定名稱在您的資源群組中必須是唯一的。

您可以使用 [AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)來查詢可用的維護設定。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window-in-preview"></a>在預覽中使用排程的時間範圍 (建立維護設定) 


> [!IMPORTANT]
> 排程的視窗功能目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

當 Azure 會在您的資源上套用更新時，請使用 AzMaintenanceConfiguration 來建立具有已排程時段的維護設定。 此範例會建立一個名為 Myconfig.xml 的維護設定，並在每個月的第四個星期一排程時段為5小時。 一旦建立排程的視窗，您就不再需要手動套用更新。

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> 維護 **期間** 必須是 *2 小時* 或更長的時間。 在35天內，維護 **週期** 至少必須設定為一次。

維護 **週期** 可以表示為每日、每週或每月排程。 每日排程範例為 recurEvery： Day、recurEvery：3Days。 每週排程範例為 recurEvery：3Weeks、recurEvery： Week 星期六、星期日。 每月排程範例如下 recurEvery： Month day23、day24、recurEvery： Month Last 星期日、recurEvery： Month 第四個月。


## <a name="assign-the-configuration"></a>指派設定

使用 [AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) 將設定指派給隔離的 VM 或 Azure 專用主機。

### <a name="isolated-vm"></a>隔離的 VM

使用設定的識別碼將設定套用至 VM。 指定 `-ResourceType VirtualMachines` 和提供 vm 的名稱 `-ResourceName` ，以及 vm 的資源群組 `-ResourceGroupName` 。 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>專用主機

若要將設定套用至專用主機，您也必須包含 `-ResourceType hosts` `-ResourceParentName` 主機群組的名稱和 `-ResourceParentType hostGroups` 。 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>檢查是否有擱置中的更新

使用 [AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) 查看是否有擱置中的更新。 如果 VM 與您所登入的訂用帳戶不同，請使用 `-subscription` 來指定該 VM 的 Azure 訂用帳戶。

如果沒有任何要顯示的更新，此命令將不會傳回任何內容。 否則，它會傳回 PSApplyUpdate 物件：

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>隔離的 VM

檢查隔離 VM 的擱置更新。 在此範例中，會將輸出格式化為表格，以方便閱讀。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>專用主機

檢查專用主機的擱置更新。 在此範例中，會將輸出格式化為表格，以方便閱讀。 將資源的值取代為您自己的值。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>套用更新

使用 [AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) 來套用暫止的更新。

### <a name="isolated-vm"></a>隔離的 VM

建立將更新套用至隔離 VM 的要求。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功時，此命令會傳回 `PSApplyUpdate` 物件。 您可以在命令中使用 Name 屬性 `Get-AzApplyUpdate` 來檢查更新狀態。 請參閱 [檢查更新狀態](#check-update-status)。

### <a name="dedicated-host"></a>專用主機

將更新套用至專用主機。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>檢查更新狀態
使用 [AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) 來檢查更新的狀態。 下方顯示的命令會藉由使用參數來顯示最新的更新狀態 `default` `-ApplyUpdateName` 。 您可以用 [AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) 命令所傳回的更新 (名稱取代) 來取得特定更新的狀態。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime 將是更新完成的時間（無論是由您或您的平臺所起始），以防未使用自我維護視窗。 如果從未有透過維護控制套用的更新，則會顯示預設值。

### <a name="isolated-vm"></a>隔離的 VM

檢查特定虛擬機器的更新。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>專用主機

檢查專用主機的更新。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>移除維護設定

使用 [AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) 來刪除維護設定。

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>後續步驟
若要深入瞭解，請參閱 [維護和更新](maintenance-and-updates.md)。
