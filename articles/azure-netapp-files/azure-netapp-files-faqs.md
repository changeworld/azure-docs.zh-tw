---
title: 關於 Azure NetApp Files 的常見問題 |Microsoft Docs
description: 查看有關 Azure NetApp Files 的常見問題，例如網路、安全性、效能、容量管理，以及資料移轉/保護。
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
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: b-juche
ms.openlocfilehash: 913d61c506505d18fff416291e7f3b718f1d92f3
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913493"
---
# <a name="faqs-about-azure-netapp-files"></a>關於 Azure NetApp Files 的常見問題

本文將回答有關 Azure NetApp Files (常見問題) 的常見問題。 

## <a name="networking-faqs"></a>網路常見問題

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 資料路徑是否經過網際網路？  

不會。 NFS 資料路徑不會經過網際網路。 Azure NetApp Files 是部署至 Azure 虛擬網路的 azure 原生服務， (VNet) 可用的服務。 Azure NetApp Files 使用委派的子網，並直接在 VNet 上布建網路介面。 

如需詳細資料，請參閱 [Azure NetApp Files 網路規劃的指導方針](./azure-netapp-files-network-topologies.md) 。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>我可以將已建立的 VNet 連線到 Azure NetApp Files 服務嗎？

是的，您可以將您建立的 Vnet 連接到服務。 

如需詳細資料，請參閱 [Azure NetApp Files 網路規劃的指導方針](./azure-netapp-files-network-topologies.md) 。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>我可以使用 DNS FQDN 名稱掛接 Azure NetApp Files 的 NFS 磁片區嗎？

是的，如果您建立必要的 DNS 專案，就可以了。 Azure NetApp Files 提供布建磁片區的服務 IP。 

> [!NOTE] 
> Azure NetApp Files 可以視需要為服務部署額外的 Ip。  DNS 專案可能需要定期更新。

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>我可以為 Azure NetApp Files 磁片區設定或選取自己的 IP 位址嗎？  

不會。 對 Azure NetApp Files 磁片區的 IP 指派是動態的。 不支援靜態 IP 指派。 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files 是否支援 (IPv4 和 IPv6) VNet 的雙重 stack？

否，Azure NetApp Files 目前不支援 (IPv4 和 IPv6) VNet 的雙重 stack。  
 
## <a name="security-faqs"></a>安全性常見問題集

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM 與儲存體之間的網路流量是否會加密？

Nfsv4.1 4.1 用戶端與 Azure NetApp Files 磁片區之間的資料流量，可以使用 Kerberos 和 AES-256 加密來加密。 如需詳細資訊，請參閱為 [Azure NetApp Files 設定 nfsv4.1 4.1 Kerberos 加密](configure-kerberos-encryption.md) 。   

NFSv3 或 SMB3 用戶端與 Azure NetApp Files 磁片區之間的資料流量不會加密。 不過，從 Azure VM (執行 NFS 或 SMB 用戶端) 到 Azure NetApp Files 的流量，就像任何其他 Azure VM 對 VM 流量一樣安全。 這是 Azure 資料中心網路的本機流量。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>存放區是否可以加密？

所有 Azure NetApp Files 磁片區都會使用 FIPS 140-2 標準進行加密。 所有金鑰都是由 Azure NetApp Files 服務所管理。 

### <a name="how-are-encryption-keys-managed"></a>如何管理加密金鑰？ 

Azure NetApp Files 的金鑰管理是由服務處理。 每個磁片區都會產生唯一的 XTS-AES-256 資料加密金鑰。 加密金鑰階層可用來加密和保護所有磁片區金鑰。 這些加密金鑰永遠不會以未加密的格式顯示或報告。 刪除磁片區時，會立即刪除加密金鑰。

客戶管理的金鑰 (攜帶您自己的金鑰) 使用 Azure 專用 HSM 可在美國東部、美國中南部、美國西部2和 US Gov 維吉尼亞州區域中以受控制的基礎提供支援。 您可以在上要求存取權 [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) 。 當容量可供使用時，將會核准要求。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>我可以設定 NFS 匯出原則規則來控制 Azure NetApp Files 服務裝載目標的存取嗎？

