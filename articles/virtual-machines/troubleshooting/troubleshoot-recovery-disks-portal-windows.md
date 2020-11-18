---
title: 針對 Azure 入口網站中的 Windows VM 進行疑難排解 |Microsoft Docs
description: 瞭解如何透過 Azure 入口網站將 OS 磁片連接至復原 VM，以針對 Azure 中的 Windows 虛擬機器問題進行疑難排解。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735223"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>透過 Azure 入口網站將 OS 磁片連接至復原 VM，以針對 Windows VM 進行疑難排解
如果您在 Azure 中的 Windows 虛擬機器 (VM) 遇到啟動或磁片錯誤，您可能需要在虛擬硬碟 (VHD) 上執行疑難排解步驟。 常見的範例是應用程式更新失敗，導致 VM 無法成功啟動。 本文將詳細說明如何使用 Azure 入口網站將您的虛擬硬碟連接至另一個 Windows VM 以修正任何錯誤，然後重新建立原始 VM。 

## <a name="recovery-process-overview"></a>復原程序概觀
疑難排解程序如下所示︰

1. 停止受影響的 VM。
1. 建立 VM 的 OS 磁碟快照集。
1. 從快照集建立虛擬硬碟。
1. 將虛擬硬碟連結和掛接至另一個 Windows VM，以進行疑難排解。
1. 連接至疑難排解 VM。 編輯檔案或執行任何工具來修正原始虛擬硬碟上的問題。
1. 從疑難排解 VM 卸載並中斷連結虛擬硬碟。
1. 交換 VM 的 OS 磁片。

> [!NOTE]
> 本文並不適用于具有非受控磁片的 Vm。

## <a name="take-a-snapshot-of-the-os-disk"></a>製作 OS 磁碟的快照集
快照集是虛擬硬碟的完整唯讀複本。 建議您在建立快照集之前，先將 VM 完全關閉，以清除進行中的任何處理程式。 若要取得 OS 磁片的快照集，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。 從側邊欄選取 [ **虛擬機器** ]，然後選取有問題的 VM。
1. 在左窗格中，選取 [ **磁片**]，然後選取 OS 磁片的名稱。
    ![顯示磁片設定中 OS 磁片名稱的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. 在 OS 磁片的 [ **總覽** ] 頁面上，選取 [ **建立快照** 集]。
1. 在與 OS 磁片相同的位置中建立快照集。

## <a name="create-a-disk-from-the-snapshot"></a>從快照集建立磁碟
若要從快照集建立磁片，請遵循下列步驟：

1. 從 Azure 入口網站選取 [ **Cloud Shell** ]。

    ![顯示開啟 Azure Cloud Shell 之按鈕的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 執行下列 PowerShell 命令，以從快照集建立受控磁片。 以適當的名稱取代範例名稱。

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. 如果命令成功執行，您將會在所提供的資源群組中看到新的磁片。

## <a name="attach-the-disk-to-another-vm"></a>將磁片連結至另一個 VM
在接下來幾個步驟中，您將使用另一個 VM 進行疑難排解。 將磁片連結至疑難排解 VM 之後，您可以流覽並編輯磁片的內容。 此程式可讓您更正任何設定錯誤，或查看其他應用程式或系統記錄檔。 若要將磁片連結至另一個 VM，請遵循下列步驟：

1. 從入口網站中選取您的資源群組，然後選取您的疑難排解 VM。 選取 [**磁片**]  >  **編輯**[  >  **新增資料磁片**]。

    ![顯示在入口網站中連接現有磁片之選項的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 在 [ **資料磁片** ] 清單中，選取您所識別 VM 的 OS 磁片。 如果您沒有看到 OS 磁片，請確定疑難排解 VM 和 OS 磁片位於相同區域 (位置) 。 
3. 選取 [ **儲存** ] 以套用變更。

## <a name="mount-the-attached-data-disk-to-the-vm"></a>將連接的資料磁片掛接至 VM

1. 開啟疑難排解 VM 的遠端桌面連線。 
2. 在疑難排解 VM 中，開啟 **伺服器管理員**，然後選取 [檔案 **和存放服務**]。 

    ![顯示在伺服器管理員內選取檔案和存放服務的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. 會自動偵測並連接資料磁碟。 若要查看已連接磁碟的清單，請選取 [磁碟]。 您可以選取您的資料磁碟以檢視磁碟區資訊，包括磁碟機代號。 下列範例顯示連接的資料磁片，並使用磁片磁碟機 **F**。

    ![顯示伺服器管理員中連接的磁片和磁片區資訊的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>修正原始虛擬硬碟的問題
已掛接現有的虛擬硬碟掛，您現在可以視需要執行任何維護和疑難排解步驟。 解決所有錯誤之後，請繼續進行下列步驟。

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>卸載並中斷連結原始虛擬硬碟
從疑難排解 VM 卸離現有的虛擬硬碟。 在將虛擬硬碟連結至疑難排解 VM 的租用發行之前，您無法將虛擬硬碟與任何其他 VM 搭配使用。

1. 從遠端桌面會話到您的 VM，開啟 **伺服器管理員**，然後選取 [檔案 **和存放服務**]。

    ![顯示在伺服器管理員中選取 [檔案和存放服務] 的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. 選取 [ **磁片**]，在資料磁片上按一下滑鼠右鍵，然後選取 [ **離線**]。

    ![顯示伺服器管理員中的離線設定資料磁片的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 將虛擬硬碟從 VM 卸離。 在 Azure 入口網站中選取您的 VM，然後選取 [ **磁片**]。 
4. 選取 [ **編輯**]，選取您連接的 OS 磁片，然後選取 [ **刪除**]。

    ![顯示用於卸離現有虛擬硬碟之選項的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    繼續之前，請先等候 VM 中的資料磁片成功刪除。

## <a name="swap-the-os-disk-for-the-vm"></a>交換 VM 的 OS 磁片

Azure 入口網站現在支援變更 VM 的 OS 磁片。 若要執行此動作，請依照下列步驟執行：

1. 移至 [Azure 入口網站](https://portal.azure.com)。 從側邊欄選取 [ **虛擬機器** ]，然後選取有問題的 VM。
1. 在左窗格中，選取 [ **磁片**]，然後選取 [ **交換 OS 磁片**]。
   
    ![顯示 Azure 入口網站中的 OS 磁片交換按鈕的螢幕擷取畫面。](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 選擇您修復的新磁片，然後輸入 VM 的名稱以確認變更。 如果您在清單中看不到磁片，請在從疑難排解 VM 卸離磁片之後等候10到15分鐘。 也請確定磁片與 VM 位於相同的位置。
1. 選取 [確定]。

## <a name="next-steps"></a>後續步驟
如果您在連線至 VM 時遇到問題，請參閱 [疑難排解 AZURE vm 的遠端桌面](troubleshoot-rdp-connection.md)連線。 對於存取在 VM 上執行之應用程式的問題，請參閱針對 [WINDOWS VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md)。

如需使用 Azure Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 總覽](../../azure-resource-manager/management/overview.md)。


