---
title: 掛接適用于虛擬機器的 Azure NetApp Files 磁片區
description: 瞭解如何在 Azure 中掛接或卸載 Windows 虛擬機器或 Linux 虛擬機器的磁片區。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.openlocfilehash: 4ad3800748330d5c3a6a32c6c0824bc72a05d0ef
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533082"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區 

您可以視需要對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區。  Linux 虛擬機器的掛接指示可與 Azure NetApp Files 上取得。  

> [!IMPORTANT] 
> 您必須至少有一個匯出原則，才能存取 NFS 磁片區。

1. 按一下 [**磁片**區] 分頁，然後選取您要掛接的磁片區。 
2. 按一下所選磁片區中的 [**掛接指示**]，然後依照指示來裝載磁片區。 

    ![裝載指示 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![裝載指示 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * 如果您要掛接 NFS 磁片區，請確定您在 `vers` 命令中使用選項， `mount` 以指定對應至您要掛接之磁片區的 NFS 通訊協定版本。 
    * 如果您使用 NFSv 4.1，請使用下列命令來掛接檔案系統：`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. 如果您想要在 Azure VM 啟動或重新開機時自動掛接 NFS 磁片區，請將專案新增至 `/etc/fstab` 主機上的檔案。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`是 [磁片區屬性] 分頁中找到之 Azure NetApp Files 磁片區的 IP 位址。
    * `$FILEPATH`是 Azure NetApp Files 磁片區的匯出路徑。
    * `$MOUNTPOINT`是在用來裝載 NFS 匯出的 Linux 主機上建立的目錄。

4. 如果您想要使用 NFS 將磁片區掛接到 Windows：

    a. 請先將磁片區掛接到 Unix 或 Linux VM。  
    b. `chmod 777`針對磁片區執行或 `chmod 775` 命令。  
    c. 透過 Windows 上的 NFS 用戶端掛接磁片區。
    
5. 如果您想要掛接 NFS Kerberos 磁片區，請參閱[Configure nfsv 4.1 kerberos encryption](configure-kerberos-encryption.md)以取得其他詳細資料。 

## <a name="next-steps"></a>後續步驟

* [針對 Azure NetApp Files 設定 NFSv4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常見問題集](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [網路檔案系統概觀](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
* [掛接 NFS Kerberos 磁片區](configure-kerberos-encryption.md#kerberos_mount)
