---
title: 關於 Azure NetApp 檔的常見問題解答 |微軟文件
description: 回答有關 Azure NetApp 檔常見問題的解答。
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
ms.date: 04/01/2020
ms.author: b-juche
ms.openlocfilehash: 59453dbb9617f5a2ddbdca08709747b11a688560
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547091"
---
# <a name="faqs-about-azure-netapp-files"></a>關於 Azure NetApp 檔案的常見問題解答

本文回答了有關 Azure NetApp 檔的常見問題 (FAQ)。 

## <a name="networking-faqs"></a>網路常見問題解答

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 資料路徑是否通過 Internet?  

否。 NFS 資料路徑不會通過 Internet。 Azure NetApp 檔案是一種 Azure 本機服務,可部署到該服務可用的 Azure 虛擬網路 (VNet) 中。 Azure NetApp 檔案使用委派的子網,並直接在 VNet 上提供網路介面。 

有關詳細資訊[,請參閱 Azure NetApp 檔案網路規劃指南](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>是否可以將已建立的 VNet 連接到 Azure NetApp 檔案服務?

可以,您可以將創建的 VNet 連接到服務。 

有關詳細資訊[,請參閱 Azure NetApp 檔案網路規劃指南](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>我能否使用 DNS FQDN 名稱裝載 Azure NetApp 檔的 NFS 卷?

是的,如果創建所需的 DNS 條目,則可以。 Azure NetApp 檔為預配卷提供服務 IP。 

> [!NOTE] 
> Azure NetApp 檔案可以根據需要為服務部署其他 IP。  可能需要定期更新 DNS 條目。

## <a name="security-faqs"></a>安全性常見問題集

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>是否可以加密 Azure VM 和儲存之間的網路流量?

數據流量(來自 NFSv3、NFSv4.1 或 SMBv3 用戶端到 Azure NetApp 檔卷的流量)未加密。 但是,從 Azure VM(運行 NFS 或 SMB 用戶端)到 Azure NetApp 檔的流量與任何其他 Azure VM 到 VM 的流量一樣安全。 此流量是 Azure 資料中心網路的本地流量。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>存儲是否可以在靜態時加密?

所有 Azure NetApp 檔卷都使用 FIPS 140-2 標準進行加密。 所有密鑰都由 Azure NetApp 檔案服務管理。 

### <a name="how-are-encryption-keys-managed"></a>如何管理加密金鑰? 

Azure NetApp 檔的關鍵管理由服務處理。 為每個卷生成唯一的 XTS-AES-256 數據加密密鑰。 加密金鑰層次結構用於加密和保護所有卷密鑰。 這些加密金鑰永遠不會以未加密的格式顯示或報告。 刪除卷時,將立即刪除加密密鑰。

在美國東部、美國西部 2 和美國中南部區域,使用 Azure 專用 HSM 對使用者管理密鑰(自帶密鑰)的支援在受控基礎上提供。  您可以在 請求訪問**anffeedback@microsoft.com**。 當容量可用時,請求將被批准。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>是否可以配置 NFS 匯出策略規則來控制對 Azure NetApp 檔案服務載入目標的存取?


可以,您可以在單個 NFS 匯出策略中配置最多五個規則。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp 檔是否支援網路安全組?

否,目前無法將網路安全組應用於 Azure NetApp 檔的授權子網或服務創建的網路介面。

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>是否可以將 Azure IAM 與 Azure NetApp 檔案一起使用?

是的,Azure NetApp 檔支援具有 Azure IAM 的 RBAC 功能。

## <a name="performance-faqs"></a>效能常見問題解答

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>應該如何優化或調整 Azure NetApp 檔案性能?

您可以根據效能要求執行以下操作: 
- 確保虛擬機的大小正確。
- 為 VM 啟用加速網路。
- 為容量池選擇所需的服務級別和大小。
- 創建具有所需配額大小的容量和性能的卷。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何將 Azure NetApp 檔案的基於輸送量的服務層級轉換為 IOPS?

您可以使用以下公式將 MB/s 轉換為 IOPS:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何更改卷的服務級別?

當前不支援更改卷的服務級別。

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何監視 Azure NetApp 檔案性能?

Azure NetApp 檔案提供卷性能指標。 您還可以使用 Azure 監視器監視 Azure NetApp 檔案的使用指標。  有關 Azure NetApp 檔案的效能指標清單,請參閱[Azure NetApp 檔案的指標](azure-netapp-files-metrics.md)。

## <a name="nfs-faqs"></a>NFS 常見問題解答

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我希望在啟動或重新啟動 Azure VM 時自動裝載卷。  如何為持久性 NFS 卷配置主機?

要在 VM 啟動或重新啟動時自動載入 NFS 卷,`/etc/fstab`請將條目 添加到主機上的檔。 

有關詳細資訊[,請參閱載入或卸載 Windows 或 Linux 虛擬機器的卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>為什麼 NFS 用戶端上的 DF 命令不顯示預配的卷大小?

DF 中報告的卷大小是 Azure NetApp 檔卷可以增長到的最大大小。 DF 命令中的 Azure NetApp 檔卷的大小不反映卷的配額或大小。  您可以通過 Azure 門戶或 API 獲取 Azure NetApp 檔卷大小或配額。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 檔案支援哪些 NFS 版本?

Azure NetApp 檔案支援 NFSv3 和 NFSv4.1。 您可以使用任 NFS 版本[建立卷](azure-netapp-files-create-volumes.md)。 

### <a name="how-do-i-enable-root-squashing"></a>如何啟用根擠壓?

根擠壓當前不受支援。

## <a name="smb-faqs"></a>中小企業常見問題解答

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 存取是否需要活動目錄連接? 

是的,在部署 SMB 卷之前,必須創建活動目錄連接。 指定的網域控制器必須由 Azure NetApp 檔的委派子網訪問,才能成功連接。  有關詳細資訊[,請參閱建立 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)。 

### <a name="how-many-active-directory-connections-are-supported"></a>支援多少個活動目錄連接?

Azure NetApp 檔案不支援單個*區域*中的多個活動目錄 (AD) 連接,即使 AD 連接位於不同的 NetApp 帳戶中也是如此。 但是,只要 AD 連接位於不同的區域,就可以在單個*訂閱*中具有多個 AD 連接。 如果單個區域中需要多個 AD 連接,則可以使用單獨的訂閱來執行此操作。 

每個 NetApp 帳戶配置 AD 連接;AD 連接僅透過其創建的 NetApp 帳戶可見。

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 檔是否支援 Azure 活動目錄? 

支援[Azure 活動目錄 (AD) 網域服務和](https://docs.microsoft.com/azure/active-directory-domain-services/overview)[活動目錄域服務 (AD DS)。](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 您可以將現有的活動目錄域控制器與 Azure NetApp 檔案一起使用。 域控制器可以作為虛擬機駐留在 Azure 中,也可以通過 ExpressRoute 或 S2S VPN駐留在內部。 Azure NetApp 檔案目前不支援[Azure 活動目錄](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)的 AD 聯接。

如果將 Azure NetApp 檔與 Azure 活動目錄域服務`OU=AADDC Computers`一起使用,則組織單位路徑是為 NetApp 帳戶配置活動目錄時。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>支援哪些版本的 Windows 伺服器活動目錄?

Azure NetApp 檔案支援 Windows 伺服器 2008r2SP1-2019 版本的活動目錄域服務。

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>為什麼我的 SMB 用戶端上的可用空間不顯示預配大小?

SMB 客戶端報告的卷大小是 Azure NetApp 檔案卷可以增長到的最大大小。 SMB 客戶端上顯示的 Azure NetApp 檔卷的大小不反映卷的配額或大小。 您可以通過 Azure 門戶或 API 獲取 Azure NetApp 檔卷大小或配額。

### <a name="does-azure-netapp-files-support-kerberos-encryption"></a>Azure NetApp 檔案是否支援 Kerberos 加密?

是的,默認情況下,Azure NetApp 檔案支援 AES-128 和 AES-256 加密,用於服務和目標活動目錄域控制器之間的流量。 有關要求[,請參閱為 Azure NetApp 檔案建立 SMB 卷](azure-netapp-files-create-volumes-smb.md)。 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>容量管理常見問題解答

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何監視容量池和 Azure NetApp 檔案的容量使用方式? 

Azure NetApp 檔案提供容量池和卷使用方式指標。 您還可以使用 Azure 監視器監視 Azure NetApp 檔案的使用方式。 有關詳細資訊[,請參閱 Azure NetApp 檔案的指標](azure-netapp-files-metrics.md)。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>我能否通過 Azure 儲存資源管理員管理 Azure NetApp 檔?

否。 Azure 儲存資源管理員不支援 Azure NetApp 檔案。

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>如何確定目錄是否接近限制大小?

您可以使用用戶端的命令`stat`查看目錄是否接近最大大小限制 (320 MB)。

對於 320 MB 目錄,塊數為 655360,每個塊大小為 512 位元組。  (即 320x1024x1024/512。  

範例：

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>資料移轉和保護常見問題解答

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何將資料遷移到 Azure NetApp 檔?
Azure NetApp 檔案提供 NFS 和 SMB 卷。  您可以使用任何基於檔的複本工具將資料移到服務。 

NetApp 提供基於 SaaS 的解決方案[,NetApp 雲端同步](https://cloud.netapp.com/cloud-sync-service)。 該解決方案使您能夠將 NFS 或 SMB 資料複製到 Azure NetApp 檔案 NFS 匯出或 SMB 共用。 

您還可以使用各種免費工具來複製數據。 對於 NFS,可以使用工作負荷工具(如[rsync)](https://rsync.samba.org/examples.html)將來源資料複製並同步到 Azure NetApp 檔卷中。 對於 SMB,您可以以同樣的方式使用工作負載[機器人。](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)  這些工具還可以複製檔或資料夾許可權。 

資料從本地遷移到 Azure NetApp 檔案的要求如下: 

- 確保 Azure NetApp 檔在目標 Azure 區域中可用。
- 驗證來源和 Azure NetApp 檔案目標卷 IP 位址之間的網路連接。 通過 ExpressRoute 支援本地和 Azure NetApp 檔案服務之間的數據傳輸。
- 創建目標 Azure NetApp 檔卷。
- 使用首選檔案複製工具將源資料傳輸到目標卷。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何在另一個 Azure 區域中創建 Azure NetApp 檔卷的複本?
    
Azure NetApp 檔案提供 NFS 和 SMB 卷。  任何基於檔的副本工具都可用於在 Azure 區域之間複製數據。 

NetApp 提供基於 SaaS 的解決方案[,NetApp 雲端同步](https://cloud.netapp.com/cloud-sync-service)。 該解決方案使您能夠將 NFS 或 SMB 資料複製到 Azure NetApp 檔案 NFS 匯出或 SMB 共用。 

您還可以使用各種免費工具來複製數據。 對於 NFS,可以使用工作負荷工具(如[rsync)](https://rsync.samba.org/examples.html)將來源資料複製並同步到 Azure NetApp 檔卷中。 對於 SMB,您可以以同樣的方式使用工作負載[機器人。](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)  這些工具還可以複製檔或資料夾許可權。 

將 Azure NetApp 檔案卷複製到其他 Azure 區域的要求如下: 
- 確保 Azure NetApp 檔在目標 Azure 區域中可用。
- 驗證每個區域中的 VNet 之間的網路連接。 目前,不支援 VNet 之間的全域對等互連。  您可以通過與 ExpressRoute 電路連結或使用 S2S VPN 連接來建立 VNet 之間的連接。 
- 創建目標 Azure NetApp 檔卷。
- 使用首選檔案複製工具將源資料傳輸到目標卷。

### <a name="is-migration-with-azure-data-box-supported"></a>是否支援使用 Azure 資料框進行遷移?

否。 Azure 資料框目前不支援 Azure NetApp 檔案。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是否支援 Azure 導入/匯出服務的遷移?

否。 Azure 匯入/匯出服務目前不支援 Azure NetApp 檔案。

## <a name="next-steps"></a>後續步驟  

- [微軟 Azure 快速路由常見問題解答](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [微軟 Azure 虛擬網路常見問題解答](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure 資料箱](https://docs.microsoft.com/azure/databox-family/)
- [有關 Azure NetApp 檔案的 SMB 性能的常見問題解答](azure-netapp-files-smb-performance.md)
