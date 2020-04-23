---
title: 為虛擬機器安裝 Azure NetApp 檔案卷
description: 瞭解如何在 Azure 中裝載或卸載 Windows 虛擬機器或 Linux 虛擬機器的卷。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084935"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區 

您可以視需要對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區。  Linux 虛擬機器的掛接指示可與 Azure NetApp Files 上取得。  

1. 按下「**捲」** 邊欄選項卡,然後選擇要為其裝載的卷。 
2. 按下"裝載所選卷**中的說明**",然後按照說明裝載卷。 

    ![安裝指令 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![安裝說明 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    如果使用 NFSv4.1,請使用以下指令來裝載檔案系統:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. 如果要在啟動或重新啟動 Azure VM 時自動載入 NFS 卷,`/etc/fstab`請向主機上的檔添加一個條目。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`是在卷屬性邊欄選項卡中找到的 Azure NetApp 檔卷的 IP 位址。
    * `$FILEPATH`是 Azure NetApp 檔卷的匯出路徑。
    * `$MOUNTPOINT`是在用於裝載 NFS 匯出的 Linux 主機上創建的目錄。

4. 如果要使用 NFS 將捲載到 Windows:

    a. 首先將捲安裝到 Unix 或 Linux VM 上。  
    b. 對卷`chmod 777`運行`chmod 775`或命令。  
    c. 通過 Windows 上的 NFS 用戶端將捲安裝。

## <a name="next-steps"></a>後續步驟

* [針對 Azure NetApp Files 設定 NFSv4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常見問題解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [網路檔案系統概觀](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
