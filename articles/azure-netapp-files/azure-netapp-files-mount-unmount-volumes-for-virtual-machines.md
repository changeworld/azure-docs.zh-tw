---
title: 為虛擬機器安裝 Azure NetApp 檔卷
description: 瞭解如何在 Azure 中裝載或卸載 Windows 虛擬機器或 Linux 虛擬機器的卷。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551534"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區 

您可以視需要對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區。  Linux 虛擬機器的掛接指示可與 Azure NetApp Files 上取得。  

1. 按一下"**卷"** 邊欄選項卡，然後選擇要為其裝載的卷。 
2. 按一下"裝載所選卷**中的說明**"，然後按照說明裝載卷。 

    ![安裝指令 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![安裝說明 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    如果使用 NFSv4.1，請使用以下命令來裝載檔案系統：`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. 如果要在啟動或重新開機 Azure VM 時自動裝載 NFS 卷，請向主機上`/etc/fstab`的檔添加一個條目。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`是在卷屬性邊欄選項卡中找到的 Azure NetApp 檔卷的 IP 位址。
    * `$FILEPATH`是 Azure NetApp 檔卷的匯出路徑。
    * `$MOUNTPOINT`是在用於裝載 NFS 匯出的 Linux 主機上創建的目錄。

4. 如果要使用 NFS 將卷裝載到 Windows：

    a. 首先將卷安裝到 Unix 或 Linux VM 上。  
    b. 對卷`chmod 777`運行`chmod 775`或 命令。  
    c. 通過 Windows 上的 NFS 用戶端將卷安裝。

## <a name="next-steps"></a>後續步驟

* [針對 Azure NetApp Files 設定 NFSv4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常見問題解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
