---
title: Azure 上的 SAP： Azure Vm 的支援案例
description: Azure 虛擬機器 SAP 工作負載的支援案例
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
ms.openlocfilehash: 1945dc3b9fa03354ef447f813d95b6040a4b7b91
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833329"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 虛擬機器支援案例上的 SAP 工作負載
在 Azure 中設計 SAP NetWeaver、Business one `Hybris` 或 S/4HANA 系統架構，可為各種架構和工具開啟許多不同的機會，讓您能夠使用可調整、有效率且高可用性的部署。 雖然相依于使用的作業系統或 DBMS，但還是有一些限制。 此外，在 Azure 中，不支援在內部部署支援的所有案例。 本檔將會以獨佔方式使用 Azure Vm，以提供支援的非高可用性設定和高可用性設定和架構。 針對[Hana 大型實例](./hana-overview-architecture.md)支援的案例，請參閱[Hana 大型實例支援的案例](./hana-supported-scenario.md)一文。 


## <a name="2-tier-configuration"></a>2層設定
SAP 2 層設定會被視為在相同伺服器或 VM 單位上執行的 SAP DBMS 和應用層的組合層中建立。 第二層會被視為使用者介面層。 在2層設定的案例中，DBMS 和 SAP 應用層會共用 Azure VM 的資源。 因此，您必須以那些不會競爭資源的方式來設定不同的元件。 您也必須小心，不要過度訂閱 VM 的資源。 這種設定並不會提供任何高可用性，除了涉及不同 Azure 元件的[Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)之外。

這類設定的圖形標記法如下所示：

