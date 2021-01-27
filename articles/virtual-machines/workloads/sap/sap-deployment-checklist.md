---
title: SAP 工作負載規劃和部署檢查清單 | Microsoft Docs
description: 規劃將 SAP 工作負載部署到 Azure 和部署工作負載的檢查清單
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 944e687c27d46a9cf3250cb21024b4e5a52dc62c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871514"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure 上的 SAP 工作負載：規劃和部署檢查清單

這份檢查清單是針對將 SAP NetWeaver、S/4HANA 和 Hybris 應用程式移至 Azure 基礎結構即服務的客戶而設計。 在整個專案期間，客戶和/或 SAP 夥伴應查看檢查清單。 請務必注意，許多檢查都是在專案的開頭和規劃階段完成。 部署完成之後，在已部署的 Azure 基礎結構或 SAP 軟體版本上進行直接變更可能會變得複雜。

在您的專案期間，查看重要里程碑的檢查清單。 這麼做可讓您在發生小型問題之前偵測出小問題。 您也會有足夠的時間來重新設計及測試任何必要的變更。 請勿將此檢查清單視為完成。 視您的情況而定，您可能需要執行更多的檢查。

檢查清單不包含獨立于 Azure 的工作。 例如，SAP 應用程式介面會在移至 Azure 平臺或主機服務提供者時變更。

這份檢查清單也可以用於已部署的系統。 自您部署之後，可能已加入新的功能，例如寫入加速器和可用性區域，以及新的 VM 類型。 因此，定期審核檢查清單會很有用，以確保您知道 Azure 平臺的新功能。

## <a name="project-preparation-and-planning-phase"></a>專案準備和規劃階段
在這個階段中，您會規劃將 SAP 工作負載遷移至 Azure 平臺。 至少，在此階段中，您需要建立下列檔，並定義和討論下列各項的遷移元素：

