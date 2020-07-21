---
title: 適用于 Azure 虛擬機器的常見 PowerShell 命令
description: 用來協助您開始在 Azure 中建立及管理 Vm 的一般 PowerShell 命令。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: c74713ac6ca2b90b20b4e3b2409e3a9761c9c3f2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508435"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>用於建立及管理 Azure 虛擬機器的常用 PowerShell 命令

本文涵蓋一些 Azure PowerShell 命令，您可用來建立及管理您的 Azure 訂用帳戶中的虛擬機器。  如需特定命令列參數和選項的詳細說明，您可以使用 **Get-Help** *命令*。

 

如果您要執行本文中的多個命令，下列變數可能會相當實用：

- $location - 虛擬機器的位置。 您可以使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 來尋找適合您的[地理區域](https://azure.microsoft.com/regions/)。
- $myResourceGroup - 包含虛擬機器之資源群組的名稱。
- $myVM - 虛擬機器的名稱。

## <a name="create-a-vm---simplified"></a>建立 VM (簡化)

| Task | Command |
| ---- | ------- |
| 建立簡易 VM | [New-AzVM](/powershell/module/az.compute/new-azvm) -命名 $myVM <BR></BR><BR></BR> New-AzVM 有一組「已簡化」** 的參數，其只需要單一名稱。 -Name 的值會用來作為所有建立新 VM 所需資源的名稱。 您可以指定多個，但這是唯一必要的值。|
| 從自訂映像建立 VM | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>您必須已建立自己的[受控映像](capture-image-resource.md)。 您可以使用映像來建立多個相同的 VM。 |



## <a name="create-a-vm-configuration"></a>建立 VM 組態

| Task | Command |
| ---- | ------- |
| 建立 VM 組態 |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 組態用來定義或更新 VM 的設定。 系統會使用 VM 的名稱及其 [大小](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)初始化組態。 |
| 新增組態設定 |$vm = [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>包含[認證](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1)的作業系統設定會新增至您先前使用 New-AzVMConfig 建立的組態物件。 |
| 新增網路介面 |$vm = [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM 必須有[網路介面](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json)才可在虛擬網路中進行通訊。 您也可以使用 [Get-AzNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) 擷取現有的網路介面物件。 |
| 指定平台映像 |$vm = [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[映像資訊](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 會新增至您先前使用 New-AzVMConfig 建立的組態物件。 只有在您設定作業系統磁碟使用平台映像時，才會使用此命令傳回的物件。 |
| 建立 VM |[New-AzVM](/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>所有資源都會在[資源群組](../../azure-resource-manager/management/manage-resource-groups-powershell.md)中建立。 執行此命令之前，請執行 New-AzVMConfig、Set-AzVMOperatingSystem、Set-AzVMSourceImage、Add-AzVMNetworkInterface 和 Set-AzVMOSDisk。 |
| 更新 VM |[Update-AzVM](/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>使用 Get-AzVM 取得目前的 VM 組態，變更 VM 物件上的組態設定，然後執行此命令。 |

## <a name="get-information-about-vms"></a>取得 VM 的相關資訊

| Task | Command |
| ---- | ------- |
| 列出訂用帳戶中的 VM |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| 列出資源群組中的 VM |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>若要取得您的訂用帳戶中的資源群組清單，請使用 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)初始化組態。 |
| 取得 VM 的相關資訊 |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>管理 VM
| Task | Command |
| --- | --- |
| 啟動 VM |[Start-AzVM](/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 停止 VM |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 重新啟動執行中的 VM |[Restart-AzVM](/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 刪除 VM |[Remove-AzVM](/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>後續步驟
* 請參閱 [使用 Resource Manager 和 PowerShell 建立 Windows VM](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json)中建立虛擬機器的基本步驟。
