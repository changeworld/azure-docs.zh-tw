---
title: 什麼是 Azure SQL？
titleSuffix: " "
description: 瞭解 Azure SQL 服務系列內的不同選項： Azure SQL Database、Azure SQL 受控執行個體和 Azure VM 上的 SQL Server。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server 雲端，SQL Server 在雲端，PaaS 資料庫，雲端 SQL Server，DBaaS，IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: ff8dd9d10bf5970a5a96080beb51e2c111d481e3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87280846"
---
# <a name="what-is-azure-sql"></a>什麼是 Azure SQL？ 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL 是一系列受控、安全且智慧型的產品，其使用 Azure 雲端中的 SQL Server 資料庫引擎。

- **Azure SQL Database**：在包含無伺服器計算的智慧型受控資料庫服務上，支援新式雲端應用程式。 
- **AZURE SQL 受控執行個體**：使用智慧型的完全受控實例即服務，大規模地現代化現有的 SQL Server 應用程式，與 SQL Server 的資料庫引擎幾乎100% 的功能同位。 最適合用於雲端的大部分遷移。
- **在 Azure vm 上 SQL Server**：輕鬆隨即轉移您的 SQL Server 工作負載，並維護 100% SQL Server 相容性和作業系統層級的存取。 
 
Azure SQL 建基於熟悉的 SQL Server 引擎，因此您可以輕鬆地遷移應用程式，並繼續使用您熟悉的工具、語言和資源。 您的技能和經驗轉移到雲端，讓您可以更進一步運用現有的功能。 

瞭解每個產品如何融入 Microsoft 的 Azure SQL 資料平臺，以符合您業務需求的正確選項。 無論您的成本節約或最低管理優先順序，本文都可以協助您決定哪一種方法符合您最關心的商務需求。


## <a name="overview"></a>概觀

在現今的資料驅動世界中，推動數位轉型的方式越來越大，因為我們能夠管理大量資料，並充分發揮其潛力。 但是現今的資料資產越來越複雜，資料裝載于內部部署、雲端或網路邊緣。 建立智慧型和沉浸式應用程式的開發人員，可能會發現其本身受到限制，而這些限制最終會影響他們的經驗。 因平臺不相容而產生的限制、資料安全性不佳、資源不足和價格效能屏障，會造成可能阻礙應用程式現代化和開發的複雜性。 

在 Azure 與內部部署 SQL Server 資料庫的任何討論中，您要了解的第一件事情是您可以兩者都用。 Microsoft 的資料平臺利用 SQL Server 技術，讓它可在實體內部部署機器、私人雲端環境、協力廠商裝載的私用雲端環境和公用雲端中使用。 


### <a name="fully-managed-and-always-up-to-date"></a>完全受控且永遠保持在最新狀態 

花更多時間進行創新，並縮短修補、更新及備份資料庫的時間。 Azure 是唯一具有長時間 SQL 的雲端，會自動套用最新的更新和修補程式，讓您的資料庫永遠保持在最新狀態，以消除支援終止的麻煩。 甚至還會自動化複雜的工作，例如效能微調、高可用性、嚴重損壞修復和備份作業，讓您專注于應用程式。  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>使用內建的智慧型安全性來保護您的資料 

Azure 會持續監視您的資料是否有威脅。 有了 Azure SQL，您可以：

