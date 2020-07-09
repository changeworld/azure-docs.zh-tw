---
title: 使用 Azure Site Recovery 設定 SQL Server 的嚴重損壞修復
description: 本文說明如何使用 SQL Server 和 Azure Site Recovery 來設定 SQL Server 的嚴重損壞修復。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 34cfafadabd9a6328cbe85a5444211828df9db6d
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86133715"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>設定 SQL Server 的災害復原

本文說明如何協助保護應用程式的 SQL Server 後端。 您可以使用 SQL Server 商務持續性和嚴重損壞修復（BCDR）技術和[Azure Site Recovery](site-recovery-overview.md)的組合來達到此目的。

開始之前，請確定您瞭解 SQL Server 的嚴重損壞修復功能。 這些功能包括：

* 容錯移轉叢集
* Always On 可用性群組
* 資料庫鏡像
* 記錄傳送
* 使用中的地理複寫
* 自動容錯移轉群組

## <a name="combining-bcdr-technologies-with-site-recovery"></a>結合 BCDR 技術與 Site Recovery

您選擇用來復原 SQL Server 實例的 BCDR 技術應該根據您的復原時間目標（RTO）和復原點目標（RPO）需求，如下表所述。 結合 Site Recovery 與您所選技術的容錯移轉作業，來協調整個應用程式的復原。

