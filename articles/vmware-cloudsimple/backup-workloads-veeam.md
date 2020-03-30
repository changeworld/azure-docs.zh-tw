---
title: Azure VMware 解決方案（按雲簡單 - 使用 Veeam 備份私有雲上的工作負載虛擬機器）
description: 描述如何使用 Veeam B&R 9.5 備份在基於 Azure 的雲簡單私有雲中運行的虛擬機器
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025124"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>使用 Veeam B&R 備份雲簡單私有雲上的工作負載 VM

本指南介紹如何使用 Veeam B&R 9.5 備份在基於 Azure 的雲簡單私有雲中運行的虛擬機器。

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>關於 Veeam 備份和恢復解決方案

Veeam 解決方案包括以下元件。

**備份伺服器**

備份伺服器是 Windows 伺服器 （VM），用作 Veeam 的控制中心並執行以下功能： 

* 協調備份、複製、恢復驗證和恢復任務
* 控制作業調度和資源配置
* 允許您設置和管理備份基礎結構元件，並為備份基礎結構指定全域設置

**代理伺服器**

代理伺服器安裝在備份伺服器和備份基礎結構的其他元件之間。 它們管理以下功能：

* 從生產存儲中檢索 VM 資料
* 壓縮
* 重複資料刪除
* 加密
* 將資料傳輸到備份存儲庫

**備份存儲庫**

備份存儲庫是 Veeam 保存複製 VM 的備份檔案、VM 副本和中繼資料的存儲位置。  存儲庫可以是具有本地磁片（或裝載的 NFS/SMB）的 Windows 或 Linux 伺服器，也可以是硬體存儲重復資料消除設備。

### <a name="veeam-deployment-scenarios"></a>Veeam 部署方案
可以利用 Azure 提供備份存儲庫和存儲目標，用於長期備份和存檔。 私有雲中的 VM 和 Azure 中的備份存儲庫之間的所有備份網路流量都通過高頻寬、低延遲鏈路傳輸。 跨區域的複製流量通過內部 Azure 背板網路傳輸，從而降低使用者的頻寬成本。

**基本部署**

對於要備份的結核病小於 30 TB 的環境，CloudSimple 建議進行以下配置：

* 安裝在私有雲中同一 VM 上的 Veeam 備份伺服器和代理伺服器。
* Azure 中基於 Linux 的主備份存儲庫配置為備份作業的目標。
* `azcopy`用於將資料從主備份存儲庫複製到複製到另一個區域的 Azure Blob 容器。

![基本部署方案](media/veeam-basicdeployment.png)

**進階部署**

對於要備份超過 30 TB 的環境，CloudSimple 建議進行以下配置：

* 如 Veeam 建議，vSAN 群集中每個節點有一個代理伺服器。
* 私有雲中基於 Windows 的主備份存儲庫可緩存五天的資料，以便快速還原。
* Azure 中的 Linux 備份存儲庫作為備份副本作業的目標，具有較長的持續時間保留時間。 此存儲庫應配置為橫向擴展備份存儲庫。
* `azcopy`用於將資料從主備份存儲庫複製到複製到另一個區域的 Azure Blob 容器。

![基本部署方案](media/veeam-advanceddeployment.png)

在上圖中，請注意備份代理是一個 VM，具有對 vSAN 資料存儲上的工作負載 VM 磁片的熱添加存取權限。 Veeam 使用虛擬裝置備份代理傳輸模式進行 vSAN。

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>雲簡單 Veeam 解決方案的要求

Veeam 解決方案要求您執行以下操作：

* 提供您自己的 Veeam 許可證。
* 部署和管理 Veeam 以備份雲簡單私有雲中運行的工作負載。

此解決方案為您提供了對 Veeam 備份工具的完全控制，並提供了使用本機 Veeam 介面或 Veeam vCenter 外掛程式來管理 VM 備份作業的選擇。

