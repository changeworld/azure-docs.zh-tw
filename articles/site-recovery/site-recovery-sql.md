---
title: 使用 Azure 網站恢復為 SQL Server 設置災害復原
description: 本文介紹如何使用 SQL Server 和 Azure 網站恢復為 SQL Server 設置災害復原。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084745"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>設定 SQL Server 的災害復原

本文介紹如何説明保護應用程式的後端的 SQL Server。 通過使用 SQL Server 業務連續性和災害復原 （BCDR） 技術和 Azure[網站恢復](site-recovery-overview.md)的組合來執行此操作。

在開始之前，請確保您瞭解 SQL Server 災害復原功能。 這些功能包括：

* 容錯移轉叢集
* Always On 可用性群組
* 資料庫鏡像
* 記錄傳送
* 使用中的地理複寫
* 自動容錯移轉群組

## <a name="combining-bcdr-technologies-with-site-recovery"></a>將 BCDR 技術與網站恢復相結合

您選擇 BCDR 技術來恢復 SQL Server 實例應基於您的恢復時間目標 （RTO） 和復原點目標 （RPO） 需求，如下表所述。 將網站恢復與所選技術的容錯移轉操作相結合，以協調整個應用程式的恢復。

部署類型 | BCDR 技術 | SQL 伺服器的預期 RTO | SQL 伺服器的預期 RPO |
--- | --- | --- | ---
Azure 基礎結構上的 SQL Server 作為服務 （IaaS） 虛擬機器 （VM） 或本地。| [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | 使輔助副本為主副本所佔用的時間。 | 由於對輔助副本的複製是非同步，因此存在一些資料丟失。
Azure IaaS VM 或本地的 SQL 伺服器。| [容錯移轉叢集 (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | 節點之間容錯移轉所花時間。 | 由於"始終在 FCI 上"使用共用存儲，因此在容錯移轉時可以使用存儲實例的相同視圖。
Azure IaaS VM 或本地的 SQL 伺服器。| [資料庫鏡像（高性能模式）](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | 強制服務所花的時間，該服務使用鏡像伺服器作為暖待命伺服器。 | 複寫不是同步進行。 鏡像資料庫可能會稍微落後主體資料庫。 滯後通常很小。 但是，如果主體或鏡像伺服器的系統負載很大，則可能會變大。<br/><br/>記錄傳送可以補充資料庫鏡像。 它是非同步資料庫鏡像的有利替代方法。
SQL 作為 Azure 上的服務 （PaaS） 的平臺。<br/><br/>此部署類型包括彈性池和 Azure SQL 資料庫伺服器。 | 使用中的地理複寫 | 容錯移轉觸發後 30 秒。<br/><br/>當為其中一個次要資料庫啟動容錯移轉時，所有其他次要資料庫將自動連結到新的主資料庫。 | 五秒的 RPO。<br/><br/>活動異地複製使用 SQL Server 的"始終打開"技術。 它通過使用快照隔離將主資料庫上提交的事務非同步複製到次要資料庫。<br/><br/>輔助資料保證永遠不會有部分事務。
SQL 作為 PaaS 配置了 Azure 上的活動異地複製。<br/><br/>此部署類型包括 SQL 資料庫託管實例、彈性池和 SQL 資料庫伺服器。 | 自動容錯移轉群組 | 一小時的 RTO。 | 五秒的 RPO。<br/><br/>自動容錯移轉組在活動異地複製之上提供組語義。 但使用相同的非同步複製機制。
Azure IaaS VM 或本地的 SQL 伺服器。| 使用 Azure 網站恢復進行複製 | RTO 通常少於 15 分鐘。 要瞭解更多資訊，請閱讀[網站恢復提供的 RTO SLA。](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) | 應用程式一致性為 1 小時，崩潰一致性為 5 分鐘。 如果您正在尋找更低的 RPO，請使用其他 BCDR 技術。

> [!NOTE]
> 當您説明使用網站恢復保護 SQL 工作負載時，需要考慮以下幾個事項：
> * 網站恢復與應用程式無關。 網站恢復可説明保護部署在受支援的作業系統上的任何版本的 SQL Server。 要瞭解更多資訊，請參閱複製電腦[恢復的支援矩陣](vmware-physical-azure-support-matrix.md#replicated-machines)。
> * 您可以選擇在 Azure、Hyper-V、VMware 或物理基礎結構的任何部署中使用網站恢復。 請按照本文末尾的指南，瞭解如何使用網站恢復[説明保護 SQL Server 群集](#how-to-help-protect-a-sql-server-cluster)。
> * 確保機器上觀察到的資料更改速率在[網站恢復限制](vmware-physical-azure-support-matrix.md#churn-limits)範圍內。 更改速率以每秒寫入位元組為單位進行測量。 對於運行 Windows 的電腦，您可以通過在工作管理員中選擇 **"性能**"選項卡來查看此更改率。 觀察每個磁片的寫入速度。
> * 網站恢復支援在存儲空間直接上複製容錯移轉叢集實例。 要瞭解更多資訊，請參閱[如何啟用存儲空間直接複製](azure-to-azure-how-to-enable-replication-s2d-vms.md)。

## <a name="disaster-recovery-of-an-application"></a>應用程式的災害復原

網站恢復在恢復計畫的説明下協調整個應用程式的測試容錯移轉和容錯移轉。

有一些先決條件，以確保您的恢復計畫完全定制根據您的需要。 任何 SQL Server 部署通常需要活動目錄部署。 它還需要應用程式層的連接。

### <a name="step-1-set-up-active-directory"></a>第 1 步：設置活動目錄

在輔助恢復網站中設置活動目錄，以便 SQL Server 正常運行。

* **小型企業**：您有少量的應用程式和一個本地網站的網域控制站。 如果要故障接管整個網站，請使用網站恢復複製。 此服務將網域控制站複製到輔助資料中心或 Azure。
* **大中型企業**：您可能需要設置其他網域控制站。
  - 如果您有大量應用程式、具有 Active Directory 林，並且希望按應用程式或工作負荷進行容錯移轉，請在輔助資料中心或 Azure 中設置另一個網域控制站。
  -  如果使用"始終打開"可用性組恢復到遠端站台，請在輔助網站或 Azure 中設置另一個網域控制站。 此網域控制站用於恢復的 SQL Server 實例。

本文中的說明假定網域控制站在輔助位置可用。 要瞭解更多資訊，請參閱[説明使用網站恢復保護活動目錄](site-recovery-active-directory.md)的過程。

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>第 2 步：確保與其他層連接

在目標 Azure 區域中運行資料庫層後，請確保與應用程式和 Web 層具有連接。 提前採取必要步驟，驗證與測試容錯移轉的連接。

要瞭解如何設計應用程式以考慮連接，請參閱以下示例：

* [設計雲災害復原應用程式](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [彈性池災害復原策略](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>步驟 3：與"始終打開"、活動異地複製和自動容錯移轉組交互操作

BCDR 技術始終打開、活動異地複製和自動容錯移轉組在目標 Azure 區域中運行 SQL Server 的輔助副本。 應用程式容錯移轉的第一步是指定此副本為主副本。 此步驟假定輔助控制器中已有網域控制站。 如果選擇執行自動容錯移轉，則可能不需要此步驟。 僅在資料庫容錯移轉完成後，才能對 Web 和應用程式層進行容錯移轉。

> [!NOTE]
> 如果説明使用網站恢復來保護 SQL 電腦，則只需創建這些電腦的恢復組，並在恢復計畫中添加其容錯移轉。

使用應用程式和 Web 層虛擬機器[創建恢復計畫](site-recovery-create-recovery-plans.md)。 以下步驟演示如何添加資料庫層的容錯移轉：

1. 導入腳本以在[資源管理器虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)和[經典虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中容錯移轉 SQL 可用性組。 將腳本導入 Azure 自動化帳戶。

    [!["部署到 Azure"徽標的圖像](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 添加 ASR-SQL-FailoverAG 腳本作為恢復計畫的第一組的預操作。

1. 按照腳本中提供的說明創建自動化變數。 此變數提供可用性組的名稱。

### <a name="step-4-conduct-a-test-failover"></a>第 4 步：執行測試容錯移轉

某些 BCDR 技術（如 SQL 始終打開）不支援本機測試容錯移轉。 *我們只在使用此類技術時*才推薦以下方法。

1. 在 Azure 中承載可用性組副本的 VM 上設置[Azure 備份](../backup/backup-azure-arm-vms.md)。

1. 在觸發恢復計畫的測試容錯移轉之前，從上一步中的備份中恢復 VM。

    ![顯示從 Azure 備份還原配置的視窗的螢幕截圖](./media/site-recovery-sql/restore-from-backup.png)

1. [強制](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)從備份還原的 VM 中的仲裁。

1. 將攔截器的 IP 位址更新為測試容錯移轉網路中可用的位址。

    ![規則視窗和 IP 位址屬性對話方塊的螢幕截圖](./media/site-recovery-sql/update-listener-ip.png)

1. 使接聽程式連線。

    ![顯示伺服器名稱和狀態Content_AG視窗的螢幕截圖](./media/site-recovery-sql/bring-listener-online.png)

1. 確保容錯移轉網路中的負載等化器具有一個 IP 位址、與每個可用性組攔截器對應的前端 IP 位址池以及後端池中的 SQL Server VM。

     ![標題為"SQL-Alwayson-LB - 前端 IP 池"的視窗螢幕截圖](./media/site-recovery-sql/create-load-balancer1.png)

    ![標題為"SQL-Alwayson-LB - 後端 IP 池"的視窗螢幕截圖](./media/site-recovery-sql/create-load-balancer2.png)

1. 在以後的恢復組中，添加應用程式層的容錯移轉，然後添加 Web 層，用於此恢復計畫。

1. 執行恢復計畫的測試容錯移轉，以測試應用程式的端到端容錯移轉。

## <a name="steps-to-do-a-failover"></a>進行容錯移轉的步驟

在步驟 3 中添加腳本並在步驟 4 中驗證腳本後，可以執行步驟 3 中創建的恢復計畫容錯移轉。

在測試容錯移轉和容錯移轉恢復計畫中，應用程式和 Web 層的容錯移轉步驟應相同。

## <a name="how-to-help-protect-a-sql-server-cluster"></a>如何説明保護 SQL Server 群集

對於運行 SQL Server 標準版或 SQL Server 2008 R2 的群集，我們建議您使用網站恢復複製來説明保護 SQL Server。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 到 Azure 和本地到 Azure

網站恢復在複製到 Azure 區域時不提供來賓群集支援。 SQL Server 標準版也不提供低成本的災害復原解決方案。 在這種情況下，我們建議您將 SQL Server 群集保護到主位置的獨立 SQL Server 實例，並在輔助位置恢復它。

1. 在主 Azure 區域或本地網站上配置其他獨立 SQL Server 實例。

1. 將實例配置為充當要説明保護的資料庫的鏡像。 在高安全模式下配置鏡像。

1. 在[Azure、Hyper-V](site-recovery-hyper-v-site-to-azure.md)[Azure](azure-to-azure-tutorial-enable-replication.md)或[VMware VM 和物理伺服器](site-recovery-vmware-to-azure-classic.md)的主網站上配置網站恢復。

1. 使用網站恢復複製將新的 SQL Server 實例複製到輔助網站。 因為它是高安全性鏡像副本，它將與主群集同步，但使用網站恢復複製進行複製。

   ![顯示主網站、網站恢復和 Azure 之間的關係和流的標準群集的圖像](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>容錯回復考量

對於 SQL Server 標準群集，計畫外容錯移轉後容錯移轉需要 SQL Server 備份和還原。 此操作從鏡像實例到原始群集，重新建立鏡像。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>與網站恢復一起使用時，SQL Server 如何獲得許可？

SQL Server 的網站恢復複製包含在軟體保證災害復原權益中。 此覆蓋範圍適用于所有網站恢復方案：本地到 Azure 災害復原和跨區域 Azure IaaS 災害復原。 有關詳細資訊[，請參閱 Azure 網站恢復定價](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="will-site-recovery-support-my-sql-server-version"></a>網站恢復是否會支援我的 SQL Server 版本？

網站恢復與應用程式無關。 網站恢復可説明保護部署在受支援的作業系統上的任何版本的 SQL Server。 有關詳細資訊，請參閱複製電腦[恢復的支援矩陣](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[網站恢復體系結構](site-recovery-components.md)的更多。
* 對於 Azure 中的 SQL Server，詳細瞭解輔助 Azure 區域中用於恢復[的高可用性解決方案](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)。
* 對於 SQL 資料庫，詳細瞭解輔助 Azure 區域中[恢復的業務連續性](../sql-database/sql-database-business-continuity.md)和[高可用性](../sql-database/sql-database-high-availability.md)選項。
* 對於本地的 SQL Server 電腦，詳細瞭解 Azure 虛擬機器中恢復的高[可用性選項](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions)。
