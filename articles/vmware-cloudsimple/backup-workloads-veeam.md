---
title: Azure VMware Solution by CloudSimple-使用 Veeam 備份私人雲端上的工作負載虛擬機器
description: 說明如何使用 Veeam B&R 9.5 來備份在以 Azure 為基礎的 CloudSimple 私人雲端中執行的虛擬機器。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be606b3e23a594e67acf3f169d88353403d8577
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899332"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>使用 Veeam B&R 備份 CloudSimple 私人雲端上的工作負載 Vm

本指南說明如何使用 Veeam B&R 9.5 來備份在以 Azure 為基礎的 CloudSimple 私人雲端中執行的虛擬機器。

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>關於 Veeam 備份和復原解決方案

Veeam 解決方案包含下列元件。

**備份伺服器**

備份伺服器是 Windows server (VM) ，可作為 Veeam 的控制中心，並執行這些功能： 

* 協調備份、複寫、修復驗證和還原工作
* 控制工作排程和資源配置
* 可讓您設定和管理備份基礎結構元件，並指定備份基礎結構的全域設定

**Proxy 伺服器**

Proxy 伺服器會安裝在備份伺服器與備份基礎結構的其他元件之間。 他們會管理下列功能：

* 從生產儲存體抓取 VM 資料
* 壓縮
* 重複資料刪除
* 加密
* 將資料傳輸至備份儲存機制

**備份儲存機制**

備份儲存機制是 Veeam 保留複寫 Vm 的備份檔案、VM 複本和中繼資料的儲存位置。  存放庫可以是具有本機磁片的 Windows 或 Linux 伺服器 (或裝載的 NFS/SMB) 或硬體儲存體重復資料刪除設備。

### <a name="veeam-deployment-scenarios"></a>Veeam 部署案例
您可以利用 Azure 來提供備份儲存機制和長期備份和封存的儲存體目標。 私人雲端中的 Vm 與 Azure 中的備份儲存機制之間的所有備份網路流量，都會透過高頻寬、低延遲連結來傳輸。 跨區域的複寫流量會透過內部 Azure 後擋板網路傳送，進而降低使用者的頻寬成本。

**基本部署**

針對少於 30 TB 的環境進行備份，CloudSimple 建議下列設定：

* 在私人雲端中的相同 VM 上安裝 Veeam 備份伺服器和 proxy 伺服器。
* Azure 中以 Linux 為主的主要備份存放庫，設定為備份作業的目標。
* `azcopy` 用來將資料從主要備份儲存機制複製到已複寫至另一個區域的 Azure blob 容器。

![顯示基本 Veeam 部署案例的圖表。](media/veeam-basicdeployment.png)

**進階部署**

針對超過 30 TB 的環境進行備份，CloudSimple 建議下列設定：

* 針對 vSAN 叢集中的每個節點一部 proxy 伺服器，如 Veeam 所建議。
* 私人雲端中以 Windows 為基礎的主要備份儲存機制，可快取五天的資料以供快速還原。
* Azure 中的 Linux 備份儲存機制，作為長期保留期間備份複製作業的目標。 此存放庫應設定為相應放大備份存放庫。
* `azcopy` 用來將資料從主要備份儲存機制複製到已複寫至另一個區域的 Azure blob 容器。

![基本部署案例](media/veeam-advanceddeployment.png)

在上圖中，請注意備份 proxy 是具有 vSAN 資料存放區之工作負載 VM 磁片的熱新增存取權的 VM。 Veeam 會針對 vSAN 使用虛擬裝置備份 proxy 傳輸模式。

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>CloudSimple 上的 Veeam 解決方案需求

Veeam 解決方案會要求您執行下列作業：

* 提供您自己的 Veeam 授權。
* 部署及管理 Veeam，以備份在 CloudSimple 私用雲端中執行的工作負載。

此解決方案可讓您完整控制 Veeam 備份工具，並提供使用原生 Veeam 介面或 Veeam vCenter 外掛程式來管理 VM 備份作業的選項。

