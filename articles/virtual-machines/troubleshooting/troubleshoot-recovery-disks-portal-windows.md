---
title: 在 Azure 入口網站中使用 Windows 疑難排解 VM | Microsoft Docs
description: 了解如何在 Azure 中使用 Azure 入口網站將 OS 磁碟連接至復原 VM，以針對 Windows 虛擬機器問題進行疑難排解
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
ms.openlocfilehash: 9b51205fe67bfe5be46491b0238e987fc14f6737
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074350"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>使用 Azure 入口網站將 OS 磁碟連結至復原 VM，以針對 Windows VM 進行疑難排解
如果 Azure 中的 Windows 虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對虛擬硬碟本身執行疑難排解步驟。 常見的例子是應用程式更新無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure 入口網站將虛擬硬碟連接至另一個 Windows VM，以修正任何錯誤，然後重新建立原始 VM。 

## <a name="recovery-process-overview"></a>復原程序概觀
疑難排解程序如下所示︰

1. 停止受影響的 VM。
1. 建立 VM 的 OS 磁碟快照集。
1. 從快照集建立虛擬硬碟。
1. 將虛擬硬碟連結和掛接至另一個 Windows VM，以進行疑難排解。
1. 連接至疑難排解 VM。 編輯檔案或執行任何工具來修正原始虛擬硬碟的問題。
1. 從疑難排解 VM 卸載並中斷連結虛擬硬碟。
1. 交換 VM 的 OS 磁片。

> [!NOTE]
> 本文不適用於具有非受控磁片的 VM。

## <a name="take-a-snapshot-of-the-os-disk"></a>取得 OS 磁片的快照集
快照集是完整的虛擬硬碟 (VHD) 唯讀複本。 建議您在建立快照集之前，先將 VM 完全關閉，以清除進行中的任何處理程式。 若要取得 OS 磁片的快照集，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。 從側邊欄選取 [ **虛擬機器** ]，然後選取有問題的 VM。
1. 在左窗格中，選取 [ **磁片**]，然後選取 OS 磁片的名稱。
    ![作業系統磁片名稱的相關影像](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. 在 OS 磁片的 [ **總覽** ] 頁面上，選取 [ **建立快照**集]。
1. 在與 OS 磁片相同的位置中建立快照集。

## <a name="create-a-disk-from-the-snapshot"></a>從快照集建立磁碟
若要從快照集建立磁片，請遵循下列步驟：

1. 從 Azure 入口網站選取 [ **Cloud Shell** ]。

    ![開啟 Cloud Shell 的相關影像](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 執行下列 PowerShell 命令，以從快照集建立受控磁片。 您應該以適當的名稱取代這些範例名稱。

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. 如果命令成功執行，您將會在所提供的資源群組中看到新的磁片。

## <a name="attach-the-disk-to-another-vm"></a>將磁片連結至另一個 VM
在接下來幾個步驟中，您將使用另一個 VM 進行疑難排解。 將磁片連結至疑難排解 VM 之後，您可以流覽並編輯磁片的內容。 此程式可讓您更正任何設定錯誤，或查看其他應用程式或系統記錄檔。 若要將磁片連結至另一個 VM，請遵循下列步驟：

1. 從入口網站選取資源群組，然後選取疑難排解 VM。 選取 [ **磁片**]，選取 [ **編輯**]，然後按一下 [ **新增資料磁片**：

    ![在入口網站中連結現有磁碟](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 在 [ **資料磁片** ] 清單中，選取您所識別 VM 的 OS 磁片。 如果您沒有看到 OS 磁片，請確定疑難排解 VM 和 OS 磁片位於相同區域 (位置) 。 
3. 選取 [ **儲存** ] 以套用變更。

## <a name="mount-the-attached-data-disk-to-the-vm"></a>將連接的資料磁片掛接至 VM

1. 開啟疑難排解 VM 的遠端桌面連線。 
2. 在疑難排解 VM 中，開啟 **伺服器管理員**，然後選取 [檔案 **和存放服務**]。 

    ![選取伺服器管理員內的檔案和存放服務](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. 會自動偵測並連接資料磁碟。 若要查看已連接磁碟的清單，請選取 [磁碟]****。 您可以選取您的資料磁碟以檢視磁碟區資訊，包括磁碟機代號。 下列範例會顯示已連接的資料磁碟並使用 **F:**：

    ![伺服器管理員中的已連接磁碟和磁碟區資訊](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>修正原始虛擬硬碟的問題
已掛接現有的虛擬硬碟掛，您現在可以視需要執行任何維護和疑難排解步驟。 解決問題之後，請繼續進行下列步驟。

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸載並中斷連結原始虛擬硬碟
錯誤解決之後，請將現有虛擬硬碟與疑難排解 VM 的連結中斷。 直到將虛擬硬碟連結至疑難排解 VM 的租用釋放，您才能將虛擬硬碟用於其他任何 VM。

1. 從 RDP 工作階段到您的 VM，開啟 [伺服器管理員]****，然後選取 [檔案和存放服務]****：

    ![在伺服器管理員中選取檔案和存放服務](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. 依序選取 [磁碟]**** 及您的資料磁碟。 在資料磁碟上按一下滑鼠右鍵，然後選取 [離線]****：

    ![在伺服器管理員中將資料磁碟設為離線](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 現在從 VM 中斷連結虛擬硬碟。 在 Azure 入口網站中選取 VM，然後按一下 [磁碟]****。 
4. 選取 [ **編輯**]，選取您附加的 OS 磁片，然後按一下 [卸 **離**]：

    ![將現有虛擬硬碟中斷連結](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    請稍候，等待 VM 成功將資料磁碟中斷連結再繼續。

## <a name="swap-the-os-disk-for-the-vm"></a>交換 VM 的 OS 磁片

Azure 入口網站現在支援變更 VM 的 OS 磁片。 若要執行此動作，請依照下列步驟執行：

1. 移至 [Azure 入口網站](https://portal.azure.com)。 從側邊欄選取 [ **虛擬機器** ]，然後選取有問題的 VM。
1. 在左窗格中，選取 [ **磁片**]，然後選取 [ **交換 OS 磁片**]。
        ![Azure 入口網站中交換 OS 磁片的相關映射](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 選擇您修復的新磁片，然後輸入 VM 的名稱以確認變更。 如果您在清單中看不到磁片，請在從疑難排解 VM 卸離磁片之後等候 10 ~ 15 分鐘。 也請確定磁片與 VM 位於相同的位置。
1. 選取 [確定]。

## <a name="next-steps"></a>後續步驟
如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 RDP 連接進行疑難排解](troubleshoot-rdp-connection.md)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Windows VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md)。

如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。