1. 高層級的設計檔。 本檔應包含：
    - 目前的 SAP 元件和應用程式清查，以及適用于 Azure 的目標應用程式清查。
    - 責任指派對照表 (RACI) ，其定義相關各方的責任和指派。 從高階開始，並在整個規劃和第一次部署時，使用更細微的層級。
    - 高層級的方案架構。
    - 決定要部署到哪些 Azure 區域。 請參閱 [Azure 區域清單](https://azure.microsoft.com/global-infrastructure/regions/)。 若要瞭解每個區域中可用的服務，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。
    - 從內部部署連線至 Azure 的網路架構。 開始熟悉 [Azure 的虛擬資料中心藍圖](/azure/architecture/vdc/)。
    - 在 Azure 中執行高影響力商務資料的安全性原則。 若要瞭解資料安全性，請從 [Azure 安全性檔案](../../../security/index.yml)開始。
2.  技術設計檔。 本檔應包含：
    - 解決方案的區塊圖。
    - Azure 中計算、儲存體和網路元件的大小調整。 如需 Azure Vm 的 SAP 大小調整，請參閱 [SAP 
    -  注意 #1928533] (https://launchpad.support.sap.com/#/notes/1928533) 。
    - 商務持續性和嚴重損壞修復架構。
    - OS、DB、核心和 SAP 支援套件版本的詳細資訊。 Azure Vm 支援 SAP NetWeaver 或 S/4HANA 所支援的每個 OS 版本，並不一定如此。 DBMS 版本也是如此。 檢查下列來源以進行調整，並在必要時升級 SAP 版本、DBMS 版本和作業系統版本，以確保 SAP 和 Azure 支援。 您必須擁有 SAP 和 Azure 支援的發行組合，才能取得 SAP 和 Microsoft 的完整支援。 如有必要，您需要規劃升級一些軟體元件。 如需支援的 SAP、OS 和 DBMS 軟體的詳細資訊，請參閱：
        - [SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 此附注定義 Azure Vm 上所支援的最低 OS 版本。 它也會定義大部分非 HANA 資料庫所需的最小資料庫版本。 最後，它會針對 SAP 支援的 Azure VM 類型，提供 SAP 大小調整。
        - [SAP 支援附注 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 此附注定義 Azure 儲存體的支援原則，以及 Microsoft 所需的支援關係。
        - [SAP 支援附注 #2039619](https://launchpad.support.sap.com/#/notes/2039619)。 此附注定義了 Azure 的 Oracle 支援矩陣。 Oracle 僅支援將 Windows 和 Oracle Linux 作為 Azure 上的客體作業系統，以進行 SAP 工作負載。 此支援聲明也適用于執行 SAP 實例的 SAP 應用層。 不過，Oracle 不支援透過 Pacemaker 在 Oracle Linux 中使用 SAP Central Services 的高可用性。 如果您需要在 Oracle Linux 上 ASCS 的高可用性，您必須使用適用于 Linux 的 SIOS 保護套件。 如需詳細的 SAP 認證資料，請參閱 SAP 支援附注 [#1662610-適用于 Linux 的 SIOS 保護套件支援詳細](https://launchpad.support.sap.com/#/notes/1662610)資料。 針對 Windows，支援 sap Central Services 的 SAP 支援的 Windows Server 容錯移轉叢集解決方案，可與 Oracle 做為 DBMS 層一起使用。
        - [SAP 支援附注 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。 此附注提供不同作業系統版本 SAP Hana 的支援矩陣。
        - SAP Hana 支援的 Azure Vm 和 [HANA 大型實例](./hana-overview-architecture.md) 會列在 [SAP 網站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)上。
        - [SAP 產品可用性矩陣](https://support.sap.com/en/)。
        - [SAP 支援附注 #2555629-SAP Hana 2.0 動態分層-虛擬化和雲端支援](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP 支援附注 #1662610-適用于 Linux 的 SIOS 保護套件支援詳細資料](https://launchpad.support.sap.com/#/notes/1662610)
        - 適用于其他 SAP 特定產品的 SAP 附注。     
    - Azure 上的 Windows、SLES 和 RHEL guest 作業系統都支援使用 SAP Central Services 的多重 SID 叢集設定。 請記住，群發半徑可以增加您在這類多重 SID 叢集上放置的 ASCS/SCS。 您可以在下列文章中找到個別的虛擬作業系統案例的檔：
        - [在 Azure 上搭配 Windows Server 容錯移轉叢集和共用磁碟的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [在 Azure 上搭配 Windows Server 容錯移轉叢集和檔案共用的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的多 SID 指南 SUSE Linux Enterprise Server](./high-availability-guide-suse-multi-sid.md)
        - [Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的多 SID 指南 Red Hat Enterprise Linux](./high-availability-guide-rhel-multi-sid.md)
    - 高可用性和嚴重損壞修復架構。
        - 根據 RTO 和 RPO，定義高可用性和嚴重損壞修復架構所需的外觀。
        - 若要瞭解區域內的高可用性，請檢查所需的 DBMS 在 Azure 中提供的功能。 大部分 DBMS 封裝都提供同步熱待命的同步方法，我們建議用於生產系統。 此外，請檢查適用于不同資料庫的 SAP 相關檔，從適用于 [sap 工作負載的 Azure 虛擬機器 DBMS 部署](./dbms_guide_general.md) 和相關檔的考慮開始。
           不支援使用 Windows Server 容錯移轉叢集搭配 DBMS 層的共用磁片設定，例如 [SQL Server 所述](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)。 相反地，請使用類似下列的解決方案：
           - [SQL Server AlwaysOn](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 資料保護](../oracle/configure-oracle-dataguard.md)
           - [HANA 系統複寫](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - 針對跨 Azure 區域的嚴重損壞修復，請參閱不同 DBMS 廠商提供的解決方案。 其中大部分支援非同步複寫或記錄傳送。
        - 針對 SAP 應用層，請判斷您是否要在相同的 Azure 區域或 DR 區域中，執行您的業務迴歸測試系統，最好是生產部署的複本。 在第二個案例中，您可以將該商務回歸系統的目標設為生產部署的 DR 目標。
        - 如果您決定不要在 DR 網站中放置非生產系統，請查看 Azure Site Recovery，作為將 SAP 應用層複寫至 Azure DR 區域的方法。 如需詳細資訊，請參閱 [設定多層式 SAP NetWeaver 應用程式部署](../../../site-recovery/site-recovery-sap.md)的嚴重損壞修復。
        - 如果您決定使用 [Azure 可用性區域](../../../availability-zones/az-overview.md)的結合 HADR 設定，請熟悉可用性區域可供使用的 Azure 區域。 此外，也請考慮可能因為兩個可用性區域之間的網路延遲增加而引進的限制。  
3.  所有 SAP 介面 (SAP 和非 SAP) 的清查。
4.  基礎服務的設計。 此設計應該包含下列專案：
    - Active Directory 和 DNS 設計。
    - Azure 內的網路拓撲與不同 SAP 系統的指派。
    - [Azure 角色型存取控制 (AZURE RBAC) ](../../../role-based-access-control/overview.md) 結構，適用于在 azure 中管理基礎結構和 SAP 應用程式的小組。
    - 資源群組拓撲。
    - [標記策略](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing)。
    - Vm 和其他基礎結構元件及/或邏輯名稱的命名慣例。
5.  Microsoft Professional 或頂級支援合約。 如果您有 Microsoft 的頂級支援合約，請找出您的 Microsoft 技術支援客戶經理 (TAM) 。 如需 SAP 支援需求，請參閱 [sap 支援附注 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。
6.  訂用帳戶的 Azure 訂用帳戶數目和核心配額。 [開啟支援要求，以視需要增加 Azure](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) 訂用帳戶的配額。
7.  將 SAP 資料移轉至 Azure 的資料減少和資料移轉計畫。 針對 SAP NetWeaver 系統，SAP 有關于如何限制大量資料量的指導方針。 請參閱關於 SAP ERP 系統中資料管理的 [sap 指南](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) 。 部分內容也適用于一般的 NetWeaver 和 S/4HANA 系統。
8.  自動化的部署方法。 Azure 上的基礎結構部署自動化目標是以具決定性的方式進行部署，並取得決定性的結果。 許多客戶使用 PowerShell 或以 CLI 為基礎的腳本。 但有各種開放原始碼技術，您可以用來部署適用于 SAP 的 Azure 基礎結構，甚至安裝 SAP 軟體。 您可以在 GitHub 上找到範例：
    - [Azure Cloud 雲端中的自動化 SAP 部署](https://github.com/Azure/sap-hana) \(英文\)
    - [SAP Hana 安裝](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  定義客戶、系統整合者、Microsoft 和其他相關合作物件之間的一般設計和部署審查步調。


## <a name="pilot-phase-strongly-recommended"></a>試驗階段 (強烈建議) 
 
您可以在專案規劃和準備之前或期間執行試驗。 您也可以使用試驗階段來測試規劃和準備階段期間所做的方法與設計。 您可以擴充試驗階段，讓它成為真正的概念證明。

建議您在試驗部署期間設定並驗證完整的 HADR 方案和安全性設計。 有些客戶會在此階段執行調整規模測試。 其他客戶使用 SAP 沙箱系統的部署作為試驗階段。 我們假設您已找出您想要遷移至 Azure 以進行試驗的系統。

1. 優化資料傳輸至 Azure。 最佳選擇會高度依賴特定案例。 如果 ExpressRoute 線路有足夠的頻寬，則透過 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 從內部部署傳輸是最快速的。 在其他情況下，透過網際網路傳輸會更快。
2. 針對涉及匯出和匯入資料的異類 SAP 平臺遷移，請測試和優化匯出和匯入階段。 針對 SQL Server 為目的地平臺的大型遷移，您可以找到 [建議](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)。 如果您不需要合併的版本升級，您可以使用 [遷移監視器]/[SWPM]。 當您將遷移與 SAP 發行升級合併時，可以使用 [SAP sql-dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 進程。 若要這樣做，您必須符合來源和目標 DBMS 平臺組合的特定需求。 此程式記載在 [SUM 2.0 SP03 的資料庫移轉選項 (的) ](https://launchpad.support.sap.com/#/notes/2631152)。
   1.  匯出至來源、將檔案上傳至 Azure，並匯入效能。 最大化匯出和匯入之間的重迭。
   2.  針對基礎結構調整大小的目的，評估目標和目的地平臺上的資料庫數量。
   3.  驗證和優化時機。
1. 技術驗證。
   1. VM 類型。
        - 請參閱 SAP 支援附注、SAP Hana 硬體目錄和 SAP PAM 中的資源。 請確定 Azure 支援的 Vm、這些 VM 類型支援的 OS 版本，以及支援的 SAP 和 DBMS 版本都沒有變更。
        - 再次驗證您在 Azure 上部署之應用程式和基礎結構的大小。 如果您要移動現有的應用程式，您通常可以從所使用的基礎結構，以及 Sap 效能評定 [網頁](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) ，將所需的 sap 衍生出來，並將其與 [sap 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中列出的 sap 編號進行比較。 也請記住 [這篇文章，以瞭解 sap 評等](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) 。
        - 評估並測試您的 Azure Vm 大小，並針對您在規劃階段所選擇之 VM 類型的最大儲存體輸送量和網路輸送量。 您可以在這裡找到資料：
           -  [Azure 中 Windows 虛擬機器的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請務必考慮調整大小的最大未快取 *磁片輸送量* 。
           -  [Azure 中 Linux 虛擬機器的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請務必考慮調整大小的最大未快取 *磁片輸送量* 。
   2. [儲存空間]。
        - 檢查 [SAP 工作負載的檔 Azure 儲存體類型](./planning-guide-storage.md)
        - 您至少要針對代表 SAP 應用層的 Vm 使用 [Azure 標準 SSD 儲存體](../../disks-types.md#standard-ssd) ，並針對不具效能敏感性的 dbms 進行部署。
        - 一般而言，我們不建議使用 [Azure 標準 HDD 磁片](../../disks-types.md#standard-hdd)。
        - 針對任何遠端效能敏感的 DBMS Vm，使用 [Azure 進階儲存體](../../disks-types.md#premium-ssd) 。
        - 使用 [Azure 受控磁片](https://azure.microsoft.com/services/managed-disks/)。
        - 透過 M 系列將「Azure 寫入加速器」用於 DBMS 記錄磁碟機。 請注意寫入加速器的限制和使用方式，如 [寫入加速器](../../how-to-enable-write-accelerator.md)中所述。
        - 針對不同的 DBMS 類型，請檢查一般檔所指向的 [一般 SAP 相關 dbms 檔](./dbms_guide_general.md) 和 DBMS 特定檔。
        - 如需有關 SAP Hana 的詳細資訊，請參閱 [SAP Hana Azure 上的基礎結構設定和作業](./hana-vm-operations.md)。
        - 請絕對避免透過裝置識別碼將 Azure 資料磁碟掛接到 Azure Linux VM。 相反地，請使用全域唯一識別碼 (UUID)。 舉例來說，使用圖形工具來掛接 Azure 資料磁碟時應多加留意。 請仔細檢查/etc/fstab 中的專案，以確定已使用 UUID 來掛接磁片。 您可以在 [這篇文章](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)中找到更多詳細資料。
   3. 網路功能。
        - 測試和評估您的虛擬網路基礎結構，並在不同的 Azure 虛擬網路中或跨不同的 SAP 應用程式散發。
        -  評估單一 Azure 虛擬網路中的中樞和輪輻虛擬網路架構方法或微分割方法。 此評估的基礎：
               1. [對等互連 Azure 虛擬網路](../../../virtual-network/virtual-network-peering-overview.md)之間的資料交換成本。 如需成本的詳細資訊，請參閱 [虛擬網路定價](https://azure.microsoft.com/pricing/details/virtual-network/)。
               2. 在 Azure 虛擬網路之間進行對等互連快速中斷連線的優點，而不是變更網路安全性群組以隔離虛擬網路內的子網。 這項評估適用于在虛擬網路的子網中裝載的應用程式或 Vm 成為安全性風險的情況。
                3. 集中記錄和審核內部部署、外界內外的網路流量，以及您在 Azure 中建立的虛擬資料中心。
        - 評估並測試 SAP 應用層和 SAP DBMS 層之間的資料路徑。
            -  不支援將 [Azure 網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/) 放置於 sap 應用程式和 sap 系統的 DBMS 層（以 sap NetWeaver、Hybris 或 S/4HANA 為基礎）之間的通訊路徑。
            -  不支援將 SAP 應用層和 SAP DBMS 放置在不同的 Azure 虛擬網路中，但不對等互連。
            -  您可以使用 [應用程式安全性群組和網路安全性群組規則](../../../virtual-network/network-security-groups-overview.md) ，來定義 sap 應用層和 sap DBMS 層之間的路由。
        - 請確定在 SAP 應用層和 SAP DBMS 層使用的 Vm 上已啟用 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 。 請記住，若要支援 Azure 中的「加速網路」，需要有各種不同的 OS 層級：
            - Windows Server 2012 R2 或更新版本。
            - SUSE Linux 12 SP3 或更新版本。
            - RHEL 7.4 或更新版本。
            - Oracle Linux 7.5。 如果您使用的是 RHCKL 核心，則需要發行 3.10.0-862.13.1. el7。 如果您是使用 Oracle UEK 核心，則需要版本5。
        - 根據 SAP 支援附注 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)，測試和評估 sap 應用層 vm 與 DBMS vm 之間的網路延遲。 針對 [SAP 支援附注 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指引評估結果。 網路延遲應為適中或良好的範圍。 例外狀況適用于 Vm 與 HANA 大型實例單位之間的流量，如 [本文中所](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)述。
        - 請確定 ILB 部署已設定為使用伺服器直接回傳。 當 Azure Ilb 用於 DBMS 層的高可用性設定時，此設定將會降低延遲。
        - 如果您搭配使用 Azure Load Balancer 與 Linux 客體作業系統，請確認 Linux 網路參數 **net.ipv4.tcp_timestamps** 設定為 **0**。 這項建議與舊版 [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421)中的建議相衝突。 SAP note 現在已更新，以指出此參數必須設定為 **0** ，才能與 Azure 負載平衡器搭配運作。
        - 請考慮使用 [Azure 鄰近放置群組](../../co-location.md) 來取得最佳的網路延遲。 如需詳細資訊，請參閱 [Azure 鄰近放置群組，以取得 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
   4. 高可用性和嚴重損壞修復部署。
        - 如果您部署 SAP 應用層，但未定義特定的 Azure 可用性區域，請確定執行 SAP 對話實例的所有 Vm 或單一 SAP 系統的中介軟體實例都部署在 [可用性設定組](../../manage-availability.md)中。
        - 如果您不需要 SAP Central Services 和 DBMS 的高可用性，您可以將這些 Vm 部署到與 SAP 應用層相同的可用性設定組中。
        - 如果您使用被動複寫來保護 SAP Central Services 和 DBMS 層以達到高可用性，請將 SAP Central Services 的兩個節點放在一個不同的可用性設定組中，並將兩個 DBMS 節點放在另一個可用性設定組中。
        - 如果您部署到 Azure 可用性區域，就無法使用可用性設定組。 但是，您必須確定將主動和被動中央服務節點部署至兩個不同的可用性區域。 使用具有最低延遲的可用性區域。
          請記住，您必須使用 [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) ，才能在可用性區域之間建立 DBMS 和 SAP Central Services 層的 Windows 或 Pacemaker 容錯移轉叢集。 您無法針對區域性部署使用 [基本 Load Balancer](../../../load-balancer/load-balancer-overview.md) 。
   5. Timeout 設定。
        - 請檢查 sap NetWeaver 開發人員對 SAP 實例的追蹤，以確定佇列伺服器與 SAP 工作進程之間沒有任何連接中斷。 您可以藉由設定這兩個登錄參數來避免這些連接中斷：
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000。 如需詳細資訊，請參閱 [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))。
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000。 如需詳細資訊，請參閱 [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))。
        - 若要避免在 Azure 中部署的內部部署 SAP GUI 介面和 SAP 應用層之間發生 GUI 超時，請檢查是否已在 default.pfl 或實例設定檔中設定這些參數：
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - 若要避免在 SAP 排入佇列程式與 SAP 工作進程之間建立連線中斷，您必須將 **將/encni/set_so_keepalive** 參數設定為 **true**。 另請參閱 [SAP note #2743751](https://launchpad.support.sap.com/#/notes/2743751)。  
        - 如果您使用 Windows 容錯移轉叢集設定，請確定已針對 Azure 正確設定未回應節點的回應時間。 [調整容錯移轉叢集網路閾值](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)的文章會列出參數，以及它們如何影響容錯移轉敏感性。 假設叢集節點位於相同的子網中，您應該變更這些參數：
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. 作業系統設定或修補程式
        - 若要在 SAP 上執行 HANA，請閱讀下列附注和檔：
            -   [SAP 支援附注 #2814271-在 Azure 上 SAP Hana 備份失敗，發生總和檢查碼錯誤](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP 支援附注 #2753418-計時器回復可能會導致效能降低](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP 支援附注 #2791572-因為 Azure 中的 Hyper-v 缺少 VDSO 支援，所以效能降低](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP 支援附注 #2382421-將 HANA 和 OS 層級上的網路設定優化](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP 支援附注 #2694118-Red Hat Enterprise Linux Azure 上的 HA Add-On](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP 支援附注 #1984787-SUSE LINUX Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP 支援附注 #2002167-Red Hat Enterprise Linux 7.x：安裝和升級](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP 支援附註 #2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP 支援附注 #2772999-Red Hat Enterprise Linux 8.x：安裝和設定](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP 支援附注 #2777782-SAP Hana DB：適用于 RHEL 8 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP 支援附注 #2578899-SUSE Linux Enterprise Server 15：安裝注意事項](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP 支援附注 #2455582-Linux：執行以 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP 支援附注 #2729475-HWCCT 失敗，並出現「不支援虛擬機器」的 Azure Vm 上的 SAP Hana 認證](https://launchpad.support.sap.com/#/notes/2729475)
1. 測試您的高可用性和嚴重損壞修復程式。
   1. 藉由關閉 Vm (Windows 客體作業系統) 或將作業系統進入緊急模式 (Linux 客體作業系統) ，來模擬容錯移轉情況。 此步驟可協助您找出您的容錯移轉設定是否按照設計的方式運作。
   1. 測量執行容錯移轉所需的時間。 如果時間太長，請考慮：
        - 針對 SUSE Linux，請使用 SBD 裝置，而不是使用 Azure 隔離代理程式來加速容錯移轉。
        - 針對 SAP Hana，如果資料重載花費的時間太長，請考慮布建更多儲存體頻寬。
   3. 測試您的備份/還原順序和時間，並在需要時進行修正。 請確定備份時間已足夠。 您也需要測試還原和還原時間的活動。 當您的 RTO 依賴資料庫或 VM 還原程式時，請確定還原時間在您的 RTO Sla 內。
   4. 測試跨區域 DR 功能和架構。
1. 安全性檢查。
   1.  (Azure RBAC) 架構，測試 Azure 角色型存取控制的有效性。 其目標是要區分和限制不同小組的存取權和許可權。 例如，SAP 基礎團隊成員應該能夠部署 Vm，並將磁片從 Azure 儲存體指派至指定的 Azure 虛擬網路。 但是 SAP 基礎團隊應該無法建立自己的虛擬網路，也不能變更現有虛擬網路的設定。 網路小組的成員應該無法將 Vm 部署到執行 SAP 應用程式和 DBMS Vm 的虛擬網路中。 此小組的成員也不能變更 Vm 的屬性，或甚至是刪除 Vm 或磁片。  
   1.  確認 [網路安全性群組和 ASC](../../../virtual-network/network-security-groups-overview.md) 規則如預期般運作，並防護受保護的資源。
   1.  確定所有需要加密的資源都已加密。 定義和執行程式來備份憑證、儲存和存取這些憑證，以及還原加密的實體。
   1.  從 OS 支援的觀點來看，盡可能使用 OS 磁片的 [Azure 磁碟加密](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) 。
   1.  請確定您不是使用太多層的加密。 在某些情況下，搭配使用 Azure 磁碟加密與其中一個 DBMS 透明資料加密方法，以保護相同伺服器上的不同磁片或元件，是合理的。  例如，在 SAP DBMS 伺服器上，如果作業系統支援 ADE) ，以及 DBMS 資料持續性檔案未使用的資料磁片 () ，就可以在作業系統開機 (磁片上啟用 Azure 磁碟加密 (ADE) 。  例如，在保存 DBMS TDE 加密金鑰的磁片上使用 ADE。
1. 效能測試。 在 SAP 中，根據 SAP 追蹤和度量，進行下列比較：
   - 在適用的情況下，比較前10個線上報表與您目前的實施。
   - 在適用的情況下，比較前10個批次作業與您目前的執行。
   - 比較透過介面到 SAP 系統的資料傳輸。 將焦點放在您知道傳輸在不同位置之間的介面，例如從內部部署到 Azure。


## <a name="non-production-phase"></a>非生產階段 
在這個階段中，我們假設在成功的試驗或概念證明 (POC) 之後，您就開始將非生產環境 SAP 系統部署到 Azure。 將您在 POC 期間所學到的所有內容，納入此部署中。 針對 Poc 所列的所有準則和步驟也適用于這種部署。

在這個階段中，您通常會將開發系統、單元測試系統，以及商務迴歸測試系統部署到 Azure。 我們建議在一個 SAP 應用程式行中至少有一個非生產系統，具有未來生產系統將擁有的完整高可用性設定。 以下是您必須在這個階段完成的一些額外步驟：  

1.  將系統從舊平臺移至 Azure 之前，請收集資源耗用量資料，例如 CPU 使用量、儲存體輸送量和 IOPS 資料。 尤其是從 DBMS 層單位收集此資料，但也會從應用層單位收集。 此外，也請測量網路和儲存體延遲。
2.  記錄系統的可用性使用時間模式。 其目標是要找出非生產系統是否需要每天、每天或是否有非生產系統可在一周或每個月的特定階段關機。
3.  測試並判斷您是否要在 Azure 中為您的 Vm 建立自己的 OS 映射，或是否要使用 Azure 共用映射庫中的映射。 如果您使用的是來自共用映射庫的映射，請務必使用反映作業系統廠商支援合約的映射。 針對某些 OS 廠商，共用映射庫可讓您攜帶自己的授權映射。 若為其他作業系統映射，則支援包含在 Azure 的報價價格中。 如果您決定建立自己的 OS 映像，您可以在下列文章中找到相關文件：
    -   [建立在 Azure 中部署的 Windows VM 一般化映射](../../windows/capture-image-resource.md)
    -   [建立在 Azure 中部署之 Linux VM 的一般化映射](../../linux/capture-image.md)
3.  如果您使用來自共用映射庫的 SUSE 和 Red Hat Linux 映射，您必須使用共用映射庫中 Linux 廠商提供的 SAP 映射。
4.  請務必滿足 Microsoft 支援合約的 SAP 支援需求。 請參閱 [SAP 支援附注 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 針對「HANA 大型實例」，請參閱上 [架需求](./hana-onboarding-requirements.md)。
4.  請確定正確的人員會取得 [預定的維修通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) ，讓您可以選擇最適合的停機時間。
5.  請經常檢查 [Channel 9](https://channel9.msdn.com/) 等通道上的 Azure 簡報，以瞭解可能適用于您的部署的新功能。
6.  檢查與 Azure 相關的 SAP 附注，例如 [支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)，適用于新的 VM sku 和新支援的 OS 和 DBMS 版本。 比較新 VM 類型與較舊 VM 類型的定價，讓您可以部署具有最佳價格/效能比例的 Vm。
7.  重新檢查 SAP 支援附注、SAP Hana 硬體目錄和 SAP PAM。 請確定 Azure 支援的 Vm、這些 Vm 上支援的 OS 版本，以及支援的 SAP 和 DBMS 版本都沒有變更。
8.  請在 Azure 中檢查 [SAP 網站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 是否有新的 HANA 認證 sku。 將新 Sku 的價格與您計畫使用的 Sku 做比較。 最後，進行必要的變更，以使用具有最佳價格/效能比例的變更。
9.  調整您的部署腳本以使用新的 VM 類型，並納入您要使用的新 Azure 功能。
10. 在部署基礎結構之後，請根據 SAP 支援附注 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)，測試和評估 sap 應用層 vm 與 DBMS vm 之間的網路延遲。 針對 [SAP 支援附注 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指引評估結果。 網路延遲應為適中或良好的範圍。 例外狀況適用于 Vm 與 HANA 大型實例單位之間的流量，如 [本文中所](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)述。 請確定適用于 [SAP 工作負載的 Azure 虛擬機器 DBMS 部署](./dbms_guide_general.md#azure-network-considerations) 和 [azure 上的 SAP Hana 基礎結構設定和作業](./hana-vm-operations.md) 所述的任何限制適用于您的部署。
11. 確定您的 Vm 已部署到正確的 [azure 鄰近放置群組](../../co-location.md)（如 azure 鄰近放置群組所述），以 [提供 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
11. 在套用工作負載之前，請先執行針對概念證明階段所列出的所有其他檢查。
12. 當工作負載適用時，請記錄 Azure 中系統的資源耗用量。 將此耗用量與您舊平臺的記錄進行比較。 如果您發現有很大的差異，請調整未來部署的 VM 大小。 請記住，當您縮減、儲存和 Vm 的網路頻寬也會降低時。
    - [Azure 中的 Windows 虛擬機器大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure 中的 Linux 虛擬機器大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 使用系統複製功能和流程進行實驗。 目標是要讓您輕鬆地複製開發系統或測試系統，讓專案小組能夠快速取得新系統。 
14. 將您小組的 Azure 角色型存取權、許可權和流程優化並加以調整，以確定您有分開的職責。 同時，請確定所有小組都可以在 Azure 基礎結構中執行其工作。
15. 練習、測試及記錄高可用性和嚴重損壞修復程式，讓您的員工能夠執行這些工作。 找出缺點，並調整您要整合到部署中的新 Azure 功能。


## <a name="production-preparation-phase"></a>生產準備階段 
在這個階段中，您會在非生產環境部署期間收集您遇到並學習到的內容，並將它套用至未來的生產環境部署。 您也需要準備目前裝載位置與 Azure 之間的資料傳輸作業。

1.  移至 Azure 之前，請先完成生產系統的必要 SAP 版本升級。
1.  在遷移生產系統之後，您必須同意功能與商務測試的商務擁有者。
1.  請確定這些測試已完成，且來源系統位於目前的裝載位置。 請避免在系統移至 Azure 之後第一次執行測試。
1.  測試將生產系統移轉至 Azure 的流程。 如果您不是在相同的時間範圍內將所有生產系統移至 Azure，請建立需要位於相同裝載位置的生產系統群組。 測試資料移轉。 以下是一些常見的方法：
    - 使用 DBMS 方法（例如備份/還原）搭配 SQL Server Always On、HANA 系統複寫或記錄傳送，以在 Azure 中植入和同步處理資料庫內容。
    - 針對較小的資料庫使用備份/還原。
    - 使用已整合至 SAP SWPM 的 SAP 遷移監視器來執行異類的遷移。
    - 如果您需要將您的遷移與 SAP 版本升級合併，請使用 [SAP sql-dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 進程。 請記住，不支援所有來源 DBMS 和目標 DBMS 的組合。 您可以在不同版本的特定 SAP 支援附注中找到詳細資訊。 例如， [資料庫移轉選項 (的) SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872)。
    - 測試資料傳輸輸送量是否優於網際網路或透過 ExpressRoute，以防您需要移動備份或 SAP 匯出檔案。 如果您是透過網際網路移動資料，您可能需要變更某些您必須為未來生產系統進行的網路安全性群組/應用程式安全性群組規則。
1.  將系統從舊平臺移至 Azure 之前，請收集資源耗用量資料。 有用的資料包含 CPU 使用量、儲存體輸送量和 IOPS 資料。 尤其是從 DBMS 層單位收集此資料，但也會從應用層單位收集。 此外，也請測量網路和儲存體延遲。
1.  重新檢查 SAP 支援附注和必要的作業系統設定、SAP Hana 硬體目錄和 SAP PAM。 請確定 Azure 支援的 Vm、這些 Vm 中支援的 OS 版本，以及支援的 SAP 和 DBMS 版本都沒有變更。
1.  更新部署腳本，將您在 VM 類型和 Azure 功能上所做的最新決策納入考慮。
1.  在部署基礎結構和應用程式之後，請驗證：
    - 已部署正確的 VM 類型，並具有正確的屬性和儲存體大小。
    - Vm 位於正確和所需的作業系統版本和修補程式，而且是一致的。
    - Vm 會依照需要和一致的方式進行強化。
    - 已安裝並部署正確的應用程式版本和修補程式。
    - Vm 已依照規劃部署至 Azure 可用性設定組。
    - Azure 進階儲存體適用于延遲敏感的磁片，或必須有 [99.9% 的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 。
    - Azure 寫入加速器已正確部署。
        - 請確定在 Vm、儲存空間或等量集合中，已在需要寫入加速器的磁片上正確建立。
        - 檢查 [Linux 上的軟體 RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)設定。
        - 檢查 [Azure 中 Linux vm 上的 LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm)設定。
    - [Azure 受控磁片](https://azure.microsoft.com/services/managed-disks/) 會以獨佔方式使用。
    - Vm 已部署到正確的可用性設定組和可用性區域中。
    - 在 SAP 應用層和 SAP DBMS 層使用的 Vm 上會啟用[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
    - Sap 應用程式與 sap 系統的 DBMS 層之間的通訊路徑中沒有任何 [Azure 網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/) （以 sap NetWeaver、Hybris 或 S/4HANA 為基礎）。
    - 應用程式安全性群組和網路安全性群組規則允許視需要進行通訊和規劃，並在必要時封鎖通訊。
    - 如先前所述，已正確設定 Timeout 設定。
    - Vm 會部署到正確的 [azure 鄰近放置群組](../../co-location.md)（如 azure 鄰近放置群組所述），以 [提供 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
    - SAP 應用層 Vm 與 DBMS Vm 之間的網路延遲會如 SAP 支援附注 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中所述進行測試和驗證。 針對 [SAP 支援附注 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指引評估結果。 網路延遲應為適中或良好的範圍。 例外狀況適用于 Vm 與 HANA 大型實例單位之間的流量，如 [本文中所](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)述。
    - 加密是在必要時和適當的加密方法所執行。
    - 介面和其他應用程式可以連線至新部署的基礎結構。
1.  建立腳本來回應預定的 Azure 維護。 判斷系統和 Vm 應重新開機的順序，以進行預定的維護。
    

## <a name="go-live-phase"></a>上線階段
在上線階段，請務必遵循您在先前階段中所開發的操作手冊。 執行您所測試和練習的步驟。 請勿接受設定和處理常式的最後一分鐘變更。 此外，請完成下列步驟：

1. 確認 Azure 入口網站監視功能及其他監視工具正常運作。 我們建議適用于 Windows 的 Windows 效能監視器 (perfmon) ，以及適用于 Linux 的 SAR。
    - CPU 計數器。
        - 平均 CPU 時間、所有 Cpu (總計) 
        - M128 Vm 上每個個別處理器 (128 處理器的平均 CPU 時間) 
        - CPU 核心時間，每個個別處理器
        - CPU 使用者時間，每個個別處理器
    - 記憶體。
        - 可用記憶體
        - 記憶體頁面/秒
        - 記憶體頁面輸出/秒
    - 磁碟]。
        - 每個個別磁片的磁片讀取（KBps）
        - 每個個別磁片的磁片讀取次數/秒
        - 每個個別磁片的磁片讀取（以微秒/讀取）
        - 每個個別磁片的磁片寫入（KBps）
        - 磁片寫入數/秒，每個個別磁片
        - 每個個別磁片的磁片寫入（以微秒/讀取）
    - 網路。
        - 每秒的網路封包數
        - 網路封包輸出/秒
        - 網路 KB/秒
        - 網路 KB 輸出/秒
1.  資料移轉之後，請對企業擁有者執行您同意的所有驗證測試。 只有當您有原始來源系統的結果時，才接受驗證測試結果。
1.  檢查介面是否正常運作，以及其他應用程式是否可與新部署的生產系統進行通訊。
1.  透過 SAP transaction STMS 檢查傳輸和修正系統。
1.  在系統發行以供生產環境後執行資料庫備份。
1.  針對生產環境發行系統之後，執行 SAP 應用層 Vm 的 VM 備份。
1.  若 SAP 系統不屬於目前的上線階段，但在此上線階段期間與您移至 Azure 的 SAP 系統通訊，您必須在 SM51 中重設主機名稱緩衝區。 這麼做會移除與您移至 Azure 的應用程式實例名稱相關聯的舊快取 IP 位址。  


## <a name="post-production"></a>進入生產環境後
這個階段是關於監視、操作及管理系統。 從 SAP 的觀點來看，您必須在舊的裝載位置中完成的一般工作才適用。 也請完成這些 Azure 專屬的工作：

1. 查看適用于高充電系統的 Azure 發票。
2. 將 VM 端和儲存體端的價格/效能效率優化。
3. 將您可以關閉系統的時間優化。  


## <a name="next-steps"></a>後續步驟
請參閱以下文章：

- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](./planning-guide.md)
- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](./deployment-guide.md)
- [適用于 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考慮](./dbms_guide_general.md)