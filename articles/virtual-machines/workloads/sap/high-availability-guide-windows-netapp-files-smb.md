---
title: 在 Windows 上使用 azure NetApp Files (SMB) 的適用于 SAP NW 的 Azure Vm HA |Microsoft Docs
description: 'Sap NetWeaver 在 Windows 上的 Azure Vm 上的高可用性，適用于 SAP 應用程式的 Azure NetApp Files (SMB) '
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: 90b0ab4fdabd40e803d1f85a640e4cb387e40c44
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958943"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Sap NetWeaver 在 Windows 上的 Azure Vm 上的高可用性，適用于 SAP 應用程式的 Azure NetApp Files (SMB) 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

本文說明如何在[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)上使用[SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ，在 Windows vm 上部署、設定虛擬機器、安裝叢集架構，以及安裝高可用性的 SAP NetWeaver 7.50 系統。  

本文不會詳細描述資料庫層。 我們假設已建立 Azure [虛擬網路](../../../virtual-network/virtual-networks-overview.md) 。  

請先閱讀下列 SAP Note 和文件：

* [Azure NetApp Files 文件][anf-azure-doc] 
* SAP 附注 [1928533][1928533]，其中包含：  
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上的 Windows 所需的 SAP 核心版本
* SAP Note [2015553][2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2178632][2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [1999351][1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) 列出 SMB 3.x 通訊協定的 SAP 支援 CA 功能的必要條件。
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) 有 Windows 2012 和2016上執行的 SAP transaction AL11 緩慢的疑難排解資訊。
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) 包含 Windows Server 上具有 SMB 3.0 通訊協定之檔案共用的透明容錯移轉功能的相關資訊。
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) 有建議 (停用8.3 名稱產生) ，以解決資料存取期間不良的檔案系統效能/錯誤。
* [在 Azure 之 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用上安裝 SAP NetWeaver 高可用性](./sap-high-availability-installation-wsfc-file-share.md) 
* [SAP NetWeaver 的 Azure 虛擬機器高可用性架構和案例](./sap-high-availability-architecture-scenarios.md)
* [在 ASCS 叢集中新增探查埠](sap-high-availability-installation-wsfc-file-share.md)
* [在容錯移轉叢集上安裝 () SCS 實例](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [建立適用於 Azure NetApp Files 的 SMB 磁碟區](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)
* [使用 Azure NetApp Files 在 Microsoft Azure 上的 NetApp SAP 應用程式][anf-sap-applications-azure]

## <a name="overview"></a>概觀

SAP 已針對在 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理，為叢集共用磁碟開發新的方法和替代方案。 您可以使用 SMB 檔案共用來部署 SAP 全域主機檔案，而不是使用叢集共用磁片。 Azure NetApp Files 支援使用 Active Directory 的 SMBv3 (以及使用 NTFS) 搭配 NTFS ACL。 因為 Azure NetApp Files 是 PaaS 服務) ，所以會自動提供 (高可用性。 這些功能讓 Azure NetApp Files 成為裝載 SAP global 的 SMB 檔案共用的絕佳選項。  
[Azure Active Directory (AD) 網域服務](../../../active-directory-domain-services/overview.md)和[Active Directory Domain Services (](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) AD DS) 都受到支援。 您可以使用現有的 Active Directory 網域控制站搭配 Azure NetApp Files。 網域控制站可以在 Azure 中做為虛擬機器，或透過 ExpressRoute 或 S2S VPN 在內部部署。 在本文中，我們將使用 Azure VM 中的網域控制站。  
SAP Netweaver 中央服務的高可用性 (HA) 需要共用儲存體。 若要在 Windows 上達成此目標，您目前必須建立 SOFS 叢集或使用叢集共用磁片 s/w，例如 SIOS。 現在您可使用在 Azure NetApp Files 上部署的共用儲存體來達到 SAP Netweaver HA。 使用共用儲存體的 Azure NetApp Files 可免除 SOFS 或 SIOS 的需求。  

> [!NOTE]
> 針對 SAP NetWeaver 7.40 (和更新版本)，包含 SAP 核心 7.49 (和更新版本)，支援使用檔案共用進行 SAP ASCS/SCS 執行個體叢集處理。  

![使用 SMB 共用的 SAP ASCS/SCS HA 架構](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 檔案共用的必要條件如下：
* SMB 3.0 (或更新版本) 通訊協定。
* 能夠設定 Active Directory 存取控制清單 (Acl) Active Directory 使用者群組和電腦 $ computer 物件。
* 檔案共用必須啟用 HA。

Azure NetApp Files 提供此參考架構中 SAP Central services 的共用：

![具有 SMB 共用詳細資料的 SAP ASCS/SCS HA 架構](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>建立和掛接適用于 Azure NetApp Files 的 SMB 磁片區

執行下列步驟，以準備使用 Azure NetApp Files。  

1. 遵循[註冊 Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)的步驟  
2. 建立 Azure NetApp 帳戶，遵循[建立 NetApp 帳戶](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中所述的步驟  
3. 設定容量集區，並遵循[設定容量集](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)區中的指示
4. Azure NetApp Files 資源必須位於委派的子網中。 遵循將 [子網委派給 Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) 的指示，建立委派的子網。  

   > [!IMPORTANT]
   > 您必須先建立 Active Directory 連線，然後再建立 SMB 磁碟區。 檢查 [Active Directory 連接的需求](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)。  

5. 建立 Active Directory 連接，如[建立 Active Directory 連接](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)中所述  
6. 遵循[新增 smb 磁片](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)區中的指示，建立 Smb Azure NETAPP Files SMB 磁片區  
7. 將 SMB 磁片區掛接在您的 Windows 虛擬機器上。

> [!TIP]
> 您可以找到有關如何掛接 Azure NetApp Files 磁片區的指示，如果您在 [Azure 入口網站](https://portal.azure.com/#home) 中流覽至 [Azure netapp files] 物件，請按一下 [ **磁片** 區] 分頁，然後 **掛接指示**。  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>使用 Windows 容錯移轉叢集準備 SAP HA 的基礎結構 

1. [設定 Azure 內部負載平衡器的 ASCS/SCS 負載平衡規則](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716)。
2. [將 Windows 虛擬機器新增至網域](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c)。
3. [在 SAP ASCS/SCS 實例的兩個叢集節點上新增登錄專案](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [設定 SAP ASCS/SCS 實例的 Windows Server 容錯移轉叢集](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. 如果您使用 Windows Server 2016，建議您設定 [Azure 雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)。


## <a name="install-sap-ascs-instance-on-both-nodes"></a>在兩個節點上安裝 SAP ASCS 實例

您需要下列來自 SAP 的軟體：
   * SAP 軟體布建管理員 (SWPM) 安裝工具版本 SPS25 或更新版本。
   * SAP 核心7.49 或更新版本
   * 建立叢集 SAP ASCS/SCS 實例的虛擬主機名稱 (叢集網路名稱) ，如 [建立叢集 SAP ASCS/scs 實例的虛擬主機名稱](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097)中所述。

> [!NOTE]
> 針對 SAP NetWeaver 7.40 (和更新版本)，包含 SAP 核心 7.49 (和更新版本)，支援使用檔案共用進行 SAP ASCS/SCS 執行個體叢集處理。  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>在第一個 ASCS/SCS 叢集節點上安裝 ASCS/SCS 實例

1. 在第一個叢集節點上安裝 SAP ASCS/SCS 實例。 啟動 SAP SWPM 安裝工具，然後流覽至：「**產品**  >  **DBMS** > 安裝 > 應用程式伺服器 ABAP (或 JAVA) > High-Availability System > ASCS/SCS 實例 > 第一個叢集節點。  

2. 選取 [檔案 **共用** 叢集] 作為 SWPM 中的叢集共用設定。  
3. 當系統提示您輸入 **SAP 系統叢集參數** 時，請輸入您已建立為檔案 **共用主機名稱** 之 Azure NetApp Files SMB 共用的主機名稱。  在此範例中，SMB 共用主機名稱為 **anfsmb-9562**。 

   > [!IMPORTANT]
   > 如果必要條件檢查程式導致 SWPM 顯示不符合持續可用性功能條件，則可以依照 [當您嘗試存取 Windows 中不再存在的共用資料夾時，依照延遲錯誤訊息](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)中的指示來解決此問題。  

   > [!TIP]
   > 如果必要條件檢查程式導致 SWPM 顯示的交換大小條件不符合，您可以流覽至我的電腦>系統屬性>效能設定> Advanced> 虛擬記憶體> 變更，以調整交換大小。  

4. 使用 PowerShell 設定 SAP 叢集資源（ `SAP-SID-IP` 探查埠）。 請在其中一個 SAP ASCS/SCS 叢集節點上執行這項設定，如 [設定探查埠](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)中所述。

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>在第二個 ASCS/SCS 叢集節點上安裝 ASCS/SCS 實例

1. 在第二個叢集節點上安裝 SAP ASCS/SCS 實例。 啟動 SAP SWPM 安裝工具，然後流覽至 **產品**  >  **DBMS** > 安裝 > 應用程式伺服器 ABAP (或 JAVA) > High-Availability System > ASCS/SCS 實例 > 其他叢集節點。  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>安裝 DBMS 執行個體和 SAP 應用程式伺服器

安裝下列各項，完成您的 SAP 安裝：

   * DBMS 實例  
   * 主要 SAP 應用程式伺服器  
   * 其他 SAP 應用程式伺服器  

## <a name="test-the-sap-ascsscs-instance-failover"></a>測試 SAP ASCS/SCS 實例容錯移轉 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>從叢集節點 A 容錯移轉到叢集節點 B 和背面
在此測試案例中，我們會將叢集節點 sapascs1 稱為節點 A，以及叢集節點 sapascs2 作為節點 B。

1. 確認叢集資源正在節點 A 上執行。 ![圖1：在容錯移轉測試之前，在節點 A 上執行的 Windows Server 容錯移轉叢集資源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 重新開機叢集節點 A。SAP 叢集資源將會移至叢集節點 B。 ![圖2：容錯移轉測試之後，在節點 B 上執行的 Windows Server 容錯移轉叢集資源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>鎖定專案測試

1. 確認 SAP 排入佇列複寫伺服器 (ERS) 為使用中  
2. 登入 SAP 系統，執行交易 SU01 並在變更模式中開啟使用者識別碼。 這會產生 SAP 鎖定專案。  
3. 當您登入 SAP 系統時，藉由流覽至 transaction ST12 來顯示鎖定專案。  
4. 將 ASCS 資源從叢集節點 A 容錯移轉到叢集節點 B。  
5. 確認保留在 SAP ASCS/SCS 叢集資源容錯移轉之前所產生的鎖定專案。  

![圖3：在容錯移轉測試之後保留鎖定專案](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

如需詳細資訊，請參閱 [使用 ERS 針對 ASCS 中的排入佇列的容錯移轉進行疑難排解](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 瞭解如何建立高可用性，並規劃 SAP 的嚴重損壞修復 
* Azure 上的 HANA (大型實例) ，請參閱 [SAP Hana (大型實例) 高可用性和 Azure 上的嚴重損壞修復](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性，並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
