---
title: SAP 工作負載規劃和部署檢查清單 | Microsoft Docs
description: 規劃 SAP 工作負載部署至 Azure 和部署工作負載的檢查清單
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9f517eb5bd113d8d54714b75bea4c8436882d0f9
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924422"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure 上的 SAP 工作負載：規劃和部署檢查清單

此檢查清單是針對將 SAP NetWeaver、S/4HANA 和 Hybris 應用程式移至 Azure 基礎結構即服務的客戶而設計。 在專案的整個持續期間，客戶和/或 SAP 合作夥伴應檢查檢查清單。 請務必注意，許多檢查都是在專案的開頭和在規劃階段完成的。 完成部署之後，對已部署的 Azure 基礎結構或 SAP 軟體版本的直接變更可能會變得複雜。

在您的專案期間，檢查關鍵里程碑的檢查清單。 這麼做可讓您在發生大型問題之前，先偵測出小問題。 您也會有足夠的時間來重新設計及測試任何必要的變更。 請不要考慮這份檢查清單的完成。 視您的情況而定，您可能需要執行更多的檢查。

此檢查清單不包含與 Azure 無關的工作。 例如，移至 Azure 平臺或主機服務提供者時，SAP 應用程式介面會變更。

此檢查清單也可以用於已部署的系統。 新功能（例如寫入加速器和可用性區域）和新的 VM 類型可能已在您部署之後新增。 因此，定期檢查檢查清單以確保您瞭解 Azure 平臺的新功能是很有用的。

## <a name="project-preparation-and-planning-phase"></a>專案準備和規劃階段
在此階段中，您會規劃將 SAP 工作負載遷移至 Azure 平臺。 在此階段中，您至少必須建立下列檔，並定義和討論下列遷移元素：