如果您是現有的 Veeam 使用者，可以略過 Veeam 方案元件的章節，並直接繼續進行 [Veeam 部署案例](#veeam-deployment-scenarios)。

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>在 CloudSimple 私用雲端中安裝及設定 Veeam 備份

下列各節說明如何安裝和設定 CloudSimple 私用雲端的 Veeam 備份解決方案。

部署程式是由下列步驟所組成：

1. [vCenter UI：在您的私人雲端中設定基礎結構服務](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple 入口網站：設定 Veeam 的私人雲端網路](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple 入口網站：提升許可權](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure 入口網站：將您的虛擬網路連線到私人雲端](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure 入口網站：在 Azure 中建立備份儲存機制](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure 入口網站：設定長期資料保留的 Azure blob 儲存體](#configure-azure-blob-storage-for-long-term-data-retention)
7. [私人雲端的 vCenter UI：安裝 Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam 主控台：設定 Veeam 備份 & 復原軟體](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple 入口網站：設定 Veeam 存取和取消提升許可權](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>開始之前

開始 Veeam 部署之前，需要下列各項：

* 您所擁有的 Azure 訂用帳戶
* 預先建立的 Azure 資源群組
* 您訂用帳戶中的 Azure 虛擬網路
* Azure 儲存體帳戶
* 使用 CloudSimple 入口網站建立的 [私人雲端](create-private-cloud.md) 。  

在執行階段中，需要下列專案：

* 適用于 Windows 的 VMware 範本，可安裝 Veeam (例如 Windows Server 2012 R2-64 位映射) 
* 為備份網路識別的一個可用 VLAN
* 要指派給備份網路的子網 CIDR
* Veeam 9.5 u3 可安裝的媒體 (ISO) 上傳至私人雲端的 vSAN 資料存放區

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI：在您的私人雲端中設定基礎結構服務

在私用雲端中設定基礎結構服務，讓您輕鬆地管理您的工作負載和工具。

* 如果有下列任何一種情況，您可以如 [設定 vCenter 身分識別來源以使用 Active Directory](set-vcenter-identity.md) 所述，新增外部識別提供者：

  * 您想要識別您的內部部署 Active Directory 私人雲端中 (AD) 的使用者。
  * 您想要在私用雲端中為所有使用者設定 AD。
  * 您想要使用 Azure AD。
* 若要針對私人雲端中的工作負載提供 IP 位址查閱、IP 位址管理和名稱解析服務，請依照在 [CloudSimple 私人雲端中設定 DNS 和 DHCP 應用程式和工作負載中](dns-dhcp-setup.md)所述，設定 DHCP 和 DNS 伺服器。

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple 私人雲端：設定 Veeam 的私人雲端網路

存取 CloudSimple 入口網站，以設定 Veeam 解決方案的私用雲端網路。

為備份網路建立 VLAN，並為它指派子網 CIDR。 如需相關指示，請參閱 [建立和管理 vlan/子網](create-vlan-subnet.md)。

建立管理子網和備份網路之間的防火牆規則，以允許 Veeam 所使用的埠上的網路流量。 請參閱 Veeam 主題 [使用的埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)。 如需建立防火牆規則的指示，請參閱 [設定防火牆資料表和規則](firewall.md)。

下表提供埠清單。

| 圖示 | 描述 | 圖示 | 描述 |
| ------------ | ------------- | ------------ | ------------- |
| 備份伺服器  | vCenter  | HTTPS/TCP  | 443 |
| 備份伺服器 <br> *部署 Veeam 備份所需 & 複寫元件* | 備份 Proxy  | TCP/UDP  | 135、137到139和445 |
    | 備份伺服器   | DNS  | UDP  | 53  | 
    | 備份伺服器   | Veeam 更新通知伺服器  | TCP  | 80  | 
    | 備份伺服器   | Veeam 授權補救伺服器  | TCP  | 443  | 
    | 備份 Proxy   | vCenter |   |   | 
    | 備份 Proxy  | Linux 備份儲存機制   | TCP  | 22  | 
    | 備份 Proxy  | Windows 備份存放庫  | TCP  | 49152-65535   | 
    | 備份儲存機制  | 備份 Proxy  | TCP  | 2500-5000  | 
    | 來源備份儲存機制<br> *用於備份複製作業*  | 目標備份儲存機制  | TCP  | 2500-5000  | 

依照 [設定防火牆資料表和規則](firewall.md)中所述，在工作負載子網和備份網路之間建立防火牆規則。  若要進行應用程式感知備份和還原，您必須在裝載特定應用程式的工作負載 Vm 上開啟 [額外的埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) 。

根據預設，CloudSimple 會提供 1Gbps ExpressRoute 連結。 針對較大的環境大小，可能需要較高的頻寬連結。 如需更高頻寬連結的詳細資訊，請聯絡 Azure 支援。

若要繼續設定，您需要授權金鑰和對等電路 URI 以及 Azure 訂用帳戶的存取權。  您可以在 CloudSimple 入口網站的 [虛擬網路連線] 頁面上找到這項資訊。 如需相關指示，請參閱 [取得 Azure 虛擬網路對 CloudSimple 連線的對等互連資訊](virtual-network-connection.md)。 如果您在取得資訊時遇到任何問題，請 [聯絡支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)人員。

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple 私人雲端：提升 cloudowner 的許可權

預設的 ' cloudowner ' 使用者在私用雲端 vCenter 中沒有足夠的許可權可安裝 VEEAM，因此必須提升使用者的 vCenter 許可權。 如需詳細資訊，請參閱 [提升許可權](escalate-private-cloud-privileges.md)。

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure 入口網站：將您的虛擬網路連線到私人雲端

遵循 [使用 ExpressRoute 的 Azure 虛擬網路](azure-expressroute-connection.md)連線中的指示，將您的虛擬網路連線到私人雲端。

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure 入口網站：建立備份存放庫 VM

1. 建立具有 (2 個 vcpu 和 8 GB 記憶體) 的標準 D2 v3 VM。
2. 選取以 CentOS 7.4 為基礎的映射。
3. 為 VM 設定 (NSG) 的網路安全性群組。 確認 VM 沒有公用 IP 位址，且無法從公用網際網路連線。
4. 建立新 VM 的使用者名稱和密碼型使用者帳戶。 如需相關指示，請參閱 [Azure 入口網站中的建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)。
5. 建立 1x512 GiB 標準 HDD，並將其連結至存放庫 VM。  如需相關指示，請參閱 [Azure 入口網站中的如何將受控資料磁片連結至 WINDOWS VM](../virtual-machines/windows/attach-managed-disk-portal.md)。
6. [在受控磁片上建立 XFS 磁片](https://www.digitalocean.com/docs/volumes/how-to/)區。 使用先前提及的認證來登入 VM。 執行下列腳本來建立邏輯磁片區、將磁片新增至該磁片區、建立 XFS filesystem [磁碟分割](https://www.digitalocean.com/docs/volumes/how-to/partition/) ，然後在/backup1 路徑下 [掛接](https://www.digitalocean.com/docs/volumes/how-to/mount/) 磁碟分割。

    範例指令碼：

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. 將/backup1 公開為在私人雲端中執行之 Veeam 備份伺服器的 NFS 掛接點。 如需相關指示，請參閱數位海洋文章 [如何設定 CentOS 6 上的 NFS 掛接](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6)。 當您在 Veeam 備份伺服器中設定備份儲存機制時，請使用此 NFS 共用名稱。

8. 設定 NSG 中的篩選規則，讓備份存放庫 VM 明確地允許進出 VM 的所有網路流量。

> [!NOTE]
> Veeam Backup & 複寫會使用 SSH 通訊協定與 Linux 備份儲存機制進行通訊，而且需要 Linux 存放庫上的 SCP 公用程式。 確認 SSH 背景程式已正確設定，且 SCP 可在 Linux 主機上使用。

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>設定長期資料保留的 Azure blob 儲存體

1. 建立一般用途的儲存體帳戶 (GPv2 標準類型的) 和 blob 容器，如 Microsoft 影片 [消費者入門的 Azure 儲存體](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)所述。
2. 建立 Azure 儲存體容器，如 [建立容器](/rest/api/storageservices/create-container) 參考中所述。
2. `azcopy`從 Microsoft 下載適用于 Linux 的命令列公用程式。 您可以在 CentOS 7.5 的 bash shell 中使用下列命令。

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. 使用命令，將 `azcopy` 備份檔案複製到 blob 容器或從中複製。  請參閱 [使用 AzCopy On Linux 傳送資料](../storage/common/storage-use-azcopy-v10.md) 以取得詳細命令。

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>私人雲端的 vCenter 主控台：安裝 Veeam B&R

從您的私人雲端存取 vCenter 以建立 Veeam 服務帳戶、安裝 Veeam B&R 9.5，以及使用服務帳戶設定 Veeam。

1. 建立名為「Veeam 備份角色」的新角色，並依 Veeam 的建議指派必要的許可權。 如需詳細資訊，請參閱 Veeam 主題的 [必要許可權](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)。
2. 在 vCenter 中建立新的「Veeam 使用者群組」群組，並為其指派「Veeam 備份角色」。
3. 建立新的「Veeam 服務帳戶」使用者，並將它新增至「Veeam 使用者群組」。

    ![建立 Veeam 服務帳戶](media/veeam-vcenter01.png)

4. 使用備份網路 VLAN 在 vCenter 中建立分散式通訊埠群組。 如需詳細資訊，請參閱在 [VSphere Web 用戶端中建立分散式埠群組](https://www.youtube.com/watch?v=wpCd5ZbPOpA)的 VMware 影片。
5. 依據 [Veeam 系統需求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)，為 vCenter 中的 Veeam 備份和 proxy 伺服器建立 vm。 您可以使用 Windows 2012 R2 或 Linux。 如需詳細資訊，請參閱 [使用 Linux 備份存放庫的需求](https://www.veeam.com/kb2216)。
6. 將可安裝的 Veeam ISO 掛接為 Veeam 備份伺服器 VM 中的 CDROM 裝置。
7. 使用 RDP 會話連線到 Windows 2012 R2 電腦 (Veeam 安裝) 的目標，在 Windows 2012 R2 VM 中 [安裝 Veeam B&R 9.5 u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) 。
8. 尋找 Veeam 備份伺服器 VM 的內部 IP 位址，並將 IP 位址設定為 DHCP 伺服器中的靜態 IP 位址。 完成此作業所需的確切步驟取決於 DHCP 伺服器。 例如，Netgate 文章 <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">靜態 DHCP</a> 對應說明如何使用 pfSense 路由器設定 DHCP 伺服器。

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam 主控台：安裝 Veeam 備份和修復軟體

使用 Veeam 主控台設定 Veeam 備份和修復軟體。 如需詳細資訊，請參閱 [Veeam Backup & Replication v9-安裝和部署](https://www.youtube.com/watch?v=b4BqC_WXARk)。

1. 將 VMware vSphere 新增為受管理的伺服器環境。 出現提示時，請提供您在私人雲端的 VCenter 主控台開頭建立之 Veeam 服務帳戶的認證 [：安裝 Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)。

    * 使用負載控制和預設 advanced settings 的預設設定。
    * 將掛接伺服器位置設定為備份伺服器。
    * 將 Veeam 伺服器的設定備份位置變更為遠端存放庫。

2. 在 Azure 中新增 Linux 伺服器作為備份存放庫。

    * 針對 [載入控制] 和 [advanced] 設定使用預設設定。 
    * 將掛接伺服器位置設定為備份伺服器。
    * 將 Veeam 伺服器的設定備份位置變更為遠端存放庫。

3. 使用 **Home> 設定備份設定** 來啟用設定備份的加密。

4. 新增 Windows server VM 作為 VMware 環境的 proxy 伺服器。 針對 proxy 使用「流量規則」，透過網路加密備份資料。

5. 設定備份作業。
    * 若要設定備份作業，請遵循 [建立備份工作](https://www.youtube.com/watch?v=YHxcUFEss4M)中的指示。
    * 在 [ **Advanced Settings > Storage**] 下啟用備份檔案的加密。

6. 設定備份複製作業。

    * 若要設定備份複製作業，請遵循影片中 [建立備份複製作業](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)的指示。
    * 在 [ **Advanced Settings > Storage**] 下啟用備份檔案的加密。

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple 入口網站：設定 Veeam 存取和取消提升許可權
建立 Veeam 備份和復原伺服器的公用 IP 位址。 如需相關指示，請參閱 [配置公用 IP 位址](public-ips.md)。

使用來建立防火牆規則，以允許 Veeam 備份伺服器建立 Veeam 網站的輸出連線，以在 TCP 埠80上下載更新/修補程式。 如需相關指示，請參閱 [設定防火牆資料表和規則](firewall.md)。

若要取消提升許可權，請參閱 [取消提升許可權](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="references"></a>參考資料

### <a name="cloudsimple-references"></a>CloudSimple 參考

* [建立私人雲端](create-private-cloud.md)
* [建立及管理 Vlan/子網](create-vlan-subnet.md)
* [vCenter 身分識別來源](set-vcenter-identity.md)
* [工作負載 DNS 和 DHCP 設定](dns-dhcp-setup.md)
* [提升權限](escalate-privileges.md)
* [設定防火牆資料表和規則](firewall.md)
* [私用雲端許可權](learn-private-cloud-permissions.md)
* [配置公用 IP 位址](public-ips.md)

### <a name="veeam-references"></a>Veeam 參考

* [使用的埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [必要權限](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [系統需求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [& 複寫安裝 Veeam 備份](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [適用于 Linux 的多重 OS FLR 和儲存機制支援所需的模組和許可權](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication v9-安裝與部署-影片](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [建立備份作業的 Veeam v9-影片](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 建立備份複製作業-影片](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure 參考

* [使用 Azure 入口網站為 ExpressRoute 設定虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [將 VNet 連線到線路 - 不同訂用帳戶](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [在 Azure 入口網站中建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)
* [如何將受控資料磁碟連結至 Azure 入口網站中的 Windows VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [消費者入門與 Azure 儲存體影片](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [建立容器](/rest/api/storageservices/create-container)
* [使用 AzCopy on Linux 傳送資料](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>VMware 參考

* [在 vSphere Web 用戶端中建立分散式埠群組-影片](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>其他參考資料

* [在受控磁片上建立 XFS 磁片區-RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [如何在 CentOS 7 上設定 NFS 掛接-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [設定 DHCP 伺服器-Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