是的，您可以在單一 NFS 匯出原則中設定最多五個規則。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files 是否支援網路安全性群組？

否，您目前無法將網路安全性群組套用至 Azure NetApp Files 的委派子網或服務所建立的網路介面。

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>是否可以搭配使用 Azure RBAC 與 Azure NetApp Files？

是，Azure NetApp Files 支援 Azure RBAC 功能。

## <a name="performance-faqs"></a>效能常見問題集

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>我該怎麼做才能優化或調整 Azure NetApp Files 的效能？

您可以根據效能需求採取下列動作： 
- 請確定虛擬機器的大小適當。
- 為 VM 啟用加速網路。
- 選取所需的服務層級和容量集區的大小。
- 為容量和效能建立具有所需配額大小的磁片區。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何? 將 Azure NetApp Files 的以輸送量為基礎的服務層級轉換為 IOPS？

您可以使用下列公式，將 MB/s 轉換成 IOPS：  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何? 變更磁片區的服務層級？

您可以藉由將磁片區移至另一個使用磁片區所需 [服務層級](azure-netapp-files-service-levels.md) 的容量集區，來變更現有磁片區的服務層級。 請參閱 [動態變更磁片區的服務層級](dynamic-change-volume-service-level.md)。 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何? 監視 Azure NetApp Files 的效能嗎？

Azure NetApp Files 提供大量效能度量。 您也可以使用 Azure 監視器來監視 Azure NetApp Files 的使用計量。  如需 Azure NetApp Files 的效能計量清單，請參閱 [Azure Netapp files 的計量](azure-netapp-files-metrics.md) 。

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Nfsv4.1 4.1 上的 Kerberos 對效能有何影響？