1. 高階設計檔。 本檔應包含：
    - SAP 元件和應用程式的目前清查，以及 Azure 的目標應用程式清查。
    - 責任指派矩陣 (RACI) ，定義相關合作物件的責任和指派。 從高階開始，並在整個規劃和第一個部署中處理更細微的層級。
    - 高階解決方案架構。
    - 決定要部署到哪些 Azure 區域。 請參閱[Azure 區域清單](https://azure.microsoft.com/global-infrastructure/regions/)。 若要瞭解每個區域中可用的服務，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。
    - 從內部部署連線至 Azure 的網路架構。 開始熟悉[Azure 的虛擬資料中心藍圖](/azure/architecture/vdc/)。
    - 在 Azure 中執行高影響力商務資料的安全性原則。 若要深入瞭解資料安全性，請從[Azure 安全性檔案](../../../security/index.yml)開始。
2.  技術設計檔。 本檔應包含：
    - 方案的區塊圖表。
    - Azure 中的計算、儲存體和網路元件大小。 如需 Azure Vm 的 SAP 大小調整，請參閱[sap 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。
    - 商務持續性和嚴重損壞修復架構。
    - OS、DB、核心和 SAP 支援套件版本的詳細資訊。 Azure Vm 支援 SAP NetWeaver 或 S/4HANA 所支援的每個 OS 版本，這不一定是正確的。 DBMS 版本也是如此。 請檢查下列來源，以配合並視需要升級 SAP 版本、DBMS 版本和 OS 版本，以確保 SAP 和 Azure 支援。 您必須擁有 SAP 和 Azure 支援的發行組合，才能取得 SAP 和 Microsoft 的完整支援。 如有需要，您必須規劃升級某些軟體元件。 如需支援的 SAP、OS 和 DBMS 軟體的詳細資訊，請參閱：
        - [SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 此附注會定義 Azure Vm 上支援的最低 OS 版本。 它也會定義大多數非 HANA 資料庫所需的最小資料庫發行。 最後，它會針對 SAP 支援的 Azure VM 類型提供 SAP 大小調整。
        - [SAP 支援附注 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 此附注會定義 Azure 儲存體的支援原則，並支援 Microsoft 所需的關係。
        - [SAP 支援附注 #2039619](https://launchpad.support.sap.com/#/notes/2039619)。 此附注會定義 Azure 的 Oracle 支援矩陣。 Oracle 僅支援將 Windows 和 Oracle Linux 作為 SAP 工作負載的 Azure 上的客體作業系統。 此支援聲明也適用于執行 SAP 實例的 SAP 應用層。 不過，Oracle 不支援透過 Pacemaker 在 Oracle Linux 中使用 SAP 中央服務的高可用性。 如果您需要 Oracle Linux 上 ASCS 的高可用性，您必須使用適用于 Linux 的 SIOS 保護套件。 如需詳細的 SAP 認證資料，請參閱 SAP 支援附注[#1662610-適用于 Linux 的 SIOS 保護套件支援詳細](https://launchpad.support.sap.com/#/notes/1662610)資料。 針對 Windows，支援 SAP 中央服務的 SAP 支援的 Windows Server 容錯移轉叢集解決方案與 Oracle 做為 DBMS 層。
        - [SAP 支援附注 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。 此附注提供不同 OS 版本 SAP Hana 的支援矩陣。
        - [SAP 網站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)上列出 SAP Hana 支援的 Azure Vm 和[HANA 大型實例](./hana-overview-architecture.md)。
        - [SAP 產品可用性矩陣](https://support.sap.com/en/)。
        - [SAP 支援附注 #2555629-SAP Hana 2.0 動態分層–虛擬程式和雲端支援](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP 支援附注 #1662610-適用于 Linux SIOS 保護套件的支援詳細資料](https://launchpad.support.sap.com/#/notes/1662610)
        - 適用于其他 SAP 特定產品的 SAP 附注。     
    - 我們建議針對 SAP 生產系統進行嚴格的三層式設計。 我們不建議將 ASCS 和/或 DBMS 和（或）應用程式伺服器合併在一個 VM 上。 在 Azure 上的 Windows 客體作業系統上支援使用 SAP 中央服務的多 SID 叢集設定。 但在 Azure 上，Linux 作業系統上的 SAP 中央服務不支援此設定。 您可以在下列文章中找到 Windows 客體作業系統案例的檔：
        - [在 Azure 上搭配 Windows Server 容錯移轉叢集和共用磁碟的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [在 Azure 上搭配 Windows Server 容錯移轉叢集和檔案共用的 SAP ASCS/SCS 執行個體多重 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
    - 高可用性和嚴重損壞修復架構。
        - 根據 RTO 和 RPO，定義高可用性和嚴重損壞修復架構所需的外觀。
        - 針對區域內的高可用性，請檢查所需的 DBMS 在 Azure 中提供的功能。 大部分的 DBMS 封裝都會提供同步熱待命的同步方法，我們建議用於生產系統。 另請參閱 SAP 相關檔中的不同資料庫，從適用于[sap 工作負載的 Azure 虛擬機器 DBMS 部署考慮](./dbms_guide_general.md)和相關檔開始。
           不支援使用 Windows Server 容錯移轉叢集搭配 DBMS 層的共用磁片設定（例如，[針對 SQL Server 所述](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)）。 請改用下列解決方案：
           - [SQL Server AlwaysOn](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 資料保護](../oracle/configure-oracle-dataguard.md)
           - [HANA 系統複寫](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - 針對跨 Azure 區域的嚴重損壞修復，請參閱不同 DBMS 廠商所提供的解決方案。 其中大部分支援非同步複寫或記錄傳送。
        - 針對 SAP 應用層，請判斷您是否要執行商務迴歸測試系統，這在理想情況下是生產部署的複本，位於相同的 Azure 區域或 DR 區域中。 在第二個案例中，您可以將該商務回歸系統的目標設為生產部署的 DR 目標。
        - 如果您決定不要在 DR 網站中放置非生產系統，請參閱 Azure Site Recovery 做為將 SAP 應用層複寫至 Azure DR 區域的方法。 如需詳細資訊，請參閱[設定多層式 SAP NetWeaver 應用程式部署](../../../site-recovery/site-recovery-sap.md)的嚴重損壞修復。
        - 如果您決定使用[Azure 可用性區域](../../../availability-zones/az-overview.md)結合 HADR 設定，請熟悉可用可用性區域的 Azure 區域。 此外，也請考慮增加兩個可用性區域之間的網路延遲，可能會產生的限制。  
3.  所有 SAP 介面的清查 (SAP 和非 SAP) 。
4.  基礎服務的設計。 這項設計應該包含下列專案：
    - Active Directory 和 DNS 設計。
    - Azure 內的網路拓撲和不同 SAP 系統的指派。
    - Azure[角色型存取控制 (AZURE RBAC) ](../../../role-based-access-control/overview.md)結構，適用于在 azure 中管理基礎結構和 SAP 應用程式的小組。
    - 資源群組拓撲。
    - [標記策略](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing)。
    - Vm 和其他基礎結構元件和（或）邏輯名稱的命名慣例。
5.  Microsoft 頂級支援合約。 識別您的 Microsoft 技術客戶經理 (TAM) 。 如需 SAP 支援需求，請參閱[sap 支援附注 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。
6.  訂用帳戶的 Azure 訂用帳戶和核心配額數目。 [開啟支援要求，視需要增加 Azure](../../../azure-portal/supportability/resource-manager-core-quotas-request.md)訂用帳戶的配額。
7.  將 SAP 資料移轉至 Azure 的資料縮減和資料移轉計畫。 針對 SAP NetWeaver 系統，SAP 具有如何限制大量資料量的指導方針。 請參閱這份有關 SAP ERP 系統中資料管理的[sap 指南](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2)。 部分內容也適用于一般的 NetWeaver 和 S/4HANA 系統。
8.  自動化部署方法。 Azure 上的基礎結構部署自動化目標是以具決定性的方式進行部署，並取得具決定性的結果。 許多客戶使用 PowerShell 或 CLI 架構的腳本。 但有各種開放原始碼技術可供您用來部署適用于 SAP 的 Azure 基礎結構，甚至是安裝 SAP 軟體。 您可以在 GitHub 上找到範例：
    - [Azure Cloud 雲端中的自動化 SAP 部署](https://github.com/Azure/sap-hana) \(英文\)
    - [SAP Hana 安裝](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  定義您身為客戶、系統整合者、Microsoft 和其他相關合作物件的一般設計和部署審查步調。


## <a name="pilot-phase-strongly-recommended"></a>試驗階段 (強烈建議) 
 
您可以在專案規劃和準備的前後執行試驗。 您也可以使用試驗階段來測試在規劃和準備階段期間所做的方法和設計。 而且您可以擴充試驗階段，使其成為真正的概念證明。

我們建議您在試驗部署期間，設定並驗證完整的 HADR 解決方案和安全性設計。 有些客戶會在此階段執行擴充性測試。 其他客戶會使用 SAP 沙箱系統的部署做為試驗階段。 我們假設您已找出您想要遷移至 Azure 進行試驗的系統。

1. 將資料傳輸優化至 Azure。 最佳選擇非常依賴特定案例。 如果 ExpressRoute 線路有足夠的頻寬，從內部部署到[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)的傳送速率最快。 在其他情況下，透過網際網路傳輸的速度會更快。
2. 針對涉及匯出和匯入資料的異類 SAP 平臺遷移，請測試並優化匯出和匯入階段。 對於 SQL Server 是目的地平臺的大型遷移，您可以找到[建議](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)。 如果您不需要合併的發行升級，您可以使用 [遷移監視器]/[SWPM]。 當您結合了遷移與 SAP 版本升級時，可以使用[SAP sql-dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)進程。 若要這樣做，您必須符合來源和目標 DBMS 平臺組合的特定需求。 此程式記載于[SUM 2.0 SP03 的資料庫移轉選項 (的) ](https://launchpad.support.sap.com/#/notes/2631152)。
   1.  匯出至來源、匯出檔案上傳至 Azure，以及匯入效能。 最大化匯出和匯入之間的重迭。
   2.  基於基礎結構大小的目的，評估目標和目的地平臺上的資料庫數量。
   3.  驗證並優化計時。
1. 技術驗證。
   1. VM 類型。
        - 請參閱 SAP 支援附注、SAP Hana 硬體目錄，以及 SAP PAM 中的資源。 請確定不會變更 Azure 支援的 Vm、這些 VM 類型支援的作業系統版本，以及支援的 SAP 和 DBMS 版本。
        - 再次驗證您在 Azure 上部署之應用程式和基礎結構的大小。 如果您要移動現有的應用程式，您通常可以從所使用的基礎結構和[sap 基準測試網頁](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)衍生所需的 sap，並將其與[sap 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中列出的 sap 數位進行比較。 也請記住[這篇關於 sap 評等的文章](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)。
        - 針對您在規劃階段中所選擇 VM 類型的最大儲存體輸送量和網路輸送量，評估及測試您的 Azure Vm 大小。 您可以在這裡找到資料：
           -  [Azure 中的 Windows 虛擬機器大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請務必考慮調整大小的最大未快取*磁片輸送量*。
           -  [Azure 中 Linux 虛擬機器的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請務必考慮調整大小的最大未快取*磁片輸送量*。
   2. [儲存空間]。
        - 針對代表 SAP 應用層的 Vm，以及針對不區分效能的 Dbms 部署，請至少使用[Azure 標準 SSD 儲存體](../../windows/disks-types.md#standard-ssd)。
        - 一般而言，我們不建議使用[Azure 標準 HDD 磁片](../../windows/disks-types.md#standard-hdd)。
        - 針對任何遠端效能敏感的 DBMS Vm，使用[Azure 進階儲存體](../../windows/disks-types.md#premium-ssd)。
        - 使用[Azure 受控磁片](https://azure.microsoft.com/services/managed-disks/)。
        - 透過 M 系列將「Azure 寫入加速器」用於 DBMS 記錄磁碟機。 請注意寫入加速器的限制和使用方式，如[寫入加速器](../../linux/how-to-enable-write-accelerator.md)所述。
        - 針對不同的 DBMS 類型，請檢查[一般 SAP 相關的 dbms 檔](./dbms_guide_general.md)和一般檔指向的 dbms 特定檔。
        - 如需 SAP Hana 的詳細資訊，請參閱[Azure 上的 SAP Hana 基礎結構設定和作業](./hana-vm-operations.md)。
        - 請絕對避免透過裝置識別碼將 Azure 資料磁碟掛接到 Azure Linux VM。 相反地，請使用全域唯一識別碼 (UUID)。 舉例來說，使用圖形工具來掛接 Azure 資料磁碟時應多加留意。 請仔細檢查/etc/fstab 中的專案，確定已使用 UUID 來掛接磁片。 您可以在[這篇文章](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)中找到更多詳細資料。
   3. 網路功能。
        - 測試及評估您的虛擬網路基礎結構，以及在不同的 Azure 虛擬網路中進行 SAP 應用程式的散發。
        -  評估單一 Azure 虛擬網路內的中樞和輪輻虛擬網路架構方法或 microsegmentation 方法。 評估基礎：
               1. [對等互連 Azure 虛擬網路](../../../virtual-network/virtual-network-peering-overview.md)之間的資料交換成本。 如需成本的詳細資訊，請參閱[虛擬網路定價](https://azure.microsoft.com/pricing/details/virtual-network/)。
               2. Azure 虛擬網路之間的對等互連快速中斷連線的優點，而不是將網路安全性群組變更為隔離虛擬網路中的子網。 這項評估適用于在虛擬網路的子網中裝載的應用程式或 Vm 已成為安全性風險的情況。
                3. 集中記錄和審核內部部署、外部世界，以及您在 Azure 中建立的虛擬資料中心之間的網路流量。
        - 評估和測試 SAP 應用層與 SAP DBMS 層之間的資料路徑。
            -  不支援將[Azure 網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)放置在 sap 應用程式與 sap 系統的 DBMS 層（以 sap NetWeaver、Hybris 或 S/4HANA 為基礎）之間的通訊路徑中。
            -  不支援在不同的 Azure 虛擬網路中放置 SAP 應用層和 SAP DBMS，而不對等互連。
            -  您可以使用[應用程式安全性群組和網路安全性群組規則](../../../virtual-network/security-overview.md)，來定義 sap 應用層與 sap DBMS 層之間的路由。
        - 請確定在 SAP 應用層和 SAP DBMS 層中使用的 Vm 上已啟用[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 請記住，若要支援 Azure 中的「加速網路」，需要有各種不同的 OS 層級：
            - Windows Server 2012 R2 或更新版本。
            - SUSE Linux 12 SP3 或更新版本。
            - RHEL 7.4 或更新版本。
            - Oracle Linux 7.5。 如果您使用的是 RHCKL 核心，則需要 release 3.10.0-862.13.1. el7。 如果您使用的是 Oracle UEK 核心，則需要第5版。
        - 根據 SAP 支援附注[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)，測試和評估 sap 應用層 vm 與 DBMS vm 之間的網路延遲。 根據[SAP 支援附注 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指引來評估結果。 網路延遲應為適中或良好的範圍。 例外狀況適用于 Vm 與 HANA 大型實例單位之間的流量，如[這篇文章](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)中所述。
        - 請確定 ILB 部署已設定為使用伺服器直接回傳。 當 Azure Ilb 在 DBMS 層上用於高可用性設定時，此設定可減少延遲。
        - 如果您同時使用 Azure Load Balancer 與 Linux 客體作業系統，請檢查 Linux 網路參數**net.tcp. tcp_timestamps**是否設定為**0**。 這項建議與舊版[SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421)中的建議衝突。 SAP 附注現在已更新，以指出此參數必須設定為**0** ，才能使用 Azure 負載平衡器。
        - 請考慮使用[Azure 鄰近放置群組](../../linux/co-location.md)來取得最佳的網路延遲。 如需詳細資訊，請參閱[Azure 鄰近放置群組，以取得 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
   4. 高可用性和嚴重損壞修復部署。
        - 如果您在未定義特定 Azure 可用性區域的情況下部署 SAP 應用層，請確定執行 SAP 對話實例或單一 SAP 系統中介軟體實例的所有 Vm 都部署在[可用性設定組](../../windows/manage-availability.md)中。
        - 如果您不需要 SAP 中央服務和 DBMS 的高可用性，您可以將這些 Vm 部署到與 SAP 應用層相同的可用性設定組中。
        - 如果您使用被動複寫來保護 SAP 中央服務和 DBMS 層，以獲得高可用性，請將 SAP 中央服務的兩個節點放在一個不同的可用性設定組，並將兩個 DBMS 節點置於另一個可用性設定組中。
        - 如果您將部署到 Azure 可用性區域，就無法使用可用性設定組。 但是，您必須確定將主動和被動中央服務節點部署到兩個不同的可用性區域。 使用在兩者之間具有最低延遲的可用性區域。
          請記住，您需要使用[Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)來建立跨可用性區域 DBMS 和 SAP 中央服務層的 Windows 或 Pacemaker 容錯移轉叢集的使用案例。 您不能使用[基本 Load Balancer](../../../load-balancer/load-balancer-overview.md)進列區域性部署。
   5. Timeout 設定。
        - 檢查 sap NetWeaver 開發人員追蹤的 SAP 實例，以確定排入佇列伺服器與 SAP 工作進程之間不會中斷連接。 您可以藉由設定這兩個登錄參數來避免這些連接中斷：
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000。 如需詳細資訊，請參閱[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))。
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000。 如需詳細資訊，請參閱[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))。
        - 若要避免內部部署 SAP GUI 介面和 Azure 中部署的 SAP 應用層之間的 GUI 超時，請檢查是否已在 default.pfl 或實例設定檔中設定這些參數：
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - 若要避免在 SAP 排入佇列進程和 SAP 工作進程之間建立連線中斷，您必須將**將/encni/set_so_keepalive**參數設定為**true**。 另請參閱[SAP note #2743751](https://launchpad.support.sap.com/#/notes/2743751)。  
        - 如果您使用 Windows 容錯移轉叢集設定，請確定已正確地為 Azure 設定未回應節點的回應時間。 [調整容錯移轉叢集網路閾值](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)一文會列出參數，以及它們如何影響容錯移轉敏感性。 假設叢集節點位於相同的子網中，您應該變更這些參數：
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. OS 設定或修補程式
        - 如需在 SAP 上執行 HANA，請閱讀下列注意事項和檔：
            -   [SAP 支援附注 #2814271-SAP Hana 備份在 Azure 上失敗，發生總和檢查碼錯誤](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP 支援附注 #2753418-因計時器回溯而造成的潛在效能降低](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP 支援附注 #2791572-效能降低，因為 Azure 中的 Hyper-v 缺少 VDSO 支援](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP 支援附注 #2382421-優化 HANA 和 OS 層級上的網路設定](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP 支援附注 #2694118-Red Hat Enterprise Linux Azure 上的 HA 附加元件](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP 支援附注 #1984787-SUSE LINUX Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP 支援附注 #2002167-Red Hat Enterprise Linux 7.x：安裝和升級](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP 支援附註 #2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP 支援附注 #2772999-Red Hat Enterprise Linux 8.x：安裝和設定](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP 支援附注 #2777782-SAP Hana DB：適用于 RHEL 8 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP 支援附注 #2578899-SUSE Linux Enterprise Server 15：安裝注意事項](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP 支援附注 #2455582-Linux：執行以 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP 支援附注 #2729475-HWCCT 失敗，錯誤為「不支援「虛擬機器」，因為 Azure Vm 已通過 SAP Hana 認證](https://launchpad.support.sap.com/#/notes/2729475)
1. 測試您的高可用性和嚴重損壞修復程式。
   1. 藉由關閉 Vm (Windows 客體作業系統) 或將作業系統置於緊急模式 (Linux 客體作業系統) 來模擬容錯移轉情況。 此步驟將協助您找出您的容錯移轉設定是否如設計正常。
   1. 測量執行容錯移轉所花費的時間。 如果時間太長，請考慮：
        - 針對 SUSE Linux，請使用 SBD 裝置，而不是 Azure 隔離代理程式來加速容錯移轉。
        - 針對 SAP Hana，如果重載資料花費的時間太長，請考慮布建更多儲存空間頻寬。
   3. 測試備份/還原順序和時機，並視需要進行更正。 請確定備份時間已足夠。 您也需要測試還原和時間還原活動。 請確定在您的 RTO 依賴資料庫或 VM 還原程式的情況下，還原時間都在您的 RTO Sla 內。
   4. 測試跨區域 DR 功能和架構。
1. 安全性檢查。
   1.  (Azure RBAC) 架構，測試 Azure 角色型存取控制的有效性。 其目標是要區分和限制不同小組的存取權與許可權。 例如，SAP 基礎小組成員應該能夠部署 Vm，並將磁片從 Azure 儲存體指派至指定的 Azure 虛擬網路。 但是 SAP 基礎小組不應該能夠建立自己的虛擬網路，也不能變更現有虛擬網路的設定。 網路小組的成員不應該能夠將 Vm 部署到 SAP 應用程式和 DBMS Vm 執行所在的虛擬網路中。 或者，此小組的成員也不能變更 Vm 的屬性，甚至是刪除 Vm 或磁片。  
   1.  請確認[網路安全性群組和 ASC](../../../virtual-network/security-overview.md)規則是否如預期般運作，並防護受保護的資源。
   1.  確定所有需要加密的資源都已加密。 定義和執行處理常式以備份憑證、儲存和存取這些憑證，以及還原加密的實體。
   1.  從 OS 支援的觀點來看，盡可能使用 OS 磁片的[Azure 磁碟加密](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md)。
   1.  請確定您不是使用太多層的加密。 在某些情況下，使用 Azure 磁碟加密搭配其中一種 DBMS 透明資料加密方法來保護相同伺服器上的不同磁片或元件是合理的。  例如，在 SAP DBMS 伺服器上，您可以在作業系統開機磁片上啟用 Azure 磁碟加密 (ADE)  (如果 OS 支援 ADE) ，而那些資料磁片 (不是 DBMS 資料持續性檔案所使用的) 。  例如，在保留 DBMS TDE 加密金鑰的磁片上使用 ADE。
1. 效能測試。 在 SAP 中，根據 SAP 追蹤和度量，進行這些比較：
   - 適用時，請將前10個線上報表與您目前的執行方式進行比較。
   - 若適用，請將前10個批次作業與您目前的執行方式進行比較。
   - 比較透過介面到 SAP 系統的資料傳輸。 將焦點放在您知道傳輸在不同位置（例如從內部部署至 Azure）之間進行的介面。


## <a name="non-production-phase"></a>非生產階段 
在這個階段中，我們假設在成功試驗或概念證明 (POC) 之後，您就開始將非生產環境的 SAP 系統部署到 Azure。 將您在 POC 期間所學到的所有內容，納入此部署中。 針對 Poc 列出的所有準則和步驟也適用于此部署。

在此階段中，您通常會將開發系統、單元測試系統和商務迴歸測試系統部署至 Azure。 我們建議一個 SAP 應用程式行中至少有一個非生產系統，其具有未來生產系統所將擁有的完整高可用性設定。 以下是您需要在此階段完成的一些額外步驟：  

1.  將系統從舊平臺移至 Azure 之前，請收集資源耗用量資料，例如 CPU 使用量、儲存體輸送量及 IOPS 資料。 特別是從 DBMS 層單位收集此資料，但也從應用層單位收集。 此外，也請測量網路和儲存體延遲。
2.  記錄系統的可用性使用時間模式。 其目標是要找出非生產系統是否需要每天都能使用，或是否有非生產系統可以在一周或一個月的特定階段關閉。
3.  測試並判斷您是否想要在 Azure 中為您的 Vm 建立自己的 OS 映射，或是否要使用 Azure 共用映射庫中的映射。 如果您使用的是共用映射庫中的映射，請務必使用反映作業系統廠商支援合約的映射。 針對某些 OS 廠商，共用映射庫可讓您攜帶自己的授權映射。 針對其他 OS 映射，支援會包含在 Azure 所括住的價格中。 如果您決定建立自己的 OS 映像，您可以在下列文章中找到相關文件：
    -   [建立在 Azure 中部署之 Windows VM 的一般化映射](../../windows/capture-image-resource.md)
    -   [建立在 Azure 中部署之 Linux VM 的一般化映射](../../linux/capture-image.md)
3.  如果您使用來自共用映射資源庫的 SUSE 和 Red Hat Linux 映射，您需要在共用映射資源庫中使用 Linux 廠商提供的 SAP 映射。
4.  請務必滿足 Microsoft 支援合約的 SAP 支援需求。 請參閱[SAP 支援附注 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 對於 HANA 大型實例，請參閱上[架需求](./hana-onboarding-requirements.md)。
4.  請確定適當的人員取得[預定的維護通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)，讓您可以選擇最佳的停機。
5.  經常檢查[Channel 9](https://channel9.msdn.com/)等頻道上的 Azure 簡報，以瞭解可能適用于您的部署的新功能。
6.  檢查與 Azure 相關的 SAP 附注，例如[支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)，適用于新的 VM sku 和新支援的 OS 和 DBMS 版本。 比較新 VM 類型與舊版 VM 類型的價格，讓您可以部署具有最佳價格/效能比率的 Vm。
7.  重新檢查 SAP 支援附注、SAP Hana 硬體目錄，以及 SAP PAM。 請確定 Azure 支援的 Vm、這些 Vm 上支援的作業系統版本，以及支援的 SAP 和 DBMS 版本沒有任何變更。
8.  請查看[SAP 網站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，以取得 Azure 中新的 HANA 認證 sku。 比較新 Sku 與您規劃使用的 Sku 的價格。 最後，進行必要的變更，以使用具有最佳價格/效能比率的變更。
9.  調整您的部署腳本，以使用新的 VM 類型，並納入您想要使用的新 Azure 功能。
10. 部署基礎結構之後，請根據 SAP 支援附注[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)，測試和評估 sap 應用層 vm 與 DBMS vm 之間的網路延遲。 根據[SAP 支援附注 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指引來評估結果。 網路延遲應為適中或良好的範圍。 例外狀況適用于 Vm 與 HANA 大型實例單位之間的流量，如[這篇文章](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)中所述。 請確定適用于[SAP 工作負載的 azure 虛擬機器 DBMS 部署考慮](./dbms_guide_general.md#azure-network-considerations)和[azure 上的 SAP Hana 基礎結構設定和作業](./hana-vm-operations.md)都不會有任何限制，會套用至您的部署。
11. 請確定您的 Vm 已部署至正確的[azure 鄰近放置群組](../../linux/co-location.md)（如 Azure 鄰近放置群組中所述），[以取得 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
11. 在套用工作負載之前，請先執行針對概念證明階段列出的其他所有檢查。
12. 當工作負載適用時，記錄 Azure 中系統的資源耗用量。 比較此耗用量與舊版平臺的記錄。 如果您發現有很大的差異，請調整未來部署的 VM 大小。 請記住，當您縮減時，Vm 的儲存體和網路頻寬也會減少。
    - [Azure 中的 Windows 虛擬機器大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure 中的 Linux 虛擬機器大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 使用系統複製功能和程式進行實驗。 目標是讓您輕鬆複製開發系統或測試系統，讓專案小組可以快速地取得新的系統。 請考慮使用[SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance)來進行這些工作。
14. 優化並調整小組的 Azure 角色型存取、許可權和流程，以確保您有責任區隔。 同時，請確定所有小組都可以在 Azure 基礎結構中執行其工作。
15. 練習、測試及記錄高可用性和嚴重損壞修復程式，讓您的員工能夠執行這些工作。 找出缺點，並調整您要整合到部署中的新 Azure 功能。


## <a name="production-preparation-phase"></a>生產準備階段 
在此階段中，收集您在非生產環境部署期間所經歷和學習的內容，並將其套用至未來的生產環境部署。 您也需要準備目前裝載位置與 Azure 之間的資料傳輸工作。

1.  在移至 Azure 之前，先完成生產系統的必要 SAP 版本升級。
1.  同意在遷移生產系統之後，需要執行之功能與商務測試的商務擁有者。
1.  請確定這些測試已完成，且來源系統位於目前的裝載位置。 在系統移至 Azure 之後，請避免第一次執行測試。
1.  測試將生產系統移轉至 Azure 的程式。 如果您不是在相同的時間範圍內將所有生產系統移至 Azure，請建立需要位於相同裝載位置的生產系統群組。 測試資料移轉。 以下是一些常見的方法：
    - 使用 DBMS 方法（例如備份/還原）搭配 SQL Server Always On、HANA 系統複寫或記錄傳送，來植入 Azure 中的資料庫內容並加以同步處理。
    - 針對較小的資料庫，請使用備份/還原。
    - 使用整合到 SAP SWPM 的 SAP 遷移監視器來執行異類遷移。
    - 如果您需要將您的遷移與 SAP 版本升級結合，請使用[SAP sql-dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)進程。 請記住，並非所有來源 DBMS 和目標 DBMS 的組合都受到支援。 您可以在不同的 SQL-DMO 版本的特定 SAP 支援附注中找到詳細資訊。 例如，[資料庫移轉選項 (為 SUM 2.0 SP04 的) ](https://launchpad.support.sap.com/#/notes/2644872)。
    - 透過網際網路或透過 ExpressRoute 測試資料傳輸輸送量是否較佳，以防您需要移動備份或 SAP 匯出檔案。 如果您要透過網際網路移動資料，您可能需要變更某些您必須為未來生產系統準備的網路安全性群組/應用程式安全性群組規則。
1.  將系統從舊平臺移至 Azure 之前，請先收集資源耗用量資料。 有用的資料包含 CPU 使用量、儲存體輸送量和 IOPS 資料。 特別是從 DBMS 層單位收集此資料，但也從應用層單位收集。 此外，也請測量網路和儲存體延遲。
1.  重新檢查 SAP 支援附注和所需的 OS 設定、SAP Hana 硬體目錄，以及 SAP PAM。 請確定在 Azure 支援的 Vm、這些 Vm 中支援的 OS 版本，以及支援的 SAP 和 DBMS 版本中，沒有任何變更。
1.  更新部署腳本，將您在 VM 類型和 Azure 功能上所做的最新決策納入考慮。
1.  部署基礎結構和應用程式之後，請驗證：
    - 已部署正確的 VM 類型，且具有正確的屬性和儲存體大小。
    - Vm 是在正確和所需的作業系統版本和修補程式上，而且是一致的。
    - Vm 會依需求和一致的方式進行強化。
    - 已安裝並部署正確的應用程式版本和修補程式。
    - Vm 已依照規劃部署到 Azure 可用性設定組中。
    - Azure 進階儲存體用於延遲敏感磁片，或需要[99.9% 的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 。
    - 已正確部署 Azure 寫入加速器。
        - 請確定在 Vm、儲存空間或等量集合中，已在需要寫入加速器的磁片上正確建立。
        - 檢查[Linux 上的軟體 RAID](../../linux/configure-raid.md)設定。
        - 檢查[Azure 中 Linux vm 上的 LVM](../../linux/configure-lvm.md)設定。
    - [Azure 受控磁片](https://azure.microsoft.com/services/managed-disks/)會以獨佔方式使用。
    - Vm 已部署到正確的可用性設定組和可用性區域。
    - 在 SAP 應用層和 SAP DBMS 層使用的 Vm 上已啟用[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
    - 根據 SAP NetWeaver、Hybris 或 S/4HANA，sap 應用程式與 SAP 系統的 DBMS 層之間的通訊路徑中沒有任何[Azure 網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)。
    - 「應用程式安全性群組」和「網路安全性群組」規則允許視需要進行通訊及規劃，並在必要時封鎖通訊。
    - 如先前所述，正確設定了 Timeout 設定。
    - Vm 會部署到正確的[azure 鄰近放置群組](../../linux/co-location.md)（如 Azure 鄰近性放置群組中所述），[以取得 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
    - SAP 應用層 Vm 與 DBMS Vm 之間的網路延遲會如 SAP 支援附注[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中所述進行測試和驗證。 根據[SAP 支援附注 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指引來評估結果。 網路延遲應為適中或良好的範圍。 例外狀況適用于 Vm 與 HANA 大型實例單位之間的流量，如[這篇文章](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)中所述。
    - 加密會在必要時和適當的加密方法中執行。
    - 介面和其他應用程式可以連接新部署的基礎結構。
1.  建立腳本來回應規劃的 Azure 維護。 判斷系統和 Vm 應重新開機以進行預定維護的順序。
    

## <a name="go-live-phase"></a>上線階段
在上線階段，請務必遵循您在先前階段開發的操作手冊。 執行您已測試和練習的步驟。 不接受設定和處理常式中的最後一分鐘變更。 也請完成下列步驟：

1. 確認 Azure 入口網站監視功能及其他監視工具正常運作。 建議您使用 Windows 效能監視器 (perfmon) for Windows 和 SAR for Linux。
    - CPU 計數器。
        - 平均 CPU 時間， (所有 Cpu 的總計) 
        - 平均 CPU 時間，M128 Vm 上的每個個別處理器 (128 處理器) 
        - CPU 核心時間，每個個別處理器
        - CPU 使用者時間，每個個別處理器
    - 記憶體。
        - 可用記憶體
        - 記憶體分頁/秒
        - 記憶體分頁輸出/秒
    - 磁碟]。
        - 每個個別磁片的磁片讀取（以 KBps 為單位）
        - 每個個別磁片的磁片讀取數/秒
        - 每個個別磁片的磁片讀取（微秒/讀取）
        - 每個個別磁片的磁片寫入（KBps）
        - 每個個別磁片的磁片寫入數/秒
        - 每個個別磁片的磁片寫入（微秒/讀取）
    - 網路。
        - 每秒的網路封包數
        - 網路封包數/秒
        - 每秒的網路 KB 數
        - 網路 KB 輸出/秒
1.  資料移轉之後，請執行您與企業擁有者商定的所有驗證測試。 只有當您有原始來源系統的結果時，才接受驗證測試結果。
1.  檢查介面是否正常運作，以及其他應用程式是否可以與新部署的生產系統進行通訊。
1.  透過 SAP transaction STMS 檢查傳輸和更正系統。
1.  在系統發行以供生產環境之後，執行資料庫備份。
1.  在系統發行以供生產環境之後，執行 SAP 應用層 Vm 的 VM 備份。
1.  針對不屬於目前上線階段的 SAP 系統，但會與您在此上線階段移至 Azure 的 SAP 系統進行通訊，您需要在 SM51 中重設主機名稱緩衝區。 這麼做會移除與您移至 Azure 的應用程式實例名稱相關聯的舊快取 IP 位址。  


## <a name="post-production"></a>進入生產環境後
這個階段是關於監視、操作和管理系統。 從 SAP 的觀點來看，您必須在舊的主控位置中完成的一般工作才會套用。 也請完成這些 Azure 特有的工作：

1. 查看適用于高收費系統的 Azure 發票。
2. 將 VM 端和儲存體端的價格/效能效率優化。
3. 優化您可以關閉系統的時間。  


## <a name="next-steps"></a>後續步驟
請參閱以下文章：

- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](./planning-guide.md)
- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](./deployment-guide.md)
- [適用于 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考慮](./dbms_guide_general.md)