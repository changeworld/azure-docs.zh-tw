---
title: 使用 Azure Site Recovery 設定 SQL Server 的嚴重損壞修復
description: 本文說明如何使用 SQL Server 和 Azure Site Recovery，為 SQL Server 設定嚴重損壞修復。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 1b02b089fea7e883bdc6c58c7a2845af12b50a37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87824523"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>設定 SQL Server 的災害復原

本文說明如何協助保護應用程式的 SQL Server 後端。 您可以使用 SQL Server 商務持續性和嚴重損壞修復的組合 (BCDR) 技術和 [Azure Site Recovery](site-recovery-overview.md)。

開始之前，請確定您瞭解 SQL Server 的嚴重損壞修復功能。 這些功能包括：

* 容錯移轉叢集
* Always On 可用性群組
* 資料庫鏡像
* 記錄傳送
* 使用中的地理複寫
* 自動容錯移轉群組

## <a name="combining-bcdr-technologies-with-site-recovery"></a>結合 BCDR 技術與 Site Recovery

您選擇用來復原 SQL Server 實例的 BCDR 技術，應該根據您的復原時間目標 (RTO) 和復原點目標 (RPO) 需求，如下表所述。 結合 Site Recovery 與您所選技術的容錯移轉作業，以協調整個應用程式的復原。

