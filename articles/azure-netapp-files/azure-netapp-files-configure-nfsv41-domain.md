---
title: 為 Azure NetApp Files 設定 Nfsv4.1 4.1 預設網域 |Microsoft Docs
description: 說明如何設定 NFS 用戶端搭配使用 Nfsv4.1 4.1 與 Azure NetApp Files。
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
ms.date: 10/14/2020
ms.author: b-juche
ms.openlocfilehash: c3c853190d5f63bbe9012727d8b7b7ac91da135f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072147"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>針對 Azure NetApp Files 設定 NFSv4.1 預設網域

NFSv4 引進驗證網域的概念。 Azure NetApp Files 目前支援從服務到 NFS 用戶端的僅限根目錄使用者對應。 若要使用 Nfsv4.1 4.1 功能搭配 Azure NetApp Files，您需要更新 NFS 用戶端。

## <a name="default-behavior-of-usergroup-mapping"></a>使用者/群組對應的預設行為

根對應預設為 `nobody` 使用者，因為 NFSv4 網域預設會設定為 `localdomain` 。 當您將 Azure NetApp Files Nfsv4.1 4.1 磁片區掛接為根目錄時，您會看到檔案許可權，如下所示：  

![Nfsv4.1 4.1 的使用者/群組對應預設行為](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

如上一個範例所示，的使用者 `file1` 應該是 `root` ，但它預設會對應到 `nobody` 。  本文說明如何將 `file1` `root` 設定變更為，以將使用者設定為 `idmap Domain` `defaultv4iddomain.com` 。  

## <a name="steps"></a>步驟 

1. 編輯 `/etc/idmapd.conf` NFS 用戶端上的檔案。   
    取消批註該行 `#Domain` (也就是說， `#` 從行) 移除，然後將值變更 `localdomain` 為 `defaultv4iddomain.com` 。 

    初始設定： 
    
    ![Nfsv4.1 4.1 的初始設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    更新的設定：
    
    ![Nfsv4.1 4.1 的更新設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 卸載任何目前裝載的 NFS 磁片區。
3. 更新 `/etc/idmapd.conf` 檔案。
4. `rpcbind`在您的主機上重新開機服務 (`service rpcbind restart`) ，或直接將主機重新開機。
5. 視需要掛接 NFS 磁片區。   

    請參閱 [為 Windows 或 Linux 虛擬機器掛接或卸載磁片](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)區。 

下列範例顯示產生的使用者/群組變更： 

![顯示產生的使用者/群組變更範例的螢幕擷取畫面。](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

如範例所示，使用者/群組現在已從變更 `nobody` 為 `root` 。

## <a name="behavior-of-other-non-root-users-and-groups"></a>其他 (非根) 使用者和群組的行為

Azure NetApp Files 支援本機使用者 (在主機上本機建立的使用者，) 具有與 Nfsv4.1 4.1 磁片區中檔案或資料夾相關聯的許可權。 不過，此服務目前不支援跨多個節點對應使用者/群組。 因此，在某個主機上建立的使用者預設不會對應到在另一部主機上建立的使用者。 

在下列範例中， `Host1` 有三個現有的測試使用者帳戶 (`testuser01` 、 `testuser02` `testuser03`) ： 

![顯示 Host1 有三個現有測試使用者帳戶的螢幕擷取畫面。](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

在上，請注意，尚未 `Host2` 建立測試使用者帳戶，但相同的磁片區裝載于兩個主機上：

![Nfsv4.1 4.1 的結果設定](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>後續步驟 

[對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

