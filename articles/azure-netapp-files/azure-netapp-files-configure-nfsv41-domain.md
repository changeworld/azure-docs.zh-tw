---
title: 針對 Azure NetApp Files 設定 NFSv 4.1 預設網域 |Microsoft Docs
description: 說明如何設定 NFS 用戶端，以搭配使用 NFSv 4.1 與 Azure NetApp Files。
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: dda911add42568e76160e4233502a1f4f550520d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483715"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>針對 Azure NetApp Files 設定 NFSv4.1 預設網域

NFSv4 引進了驗證網域的概念。 Azure NetApp Files 目前支援從服務到 NFS 用戶端的僅限根使用者對應。 若要搭配使用 NFSv 4.1 功能與 Azure NetApp Files，您必須更新 NFS 用戶端。

## <a name="default-behavior-of-usergroup-mapping"></a>使用者/群組對應的預設行為

根對應預設為 `nobody` 使用者，因為 NFSv4 網域設定為 `localdomain` 。 當您將 Azure NetApp Files NFSv 4.1 磁片區掛接為 root 時，您會看到檔案許可權，如下所示：  

![NFSv 4.1 使用者/群組對應的預設行為](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

如上述範例所示，的使用者 `file1` 應該是 `root` ，但根據預設，它會對應至 `nobody` 。  本文說明如何將 `file1` 使用者設定為 `root` 。  

## <a name="steps"></a>步驟 

1. 編輯 `/etc/idmapd.conf` NFS 用戶端上的檔案。   
    取消批註程式程式碼 `#Domain` （也就是 `#` 從行中移除），並將值變更 `localdomain` 為 `defaultv4iddomain.com` 。 

    初始設定： 
    
    ![NFSv 4.1 的初始設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    更新的設定：
    
    ![已更新 NFSv 4.1 的設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 卸載任何目前裝載的 NFS 磁片區。
3. 更新 `/etc/idmapd.conf` 檔案。
4. `rpcbind`在您的主機上重新開機服務（ `service rpcbind restart` ），或直接重新開機主機。
5. 視需要掛接 NFS 磁片區。   

    請參閱[掛接或卸載 Windows 或 Linux 虛擬機器的磁片](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)區。 

下列範例顯示產生的使用者/群組變更： 

![NFSv 4.1 的產生設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

如範例所示，使用者/群組現在已從變更 `nobody` 為 `root` 。

## <a name="behavior-of-other-non-root-users-and-groups"></a>其他（非根）使用者和群組的行為

Azure NetApp Files 支援本機使用者（在主機本機建立的使用者），其具有與 NFSv 4.1 磁片區中的檔案或資料夾相關聯的許可權。 不過，此服務目前不支援跨多個節點對應使用者/群組。 因此，在一部主機上建立的使用者預設不會對應到在另一部主機上建立的使用者。 

在下列範例中， `Host1` 有三個現有的測試使用者帳戶（ `testuser01` 、 `testuser02` 、 `testuser03` ）： 

![NFSv 4.1 的產生設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

請 `Host2` 注意，在上，尚未建立測試使用者帳戶，但同時在兩部主機上裝載相同的磁片區：

![NFSv 4.1 的產生設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>下一步 

[對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

