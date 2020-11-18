---
title: Azure NetApp Files 的新功能 | Microsoft Docs
description: 提供 Azure NetApp Files 最新功能和增強功能的摘要。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/05/2020
ms.author: b-juche
ms.openlocfilehash: 3f13d997f73e9737b8770eec7e0742d50bf1abb8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421697"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp Files 的新功能

Azure NetApp Files 會定期更新。 本文提供最新功能和增強功能的摘要。 

## <a name="november-2020"></a>2020 年 11 月

* [快照集還原](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    快照集還原功能可讓您快速地將磁碟區還原為建立特定快照集時所處的狀態。 在大部分的情況下，還原磁碟區的速度會比將個別檔案從快照集還原至作用中檔案系統快得多。 相較於將快照集還原至新的磁碟區，其空間利用率也較高。

## <a name="september-2020"></a>2020 年 9 月

* [Azure NetApp Files 跨區域複寫](cross-region-replication-introduction.md) (公開預覽)

  Azure NetApp Files 現在支援跨區域複寫。 有了這項新的災害復原功能，您即可利用快速且符合成本效益的方式，將您的 Azure NetApp Files 磁碟區從一個 Azure 區域複寫到另一個 Azure 區域，保護您的資料免於受到無法預見的區域失敗。 Azure NetApp Files 跨區域複寫會利用 NetApp SnapMirror® 技術；只有變更的區塊會以壓縮且有效率的格式透過網路傳送。 這項專屬技術可將跨區域複寫所需的資料量降到最低，因而節省資料傳輸成本。 其也會縮短複寫時間，讓您可以達到較小的復原點目標 (RPO)。

* [手動 QoS 容量集區](manual-qos-capacity-pool-introduction.md) (預覽)  

    在手動 QoS 容量集區中，您可以個別指派磁碟區的容量和輸送量。 以手動 QoS 容量集區建立的所有磁碟區輸送量總計，受限於集區的輸送量總計。 這是由集區大小和服務層級輸送量的組合所決定。 或者，容量集區的 [QoS 類型](azure-netapp-files-understand-storage-hierarchy.md#qos_types) 可以是 [自動 (自動)]，此為預設值。 在自動 QoS 容量集區中，輸送量會自動指派給集區中的磁碟區，並與指派給磁碟區的大小配額成正比。

* [LDAP 簽署](azure-netapp-files-create-volumes-smb.md) (預覽)   

    Azure NetApp Files 現在支援 LDAP 簽署，以便在 Azure NetApp Files 服務與使用者指定的 Active Directory Domain Services 網域控制站之間進行安全的 LDAP 查詢。 此功能目前為預覽狀態。

* [AD 驗證的 AES 加密](azure-netapp-files-create-volumes-smb.md) (預覽)

    Azure NetApp Files 現在支援對 DC 進行 LDAP 連線的 AES 加密，以啟用 SMB 磁碟區的 AES 加密。 此功能目前為預覽狀態。 

* 新的[計量](azure-netapp-files-metrics.md)：   

    * 新的磁碟區計量： 
        * 磁碟區配置大小：磁碟區的佈建大小
    * 新的集區計量： 
        * 集區配置大小：集區的佈建大小 
        * 集區的快照集大小總計：集區中所有磁碟區的快照集大小總和

## <a name="july-2020"></a>2020 年 7 月

* [雙重通訊協定 (NFSv3 和 SMB) 磁碟區](create-volumes-dual-protocol.md)

    您現在可以建立 Azure NetApp Files 磁碟區，以允許透過 LDAP 使用者對應的支援同時進行雙重通訊協定 (NFS v3 和 SMB) 存取。 這項功能可讓您使用以 Linux 為基礎的工作負載，以產生資料並加以儲存在 Azure NetApp Files 磁碟區的使用案例。 同時，您的員工必須使用以 Windows 為基礎的用戶端和軟體，從相同的 Azure NetApp Files 磁碟區分析新產生的資料。 同時雙重通訊協定存取功能讓您不需要將工作負載產生的資料複製到具有不同通訊協定的個別磁碟區，以便進行後續分析、節省儲存成本和營運時間。 這項功能是免費的 (一般 [Azure NetApp Files 儲存成本](https://azure.microsoft.com/pricing/details/netapp/) 仍適用) 且已正式推出。 若要深入了解，請參閱[同時進行的雙重通訊協定存取文件](create-volumes-dual-protocol.MD)。

* [NFS v4.1 Kerberos 加密傳輸中](configure-kerberos-encryption.MD)

    Azure NetApp Files 現在支援 Kerberos 模式的 NFS 用戶端加密 (krb5、krb5i 和 krb5p) 與 AES-256 加密，為您提供了額外的資料安全性。 這項功能是免費的 (一般 [Azure NetApp Files 儲存成本](https://azure.microsoft.com/pricing/details/netapp/) 仍適用) 且已正式推出。 若要深入了解，請參閱 [NFS v4.1 Kerberos 加密文件](configure-kerberos-encryption.MD)。

* [動態磁碟區服務層級變更](dynamic-change-volume-service-level.MD)

    雲端承諾擁有 IT 費用的彈性。 您現在可以將磁碟區移到另一個使用您所需磁碟區服務層級的容量集區，以變更現有 Azure NetApp Files 磁碟區的服務層級。 此磁碟區的就地服務層級變更不需要您遷移資料。 也不會影響磁碟區的資料平面存取。 您可以變更現有的磁碟區，以使用較高的服務層級來獲得更好的效能，或使用較低的服務等級來獲得成本最佳化。 這項功能是免費的 (一般 [Azure NetApp Files 儲存成本](https://azure.microsoft.com/pricing/details/netapp/) 仍適用) 且目前處於公開預覽狀態。 您可以遵循[動態磁碟區服務層級變更文件](dynamic-change-volume-service-level.md)來註冊功能預覽。

* [磁碟區快照集原則](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (預覽) 

    Azure NetApp Files 可讓您建立磁碟區的時間點快照集。 您現在可以建立快照集原則，讓 Azure NetApp Files 以您選擇的頻率自動建立磁碟區快照集。 您可以排程要在每小時、每天、每週或每月週期中執行的快照集。 您也可以指定要保留在快照集原則中的快照集數目上限。 這項功能是免費的 (一般 [Azure NetApp Files 儲存成本](https://azure.microsoft.com/pricing/details/netapp/) 仍適用) 且目前處於預覽狀態。 您可以遵循[磁碟區快照集原則文件](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)來註冊功能預覽。

* [NFS 根存取匯出原則](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files 現在可讓您指定根帳號是否可以存取磁碟區。 

* [隱藏快照集路徑](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files 現在可讓您指定使用者是否可以在裝載的磁碟區上查看及存取 `.snapshot` 目錄 (NFS 用戶端) 或 `~snapshot` 資料夾 (SMB 用戶端)。

## <a name="may-2020"></a>2020 年 5 月

* [備份原則使用者](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) (預覽)

    Azure NetApp Files 可讓您將需要更高權限的其他帳戶，納入為了使用 Azure NetApp Files 而建立的電腦帳戶。 指定的帳號可允許在檔案或資料夾層級變更 NTFS 權限。 例如，您可以指定非特殊權限服務帳戶，用來將資料移轉至 Azure NetApp Files 中的 SMB 檔案共用。 「備份原則使用者」功能目前為預覽狀態。

## <a name="next-steps"></a>後續步驟
* [什麼是 Azure NetApp Files](azure-netapp-files-introduction.md)
* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md) 
