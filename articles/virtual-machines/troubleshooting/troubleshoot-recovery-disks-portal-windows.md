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
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249993"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>使用 Azure 入口網站將 OS 磁碟連結至復原 VM，以針對 Windows VM 進行疑難排解
如果 Azure 中的 Windows 虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對虛擬硬碟本身執行疑難排解步驟。 常見的例子是應用程式更新無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure 入口網站將虛擬硬碟連接至另一個 Windows VM，以修正任何錯誤，然後重新建立原始 VM。 

## <a name="recovery-process-overview"></a>復原程序概觀
疑難排解程序如下所示︰

1. 停止受影響的 VM。
1. 為 VM 的 OS 磁片創建快照。
1. 從快照創建虛擬硬碟。
1. 將虛擬硬碟連結和掛接至另一個 Windows VM，以進行疑難排解。
1. 連接至疑難排解 VM。 編輯檔案或執行任何工具來修正原始虛擬硬碟的問題。
1. 從疑難排解 VM 卸載並中斷連結虛擬硬碟。
1. 將作業系統磁片交換為 VM。

> [!NOTE]
> 本文不適用於具有非託管磁片的 VM。

## <a name="take-a-snapshot-of-the-os-disk"></a>拍攝作業系統磁片的快照
快照集是完整的虛擬硬碟 (VHD) 唯讀複本。 我們建議您在拍攝快照之前乾淨地關閉 VM，以清除正在進行的任何進程。 要拍攝作業系統磁片的快照，請按照以下步驟操作：

1. 移至 [Azure 入口網站 ](https://portal.azure.com)。 從側邊欄中選擇**虛擬機器**，然後選擇有問題的 VM。
1. 在左側窗格中，選擇**磁片**，然後選擇 OS 磁片的名稱。
    ![有關作業系統磁片名稱的圖像](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. 在 OS 磁片的 **"概述"** 頁上，然後選擇 **"創建快照**"。
1. 在與作業系統磁片相同的位置創建快照。

## <a name="create-a-disk-from-the-snapshot"></a>從快照集建立磁碟
要從快照創建磁片，請按照以下步驟操作：

1. 從 Azure 門戶中選擇**雲外殼**。

    ![有關開放雲外殼的圖像](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 運行以下 PowerShell 命令，從快照創建託管磁片。 應將這些示例名稱替換為適當的名稱。

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
3. 如果命令成功運行，您將在提供的資源組中看到新磁片。

## <a name="attach-the-disk-to-another-vm"></a>將磁片連接到另一個 VM
在接下來幾個步驟中，您將使用另一個 VM 進行疑難排解。 將磁片附加到故障排除 VM 後，可以流覽和編輯磁片的內容。 此過程允許您更正任何配置錯誤或查看其他應用程式或系統日誌檔。 要將磁片附加到其他 VM，請按照以下步驟操作：

1. 從入口網站選取資源群組，然後選取疑難排解 VM。 選擇**磁片**，選擇 **"編輯**"，然後按一下"**添加資料磁片**：

    ![在入口網站中連結現有磁碟](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 在 **"資料磁片"** 清單中，選擇您標識的 VM 的 OS 磁片。 如果看不到 OS 磁片，請確保對 VM 和 OS 磁片進行故障排除，並在同一區域（位置）中。 
3. 選擇 **"保存"** 以應用更改。

## <a name="mount-the-attached-data-disk-to-the-vm"></a>將附加的資料磁片安裝到 VM

1. 打開遠端桌面連線到故障排除 VM。 
2. 在疑難排解 VM 中，打開**伺服器管理員**，然後選擇**檔和存儲服務**。 

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
4. 選擇 **"編輯**"，選擇您連接的 OS 磁片，然後按一下 **"分離**" ：

    ![將現有虛擬硬碟中斷連結](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    請稍候，等待 VM 成功將資料磁碟中斷連結再繼續。

## <a name="swap-the-os-disk-for-the-vm"></a>將作業系統磁片交換為 VM

Azure 門戶現在支援更改 VM 的 OS 磁片。 若要這樣做，請遵循下列步驟：

1. 移至 [Azure 入口網站 ](https://portal.azure.com)。 從側邊欄中選擇**虛擬機器**，然後選擇有問題的 VM。
1. 在左側窗格中，選擇**磁片**，然後選擇 **"交換 OS 磁片**"。
        ![有關 Azure 門戶中交換 OS 磁片的圖像](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 選擇您修復的新磁片，然後鍵入 VM 的名稱以確認更改。 如果在清單中看不到磁片，請等待 10 + 15 分鐘後，您將磁片從故障排除 VM 中分離。 還要確保磁片與 VM 處於同一位置。
1. 選取 [確定]。

## <a name="next-steps"></a>後續步驟
如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 RDP 連接進行疑難排解](troubleshoot-rdp-connection.md)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Windows VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md)。

如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。