請參閱 [nfsv4.1 4.1 上 Kerberos 的效能影響](configure-kerberos-encryption.md#kerberos_performance) ，以取得 nfsv4.1 4.1 的安全性選項、已測試的效能向量，以及預期的效能影響的相關資訊。 

## <a name="nfs-faqs"></a>NFS 常見問題集

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我想要在 Azure VM 啟動或重新開機時自動裝載磁片區。  如何? 針對持續性 NFS 磁片區設定我的主機嗎？

若要在 VM 啟動或重新開機時自動掛接 NFS 磁片區，請在主機上將專案新增至該檔案 `/etc/fstab` 。 

如需詳細資訊，請參閱為 [Windows 或 Linux 虛擬機器掛接或卸載磁片](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 區。  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>為什麼 NFS 用戶端上的 DF 命令不會顯示布建的磁片區大小？

在 DF 中報告的磁片區大小是 Azure NetApp Files 磁片區可成長的大小上限。 在 DF 命令中，Azure NetApp Files 磁片區的大小不會反映磁片區的配額或大小。  您可以透過 Azure 入口網站或 API 取得 Azure NetApp Files 磁片區大小或配額。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files 支援哪些 NFS 版本？

Azure NetApp Files 支援 NFSv3 和 Nfsv4.1 4.1。 您可以使用任何 NFS 版本來 [建立磁片](azure-netapp-files-create-volumes.md) 區。 

### <a name="how-do-i-enable-root-squashing"></a>如何? 啟用根抓？

您可以指定根帳號是否可以存取磁片區，而不是使用磁片區的匯出原則。 如需詳細資訊，請參閱 [設定 NFS 磁片區的匯出原則](azure-netapp-files-configure-export-policy.md) 。

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>我可以針對多個磁片區使用相同的檔案路徑 (磁片區建立權杖) 嗎？

是，您可以。 不過，檔案路徑必須在不同的訂用帳戶或不同的區域中使用。   

例如，您可以建立名為的磁片區 `vol1` 。 然後，您可以在 `vol1` 不同的容量集區中建立另一個磁片區，但在相同的訂用帳戶和區域中呼叫。 在此情況下，使用相同的磁片區名稱 `vol1` 將會造成錯誤。 若要使用相同的檔案路徑，該名稱必須位於不同的區域或訂用帳戶中。

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>當我嘗試透過 Windows 用戶端存取 NFS 磁片區時，為什麼用戶端需要很長的時間來搜尋資料夾和子資料夾？

請確定 `CaseSensitiveLookup` 已在 Windows 用戶端上啟用，以加速資料夾和子資料夾的查閱：

1. 使用下列 PowerShell 命令來啟用 CaseSensitiveLookup：   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. 在 Windows 伺服器上裝載磁片區。   
    範例：   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

## <a name="smb-faqs"></a>SMB 常見問題集

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp Files 支援哪些 SMB 版本？

Azure NetApp Files 支援 SMB 2.1 和 SMB 3.1 (，其中包含 SMB 3.0) 的支援。    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 存取需要 Active Directory 連接嗎？ 

是的，您必須先建立 Active Directory 連接，然後再部署 SMB 磁片區。 指定的網域控制站必須可供 Azure NetApp Files 的委派子網存取，才能成功連線。  請參閱 [建立 SMB 磁片](./azure-netapp-files-create-volumes-smb.md) 區以取得詳細資料。 

### <a name="how-many-active-directory-connections-are-supported"></a>支援的 Active Directory 連接數目有多少？

即使 AD 連線位於不同的 NetApp 帳戶，Azure NetApp Files 也不支援在單一 *區域* 中使用多個 ACTIVE DIRECTORY (ad) 連接。 不過，您可以在單一 *訂* 用帳戶中擁有多個 ad 連線，只要 ad 連線位於不同的區域即可。 如果您在單一區域中需要多個 AD 連接，您可以使用個別的訂用帳戶來執行這項作業。 

AD 連線是針對每個 NetApp 帳戶設定;只有透過建立 AD 連線的 NetApp 帳戶才能看到 AD 連接。

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files 是否支援 Azure Active Directory？ 

[Azure Active Directory (AD) 網域服務](../active-directory-domain-services/overview.md)和[Active Directory Domain Services (](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) AD DS) 都受到支援。 您可以使用現有的 Active Directory 網域控制站搭配 Azure NetApp Files。 網域控制站可以位於 Azure 中，作為虛擬機器，或透過 ExpressRoute 或 S2S VPN 在內部部署。 Azure NetApp Files 目前並不支援 [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) 的 AD join。

如果您將 Azure NetApp Files 與 Azure Active Directory Domain Services 搭配使用，當您為 NetApp 帳戶設定 Active Directory 時，組織單位路徑是 `OU=AADDC Computers`。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>支援哪些版本的 Windows Server Active Directory？

Azure NetApp Files 支援 Active Directory Domain Services 版的 Windows Server 2008r2SP1 2019 版。

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>為什麼 SMB 用戶端上的可用空間不會顯示布建的大小？

SMB 用戶端所報告的磁片區大小是 Azure NetApp Files 磁片區可成長的大小上限。 SMB 用戶端上所顯示的 Azure NetApp Files 磁片區大小，並不會反映磁片區的配額或大小。 您可以透過 Azure 入口網站或 API 取得 Azure NetApp Files 磁片區大小或配額。

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>我連接到 SMB 共用時發生問題。 我該怎麼辦？

最佳做法是將電腦時鐘同步處理的最大容錯設定為五分鐘。 如需詳細資訊，請參閱 [電腦時鐘同步處理的最大容錯](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11))。 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>容量管理常見問題

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何? 監視容量集區和 Azure NetApp Files 數量的使用量？ 

Azure NetApp Files 提供容量集區和磁片區使用計量。 您也可以使用 Azure 監視器來監視 Azure NetApp Files 的使用量。 如需詳細資料，請參閱 [Azure NetApp Files 的計量](azure-netapp-files-metrics.md) 。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>我可以透過 Azure 儲存體總管管理 Azure NetApp Files 嗎？

不會。 Azure 儲存體總管不支援 Azure NetApp Files。

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>如何? 判斷目錄是否接近限制大小？

您可以使用 `stat` 來自用戶端的命令，查看目錄是否接近目錄中繼資料的大小上限 (320 MB) 。

若為 320 MB 目錄，區塊數目為655360，每個區塊的大小為512個位元組。   (，也就是 320x1024x1024/512. )   

範例：

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>資料移轉和保護常見問題

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何? 將資料移轉至 Azure NetApp Files？
Azure NetApp Files 提供 NFS 和 SMB 磁片區。  您可以使用任何以檔案為基礎的複製工具，將資料移轉至服務。 

NetApp 提供以 SaaS 為基礎的解決方案，也就是 [Netapp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)。 此解決方案可讓您將 NFS 或 SMB 資料複寫至 Azure NetApp Files NFS 匯出或 SMB 共用。 

您也可以使用一系列的免費工具來複製資料。 針對 NFS，您可以使用工作負載工具（例如 [rsync](https://rsync.samba.org/examples.html) ），將來源資料複製並同步處理到 Azure NetApp Files 磁片區中。 針對 SMB，您可以使用相同的方式來使用工作負載 [robocopy](/windows-server/administration/windows-commands/robocopy) 。  這些工具也可以複製檔案或資料夾的許可權。 

從內部部署資料移轉至 Azure NetApp Files 的需求如下： 

- 確定 Azure NetApp Files 可在目標 Azure 區域中使用。
- 驗證來源與 Azure NetApp Files 目標磁片區 IP 位址之間的網路連線能力。 ExpressRoute 不支援內部部署與 Azure NetApp Files 服務之間的資料傳輸。
- 建立目標 Azure NetApp Files 磁片區。
- 使用您慣用的檔案複製工具，將來源資料傳輸至目標磁片區。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何? 在另一個 Azure 區域中建立 Azure NetApp Files 磁片區的複本嗎？
    
Azure NetApp Files 提供 NFS 和 SMB 磁片區。  任何以檔案為基礎的複製工具都可用來在 Azure 區域之間複寫資料。 

NetApp 提供以 SaaS 為基礎的解決方案，也就是 [Netapp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)。 此解決方案可讓您將 NFS 或 SMB 資料複寫至 Azure NetApp Files NFS 匯出或 SMB 共用。 

您也可以使用一系列的免費工具來複製資料。 針對 NFS，您可以使用工作負載工具（例如 [rsync](https://rsync.samba.org/examples.html) ），將來源資料複製並同步處理到 Azure NetApp Files 磁片區中。 針對 SMB，您可以使用相同的方式來使用工作負載 [robocopy](/windows-server/administration/windows-commands/robocopy) 。  這些工具也可以複製檔案或資料夾的許可權。 

將 Azure NetApp Files 磁片區複製到另一個 Azure 區域的需求如下： 
- 確定 Azure NetApp Files 可在目標 Azure 區域中使用。
- 在每個區域中的 Vnet 之間驗證網路連線能力。 目前不支援 Vnet 之間的全域對等互連。  您可以使用 ExpressRoute 線路連結或使用 S2S VPN 連接，在 Vnet 之間建立連線。 
- 建立目標 Azure NetApp Files 磁片區。
- 使用您慣用的檔案複製工具，將來源資料傳輸至目標磁片區。

### <a name="is-migration-with-azure-data-box-supported"></a>是否支援遷移 Azure 資料箱？

不會。 Azure 資料箱目前不支援 Azure NetApp Files。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是否支援使用 Azure 匯入/匯出服務進行遷移？

不會。 Azure 匯入/匯出服務目前不支援 Azure NetApp Files。

## <a name="next-steps"></a>後續步驟  

- [Microsoft Azure ExpressRoute 常見問題](../expressroute/expressroute-faqs.md)
- [Microsoft Azure 虛擬網路常見問題](../virtual-network/virtual-networks-faq.md)
- [如何建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure 資料箱](../databox/index.yml)
- [Azure NetApp Files 的 SMB 效能常見問題](azure-netapp-files-smb-performance.md)
