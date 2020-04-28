---
title: 選擇正確的部署選項
description: 瞭解如何在 azure 虛擬機器上的 SQL 資料庫、SQL 受控實例和 SQL Server 之間，選擇 Azure SQL 中的部署選項。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821340"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>在 Azure SQL 中選擇正確的部署選項

瞭解每個部署選項如何融入 Microsoft 的 Azure SQL 資料平臺，並針對您的商務需求取得符合適當選項的協助。 無論您的成本節約或最低管理優先順序，本文都可以協助您決定哪一種方法符合您最關心的商務需求。

## <a name="microsofts-azure-sql-data-platform"></a>Microsoft 的 Azure SQL 資料平臺

從隨即轉移到現有應用程式的現代化，到打造現代化的雲端服務，Azure SQL 是現代化的 SQL 平臺，提供數個部署選項，由領先業界的 Microsoft SQL Server 引擎提供技術支援。 Azure SQL 設計用來支援各種不同層級的應用程式模式，以控制基礎平臺，以符合最嚴苛的遷移和現代化需求。 Azure SQL 藉由提供單一、統一的管理體驗，消除了大規模管理各種 SQL Server 型應用程式的複雜性。

在 Azure 與內部部署 SQL Server 資料庫的任何討論中，您要了解的第一件事情是您可以兩者都用。 Microsoft 的資料平臺利用 SQL Server 技術，讓它可在實體內部部署機器、私人雲端環境、協力廠商裝載的私用雲端環境和公用雲端中使用。 Azure 虛擬機器（SQL 虛擬機器）上的 SQL Server 可讓您透過結合內部部署和雲端裝載的部署，滿足獨特且多樣化的商務需求，同時在這些環境中使用相同的伺服器產品、開發工具和專業知識。

   ![雲端 SQL Server 選項：IaaS 上的 SQL Server 或雲端中的 SaaS SQL 資料庫。](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如圖中所示，每個供應專案都可以根據您在基礎結構中的管理層級，以及成本效率的程度來表徵。

在 Azure 中，您可以讓 SQL Server 工作負載以託管服務（[PaaS](https://azure.microsoft.com/overview/what-is-paas/)）或託管基礎結構（[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)）的形式執行。 在 PaaS 中，您有多個部署選項，而且每個部署選項中都有多個服務層級。 在 PaaS 或 IaaS 之間做決定時，您需要詢問的重要問題是您要管理資料庫、套用修補程式，以及取得備份，還是要將這些作業委派給 Azure？

根據答案，您會有下列選項：

- [**SQL 資料庫**](sql-database-technical-overview.md)：最適合想要使用最新穩定 SQL Server 功能，且在開發和行銷方面有時間限制的現代化雲端應用程式。 一個完全受控的 SQL 資料庫引擎，以最新穩定版的 SQL Server Enterprise 為基礎。 這是裝載在 Azure 雲端的關聯式資料庫即服務 (DBaaS)，其產業類別屬於「平台即服務 (PaaS)」**。 SQL Database 有多個部署選項，而且每個部署選項都會建立在 Microsoft 所擁有、託管及維護的標準化硬體和軟體上。 使用 SQL Server，您可以使用需要大量設定的內建功能（內部部署或 Azure 虛擬機器中的功能）。 使用 SQL Database 時，您可以隨用隨付，並使用相應增加或相應放大選項以取得不需中斷的更強大功能。 SQL Database 有一些 SQL Server 無法使用的額外功能，例如內建的高可用性、智慧和管理。


  資料庫提供下列部署選項：
  - 做為[***單一資料庫***](sql-database-single-database.md)，其中包含透過資料庫伺服器管理的自有資源集。 單一資料庫與 SQL Server 中的自主[資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)類似。 此選項適用於新的雲端應用程式的現代應用程式開發。 [超大規模資料庫](sql-database-service-tier-hyperscale.md)和[無伺服器](sql-database-serverless.md)選項可供使用。
  - [***彈性***](sql-database-elastic-pool.md)集區，這是一種資料庫集合，其中包含透過資料庫伺服器管理的共用資源集。 單一資料庫可以移入和移出彈性集區。 此選項已針對使用多租使用者 SaaS 應用程式模式的新雲端應用程式的新式應用程式開發優化。 彈性集區提供符合成本效益的解決方案，可用於管理具有變數使用模式之多個資料庫的效能。
  - [***資料庫伺服器***](sql-database-servers.md)，用來管理單一資料庫和彈性集區的群組。 資料庫伺服器扮演多個單一或集區[資料庫、登](sql-database-manage-logins.md)入、[防火牆規則](sql-database-firewall-configure.md)、[審核規則](sql-database-auditing.md)、[威脅偵測原則](sql-database-threat-detection.md)和[容錯移轉群組](sql-database-auto-failover-group.md)的中央管理點。

- [**SQL 受控實例**](sql-database-managed-instance.md)：最適合用於雲端的大部分遷移。 受控實例是系統和使用者資料庫的集合，其中包含一組已準備就緒的共用資源。 適用于新的應用程式或現有的內部部署應用程式，想要使用最新穩定的 SQL Server 功能，並以最少的變更遷移至雲端。 受控實例類似于[Microsoft SQL Server 資料庫引擎](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview)的實例，提供資料庫的共用資源以及其他實例範圍的功能。 受控執行個體支援從內部部署進行資料庫移轉，且幾乎或完全不會對資料庫進行任何變更。 此選項還會提供 Azure SQL Database 的所有 PaaS 優勢，但會新增先前只在 SQL VM 中提供的功能。 這包括原生虛擬網路 (VNet) 以及與內部部署 SQL Server 接近 100% 的相容性。 受控實例可提供完整的 SQL Server 存取和功能相容性，以便將 SQL Server 遷移至 Azure。


- [**SQL 虛擬機器**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)：適用于需要 OS 層級存取的遷移和應用程式。 對於需要快速遷移至雲端的現有應用程式，SQL 虛擬機器已準備就緒，而且變更不會變更。 SQL 虛擬機器提供 SQL Server 實例和基礎作業系統的完整系統管理控制權，可供遷移至 Azure。 當您不想購買內部部署 SQL Server 非生產硬體時的快速開發和測試案例。 SQL 虛擬機器屬於業界類別的*基礎結構即服務（IaaS）* ，可讓您在 Azure 雲端中完全受控的虛擬機器（VM）內執行 SQL Server。 SQL 虛擬機器也會在 Microsoft 所擁有、託管及維護的標準化硬體上執行。 使用 SQL 虛擬機器時，您可以隨用隨付，針對已包含在 SQL Server 映射中的 SQL Server 授權，或輕鬆使用現有的授權。 您也可以視需要停止或繼續執行 VM。 SQL Server 安裝並裝載在雲端中執行的 Windows Server 或 Linux 虛擬機器上，也稱為基礎結構即服務（IaaS）。 SQL 虛擬機器是遷移內部部署 SQL Server 資料庫和應用程式，而不需要變更任何資料庫的絕佳選項。 所有近期的 SQL Server 版本均可安裝在 IaaS 虛擬機器中。 與 SQL database 和 SQL 受控實例最大的差異在於，SQL Server 的 Vm 可以完全控制資料庫引擎。 您可以選擇啟動維護/修補的時機，將復原模式變更為簡單或大量記錄，並在需要時暫停或啟動服務，而且您可以完全自訂 SQL Server 的資料庫引擎。 隨著此額外的控制，管理虛擬機器還有額外的責任。

  已經過最佳化，適合將現有的應用程式移轉到 Azure 或者在混合式部署中將現有的內部部署應用程式擴充到雲端。 此外，您可以使用虛擬機器中的 SQL Server 開發和測試傳統的 SQL Server 應用程式。 有了 SQL 虛擬機器，您可以透過專用的 SQL Server 實例和雲端式 VM，擁有完整的系統管理許可權。 當組織擁有可用來維護虛擬機器的 IT 資源時，此選項會是最佳選擇。 這些功能可讓您建立高度自訂的系統，以解決應用程式的特定效能和可用性需求。


下表列出額外的差異，但***資料庫和受控實例都已優化，可將布建和管理許多資料庫的整體管理成本降到最低。*** 由於您無需管理任何虛擬機器、作業系統或資料庫軟體，它會將進行中的系統管理成本降到最低。 您不需要管理升級、高可用性或 [備份](sql-database-automated-backups.md)。 一般而言，Azure SQL Database 可能會大幅增加由單一 IT 或開發資源管理的資料庫數目。 [彈性集區](sql-database-elastic-pool.md)也支援 SaaS 多租用戶應用程式架構，其中包括租用戶隔離和調整等功能，可藉由跨資料庫共用資源來降低成本。 [受控執行個體](sql-database-managed-instance.md)可提供執行個體範圍功能的支援，以便輕鬆地移轉現有的應用程式，以及在資料庫之間共用資源。

| SQL DATABASE | SQL 受控執行個體 | SQL 虛擬機器 |
| :--- | :--- | :--- |
|支援大多數的內部部署資料庫層級功能。 提供最常使用的 SQL Server 功能。<br/>保證 99.995% 的可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>將所需的資源 (CPU/儲存體) 指派給個別資料庫的能力。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 支援幾乎所有的內部部署執行個體層級和資料庫層級功能。 與內部部署 SQL Server 高度相容。<br/>保證 99.99% 可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>從 SQL Server 輕鬆移轉。<br/>Azure VNet 中的私人 IP 位址。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 您對 SQL Server 引擎具有完全的控制權。 支援所有內部部署功能。<br/>最高可達99.99% 的可用性。<br/>與內部部署 SQL Server 的相符版本完全一致。<br/>已修正，這是已知的資料庫引擎版本。<br/>從內部部署 SQL Server 輕鬆移轉。<br/>Azure VNet 中的私人 IP 位址。<br/>您可以在 SQL Server 所在的主機上部署應用程式或服務。|
|從 SQL Server 移轉可能很難。<br/>無法使用部分 SQL Server 功能。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。<br/>無法指派私人 IP 位址 (您可以使用防火牆規則限制存取)。|仍有極少數的 SQL Server 功能無法使用。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。|您需要管理備份和修補程式。<br>您需要實作自己的高可用性解決方案。<br/>變更資源 (CPU/儲存體) 時會出現停機時間|
| 最多 100 TB 的資料庫。 | 最多 8 TB。 | SQL Server 的實例，最多可達 256 TB 的儲存體。 執行個體可以支援所需數量的資料庫。 |
| 內部部署應用程式可以存取 Azure SQL Database 中的資料。 | 使用 Azure Express Route 或 VPN 閘道[執行原生虛擬網路](sql-database-managed-instance-vnet-configuration.md)和內部部署環境的連線。 | 有了 SQL 虛擬機器，您就可以讓應用程式在雲端中執行，而且部分內部部署。 例如，您可以透過 [ [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)] 將內部部署網路和 Active Directory 網域延伸到雲端。 如需有關混合式雲端解決方案的詳細資訊，請參閱[將內部部署資料解決方案擴充至雲端](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)。 |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>選擇資料庫、受控實例或 SQL 虛擬機器的商業動機

有數個因素會影響您在不同資料供應專案之間選擇的決定：

- [成本](#cost) - PaaS 和 IaaS 選項都包括基底價格，其中涵蓋基礎結構和授權。 但是，使用 IaaS 選項，您需要投入更多時間和資源來管理資料庫，而在 PaaS 中，您會收到價格中包含的這些管理功能。 IaaS 選項可讓您在不使用資源時將其關機以降低成本，而 PaaS 版本一律會執行，除非您在需要時卸載並重新建立您的資源。
- [管理](#administration) - PaaS 選項會降低您管理資料庫所需投入的時間量。 不過，它也會限制您可以執行或執行的自訂管理工作和腳本的範圍。 例如，單一或集區資料庫不支援 CLR，但受控實例支援。 此外，PaaS 中沒有任何部署選項支援使用追蹤旗標。
- [服務等級協定](#service-level-agreement-sla) - IaaS 和 PaaS 都提供最高業界標準的 SLA。 針對基礎架構，PaaS 選項可保證 99.99 % 的 SLA，而 IaaS 保證 99.95% 的 SLA，這表示您需要實作其他的機制來確保資料庫的可用性。 您可以藉由在 VM 中建立額外的 SQL Server 並設定 AlwaysOn 可用性群組，在99.99% 執行高可用性解決方案。
- [移至 Azure 的時間](#market) - Azure VM 中的 SQL Server 與您的環境完全相符，因此從內部部署移轉至 Azure SQL VM 與將資料庫從一個內部部署伺服器移動到另一個伺服器沒有什麼不同。 受控執行個體還可以實現極其輕鬆的移轉；但是，在移轉至受控執行個體之前，可能需要套用一些變更。

下列各節將更詳細地討論這些因素。

### <a name="cost"></a>成本

無論您是現金不足的新公司，或是在預算有限的情況下運作的已成立公司內部小組，有限資金經常會是決定主控資料庫方式的主要關鍵。 在本節中，您會瞭解 Azure 中與這兩個關係資料庫選項相關的計費和授權基本概念： SQL Database 和 SQL 虛擬機器。 您也會了解應用程式總成本的計算。

#### <a name="billing-and-licensing-basics"></a>計費和授權基本概念

目前，**SQL Database** 以服務形式銷售並提供數個部署選項，而且在資源價格不同的數個服務層級中，所有資源都根據您所選擇的服務層級和計算大小，以固定費率計算每小時的費用。 如需有關目前支援服務層級、計算大小和儲存體帳戶的最新資訊，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。

- 有了 SQL database，您可以從基本層的 5 $/月開始，選擇符合您需求的服務層級。
- 您可以建立[彈性集區](sql-database-elastic-pool.md)以便在資料庫執行個體間共用資源，進而降低成本及因應使用尖峰。
- 使用 SQL 受控實例時，您也可以自備授權。 如需採用自己的授權的詳細資訊，請參閱 [Azure 上藉軟體保證而實現的授權行動性](https://azure.microsoft.com/pricing/license-mobility/)或使用 [Azure Hybrid Benefit 計算機](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)查看如何**節省高達 40%**。

此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。 您可以動態調整服務層級和計算大小，以滿足應用程式的不同輸送量需求。

透過**sql 資料庫和 sql 受控實例**，Microsoft 會自動設定、修補及升級資料庫軟體，以降低您的系統管理成本。 此外，它 [內建的備份](sql-database-automated-backups.md) 功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。

有了**SQL 虛擬機器**，您可以使用任何平臺提供的 SQL Server 映射（其中包含授權）或攜帶您的 SQL Server 授權。 所有支援的 SQL Server 版本 (2008R2、2012、2014、2016 以及 Developer、Express、Web、Standard、Enterprise) 都可供使用。 此外，自備授權版本 (BYOL) 的映像也可供使用。 使用 Azure 所提供的映像時，營運成本取決於您所選擇的 VM 大小以及 SQL Server 版本。 不論 VM 大小或 SQL Server 版本為何，您須支付 SQL Server 和 Windows 或 Linux Server 的每分鐘授權成本，以及 VM 磁碟的 Azure 儲存體成本。 每分鐘計費選項可讓您隨時使用 SQL Server，而無需購買其他 SQL Server 授權。 如果在 Azure 中採用自己的 SQL Server 授權，您僅需支付伺服器和儲存體成本。 如需採用自己的授權的詳細資訊，請參閱 [Azure 上透過軟體保證的授權機動性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

#### <a name="calculating-the-total-application-cost"></a>計算應用程式總成本

當您開始使用雲端平台時，執行應用程式的成本包括新開發和持續管理成本，以及公用雲端平台的服務成本。

如需價格的詳細資訊，請參閱下列資源：

- [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)
- 適用于[SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)的[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>系統管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度。 使用 IaaS 和 PaaS，Microsoft 可以管理基礎硬體、自動複製所有資料以提供災害復原、設定及升級資料庫軟體、管理負載平衡，以及在資料中心發生伺服器故障時進行透明容錯移轉。

- 使用**sql database 和 sql 受控實例**，您可以繼續管理您的資料庫，但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例包括資料庫和登入、索引和查詢微調，以及稽核和安全性。 此外，設定其他資料中心的高可用性時，需要進行最小的設定和系統管理。
- 有了**SQL 虛擬機器**，您就可以完全掌控作業系統和 SQL Server 實例設定。 有了 VM，您可以決定何時更新/升級作業系統與資料庫軟體，以及何時安裝任何其他軟體 (例如防毒)。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 此外，您還可以控制 VM 的大小、磁碟的數目及其儲存體組態。 Azure 可讓您視需要變更 VM 大小。 如需相關資料，請參閱 [Azure 的虛擬機器和雲端服務大小](../virtual-machines/windows/sizes.md)。

### <a name="service-level-agreement-sla"></a>服務等級協定 (SLA)

對於許多 IT 部門而言，達到服務等級協定 (SLA) 的正常運作時間義務是首要任務。 在本節中，我們將瞭解 SLA 對每個資料庫主控選項的作用。

對於 **SQL Database**，Microsoft 提供 99.99% 的可用性 SLA。 如需最新資訊，請參閱 [服務等級協定](https://azure.microsoft.com/support/legal/sla/sql-database/)。

針對**SQL 虛擬機器**，Microsoft 提供99.95% 的可用性 SLA，僅涵蓋虛擬機器。 本 SLA 未涵蓋在 VM 上執行的程序 (例如 SQL Server)，而且您必須裝載一個可用性設定組中的至少兩個 VM 執行個體。 如需最新資訊，請參閱 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 如需 VM 內的資料庫高可用性 (HA)，您應在 SQL Server 中設定其中一個支援的高可用性選項，例如 [AlwaysOn 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 使用支援的高可用性選項並不提供額外的 SLA，但可讓您達成 >99.99% 的資料庫可用性。

### <a name="time-to-move-to-azure"></a><a name="market"></a>移至 Azure 的時間

當新解決方案的開發人員生產力和快速上市時間很重要時， **SQL database** （單一資料庫或彈性集區）是適合雲端設計應用程式的解決方案。 有了程式設計 DBA 類似功能，此選項非常適合雲端架構設計人員和開發人員，因為它會降低管理基礎作業系統和資料庫的需求。

**SQL 受控實例**可大幅簡化將現有應用程式遷移至 azure SQL 的工作，讓您能夠快速將遷移的資料庫應用程式帶入 azure 市場。

如果您現有或新的應用程式需要大型資料庫或存取 SQL Server 或 Windows/Linux 中的所有功能，而且您想要避免取得新內部部署硬體的時間和費用， **SQL 虛擬機器**就很理想。 這也很適合想要依現狀將現有的內部部署應用程式和資料庫移轉至 Azure - 在不適合使用 Azure SQL Database 受控執行個體的情況下。 由於您無需變更簡報、應用程式和資料層，您會在重新架構現有解決方案時節省時間和預算。 相反地，您可以將重點放在將所有解決方案移轉至 Azure，並進行 Azure 平台可能需要的某些效能最佳化作業。 如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳作法](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>後續步驟

- 請參閱[您的第一個 AZURE SQL database](sql-database-single-database-get-started.md) ，以開始使用 SQL Database。
- 請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)。
- 請參閱 [在 Azure 中佈建 SQL Server 虛擬機器](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) 以開始使用 Azure VM 上的 SQL Server。
- [針對您的內部部署資料庫，識別正確的 sql database 或 sql 受控實例 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
