---
title: SAP 工作負載規劃和部署檢查清單 | Microsoft Docs
description: 用於規劃 SAP 工作負載部署到 Azure 和部署工作負荷的檢查表
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
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060072"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure 上的 SAP 工作負載：規劃和部署檢查表

此檢查表專為將 SAP NetWeaver、S/4HANA 和 Hybris 應用程式移至 Azure 基礎結構作為服務的客戶而設計。 在整個專案期間，客戶和/或 SAP 合作夥伴應查看檢查表。 請務必注意，許多檢查是在專案開始時和規劃階段完成的。 部署完成後，對已部署的 Azure 基礎結構或 SAP 軟體版本進行直接更改可能會變得複雜。

在專案期間查看關鍵里程碑的檢查表。 這樣做將使您能夠在小問題成為大問題之前檢測到它們。 您還將有足夠的時間重新設計和測試任何必要的更改。 不要認為此清單已完成。 根據您的情況，您可能需要執行更多檢查。

該檢查表不包括獨立于 Azure 的任務。 例如，SAP 應用程式介面在移動到 Azure 平臺或託管提供程式期間會更改。

此檢查表還可用於已部署的系統。 自部署以來，可能添加了新功能（如寫入加速器和可用性區域）和新的 VM 類型。 因此，定期查看檢查表以確保瞭解 Azure 平臺中的新功能非常有用。

## <a name="project-preparation-and-planning-phase"></a>專案準備和規劃階段
在此階段，您可以計畫將 SAP 工作負荷遷移到 Azure 平臺。 至少，在此階段，您需要創建以下文檔，並定義和討論遷移的以下元素：