- 使用智慧型[先進的威脅偵測](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services)和主動式弱點評估警示，即時補救潛在威脅。 
- 透過內[建安全性控制](https://azure.microsoft.com/overview/security/)，包括 t-sql、驗證、網路和金鑰管理，取得領先業界的多層式保護。 
- 利用任何雲端資料庫服務的最全面[合規性](https://azure.microsoft.com/overview/trusted-cloud/compliance/)涵蓋範圍。 


### <a name="business-motivations"></a>業務動機

有數個因素會影響您在不同資料供應專案之間選擇的決定：

- [成本](#cost)： PaaS 和 IaaS 選項都包含基本價格，涵蓋基礎結構和授權。 不過，使用 IaaS 選項時，您必須投入額外的時間和資源來管理您的資料庫，而在 PaaS 中，您可以取得這些系統管理功能（價格包括在內）。 IaaS 可讓您在不使用資源時將其關機以降低成本，而 PaaS 一律會執行，除非您在需要時卸載並重新建立資源。
- [管理](#administration)： PaaS 選項可縮短管理資料庫所需投入的時間量。 不過，它也會限制您可以執行或執行的自訂管理工作和腳本的範圍。 例如，SQL Database 不支援 CLR，但是 SQL 受控執行個體的實例支援。 此外，PaaS 中沒有任何部署選項支援使用追蹤旗標。
- [服務等級協定](#service-level-agreement-sla)： IaaS 和 PaaS 皆提供高標準 SLA。 針對基礎架構，PaaS 選項可保證 99.99 % 的 SLA，而 IaaS 保證 99.95% 的 SLA，這表示您需要實作其他的機制來確保資料庫的可用性。 您可以藉由建立額外的 SQL 虛擬機器，並執行 SQL Server Always On 可用性群組高可用性解決方案，來達到99.99% 的 SLA。 
- [移至 azure 的時間](#market)： azure VM 上的 SQL Server 與您的環境完全相符，因此從內部部署至 Azure vm 的遷移與將資料庫從一部內部部署伺服器移至另一部伺服器並無不同。 SQL 受控執行個體也可以輕鬆地進行遷移;不過，在您的遷移之前，可能需要套用一些變更。 


## <a name="service-comparison"></a>服務比較

   ![雲端 SQL Server 選項：在 IaaS 上 SQL Server，或雲端中的 SaaS SQL Database。](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如圖所示，每個服務供應專案的特性都可以是您在基礎結構上的管理層級，以及成本效率的程度。

在 Azure 中，您可以讓 SQL Server 工作負載以託管服務（[PaaS](https://azure.microsoft.com/overview/what-is-paas/)）或託管基礎結構（[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)）的形式執行。 在 PaaS 中，您有多個產品選項，以及每個選項內的服務層級。 在 PaaS 或 IaaS 之間做決定時，您需要詢問的重要問題是您要管理資料庫、套用修補程式，以及取得備份，還是要將這些作業委派給 Azure？

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md)是 Azure 中託管的關係資料庫即服務（DBaaS），屬於業界類別的*平臺即服務（PaaS）*。 
- 最適合想要使用最新穩定 SQL Server 功能，且在開發和行銷方面有時間限制的現代化雲端應用程式。 
- 完全受控 SQL Server 資料庫引擎，以 SQL Server 的最新穩定企業版為基礎。 SQL Database 有兩個部署選項，建置於由 Microsoft 所擁有、託管及維護的標準化硬體和軟體上。 

有了 SQL Server，您就可以使用需要大量設定的內建功能（內部部署或 Azure 虛擬機器中的功能）。 使用 SQL Database 時，您可以隨用隨付，並使用相應增加或相應放大選項以取得不需中斷的更強大功能。 SQL Database 有一些 SQL Server 無法使用的額外功能，例如內建的高可用性、智慧和管理。


Azure SQL Database 提供下列部署選項：
  - 做為[*單一資料庫*](database/single-database-overview.md)，其中包含透過[邏輯 SQL server](database/logical-servers.md)管理的自有資源集。 單一資料庫與 SQL Server 中的自主[資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)類似。 此選項適用於新的雲端應用程式的現代應用程式開發。 [超大規模資料庫](database/service-tier-hyperscale.md)和[無伺服器](database/serverless-tier-overview.md)選項可供使用。
  - [*彈性*](database/elastic-pool-overview.md)集區，這是一種資料庫集合，其中包含透過[邏輯 SQL server](database/logical-servers.md)管理的共用資源集。 單一資料庫可以移入和移出彈性集區。 此選項已針對使用多租使用者 SaaS 應用程式模式的新雲端應用程式的新式應用程式開發優化。 彈性集區提供符合成本效益的解決方案，可用於管理具有變數使用模式之多個資料庫的效能。

### <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

[AZURE SQL 受控執行個體](managed-instance/sql-managed-instance-paas-overview.md)屬於業界類別的*平臺即服務（PaaS）*，最適合用於雲端的大部分遷移。 SQL 受控執行個體是系統和使用者資料庫的集合，其中包含一組已準備就緒的共用資源。  
- 適用于新的應用程式或現有的內部部署應用程式，想要使用最新穩定的 SQL Server 功能，並以最少的變更遷移至雲端。 SQL 受控執行個體的實例類似于[Microsoft SQL Server database engine](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview)的實例，提供資料庫的共用資源以及其他實例範圍的功能。 
- SQL 受控執行個體支援從內部部署的資料庫移轉，而且幾乎不需要變更資料庫。 此選項提供 Azure SQL Database 的所有 PaaS 優勢，但會新增先前只在 SQL Server Vm 中提供的功能。 這包括原生虛擬網路，以及與內部部署 SQL Server 接近100% 的相容性。 SQL 受控執行個體的實例可提供完整的 SQL Server 存取和功能相容性，以便將 SQL Server 遷移至 Azure。

### <a name="sql-server-on-azure-vm"></a>Azure VM 上的 SQL Server

[AZURE VM 上的 SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)屬於業界類別的*基礎結構即服務（IaaS）* ，可讓您在 azure 中完全受控的虛擬機器（VM）內執行 SQL Server。 
- 適用于需要 OS 層級存取的遷移和應用程式。 Azure 中的 SQL 虛擬機器已準備就緒，可供需要快速遷移至雲端的現有應用程式，只需進行最少的變更或變更。 SQL 虛擬機器提供 SQL Server 實例和基礎作業系統的完整系統管理控制權，可供遷移至 Azure。 
- 當您不想購買內部部署 SQL Server 非生產硬體時的快速開發和測試案例。 SQL 虛擬機器也會在 Microsoft 所擁有、託管及維護的標準化硬體上執行。 使用 SQL 虛擬機器時，您可以隨用隨付，針對已包含在 SQL Server 映射中的 SQL Server 授權，或輕鬆使用現有的授權。 您也可以視需要停止或繼續執行 VM。 
- SQL Server 安裝並裝載在雲端中執行的 Windows Server 或 Linux 虛擬機器上，也稱為基礎結構即服務（IaaS）。 SQL 虛擬機器是遷移內部部署 SQL Server 資料庫和應用程式，而不需要變更任何資料庫的絕佳選項。 所有近期的 SQL Server 版本均可安裝在 IaaS 虛擬機器中。 

    與 SQL Database 和 SQL 受控執行個體最顯著的差異在於 Azure 虛擬機器上的 SQL Server 允許完整控制資料庫引擎。 您可以選擇啟動維護/修補的時機，將復原模式變更為簡單或大量記錄，並在需要時暫停或啟動服務，而且您可以完全自訂 SQL Server 的資料庫引擎。 隨著此額外的控制，管理虛擬機器還有額外的責任。
- 已經過最佳化，適合將現有的應用程式移轉到 Azure 或者在混合式部署中將現有的內部部署應用程式擴充到雲端。 此外，您可以使用虛擬機器中的 SQL Server 開發和測試傳統的 SQL Server 應用程式。 有了 SQL 虛擬機器，您可以透過專用的 SQL Server 實例和雲端式 VM，擁有完整的系統管理許可權。 當組織擁有可用來維護虛擬機器的 IT 資源時，此選項會是最佳選擇。 這些功能可讓您建立高度自訂的系統，以解決應用程式的特定效能和可用性需求。

下表列出額外的差異，但*SQL Database 和 SQL 受控執行個體都已優化，可將布建和管理許多資料庫的整體管理成本降到最低。* 因為您不需要管理任何虛擬機器、作業系統或資料庫軟體，所以持續進行的系統管理成本會降低。 您不需要管理升級、高可用性或 [備份](database/automated-backups-overview.md)。 

一般來說，SQL Database 和 SQL 受控執行個體可能會大幅增加單一 IT 或開發資源所管理的資料庫數目。 [彈性集區](database/elastic-pool-overview.md)也支援 SaaS 多租用戶應用程式架構，其中包括租用戶隔離和調整等功能，可藉由跨資料庫共用資源來降低成本。 [SQL 受控執行個體](managed-instance/sql-managed-instance-paas-overview.md)提供實例範圍功能的支援，讓您輕鬆地遷移現有的應用程式，以及在資料庫之間共用資源。

### <a name="comparison-table"></a>比較表

| Azure SQL Database | Azure SQL 受控執行個體 | Azure VM 上的 SQL Server |
| :--- | :--- | :--- |
|支援大多數的內部部署資料庫層級功能。 提供最常使用的 SQL Server 功能。<br/>保證 99.995% 的可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>將所需的資源 (CPU/儲存體) 指派給個別資料庫的能力。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 支援幾乎所有的內部部署執行個體層級和資料庫層級功能。 與 SQL Server 的高度相容性。<br/>保證 99.99% 可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>從 SQL Server 輕鬆移轉。<br/>Azure 虛擬網路中的私人 IP 位址。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 您對 SQL Server 引擎具有完全的控制權。 支援所有內部部署功能。<br/>最高可達99.99% 的可用性。<br/>與內部部署 SQL Server 的相符版本完全一致。<br/>已修正、已知的資料庫引擎版本。<br/>從 SQL Server 輕鬆移轉。<br/>Azure 虛擬網路中的私人 IP 位址。<br/>您能夠在放置 SQL Server 的主機上部署應用程式或服務。|
|從 SQL Server 遷移可能是一項挑戰。<br/>無法使用部分 SQL Server 功能。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。<br/>使用[Azure 私人連結](database/private-endpoint-overview.md)的私人 IP 位址支援。|仍有極少數的 SQL Server 功能無法使用。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。|您需要管理備份和修補程式。<br>您需要實作自己的高可用性解決方案。<br/>變更資源 (CPU/儲存體) 時會出現停機時間|
| 最多 100 TB 的資料庫。 | 最多 8 TB。 | SQL Server 的實例，最多可達 256 TB 的儲存體。 執行個體可以支援所需數量的資料庫。 |
| 內部部署應用程式可以存取 Azure SQL Database 中的資料。 | 使用 Azure Express Route 或 VPN 閘道[執行原生虛擬網路](managed-instance/vnet-existing-add-subnet.md)和內部部署環境的連線。 | 有了 SQL 虛擬機器，您就可以讓應用程式在雲端中執行，而且部分內部部署。 例如，您可以透過 [ [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)] 將內部部署網路和 Active Directory 網域延伸到雲端。 如需有關混合式雲端解決方案的詳細資訊，請參閱[將內部部署資料解決方案擴充至雲端](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)。 |


## <a name="cost"></a>成本

無論您是錢現金的新創公司，或是已建立且在嚴格預算限制之下運作的公司小組，在決定如何裝載您的資料庫時，有限的資金量通常是主要的驅動程式。 在本節中，您會瞭解 Azure 中與 Azure SQL 服務系列相關聯的計費和授權基本概念。  您也會了解應用程式總成本的計算。

### <a name="billing-and-licensing-basics"></a>計費和授權基本概念

目前， **SQL Database**和**SQL 受控執行個體**都是以服務的形式銷售，而且有數個選項可供使用，而且在數個服務層級中具有不同的資源價格，這些都是根據您所選擇的服務層級和計算大小，每小時以固定費率計費。 如需目前支援的服務層級、計算大小和儲存體數量的最新資訊，請參閱適用于[SQL Database 和 SQL 受控執行個體之 SQL Database 和 vCore 為基礎的購買模型](database/service-tiers-vcore.md)的[以 DTU 為基礎的購買模型](database/service-tiers-dtu.md)。

- 有了 SQL Database，您就可以從適用于基本層的 5 $/月，選擇符合您需求的服務層級，而且可以建立[彈性](database/elastic-pool-overview.md)集區以在資料庫之間共用資源，以降低成本並容納使用量尖峰。
- 有了 SQL 受控執行個體，您也可以自備授權。 如需自備授權的詳細資訊，請參閱[Azure 上的藉軟體保證而實現的授權行動性](https://azure.microsoft.com/pricing/license-mobility/)或使用[Azure Hybrid Benefit 計算機](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)，以瞭解如何省下**最多 40%** 的資料。

此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。 您可以動態調整服務層級和計算大小，以滿足應用程式的不同輸送量需求。

使用**SQL Database**和**SQL 受控執行個體**，Azure 會自動設定、修補和升級資料庫軟體，以降低您的系統管理成本。 此外，它 [內建的備份](database/automated-backups-overview.md) 功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。

有了**Azure vm 上的 SQL**，您就可以使用任何平臺提供的 SQL Server 映射（其中包含授權）或攜帶您的 SQL Server 授權。 所有支援的 SQL Server 版本（2008R2、2012、2014、2016、2017、2019）和版本（Developer、Express、Web、Standard、Enterprise）都可供使用。 此外，自備授權版本 (BYOL) 的映像也可供使用。 使用 Azure 所提供的映像時，營運成本取決於您所選擇的 VM 大小以及 SQL Server 版本。 不論 VM 大小或 SQL Server 版本為何，您須支付 SQL Server 和 Windows 或 Linux Server 的每分鐘授權成本，以及 VM 磁碟的 Azure 儲存體成本。 每分鐘計費選項可讓您隨時使用 SQL Server，而無需購買其他 SQL Server 授權。 如果在 Azure 中採用自己的 SQL Server 授權，您僅需支付伺服器和儲存體成本。 如需採用自己的授權的詳細資訊，請參閱 [Azure 上透過軟體保證的授權機動性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

#### <a name="calculating-the-total-application-cost"></a>計算應用程式總成本

當您開始使用雲端平台時，執行應用程式的成本包括新開發和持續管理成本，以及公用雲端平台的服務成本。

如需價格的詳細資訊，請參閱下列資源：

- [SQL Database & SQL 受控執行個體定價](https://azure.microsoft.com/pricing/details/sql-database/)
- 適用于[SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)的[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>系統管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度。 透過 IaaS 和 PaaS，Azure 會管理基礎結構並自動複寫所有資料，以提供嚴重損壞修復、設定和升級資料庫軟體、管理負載平衡，以及在資料中心內發生伺服器失敗時進行透明容錯移轉。

- 有了**SQL Database**和**SQL 受控執行個體**，您就可以繼續管理您的資料庫，但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例包括資料庫和登入、索引和查詢微調，以及稽核和安全性。 此外，設定其他資料中心的高可用性時，需要進行最小的設定和系統管理。
- 有了**AZURE VM 上的 SQL**，您就可以完全掌控作業系統和 SQL Server 實例設定。 有了 VM，您可以決定何時更新/升級作業系統與資料庫軟體，以及何時安裝任何其他軟體（例如防毒程式）。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 此外，您還可以控制 VM 的大小、磁碟的數目及其儲存體組態。 Azure 可讓您視需要變更 VM 大小。 如需相關資料，請參閱 [Azure 的虛擬機器和雲端服務大小](../virtual-machines/windows/sizes.md)。

## <a name="service-level-agreement-sla"></a>服務等級協定（SLA）

對於許多 IT 部門而言，符合服務等級協定（SLA）的即時責任是最重要的。 在本節中，我們將瞭解 SLA 對每個資料庫主控選項的作用。

針對**Azure SQL Database**和**Azure SQL 受控執行個體**，Microsoft 提供99.99% 的可用性 SLA。 如需最新資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/sql-database/)。

針對**AZURE VM 上的 SQL**，Microsoft 提供99.95% 的可用性 SLA，其中僅涵蓋虛擬機器。 本 SLA 未涵蓋在 VM 上執行的程序 (例如 SQL Server)，而且您必須裝載一個可用性設定組中的至少兩個 VM 執行個體。 如需最新資訊，請參閱 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 如需 Vm 內的資料庫高可用性（HA），您應該在 SQL Server 中設定其中一個支援的高可用性選項，例如[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 使用支援的高可用性選項並不提供額外的 SLA，但可讓您達成 >99.99% 的資料庫可用性。

## <a name="time-to-move-to-azure"></a><a name="market"></a>移至 Azure 的時間

當新解決方案的開發人員生產力和快速上市時間很重要時， **Azure SQL Database**是雲端設計應用程式的正確解決方案。 有了程式設計 DBA 類似功能，此選項非常適合雲端架構設計人員和開發人員，因為它會降低管理基礎作業系統和資料庫的需求。

**AZURE SQL 受控執行個體**可大幅簡化將現有應用程式遷移至 azure 的工作，讓您可以快速地將已遷移的資料庫應用程式帶入 azure 市場。

如果您現有或新的應用程式需要大型資料庫或存取 SQL Server 或 Windows/Linux 中的所有功能，而且您想要避免取得新內部部署硬體的時間和費用，則**AZURE VM 上的 SQL**非常理想。 當您想要將現有的內部部署應用程式和資料庫移轉至 Azure 時，如果 SQL Database 或 SQL 受控執行個體不適合，這也是很適合的情況。 由於您無需變更簡報、應用程式和資料層，您會在重新架構現有解決方案時節省時間和預算。 相反地，您可以將重點放在將所有解決方案移轉至 Azure，並進行 Azure 平台可能需要的某些效能最佳化作業。 如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳作法](virtual-machines/windows/performance-guidelines-best-practices.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>後續步驟

- 若要開始使用 SQL Database，請參閱[您的第一個 Azure SQL Database](database/single-database-create-quickstart.md)。
- 請參閱[您的第一個 AZURE sql 受控執行個體](managed-instance/instance-create-quickstart.md)，以開始使用 SQL 受控執行個體。 
- 請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)。
- 請參閱 [在 Azure 中佈建 SQL Server 虛擬機器](virtual-machines/windows/create-sql-vm-portal.md) 以開始使用 Azure VM 上的 SQL Server。
- [針對您的內部部署資料庫，識別正確的 SQL Database 或 SQL 受控執行個體 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
