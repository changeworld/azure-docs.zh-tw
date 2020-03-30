---
title: 選擇正確的部署選項
description: 瞭解如何在 Azure 虛擬機器上的 SQL 資料庫、SQL 託管實例和 SQL Server 之間選擇 Azure SQL 中的部署選項。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server 雲端, 雲端中的 SQL Server, PaaS 資料庫, 雲端 SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/22/2019
ms.openlocfilehash: e642454807511e8e0bc0b6b6ca7af837e03de2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821340"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>在 Azure SQL 中選擇正確的部署選項

瞭解每個部署選項如何適合 Microsoft 的 Azure SQL 資料平臺，並獲取有關匹配適合業務需求選項的説明。 無論您是優先考慮成本節約還是將管理降至最低，本文都可以説明您確定哪種方法針對您最關心的業務需求提供。

## <a name="microsofts-azure-sql-data-platform"></a>微軟的 Azure SQL 資料平臺

從升降遷移到現有應用程式的現代化，到構建現代雲服務，Azure SQL 是一個現代 SQL 平臺，提供多種部署選項，由業界領先的 Microsoft SQL Server 引擎提供支援。 Azure SQL 旨在支援各種應用程式模式，對底層平臺進行不同級別的控制，以滿足最苛刻的遷移和現代化要求。 Azure SQL 通過提供單一的統一管理體驗，消除了大規模管理基於 SQL Server 的應用程式的不同集合的複雜性。