如果您是現有 Veeam 使用者，則可以跳過 Veeam 解決方案元件部分，直接轉到[Veeam 部署方案](#veeam-deployment-scenarios)。

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>在雲簡單私有雲中安裝和配置 Veeam 備份

以下各節介紹如何為雲簡單私有雲安裝和配置 Veeam 備份解決方案。

部署過程包括以下步驟：

1. [vCenter UI：在私有雲中設置基礎設施服務](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [雲簡單門戶：為 Veeam 設置私有雲網路](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [雲簡單門戶：升級許可權](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure 門戶：將虛擬網路連接到私有雲](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure 門戶：在 Azure 中創建備份存儲庫](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure 門戶：配置 Azure Blob 存儲以進行長期資料保留](#configure-azure-blob-storage-for-long-term-data-retention)
7. [私有雲的 vCenter UI：安裝 Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam 主控台：配置 Veeam 備份&恢復軟體](#veeam-console-install-veeam-backup-and-recovery-software)
9. [雲簡單門戶：設置 Veeam 存取權限和降級許可權](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>開始之前

在開始 Veeam 部署之前，需要以下操作：

* 您擁有的 Azure 訂閱
* 預先創建的 Azure 資源組
* 訂閱中的 Azure 虛擬網路
* Azure 儲存體帳戶
* 使用雲簡單門戶創建的[私有雲](create-private-cloud.md)。  

在實施階段需要以下專案：

* VMware 範本，用於安裝 Veeam（如 Windows 伺服器 2012 R2 - 64 位映射）
* 為備份網路標識了一個可用的 VLAN
* 要分配給備份網路的子網的 CIDR
* Veeam 9.5 u3 可安裝媒體 （ISO） 上傳到私有雲的 vSAN 資料存儲

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI：在私有雲中設置基礎設施服務

在私有雲中配置基礎設施服務，以便輕鬆管理工作負載和工具。

* 您可以添加外部標識提供程式，如["設置 vCenter 標識源"中所述，以便使用 Active Directory（](set-vcenter-identity.md)如果以下任一項適用）：

  * 您希望從私有雲中的本地活動目錄 （AD） 中標識使用者。
  * 您希望在私有雲中為所有使用者設置 AD。
  * 您想要使用 Azure AD。
* 要為私有雲中的工作負載提供 IP 位址查找、IP 位址管理和名稱解析服務，請設置 DHCP 和 DNS 伺服器，如[在雲簡單私有雲中設置 DNS 和 DHCP 應用程式和工作負載中](dns-dhcp-setup.md)所述。

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>雲簡單私有雲：為 Veeam 設置私有雲網路

訪問 CloudSimple 門戶，為 Veeam 解決方案設置私有雲網路。

為備份網路創建 VLAN 並為其分配子網 CIDR。 有關說明，請參閱[創建和管理 VLAN/子網](create-vlan-subnet.md)。

在管理子網和備份網路之間創建防火牆規則，以允許 Veeam 使用的埠上的網路流量。 請參閱 Veeam 主題["已使用埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)"。 有關防火牆規則創建的說明，請參閱[設置防火牆表和規則](firewall.md)。

下表提供了埠清單。

| 圖示 | 描述 | 圖示 | 描述 |
| ------------ | ------------- | ------------ | ------------- |
| 備份伺服器  | vCenter  | HTTPS / TCP  | 443 |
| 備份伺服器 <br> *部署 Veeam 備份&複製元件所需的* | 備份代理  | TCP/UDP  | 135、137 到 139 和 445 |
    | 備份伺服器   | DNS  | UDP  | 53  | 
    | 備份伺服器   | Veeam 更新通知伺服器  | TCP  | 80  | 
    | 備份伺服器   | Veeam 許可證補救伺服器  | TCP  | 443  | 
    | 備份代理   | vCenter |   |   | 
    | 備份代理  | Linux 備份存儲庫   | TCP  | 22  | 
    | 備份代理  | 視窗備份存儲庫  | TCP  | 49152 - 65535   | 
    | 備份存儲庫  | 備份代理  | TCP  | 2500 -5000  | 
    | 源備份存儲庫<br> *用於備份複製作業*  | 目標備份存儲庫  | TCP  | 2500 - 5000  | 

在工作負載子網和備份網路之間創建防火牆規則，如[設置防火牆表和規則](firewall.md)中所述。  對於應用程式感知備份和還原，必須在承載特定應用程式的工作負載 VM 上打開[其他埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)。

預設情況下，CloudSimple 提供 1Gbps 快速路由連結。 對於較大的環境大小，可能需要更高的頻寬鏈路。 有關高頻寬連結的詳細資訊，請與 Azure 支援部門聯繫。

要繼續設置，您需要授權金鑰和對等電路 URI 以及訪問 Azure 訂閱。  此資訊可在雲簡單門戶中的虛擬網路連接頁面上找到。 有關說明，請參閱[獲取 Azure 虛擬網路到雲簡單連接的對等資訊](virtual-network-connection.md)。 如果您在獲取資訊時遇到任何問題，[請聯繫支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)人員。

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>雲簡單私有雲：提升雲擁有者的許可權

預設的"雲擁有者"使用者在私有雲 vCenter 中沒有足夠的許可權來安裝 VEEAM，因此必須升級使用者的 vCenter 許可權。 有關詳細資訊，請參閱[升級許可權](escalate-private-cloud-privileges.md)。

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure 門戶：將虛擬網路連接到私有雲

使用 ExpressRoute 按照[Azure 虛擬網路連接](azure-expressroute-connection.md)中的說明將虛擬網路連接到私有雲。

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure 門戶：創建備份存儲庫 VM

1. 創建具有（2 個 vCPU 和 8 GB 記憶體）的標準 D2 v3 VM。
2. 選擇基於 CentOS 7.4 的圖像。
3. 為 VM 配置網路安全性群組 （NSG）。 驗證 VM 沒有公共 IP 位址，並且無法從公共 Internet 進行。
4. 為新 VM 創建基於使用者名和密碼的使用者帳戶。 有關說明，請參閱[在 Azure 門戶中創建 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)。
5. 創建 1x512 GiB 標準硬碟並將其附加到存儲庫 VM。  有關說明，請參閱[如何將託管資料磁片附加到 Azure 門戶中的 Windows VM。](../virtual-machines/windows/attach-managed-disk-portal.md)
6. [在託管磁片上創建 XFS 卷](https://www.digitalocean.com/docs/volumes/how-to/)。 使用前面提到的憑據登錄到 VM。 執行以下腳本以創建邏輯卷、向其添加磁片、創建 XFS 檔案系統[分區](https://www.digitalocean.com/docs/volumes/how-to/partition/)以及[將分區裝載](https://www.digitalocean.com/docs/volumes/how-to/mount/)到 /backup1 路徑下。

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

7. 將 /backup1 作為 NFS 裝載點公開/備份，以指向在私有雲中運行的 Veeam 備份伺服器。 有關說明，請參閱數位海洋文章["如何在 CentOS 6 上設置 NFS 山"。](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6) 在 Veeam 備份伺服器中配置備份存儲庫時，使用此 NFS 共用名稱稱。

8. 在 NSG 中為備份存儲庫 VM 配置篩選規則，以顯式允許所有網路流量進入或從 VM 傳輸。

> [!NOTE]
> Veeam 備份&複製使用 SSH 協定與 Linux 備份存儲庫進行通信，並且需要 Linux 存儲庫上的 SCP 實用程式。 驗證 SSH 守護進程配置正確，以及 SCP 在 Linux 主機上是否可用。

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>配置 Azure Blob 存儲以進行長期資料保留

1. 創建標準類型的通用存儲帳戶 （GPv2） 和 Blob 容器，如 Microsoft 視頻["開始使用 Azure 存儲"](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)中所述。
2. 創建 Azure 存儲容器，如[創建容器](https://docs.microsoft.com/rest/api/storageservices/create-container)引用中所述。
2. 從微軟`azcopy`下載 Linux 的命令列實用程式。 您可以在 CentOS 7.5 中的 bash 外殼中使用以下命令。

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. 使用`azcopy`命令將備份檔案複製到 blob 容器中。這些操作將備份檔案複製到或從 blob 容器複製。  有關詳細命令[，請參閱在 Linux 上使用 AzCopy 傳輸資料](../storage/common/storage-use-azcopy-linux.md)。

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>私有雲的 vCenter 主控台：安裝 Veeam B&R

從私有雲訪問 vCenter 以創建 Veeam 服務帳戶，安裝 Veeam B&R 9.5，並使用服務帳戶配置 Veeam。

1. 創建名為"Veeam 備份角色"的新角色，並根據需要分配 Veeam 的建議許可權。 有關詳細資訊，請參閱 Veeam 主題["必需許可權](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)"。
2. 在 vCenter 中創建新的"Veeam 使用者組"組，並將其分配給"Veeam 備份角色"。
3. 創建新的"Veeam 服務帳戶"使用者並將其添加到"Veeam 使用者組"。

    ![創建 Veeam 服務帳戶](media/veeam-vcenter01.png)

4. 使用備份網路 VLAN 在 vCenter 中創建分散式埠組。 有關詳細資訊，請查看 VMware 視頻[在 vSphere Web 用戶端中創建分散式埠組](https://www.youtube.com/watch?v=wpCd5ZbPOpA)。
5. 根據[Veeam 系統要求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)，在 vCenter 中為 Veeam 備份和代理伺服器創建 VM。 您可以使用 Windows 2012 R2 或 Linux。 有關詳細資訊，請參閱[使用 Linux 備份存儲庫的要求](https://www.veeam.com/kb2216)。
6. 將可安裝的 Veeam ISO 作為 CDROM 設備安裝在 Veeam 備份伺服器 VM 中。
7. 使用 RDP 會話到 Windows 2012 R2 電腦（Veeam 安裝的目標），在 Windows 2012 R2 VM 上安裝[Veeam B&R 9.5u3。](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
8. 查找 Veeam 備份伺服器 VM 的內部 IP 位址，並將 IP 位址配置為在 DHCP 伺服器中為靜態位址。 執行此操作所需的確切步驟取決於 DHCP 伺服器。 例如，Netgate 文章靜態<a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">DHCP 映射</a>解釋了如何使用 pfSense 路由器配置 DHCP 伺服器。

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam 主控台：安裝 Veeam 備份和恢復軟體

使用 Veeam 主控台配置 Veeam 備份和恢復軟體。 有關詳細資訊，請參閱[Veeam 備份&複製 v9 - 安裝和部署](https://www.youtube.com/watch?v=b4BqC_WXARk)。

1. 將 VMware vSphere 添加為託管伺服器環境。 當出現提示時，提供您在私有雲 vCenter 主控台開頭創建的 Veeam 服務帳戶[的憑據：安裝 Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)。

    * 對負載控制和預設高級設置使用預設設置。
    * 將裝載伺服器位置設置為備份伺服器。
    * 將 Veeam 伺服器的配置備份位置更改為遠端存放庫。

2. 將 Azure 中的 Linux 伺服器添加為備份存儲庫。

    * 使用預設設置進行負載控制和高級設置。 
    * 將裝載伺服器位置設置為備份伺服器。
    * 將 Veeam 伺服器的配置備份位置更改為遠端存放庫。

3. 使用**主>配置備份設置**啟用配置備份加密。

4. 將 Windows 伺服器 VM 添加為 VMware 環境的代理伺服器。 使用代理的"流量規則"，通過線路加密備份資料。

5. 配置備份作業。
    * 要配置備份作業，請按照[創建備份作業](https://www.youtube.com/watch?v=YHxcUFEss4M)中的說明操作。
    * 在 **"存儲">** 啟用備份檔案加密。

6. 配置備份複製作業。

    * 要配置備份複製作業，請按照視頻中的說明[創建備份複製作業](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)。
    * 在 **"存儲">** 啟用備份檔案加密。

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>雲簡單門戶：設置 Veeam 存取權限和降級許可權
為 Veeam 備份和恢復伺服器創建公共 IP 位址。 有關說明，請參閱[分配公共 IP 位址](public-ips.md)。

創建防火牆規則，用於允許 Veeam 備份伺服器創建到 Veeam 網站的出站連接，以便在 TCP 埠 80 上下載更新/修補程式。 有關說明，請參閱[設置防火牆表和規則](firewall.md)。

要取消許可權，請參閱[降級特權](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="references"></a>參考

### <a name="cloudsimple-references"></a>雲簡單引用

* [建立私人雲端](create-private-cloud.md)
* [創建和管理 VLAN/子網](create-vlan-subnet.md)
* [vCenter 標識源](set-vcenter-identity.md)
* [工作負載 DNS 和 DHCP 設置](dns-dhcp-setup.md)
* [提升權限](escalate-privileges.md)
* [設定防火牆資料表和規則](firewall.md)
* [私有雲許可權](learn-private-cloud-permissions.md)
* [分配公共 IP 位址](public-ips.md)

### <a name="veeam-references"></a>Veeam 參考文獻

* [已使用埠](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [必需許可權](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [系統要求](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [安裝 Veeam 備份&複製](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Linux 的多作業系統 FLR 和存儲庫支援所需的模組和許可權](https://www.veeam.com/kb2216)
* [Veeam 備份&複製 v9 - 安裝和部署 - 視頻](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 創建備份作業 - 視頻](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 創建備份複製作業 - 視頻](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure 引用

* [使用 Azure 入口網站為 ExpressRoute 設定虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [將 VNet 連線到線路 - 不同訂用帳戶](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [在 Azure 入口網站中建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)
* [如何在 Azure 入口網站中將受控資料磁碟連結到 Windows VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [開始使用 Azure 存儲 - 視頻](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Create Container (建立容器)](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [使用 AzCopy on Linux 傳送資料](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware 參考

* [在 vSphere Web 用戶端中創建分散式埠組 - 視頻](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>其他參考資料

* [在託管磁片上創建 XFS 卷 - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [如何在 CentOS 7 上設置 NFS 安裝 - 如何鍛造](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [配置 DHCP 伺服器 - 網門](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
