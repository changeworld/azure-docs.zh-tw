---
title: 使用 PowerShell 的 Azure 虛擬機器的維護控制
description: 瞭解如何使用維護控制和 PowerShell 控制何時將維護應用於 Azure VM。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060126"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>預覽：使用維護控制和 Azure 電源外殼控制更新

使用維護控制管理不需要重新開機的平臺更新。 Azure 經常更新其基礎結構以提高可靠性、性能、安全性或啟動新功能。 大多數更新對使用者都是透明的。 某些敏感工作負載（如遊戲、媒體流和金融交易）甚至無法容忍幾秒鐘的 VM 凍結或斷開連接以進行維護。 維護控制允許您選擇等待平臺更新並在 35 天滾動視窗中應用它們。 

通過維護控制，您可以決定何時將更新應用於隔離的 VM。

通過維護控制，您可以：
- 批量更新到一個更新包中。
- 最多等待 35 天才能應用更新。 
- 使用 Azure 函數自動為維護視窗執行平臺更新。
- 維護配置跨訂閱和資源組工作。 

> [!IMPORTANT]
> 維護控制當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 

## <a name="limitations"></a>限制

- VM 必須位於[專用主機](./linux/dedicated-hosts.md)上，或者使用[隔離的 VM 大小](./linux/isolation.md)創建。
- 35 天后，將自動應用更新。
- 使用者必須具有**資源參與者**存取權限。


## <a name="enable-the-powershell-module"></a>啟用 PowerShell 模組

確保`PowerShellGet`是最新的。

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az.維護 PowerShell Cmdlet 處於預覽版中，因此您需要在雲殼或`AllowPrerelease`本地 PowerShell 安裝中安裝具有參數的模組。   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

如果要在本地安裝，請確保以管理員身份打開 PowerShell 提示。

可能還會要求您確認要從*不受信任的存儲庫*進行安裝。 鍵入`Y`或選擇 **"是全部"** 以安裝模組。



## <a name="create-a-maintenance-configuration"></a>創建維護配置

將資源組創建為配置的容器。 在此示例中，在*Eastus*中創建了名為*myMaintenanceRG*的資源組。 如果已有要使用的資源組，則可以跳過此部分，並在其餘示例中將資源組名稱替換為您自己的資源組名稱。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

使用[新-Az 維護配置](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration)創建維護配置。 本示例創建名為*myConfig*的維護配置，該配置範圍為主機。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

使用`-MaintenanceScope host`可確保維護配置用於控制對主機的更新。

如果嘗試創建具有相同名稱的配置，但在不同的位置，將出現錯誤。 配置名稱必須對訂閱是唯一的。

您可以使用[Get-Az 維護配置](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)查詢可用的維護配置。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>分配配置

使用["新配置分配"](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment)將配置分配給隔離的 VM 或 Azure 專用主機。

### <a name="isolated-vm"></a>隔離的 VM

使用配置的 ID 將配置應用於 VM。 指定`-ResourceType VirtualMachines`和提供 的`-ResourceName`VM 的名稱 以及 的`-ResourceGroupName`VM 的資源組。 

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

要將配置應用於專用主機，還需要包含`-ResourceType hosts``-ResourceParentName`具有主機組名稱 和`-ResourceParentType hostGroups`。 


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

## <a name="check-for-pending-updates"></a>檢查掛起的更新

使用[獲取-Az 維護更新](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate)以查看是否有掛起的更新。 如果`-subscription`VM 與登錄的訂閱不同，則用於指定 VM 的 Azure 訂閱。

如果沒有要顯示的更新，則此命令將不返回任何內容。 否則，它將返回 PSApplyUpdate 物件：

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

檢查隔離 VM 的掛起更新。 在此示例中，輸出被格式化為可讀性表。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>專用主機

檢查專用主機的掛起更新。 在此示例中，輸出被格式化為可讀性表。 將資源的值替換為您自己的值。

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

使用[New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)應用掛起的更新。

### <a name="isolated-vm"></a>隔離的 VM

創建將更新應用於隔離 VM 的請求。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功後，此命令將返回物件`PSApplyUpdate`。 可以使用`Get-AzApplyUpdate`命令中的 Name 屬性檢查更新狀態。 請參閱[檢查更新狀態](#check-update-status)。

### <a name="dedicated-host"></a>專用主機

將更新應用於專用主機。

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
使用[獲取-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate)檢查更新的狀態。 下面顯示的命令顯示了使用`default``-ApplyUpdateName`參數的最新更新的狀態。 您可以替換更新的名稱（由[New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)命令返回）來獲取特定更新的狀態。

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
上次更新時間將是更新完成的時間，由您啟動，或者由平臺啟動，以防未使用自我維護視窗。 如果從未通過維護控制應用更新，則將顯示預設值。

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

檢查對專用主機的更新。

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

## <a name="remove-a-maintenance-configuration"></a>刪除維護配置

使用["刪除-Az 維護配置"](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration)刪除維護配置。

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>後續步驟
要瞭解更多資訊，請參閱[維護和更新](maintenance-and-updates.md)。