部署類型 | BCDR 技術 | SQL Server 的預期 RTO | SQL Server 的預期 RPO |
--- | --- | --- | ---
SQL Server Azure 基礎結構即服務 (IaaS) 虛擬機器 (VM) 或內部部署。| [Always On 可用性群組](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | 使次要複本成為主要複本所花費的時間。 | 由於複寫至次要複本是非同步，因此會遺失一些資料。
在 Azure IaaS VM 上或在內部部署 SQL Server。| [容錯移轉叢集 (Always On FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | 在節點之間進行容錯移轉所花費的時間。 | 因為 Always On FCI 使用共用存放裝置，所以在容錯移轉時，會有儲存體實例的相同觀點。
在 Azure IaaS VM 上或在內部部署 SQL Server。| [ (高效能模式的資料庫鏡像) ](/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | 強制服務所花費的時間，使用鏡像伺服器做為暖待命伺服器。 | 複寫不是同步進行。 鏡像資料庫可能會稍微落後主體資料庫。 延遲通常很小。 但是如果主體或鏡像伺服器的系統負載過重，則可能會變成龐大。<br/><br/>記錄傳送可以是資料庫鏡像的補充。 這是非同步資料庫鏡像的理想替代方案。
SQL 作為平臺即服務 (PaaS) 在 Azure 上。<br/><br/>此部署類型包含單一資料庫和彈性集區。 | 使用中的地理複寫 | 觸發容錯移轉之後的30秒。<br/><br/>針對其中一個次要資料庫啟用容錯移轉時，所有其他次要複本都會自動連結到新的主資料庫。 | 五秒的 RPO。<br/><br/>主動式異地複寫使用 SQL Server 的 Always On 技術。 它會使用快照集隔離，以非同步方式將主資料庫上認可的交易複寫到次要資料庫。<br/><br/>次要資料保證永遠不會有部分交易。
使用 Azure 上的主動式異地複寫設定的 SQL 為 PaaS。<br/><br/>此部署類型包含受管理的實例、彈性集區和單一資料庫。 | 自動容錯移轉群組 | 一小時的 RTO。 | 五秒的 RPO。<br/><br/>自動容錯移轉群組會在主動式異地複寫之上提供群組語義。 但使用相同的非同步複寫機制。
在 Azure IaaS VM 上或在內部部署 SQL Server。| 使用 Azure Site Recovery 複寫 | RTO 通常少於15分鐘。 若要深入瞭解，請閱讀 [Site Recovery 所提供的 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 | 應用程式一致性為一小時，而當機一致性為五分鐘。 如果您要尋找較低的 RPO，請使用其他 BCDR 技術。

> [!NOTE]
> 當您使用 Site Recovery 協助保護 SQL 工作負載時，有幾個重要的考慮：
> * Site Recovery 是應用程式中立的。 Site Recovery 可協助保護任何部署在支援之作業系統上的 SQL Server 版本。 若要深入瞭解，請參閱 [支援矩陣以](vmware-physical-azure-support-matrix.md#replicated-machines) 復原已複寫的機器。
> * 您可以選擇使用 Site Recovery 在 Azure、Hyper-v、VMware 或實體基礎結構上進行任何部署。 請遵循本文結尾的指導方針， [以瞭解如何使用 Site Recovery 協助保護 SQL Server](#how-to-help-protect-a-sql-server-cluster) 叢集。
> * 確定電腦上觀察到的資料變更率是在 [Site Recovery 限制](vmware-physical-azure-support-matrix.md#churn-limits)內。 變更率是以每秒的寫入位元組數來測量。 針對執行 Windows 的電腦，您可以選取工作管理員中的 [ **效能** ] 索引標籤，以查看此變更率。 觀察每個磁片的寫入速度。
> * Site Recovery 支援儲存空間直接存取上的容錯移轉叢集實例複寫。 若要深入瞭解，請參閱 [如何啟用儲存空間直接存取](azure-to-azure-how-to-enable-replication-s2d-vms.md)複寫。
> 
> 當您將 SQL 工作負載遷移至 Azure 時，建議您 [在 Azure 虛擬機器上套用 SQL Server 的效能指導方針](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)。

## <a name="disaster-recovery-of-an-application"></a>應用程式的嚴重損壞修復

Site Recovery 透過復原計畫的協助來協調整個應用程式的測試容錯移轉和容錯移轉。

有一些必要條件可確保您的復原方案會根據您的需求完全自訂。 任何 SQL Server 部署通常都需要 Active Directory 部署。 它也需要您應用層的連線能力。

### <a name="step-1-set-up-active-directory"></a>步驟1：設定 Active Directory

在次要復原網站中設定 Active Directory，讓 SQL Server 正常執行。

* **Small enterprise**：您有少數的應用程式，以及內部部署網站的單一網域控制站。 如果您想要容錯移轉整個網站，請使用 Site Recovery 複寫。 此服務會將網域控制站複寫至次要資料中心或 Azure。
* **中型至大型企業**：您可能需要設定額外的網域控制站。
  - 如果您有大量應用程式、Active Directory 樹系，且想要依應用程式或工作負載進行容錯移轉，請在次要資料中心或 Azure 中設定另一個網域控制站。
  -  如果您使用 Always On 可用性群組復原至遠端網站，請在次要網站或 Azure 中設定另一個網域控制站。 此網域控制站用於已復原的 SQL Server 實例。

本文中的指示假設在次要位置中有可用的網域控制站。 若要深入瞭解，請參閱 [使用 Site Recovery 協助保護 Active Directory](site-recovery-active-directory.md)的程式。

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>步驟2：確定與其他層的連線能力

在目標 Azure 區域中執行資料庫層之後，請確定您已與應用程式和 web 層連接。 事先採取必要的步驟，以驗證與測試容錯移轉的連接。

若要瞭解如何設計應用程式以進行連線考慮，請參閱下列範例：

* [設計雲端災難復原的應用程式](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [彈性集區嚴重損壞修復策略](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>步驟3：與 Always On、主動式異地複寫和自動容錯移轉群組交互操作

BCDR 技術 Always On、主動式異地複寫和自動容錯移轉群組都具有在目標 Azure 區域中執行 SQL Server 的次要複本。 應用程式容錯移轉的第一個步驟是將此複本指定為主要複本。 此步驟假設您在次要區域中已經有網域控制站。 如果您選擇進行自動容錯移轉，則可能不需要此步驟。 只有在資料庫容錯移轉完成之後，才會容錯移轉您的 web 和應用層。

> [!NOTE]
> 如果您已協助保護 Site Recovery 的 SQL 電腦，您只需要建立這些電腦的復原群組，並在復原方案中新增其容錯移轉。

使用應用程式和 web 層虛擬機器[建立復原方案](site-recovery-create-recovery-plans.md)。 下列步驟顯示如何加入資料庫層的容錯移轉：

1. 匯入腳本，以在 [Resource Manager 虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) 和 [傳統虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中進行 SQL 可用性群組的容錯移轉。 將腳本匯入您的 Azure 自動化帳戶。

    [![「部署至 Azure」標誌的影像](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 將 ASR-SQL-Asr-sql-failoverag 腳本新增為復原方案第一個群組的前置動作。

1. 依照腳本中的指示來建立自動化變數。 此變數提供可用性群組的名稱。

### <a name="step-4-conduct-a-test-failover"></a>步驟4：進行測試容錯移轉

某些 BCDR 技術（例如 SQL Always On）本身不支援測試容錯移轉。 *只有在使用這類技術時，才建議使用*下列方法。

1. 設定在 Azure 中裝載可用性群組複本的 VM 上的 [Azure 備份](../backup/backup-azure-vms-first-look-arm.md) 。

1. 在觸發復原計畫的測試容錯移轉之前，請從上一個步驟所建立的備份復原 VM。

    ![顯示從 Azure 備份還原設定之視窗的螢幕擷取畫面](./media/site-recovery-sql/restore-from-backup.png)

1. 在已從備份還原的 VM 中[強制執行仲裁](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)。

1. 將接聽程式的 IP 位址更新為測試容錯移轉網路中可用的位址。

    ![[規則視窗] 和 [IP 位址屬性] 對話方塊的螢幕擷取畫面](./media/site-recovery-sql/update-listener-ip.png)

1. 使接聽程式連線。

    ![顯示伺服器名稱和狀態的視窗標示 Content_AG 的螢幕擷取畫面](./media/site-recovery-sql/bring-listener-online.png)

1. 確定容錯移轉網路中的負載平衡器有一個 IP 位址，從對應至每個可用性群組接聽程式的前端 IP 位址池，以及後端集區中 SQL Server VM。

     ![標題為 "SQL-AlwaysOn-LB-前端 IP 集區] 的螢幕擷取畫面](./media/site-recovery-sql/create-load-balancer1.png)

    ![標題為 [SQL-AlwaysOn-LB-後端 IP 集區] 的螢幕擷取畫面](./media/site-recovery-sql/create-load-balancer2.png)

1. 在稍後的復原群組中，針對此復原方案新增應用層的容錯移轉，然後再加入您的 web 層。

1. 進行復原計畫的測試容錯移轉，以測試應用程式的端對端容錯移轉。

## <a name="steps-to-do-a-failover"></a>進行容錯移轉的步驟

在步驟3中新增腳本並在步驟4中進行驗證之後，您可以執行步驟3中建立之復原方案的容錯移轉。

在測試容錯移轉和容錯移轉復原計畫中，應用程式和 web 層的容錯移轉步驟應該相同。

## <a name="how-to-help-protect-a-sql-server-cluster"></a>如何協助保護 SQL Server 叢集

對於執行 SQL Server Standard edition 或 SQL Server 2008 R2 的叢集，建議您使用 Site Recovery 複寫來協助保護 SQL Server。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 到 Azure 和內部部署至 Azure

Site Recovery 不會在複寫至 Azure 區域時提供來賓叢集支援。 SQL Server Standard edition 也不提供低成本的嚴重損壞修復解決方案。 在此案例中，建議您將 SQL Server 叢集保護至主要位置中的獨立 SQL Server 實例，並在次要資料庫中加以復原。

1. 在主要 Azure 區域或內部部署網站上設定額外的獨立 SQL Server 實例。

1. 將實例設定為您想要協助保護之資料庫的鏡像。 在高安全性模式下設定鏡像。

1. 在 [Azure](azure-to-azure-tutorial-enable-replication.md)、 [Hyper-v](./hyper-v-azure-tutorial.md)或 [VMware vm 和實體伺服器](./vmware-azure-tutorial.md)的主要網站上設定 Site Recovery。

1. 使用 Site Recovery 複寫將新的 SQL Server 實例複寫至次要網站。 由於它是高安全性的重新影複，因此它會與主要叢集同步處理，但是會使用 Site Recovery 複寫進行複寫。

   ![標準叢集的影像，顯示主要網站、Site Recovery 和 Azure 之間的關聯性和流程](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>容錯回復考量

針對 SQL Server 標準叢集，在未規劃的容錯移轉之後進行容錯回復需要 SQL Server 備份和還原。 這項作業是透過重新建立鏡像，從鏡像實例至原始叢集進行。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>使用 Site Recovery 時，SQL Server 如何獲得授權？

SQL Server 的 Site Recovery 複寫會在軟體保證嚴重損壞修復權益下討論。 此涵蓋範圍適用于所有 Site Recovery 案例：內部部署至 Azure 的嚴重損壞修復和跨區域 Azure IaaS 嚴重損壞修復。 如需詳細資訊，請參閱 [Azure Site Recovery 定價](https://azure.microsoft.com/pricing/details/site-recovery/) 。

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery 支援我的 SQL Server 版本嗎？

Site Recovery 是應用程式中立的。 Site Recovery 可協助保護任何部署在支援之作業系統上的 SQL Server 版本。 如需詳細資訊，請參閱 [支援矩陣以](vmware-physical-azure-support-matrix.md#replicated-machines) 復原已複寫的機器。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Site Recovery 架構](./azure-to-azure-architecture.md)。
* 針對 Azure 中的 SQL Server，深入瞭解在次要 Azure 區域中復原的 [高可用性解決方案](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) 。
* 如 SQL Database，請深入瞭解在次要 Azure 區域中復原的 [商務持續性](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) 和 [高可用性](../azure-sql/database/high-availability-sla.md) 選項。
* 針對位於內部部署 SQL Server 的電腦，深入瞭解 Azure 虛擬機器中的復原 [高可用性選項](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) 。