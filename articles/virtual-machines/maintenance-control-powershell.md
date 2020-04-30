---
title: 使用 PowerShell 進行 Azure 虛擬機器的維護控制
description: 瞭解如何使用維護控制和 PowerShell，控制何時將維護套用至您的 Azure Vm。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: b1c72c2f606ab653d7e3f1d81f7278571e8e4978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136527"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>控制維護控制和 Azure PowerShell 的更新

維護控制可讓您決定何時要將更新套用到隔離的 Vm 和 Azure 專用主機。 本主題涵蓋維護控制的 Azure PowerShell 選項。 如需使用維護控制、其限制和其他管理選項之優點的詳細資訊，請參閱[管理具有維護控制的平臺更新](maintenance-control.md)。
 
## <a name="enable-the-powershell-module"></a>啟用 PowerShell 模組

請確定`PowerShellGet`是最新的。

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

如果您要在本機安裝，請務必以系統管理員身分開啟 PowerShell 提示字元。

系統可能也會要求您確認是否要從*不受信任*的存放庫進行安裝。 輸入`Y`或選取 **[全部皆是]** 以安裝模組。


## <a name="create-a-maintenance-configuration"></a>建立維護設定

建立資源群組作為您設定的容器。 在此範例中，會在*eastus*中建立名為*myMaintenanceRG*的資源群組。 如果您已經有想要使用的資源群組，可以略過此部分，並在其餘範例中以您自己的名稱取代資源組名。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

使用[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration)來建立維護設定。 這個範例會建立名為*myconfig.xml*的維護設定，範圍限定于主機。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

使用`-MaintenanceScope host`可確保維護設定用於控制主機的更新。

如果您嘗試使用相同的名稱建立設定，但在不同的位置，您會收到錯誤。 設定名稱在您的訂用帳戶中必須是唯一的。

您可以使用[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)來查詢可用的維護設定。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>指派設定

使用[AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment)將設定指派給隔離的 VM 或 Azure 專用主機。

### <a name="isolated-vm"></a>隔離的 VM

使用設定的識別碼，將設定套用至 VM。 指定`-ResourceType VirtualMachines`並提供 vm 的名稱`-ResourceName`，以及 vm 的資源群組。 `-ResourceGroupName` 

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

若要將設定套用至專用主機，您也必須包含`-ResourceType hosts`、 `-ResourceParentName`和主機群組的名稱，以及。 `-ResourceParentType hostGroups` 


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

## <a name="check-for-pending-updates"></a>檢查暫止的更新

使用[AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate)查看是否有擱置中的更新。 如果`-subscription` VM 的 Azure 訂用帳戶與您登入的帳戶不同，請使用來指定它。

如果沒有要顯示的更新，此命令將不會傳回任何內容。 否則，它會傳回 PSApplyUpdate 物件：

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

檢查隔離 VM 的暫止更新。 在此範例中，會將輸出格式化為資料表以方便閱讀。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>專用主機

以檢查專用主機的暫止更新。 在此範例中，會將輸出格式化為資料表以方便閱讀。 將資源的值取代為您自己的值。

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

使用[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)來套用擱置中的更新。

### <a name="isolated-vm"></a>隔離的 VM

建立將更新套用至隔離 VM 的要求。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功時，此命令會傳回`PSApplyUpdate`物件。 您可以在`Get-AzApplyUpdate`命令中使用 Name 屬性來檢查更新狀態。 請參閱[檢查更新狀態](#check-update-status)。

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
使用[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate)來檢查更新的狀態。 下面顯示的命令會使用`default`的`-ApplyUpdateName`參數來顯示最新更新的狀態。 您可以替代更新的名稱（由[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)命令傳回）來取得特定更新的狀態。

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
LastUpdateTime 會是更新完成的時間，可能是由您或平臺在未使用自我維護視窗時所起始。 如果從未透過維護控制套用更新，它將會顯示預設值。

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

檢查是否有專用主機的更新。

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

使用[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration)刪除維護設定。

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>後續步驟
若要深入瞭解，請參閱[維護和更新](maintenance-and-updates.md)。
