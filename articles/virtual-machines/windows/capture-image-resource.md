---
title: 在 Azure 中建立受控映像
description: 在 Azure 中建立一般化 VM 或 VHD 的受控映像。 映像可用來建立多部使用受控磁碟的 VM。
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 751fa9f9fe2ba17a982b71a6332be302804f0dcc
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047287"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>在 Azure 中建立一般化 VM 的受控映像

您可以從在儲存體帳戶中儲存為受控磁碟或非受控磁碟的一般化虛擬機器 (VM)，建立受控映像資源。 映像接著便可用來建立多個 VM。 如需受控映像計費方式的相關資訊，請參閱[受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。 

一個受控映像最多可支援 20 個同時部署。 嘗試從相同的受控映像同時建立 20 個以上的 VM 時，因為單一 VHD 的儲存體效能限制之故，可能會導致佈建逾時。 若要同時建立 20 個以上的 VM，請使用[共用映像資源庫](shared-image-galleries.md)中設定為每 20 個並行虛擬機器部署 1 個複本的映像。

## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 將 Windows VM 一般化

Sysprep 會移除您的所有個人帳戶與安全性資訊，然後準備使用機器做為映像。 如需 Sysprep 的詳細資訊，請參閱 [Sysprep 概觀](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

請確定 Sysprep 支援電腦上執行的伺服器角色。 如需詳細資訊，請參閱[伺服器角色的 Sysprep 支援](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)和[不支援的案例](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)。 

> [!IMPORTANT]
> 在 VM 中執行 Sysprep 之後，該 VM 便會被視為「已一般化」，而且無法重新啟動。 將 VM 一般化的程序是無法復原的。 如果您需要讓原始 VM 保持運作，就應該建立 [VM 的複本](create-vm-specialized.md#option-3-copy-an-existing-azure-vm)，然後將複本一般化。 
>
>Sysprep 要求磁片磁碟機必須完整解密。 如果您已在 VM 上啟用加密，請在執行 Sysprep 之前先停用加密。
>
> 如果您打算在第一次將虛擬硬碟 (VHD) 上傳到 Azure 之前，先執行 Sysprep，請確定您已[準備好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  
> 
> 

若要將您的 Windows VM 一般化，請依照下列步驟執行：

1. 登入您的 Windows VM。
   
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 

3.  (C:\Windows\Panther) 刪除 >\panther\setupact.log 目錄。 然後將目錄變更為%windir%\system32\sysprep，然後執行 `sysprep.exe` 。
   
4. 在 [系統準備工具] 對話方塊中，選取 [進入系統全新體驗 (OOBE)]，然後選取 [一般化] 核取方塊。
   
5. 針對 [關機選項]，選取 [關機]。
   
6. 選取 [確定]。
   
    ![啟動 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Sysprep 完成時，它會將 VM 關機。 不要重新啟動 VM。

> [!TIP]
> **選擇性** 請使用 [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) 來最佳化您的映像，縮短虛擬機器的第一次開機時間。
>
> 若要最佳化您的映像，請在 Windows 檔案總管中按兩下 VHD 來掛接，然後使用 `/optimize-image` 參數執行 DISM。
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> 其中 D: 是掛接 VHD 的路徑。
>
> 執行 `DISM /optimize-image` 應該是您對 VHD 進行的最後修改。 如果在部署之前對 VHD 進行了任何變更，則需再次執行 `DISM /optimize-image`。

## <a name="create-a-managed-image-in-the-portal"></a>在入口網站中建立受控映像 

1. 請前往 [Azure 入口網站](https://portal.azure.com)管理 VM 映像。 搜尋並選取 [虛擬機器]。

2. 從清單中選取您的虛擬機器。

3. 在 VM 的 [虛擬機器] 頁面上的上方功能表中選取 [擷取]。

   [建立映像] 頁面隨即出現。

4. 針對 [名稱]，請接受預先填入的名稱或輸入您要為映像使用的名稱。

5. 針對 [資源群組]，請選取 [新建] 並輸入名稱，或從下拉式清單中選取要使用的資源群組。

6. 如果您想要在建立映像之後刪除來源 VM，請選取 [自動在建立映像後刪除此虛擬機器]。

7. 若要讓該映像可在任何[可用性區域](../../availability-zones/az-overview.md) 中使用，請為 [區域復原] 選取 [開啟]。

8. 選取 [建立] 以建立映像。

建立映像之後，您可以在資源群組的資源清單中看見它顯示為 [映像] 資源。



## <a name="create-an-image-of-a-vm-using-powershell"></a>使用 PowerShell 建立 VM 的映射

 

直接從 VM 建立映像，可確保映像包含 VM 的所有相關磁碟，包括 OS 磁碟與任何資料磁碟。 此範例示範如何從使用受控磁碟的 VM 建立受控映像。

在您開始之前，請確定您擁有最新版本的 Azure PowerShell 模組。 若要尋找版本，請在 PowerShell 中執行 `Get-Module -ListAvailable Az`。 若要升級，請參閱[使用 PowerShellGet 在 Windows 上 安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，請執行 `Connect-AzAccount` 以建立與 Azure 的連線。


> [!NOTE]
> 如果您想要將映像儲存於區域備援的儲存體中，則需要在支援[可用性區域](../../availability-zones/az-overview.md)且在映像設定 (`New-AzImageConfig` 命令) 中包含 `-ZoneResilient` 參數的區域中建立它。

若要建立 VM 映像，請依照下列步驟執行：

1. 建立一些變數。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. 確定已解除配置 VM。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 將虛擬機器的狀態設定為 [一般化] 。 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 取得虛擬機器。 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 建立映像組態。

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. 建立映像。

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>使用 PowerShell 從受控磁碟建立受控映像

若只要為 OS 磁碟建立映像，請將受控磁碟識別碼指定為 OS 磁碟：

    
1. 建立一些變數。 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. 取得 VM。

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 取得受控磁碟的識別碼。

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 建立映像組態。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 建立映像。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>使用 PowerShell 從快照集建立映射

您可以依照下列步驟，從一般化 VM 的快照集建立受控映像：

    
1. 建立一些變數。 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 取得快照集。

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 建立映像組態。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 建立映像。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>從使用儲存體帳戶的 VM 建立映像

若要從並未使用受控磁碟的 VM 建立受控映像，需要使用儲存體帳戶中 OS VHD 的 URI，其格式為： https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*。 在此範例中，VHD 位於名為 *vhdcontainer* 之容器的 *mystorageaccount* 中，且 VHD 檔案名稱為 *vhdfilename.vhd*。


1.  建立一些變數。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. 停止/解除配置 VM。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 將 VM 標示為一般化。

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  使用一般化 OS VHD 建立映像。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>後續步驟
- [從受控映像建立 VM](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。    
