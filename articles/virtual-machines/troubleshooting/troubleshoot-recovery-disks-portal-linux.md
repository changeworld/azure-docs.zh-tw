---
title: 在 Azure 入口網站中使用 Linux 疑難排解 VM | Microsoft Docs
description: 了解如何使用 Azure 入口網站將 OS 磁碟連接至復原 VM，以針對 Linux 虛擬機器問題進行疑難排解
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: affdee6871649102ef7881fb0f540eba6ab450ca
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200952"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>使用 Azure 入口網站將 OS 磁碟連結至復原 VM，以針對 Linux VM 進行疑難排解
如果 Linux 虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對虛擬硬碟本身執行疑難排解步驟。 常見的例子是 `/etc/fstab` 中的項目無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure 入口網站將虛擬硬碟連接至另一個 Linux VM，以修正任何錯誤，然後重新建立原始 VM。

## <a name="recovery-process-overview"></a>復原程序概觀
疑難排解程序如下所示︰

1. 停止受影響的 VM。
1. 製作 VM 的 OS 磁片快照集。
1. 從快照集建立虛擬硬碟。
1. 將虛擬硬碟連結和掛接至另一個 Linux VM，以進行疑難排解。
1. 連接至疑難排解 VM。 編輯檔案或執行任何工具來修正原始虛擬硬碟的問題。
1. 從疑難排解 VM 卸載並中斷連結虛擬硬碟。
1. 交換 VM 的 OS 磁片。

> [!NOTE]
> 本文不適用於具有非受控磁片的 VM。

## <a name="determine-boot-issues"></a>判斷開機問題
檢查開機診斷和 VM 螢幕擷取畫面來判斷 VM 為何無法正常開機。 常見的例子是 `/etc/fstab` 中的項目無效，或因為刪除或移動基礎虛擬硬碟。

在入口網站中選取您的 VM，然後向下捲動至 [支援 + 疑難排解] 區段。 按一下 [開機診斷] 以檢視從 VM 串流過來的主控台訊息。 檢閱主控台記錄，以查看是否可以判斷 VM 為何會發生問題。 下列範例說明卡在維護模式，因而需要手動互動的 VM：

