---
title: Azure VMware 解決方案（AVS）-使用 Veeam 備份 AVS 私人雲端上的工作負載虛擬機器
description: 說明如何使用 Veeam B 來備份在 Azure 型 AVS 私人雲端中執行的虛擬機器 & R 9。5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d8dc822ec07bdf061121b97384d0e2f9f239d6e2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025124"
---
# <a name="back-up-workload-vms-on-avs-private-cloud-using-veeam-br"></a>使用 Veeam B & R 備份在 AVS 私人雲端上的工作負載 Vm

本指南說明如何使用 Veeam B & R 9.5，來備份在 Azure 型 AVS 私人雲端中執行的虛擬機器。

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>關於 Veeam 備份和復原解決方案

Veeam 解決方案包含下列元件。

**備份伺服器**

備份伺服器是一種 Windows server （VM），可做為 Veeam 的控制中心，並執行下列功能： 

* 協調備份、複寫、復原驗證和還原工作
* 控制作業排程和資源配置
* 可讓您設定和管理備份基礎結構元件，並指定備份基礎結構的全域設定

**Proxy 伺服器**

Proxy 伺服器會安裝在備份伺服器和備份基礎結構的其他元件之間。 他們會管理下列功能：

* 從生產儲存體抓取 VM 資料
* 壓縮
* 重複資料刪除
* 加密
* 將資料傳輸至備份存放庫

**備份存放庫**

備份存放庫是儲存位置，其中 Veeam 會保留複寫 Vm 的備份檔案、VM 複本和中繼資料。 存放庫可以是具有本機磁片（或掛接的 NFS/SMB）或硬體儲存體重復資料刪除設備的 Windows 或 Linux 伺服器。

### <a name="veeam-deployment-scenarios"></a>Veeam 部署案例
您可以利用 Azure 來提供備份存放庫，以及長期備份和封存的儲存體目標。 AVS 私人雲端中的 Vm 與 Azure 中的備份存放庫之間的所有備份網路流量，都會透過高頻寬、低延遲的連結來傳輸。 跨區域的複寫流量會透過內部 Azure 背板網路傳輸，以降低使用者的頻寬成本。

**基本部署**

對於少於 30 TB 要備份的環境，AVS 建議使用下列設定：

* 在 AVS 私人雲端中，將 Veeam 備份伺服器和 proxy 伺服器安裝在相同的 VM 上。
* Azure 中以 Linux 為基礎的主要備份存放庫，設定為備份作業的目標。
* `azcopy` 用來將資料從主要備份存放庫複製到已複寫至另一個區域的 Azure blob 容器。

![基本部署案例](media/veeam-basicdeployment.png)

**先進部署**

對於超過 30 TB 要備份的環境，AVS 建議使用下列設定：

* VSAN 叢集中每個節點的一個 proxy 伺服器（如 Veeam 所建議）。
* AVS 私人雲端中以 Windows 為主的主要備份存放庫，可快取五天的資料，以快速還原。
* Azure 中的 Linux 備份存放庫，做為備份複本作業的目標，持續時間較長的保留期。 此存放庫應設定為相應放大備份存放庫。
* `azcopy` 用來將資料從主要備份存放庫複製到已複寫至另一個區域的 Azure blob 容器。

![基本部署案例](media/veeam-advanceddeployment.png)

在上圖中，請注意，備份 proxy 是具有 vSAN 資料存放區上之工作負載 VM 磁片的熱新增存取權的 VM。 Veeam 使用虛擬裝置備份 proxy 傳輸模式來進行 vSAN。

## <a name="requirements-for-veeam-solution-on-avs"></a>在 AVS 上 Veeam 解決方案的需求

Veeam 解決方案會要求您執行下列動作：

* 提供您自己的 Veeam 授權。
* 部署和管理 Veeam，以備份在 AVS 私人雲端中執行的工作負載。

此解決方案可讓您完整控制 Veeam 備份工具，並提供使用原生 Veeam 介面或 Veeam vCenter 外掛程式來管理 VM 備份作業的選項。

