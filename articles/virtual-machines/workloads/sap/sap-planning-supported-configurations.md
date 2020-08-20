---
title: Azure 上的 SAP： Azure Vm 支援的案例
description: Azure 虛擬機器支援的 SAP 工作負載案例
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad1567a3a6cba2c2fbc519ffe5d384aba25ab51d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648984"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 虛擬機器支援案例上的 SAP 工作負載
在 Azure 中設計 SAP NetWeaver、Business one `Hybris` 或 S/4HANA 系統架構，為各種不同的架構和工具提供許多不同的機會，讓您可以使用這些不同的架構和工具來取得可擴充、有效率且高可用性的部署。 但相依于所使用的作業系統或 DBMS，有一些限制。 此外，並非所有支援內部部署的案例都是在 Azure 中以相同的方式支援。 本檔將引導您使用 Azure Vm，以提供支援的非高可用性設定和高可用性設定和架構。 對於 [Hana 大型實例](./hana-overview-architecture.md)所支援的案例，請參閱 [適用于 hana 大型實例的支援案例](./hana-supported-scenario.md)。 


## <a name="2-tier-configuration"></a>2層設定
SAP 2 層設定會被視為是在相同伺服器或 VM 上執行的 SAP DBMS 和應用層的組合層中建立的。 第二層會被視為使用者介面層。 在2層設定的案例中，DBMS 和 SAP 應用層會共用 Azure VM 的資源。 因此，您需要以不會爭用資源的方式來設定不同的元件。 您也必須小心，不要過度訂閱 VM 的資源。 這類設定不會提供任何高可用性，除了涉及不同 Azure 元件的 [Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/) 之外。

這類設定的圖形標記法看起來可能像這樣：

