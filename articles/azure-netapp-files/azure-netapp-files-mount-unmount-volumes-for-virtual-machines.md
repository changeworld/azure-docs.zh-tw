---
title: 裝載虛擬機器的 Azure NetApp Files 磁片區
description: 瞭解如何為 Azure 中的 Windows 虛擬機器或 Linux 虛擬機器掛接或卸載磁片區。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: eebb0a8dc23a6a442a170dcc6e19495f96ce2921
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930588"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區 

您可以視需要對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區。  Linux 虛擬機器的掛接指示可與 Azure NetApp Files 上取得。  

## <a name="requirements"></a>需求 

* 您至少必須要有一個匯出原則，才能存取 NFS 磁片區。
* 若要成功掛接 NFS 磁片區，請確定用戶端和 NFS 磁片區之間已開啟下列 NFS 埠：
    * 111 = `RPCBIND/Portmapper`
    * 635 = `mountd`
    * 2049 = `nfs`
    * 4045 = `nlockmgr` (僅限 NFSv3) 
    * 4046 = `status` (僅限 NFSv3) 

## <a name="steps"></a>步驟

1. 按一下 [ **磁片** 區] 分頁，然後選取您要掛接的磁片區。 
2. 從選取的磁片區按一下 [ **掛接指示** ]，然後依照指示來掛接磁片區。 

    ![裝載指示 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![裝載指示 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * 如果您要掛接 NFS 磁片區，請務必使用 `vers` 命令中的選項， `mount` 來指定對應至您要掛接之磁片區的 NFS 通訊協定版本。 
    * 如果您使用的是 Nfsv4.1 4.1，請使用下列命令來掛接檔案系統：  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > 如果您使用 Nfsv4.1 4.1，請確定裝載匯出的所有 Vm 都會使用唯一的主機名稱。

3. 如果您想要在 Azure VM 啟動或重新開機時自動裝載 NFS 磁片區，請將專案新增至 `/etc/fstab` 主機上的檔案。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` 是在 [磁片區屬性] 分頁中找到的 Azure NetApp Files 磁片區的 IP 位址。
    * `$FILEPATH` 這是 Azure NetApp Files 磁片區的匯出路徑。
    * `$MOUNTPOINT` 是在 Linux 主機上建立的目錄，用來掛接 NFS 匯出。

4. 如果您想要使用 NFS 將磁片區掛接至 Windows：

    a. 先將磁片區掛接到 Unix 或 Linux VM。  
    b. `chmod 777`對磁片區執行或 `chmod 775` 命令。  
    c. 透過 Windows 上的 NFS 用戶端裝載磁片區。
    
5. 如果您想要掛接 NFS Kerberos 磁片區，請參閱 [設定 nfsv4.1 4.1 kerberos 加密](configure-kerberos-encryption.md) 以取得其他詳細資料。 

## <a name="next-steps"></a>後續步驟

* [針對 Azure NetApp Files 設定 NFSv4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常見問題集](./azure-netapp-files-faqs.md#nfs-faqs)
* [網路檔案系統概觀](/windows-server/storage/nfs/nfs-overview)
* [掛接 NFS Kerberos 磁片區](configure-kerberos-encryption.md#kerberos_mount)