部署類型 | BCDR 技術 | SQL Server 的預期 RTO | SQL Server 的預期 RPO |
--- | --- | --- | ---
在 Azure 基礎結構即服務（IaaS）虛擬機器（VM）或內部部署環境中 SQL Server。| [Always On 可用性群組](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | 將次要複本設為主要複本所花費的時間。 | 因為複寫到次要複本是非同步，所以會發生資料遺失的情況。
在 Azure IaaS VM 或內部部署環境中 SQL Server。| [容錯移轉叢集 (Always On FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | 在節點之間進行故障切換所花費的時間。 | 由於 Always On FCI 會使用共用存放裝置，因此在容錯移轉時，會有相同的儲存體實例視圖。
在 Azure IaaS VM 或內部部署環境中 SQL Server。| [資料庫鏡像（高效能模式）](/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | 強制服務所花費的時間，使用鏡像伺服器做為暖待命伺服器。 | 複寫不是同步進行。 鏡像資料庫可能會稍微落後主體資料庫。 延遲通常很小。 但如果主體或鏡像伺服器的系統負載過重，可能會變得很大。<br/><br/>記錄傳送可以是資料庫鏡像的補充。 這是非同步資料庫鏡像的理想替代方法。
SQL 做為 Azure 上的平臺即服務（PaaS）。<br/><br/>此部署類型包含單一資料庫和彈性集區。 | 使用中的地理複寫 | 觸發容錯移轉後的30秒。<br/><br/>針對其中一個次要資料庫啟用容錯移轉時，所有其他的次要複本都會自動連結至新的主要複本。 | 五秒的 RPO。<br/><br/>主動式異地複寫使用 SQL Server 的 Always On 技術。 它會使用快照集隔離，以非同步方式將主資料庫上認可的交易複寫到次要資料庫。<br/><br/>次要資料保證永遠不會有部分交易。
使用 Azure 上的主動式異地複寫設定的 SQL 做為 PaaS。<br/><br/>此部署類型包含受控實例、彈性集區和單一資料庫。 | 自動容錯移轉群組 | 一小時的 RTO。 | 五秒的 RPO。<br/><br/>自動容錯移轉群組在主動式異地複寫之上提供群組語義。 但使用相同的非同步複寫機制。
在 Azure IaaS VM 或內部部署環境中 SQL Server。| 使用 Azure Site Recovery 進行複寫 | RTO 通常少於15分鐘。 若要深入瞭解，請參閱[Site Recovery 所提供的 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 | 應用程式一致性的一小時，以及5分鐘的損毀一致性。 如果您要尋找較低的 RPO，請使用其他 BCDR 技術。

> [!NOTE]
> 當您使用 Site Recovery 來協助保護 SQL 工作負載時，有幾個重要的考慮：
> * Site Recovery 與應用程式無關。 Site Recovery 可以協助保護部署在支援的作業系統上的任何 SQL Server 版本。 若要深入瞭解，請參閱復原已複寫機器的[支援矩陣](vmware-physical-azure-support-matrix.md#replicated-machines)。
> * 您可以選擇在 Azure、Hyper-v、VMware 或實體基礎結構的任何部署中使用 Site Recovery。 請遵循本文結尾的指導方針，[以瞭解如何使用 Site Recovery 協助保護 SQL Server](#how-to-help-protect-a-sql-server-cluster)叢集。
> * 請確定電腦上觀察到的資料變更率在[Site Recovery 限制](vmware-physical-azure-support-matrix.md#churn-limits)內。 變更率是以每秒寫入位元組數來測量。 對於執行 Windows 的電腦，您可以選取 [工作管理員] 中的 [**效能**] 索引標籤，以查看此變更率。 觀察每個磁片的寫入速度。
> * Site Recovery 支援儲存空間直接存取上的容錯移轉叢集實例複寫。 若要深入瞭解，請參閱[如何啟用儲存空間直接存取](azure-to-azure-how-to-enable-replication-s2d-vms.md)複寫。

## <a name="disaster-recovery-of-an-application"></a>應用程式的損毀修復

Site Recovery 會透過復原計畫的協助，協調整個應用程式的測試容錯移轉和容錯移轉。

有一些必要條件可確保您的復原計畫已根據您的需求完全自訂。 任何 SQL Server 部署通常需要 Active Directory 部署。 它也需要您應用層的連線能力。

### <a name="step-1-set-up-active-directory"></a>步驟1：設定 Active Directory

設定次要復原網站中的 Active Directory，讓 SQL Server 正常執行。

* **小型企業**：您有少數的應用程式，以及適用于內部部署網站的單一網域控制站。 如果您想要故障通過整個網站，請使用 Site Recovery 複寫。 此服務會將網域控制站複寫至次要資料中心或 Azure。
* **中型到大型企業**：您可能需要設定額外的網域控制站。
  - 如果您有大量的應用程式、具有 Active Directory 樹系，而且想要依應用程式或工作負載進行故障切換，請在次要資料中心或 Azure 中設定另一個網域控制站。
  -  如果您使用 Always On 可用性群組復原至遠端網站，請在次要網站或 Azure 中設定另一個網域控制站。 此網域控制站用於復原的 SQL Server 實例。

本文中的指示假設在次要位置中可以使用網域控制站。 若要深入瞭解，請參閱[使用 Site Recovery 協助保護 Active Directory](site-recovery-active-directory.md)的程式。

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>步驟2：確定與其他層的連線能力

在目標 Azure 區域中執行資料庫層之後，請確定您已與應用程式和 web 層連接。 事先採取必要的步驟，以驗證與測試容錯移轉的連線能力。

若要瞭解如何設計連線性考慮的應用程式，請參閱下列範例：

* [設計雲端嚴重損壞修復的應用程式](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [彈性集區嚴重損壞修復策略](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>步驟3：與 Always On、主動式異地複寫和自動容錯移轉群組交互操作

BCDR 技術 Always On、主動式異地複寫和自動容錯移轉群組具有在目標 Azure 區域中執行 SQL Server 的次要複本。 應用程式容錯移轉的第一個步驟是將此複本指定為「主要」。 此步驟假設您在次要資料庫中已有網域控制站。 如果您選擇執行自動容錯移轉，則可能不需要此步驟。 只有在資料庫容錯移轉完成後，才會容錯移轉您的 web 和應用層。

> [!NOTE]
> 如果您已協助使用 Site Recovery 來保護 SQL 機器，您只需要建立這些機器的復原群組，並在復原計畫中新增其容錯移轉。

[建立](site-recovery-create-recovery-plans.md)應用程式和 web 層虛擬機器的復原方案。 下列步驟示範如何加入資料庫層的容錯移轉：

1. 匯入腳本，以在[Resource Manager 的虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)和[傳統虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中故障切換 SQL 可用性群組。 將腳本匯入您的 Azure 自動化帳戶。

    [![「部署至 Azure」標誌的影像](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 新增 Asr-sql-failoverag 腳本作為復原方案第一個群組的前置動作。

1. 請遵循腳本中提供的指示來建立自動化變數。 這個變數會提供可用性群組的名稱。

### <a name="step-4-conduct-a-test-failover"></a>步驟4：執行測試容錯移轉

某些 BCDR 技術（例如 SQL Always On）原本就不支援測試容錯移轉。 *只有在使用這類技術時，才*建議您使用下列方法。

1. 在 Azure 中裝載可用性群組複本的 VM 上設定[Azure 備份](../backup/backup-azure-vms-first-look-arm.md)。

1. 觸發復原方案的測試容錯移轉之前，請先從上一個步驟中所建立的備份來復原 VM。

    ![顯示從 Azure 備份還原設定之視窗的螢幕擷取畫面](./media/site-recovery-sql/restore-from-backup.png)

1. 在 VM 中[強制執行](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)從備份還原的仲裁。

1. 將接聽程式的 IP 位址更新為測試容錯移轉網路中可用的位址。

    ![[規則] 視窗和 [IP 位址屬性] 對話方塊的螢幕擷取畫面](./media/site-recovery-sql/update-listener-ip.png)

1. 使接聽程式連線。

    ![顯示伺服器名稱和狀態 Content_AG 標籤的視窗螢幕擷取畫面](./media/site-recovery-sql/bring-listener-online.png)

1. 請確定容錯移轉網路中的負載平衡器具有一個 IP 位址、來自對應到每個可用性群組接聽程式的前端 IP 位址池，以及後端集區中的 SQL Server VM。

     ![標題為「SQL-AlwaysOn-l i-前端 IP 集區」之視窗的螢幕擷取畫面](./media/site-recovery-sql/create-load-balancer1.png)

    ![標題為「SQL-AlwaysOn-LB-後端 IP 集區」之視窗的螢幕擷取畫面](./media/site-recovery-sql/create-load-balancer2.png)

1. 在後續的復原群組中，為您的應用層新增此復原方案的容錯移轉，後面接著您的 web 層。

1. 執行復原方案的測試容錯移轉，以測試應用程式的端對端容錯移轉。

## <a name="steps-to-do-a-failover"></a>進行容錯移轉的步驟

在步驟3中新增腳本並在步驟4中進行驗證之後，您就可以執行在步驟3中建立之復原方案的容錯移轉。

在測試容錯移轉和容錯移轉復原計畫中，應用程式和 web 層的容錯移轉步驟應相同。

## <a name="how-to-help-protect-a-sql-server-cluster"></a>如何協助保護 SQL Server 叢集

對於執行 SQL Server Standard edition 或 SQL Server 2008 R2 的叢集，建議您使用 Site Recovery 複寫來協助保護 SQL Server。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 至 azure 和內部部署至 Azure

Site Recovery 不會在複寫至 Azure 區域時提供來賓叢集支援。 SQL Server Standard edition 也不提供低成本的嚴重損壞修復解決方案。 在此案例中，建議您將 SQL Server 叢集保護到主要位置中的獨立 SQL Server 實例，並在次要資料庫中將它復原。

1. 在主要 Azure 區域或內部部署網站上設定額外的獨立 SQL Server 實例。

1. 將實例設定為要協助保護之資料庫的鏡像。 在高安全性模式下設定鏡像。

1. 在[Azure](azure-to-azure-tutorial-enable-replication.md)、 [Hyper-v](./hyper-v-azure-tutorial.md)或[VMware vm 和實體伺服器](./vmware-azure-tutorial.md)的主要網站上設定 Site Recovery。

1. 使用 Site Recovery 複寫，將新的 SQL Server 實例複寫至次要網站。 因為它是高安全性鏡像複本，所以它會與主要叢集同步處理，但是會使用 Site Recovery 複寫進行複寫。

   ![標準叢集的影像，顯示主要網站、Site Recovery 和 Azure 之間的關聯性和流程](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>容錯回復考量

針對 SQL Server 標準叢集，在未規劃的容錯移轉之後進行容錯回復需要 SQL Server 備份和還原。 這項作業是透過重新建立鏡像，從鏡像實例到原始叢集進行。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>搭配 Site Recovery 使用時，SQL Server 如何取得授權？

「軟體保證嚴重損壞修復」權益涵蓋 SQL Server 的 Site Recovery 複寫。 本涵蓋範圍適用于所有 Site Recovery 案例：內部部署至 Azure 的嚴重損壞修復和跨區域 Azure IaaS 嚴重損壞修復。 如需詳細資訊，請參閱[Azure Site Recovery 定價](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery 支援我的 SQL Server 版本嗎？

Site Recovery 與應用程式無關。 Site Recovery 可以協助保護部署在支援的作業系統上的任何 SQL Server 版本。 如需詳細資訊，請參閱復原已複寫機器的[支援矩陣](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Site Recovery 架構](./azure-to-azure-architecture.md)。
* 如需 Azure 中的 SQL Server，請深入瞭解在次要 Azure 區域中復原的[高可用性解決方案](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)。
* 如 SQL Database，請深入瞭解在次要 Azure 區域中復原的[商務持續性](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)和[高可用性](../azure-sql/database/high-availability-sla.md)選項。
* 如需在內部部署 SQL Server 機，請深入瞭解 Azure 虛擬機器中復原的[高可用性選項](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)。