![簡單的2層設定](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

針對適用于生產和非生產案例的 SQL Server、Oracle、Db2、maxDB 和 SAP ASE 的 DBMS 系統，Windows、Red Hat、SUSE 和 Oracle Linux 都支援這類設定。 針對做為 DBMS 的 SAP Hana，只有非生產案例才支援這種類型的設定。 這也包括[AZURE HANA 大型實例](./hana-overview-architecture.md)的部署案例。
針對 Azure 上支援的所有 OS/DBMS 組合，支援這種類型的設定。 不過，您必須以 DBMS 和 SAP 元件不會競爭記憶體和 CPU 資源，因而超過實體可用資源的方式，設定 DBMS 和 SAP 元件的設定。 這必須藉由限制 DBMS 可配置的記憶體來完成。 您也需要限制應用程式實例上的 SAP 延伸記憶體。 您也需要監視整體 VM 的 CPU 耗用量，以確保元件不會最大化 CPU 資源。 

> [!NOTE]
> 針對生產環境 SAP 系統，我們建議額外的高可用性和最終的嚴重損壞修復設定，如本檔稍後所述


## <a name="3-tier-configuration"></a>3層設定
在這類設定中，您會將 SAP 應用層和 DBMS 層分隔成不同的 Vm。 您通常會針對較大的系統執行此動作，而不會因為 SAP 應用層的資源而有更多彈性。 在最簡單的設定中，除了涉及不同 Azure 元件的[Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)之外，沒有任何高可用性。 

圖形表示如下所示：

![簡單的2層設定](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

在適用于生產和非生產環境的 SQL Server、Oracle、Db2、SAP Hana、maxDB 和 SAP ASE 的 DBMS 系統上，Windows、Red Hat、SUSE 和 Oracle Linux 都支援這種類型的設定。 這是[AZURE HANA 大型實例](./hana-overview-architecture.md)的預設部署設定。 為了簡化，我們不會在 SAP 應用層中區分 SAP 中央服務和 SAP 對話實例。 在這個簡單的3層式設定中，SAP 中央服務不會有高可用性保護。

> [!NOTE]
> 針對生產環境 SAP 系統，我們建議額外的高可用性和最終的嚴重損壞修復設定，如本檔稍後所述


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>每個 VM 或 HANA 大型實例單位的多個 DBMS 實例
在此設定類型中，您會在每個 Azure VM 或 HANA 大型實例單位上裝載多個 DBMS 實例。 動機可能是要維護較少的作業系統，並降低成本。 其他動機可以藉由在多個 DBMS 實例之間共用較大型 VM 或 HANA 大型實例單位的資源，以提供更多彈性和更高的效率。 到目前為止，這些設定大部分都是針對非生產系統所顯示。

像這樣的設定可能如下所示：

![一個單位中的多個 DBMS 實例](./media/sap-planning-supported-configurations/multiple-database-instances.png)

支援這種類型的 DBMS 部署：

- Windows 上的 SQL Server
- IBM Db2。 在[Linux、UNIX (的多個實例](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)一文中尋找詳細資料) 
- 針對 Oracle。 如需詳細資訊，請參閱[sap 支援附注 #1778431](https://launchpad.support.sap.com/#/notes/1778431)和相關的 sap 附注
- 針對 SAP Hana，支援在一個 VM 上有多個實例，SAP 會呼叫這個部署方法 MCOS。 如需詳細資訊，請參閱 SAP 文章 [一部主機上的多個 SAP Hana 系統 (MCOS) ] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html) 

在一部主機上執行多個資料庫實例，您必須確定不同的實例不會競爭資源，因而超出 VM 的實體資源限制。 特別是當您需要將記憶體上限為共用 VM 所能配置之實例的任何人時，就會發生這種情況。 這也可能適用于不同資料庫實例可以利用的 CPU 資源。 所有提及的 DBMS 都有設定，可限制實例層級的記憶體配置和 CPU 資源。
為了支援 Azure Vm 的這類設定，預期用於不同實例所管理之資料庫的資料和記錄/重做記錄檔的磁片或磁片區是分開的。 換句話說，其他 DBMS 實例所管理之資料庫的資料或記錄/重做記錄檔，不應該共用相同的磁片或磁片區。 

HANA 大型實例的磁片設定已進行傳遞，並會在[Hana 大型實例的支援案例](./hana-supported-scenario.md#single-node-mcos)中詳述。 

> [!NOTE]
> 針對生產環境 SAP 系統，我們建議額外的高可用性和最終的嚴重損壞修復設定，如本檔稍後所述。 如本檔稍後所述的高可用性設定，不支援具有多個 DBMS 實例的 Vm。


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>一個 VM 中的多個 SAP 對話實例
在許多情況下，會將多個對話方塊實例部署在裸機伺服器上，甚至是在私人雲端中執行的 Vm 中。 這類設定的原因是要將某些 SAP 對話實例量身打造至特定工作負載、商務功能或工作負載類型。 不將這些實例隔離到不同 Vm 的原因，是作業系統維護和作業的工作。 或者，在許多情況下，當 VM 的主機服務提供者或操作員要求每個 VM 操作和 administrated 的每月費用時，就會發生成本。 在 Azure 中，在單一 VM 內裝載多個 SAP 對話實例的案例，我們支援在 Windows、Red Hat、SUSE 和 Oracle Linux 的作業系統上進行生產和非生產用途。 如果在單一 VM 上執行多個 SAP 應用程式伺服器實例，則應該設定 SAP 核心參數 PHYS_MEMSIZE （適用于 Windows 和新式 Linux 核心）。 此外，也建議您限制擴充作業系統上的 SAP 延伸記憶體，例如，執行自動成長 SAP 延伸記憶體的 Windows。 這可以使用 SAP 設定檔參數來完成 `em/max_size_MB` 。

在3層設定中，在 Azure Vm 內執行多個 SAP 對話方塊實例的方式可能如下所示：

![一個單位中的多個 DBMS 實例](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

為了簡化，我們不會在 SAP 應用層中區分 SAP 中央服務和 SAP 對話實例。 在這個簡單的3層式設定中，SAP 中央服務不會有高可用性保護。 針對生產系統，不建議將 SAP 中央服務保持在未受保護狀態。 如需有關在 SAP Central 實例和高可用性的情況下，呼叫多 sid 設定的詳細資訊，請參閱本檔稍後的章節。

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS 層的高可用性保護
當您想要部署 SAP 生產系統時，您必須考慮高可用性設定的熱待命類型。 特別是在 SAP Hana 中，必須將資料載入記憶體才能取得完整的效能和擴充性，Azure 服務修復並不是高可用性的理想測量標準。 

在一般情況下，Microsoft 僅支援 docs.microsoft.com 的 SAP 工作負載一節底下所述的高可用性設定和軟體套件。 您可以在 SAP 附注[#1928533](https://launchpad.support.sap.com/#/notes/1928533)中讀取相同的語句。 Microsoft 不會提供 Microsoft 與 SAP 工作負載一起記載之其他高可用性協力廠商軟體架構的支援。 在這種情況下，高可用性架構的協力廠商供應商是高可用性設定的支援方，必須由您做為客戶參與支援程式。 本文將提及例外狀況。 

在一般情況中，Microsoft 會在 Azure Vm 或 HANA 大型實例單位上支援一組有限的高可用性設定。 如需 HANA 大型實例的支援案例，請閱讀[適用于 Hana 大型實例的支援案例](./hana-supported-scenario.md)檔。

針對 Azure Vm，DBMS 層級支援下列高可用性設定：

- 根據 SUSE 和 Red Hat 上的 Linux Pacemaker，SAP Hana 系統複寫。 請參閱詳細文章：
    - [SUSE Linux Enterprise Server 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability.md) \(部分機器翻譯\)
    - [Red Hat Enterprise Linux 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md) \(部分機器翻譯\)
- 使用 SUSE 和 Red Hat 上的[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) ，SAP Hana 相應放大 n + m 設定。 詳細資料列于下列文章中：
    - [使用 azure NetApp Files on SUSE Linux Enterprise Server} 在 Azure Vm 上部署具有待命節點的 SAP Hana 相應放大系統](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [在 Red Hat Enterprise Linux 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md) \(部分機器翻譯\)
- SQL Server 以 Windows 向外延展檔案服務為基礎的容錯移轉叢集。 雖然生產系統的建議是使用 SQL Server Always On，而不是叢集。 SQL Server Always On 使用個別的儲存體來提供更好的可用性。 本文會說明詳細資料： 
    - [在 Azure 虛擬機器上設定 SQL Server 容錯移轉叢集執行個體](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- 適用于 Azure 上 SQL Server 的 Windows 作業系統支援 SQL Server Always On。 這是 Azure 上生產 SQL Server 實例的預設建議。 這些文章會說明詳細資料：
    - [Azure 虛擬機器上的 SQL Server Always On 可用性群組簡介](../../../azure-sql/virtual-machines/windows/availability-group-overview.md) \(機器翻譯\)。
    - [在不同區域的 Azure 虛擬機器上設定 Always On 可用性群組](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md) \(機器翻譯\)。
    - [在 Azure 中設定 Always On 可用性群組的負載平衡器](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md) \(機器翻譯\)。
- 適用于 Windows 和 Oracle Linux 的 Oracle Data Guard。 如需 Oracle Linux 的詳細資料，請參閱這篇文章：
    - [在 Azure Linux 虛擬機器上實作 Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
- 如需 suse 和 rhel 的 IBM Db2 HADR，請參閱使用 Pacemaker 的 SUSE 和 RHEL 詳細檔，如下所示：
    - [使用 Pacemaker SUSE Linux Enterprise Server 上的 Azure Vm 上的 IBM Db2 LUW 高可用性](./dbms-guide-ha-ibm.md)
    - [Red Hat Enterprise Linux Server 上 Azure VM 的 IBM Db2 LUW 高可用性](./high-availability-guide-rhel-ibm-db2-luw.md)
- SAP ASE 和 SAP maxDB 設定，如下列檔所述：
    - [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](./dbms_guide_sapase.md)
    - [Azure VM 上的 SAP MaxDB、liveCache 與內容伺服器部署](./dbms_guide_maxdb.md)
- HANA 大型實例高可用性案例詳述于：
    - [適用于 HANA 大型實例的支援案例-使用 STONITH HSR 以提供高可用性](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [適用于 HANA 大型實例的支援案例-主機自動容錯移轉 (1 + 1) ](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> 針對上述所有案例，我們都支援在一個 VM 中設定多個 DBMS 實例。 表示在每個案例中，每個 VM 只能部署一個資料庫實例，並使用所述的高可用性方法進行保護。 目前**不**支援在相同的 Windows 或 Pacemaker 容錯移轉叢集下保護多個 DBMS 實例。 而且僅針對每個 VM 部署案例的單一實例支援 Oracle Data Guard。 

各種資料庫系統允許在一個 DBMS 實例下裝載多個資料庫。 就 SAP Hana 而言，多個資料庫可以裝載在多個資料庫容器中， (MDC) 。 針對這些多資料庫設定在一個容錯移轉叢集資源內運作的情況，支援這些設定。 不支援的設定是需要多個叢集資源的情況。 針對您要在一個 SQL Server 實例下定義多個 SQL Server 可用性群組的設定。


![DBMS HA 設定](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

視 DBMS a/或作業系統而定，Azure 負載平衡器等元件可能不需要做為解決方案架構的一部分。 

特別是針對 maxDB，存放裝置設定必須不同。 在 maxDB 中，資料和記錄檔必須位於共用存放裝置上，才能進行高可用性設定。 只有在 maxDB 的情況下，才支援共用儲存體以提供高可用性。 針對每個節點的所有其他 DBMS 個別儲存體堆疊，是唯一支援的磁片設定。

其他高可用性架構已知存在，而且也已知在 Microsoft Azure 上執行。 不過，Microsoft 並不會測試這些架構。 如果您想要使用這些架構來建立高可用性設定，就必須與該軟體的提供者合作，以：

- 開發部署架構
- 架構的部署
- 架構的支援

> [!IMPORTANT]
> Microsoft Azure Marketplace 提供各種不同的軟設備，可在 Azure 原生儲存體上提供儲存體解決方案。 這些軟設備也可用來建立 NFS 共用，理論上可以在需要待命節點的 SAP Hana 向外延展部署中使用。 由於各種原因，Microsoft 和 Azure 上的 SAP 的任何 DBMS 部署都不支援這些儲存體軟設備。 目前不支援在 SMB 共用上部署 DBMS。 NFS 共用上的 DBMS 部署僅限於[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)上的 NFS 4.1 共用。


## <a name="high-availability-for-sap-central-service"></a>SAP 中央服務的高可用性
SAP 中央服務是 SAP 設定的第二次失敗點。 因此，您也必須保護這些中央服務處理常式。 針對 SAP 工作負載讀取支援和記載的供應專案如下：

- 使用 Windows 向外延展檔案服務進行 sapmnt 和全域傳輸目錄的 windows 容錯移轉叢集伺服器。 詳細資料請參閱下列文章：
    - [在 Azure 中使用檔案共用於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-file-share.md)
    - [使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Windows 容錯移轉叢集伺服器使用以適用于 sapmnt 和全域傳輸目錄的[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)為基礎的 SMB 共用。 詳細資料列于下列文章中：
    - [使用 Azure NetApp Files 在 Windows 上的 Azure Vm 上進行 SAP NetWeaver 的高可用性，適用于 SAP 應用程式 (SMB) ](./high-availability-guide-windows-netapp-files-smb.md)
- 以 SIOS 為基礎的 Windows 容錯移轉叢集伺服器 `Datakeeper` 。 雖然 Microsoft 已記載，但您需要 SIOS 的支援關聯性，因此您可以在使用此解決方案時，與 SIOS 支援互動。 詳細資料請參閱下列文章：
    - [在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [使用 SAP ASCS/SCS 的 Windows 容錯移轉叢集和共用磁碟，為 SAP HA 準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- 在 SUSE 作業系統上 Pacemaker，並建立使用兩個 SUSE Vm 和檔案複寫的高可用性 NFS 共用 `drdb` 。 詳細資料記載于文章
    - [SAP NetWeaver 在適用於 SAP 應用程式之 SUSE Linux Enterprise Server 上的 Azure VM 高可用性](./high-availability-guide-suse.md)
    - [SUSE Linux Enterprise Server 上 Azure VM 的 NFS 高可用性](./high-availability-guide-suse-nfs.md)
- 利用[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)提供的 NFS 共用來 Pacemaker SUSE 作業系統。 詳細資料記載于
    - [針對 SAP 應用程式使用 Azure NetApp Files 在 SUSE Linux Enterprise Server 上的 Azure VM 達到 SAP NetWeaver 高可用性](./high-availability-guide-suse-netapp-files.md)
- 在叢集上裝載 NFS 共用的 Red Hat 作業系統上 Pacemaker `glusterfs` 。 您可以在文章中找到詳細資料
    - [SAP NetWeaver on Red Hat Enterprise Linux 的 Azure 虛擬機器高可用性](./high-availability-guide-rhel.md)
    - [`GlusterFS`在適用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure Vm 上](./high-availability-guide-rhel-glusterfs.md)
- 在具有裝載于[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)之 NFS 共用的 Red Hat 作業系統上 Pacemaker。 詳細資料如文章所述
    - [Azure 虛擬機器高可用性，適用于 Red Hat Enterprise Linux 上的 SAP NetWeaver，以及適用于 SAP 應用程式的 Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)

在列出的解決方案中，您需要支援與 SIOS 的關聯性，以支援 `Datakeeper` 產品，並在發生問題時直接與 sios 進行互動。 根據您授權 Windows、Red Hat 和（或） SUSE OS 的方式而定，您可能也需要與您的作業系統提供者簽訂支援合約，才能完全支援列出的高可用性設定。

設定也會顯示如下：

![DBMS 和 ASCS HA 設定](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

在圖形右側，會顯示高可用性的 SAP 中央服務。 除了使用容錯移轉叢集架構來保護 SAP 中央服務，以在發生問題時容錯移轉，也需要高可用性 NFS 或 SMB 共用，或是 Windows 共用磁片，以確保 sapmnt 和全域傳輸目錄可供使用，而不受單一 VM 的存在影響。 其他一些解決方案（例如 Windows 容錯移轉叢集伺服器和 Pacemaker）將需要 Azure 負載平衡器，以將流量導向或重新導向至狀況良好的節點。

在顯示的清單中，Oracle Linux 的作業系統沒有提及。 Oracle Linux 不支援以叢集架構的形式 Pacemaker。 如果您想要在 Oracle Linux 上部署 SAP 系統，而且需要 Oracle Linux 的高可用性架構，您必須使用協力廠商供應商。 其中一個供應商會使用其適用于 Linux 的保護套件（Azure 上的 SAP 支援）進行 SIOS。 如需詳細資訊，請參閱 SAP 附注[#1662610-適用于 LINUX SIOS 保護套件的支援詳細](https://launchpad.support.sap.com/#/notes/1662610)資料。



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>以上所列 SAP 中央服務案例支援的儲存體
因為只有 Azure 儲存體類型的子集會提供高可用性 NFS 或 SMB 共用，以確保 SAP 中央服務叢集案例中的使用量品質，所以支援的儲存體類型清單

- 具有 Windows 向外延展檔案伺服器的 windows 容錯移轉叢集伺服器可以部署在 Azure NetApp Files 以外的所有原生 Azure 儲存體類型上。 不過，建議使用進階儲存體，因為輸送量和 IOPS 中的服務等級協定較佳。
- Azure netapp files 支援具有 SMB on Azure NetApp Files 的 Windows 容錯移轉叢集伺服器。 目前**不**支援 Azure 檔案服務上的 SMB 共用。
- Windows 容錯移轉叢集伺服器具有以 SIOS 為基礎的 windows 共用磁片 `Datakeeper` ，可以部署在 Azure NetApp Files 以外的所有原生 azure 儲存體類型上。 不過，建議使用進階儲存體，因為輸送量和 IOPS 中的服務等級協定較佳。
- Azure NetApp Files 支援在 Azure NetApp Files 上使用 NFS 共用的 SUSE 或 Red Hat Pacemaker。 
- 使用 `drdb` Azure NetApp Files 以外的原生 azure 儲存體類型，可支援在兩個 vm 之間使用設定的 SUSE Pacemaker。 不過，建議使用進階儲存體，因為輸送量和 IOPS 中的服務等級協定較佳。
- `glusterfs`使用 Azure NetApp Files 以外的原生 azure 儲存體類型，可支援用於提供 NFS 共用的 Red Hat Pacemaker。 不過，建議使用進階儲存體，因為輸送量和 IOPS 中的服務等級協定較佳。

> [!IMPORTANT]
> Microsoft Azure Marketplace 提供各種不同的軟設備，可在 Azure 原生儲存體上提供儲存體解決方案。 這些軟設備也可用來建立 NFS 或 SMB 共用，理論上也可以在容錯移轉叢集的 SAP 中央服務中使用。 Microsoft 不直接支援這些解決方案。 如果您決定使用此類解決方案來建立您的 NFS 或 SMB 共用，則必須由儲存體軟設備中擁有該軟體的協力廠商提供 SAP 中央服務設定的支援。


## <a name="multi-sid-sap-central-services-failover-clusters"></a>多 SID SAP 中央服務容錯移轉叢集
為了減少大型 SAP 環境中所需的 Vm 數目，SAP 允許在容錯移轉叢集設定中執行多個不同 SAP 系統的 SAP 中央服務實例。 想像您有30個以上的 NetWeaver 或 S/4HANA 生產系統的情況。 如果沒有多重 SID 叢集，這些設定需要30個或更多的 Windows 或 Pacemaker 容錯移轉叢集設定中的60或更多 Vm。 除了所需的 DBMS 容錯移轉叢集。 在容錯移轉叢集設定中跨兩個節點部署多個 SAP 中央服務，可大幅減少 Vm 數目。 不過，在單一雙節點叢集設定上部署多個 SAP 中央服務實例也有一些缺點。 叢集設定中單一 VM 的問題適用于多個 SAP 系統。 在叢集設定中執行的客體作業系統上的維護需要更多的協調，因為多個生產環境 SAP 系統會受到影響。 SAP LaMa 之類的工具在其系統複製程式中不支援多重 SID 叢集。

在 Azure 上，具有 ENSA1 和 ENSA2 的 Windows 作業系統支援多重 SID 叢集設定。 建議您不要將舊版的「佇列複寫服務」架構 (ENSA1) 與一個多 SID 叢集上 (ENSA2) 的新架構結合。 如需這類架構的詳細資料，請閱讀文章

- [在 Azure 上搭配 Windows Server 容錯移轉叢集和共用磁碟的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [在 Azure 上搭配 Windows Server 容錯移轉叢集和檔案共用的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

針對 SUSE，也支援以 Pacemaker 為基礎的多 SID 叢集。 到目前為止，支援設定：

- 最多五個 SAP ASCS/SCS 實例
- 舊的排入佇列複寫伺服器 ice 架構 (ENSA1) 
- 兩個節點 Pacemaker 叢集設定

在[適用于 sap 應用程式的 SUSE Linux Enterprise Server 上，Azure vm 上的 Sap NetWeaver 的高可用性記載了多 SID 指南](./high-availability-guide-suse-multi-sid.md)

具有排入佇列複寫伺服器方式的多重 SID 叢集看起來像這樣

![DBMS 和 ASCS HA 設定](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP Hana 向外延展案例
SAP Hana 向外延展案例支援 HANA 認證的 Azure Vm 子集，如[SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中所列。 在資料行 ' 群集 ' 中標示為「是」的所有 Vm，都可以用於 OLAP 或 S/4HANA 向外延展。使用的 Azure 儲存體類型可支援不含待命的設定： 

- Azure 進階儲存體，包括/hana/log 磁片區的 Azure 寫入加速器
- [Ultra 磁碟](../../linux/disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

具有待命節點 () s/4HANA 的 SAP Hana 向外延展設定，僅支援裝載于 Azure NetApp Files 的 NFS 共用。

如需具有或不含待命節點之確切儲存體設定的詳細資訊，請參閱下列文章：

- [SAP HANA Azure 虛擬機器儲存體設定](./hana-vm-operations-storage.md) 
- [在 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-suse.md) \(部分機器翻譯\)
- [在 Red Hat Enterprise Linux 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md) \(部分機器翻譯\)
- [SAP 支援附注 #2080991](https://launchpad.support.sap.com/#/notes/2080991)

如需 HANA 大型實例支援 HANA 相應放大設定的詳細資訊，請參閱下列檔：

- [使用待命相應放大 HANA 大型實例的支援案例](./hana-supported-scenario.md#scale-out-with-standby)
- [適用于不含待命的 HANA 大型實例相應放大的支援案例](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>嚴重損壞修復案例
有各種支援的嚴重損壞修復案例。 我們會將嚴重損壞架構定義為應彌補方格外的完整 Azure 區域的架構。 這表示我們需要嚴重損壞修復目標，做為目標的不同 Azure 區域，以執行您的 SAP 環境。 我們會將 DBMS 層和非 DBMS 層中的方法和設定隔開。 

### <a name="dbms-layer"></a>DBMS 層
針對 DBMS 層，支援使用 DBMS 原生複寫機制的設定，例如 Always On、Oracle Data Guard、Db2 HADR、SAP ASE Always On 或 HANA 系統複寫。 在這類情況下，複寫資料流程是強制的，而不是同步，如同在單一 Azure 區域中部署的一般高可用性案例。 如需這類支援的 DBMS 嚴重損壞修復設定的典型範例，請參閱[SAP Hana 跨 Azure 區域的可用性](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions)一文。 該章節中的第二個圖形描述使用 HANA 作為範例的案例。 SAP 應用程式支援的主資料庫都可以部署在這種情況下。

支援使用較小的 VM 作為嚴重損壞修復區域中的目標實例，因為該 VM 不會經歷完整的工作負載流量。 若要這麼做，您必須記住下列考慮：

- 較小的 VM 類型不允許連接到較小 Vm 的多個磁片
- 較小的 Vm 具有較少的網路和儲存體輸送量
- 在一個 Azure 可用性設定組中收集到不同的 Vm 時，或在 M 系列系列和 Mv2 系列的 Vm 之間進行調整大小的情況時，重新調整 VM 系列的大小可能會造成問題
- 資料庫實例的 CPU 和記憶體耗用量，能夠以最少的延遲和足夠的 CPU 和記憶體資源來接收變更的資料流程，並以最少的延遲將這些變更套用至資料  

如需不同 VM 大小限制的詳細資訊，請參閱[這裡](../../sizes.md) 

另一個部署 DR 目標的支援方法，是在執行非生產環境 SAP 實例之非生產 DBMS 實例的 VM 上安裝第二個 DBMS 實例。 這可能會更具挑戰性，因為您需要瞭解在 DR 案例中，應該做為主要實例的特定目標實例，所需的記憶體、CPU 資源、網路頻寬和儲存體頻寬。 特別是在 HANA 中，強烈建議您將在共用主機上做為 DR 目標的實例設定為，讓資料不會預先載入至 DR 目標實例。

對於 HANA 大型實例 DR 案例，請檢查下列檔：

- [具有使用儲存體複寫之 DR 的單一節點](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [具有 DR (多用途) 使用儲存體複寫的單一節點](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [具有 DR (多用途) 使用儲存體複寫的單一節點](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [具有儲存體複寫的 HSR 和 DR 高可用性](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [使用儲存體複寫以 DR 進行相應放大](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [具有使用 HSR 之 DR 的單一節點](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [單一節點 HSR 至 DR (成本優化) ](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [使用 HSR 的高可用性和嚴重損壞修復](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [使用 HSR (成本優化) 的高可用性和嚴重損壞修復](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [使用 HSR 相應放大 DR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> 未針對 SAP 工作負載下的 DBMS 部署測試使用[Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 。 因此，在此時間點，SAP 系統的 DBMS 層不支援此功能。 未列出的其他 Microsoft 和 SAP 複寫方法不受支援。 使用協力廠商軟體在不同的 Azure 區域之間複寫 SAP 系統的 DBMS 層時，必須由軟體的廠商提供支援，而且不會透過 Microsoft 和 SAP 支援通道來支援。 
 
## <a name="non-dbms-layer"></a>非 DBMS 層
針對 SAP 應用層和所需的最終共用或儲存位置，客戶會利用兩個主要案例：

- 第二個 Azure 區域中的災難復原目標並非用於任何生產或非生產用途。 在此案例中，做為災難復原目標的 Vm 在理想情況下不會部署，而且生產 SAP 應用層的映射和變更會複寫到嚴重損壞修復區域。 [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md)可以執行這類工作的功能。 Azure Site Recovery 支援 Azure 到 Azure 的複寫案例，如下所示。 
- 損毀修復目標是非生產系統實際使用的 Vm。 整個 SAP 環境會散佈到兩個不同的 Azure 區域，而生產系統通常位於一個區域，而非生產系統則位於另一個區域。 在許多客戶部署中，客戶會有相當於生產系統的非生產系統。 客戶已在應用層非生產系統上預先安裝生產應用程式實例。 在容錯移轉的情況下，非生產環境實例會關閉，而生產 Vm 的虛擬名稱則會移至非生產 Vm， (在 DNS) 中指派新的 IP 位址，以及預先安裝的生產實例開始使用

### <a name="sap-central-services-clusters"></a>SAP 中央服務叢集
使用共用磁片 (Windows) 的 SAP 中央服務叢集， (Windows) 或 NFS 共用的 SMB 共用，比較難進行複寫。 在 Windows 端，Windows Storage Replication 是可行的解決方案。 在 Linux 上，rsync 是可行的解決方案。



## <a name="non-supported-scenario"></a>不支援的案例
Azure 架構上的 SAP 工作負載不支援案例清單。 「**不支援**」表示 SAP 和 Microsoft 將無法支援這些設定，而且需要延後到最終涉及的協力廠商，以提供軟體來建立這類架構。 其中兩個類別為：

- 儲存體軟設備： Azure marketplace 中提供了許多儲存體軟體設備。 某些廠商會提供有關如何在 Azure 上使用與 SAP 軟體相關之儲存體軟設備的專屬檔。 這些儲存體軟設備的廠商必須提供與這類儲存體軟設備相關的設定或部署支援。 這項事實也會在[SAP 支援附注中列入說明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- 高可用性架構：只有 Pacemaker 和 Windows Server 容錯移轉叢集支援 Azure 上的 SAP 工作負載的高可用性架構。 如先前所述，SIOS 的解決方案 `Datakeeper` 會由 Microsoft 描述並記載。 不過， `Datakeeper` 當廠商提供這些元件時，必須透過 sios 支援 sios 的元件。 SAP 也在各種 SAP 附注中列出了其他認證的高可用性架構。 其中有些是由 Azure 的協力廠商廠商認證。 儘管如此，使用這些產品的設定支援也必須由產品廠商提供。 不同的廠商與 SAP 支援流程有不同的整合。 在決定在 Azure 上部署的 SAP 設定中使用產品之前，您應該先瞭解哪一個支援程式最適用于特定廠商。
- 除了 maxDB 之外，不支援資料庫檔案位於共用磁片上的共用磁片叢集。 對於其他所有資料庫，支援的解決方案是擁有不同的儲存位置，而不是 SMB 或 NFS 共用或共用磁片，以設定高可用性案例

其他案例則不受支援，如下所示：

- 在 sap 應用層與 sap DBMS 層（如 NetWeaver、S/4HANA 和例如）之間產生較大網路延遲的部署案例 `Hybris` 。 這包括：
    - 部署其中一層內部部署，而另一層則部署在 Azure
    - 在與 DBMS 層不同的 Azure 區域中部署系統的 SAP 應用層
    - 在資料中心內部署一層，該階層會共置於 Azure 和 Azure 中的另一層，但 Azure 原生服務會提供這類架構模式
    - 在 SAP 應用層與 DBMS 層之間部署網路虛擬裝置
    - 針對 SAP DBMS 層或 SAP 全域傳輸目錄，運用裝載于 Azure 資料中心的資料中心內的儲存體
    - 透過兩個不同的雲端廠商部署兩層。 例如，在 Oracle 雲端基礎結構和 Azure 中的應用層部署 DBMS 層
- 多重實例 HANA Pacemaker 叢集設定
- Windows 叢集設定，可透過 SOFS 或 SMB 上的共用磁片，在 Windows 上支援 SAP 資料庫的及。 相反地，我們建議使用特定資料庫的原生高可用性複寫，並使用個別的儲存體堆疊
- 在 Linux 上部署支援的 SAP 資料庫，其中的資料庫檔案位於及之上的 NFS 共用中，但 SAP Hana 除外
- 在任何其他客體作業系統上部署 Oracle DBMS，而非 Windows 和 Oracle Linux。 另請參閱[SAP 支援附注 #2039619](https://launchpad.support.sap.com/#/notes/2039619)

案例 (s) 我們並未測試，因此不會有像這樣的清單：

- Azure Site Recovery 複寫 DBMS 層 Vm。 因此，我們建議利用資料庫原生非同步複寫功能，以進行潛在的嚴重損壞修復設定。
 

## <a name="next-steps"></a>後續步驟
閱讀[適用于 SAP NetWeaver 的 Azure 虛擬機器規劃與實施](./planning-guide.md)中的後續步驟




  