![簡單的2層設定](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

適用于 Windows、Red Hat、SUSE 和 Oracle Linux 的 Windows、Red Hat、SUSE 和，適用于適用于生產和非生產案例的 SQL Server、Oracle、Db2、maxDB 和 SAP ASE 的 DBMS 系統。 針對 SAP Hana 作為 DBMS，只有非生產案例支援這種類型的設定。 這也包括 [AZURE HANA 大型實例](./hana-overview-architecture.md) 的部署案例。
針對 Azure 上支援的所有 OS/DBMS 組合，支援這種類型的設定。 不過，您必須設定 DBMS 和 sap 元件的設定，讓 DBMS 和 SAP 元件不會競爭記憶體和 CPU 資源，因而超過實體可用的資源。 這必須藉由限制 DBMS 允許配置的記憶體來完成。 您也需要限制應用程式實例上的 SAP 延伸記憶體。 您也需要監視整體 VM 的 CPU 耗用量，以確保元件不會將 CPU 資源極大化。 

> [!NOTE]
> 針對生產環境 SAP 系統，我們建議額外的高可用性和最終嚴重損壞修復設定，如本檔稍後所述


## <a name="3-tier-configuration"></a>3層設定
在這類設定中，您會將 SAP 應用層和 DBMS 層分成不同的 Vm。 您通常會針對較大型的系統執行這項作業，而不會因為 SAP 應用層的資源而有更大的彈性。 在最簡單的設定中，除了涉及不同 Azure 元件的 [Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/) 之外，沒有高可用性。 

圖形化標記法如下所示：

![簡單的2層設定](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

這種類型的設定可在 Windows、Red Hat、SUSE 和 Oracle Linux （適用于 SQL Server、Oracle、Db2、SAP Hana、maxDB 和 SAP ASE 的 DBMS 系統）中，用於生產和非生產案例。 這是 [AZURE HANA 大型實例](./hana-overview-architecture.md)的預設部署設定。 為了簡化，我們並未區別 sap 中央服務與 sap 應用層中的 SAP 對話實例。 在這種簡單的三層式設定中，不會有 SAP Central Services 的高可用性保護。

> [!NOTE]
> 針對生產環境 SAP 系統，我們建議額外的高可用性和最終嚴重損壞修復設定，如本檔稍後所述


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>每個 VM 或 HANA 大型實例單位的多個 DBMS 實例
在此設定類型中，您會為每個 Azure VM 或 HANA 大型實例單位裝載多個 DBMS 實例。 動機可能是要維護的作業系統較少，而降低成本。 其他動機可能是在多個 DBMS 實例之間共用較大 VM 或 HANA 大型實例單位的資源，以提供更大的彈性和更高的效率。 到目前為止，這些設定大多顯示在非生產系統中。

像這樣的設定如下所示：

![一個單位中的多個 DBMS 實例](./media/sap-planning-supported-configurations/multiple-database-instances.png)

支援這種類型的 DBMS 部署：

- Windows 上的 SQL Server
- IBM Db2。 在[Linux、UNIX) 的多重實例 (](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)中尋找詳細資料
- 針對 Oracle。 如需詳細資訊，請參閱 [sap 支援附注 #1778431](https://launchpad.support.sap.com/#/notes/1778431) 以及相關的 sap 附注
- 針對 SAP Hana，在一個 VM 上有多個實例，則支援 SAP 呼叫這個部署方法 MCOS。 如需詳細資訊，請參閱 SAP 文章 [一部主機上的多個 SAP Hana 系統 (MCOS) ] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html) 

在一部主機上執行多個資料庫實例，您必須確定不同的實例不會爭用資源，因而超過 VM 的實體資源限制。 這特別適用于您需要將 VM 共用之實例的任何人所能配置的記憶體上限。 對於不同的資料庫實例可利用的 CPU 資源而言，也可能是如此。 所有提及的 DBMS 都有設定，可限制實例層級的記憶體配置和 CPU 資源。
為了支援 Azure Vm 的這類設定，您應該將用於不同實例所管理之資料庫的資料和記錄/重做記錄檔的磁片或磁片區分開。 或者，其他人不應該共用相同的磁片或磁片區，也就是由不同 DBMS 實例管理之資料庫的資料或記錄/重做記錄檔。 

HANA 大型實例的磁片設定已設定，並在 [Hana 大型實例的支援案例](./hana-supported-scenario.md#single-node-mcos)中詳細說明。 

> [!NOTE]
> 針對生產環境 SAP 系統，我們建議額外的高可用性和最終嚴重損壞修復設定，如本檔稍後所述。 本檔稍後所述的高可用性設定不支援具有多個 DBMS 實例的 Vm。


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>一個 VM 中的多個 SAP 對話方塊實例
在許多情況下，會將多個對話實例部署在裸機伺服器上，甚至部署在私人雲端中執行的 Vm。 這類設定的原因是要將特定的 SAP 對話實例量身訂做為特定工作負載、商務功能或工作負載類型。 將這些實例隔離到不同 Vm 的原因是作業系統維護和作業的工作。 或者，在許多情況下，如果 VM 的主機服務提供者或操作員要求每個 VM 的每月費用都是操作和系統管理的，則成本。 在 Azure 中，在單一 VM 中裝載多個 SAP 對話方塊實例的案例，在 Windows、Red Hat、SUSE 和 Oracle Linux 的作業系統上支援生產和非生產用途。 如果在單一 VM 上執行多個 SAP 應用程式伺服器實例，則應該設定 SAP 核心參數 PHYS_MEMSIZE （適用于 Windows 和新式 Linux 核心）。 此外，也建議您限制擴充作業系統上的 SAP 延伸記憶體，例如，會執行自動成長 SAP 延伸記憶體的 Windows。 您可以使用 SAP 設定檔參數來完成此操作 `em/max_size_MB` 。

在 Azure Vm 中執行多個 SAP 對話方塊實例的三層式設定下，可能會如下所示：

![一個單位中的多個 DBMS 實例](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

為了簡化，我們並未區別 sap 中央服務與 sap 應用層中的 SAP 對話實例。 在這種簡單的三層式設定中，不會有 SAP Central Services 的高可用性保護。 若為生產系統，則不建議將 SAP Central Services 保持未受保護。 如需有關所謂的詳細資訊，請參閱 SAP 中央實例的多重 SID 設定和這類多重 SID 設定的高可用性，請參閱本檔稍後的章節。

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>適用于 SAP DBMS 層的高可用性保護
當您想要部署 SAP 生產系統時，您需要考慮高可用性設定的熱待命型別。 尤其是 SAP Hana，其中的資料必須先載入至記憶體，才能取得完整的效能和擴充性，但 Azure 服務修復不是高可用性的理想量值。 

在一般情況下，Microsoft 只支援 docs.microsoft.com 中 SAP 工作負載一節底下所述的高可用性設定和軟體套件。 您可以在 SAP note [#1928533](https://launchpad.support.sap.com/#/notes/1928533)中讀取相同的語句。 Microsoft 不會提供 Microsoft 與 SAP 工作負載一起記載的其他高可用性協力廠商軟體架構的支援。 在這種情況下，高可用性架構的協力廠商供應商就是高可用性設定的支援合作物件，需要以客戶身分參與支援流程。 本文將會提及例外狀況。 

在一般情況中，Microsoft 在 Azure Vm 或 HANA 大型實例單位上支援一組有限的高可用性設定。 如需 HANA 大型實例的支援案例，請閱讀 [適用于 Hana 大型實例的檔支援案例](./hana-supported-scenario.md)。

針對 Azure Vm，DBMS 層級支援下列高可用性設定：

- SAP Hana 以 SUSE 和 Red Hat 為基礎的 Linux Pacemaker 進行系統複寫。 請參閱詳細文章：
    - [SUSE Linux Enterprise Server 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability.md) \(部分機器翻譯\)
    - [Red Hat Enterprise Linux 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md) \(部分機器翻譯\)
- 使用 SUSE 和 Red Hat 上的 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) ，SAP Hana 相應放大 n + m 設定。 下列文章列出詳細資料：
    - [使用 SUSE Linux Enterprise Server} 上的 Azure NetApp Files，在 Azure Vm 上部署具有待命節點的 SAP Hana 相應放大系統](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [在 Red Hat Enterprise Linux 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md) \(部分機器翻譯\)
- 根據 Windows 向外延展檔服務 SQL Server 容錯移轉叢集。 雖然生產系統的建議是使用 SQL Server Always On，而不是群集。 SQL Server Always On 使用個別儲存體提供更佳的可用性。 本文將說明詳細資料： 
    - [在 Azure 虛擬機器上設定 SQL Server 容錯移轉叢集執行個體](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- 適用于 Azure 上 SQL Server 的 Windows 作業系統支援 SQL Server Always On。 這是 Azure 上生產 SQL Server 實例的預設建議。 這些文章會說明詳細資料：
    - [Azure 虛擬機器上的 SQL Server Always On 可用性群組簡介](../../../azure-sql/virtual-machines/windows/availability-group-overview.md) \(機器翻譯\)。
    - [在不同區域的 Azure 虛擬機器上設定 Always On 可用性群組](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md) \(機器翻譯\)。
    - [在 Azure 中設定 Always On 可用性群組的負載平衡器](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md) \(機器翻譯\)。
- 適用于 Windows 和 Oracle Linux 的 Oracle Data Guard。 您可以在本文中找到 Oracle Linux 的詳細資料：
    - [在 Azure Linux 虛擬機器上實作 Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
- 這裡提供適用于 suse 和 RHEL 的 suse 和 rhel 詳細檔（使用 Pacemaker）的 IBM Db2 HADR：
    - [使用 Pacemaker SUSE Linux Enterprise Server 的 Azure Vm 上的 IBM Db2 LUW 高可用性](./dbms-guide-ha-ibm.md)
    - [Red Hat Enterprise Linux Server 上 Azure VM 的 IBM Db2 LUW 高可用性](./high-availability-guide-rhel-ibm-db2-luw.md)
- SAP ASE 和 SAP maxDB 設定，如下列檔中所述：
    - [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](./dbms_guide_sapase.md)
    - [Azure VM 上的 SAP MaxDB、liveCache 與內容伺服器部署](./dbms_guide_maxdb.md)
- HANA 大型實例高可用性案例詳述于：
    - [適用于 HANA 大型實例的支援案例-HSR 與 STONITH 以獲得高可用性](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [適用于 HANA 大型實例的支援案例-主機自動容錯移轉 (1 + 1) ](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> 針對上述所有案例，我們支援一個 VM 中的多個 DBMS 實例設定。 在每個案例中，每個 VM 只能部署一個資料庫實例，並使用所述的高可用性方法加以保護。 目前 **不** 支援在相同的 Windows 或 Pacemaker 容錯移轉叢集下保護多個 DBMS 實例。 此外，每個 VM 部署案例的單一實例也支援 Oracle Data Guard。 

各種資料庫系統都可讓您在一個 DBMS 實例下裝載多個資料庫。 在 SAP Hana 的情況下，多個資料庫可以裝載在多個資料庫容器中 (MDC) 。 在單一容錯移轉叢集資源內進行這些多資料庫設定的情況下，支援這些設定。 不支援的設定是需要多個叢集資源的情況。 適用于在一個 SQL Server 實例下定義多個 SQL Server 可用性群組的設定。


![DBMS HA 設定](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

視 DBMS 的/或作業系統而定，Azure 負載平衡器等元件可能會或可能不需要作為解決方案架構的一部分。 

尤其針對 maxDB，儲存體設定必須不同。 在 maxDB 中，資料和記錄檔必須位於共用存放裝置上，才能進行高可用性設定。 只有在 maxDB 的情況下，才支援共用儲存體以提供高可用性。 針對每個節點的所有其他 DBMS 個別儲存體堆疊，是唯一支援的磁片設定。

其他高可用性架構已知存在，而且也知道要在 Microsoft Azure 上執行。 不過，Microsoft 並不會測試這些架構。 如果您想要使用這些架構來建立高可用性設定，您將必須與該軟體的提供者合作，以：

- 開發部署架構
- 架構的部署
- 架構支援

> [!IMPORTANT]
> Microsoft Azure Marketplace 提供各種不同的軟設備，可在 Azure 原生儲存體上提供儲存體解決方案。 這些軟設備也可用來建立 NFS 共用，而且理論上可以在需要待命節點的 SAP Hana 向外延展部署中使用。 由於各種原因，Microsoft 和 Azure 上的 SAP 的任何 DBMS 部署都不支援這些儲存體軟設備。 目前並不支援在 SMB 共用上部署 DBMS。 NFS 共用上的 DBMS 部署僅限於 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)上的 NFS 4.1 共用。


## <a name="high-availability-for-sap-central-service"></a>SAP 中央服務的高可用性
SAP Central Services 是 SAP 設定的第二次失敗點。 因此，您也需要保護這些中央服務處理常式。 支援和記載的 SAP 工作負載的供應專案如下：

- Windows 容錯移轉叢集伺服器使用 Windows 向外延展檔案服務進行 sapmnt 和全域傳輸目錄。 詳細資料將在本文中說明：
    - [在 Azure 中使用檔案共用於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-file-share.md)
    - [使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Windows 容錯移轉叢集伺服器，其使用以適用于 sapmnt 和全域傳輸目錄的 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 為基礎的 SMB 共用。 詳細資料列于文章：
    - [Sap NetWeaver 在 Windows 上的 Azure Vm 上的高可用性，適用于 SAP 應用程式的 Azure NetApp Files (SMB) ](./high-availability-guide-windows-netapp-files-smb.md)
- 根據 SIOS 的 Windows 容錯移轉叢集伺服器 `Datakeeper` 。 雖然 Microsoft 記載了，但您需要與 SIOS 的支援關聯性，因此，您可以在使用此解決方案時與 SIOS 支援互動。 詳細資料將在本文中說明：
    - [在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [使用 SAP ASCS/SCS 的 Windows 容錯移轉叢集和共用磁碟，為 SAP HA 準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- 使用兩個 SUSE Vm 和檔案複寫來建立高可用性 NFS 共用的 SUSE 作業系統上的 Pacemaker `drdb` 。 詳細資料記載于文章
    - [SAP NetWeaver 在適用於 SAP 應用程式之 SUSE Linux Enterprise Server 上的 Azure VM 高可用性](./high-availability-guide-suse.md)
    - [SUSE Linux Enterprise Server 上 Azure VM 的 NFS 高可用性](./high-availability-guide-suse-nfs.md)
- 利用 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)提供的 NFS 共用 Pacemaker SUSE 作業系統。 詳細資料記載于
    - [針對 SAP 應用程式使用 Azure NetApp Files 在 SUSE Linux Enterprise Server 上的 Azure VM 達到 SAP NetWeaver 高可用性](./high-availability-guide-suse-netapp-files.md)
- Pacemaker 在叢集上託管 NFS 共用的 Red Hat 作業系統上 `glusterfs` 。 您可以在文章中找到詳細資料
    - [SAP NetWeaver on Red Hat Enterprise Linux 的 Azure 虛擬機器高可用性](./high-availability-guide-rhel.md)
    - [`GlusterFS` 適用于 SAP NetWeaver Red Hat Enterprise Linux 上的 Azure Vm](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker 在 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)上裝載了 NFS 共用的 Red Hat 作業系統上。 詳細資料將在本文中說明
    - [適用于 sap Red Hat Enterprise Linux NetWeaver 的 azure 虛擬機器高可用性，適用于 SAP 應用程式的 Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)

在所列的解決方案中，您需要支援與 SIOS 的關聯性，以支援 `Datakeeper` 產品，並在發生問題時直接與 sios 進行互動。 根據您授權 Windows、Red Hat 及/或 SUSE OS 的方式，您也可能需要與您的作業系統提供者簽訂支援合約，才能完全支援所列的高可用性設定。

設定也可以顯示如下：

![DBMS 和 ASCS HA 設定](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

圖形右側會顯示高可用性的 SAP Central Services。 除了使用容錯移轉叢集架構來保護 SAP Central services，以在發生問題時進行容錯移轉，也需要高可用性的 NFS 或 SMB 共用，或 Windows 共用磁片，以確保 sapmnt 和全域傳輸目錄可以獨立于單一 VM 存在時使用。 其他一些解決方案，例如 Windows 容錯移轉叢集伺服器和 Pacemaker，將需要 Azure 負載平衡器將流量導向或重新導向至狀況良好的節點。

在顯示的清單中，並沒有提及 Oracle Linux 作業系統。 Oracle Linux 不支援將 Pacemaker 作為叢集架構。 如果您想要在 Oracle Linux 上部署 SAP 系統，而且您需要適用于 Oracle Linux 的高可用性架構，則必須使用協力廠商的供應商。 其中一個供應商是使用其適用于 Linux 的保護套件 SIOS，Azure 上的 SAP 支援此套件。 如需詳細資訊，請參閱 SAP 附注 [#1662610-適用于 Linux 的 SIOS Protection Suite 支援詳細資料](https://launchpad.support.sap.com/#/notes/1662610) ，以取得更多詳細資料。



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>上面所列的 SAP Central Services 案例支援的儲存體
由於只有一部分的 Azure 儲存體類型會提供高可用性的 NFS 或 SMB 共用，因此我們的 SAP Central Services 叢集案例中的使用量品質會是支援的儲存體類型清單

- Windows 容錯移轉叢集伺服器與 Windows 向外延展檔案伺服器可以部署在 Azure NetApp Files 以外的所有原生 Azure 儲存體類型。 不過，建議您利用進階儲存體，因為輸送量和 IOPS 中有優異的服務等級協定。
- Azure NetApp Files 支援在 Azure NetApp Files 上使用 SMB 的 Windows 容錯移轉叢集伺服器。 此時間點 **不** 支援 Azure 檔案服務上的 SMB 共用。
- 具有以 SIOS 為基礎之 windows 共用磁片的 windows 容錯移轉叢集伺服器 `Datakeeper` ，可以部署在 Azure NetApp Files 以外的所有原生 azure 儲存體類型上。 不過，建議您利用進階儲存體，因為輸送量和 IOPS 中有優異的服務等級協定。
- Azure NetApp Files 支援在 Azure NetApp Files 上使用 NFS 共用的 SUSE 或 Red Hat Pacemaker。 
- 使用 `drdb` Azure NetApp Files 以外的原生 azure 儲存體類型，可支援在兩個 vm 之間使用設定的 SUSE Pacemaker。 不過，建議您利用進階儲存體，因為輸送量和 IOPS 中有優異的服務等級協定。
- 使用 `glusterfs` Azure NetApp Files 以外的原生 azure 儲存體類型，可支援使用提供 NFS 共用的 Red Hat Pacemaker。 不過，建議您利用進階儲存體，因為輸送量和 IOPS 中有優異的服務等級協定。

> [!IMPORTANT]
> Microsoft Azure Marketplace 提供各種不同的軟設備，可在 Azure 原生儲存體上提供儲存體解決方案。 這些軟設備也可以用來建立 NFS 或 SMB 共用，理論上也可用於容錯移轉叢集的 SAP Central Services 中。 Microsoft 不直接支援這些解決方案的 SAP 工作負載。 如果您決定使用這類解決方案來建立您的 NFS 或 SMB 共用，則必須由在存放裝置軟設備中擁有軟體的協力廠商提供 SAP Central Service 設定的支援。


## <a name="multi-sid-sap-central-services-failover-clusters"></a>多重 SID SAP Central Services 容錯移轉叢集
為了減少大型 SAP 環境中所需的 Vm 數目，SAP 可讓您在容錯移轉叢集設定中，執行多個不同 SAP 系統的 SAP 中央服務實例。 想像您有30個以上的 NetWeaver 或 S/4HANA 生產系統的情況。 如果沒有多重 SID 叢集，這些設定會要求在30個以上的 Windows 或 Pacemaker 容錯移轉叢集設定中有60或更多的 Vm。 除了所需的 DBMS 容錯移轉叢集之外。 在容錯移轉叢集設定的兩個節點之間部署多個 SAP central services，可以大幅減少 Vm 數目。 不過，在單一雙節點叢集設定上部署多個 SAP 中央服務實例也有一些缺點。 叢集中單一 VM 的相關問題適用于多個 SAP 系統。 在叢集中執行的虛擬作業系統上進行維護需要更多的協調，因為有多個生產環境 SAP 系統受到影響。 SAP LaMa 之類的工具在其系統複製程式中不支援多重 SID 叢集。

在 Azure 上，具有 ENSA1 和 ENSA2 的 Windows 作業系統支援多重 SID 叢集設定。 建議您不要將舊版的排入佇列複寫服務架構 (ENSA1) 與新架構 (ENSA2) 在一個多重 SID 叢集上。 這類架構的詳細資料記載于文章中。

- [在 Azure 上搭配 Windows Server 容錯移轉叢集和共用磁碟的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [在 Azure 上搭配 Windows Server 容錯移轉叢集和檔案共用的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

針對 SUSE，也支援以 Pacemaker 為基礎的多 SID 叢集。 到目前為止，支援的設定如下：

- 最多五個 SAP ASCS/SCS 實例
- 舊的佇列複寫伺服器 ice 架構 (ENSA1) 
- 兩個節點的 Pacemaker 叢集設定

在 [sap 應用程式的 SUSE Linux Enterprise Server 上，Azure vm 上的 Sap NetWeaver 的高可用性記錄了多重 SID 指南](./high-availability-guide-suse-multi-sid.md)

具有排入佇列複寫伺服器架構上的多重 SID 叢集看起來像

![DBMS 和 ASCS HA 設定](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP Hana 相應放大案例
SAP Hana 的向外延展案例支援 [SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中所列的 HANA 認證 Azure vm 的子集。 在 [叢集] 資料行中標示為 [是] 的所有 Vm，都可用於 OLAP 或 S/4HANA 向外延展。下列 Azure 儲存體類型支援不含待命的設定： 

- Azure 進階儲存體，包括/hana/log 磁片區的 Azure 寫入加速器
- [Ultra 磁碟](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

具有待命節點 (s 的 OLAP 或 S/4HANA 的向外延展設定 SAP Hana，會以 Azure NetApp Files 上裝載的 NFS 共用為獨佔支援) 。

如需具有或不含待命節點之確切儲存體設定的詳細資訊，請參閱下列文章：

- [SAP HANA Azure 虛擬機器儲存體設定](./hana-vm-operations-storage.md) 
- [在 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-suse.md) \(部分機器翻譯\)
- [在 Red Hat Enterprise Linux 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md) \(部分機器翻譯\)
- [SAP 支援附注 #2080991](https://launchpad.support.sap.com/#/notes/2080991)

如需 HANA 大型實例支援 HANA 相應放大設定的詳細資訊，請參閱下列檔：

- [支援使用待命的 HANA 大型實例擴充案例](./hana-supported-scenario.md#scale-out-with-standby)
- [不含待命的 HANA 大型實例相應放大的支援案例](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>嚴重損壞修復案例
支援各種嚴重損壞修復案例。 我們會將嚴重損壞架構定義為應彌補整個方格的完整 Azure 區域的架構。 這表示我們需要嚴重損壞修復目標與目標不同的 Azure 區域，才能執行您的 SAP 環境。 我們會在 DBMS 層和非 DBMS 層中分隔方法和設定。 

### <a name="dbms-layer"></a>DBMS 層
若是 DBMS 層，則會支援使用 DBMS 原生複寫機制的設定，例如 Always On、Oracle Data Guard、Db2 HADR、SAP ASE Always On 或 HANA 系統複寫。 在這種情況下，複寫資料流程必須是非同步，而不是同步，如同在單一 Azure 區域中部署的一般高可用性案例中。 這類支援的 DBMS 嚴重損壞修復設定的典型範例，請參閱 [SAP Hana 跨 Azure 區域的可用性](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions)。 該章節中的第二個圖形描述 HANA 作為範例的案例。 SAP 應用程式所支援的主資料庫，全都可以在這種情況下部署。

支援使用較小的 VM 作為嚴重損壞修復區域中的目標實例，因為該 VM 不會經歷完整的工作負載流量。 這樣做時，您必須記住下列考慮：

- 較小的 VM 類型不允許連接到較小 Vm 的許多磁片
- 較小的 Vm 的網路和儲存體輸送量較少
- 在一個 Azure 可用性設定組中收集不同的 Vm，或在 M 系列系列和 Mv2 系列的 Vm 之間進行調整大小調整時，VM 系列間的重新調整可能會造成問題
- 資料庫實例的 CPU 和記憶體耗用量，能夠以最小延遲和足夠的 CPU 和記憶體資源，以最小的延遲將這些變更套用至資料，以套用這些變更  

您可以在[這裡](../../sizes.md)找到不同 VM 大小限制的詳細資料 

部署 DR 目標的另一種支援方法是在執行非生產環境 SAP 實例之非生產 DBMS 實例的 VM 上安裝第二個 DBMS 實例。 這可能有點困難，因為您需要找出必須在 DR 案例中作為主要實例的特定目標實例所需的記憶體、CPU 資源、網路頻寬和儲存體頻寬。 尤其是在 HANA 中，強烈建議您設定在共用主機上做為 DR 目標的實例，如此一來，就不會將資料預先載入 DR 目標實例。

針對「HANA 大型實例 DR」案例，請查看下列檔：

- [使用儲存體複寫進行 DR 的單一節點](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [具有 DR (多用途) 使用儲存體複寫的單一節點](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [具有 DR (多用途) 使用儲存體複寫的單一節點](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [具有 HSR 和 DR 的高可用性與儲存體複寫](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [使用儲存體複寫來向外擴充 DR](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [使用 HSR 進行 DR 的單一節點](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [單一節點 HSR 至 DR (成本優化) ](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [使用 HSR 的高可用性和嚴重損壞修復](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [使用 HSR (成本優化) 的高可用性和嚴重損壞修復 ](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [使用 HSR 向外擴充 DR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> 使用 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 尚未針對 SAP 工作負載下的 DBMS 部署進行測試。 因此，SAP 系統的 DBMS 層目前不支援此功能。 Microsoft 和 SAP 未列出的其他複寫方法不受支援。 使用協力廠商軟體在不同的 Azure 區域之間複寫 SAP 系統的 DBMS 層時，必須由軟體廠商支援，而且不會透過 Microsoft 和 SAP 支援通道提供支援。 
 
## <a name="non-dbms-layer"></a>非 DBMS 層
針對 SAP 應用層以及所需的最終共用或儲存位置，客戶可利用兩種主要案例：

- 第二個 Azure 區域中的嚴重損壞修復目標並未用於任何生產或非生產用途。 在此案例中，做為嚴重損壞修復目標的 Vm，在理想情況下並不會部署，而且會將實際執行 SAP 應用層的映射和變更複寫到嚴重損壞修復區域。 [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md)可以執行這類工作的功能。 Azure Site Recovery 支援像這樣的 Azure 至 Azure 複寫案例。 
- 嚴重損壞修復的目標是非生產系統實際使用的 Vm。 整個 SAP 架構會分散到兩個不同的 Azure 區域，而生產系統通常會在另一個區域中的一個區域和非生產系統中。 在許多客戶部署中，客戶都有相當於生產系統的非生產系統。 客戶已在應用層非生產系統上預先安裝生產環境應用程式實例。 如果發生容錯移轉，就會關閉非生產實例、將生產 Vm 的虛擬名稱移至非生產 Vm (在 DNS) 中指派新的 IP 位址之後，以及預先安裝的生產實例開始使用

### <a name="sap-central-services-clusters"></a>SAP 中央服務叢集
使用共用磁片 (Windows) 的 SAP 中央服務叢集， (Windows) 或 NFS 共用的 SMB 共用比較難複寫。 在 Windows 端，Windows Storage Replication 是可能的解決方案。 在 Linux rsync 上是可行的解決方案。



## <a name="non-supported-scenario"></a>不支援的案例
Azure 架構上的 SAP 工作負載不支援案例清單。 「**不支援**」表示 SAP 和 Microsoft 將無法支援這些設定，且需要延遲至最終相關的協力廠商，以提供軟體來建立這類架構。 其中兩個類別為：

- 存放裝置軟設備： Azure marketplace 中提供了一些儲存體軟體設備。 有些廠商會提供自己的檔，說明如何在 Azure 上使用與 SAP 軟體相關的存放裝置軟設備。 這些存放裝置軟設備的廠商必須提供支援此類存放裝置軟設備的設定或部署。 這項事實也會在 [SAP 支援附注中指出 #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- 高可用性架構： Azure 上的 SAP 工作負載只有 Pacemaker 和 Windows Server 容錯移轉叢集支援高可用性架構。 如先前所述，將會說明 SIOS 的解決方案， `Datakeeper` 並記載于 Microsoft。 不過，必須 `Datakeeper` 透過 sios 來支援 sios 的元件，因為廠商提供這些元件。 SAP 也列出各種 SAP 附注中的其他經認證的高可用性架構。 其中有些是由 Azure 的協力廠商廠商認證。 不過，支援使用這些產品的設定必須由產品廠商提供。 不同的廠商在 SAP 支援流程中有不同的整合。 在決定在 Azure 上部署的 SAP 設定中使用該產品之前，您應該先瞭解哪些支援流程最適合特定廠商。
- MaxDB 不支援資料庫檔案位於共用磁片上的共用磁片叢集。 針對所有其他資料庫，支援的解決方案是使用不同的儲存位置，而不是 SMB 或 NFS 共用或共用磁片來設定高可用性案例

不支援的其他案例如下所示：

- 在 sap 應用層和 sap DBMS 層的 sap 應用層和 sap DBMS 層之間引進較大網路延遲的部署案例，如 NetWeaver、S/4HANA 所示，例如 `Hybris` 這包括：
    - 部署其中一層內部部署，而另一層則部署在 Azure 中
    - 在與 DBMS 層不同的 Azure 區域中部署系統的 SAP 應用層
    - 在與 azure 共置的資料中心部署一層，但 azure 原生服務會提供這類架構模式除外
    - 在 SAP 應用層與 DBMS 層之間部署網路虛擬裝置
    - 利用裝載于資料中心（適用于 SAP DBMS 層或 SAP 全域傳輸目錄的 Azure 資料中心）的儲存體
    - 部署兩個具有兩個不同雲端廠商的層級。 例如，在 Azure 中部署 Oracle 雲端基礎結構和應用層中的 DBMS 層
- 多重實例 HANA Pacemaker 叢集設定
- 在 ANF 上透過 SOFS 或 SMB 使用共用磁片的 windows 叢集設定，適用于 Windows 上支援的 SAP 資料庫。 相反地，我們建議使用特定資料庫的原生高可用性複寫，並使用不同的儲存體堆疊。
- 在 Linux 上部署支援的 SAP 資料庫，並在 ANF 上使用位於 NFS 共用的資料庫檔案，但 SAP Hana 除外
- 在任何其他的虛擬作業系統上部署 Oracle DBMS，而非 Windows 和 Oracle Linux。 另請參閱 [SAP 支援附注 #2039619](https://launchpad.support.sap.com/#/notes/2039619)

案例 (s) 我們並未測試，因此不會有類似下列的清單：

- Azure Site Recovery 複寫 DBMS 層 Vm。 因此，我們建議您利用資料庫原生非同步複寫功能來進行潛在的嚴重損壞修復設定
 

## <a name="next-steps"></a>後續步驟
閱讀[適用于 SAP NetWeaver 的 Azure 虛擬機器規劃和執行](./planning-guide.md)中的後續步驟




  