如果您是現有的 Veeam 使用者，可以略過 Veeam 解決方案元件上的一節，並直接繼續進行[Veeam 部署案例](#veeam-deployment-scenarios)。

## <a name="install-and-configure-veeam-backups-in-your-avs-private-cloud"></a>在您的 AVS 私人雲端中安裝和設定 Veeam 備份

下列各節說明如何安裝及設定您的 AVS 私人雲端的 Veeam 備份解決方案。

部署程式由下列步驟組成：

1. [vCenter UI：在您的 AVS 私人雲端中設定基礎結構服務](#vcenter-ui-set-up-infrastructure-services-in-your-avs-private-cloud)
2. [AVS 入口網站：設定適用于 Veeam 的 AVS 私人雲端網路](#avs-private-cloud-set-up-avs-private-cloud-networking-for-veeam)
3. [AVS 入口網站：提升許可權](#avs-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure 入口網站：將您的虛擬網路連線到 AVS 私人雲端](#azure-portal-connect-your-virtual-network-to-the-avs-private-cloud)
5. [Azure 入口網站：在 Azure 中建立備份存放庫](#azure-portal-connect-your-virtual-network-to-the-avs-private-cloud)
6. [Azure 入口網站：設定長期資料保留的 Azure blob 儲存體](#configure-azure-blob-storage-for-long-term-data-retention)
7. [AVS 私用雲端的 vCenter UI：安裝 Veeam B & R](#vcenter-console-of-avs-private-cloud-install-veeam-br)
8. [Veeam 主控台：設定 Veeam 備份 & 復原軟體](#veeam-console-install-veeam-backup-and-recovery-software)
9. [AVS 入口網站：設定 Veeam 存取和取消提升許可權](#avs-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>開始之前

開始 Veeam 部署之前，需要下列各項：

* 您所擁有的 Azure 訂用帳戶
* 預先建立的 Azure 資源群組
* 訂用帳戶中的 Azure 虛擬網路
* Azure 儲存體帳戶
* 使用 AVS 入口網站建立的[Avs 私用雲端](create-private-cloud.md)。  

在執行階段期間，需要下列專案：

* 適用于 Windows 的 VMware 範本，用來安裝 Veeam （例如 Windows Server 2012 R2-64 位映射）
* 針對備份網路識別的一個可用 VLAN
* 要指派給備份網路之子網的 CIDR
* 已上傳至 AVS 私人雲端 vSAN 資料存放區的 Veeam 9.5 u3 可安裝媒體（ISO）

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-avs-private-cloud"></a>vCenter UI：在您的 AVS 私人雲端中設定基礎結構服務

在 AVS 私人雲端中設定基礎結構服務，以輕鬆管理您的工作負載和工具。

* 您可以新增外部身分識別提供者（如[設定 vCenter 身分識別來源](set-vcenter-identity.md)中所述），以在下列任何一種情況下使用 Active Directory：
  * 您想要在您的 AVS 私人雲端中，識別內部部署 Active Directory （AD）中的使用者。
  * 您想要在您的 AVS 私人雲端中為所有使用者設定 AD。
  * 您想要使用 Azure AD。
* 若要在您的 AVS 私人雲端中為您的工作負載提供 IP 位址查閱、IP 位址管理和名稱解析服務，請依照在[您的 Avs 私人雲端中設定 DNS 和 DHCP 應用程式和工作負載](dns-dhcp-setup.md)中所述，設定 DHCP 和 DNS 伺服器。

### <a name="avs-private-cloud-set-up-avs-private-cloud-networking-for-veeam"></a>AVS 私用雲端：設定適用于 Veeam 的 AVS 私人雲端網路

存取 AVS 入口網站，為 Veeam 解決方案設定 AVS 私人雲端網路。

建立備份網路的 VLAN，並為其指派子網 CIDR。 如需指示，請參閱[建立和管理 vlan/子網](create-vlan-subnet.md)。

在管理子網和備份網路之間建立防火牆規則，以允許 Veeam 所使用之埠上的網路流量。 請參閱 Veeam 主題[使用的埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)。 如需建立防火牆規則的指示，請參閱[設定防火牆資料表和規則](firewall.md)。

下表提供埠清單。

| 圖示 | 說明 | 圖示 | 說明 |
| ------------ | ------------- | ------------ | ------------- |
| 備份伺服器  | vCenter  | HTTPS/TCP  | 443 |
| 備份伺服器 <br> *部署 Veeam 備份 & 複寫元件所需* | 備份 Proxy  | TCP/UDP  | 135、137到139和445 |
    | 備份伺服器   | DNS  | UDP  | 53  | 
    | 備份伺服器   | Veeam 更新通知伺服器  | TCP  | 80  | 
    | 備份伺服器   | Veeam 授權補救伺服器  | TCP  | 443  | 
    | 備份 Proxy   | vCenter |   |   | 
    | 備份 Proxy  | Linux 備份存放庫   | TCP  | 22  | 
    | 備份 Proxy  | Windows 備份存放庫  | TCP  | 49152-65535   | 
    | 備份存放庫  | 備份 Proxy  | TCP  | 2500-5000  | 
    | 來源備份存放庫<br> *用於備份複製作業*  | 目標備份存放庫  | TCP  | 2500-5000  | 

在工作負載子網和備份網路之間建立防火牆規則，如[設定防火牆資料表和規則](firewall.md)中所述。 針對應用程式感知備份和還原，必須在裝載特定應用程式的工作負載 Vm 上開啟[其他埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)。

根據預設，AVS 會提供1Gbps 的 ExpressRoute 連結。 針對較大的環境大小，可能需要較高的頻寬連結。 如需更多頻寬連結的詳細資訊，請洽詢 Azure 支援。

若要繼續進行設定，您需要授權金鑰和對等線路 URI，並可存取您的 Azure 訂用帳戶。 這項資訊可在 AVS 入口網站的 [虛擬網路連線] 頁面上取得。 如需指示，請參閱[取得 Azure 虛擬網路到 AVS 連線的對等資訊](virtual-network-connection.md)。 如果您在取得資訊時遇到任何問題，[請聯絡支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)人員。

### <a name="avs-private-cloud-escalate-privileges-for-cloudowner"></a>AVS 私用雲端：提升**cloudowner**的許可權

預設的 ' cloudowner ' 使用者在 AVS 私用雲端 vCenter 中沒有足夠的許可權可以安裝 VEEAM，因此使用者的 vCenter 許可權必須升級。 如需詳細資訊，請參閱[提升許可權](escalate-private-cloud-privileges.md)。

### <a name="azure-portal-connect-your-virtual-network-to-the-avs-private-cloud"></a>Azure 入口網站：將您的虛擬網路連線到 AVS 私人雲端

遵循[使用 ExpressRoute 的 Azure 虛擬網路](azure-expressroute-connection.md)連線中的指示，將您的虛擬網路連線到 AVS 私人雲端。

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure 入口網站：建立備份存放庫 VM

1. 使用（2個 vcpu 和 8 GB 記憶體）建立標準 D2 v3 VM。
2. 選取以 CentOS 7.4 為基礎的映射。
3. 設定 VM 的網路安全性群組（NSG）。 確認 VM 沒有公用 IP 位址，而且無法從公用網際網路連線。
4. 為新的 VM 建立使用者名稱和密碼型使用者帳戶。 如需指示，請參閱[在 Azure 入口網站中建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)。
5. 建立 1x512 GiB standard HDD，並將其連結至存放庫 VM。 如需指示，請參閱[如何在 Azure 入口網站中將受控資料磁片連結至 WINDOWS VM](../virtual-machines/windows/attach-managed-disk-portal.md)。
6. [在受控磁片上建立 XFS 磁片](https://www.digitalocean.com/docs/volumes/how-to/)區。 使用先前所述的認證來登入 VM。 執行下列腳本來建立邏輯磁片區、在其中新增磁片、建立 XFS filesystem[磁碟分割](https://www.digitalocean.com/docs/volumes/how-to/partition/)，並在/backup1 路徑底下[掛接](https://www.digitalocean.com/docs/volumes/how-to/mount/)磁碟分割。

    範例腳本：

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

7. 以 NFS 掛載點的形式，將/backup1 公開至在 AVS 私人雲端中執行的 Veeam 備份伺服器。 如需指示，請參閱數位海洋文章[如何在 CentOS 6 上設定 NFS 掛接](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6)。 當您在 Veeam 備份伺服器中設定備份存放庫時，請使用此 NFS 共用名稱。

8. 在備份存放庫 VM 的 NSG 中設定篩選規則，以明確地允許進出 VM 的所有網路流量。

> [!NOTE]
> Veeam 備份 & 複寫會使用 SSH 通訊協定與 Linux 備份存放庫通訊，而且需要 Linux 存放庫上的 SCP 公用程式。 確認 SSH 背景程式已正確設定，且在 Linux 主機上可以使用 SCP。

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>針對長期資料保留設定 Azure blob 儲存體

1. 建立標準類型和 blob 容器的一般用途儲存體帳戶（GPv2），如 Microsoft video[消費者入門 with Azure 儲存體](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)所述。
2. 如[建立容器](https://docs.microsoft.com/rest/api/storageservices/create-container)參考中所述，建立 Azure 儲存體容器。
2. 從 Microsoft 下載適用于 Linux 的 `azcopy` 命令列公用程式。 您可以在 CentOS 7.5 中的 bash shell 中使用下列命令。

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. 使用 `azcopy` 命令，將備份檔案複製到 blob 容器，或從該檔案複製。 如需詳細的命令，請參閱[使用 AzCopy On Linux 傳送資料](../storage/common/storage-use-azcopy-linux.md)。

### <a name="vcenter-console-of-avs-private-cloud-install-veeam-br"></a>AVS 私用雲端的 vCenter 主控台：安裝 Veeam B & R

從您的 AVS 私用雲端存取 vCenter 以建立 Veeam 服務帳戶、安裝 Veeam B & R 9.5，以及使用服務帳戶來設定 Veeam。

1. 建立名為「Veeam 備份角色」的新角色，並依照 Veeam 的建議指派必要的許可權。 如需詳細資訊，請參閱 Veeam 主題[必要許可權](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)。
2. 在 vCenter 中建立新的「Veeam 使用者群組」群組，並為其指派「Veeam 備份角色」。
3. 建立新的「Veeam 服務帳戶」使用者，並將它新增至「Veeam 使用者群組」。

    ![建立 Veeam 服務帳戶](media/veeam-vcenter01.png)

4. 使用備份網路 VLAN，在 vCenter 中建立分散式通訊埠群組。 如需詳細資訊，請參閱 VMware 影片在[VSphere Web 用戶端中建立分散式通訊埠群組](https://www.youtube.com/watch?v=wpCd5ZbPOpA)。
5. 依據[Veeam 系統需求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)，在 vCenter 中建立 Veeam 備份和 proxy 伺服器的 vm。 您可以使用 Windows 2012 R2 或 Linux。 如需詳細資訊，請參閱[使用 Linux 備份存放庫的需求](https://www.veeam.com/kb2216)。
6. 在 Veeam 備份伺服器 VM 中，將可安裝的 Veeam ISO 掛接為 CDROM 裝置。
7. 使用 Windows 2012 R2 電腦的 RDP 會話（Veeam 安裝的目標），在 Windows 2012 R2 VM 中[安裝 Veeam B & R 9.5 u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) 。
8. 尋找 Veeam 備份伺服器 VM 的內部 IP 位址，並將 IP 位址設定為 DHCP 伺服器中的靜態。 執行此操作所需的確切步驟取決於 DHCP 伺服器。 例如，Netgate 文章<a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">靜態 DHCP</a>對應說明如何使用 pfSense 路由器設定 DHCP 伺服器。

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam 主控台：安裝 Veeam 備份和修復軟體

使用 Veeam 主控台，設定 Veeam 備份和復原軟體。 如需詳細資訊，請參閱[Veeam Backup & Replication v9-安裝和部署](https://www.youtube.com/watch?v=b4BqC_WXARk)。

1. 將 VMware vSphere 新增為受管理的伺服器環境。 出現提示時，提供您在 AVS 私用雲端的 VCenter 主控台開頭所建立之 Veeam 服務帳戶的認證[： Install Veeam B & R](#vcenter-console-of-avs-private-cloud-install-veeam-br)。

    * 使用 [負載控制] 和 [預設] advanced 設定的預設設定。
    * 將掛接伺服器位置設定為備份伺服器。
    * 將 Veeam 伺服器的設定備份位置變更為遠端存放庫。

2. 在 Azure 中新增 Linux 伺服器做為備份存放庫。

    * 針對 [負載控制] 和 [高級設定] 使用預設設定。 
    * 將掛接伺服器位置設定為備份伺服器。
    * 將 Veeam 伺服器的設定備份位置變更為遠端存放庫。

3. 使用**Home > 設定備份設定**來啟用設定備份的加密。

4. 新增 Windows server VM 作為 VMware 環境的 proxy 伺服器。 使用 proxy 的「流量規則」，透過網路加密備份資料。

5. 設定備份作業。
    * 若要設定備份作業，請遵循[建立備份作業](https://www.youtube.com/watch?v=YHxcUFEss4M)中的指示。
    * 在 **高級設定 > 儲存體** 下，啟用備份檔案的加密。

6. 設定備份複製作業。

    * 若要設定備份複製作業，請遵循[建立備份複製作業](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)影片中的指示。
    * 在 **高級設定 > 儲存體** 下，啟用備份檔案的加密。

### <a name="avs-portal-set-up-veeam-access-and-de-escalate-privileges"></a>AVS 入口網站：設定 Veeam 存取和取消提升許可權
建立 Veeam 備份和復原伺服器的公用 IP 位址。 如需指示，請參閱[配置公用 IP 位址](public-ips.md)。

使用建立防火牆規則，以允許 Veeam 備份伺服器建立 Veeam 網站的輸出連線，以下載 TCP 通訊埠80上的更新/修補程式。 如需指示，請參閱[設定防火牆資料表和規則](firewall.md)。

若要取消提升許可權，請參閱[取消提升許可權](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="references"></a>參考

### <a name="avs-references"></a>AVS 參考

* [建立 AVS 私用雲端](create-private-cloud.md)
* [建立和管理 Vlan/子網](create-vlan-subnet.md)
* [vCenter 身分識別來源](set-vcenter-identity.md)
* [工作負載 DNS 和 DHCP 設定](dns-dhcp-setup.md)
* [提升許可權](escalate-privileges.md)
* [設定防火牆資料表和規則](firewall.md)
* [AVS 私人雲端許可權](learn-private-cloud-permissions.md)
* [配置公用 IP 位址](public-ips.md)

### <a name="veeam-references"></a>Veeam 參考

* [使用的埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [必要權限](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [系統需求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [安裝 Veeam 備份 & 複寫](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [適用于 Linux 的多 OS FLR 和存放庫支援所需的模組和許可權](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication v9-安裝與部署-影片](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [建立備份作業的 Veeam v9-影片](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 建立備份複製作業-影片](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure 參考

* [使用 Azure 入口網站設定 ExpressRoute 的虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [將 VNet 連線到線路-不同的訂用帳戶](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [在 Azure 入口網站中建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)
* [如何在 Azure 入口網站中將受控資料磁片連結至 Windows VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [使用 Azure 儲存體的消費者入門-影片](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Create Container (建立容器)](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [使用 AzCopy on Linux 傳送資料](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware 參考

* [在 vSphere Web 用戶端中建立分散式通訊埠群組-影片](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>其他參考資料

* [在受控磁片上建立 XFS 磁片區-RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [如何在 CentOS 7 上設定 NFS 掛接-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [設定 DHCP 伺服器-Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
