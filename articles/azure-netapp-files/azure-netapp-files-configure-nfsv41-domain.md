---
title: 為 Azure NetApp 檔配置 NFSv4.1 預設域 |微軟文檔
description: 描述如何配置 NFS 用戶端，以便使用 NFSv4.1 與 Azure NetApp 檔。
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906282"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>針對 Azure NetApp Files 設定 NFSv4.1 預設網域

NFSv4 引入了身份驗證域的概念。 Azure NetApp 檔當前支援從服務到 NFS 用戶端的純根使用者映射。 要將 NFSv4.1 功能與 Azure NetApp 檔一起使用，您需要更新 NFS 用戶端。

## <a name="default-behavior-of-usergroup-mapping"></a>使用者/組映射的預設行為

根映射預設給使用者，`nobody`因為 NFSv4 域設置為`localdomain`。 當您將 Azure NetApp 檔 NFSv4.1 卷裝載為根卷時，您將看到檔許可權，如下所示：  

![NFSv4.1 的使用者/組映射的預設行為](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

如上述示例所示，使用者應`file1`為`root`，但它預設映射到`nobody`。  本文介紹如何將`file1`使用者設置為`root`。  

## <a name="steps"></a>步驟 

1. 編輯`/etc/idmapd.conf`NFS 用戶端上的檔。   
    `#Domain`取消注釋行（`#`即從行中刪除），並將值`localdomain`更改為`defaultv4iddomain.com`。 

    初始配置： 
    
    ![NFSv4.1 的初始配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    更新的配置：
    
    ![NFSv4.1 的更新配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 卸載當前裝載的任何 NFS 卷。
3. 更新 `/etc/idmapd.conf` 檔案。
4. 重新開機主機`rpcbind`上的服務 （），`service rpcbind restart`或只是重新開機主機。
5. 根據需要安裝 NFS 卷。   

    請參閱[為 Windows 或 Linux 虛擬機器安裝或卸載卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。 

以下示例顯示生成的使用者/組更改： 

![NFSv4.1 的組建組態](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

如示例所示，使用者/組現在已更改為`nobody`。 `root`

## <a name="behavior-of-other-non-root-users-and-groups"></a>其他（非根）使用者和組的行為

Azure NetApp 檔支援具有與 NFSv4.1 卷中檔或資料夾關聯的許可權的本地使用者（在主機上本地創建的使用者）。 但是，該服務當前不支援跨多個節點映射使用者/組。 因此，預設情況下，在一台主機上創建的使用者不會映射到在另一台主機上創建的使用者。 

在下面的示例中，`Host1`有三個現有的測試使用者帳戶 （， `testuser02` `testuser03` `testuser01`， 。 

![NFSv4.1 的組建組態](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

在`Host2`上 請注意，尚未創建測試使用者帳戶，但兩個主機上都裝載了相同的卷：

![NFSv4.1 的組建組態](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>後續步驟 

[對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

