---
title: 教學課程 - 使用 Azure PowerShell 管理 Azure 磁碟
description: 在本教學課程中，您會了解如何使用 Azure PowerShell 來建立及管理虛擬機器的 Azure 磁碟
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e3d81bfaba572361304224932fadb7da5b1cc3f8
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97912882"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>教學課程 - 使用 Azure PowerShell 管理 Azure 磁碟

Azure 虛擬機器使用硬碟來儲存 VM 作業系統、應用程式和資料。 建立 VM 時，請務必選擇預期的工作負載適用的磁碟大小和組態。 本教學課程涵蓋部署和管理 VM 磁碟。 您將了解：

> [!div class="checklist"]
> * OS 磁碟和暫存磁碟
> * 資料磁碟
> * 標準和進階磁碟
> * 磁碟效能
> * 連結及準備資料磁碟

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="default-azure-disks"></a>預設 Azure 磁碟

建立 Azure 虛擬機器後，有兩個磁碟會自動連結到虛擬機器。 

**作業系統磁碟** - 作業系統磁碟可裝載 VM 作業系統，其大小可高達 4 TB。 如果您從 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 映像建立新的虛擬機器 (VM)，則通常是 127 GB (但有些映像的 OS 磁碟大小較小)。 依預設會將磁碟機代號 *C:* 指派給 OS 磁碟。 OS 磁碟的磁碟快取組態已針對 OS 效能進行最佳化。 OS 磁碟 **不得** 裝載應用程式或資料。 請對應用程式和資料使用資料磁碟，本文稍後會詳細說明。

**暫存磁碟** - 暫存磁碟會使用與 VM 位於相同 Azure 主機的固態磁碟機。 暫存磁碟的效能非常好，可用於暫存資料處理等作業。 不過，如果 VM 移至新的主機，則會移除儲存在暫存磁碟上的任何資料。 暫存磁碟的大小取決於 [VM 大小](../sizes.md)。 依預設會將磁碟機代號 *D:* 指派給暫存磁碟。

## <a name="azure-data-disks"></a>Azure 資料磁碟

您可以新增額外資料磁碟，以便安裝應用程式和儲存資料。 只要需要持久且有回應的資料儲存體，即應使用資料磁碟。 虛擬機器的大小會決定可連結到 VM 的資料磁碟數目。

## <a name="vm-disk-types"></a>VM 磁碟類型

Azure 提供兩種類型的磁碟。

**標準磁碟** - 由 HDD 所支援，可提供符合成本效益的儲存體，同時保有效能。 標準磁碟適合用於具成本效益的開發和測試工作負載。

**進階磁碟** - 採用以 SSD 為基礎的高效能、低延遲磁碟。 最適合用於執行生產工作負載的 VM。 在 [大小名稱](../vm-naming-conventions.md)中具有 **S** 的 VM 大小通常會支援進階儲存體。 例如，DS 系列、DSv2 系列、GS 系列和 FS 系列的 VM 便支援進階儲存體。 當您選取磁碟大小時，其值會上調為下一個類型。 例如，如果磁碟大小超過 64 GB，但少於 128 GB，則磁碟類型為 P10。 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

當您佈建進階儲存體磁碟時，不同於標準儲存體的是，您可獲得該磁碟的容量、IOPS 和輸送量保證。 例如，如果您建立 P50 磁碟，Azure 會為該磁碟佈建 4,095 GB 儲存體容量、7,500 IOPS 和 250 MB/秒的輸送量。 您的應用程式可以使用全部或部分的容量和效能。 進階固態硬碟的設計是為了在 99.9% 的時間內，提供低個位數毫秒延遲以及上表所述的目標 IOPS 和輸送量。

雖然上表指出每個磁碟的最大 IOPS，但可藉由分割多個資料磁碟來達到較高等級的效能。 例如，可以將 64 個資料磁碟連結到 Standard_GS5 VM。 如果上述每個磁碟的大小調整為 P30，就可以達到 80,000 IOPS 的最大值。 如需每部 VM 之最大 IOPS 的詳細資訊，請參閱 [VM 類型和大小](../sizes.md)。

## <a name="create-and-attach-disks"></a>建立和連結磁碟

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，請使用下列命令建立虛擬機器。

使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1&preserve-view=true) 設定虛擬機器上系統管理員帳戶所需的使用者名稱和密碼：


使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 來建立虛擬機器。 系統會提示您輸入 VM 系統管理員帳戶的使用者名稱和密碼。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


使用 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) 建立初始組態。 下列範例會設定大小為 128 GB 的磁碟。

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

使用 [New-AzDisk](/powershell/module/az.compute/new-azdisk) 命令來建立資料磁碟。

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) 命令來取得您要在其中新增資料磁碟的虛擬機器。

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

使用 [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) 命令將資料磁碟新增至虛擬機器組態。

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

使用 [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) 命令來更新虛擬機器。

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>準備資料磁碟

將磁碟連結到虛擬機器後，必須將作業系統設定為使用該磁碟。 下列範例示範如何手動設定第一個新增至 VM 的磁碟。 使用[自訂指令碼擴充](./tutorial-automate-vm-deployment.md)也可以自動執行此程序。

### <a name="manual-configuration"></a>手動設定

建立虛擬機器的 RDP 連線。 開啟 PowerShell 並執行這個指令碼。

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>驗證資料磁碟

若要確認資料磁碟已連結，請檢視已連結 `DataDisks` 的 `StorageProfile`。

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

輸出應會如下列範例所示：

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 VM 磁碟的相關主題，像是：

> [!div class="checklist"]
> * OS 磁碟和暫存磁碟
> * 資料磁碟
> * 標準和進階磁碟
> * 磁碟效能
> * 連結及準備資料磁碟

請前進到下一個教學課程，以了解如何自動設定 VM。

> [!div class="nextstepaction"]
> [自動設定 VM](./tutorial-automate-vm-deployment.md)
