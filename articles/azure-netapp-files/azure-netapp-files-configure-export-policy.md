---
title: 設定 NFS 磁片區的匯出原則-Azure NetApp Files
description: 說明如何使用 Azure NetApp Files 設定匯出原則來控制 NFS 磁片區的存取
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 77630ddcd61d17f3b47e6cb5d43396c1a6f0e904
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517864"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>設定 NFS 磁碟區的匯出原則

您可以設定匯出原則來控制 Azure NetApp Files 磁片區的存取權。 Azure NetApp Files 匯出原則支援使用 NFS 通訊協定的磁片區 (NFSv3 和 Nfsv4.1 4.1) 和雙重通訊協定 (NFSv3 和 SMB) 。 

您最多可以建立五個匯出原則規則。

## <a name="configure-the-policy"></a>設定原則 

1.  在 [ **磁片** 區] 頁面上，選取您要設定匯出原則的磁片區，然後選取 [ **匯出原則** ]。 您也可以在建立磁片區期間設定匯出原則。

2.  若要建立匯出原則規則，請指定下列資訊：   
    * **Index** ：指定規則的索引編號。  
      
      匯出原則可以包含最多五項規則。 系統會會根據規則在索引編號清單中的順序評估規則。 具有較低索引編號的規則會先接受評估。 例如，具有索引編號 1 的規則，會在具有索引編號 2 的規則之前接受評估。 

    * **允許的用戶端** ：指定下列其中一種格式的值：  
      * [IPv4 位址] 旁邊。 範例： `10.1.12.24`
      * 具有子網路遮罩的 IPv4 位址，以位數表示。 範例： `10.1.12.10/4`
      * 逗點分隔的 IP 位址。 您可以在單一規則中輸入多個主機 Ip，方法是以逗號分隔它們。 範例： `10.1.12.25,10.1.12.28,10.1.12.29`

    * **存取** ：選取下列其中一種存取類型：  
      * 無存取權 
      * 讀取和寫入
      * 唯讀

    * **唯讀** 和 **讀取/寫入** ：如果您搭配 nfsv4.1 4.1 使用 Kerberos 加密，請依照 [設定 nfsv4.1 4.1 Kerberos 加密](configure-kerberos-encryption.md)中的指示進行。  如需 Kerberos 的效能影響，請參閱 [nfsv4.1 4.1 上的 Kerberos 效能影響](configure-kerberos-encryption.md#kerberos_performance)。 

      ![Kerberos 安全性選項](../media/azure-netapp-files/kerberos-security-options.png) 

    * **根存取** ：指定帳戶是否 `root` 可以存取磁片區。  根據預設，根目錄存取設定為 [ **開啟** ]，而且 `root` 帳戶可以存取磁片區。

      ![匯出原則](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>後續步驟 
* [裝載或取消裝載虛擬機器磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照集](azure-netapp-files-manage-snapshots.md)