在 Azure 與內部部署 SQL Server 資料庫的任何討論中，您要了解的第一件事情是您可以兩者都用。 Microsoft 的資料平臺利用 SQL Server 技術，使其可在物理本地電腦、私有雲環境、協力廠商託管私有雲環境和公共雲之間使用。 Azure 虛擬機器（SQL 虛擬機器）上的 SQL Server 使您能夠通過本地部署和雲託管部署的組合來滿足獨特和多樣化的業務需求，同時使用相同的伺服器產品、開發工具和專業知識集跨這些環境。

   ![雲端 SQL Server 選項：IaaS 上的 SQL Server 或雲端中的 SaaS SQL 資料庫。](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如圖所示，每個產品都可以以您在基礎架構上的管理水準和成本效益程度來表示。

在 Azure 中，可以將 SQL Server 工作負荷作為託管服務[（PaaS](https://azure.microsoft.com/overview/what-is-paas/)） 或託管基礎結構[（IaaS](https://azure.microsoft.com/overview/what-is-iaas/)） 運行。 在 PaaS 中，您有多個部署選項，而且每個部署選項中都有多個服務層級。 在 PaaS 或 IaaS 之間進行決策時，您需要問的關鍵問題是，是否要管理資料庫、應用修補程式和進行備份，還是希望將這些操作委派給 Azure？

根據答案，您會有下列選項：

- [**SQL 資料庫**](sql-database-technical-overview.md)：最適合想要使用最新穩定 SQL Server 功能並在開發和行銷方面有時間限制的現代雲應用程式。 一個完全受控的 SQL 資料庫引擎，以最新穩定版的 SQL Server Enterprise 為基礎。 這是裝載在 Azure 雲端的關聯式資料庫即服務 (DBaaS)，其產業類別屬於「平台即服務 (PaaS)」**。 SQL Database 有多個部署選項，而且每個部署選項都會建立在 Microsoft 所擁有、託管及維護的標準化硬體和軟體上。 使用 SQL Server，可以使用需要大量配置的內置特性和功能（本地或 Azure 虛擬機器）。 使用 SQL Database 時，您可以隨用隨付，並使用相應增加或相應放大選項以取得不需中斷的更強大功能。 SQL 資料庫具有 SQL Server 中不可用的其他功能，例如內置的高可用性、智慧和管理。


  資料庫提供以下部署選項：
  - 作為單個[***資料庫***](sql-database-single-database.md)，其自己的資源集通過資料庫伺服器進行管理。 單個資料庫類似于 SQL Server 中包含的[資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 此選項適用於新的雲端應用程式的現代應用程式開發。 提供[超大規模](sql-database-service-tier-hyperscale.md)和無[伺服器](sql-database-serverless.md)選項。
  - [***彈性池***](sql-database-elastic-pool.md)，它是一個資料庫的集合，具有通過資料庫伺服器管理的共用資源集。 單一資料庫可以移入和移出彈性集區。 此選項針對使用多租戶 SaaS 應用程式模式的新雲誕生應用程式進行現代應用程式開發進行了優化。 彈性池提供了一個經濟高效的解決方案，用於管理具有可變使用模式的多個資料庫的性能。
  - [***資料庫伺服器***](sql-database-servers.md)，用於管理單個資料庫和彈性池的組。 資料庫伺服器充當多個單個或池資料庫、[登錄、](sql-database-manage-logins.md)[防火牆規則](sql-database-firewall-configure.md)、[審核規則](sql-database-auditing.md)、[威脅檢測策略](sql-database-threat-detection.md)和[容錯移轉組](sql-database-auto-failover-group.md)的中心管理點。

- [**SQL 託管實例**](sql-database-managed-instance.md)：最適合大多數遷移到雲。 託管實例是系統和使用者資料庫的集合，具有一組共用的資源，這些資源已準備就緒。 最適合希望使用最新穩定 SQL Server 功能並在最少的更改下遷移到雲的新應用程式或現有本地應用程式。 託管實例類似于 Microsoft SQL Server[資料庫引擎](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview)的實例，該引擎為資料庫和其他實例範圍功能提供共用資源。 受控執行個體支援從內部部署進行資料庫移轉，且幾乎或完全不會對資料庫進行任何變更。 此選項還會提供 Azure SQL Database 的所有 PaaS 優勢，但會新增先前只在 SQL VM 中提供的功能。 這包括原生虛擬網路 (VNet) 以及與內部部署 SQL Server 接近 100% 的相容性。 託管實例提供完整的 SQL Server 訪問和功能相容性，用於將 SQL 伺服器遷移到 Azure。


- [**SQL 虛擬機器**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)：最適合需要作業系統級訪問的遷移和應用程式。 SQL 虛擬機器已為需要快速遷移到雲且更改最少或無需更改的現有應用程式做好準備。 SQL 虛擬機器提供對 SQL Server 實例和基礎作業系統的完全管理控制，以便遷移到 Azure。 當您不想購買內部部署 SQL Server 非生產硬體時的快速開發和測試案例。 SQL 虛擬機器屬於行業類別 *"基礎架構即服務 "（IaaS），* 允許您在 Azure 雲中完全託管的虛擬機器 （VM） 內運行 SQL Server。 SQL 虛擬機器還運行在 Microsoft 擁有、託管和維護的標準化硬體上運行。 使用 SQL 虛擬機器時，您可以立即為 SQL Server 映射中已包含的 SQL Server 許可證付費，也可以輕鬆使用現有許可證。 您也可以視需要停止或繼續執行 VM。 在雲中安裝和託管的 SQL Server 在 Azure 上運行的 Windows 伺服器或 Linux 虛擬機器上運行，也稱為基礎結構即服務 （IaaS）。 SQL 虛擬機器是遷移本地 SQL Server 資料庫和應用程式而不進行任何資料庫更改的好選項。 所有近期的 SQL Server 版本均可安裝在 IaaS 虛擬機器中。 與 SQL 資料庫和 SQL 託管實例最顯著的區別是 SQL Server VM 允許完全控制資料庫引擎。 您可以選擇何時開始維護/修補，將恢復模型更改為簡單或批量記錄，在需要時暫停或啟動服務，並可以完全自訂 SQL Server 資料庫引擎。 有了這種額外的控制，管理虛擬機器的責任就增加了。

  已經過最佳化，適合將現有的應用程式移轉到 Azure 或者在混合式部署中將現有的內部部署應用程式擴充到雲端。 此外，您可以使用虛擬機器中的 SQL Server 開發和測試傳統的 SQL Server 應用程式。 使用 SQL 虛擬機器，您可以對專用 SQL Server 實例和基於雲的 VM 擁有完全的管理許可權。 當組織擁有可用來維護虛擬機器的 IT 資源時，此選項會是最佳選擇。 這些功能可讓您建立高度自訂的系統，以解決應用程式的特定效能和可用性需求。


下表列出了其他差異，但***資料庫和託管實例都進行了優化，可將預配和管理許多資料庫的總體管理成本降至最低。*** 由於您無需管理任何虛擬機器、作業系統或資料庫軟體，它會將進行中的系統管理成本降到最低。 您不需要管理升級、高可用性或 [備份](sql-database-automated-backups.md)。 一般而言，Azure SQL Database 可能會大幅增加由單一 IT 或開發資源管理的資料庫數目。 [彈性集區](sql-database-elastic-pool.md)也支援 SaaS 多租用戶應用程式架構，其中包括租用戶隔離和調整等功能，可藉由跨資料庫共用資源來降低成本。 [受控執行個體](sql-database-managed-instance.md)可提供執行個體範圍功能的支援，以便輕鬆地移轉現有的應用程式，以及在資料庫之間共用資源。

| SQL DATABASE | SQL 受控執行個體 | SQL 虛擬機器 |
| :--- | :--- | :--- |
|支援大多數的內部部署資料庫層級功能。 提供最常使用的 SQL Server 功能。<br/>保證 99.995% 的可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>將所需的資源 (CPU/儲存體) 指派給個別資料庫的能力。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 支援幾乎所有的內部部署執行個體層級和資料庫層級功能。 與內部部署 SQL Server 高度相容。<br/>保證 99.99% 可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>從 SQL Server 輕鬆移轉。<br/>Azure VNet 中的私人 IP 位址。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 您對 SQL Server 引擎具有完全的控制權。 支援所有內部部署功能。<br/>高達 99.99% 的可用性。<br/>與內部部署 SQL Server 的相符版本完全一致。<br/>已修正，這是已知的資料庫引擎版本。<br/>從內部部署 SQL Server 輕鬆移轉。<br/>Azure VNet 中的私人 IP 位址。<br/>您可以在 SQL Server 所在的主機上部署應用程式或服務。|
|從 SQL Server 移轉可能很難。<br/>無法使用部分 SQL Server 功能。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。<br/>無法指派私人 IP 位址 (您可以使用防火牆規則限制存取)。|仍有極少數的 SQL Server 功能無法使用。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。|您需要管理備份和修補程式。<br>您需要實作自己的高可用性解決方案。<br/>變更資源 (CPU/儲存體) 時會出現停機時間|
| 最多 100 TB 的資料庫。 | 最多 8 TB。 | 具有高達 256 TB 存儲空間的 SQL Server 實例。 執行個體可以支援所需數量的資料庫。 |
| 本地應用程式可以訪問 Azure SQL 資料庫中的資料。 | 使用 Azure 快速路由或 VPN 閘道[實現本機虛擬網路](sql-database-managed-instance-vnet-configuration.md)並連接到本地環境。 | 使用 SQL 虛擬機器，可以讓應用程式部分在雲中運行，部分在本地運行。 例如，您可以透過 [ [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)] 將內部部署網路和 Active Directory 網域延伸到雲端。 如需有關混合式雲端解決方案的詳細資訊，請參閱[將內部部署資料解決方案擴充至雲端](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)。 |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>選擇資料庫、託管實例或 SQL 虛擬機器的業務動機

有幾個因素會影響您在不同資料產品之間進行選擇的決策：

- [成本](#cost) - PaaS 和 IaaS 選項都包括基底價格，其中涵蓋基礎結構和授權。 但是，使用 IaaS 選項，您需要投入更多時間和資源來管理資料庫，而在 PaaS 中，您會收到價格中包含的這些管理功能。 IaaS 選項使您能夠在不使用資源來降低成本時關閉資源，而 PaaS 版本始終處於運行，除非您刪除資源並在需要時重新創建資源。
- [管理](#administration) - PaaS 選項會降低您管理資料庫所需投入的時間量。 但是，它還限制可以執行或運行的自訂管理工作和腳本的範圍。 例如，單個資料庫或池資料庫不支援 CLR，但託管實例支援 CLR。 此外，PaaS 中沒有任何部署選項支援使用跟蹤標誌。
- [服務等級協定](#service-level-agreement-sla) - IaaS 和 PaaS 都提供最高業界標準的 SLA。 針對基礎架構，PaaS 選項可保證 99.99 % 的 SLA，而 IaaS 保證 99.95% 的 SLA，這表示您需要實作其他的機制來確保資料庫的可用性。 通過在 VM 中創建額外的 SQL Server 並配置 AlwaysOn 可用性組，可以在 99.99% 實現高可用性解決方案。
- [移至 Azure 的時間](#market) - Azure VM 中的 SQL Server 與您的環境完全相符，因此從內部部署移轉至 Azure SQL VM 與將資料庫從一個內部部署伺服器移動到另一個伺服器沒有什麼不同。 受控執行個體還可以實現極其輕鬆的移轉；但是，在移轉至受控執行個體之前，可能需要套用一些變更。

下列各節將更詳細地討論這些因素。

### <a name="cost"></a>成本

無論您是現金不足的新公司，或是在預算有限的情況下運作的已成立公司內部小組，有限資金經常會是決定主控資料庫方式的主要關鍵。 在本節中，您將瞭解 Azure 中有關這兩個關係資料庫選項的計費和許可基礎知識：SQL 資料庫和 SQL 虛擬機器。 您也會了解應用程式總成本的計算。

#### <a name="billing-and-licensing-basics"></a>計費和授權基本概念

目前，**SQL Database** 以服務形式銷售並提供數個部署選項，而且在資源價格不同的數個服務層級中，所有資源都根據您所選擇的服務層級和計算大小，以固定費率計算每小時的費用。 如需有關目前支援服務層級、計算大小和儲存體帳戶的最新資訊，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。

- 使用 SQL 資料庫，您可以從基本層從 5 美元/月開始的各種價格中選擇適合您需求的服務層。
- 您可以建立[彈性集區](sql-database-elastic-pool.md)以便在資料庫執行個體間共用資源，進而降低成本及因應使用尖峰。
- 使用 SQL 託管實例，您還可以自帶許可證。 如需採用自己的授權的詳細資訊，請參閱 [Azure 上藉軟體保證而實現的授權行動性](https://azure.microsoft.com/pricing/license-mobility/)或使用 [Azure Hybrid Benefit 計算機](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)查看如何**節省高達 40%**。

此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。 您可以動態調整服務層級和計算大小，以滿足應用程式的不同輸送量需求。

使用**SQL 資料庫和 SQL 託管實例**，由 Microsoft 自動設定、修補和升級資料庫軟體，從而降低管理成本。 此外，它 [內建的備份](sql-database-automated-backups.md) 功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。

使用**SQL 虛擬機器**，您可以使用平臺提供的任何 SQL Server 映射（包括許可證）或攜帶 SQL Server 許可證。 所有支援的 SQL Server 版本 (2008R2、2012、2014、2016 以及 Developer、Express、Web、Standard、Enterprise) 都可供使用。 此外，自備授權版本 (BYOL) 的映像也可供使用。 使用 Azure 所提供的映像時，營運成本取決於您所選擇的 VM 大小以及 SQL Server 版本。 不論 VM 大小或 SQL Server 版本為何，您須支付 SQL Server 和 Windows 或 Linux Server 的每分鐘授權成本，以及 VM 磁碟的 Azure 儲存體成本。 每分鐘計費選項可讓您隨時使用 SQL Server，而無需購買其他 SQL Server 授權。 如果在 Azure 中採用自己的 SQL Server 授權，您僅需支付伺服器和儲存體成本。 如需採用自己的授權的詳細資訊，請參閱 [Azure 上透過軟體保證的授權機動性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

#### <a name="calculating-the-total-application-cost"></a>計算應用程式總成本

當您開始使用雲端平台時，執行應用程式的成本包括新開發和持續管理成本，以及公用雲端平台的服務成本。

如需價格的詳細資訊，請參閱下列資源：

- [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)的[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>系統管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度。 使用 IaaS 和 PaaS，Microsoft 可以管理基礎硬體、自動複製所有資料以提供災害復原、設定及升級資料庫軟體、管理負載平衡，以及在資料中心發生伺服器故障時進行透明容錯移轉。

- 使用**SQL 資料庫和 SQL 託管實例**，可以繼續管理資料庫，但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例包括資料庫和登入、索引和查詢微調，以及稽核和安全性。 此外，設定其他資料中心的高可用性時，需要進行最小的設定和系統管理。
- 使用**SQL 虛擬機器**，您可以完全控制作業系統和 SQL Server 實例配置。 有了 VM，您可以決定何時更新/升級作業系統與資料庫軟體，以及何時安裝任何其他軟體 (例如防毒)。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 此外，您還可以控制 VM 的大小、磁碟的數目及其儲存體組態。 Azure 可讓您視需要變更 VM 大小。 如需相關資料，請參閱 [Azure 的虛擬機器和雲端服務大小](../virtual-machines/windows/sizes.md)。

### <a name="service-level-agreement-sla"></a>服務等級協定 (SLA)

對於許多 IT 部門而言，達到服務等級協定 (SLA) 的正常運作時間義務是首要任務。 在本節中，我們將瞭解 SLA 對每個資料庫主控選項的作用。

對於 **SQL Database**，Microsoft 提供 99.99% 的可用性 SLA。 如需最新資訊，請參閱 [服務等級協定](https://azure.microsoft.com/support/legal/sla/sql-database/)。

對於**SQL 虛擬機器**，Microsoft 提供的 SLA 可用性為 99.95%，僅涵蓋虛擬機器。 本 SLA 未涵蓋在 VM 上執行的程序 (例如 SQL Server)，而且您必須裝載一個可用性設定組中的至少兩個 VM 執行個體。 如需最新資訊，請參閱 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 如需 VM 內的資料庫高可用性 (HA)，您應在 SQL Server 中設定其中一個支援的高可用性選項，例如 [AlwaysOn 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 使用支援的高可用性選項並不提供額外的 SLA，但可讓您達成 >99.99% 的資料庫可用性。

### <a name="time-to-move-to-azure"></a><a name="market"></a>移至 Azure 的時間

當開發人員的工作效率和新解決方案的快速上市時間至關重要時 **，SQL 資料庫**（單個資料庫或彈性池）是雲設計應用程式的正確解決方案。 有了程式設計 DBA 類似功能，此選項非常適合雲端架構設計人員和開發人員，因為它會降低管理基礎作業系統和資料庫的需求。

**SQL 託管實例**大大簡化了現有應用程式遷移到 Azure SQL，使您能夠快速將遷移的資料庫應用程式推向市場。

如果現有或新應用程式需要大型資料庫或訪問 SQL Server 或 Windows/Linux 中的所有功能，並且您希望避免獲取新的本地硬體的時間和費用，**則 SQL 虛擬機器**是完美的。 這也很適合想要依現狀將現有的內部部署應用程式和資料庫移轉至 Azure - 在不適合使用 Azure SQL Database 受控執行個體的情況下。 由於您無需變更簡報、應用程式和資料層，您會在重新架構現有解決方案時節省時間和預算。 相反地，您可以將重點放在將所有解決方案移轉至 Azure，並進行 Azure 平台可能需要的某些效能最佳化作業。 如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳作法](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>後續步驟

- [請參閱第一個 Azure SQL 資料庫](sql-database-single-database-get-started.md)以開始使用 SQL 資料庫。
- 請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)。
- 請參閱 [在 Azure 中佈建 SQL Server 虛擬機器](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) 以開始使用 Azure VM 上的 SQL Server。
- [為本機資料庫確定正確的 SQL 資料庫或 SQL 託管實例 SKU。](/sql/dma/dma-sku-recommend-sql-db/)