![檢視 VM 開機診斷主控台記錄](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

您也可以按一下橫跨在開機診斷記錄頂端的 **螢幕擷取畫面**，下載所擷取的 VM 螢幕擷取畫面。

## <a name="take-a-snapshot-of-the-os-disk"></a>取得 OS 磁片的快照集
快照集是完整的虛擬硬碟 (VHD) 唯讀複本。 建議您在建立快照集之前，先將 VM 完全關閉，以清除進行中的任何處理程式。 若要取得 OS 磁片的快照集，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。 從側邊欄選取 [ **虛擬機器** ]，然後選取有問題的 VM。
1. 在左窗格中，選取 [ **磁片**]，然後選取 OS 磁片的名稱。
    ![作業系統磁片名稱的相關影像](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. 在 OS 磁片的 [ **總覽** ] 頁面上，選取 [ **建立快照** 集]。
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
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
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

## <a name="attach-disk-to-another-vm"></a>將磁片連結至另一個 VM
在接下來幾個步驟中，您將使用另一個 VM 進行疑難排解。 將磁片連結至疑難排解 VM 之後，您可以流覽並編輯磁片的內容。 此程式可讓您更正任何設定錯誤，或查看其他應用程式或系統記錄檔。 若要將磁片連結至另一個 VM，請遵循下列步驟：

1. 從入口網站選取資源群組，然後選取疑難排解 VM。 選取 [ **磁片**]，選取 [ **編輯**]，然後按一下 [ **新增資料磁片**：

    ![在入口網站中連結現有磁碟](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 在 [ **資料磁片** ] 清單中，選取您所識別 VM 的 OS 磁片。 如果您沒有看到 OS 磁片，請確定疑難排解 VM 和 OS 磁片位於相同區域 (位置) 。 
3. 選取 [ **儲存** ] 以套用變更。

## <a name="mount-the-attached-data-disk"></a>掛接已連結的資料磁碟

> [!NOTE]
> 下列範例詳細說明 Ubuntu VM 上所需的步驟。 如果您使用不同的 Linux 發行版本，例如 Red Hat Enterprise Linux 或 SUSE，則記錄檔位置和 `mount` 命令可能稍微不同。 請參閱您的特定發行版本的文件，以了解命令中相應的變更。

1. 使用適當的認證以 SSH 登入疑難排解 VM。 如果此磁碟是第一個連結至疑難排解 VM 的資料磁碟，則很可能會連接至 `/dev/sdc`。 使用 `dmseg` 來列出已連結的磁碟︰

    ```bash
    dmesg | grep SCSI
    ```
    輸出類似於下列範例：

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    在上述範例中，OS 磁碟位於 `/dev/sda`，而提供給每個 VM 的暫存磁碟位於 `/dev/sdb`。 如果您有多個資料磁碟，它們應該是位於 `/dev/sdd`、`/dev/sde`，依此類推。

2. 建立目錄來掛接現有的虛擬硬碟。 下列範例會建立名為 `troubleshootingdisk` 的目錄：

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 如果您在現有的虛擬硬碟上有多個磁碟分割，請掛接所需的磁碟分割。 下列範例會將第一個主要磁碟分割掛接在 `/dev/sdc1`：

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 最佳做法是使用虛擬硬碟的通用唯一識別碼 (UUID)，將資料磁碟掛接在 Azure 中的 VM。 在這個簡短的疑難排解案例中，不需要使用 UUID 來掛接虛擬硬碟。 但在正常使用情況下，如果編輯 `/etc/fstab` 來使用裝置名稱掛接虛擬硬碟，而不是使用 UUID，可能會造成 VM 無法開機。


## <a name="fix-issues-on-original-virtual-hard-disk"></a>修正原始虛擬硬碟的問題
已掛接現有的虛擬硬碟掛，您現在可以視需要執行任何維護和疑難排解步驟。 解決問題之後，請繼續進行下列步驟。

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸載並中斷連結原始虛擬硬碟
錯誤解決之後，請將現有虛擬硬碟與疑難排解 VM 的連結中斷。 直到將虛擬硬碟連結至疑難排解 VM 的租用釋放，您才能將虛擬硬碟用於其他任何 VM。

1. 從疑難排解 VM 的 SSH 工作階段，卸載現有的虛擬硬碟。 首先離開掛接點的上層目錄︰

    ```bash
    cd /
    ```

    現在卸載現有的虛擬硬碟。 下列範例會卸載位於 `/dev/sdc1` 的裝置：

    ```bash
    sudo umount /dev/sdc1
    ```

2. 現在從 VM 中斷連結虛擬硬碟。 在入口網站中選取 VM，然後按一下 [磁碟]。 選取您現有的虛擬硬碟，然後按一下 [中斷連結]：

    ![將現有虛擬硬碟中斷連結](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    請稍候，等待 VM 成功將資料磁碟中斷連結再繼續。

## <a name="swap-the-os-disk-for-the-vm"></a>交換 VM 的 OS 磁片

Azure 入口網站現在支援變更 VM 的 OS 磁片。 若要這樣做，請執行下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。 從側邊欄選取 [ **虛擬機器** ]，然後選取有問題的 VM。
1. 在左窗格中，選取 [ **磁片**]，然後選取 [ **交換 OS 磁片**]。
        ![Azure 入口網站中交換 OS 磁片的相關映射](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 選擇您修復的新磁片，然後輸入 VM 的名稱以確認變更。 如果您在清單中看不到磁片，請在從疑難排解 VM 卸離磁片之後等候 10 ~ 15 分鐘。 也請確定磁片與 VM 位於相同的位置。
1. 選取 [確定]。

## <a name="next-steps"></a>後續步驟
如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 SSH 連接進行疑難排解](troubleshoot-ssh-connection.md)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Linux VM 上的應用程式連線問題進行疑難排解](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)。

如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
