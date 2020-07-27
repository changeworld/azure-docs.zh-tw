---
title: 設定 NFS 磁片區的匯出原則-Azure NetApp Files
description: 說明如何使用 Azure NetApp Files 設定匯出原則以控制 NFS 磁片區的存取
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 6d990b94210383ba4b30569693f4471f43306ed2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169813"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>設定 NFS 磁碟區的匯出原則

您可以設定匯出原則來控制對 Azure NetApp Files 磁片區的存取。 Azure NetApp Files 匯出原則支援使用 NFS 通訊協定的磁片區（NFSv3 和 NFSv 4.1）和雙重通訊協定（NFSv3 和 SMB）。 

您最多可以建立五個匯出原則規則。

## <a name="steps"></a>步驟 

1.  從 [磁片區] 頁面中，選取您要設定匯出原則的磁片區，然後按一下 [**匯出原則**]。 

    您也可以在建立磁片區期間設定匯出原則。

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

    * **根存取**  
        指定帳戶是否 `root` 可以存取磁片區。  根據預設，[根存取] 設定為 [**開啟**]，且 `root` 帳戶具有磁片區的存取權。

        ![匯出原則](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>後續步驟 
* [管理磁碟區](azure-netapp-files-manage-volumes.md)
* [裝載或取消裝載虛擬機器磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照集](azure-netapp-files-manage-snapshots.md)
