---
title: 使用 PowerShell 在 Azure 虛擬機器擴展集上進行 OS 映射升級的維護控制
description: 瞭解如何使用維護控制和 PowerShell 來控制何時將自動 OS 映射升級推出至您的 Azure 虛擬機器擴展集。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 62b2fd0554c218b733501805779745049d3b8c92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532606"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>預覽：使用 PowerShell 在 Azure 虛擬機器擴展集上進行 OS 映射升級的維護控制

維護控制可讓您決定何時要將自動的來賓 OS 映射升級套用至您的虛擬機器擴展集。 本主題涵蓋維護控制的 Azure PowerShell 選項。 如需使用維護控制的詳細資訊，請參閱 [Azure 虛擬機器擴展集的維護控制](virtual-machine-scale-sets-maintenance-control.md)。

> [!IMPORTANT]
> Azure 虛擬機器擴展集上的 OS 映射升級維護控制目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="enable-the-powershell-module"></a>啟用 PowerShell 模組

請確定 `PowerShellGet` 是最新的。    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

安裝 `Az.Maintenance` PowerShell 模組。     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

如果您要在本機進行安裝，請務必以系統管理員身分開啟 PowerShell 提示字元。

系統可能也會要求您確認是否要從未 *受信任*的存放庫進行安裝。 輸入 `Y` 或選取 **[是]，以** 安裝模組。

## <a name="connect-to-an-azure-account"></a>連線到 Azure 帳戶

使用 [disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) 和 [disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext)連接到您想要的 Azure 帳戶。

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>建立維護設定

建立資源群組作為您設定的容器。 在此範例中，會在*eastus2*中建立名為*myMaintenanceRG*的資源群組。 如果您已經有想要使用的資源群組，您可以略過此部分。 請在其餘範例中，以您自己的資源組名取代資源組名。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

使用 [AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 建立維護設定。 這個範例會建立一個名為 *myconfig.xml* 的維護設定，範圍設定為 OS 映射。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> 維護 **期間** 必須是 *5 小時* 或更長的時間。 維護 **週期** 必須設定為 *天*。

使用 `-MaintenanceScope OSImage` 可確保維護設定用來控制對虛擬作業系統的更新。

如果您嘗試使用相同的名稱建立設定，但在不同的位置，您將會收到錯誤。 設定名稱在您的資源群組中必須是唯一的。

您可以使用 [AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)來查詢可用的維護設定。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>將您的虛擬機器擴展集與維護設定建立關聯

無論維護設定的區域和訂用帳戶為何，虛擬機器擴展集都可以與任何維護設定相關聯。 藉由選擇進行維護設定，將會在下一個可用的維護視窗中自動排程擴展集的新 OS 映射更新。

使用 [AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) 將您的虛擬機器擴展集與維護設定產生關聯。

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>啟用自動 OS 升級

您可以針對即將使用維護控制的每個虛擬機器擴展集啟用自動 OS 升級。 請參閱檔 [Azure 虛擬機器擴展集自動 os 映射升級](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) ，以在您的虛擬機器擴展集上啟用自動 os 升級。 


## <a name="next-steps"></a>後續步驟

瞭解在 Azure 中執行之虛擬機器的維護和更新。

> [!div class="nextstepaction"]
> [維護和更新](maintenance-and-updates.md)