1. 高級設計文檔。 本文檔應包含：
    - SAP 元件和應用程式的當前清單，以及 Azure 的目標應用程式清單。
    - 責任分配矩陣 （RACI），用於定義相關各方的責任和分配。 從高級別開始，在整個規劃和第一次部署過程中工作到更精細的級別。
    - 高級解決方案體系結構。
    - 有關要部署到哪個 Azure 區域的決定。 請參閱[Azure 區域的清單](https://azure.microsoft.com/global-infrastructure/regions/)。 要瞭解每個區域提供哪些服務，請參閱[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。
    - 從本地連接到 Azure 的網路架構。 開始熟悉 Azure 的[虛擬資料中心藍圖](https://docs.microsoft.com/azure/architecture/vdc/)。
    - 在 Azure 中運行高影響業務資料的安全原則。 要瞭解資料安全性，從 Azure[安全文檔](https://docs.microsoft.com/azure/security/)開始。
2.  技術設計文檔。 本文檔應包含：
    - 解決方案的框圖。
    - Azure 中計算、存儲和網路元件的大小調整。 有關 Azure VM 的 SAP 大小，請參閱[SAP 支援說明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。
    - 業務連續性和災害復原體系結構。
    - 有關作業系統、資料庫、內核和 SAP 支援包版本的詳細資訊。 SAP NetWeaver 或 S/4HANA 支援的每個作業系統版本在 Azure VM 上並不一定如此。 DBMS 版本也是如此。 檢查以下源以對齊並在必要時升級 SAP 版本、DBMS 版本和作業系統版本，以確保 SAP 和 Azure 支援。 您需要有 SAP 和 Azure 支援發佈組合，才能獲得 SAP 和 Microsoft 的全力支援。 如有必要，您需要計畫升級某些軟體元件。 有關支援的 SAP、作業系統和 DBMS 軟體的更多詳細資訊，請在此處記錄：
        - [SAP 支援說明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 本說明定義了 Azure VM 上支援的最低作業系統版本。 它還定義了大多數非 HANA 資料庫所需的最小資料庫版本。 最後，它為 SAP 支援的 Azure VM 類型提供了 SAP 大小調整。
        - [SAP 支援說明#2015553](https://launchpad.support.sap.com/#/notes/2015553)。 本說明定義了有關與 Microsoft 所需的 Azure 存儲和支援關係的支援策略。
        - [SAP 支援說明#2039619](https://launchpad.support.sap.com/#/notes/2039619)。 本說明定義了 Azure 的 Oracle 支援矩陣。 Oracle 僅支援 Windows 和 Oracle Linux 作為 Azure 上的訪客作業系統，用於 SAP 工作負載。 此支援語句也適用于運行 SAP 實例的 SAP 應用程式層。 但是，Oracle 不支援通過起搏器在 Oracle Linux 中為 SAP 中央服務提供高可用性。 如果您需要在 Oracle Linux 上為 ASCS 提供高可用性，則需要使用適用于 Linux 的 SIOS 保護套件。 有關詳細的 SAP 認證資料，請參閱 #1662610 的 SAP 支援說明[- 適用于 Linux 的 SIOS 保護套件的支援詳細資訊](https://launchpad.support.sap.com/#/notes/1662610)。 對於 Windows，SAP 支援的 WINDOWS 伺服器容錯移轉叢集解決方案與 Oracle 作為 DBMS 層一起支援。
        - [SAP 支援說明#2235581](https://launchpad.support.sap.com/#/notes/2235581)。 本說明提供了不同作業系統版本上的 SAP HANA 支援矩陣。
        - SAP HANA 支援的 Azure VM 和[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)列在[SAP 網站上](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
        - [SAP 產品可用性矩陣](https://support.sap.com/en/)。
        - [SAP 支援說明#2555629 - SAP HANA 2.0 動態分層 – 虛擬機器管理程式和雲支援](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP 支援說明#1662610 - 適用于 Linux 的 SIOS 保護套件的支援詳細資訊](https://launchpad.support.sap.com/#/notes/1662610)
        - 其他 SAP 特定產品的 SAP 說明。     
    - 我們建議為 SAP 生產系統提供嚴格的三層設計。 我們不建議將 ASCS 和/或 DBMS 和/或應用伺服器合併到一個 VM 上。 Azure 上的 Windows 客體作業系統支援為 SAP 中央服務使用多 SID 群集配置。 但是，在 Azure 上的 Linux 作業系統上的 SAP 中央服務不支援此配置。 您可以在以下文章中找到 Windows 客體作業系統方案的文檔：
        - [在 Azure 上搭配 Windows Server 容錯移轉叢集和共用磁碟的 SAP ASCS/SCS 執行個體多重 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [在 Azure 上搭配 Windows Server 容錯移轉叢集和檔案共用的 SAP ASCS/SCS 執行個體多重 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - 高可用性和災害復原體系結構。
        - 基於 RTO 和 RPO，定義高可用性和災害復原體系結構需要的外觀。
        - 要在區域內獲得高可用性，請查看所需的 DBMS 在 Azure 中提供的內容。 大多數 DBMS 包提供同步熱備用的同步方法，我們建議用於生產系統。 還要檢查不同資料庫的 SAP 相關文檔，從[SAP 工作負載和相關文檔的 Azure 虛擬機器 DBMS 部署注意事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)開始。
           不支援使用 Windows Server 容錯移轉叢集與 DBMS 層的共用磁片配置，例如，[對於 SQL Server 所述](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)。 相反，請使用以下解決方案：
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 資料保護](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [HANA 系統複寫](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - 有關跨 Azure 區域的災害復原，請查看不同 DBMS 供應商提供的解決方案。 它們大多支援非同步複製或記錄傳送。
        - 對於 SAP 應用程式層，確定是運行業務迴歸測試系統（理想情況下是生產部署的副本、在同一 Azure 區域中還是在 DR 區域中）。 在第二種情況下，您可以將該業務回歸系統作為生產部署的 DR 目標。
        - 如果決定不將非生產系統放在 DR 網站中，請查看 Azure 網站恢復，作為將 SAP 應用程式層複製到 Azure DR 區域的方法。 有關詳細資訊，請參閱[為多層 SAP NetWeaver 應用部署設置災害復原](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)。
        - 如果您決定使用[Azure 可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)使用組合的 HADR 配置，請熟悉可用區域的 Azure 區域。 還要考慮兩個可用區域之間網路延遲增加可能引入的限制。  
3.  所有 SAP 介面（SAP 和非 SAP）的清單。
4.  基礎服務的設計。 此設計應包括以下專案：
    - 活動目錄和 DNS 設計。
    - Azure 中的網路拓撲和分配不同的 SAP 系統。
    - Azure 中管理基礎結構和 SAP 應用程式的團隊基於[角色的訪問](https://docs.microsoft.com/azure/role-based-access-control/overview)結構。
    - 資源組拓撲。
    - [標記策略](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)。
    - 命名 VM 和其他基礎結構元件和/或邏輯名稱的約定。
5.  微軟卓越支援合同。 確定您的 Microsoft 技術客戶經理 （TAM）。 有關 SAP 支援要求，請參閱[SAP 支援說明#2015553](https://launchpad.support.sap.com/#/notes/2015553)。
6.  訂閱的 Azure 訂閱數和核心配額數。 [打開支援請求，根據需要增加 Azure 訂閱的配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。
7.  用於將 SAP 資料移轉到 Azure 的資料縮減和資料移轉計畫。 對於 SAP NetWeaver 系統，SAP 有有關如何限制大量資料量的指導原則。 請參閱[此 SAP 指南](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2)，瞭解 SAP ERP 系統中的資料管理。 部分內容也適用于 NetWeaver 和 S/4HANA 系統。
8.  自動部署方法。 Azure 上基礎結構部署自動化的目標是以確定性的方式進行部署，並取得確定性結果。 許多客戶使用基於 PowerShell 或基於 CLI 的腳本。 但是，可以使用各種開源技術來部署用於 SAP 的 Azure 基礎結構，甚至安裝 SAP 軟體。 您可以在 GitHub 上找到示例：
    - [Azure Cloud 雲端中的自動化 SAP 部署](https://github.com/Azure/sap-hana) \(英文\)
    - [SAP HANA 安裝](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  將客戶、系統集成商、Microsoft 和其他相關方之間的常規設計和部署審核節奏定義。

 
## <a name="pilot-phase-strongly-recommended"></a>試驗階段（強烈建議）
 
您可以在專案規劃和準備之前或期間運行試驗。 您還可以使用試驗階段來測試在規劃和準備階段制定的方法和設計。 您可以擴展試驗階段，使其成為概念的真正證明。

我們建議您在試驗部署期間設置和驗證完整的 HADR 解決方案和安全設計。 在此階段，某些客戶執行可伸縮性測試。 其他客戶使用 SAP 沙箱系統的部署作為試驗階段。 我們假設您已經確定了要遷移到 Azure 進行試驗的系統。

1. 優化資料傳輸到 Azure。 最佳選擇高度依賴于特定方案。 如果 ExpressRoute 電路具有足夠的頻寬，則通過[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)從本地傳送速率最快。 在其他情況下，通過互聯網傳輸更快。
2. 對於涉及資料匯出和導入的異構 SAP 平臺遷移，測試和優化匯出和導入階段。 對於 SQL Server 是目標平臺的大型遷移，可以找到[建議](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)。 如果您不需要合併版本升級，則可以使用遷移監視器/SWPM。 將遷移與 SAP 版本升級相結合時，可以使用[SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)流程。 為此，您需要滿足源和目標 DBMS 平臺組合的某些要求。 此過程記錄在[SUM 2.0 SP03 的資料庫移轉選項 （DMO） 中](https://launchpad.support.sap.com/#/notes/2631152)。
   1.  匯出到源，將檔上載匯出到 Azure，並導入性能。 最大化匯出和導入之間的重疊。
   2.  評估目標平臺和目標平臺上資料庫的容量，以便進行基礎結構大小調整。
   3.  驗證和優化計時。
1. 技術驗證。
   1. VM 類型。
        - 再次查看 SAP 支援說明、SAP HANA 硬體目錄中和 SAP PAM 中的資源。 確保沒有更改 Azure 支援的 VM、這些 VM 類型的受支援作業系統版本以及支援的 SAP 和 DBMS 版本。
        - 再次驗證您在 Azure 上部署之應用程式和基礎結構的大小。 如果要移動現有應用程式，通常可以從您使用的基礎結構和[SAP 基準網頁](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)派生必要的 SAPS，並將其與[SAP 支援說明中列出的 SAPS 編號進行比較，#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 還要記住[關於 SAPS 評級的文章](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)。
        - 根據規劃階段選擇的 VM 類型的最大存儲輸送量和網路輸送量評估和測試 Azure VM 的大小。 您可以在此處找到資料：
           -  [Azure 中的 Windows 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請務必考慮*最大未緩存磁片輸送量*，以便調整大小。
           -  [Azure 中的 Linux 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請務必考慮*最大未緩存磁片輸送量*，以便調整大小。
   2. [儲存空間]。
        - 至少，對表示 SAP 應用程式層的 VM 以及部署不對性能敏感的 DBMS 使用[Azure 標準 SSD 存儲](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)。
        - 通常，我們不建議使用[Azure 標準硬碟磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)。
        - 對遠端性能敏感的任何 DBMS VM 使用[Azure 高級存儲](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)。
        - 使用[Azure 託管磁片](https://azure.microsoft.com/services/managed-disks/)。
        - 透過 M 系列將「Azure 寫入加速器」用於 DBMS 記錄磁碟機。 請注意寫入加速器的限制和用法，如[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)中所述。
        - 對於不同的 DBMS 類型，請查看通用文檔指向的[通用 SAP 相關 DBMS 文檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)和特定于 DBMS 的文檔。
        - 有關 SAP HANA 的詳細資訊，請參閱[SAP HANA 基礎結構配置和 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
        - 請絕對避免透過裝置識別碼將 Azure 資料磁碟掛接到 Azure Linux VM。 相反地，請使用全域唯一識別碼 (UUID)。 舉例來說，使用圖形工具來掛接 Azure 資料磁碟時應多加留意。 仔細檢查 /etc/fstab 中的條目，以確保 UUID 用於裝載磁片。 你可以[在本文](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)中找到更多的細節。
   3. 網路功能。
        - 測試和評估虛擬網路基礎結構以及 SAP 應用程式在不同 Azure 虛擬網路之間或內部的分佈情況。
        -  評估中心分支虛擬網路體系結構方法或單個 Azure 虛擬網路中的微分段方法。 此評估的基礎是：
               1. [對等 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)之間的資料交換成本。 有關成本的資訊，請參閱[虛擬網路定價](https://azure.microsoft.com/pricing/details/virtual-network/)。
               2. 快速斷開 Azure 虛擬網路之間的對等互連，而不是更改網路安全性群組以隔離虛擬網路中的子網。 此評估適用于託管在虛擬網路子網中的應用程式或 VM 成為安全風險的情況。
                3. 本地、外部世界和您在 Azure 中構建的虛擬資料中心之間的網路流量的中央日誌記錄和審核。
        - 評估和測試 SAP 應用程式層和 SAP DBMS 層之間的資料路徑。
            -  不支援將[Azure 網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)放置在 SAP 應用程式和基於 SAP NetWeaver、Hybris 或 S/4HANA 的 SAP 系統的 DBMS 層之間的通訊路徑中。
            -  不支援將 SAP 應用程式層和 SAP DBMS 放置在不同的 Azure 虛擬網路中，這些網路不進行對等處理。
            -  您可以使用[應用程式安全性群組和網路安全性群組規則](https://docs.microsoft.com/azure/virtual-network/security-overview)定義 SAP 應用程式層和 SAP DBMS 層之間的路由。
        - 確保在 SAP 應用程式層和 SAP DBMS 層中使用的 VM 上啟用[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 請記住，若要支援 Azure 中的「加速網路」，需要有各種不同的 OS 層級：
            - Windows Server 2012 R2 或更新版本。
            - SUSE Linux 12 SP3 或更高版本。
            - RHEL 7.4 或更高版本。
            - Oracle Linux 7.5。 如果您使用的是 RHCKL 內核，則需要版本 3.10.0-862.13.1.el7。 如果您使用的是 Oracle UEK 內核，則需要第 5 個版本。
        - 根據 SAP 支援說明[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926，](https://launchpad.support.sap.com/#/notes/1100926/E)測試和評估 SAP 應用程式層 VM 和 DBMS VM 之間的網路延遲。 根據[SAP 支援說明#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指南評估結果。 網路延遲應處於中等或良好範圍內。 例外情況適用于 VM 和 HANA 大型實例單元之間的流量，[如本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所述。
        - 確保將 ILB 部署設置為使用直接伺服器返回。 當 Azure ILB 用於 DBMS 層上的高可用性配置時，此設置將減少延遲。
        - 如果將 Azure 負載等化器與 Linux 客體作業系統一起使用，請檢查 Linux 網路參數**net.ipv4.tcp_timestamps**是否設置為**0**。 此建議與舊版本的[SAP 注釋#2382421](https://launchpad.support.sap.com/#/notes/2382421)中的建議相衝突。 SAP 注釋現在更新為說明需要將此參數設置為**0**才能與 Azure 負載等化器配合使用。
        - 請考慮使用[Azure 接近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)來獲得最佳網路延遲。 有關詳細資訊，請參閱[Azure 接近放置組，瞭解 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
   4. 高可用性和災害復原部署。
        - 如果在未定義特定的 Azure 可用性區域的情況下部署 SAP 應用程式層，請確保在[可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)部署了運行 SAP 對話方塊實例的所有 VM 或單個 SAP 系統的中介軟體實例。
        - 如果 SAP 中央服務和 DBMS 不需要高可用性，則可以將這些 VM 部署到與 SAP 應用程式層相同的可用性集中。
        - 如果使用被動禁止複製 SAP 中央服務和 DBMS 層以獲得高可用性，則將 SAP 中央服務的兩個節點放在一個單獨的可用性集中集中集中集中集，將兩個 DBMS 節點放在另一個可用性集中。
        - 如果部署到 Azure 可用性區域，則無法使用可用性集。 但是，您確實需要確保將主動和被動的中央服務節點部署到兩個不同的可用性區域。 使用它們之間的延遲最低的可用區域。
          請記住，您需要使用[Azure 標準負載等化器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)來使用跨可用性區域為 DBMS 和 SAP 中央服務層建立 Windows 或 Pacemaker 容錯移轉叢集的用例。 不能將[基本負載等化器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)用於地區部署。
   5. 超時設置。
        - 檢查 SAP NetWeaver 開發人員對 SAP 實例的跟蹤，以確保在排隊伺服器和 SAP 工作流程之間沒有連接中斷。 通過設置這兩個註冊表參數，可以避免這些連接中斷：
            - HKLM_SYSTEM_當前控制集_服務\Tcpip_參數\保持存留時間 = 120000。 有關詳細資訊，請參閱[保持存留時間](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))。
            - HKLM_SYSTEM_當前控制集_服務\Tcpip_參數\保持Alive間隔 = 120000。 有關詳細資訊，請參閱[保持活動間隔](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))。
        - 為避免本地 SAP GUI 介面和部署在 Azure 中的 SAP 應用程式層之間的 GUI 超時，請檢查這些參數是在預設值.pfl 還是實例設定檔中設置的：
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - 為了防止中斷 SAP 排隊流程和 SAP 工作流程之間的已建立連接，您需要將**enque/encni/set_so_keepalive**參數設置為**true**。 另請參閱[SAP 注釋#2743751](https://launchpad.support.sap.com/#/notes/2743751)。  
        - 如果使用 Windows 容錯移轉叢集配置，請確保為 Azure 正確設置對無回應節點做出反應的時間。 "[調整容錯移轉叢集網路閾值"](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)一文列出了參數及其如何影響容錯移轉敏感性。 假設叢集節點位於同一子網中，則應更改以下參數：
            - 相同的子網延遲 = 2000
            - SameSubNetThreshold = 15
            - 路由歷史記錄長度 = 30
    6. 作業系統設置或修補程式
        - 要在 SAP 上運行 HANA，請閱讀以下注釋和文檔：
            -   [SAP 支援說明#2814271 - SAP HANA 備份在 Azure 上失敗，但檢查與錯誤](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP 支援說明#2753418 - 由於計時器回退可能導致性能下降](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP 支援說明#2791572 - 由於 Azure 中缺少對超 V 的 VDSO 支援，性能下降](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP 支援說明#2382421 - 在 HANA 和作業系統級別上優化網路設定](https://launchpad.support.sap.com/#/notes/2382421)
            -   [#2694118 SAP 支援說明 - Azure 上的紅帽企業 Linux HA 附加元件](https://launchpad.support.sap.com/#/notes/2694118)
            -   [#1984787 SAP 支援說明 - SUSE LINUX 企業伺服器 12：安裝說明](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP 支援說明#2002167 - 紅帽企業 Linux 7.x：安裝和升級](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP 支援附註 #2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP 支援說明#2772999 - 紅帽企業 Linux 8.x：安裝和配置](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP 支援說明#2777782 - SAP HANA DB：RHEL 8 的推薦作業系統設置](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP 支援說明#2578899 - SUSE Linux 企業伺服器 15：安裝說明](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP 支援說明#2455582 - Linux：運行使用 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP 支援說明 #2729475 - HWCCT 失敗，在經過 SAP HANA 認證的 Azure VM 上出現錯誤"不支援虛擬機器管理程式"](https://launchpad.support.sap.com/#/notes/2729475)
1. 測試高可用性和災害復原過程。
   1. 通過關閉 VM（Windows 客體作業系統）或將作業系統置於映射模式（Linux 客體作業系統）來類比容錯移轉情況。 此步驟將説明您確定容錯移轉配置是否按照設計工作。
   1. 測量執行容錯移轉所需的時間。 如果時間太長，請考慮：
        - 對於 SUSE Linux，請使用 SBD 設備而不是 Azure 圍欄代理來加快容錯移轉。
        - 對於 SAP HANA，如果重新載入資料時間過長，請考慮預配更多存儲頻寬。
   3. 測試備份/恢復順序和計時，並在需要時進行更正。 確保備份時間已足夠。 您還需要測試還原和時間還原活動。 無論 RTO 依賴于資料庫或 VM 還原過程，請確保還原時間在 RTO SLÉ 範圍內。
   4. 測試跨區域 DR 功能和體系結構。
1. 安全性檢查。
   1. 測試基於 Azure 角色的存取控制 （RBAC） 體系結構的有效性。 目標是分離和限制不同團隊的存取權限和許可權。 例如，SAP Basis 團隊成員應該能夠部署 VM 並將 Azure 存儲中的磁片分配給給定的 Azure 虛擬網路。 但是 SAP Basis 團隊不應能夠創建自己的虛擬網路或更改現有虛擬網路的設置。 網路團隊成員不應能夠將 VM 部署到運行 SAP 應用程式和 DBMS VM 的虛擬網路中。 此團隊成員也不應更改 VM 的屬性，甚至刪除 VM 或磁片。  
   1.  驗證[網路安全性群組和 ASC](https://docs.microsoft.com/azure/virtual-network/security-overview)規則是否按預期工作，並保護受保護的資源。
   1.  確定所有需要加密的資源都已加密。 定義和實現進程以備份證書、存儲和訪問這些證書以及還原加密的實體。
   1.  從作業系統支援的角度來看，盡可能對作業系統磁片使用[Azure 磁片加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq)。
   1.  確保您沒有使用太多的加密層。 在某些情況下，將 Azure 磁片加密與 DBMS 透明資料加密方法之一一起使用以保護同一伺服器上的不同磁片或元件確實有意義。  例如，在 SAP DBMS 伺服器上，可以在作業系統引導磁片（如果作業系統支援 ADE）上啟用 Azure 磁片加密 （ADE），並且這些資料磁片（如果支援 ADE）和 DBMS 資料持久性檔未使用的資料磁片。  例如，在持有 DBMS TDE 加密金鑰的磁片上使用 ADE。
1. 效能測試。 在 SAP 中，基於 SAP 跟蹤和測量，進行以下比較：
   - 在適用的情況下，將前 10 個線上報告與當前實施情況進行比較。
   - 在適用的情況下，將前 10 個批次處理作業與當前實現進行比較。
   - 比較通過介面傳輸到 SAP 系統的資料傳輸。 關注知道傳輸在不同位置（如從本地到 Azure）之間的介面。


## <a name="non-production-phase"></a>非生產階段 
在此階段，我們假定在成功進行試驗或概念驗證 （POC） 後，您開始將非生產 SAP 系統部署到 Azure。 將您在 POC 期間學到的和體驗的所有內容都整合到此部署中。 為 POC 列出的所有條件和步驟也適用于此部署。

在此階段，您通常將開發系統、單元測試系統和業務迴歸測試系統部署到 Azure。 我們建議在一個 SAP 應用程式行中至少有一個非生產系統具有未來生產系統將具有的完整高可用性配置。 以下是在此階段需要完成的其他步驟：  

1.  在將系統從舊平臺移動到 Azure 之前，收集資源消耗資料，如 CPU 使用方式、存儲輸送量和 IOPS 資料。 特別是從 DBMS 層單元收集此資料，但也從應用程式層單元中收集這些資料。 此外，也請測量網路和儲存體延遲。
2.  記錄系統的可用性使用時間模式。 目標是確定非生產系統是否需要全天、每天可用，或者是否有非生產系統可以在一周或每月的特定階段關閉。
3.  測試並確定是要為 Azure 中的 VM 創建自己的作業系統映射，還是希望使用 Azure 共用映射庫中的圖像。 如果您使用的是共用映射庫中的圖像，請確保使用反映與作業系統供應商的支援合同的圖像。 對於某些作業系統供應商，共用映射庫允許您自帶許可證映射。 對於其他作業系統映射，支援包含在 Azure 引用的價格中。 如果您決定建立自己的 OS 映像，您可以在下列文章中找到相關文件：
    -   [生成部署在 Azure 中的 Windows VM 的通用映射](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [構建部署在 Azure 中的 Linux VM 的通用映射](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  如果您使用共用映射庫中的 SUSE 和紅帽 Linux 映射，則需要在共用映射庫中為 Linux 供應商提供的 SAP 使用映射。
4.  確保滿足 Microsoft 支援協定的 SAP 支援要求。 請參閱[SAP 支援說明#2015553](https://launchpad.support.sap.com/#/notes/2015553)。 有關 HANA 大型實例，請參閱[載入要求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)。
4.  確保合適的人員獲得[計畫維護通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)，以便您可以選擇最佳停機時間。
5.  經常檢查[通道 9](https://channel9.msdn.com/)等通道上的 Azure 演示文稿，瞭解可能適用于部署的新功能。
6.  檢查與 Azure 相關的 SAP 注釋，如[支援注釋#1928533](https://launchpad.support.sap.com/#/notes/1928533)，瞭解新的 VM SKU 和新支援的作業系統和 DBMS 版本。 將新 VM 類型的定價與較舊的 VM 類型的定價進行比較，以便部署具有最佳性價比的 VM。
7.  重新檢查 SAP 支援說明、SAP HANA 硬體目錄和 SAP PAM。 確保 Azure 支援的 VM 中沒有更改，這些 VM 上支援作業系統版本，以及支援的 SAP 和 DBMS 版本。
8.  [查看 SAP 網站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，瞭解 Azure 中新的 HANA 認證 SKU。 將新 SKU 的定價與您計畫使用的 SKU 進行比較。 最終，進行必要的更改，以使用具有最佳性價比的更改。
9.  調整部署腳本以使用新的 VM 類型併合並要使用的新 Azure 功能。
10. 部署基礎結構後，根據 SAP 支援說明[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926，](https://launchpad.support.sap.com/#/notes/1100926/E)測試和評估 SAP 應用程式層 VM 和 DBMS VM 之間的網路延遲。 根據[SAP 支援說明#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指南評估結果。 網路延遲應處於中等或良好範圍內。 例外情況適用于 VM 和 HANA 大型實例單元之間的流量，[如本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所述。 確保[Azure 虛擬機器 DBMS 部署中針對 SAP 工作負荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations)和 AZURE 上的 SAP [HANA 基礎結構配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)的注意事項中提到的任何限制都不適用於您的部署。
11. 確保 VM 部署到正確的[Azure 接近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，如[Azure 接近放置組中所述，以便使用 SAP 應用程式實現最佳網路延遲](sap-proximity-placement-scenarios.md)。
11. 在應用工作負載之前，執行為概念驗證階段列出的所有其他檢查。
12. 當工作負荷應用時，在 Azure 中記錄系統的資源消耗。 將此消耗與舊平臺的記錄進行比較。 如果您發現存在較大差異，請調整未來部署的 VM 大小。 請記住，當您縮小 VM 的尺寸、存儲和網路頻寬時，也會減少這些頻寬。
    - [Azure 中 Windows 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure 中的 Linux 虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 試驗系統複製功能和流程。 目標是讓您輕鬆複製開發系統或測試系統，以便專案團隊能夠快速獲得新系統。 請考慮將[SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance)用於這些任務。
14. 優化和磨練團隊的基於 Azure 的存取權限、許可權和流程，以確保職責分離。 同時，請確保所有團隊都可以在 Azure 基礎結構中執行任務。
15. 練習、測試和記錄高可用性和災害復原過程，使員工能夠執行這些任務。 識別缺點並調整要集成到部署中的 Azure 新功能。

 
## <a name="production-preparation-phase"></a>生產準備階段 
在此階段，收集您在非生產部署期間所體驗和學習的內容，並將其應用於未來的生產部署。 您還需要準備當前託管位置和 Azure 之間的資料傳輸工作。

1.  在遷移到 Azure 之前，完成生產系統的必要 SAP 版本升級。
1.  就生產系統移轉後需要執行的功能和業務測試與業務擁有者達成一致。
1.  確保這些測試與當前託管位置的源系統一起完成。 避免在系統移動到 Azure 後首次執行測試。
1.  測試將生產系統移轉到 Azure 的過程。 如果未在同一時間段內將所有生產系統移動到 Azure，則生成需要位於同一託管位置的生產系統組。 測試資料移轉。 下面是一些常見方法：
    - 使用 DBMS 方法，如備份/還原與 SQL Server 始終打開、HANA 系統複製或記錄傳送到 Azure 中的資料庫內容種子和同步。
    - 對較小的資料庫使用備份/還原。
    - 使用集成到 SAP SWPM 中的 SAP 遷移監視器執行異構遷移。
    - 如果需要將遷移與 SAP 版本升級相結合，請使用[SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)流程。 請記住，並非所有源 DBMS 和目標 DBMS 的組合都受支援。 您可以在 DMO 不同版本的特定 SAP 支援說明中找到更多資訊。 例如[，SUM 2.0 SP04 的資料庫移轉選項 （DMO）](https://launchpad.support.sap.com/#/notes/2644872)。
    - 測試資料傳輸輸送量是否通過互聯網或 ExpressRoute 更好，以防需要移動備份或 SAP 匯出檔。 如果要通過 Internet 傳輸資料，則可能需要更改未來生產系統需要具備的一些網路安全性群組/應用程式安全性群組規則。
1.  在將系統從舊平臺移動到 Azure 之前，請收集資源消耗資料。 有用的資料包括 CPU 使用率、存儲輸送量和 IOPS 資料。 特別是從 DBMS 層單元收集此資料，但也從應用程式層單元中收集這些資料。 此外，也請測量網路和儲存體延遲。
1.  重新檢查 SAP 支援說明和所需的作業系統設置、SAP HANA 硬體目錄和 SAP PAM。 確保 Azure 支援的 VM 中沒有更改，這些 VM 中支援作業系統版本，以及支援的 SAP 和 DBMS 版本。
1.  更新部署腳本，以考慮您在 VM 類型和 Azure 功能方面做出的最新決策。
1.  部署基礎結構和應用程式後，驗證：
    - 部署了正確的 VM 類型，具有正確的屬性和存儲大小。
    - VM 位於正確和所需的作業系統版本和修補程式上，並且是一致的。
    - VM 根據需要以統一的方式進行硬化。
    - 安裝並部署了正確的應用程式版本和修補程式。
    - VM 已按計劃部署到 Azure 可用性集中。
    - Azure 高級存儲用於延遲敏感磁片或需要[99.9% 的單 VM SLA。](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    - Azure 寫入加速器部署正確。
        - 確保在 VM 內，存儲空間或條帶集在需要寫入加速器的磁片上正確構建。
        - 檢查[Linux上軟體RAID的配置](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)。
        - 檢查[Azure 中的 Linux VM 上的 LVM 配置](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)。
    - [Azure 託管磁片](https://azure.microsoft.com/services/managed-disks/)是專門使用的。
    - VM 已部署到正確的可用性集和可用性區域。
    - [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)在 SAP 應用程式層和 SAP DBMS 層中使用的 VM 上啟用。
    - SAP 應用程式與基於 SAP NetWeaver、Hybris 或 S/4HANA 的 SAP 系統的 DBMS 層之間的通訊路徑中沒有[Azure 網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)。
    - 應用程式安全性群組和網路安全性群組規則允許根據需要進行通信，並根據需要阻止通信。
    - 超時設置設置設置正確，如前面所述。
    - VM 部署到正確的 Azure[接近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，如[Azure 接近放置組中所述，以便使用 SAP 應用程式實現最佳網路延遲](sap-proximity-placement-scenarios.md)。
    - SAP 應用程式層 VM 和 DBMS VM 之間的網路延遲[#500235和](https://launchpad.support.sap.com/#/notes/500235)[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)的 SAP 支援說明中所述。 根據[SAP 支援說明#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的網路延遲指南評估結果。 網路延遲應處於中等或良好範圍內。 例外情況適用于 VM 和 HANA 大型實例單元之間的流量，[如本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所述。
    - 加密是在必要時使用適當的加密方法實現的。
    - 介面和其他應用程式可以連接新部署的基礎結構。
1.  創建行動手冊，以便對計畫的 Azure 維護做出反應。 確定應重新開機系統和 VM 以進行計畫維護的順序。
    

## <a name="go-live-phase"></a>上線階段
在上線階段，請務必遵循您在早期階段開發的劇本。 執行測試和實踐的步驟。 不接受配置和流程的最後一分鐘更改。 還要完成以下步驟：

1. 確認 Azure 入口網站監視功能及其他監視工具正常運作。 我們建議為 Windows 和 Linux SAR 提供 Windows 效能監視器（perfmon）。
    - CPU 計數器。
        - 平均 CPU 時間，總計（所有 CPU）
        - 每個處理器的平均 CPU 時間（M128 VM 上的 128 個處理器）
        - CPU 核心程式的時間，每個處理器
        - CPU使用者時間，每個處理器
    - 記憶體。
        - 可用記憶體
        - 記憶體頁（以/碼錶示）
        - 記憶體頁出/秒
    - 磁碟]。
        - 每個磁片以 KBps 讀取磁片
        - 每個磁片的磁片讀取/秒
        - 每個磁片以微秒/讀取為單位讀取磁片
        - 磁片寫入 KBps，每個磁片
        - 每個磁片的磁片寫入/秒
        - 磁片寫入微秒/讀取，每個磁片
    - 網路。
        - 網路資料包（以/碼錶示）
        - 網路資料包出/秒
        - 網路 KB 以/秒為單位
        - 網路 KB 出/秒
1.  資料移轉後，執行您與業務擁有者商定的所有驗證測試。 僅當原始源系統有結果時，才接受驗證測試結果。
1.  檢查介面是否正常工作，以及其他應用程式是否可以與新部署的生產系統通信。
1.  通過 SAP 事務 STMS 檢查運輸和校正系統。
1.  在系統發佈以進行生產後執行資料庫備份。
1.  在發佈系統進行生產後，對 SAP 應用程式層 VM 執行 VM 備份。
1.  對於不屬於當前上線階段的 SAP 系統，但在此上線階段與遷移到 Azure 的 SAP 系統通信，需要在 SM51 中重置主機名稱緩衝區。 這樣做將刪除與移動到 Azure 的應用程式實例的名稱關聯的舊緩存 IP 位址。  


## <a name="post-production"></a>進入生產環境後
此階段是關於監視、操作和管理系統。 從 SAP 的角度來看，在舊託管位置中完成所需的常見任務適用。 也完成這些特定于 Azure 的任務：

1. 查看高收費系統的 Azure 發票。
2. 優化 VM 端和存儲端的價格/性能效率。
3. 優化可以關閉系統的時間。  


## <a name="next-steps"></a>後續步驟
 請參閱以下文章：

- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署注意事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

