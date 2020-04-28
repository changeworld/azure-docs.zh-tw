---
title: 設定 NFS 磁片區的匯出原則-Azure NetApp Files
description: 說明如何使用 Azure NetApp Files 設定匯出原則以控制 NFS 磁片區的存取
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551553"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>設定 NFS 磁碟區的匯出原則

您可以選擇性地設定匯出原則，以控制 Azure NetApp Files 磁碟區的存取權。 Azure NetApp Files 匯出原則僅支援 NFS 磁片區。  同時支援 NFSv3 和 NFSv4。 

## <a name="steps"></a>步驟 

1.  按一下 [Azure NetApp Files] 流覽窗格中的 [**匯出原則**]。 

2.  指定下列欄位的資訊以建立匯出原則規則：   
    *  **指數**   
        指定規則的索引編號。  
        匯出原則可以包含最多五項規則。 系統會會根據規則在索引編號清單中的順序評估規則。 具有較低索引編號的規則會先接受評估。 例如，具有索引編號 1 的規則，會在具有索引編號 2 的規則之前接受評估。 

    * **允許的用戶端**   
        以下列其中一個格式指定值：  
        * IPv4 位址，例如 `10.1.12.24` 
        * IPv4 位址，具有以位元數表示的子網路遮罩，例如 `10.1.12.10/4`

    * **存取**  
        請選取下列其中一個存取類型：  
        * 無存取權 
        * 讀取和寫入
        * 唯讀

    ![匯出原則](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>後續步驟 
* [管理磁碟區](azure-netapp-files-manage-volumes.md)
* [裝載或取消裝載虛擬機器磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照集](azure-netapp-files-manage-snapshots.md)
