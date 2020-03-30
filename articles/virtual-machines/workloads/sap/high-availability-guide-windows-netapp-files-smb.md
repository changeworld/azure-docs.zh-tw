---
title: Azure VM HA 用於具有 Azure NetApp 檔 （SMB） 的 Windows 上的 SAP NW。微軟文檔
description: 適用于 SAP 應用程式的 Windows 上 Azure VM 上 SAP NetWeaver 的高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591348"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>適用于 SAP 應用程式的 Windows 上 Azure VM 上 SAP NetWeaver 的高可用性

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

本文介紹如何使用[Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)上的[SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)在 Windows VM 上部署、配置虛擬機器、安裝群集框架和在 Windows VM 上安裝高可用性的 SAP NetWeaver 7.50 系統。  

本文未詳細介紹資料庫層。 我們假定 Azure[虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)已創建。  

請先閱讀下列 SAP Note 和文件：

* [Azure NetApp 檔文檔][anf-azure-doc] 
* SAP 注釋[1928533][1928533]， 包含：  
  * 支援部署 SAP 軟體的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * 微軟 Azure 上 Windows 所需的 SAP 內核版本
* SAP Note [2015553][2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2178632][2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [1999351][1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* SAP 注釋[2287140](https://launchpad.support.sap.com/#/notes/2287140)列出了 SMB 3.x 協定支援 SAP 的 CA 功能的先決條件。
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770)在 Windows 2012 和 2016 上為運行緩慢的 SAP 事務 AL11 提供了故障排除資訊。
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507)具有有關 Windows 伺服器上具有 SMB 3.0 協定的檔共用的透明容錯移轉功能的資訊。
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452)建議（停用 8.3 名稱生成）以解決資料訪問期間檔案系統性能不佳/錯誤的問題。
* [在 Azure 之 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用上安裝 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [SAP NetWeaver 的 Azure 虛擬機器高可用性架構和案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [在 ASCS 群集配置中添加探測埠](sap-high-availability-installation-wsfc-file-share.md)
* [在容錯移轉叢集上安裝 （A） SCS 實例](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [建立適用於 Azure NetApp Files 的 SMB 磁碟區](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [使用 Azure NetApp 檔在 Microsoft Azure 上使用 NetApp SAP 應用程式][anf-sap-applications-azure]

## <a name="overview"></a>總覽

SAP 已針對在 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理，為叢集共用磁碟開發新的方法和替代方案。 可以使用 SMB 檔共用來部署 SAP 全域主機檔，而不是使用群集共用磁片。 Azure NetApp 檔支援 SMBv3（以及 NFS）和使用活動目錄的 NTFS ACL。 Azure NetApp 檔自動高度可用（因為它是 PaaS 服務）。 這些功能使 Azure NetApp 檔非常適合託管 SAP 全域的 SMB 檔共用。  
支援[Azure 活動目錄 （AD） 域服務和](https://docs.microsoft.com/azure/active-directory-domain-services/overview)[活動目錄域服務 （AD DS）。](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 您可以將現有的活動目錄網域控制站與 Azure NetApp 檔一起使用。 網域控制站可以作為虛擬機器在 Azure 中，也可以通過 ExpressRoute 或 S2S VPN在內部。 在本文中，我們將在 Azure VM 中使用網域控制站。  
SAP Netweaver 中心服務的高可用性 （HA） 需要共用存儲。 為了在 Windows 上實現這一點，到目前為止，必須構建 SOFS 群集或使用群集共用磁片，如 SIOS。 現在，可以使用部署在 Azure NetApp 檔上的共用存儲來實現 SAP Netweaver HA。 將 Azure NetApp 檔用於共用存儲無需 SOFS 或 SIOS。  

> [!NOTE]
> 針對 SAP NetWeaver 7.40 (和更新版本)，包含 SAP 核心 7.49 (和更新版本)，支援使用檔案共用進行 SAP ASCS/SCS 執行個體叢集處理。  

![SAP ASCS/SCS HA 架構，具有 SMB 共用](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 檔共用的先決條件是：
* SMB 3.0 (或更新版本) 通訊協定。
* 能夠為活動目錄使用者組和電腦電腦物件設置活動目錄存取控制清單 （ACL）。
* 檔共用必須啟用 HA。

此參考體系結構中的 SAP 中央服務的共用由 Azure NetApp 檔提供：

![SAP ASCS/SCS HA 架構，具有 SMB 共用](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>為 Azure NetApp 檔創建和裝載 SMB 卷

執行以下步驟，作為使用 Azure NetApp 檔的準備。  

1. 按照步驟註冊[Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. 按照[創建 NetApp 帳戶](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)中描述的步驟創建 Azure NetApp 帳戶  
3. 按照[設置容量池中的說明設置容量池](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Azure NetApp 檔資源必須駐留在委派的子網中。 按照[將子網委派到 Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中的說明來創建委派的子網。  

> [!IMPORTANT]
> 在創建 SMB 卷之前，您需要創建活動目錄連接。 查看[活動目錄連接的要求](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)。  

5. 創建活動目錄連接，如[創建活動目錄連接中](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)所述  
6. 按照[添加 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)中的說明創建 SMB Azure NetApp 檔 SMB 卷  
7. 將 SMB 卷安裝到 Windows 虛擬機器上。

> [!TIP]
> 如果在[Azure 門戶](https://portal.azure.com/#home)中導航到 Azure NetApp 檔物件，請按一下 **"卷"** 邊欄選項卡，然後**裝載說明**，則可以找到有關如何裝載 Azure NetApp 檔卷的說明。  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>使用 Windows 容錯移轉叢集為 SAP HA 準備基礎結構 

1. [設置所需的 DNS IP 位址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [為 SAP 虛擬機器設置靜態 IP 位址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)。
3. [為 Azure 內部負載等化器設置靜態 IP 位址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e)。
4. [設定 Azure 內部負載平衡器的預設 ASCS/SCS 負載平衡規則](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c)。
5. [更改 Azure 內部負載等化器的 ASCS/SCS 預設負載平衡規則](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716)。
6. [將 Windows 虛擬機器添加到域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c)。
7. [在 SAP ASCS/SCS 實例的兩個叢集節點上添加登錄機碼](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [為 SAP ASCS/SCS 實例設置 Windows 伺服器容錯移轉叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. 如果使用 Windows 伺服器 2016，我們建議您配置 Azure[雲見證](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。


## <a name="install-sap-ascs-instance-on-both-nodes"></a>在兩個節點上安裝 SAP ASCS 實例

您需要 SAP 提供以下軟體：
   * SAP 軟體組態管理員 （SWPM） 安裝工具版本 SPS25 或更高版本。
   * SAP 內核 7.49 或更高版本
   * 為群集 SAP ASCS/SCS 實例創建虛擬主機名稱（群集網路名稱），如[為群集 SAP ASCS/SCS 實例創建虛擬主機名稱](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097)中所述。

> [!NOTE]
> 針對 SAP NetWeaver 7.40 (和更新版本)，包含 SAP 核心 7.49 (和更新版本)，支援使用檔案共用進行 SAP ASCS/SCS 執行個體叢集處理。  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>在第一個 ASCS/SCS 叢集節點上安裝 ASCS/SCS 實例

1. 在第一個叢集節點上安裝 SAP ASCS/SCS 實例。 啟動 SAP SWPM 安裝工具，然後導航到：**產品** > **DBMS** >安裝>應用程式伺服器 ABAP（或 JAVA）>高可用性系統> ASCS/SCS 實例>第一個叢集節點。  

2. 選擇**檔共用群集**作為 SWPM 中的群集共用配置。  
3. 當在步驟**SAP 系統群集參數**中提示時，輸入已創建為**檔共用主機名稱**的 Azure NetApp 檔 SMB 共用的主機名稱。  在此示例中，SMB 共用主機名稱為**afsmb-9562**。 

> [!IMPORTANT]
> 如果 SWPM 中的先決條件檢查器結果顯示未滿足連續可用性功能條件，[則可以在嘗試訪問 Windows 中不再存在的共用資料夾時，按照延遲錯誤訊息](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)中的說明進行定址。  

> [!TIP]
> 如果 SWPM 中的"先決條件檢查器結果"顯示未滿足交換大小條件，則可以通過導航到"我的電腦>系統屬性>性能設置>高級>虛擬記憶體>更改來調整 SWAP 大小。  

4. 使用 PowerShell 配置`SAP-SID-IP`SAP 群集資源（探測埠）。 在 SAP ASCS/SCS 叢集節點之一上執行此配置，如[配置探測埠](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)中所述。

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>在第二個 ASCS/SCS 叢集節點上安裝 ASCS/SCS 實例

1. 在第二個叢集節點上安裝 SAP ASCS/SCS 實例。 啟動 SAP SWPM 安裝工具，然後導航到**產品** > **DBMS** >安裝>應用程式伺服器 ABAP（或 JAVA） >高可用性系統> ASCS/SCS 實例>其他叢集節點。  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>安裝 DBMS 執行個體和 SAP 應用程式伺服器

通過安裝：

   * DBMS 實例  
   * 主 SAP 應用程式伺服器  
   * 附加的 SAP 應用程式伺服器  

## <a name="test-the-sap-ascsscs-instance-failover"></a>測試 SAP ASCS/SCS 實例容錯移轉 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>故障從叢集節點 A 容錯移轉到叢集節點 B 和返回
在此測試方案中，我們將叢集節點 sapcs1 稱為節點 A，將叢集節點 sapcs2 稱為節點 B。

1. 驗證群集資源是否在節點 A 上運行。![圖 1：在容錯移轉測試之前在節點 A 上運行的 Windows Server 容錯移轉叢集資源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 重新開機叢集節點 A。SAP 群集資源將移動到叢集節點 B。![圖 2：容錯移轉測試後在節點 B 上運行的 Windows 伺服器容錯移轉叢集資源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>鎖定輸入測試

1.驗證 SAP 佇列複製伺服器 （ERS） 是否處於活動狀態  
2. 登錄到 SAP 系統，執行事務 SU01 並在更改模式下打開使用者 ID。 這將生成 SAP 鎖條目。  
3. 當您登錄 SAP 系統時，通過導航到事務 ST12 來顯示鎖條目。  
4. 將 ASCS 資源從叢集節點 A 容錯移轉到叢集節點 B。  
5. 驗證是否保留 SAP ASCS/SCS 群集資源容錯移轉之前生成的鎖條目。  

![圖 3：容錯移轉測試後保留鎖條目](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

有關詳細資訊，請參閱使用[ERS 在 ASCS 中解決佇列容錯移轉的疑難排解](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 瞭解如何建立高可用性並規劃 SAP 的災害復原 
* 在 Azure 上（大型實例）上的 HANA，請參閱[Azure 上的 SAP HANA（大型實例）高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
