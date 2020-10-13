---
title: 什麼是 Azure SQL？
description: 了解 Azure SQL 服務系列內的不同選項：Azure VM 上的 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server 雲端, 雲端中的 SQL Server, PaaS 資料庫, 雲端 SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 4cc1eefa93366451b568da789fd48d8a8c658439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618262"
---
# <a name="what-is-azure-sql"></a>什麼是 Azure SQL？ 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL 是一個受控、安全且智慧型的產品系列，會使用 Azure 雲端中的 SQL Server 資料庫引擎。

- **Azure SQL Database**：在包含無伺服器計算的智慧型受控資料庫服務上，支援新式雲端應用程式。 
- **Azure SQL 受控執行個體**：使用智慧型、完全受控的執行個體作為服務，將您現有的 SQL Server 應用程式大規模現代化，且與 SQL Server 資料庫引擎間的功能同位幾乎達到 100%。 對於大多數的雲端移轉都非常適用。
- **Azure VM 上的 SQL Server**：讓您輕鬆進行 SQL Server 工作負載的隨即轉移，同時保有 100% 的 SQL Server 相容性，以及作業系統層級存取。 
 
Azure SQL 的建制基礎為慣用的 SQL Server 引擎，因此您可以輕鬆遷移應用程式，並繼續使用您熟悉的工具、語言和資源。 您的技能和經驗將轉移到雲端，因此您將可更進一步運用現有的資源。 

了解如何將各項產品融入 Microsoft 的 Azure SQL 資料平台中，以運用正確選項因應您的商務需求。 無論您以節省成本還是精簡管理作為優先考量，本文都可依據您最重視的商務需求，協助您決定要以何種方法達此目的。


如果您不熟悉 Azure SQL，請觀看我們深入的 [Azure SQL 影片系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中的*什麼是 Azure SQL* 影片：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]



## <a name="overview"></a>概觀

在當今這個資料化的時代，為了推動數位轉型，我們管理大量資料及掌握其潛力的能力因而愈形重要。 但現今的資料資產日趨複雜，資料裝載於內部部署、雲端或網路邊緣。 建置智慧型和沉浸式應用程式的開發人員可能會發現自己受到限制，而這些限制終將影響他們的經驗。 因平台不相容、資料安全性不佳、資源不足和性價比障礙而產生的限制，可能會造成阻礙應用程式現代化和開發的複雜因素。 

在 Azure 與內部部署 SQL Server 資料庫的任何討論中，您要了解的第一件事情是您可以兩者都用。 Microsoft 資料平台會利用 SQL Server 技術，使其可在跨內部部署實體機器、私人雲端環境、第三方代管的私人雲端環境，和公用雲端中使用。 


### <a name="fully-managed-and-always-up-to-date"></a>完全受控且隨時更新 

著重於創新，而無須投注過多心力在修補、更新和備份資料庫上。 Azure 是唯一可由 SQL 持續自動套用最新更新和修補程式的雲端，讓您將資料庫永遠保持在最新狀態，而無須再煩惱支援終止的瑣碎事務。 即使是效能微調、高可用性、災害復原和備份之類的複雜工作，也都可以自動化，而讓您全心投入於應用程式。  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>使用內建的智慧型安全性保護您的資料 

Azure 會持續監視您的資料是否受到威脅。 透過 Azure SQL，您將可：

- 使用智慧型[進階威脅防護](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services)和主動式弱點評量警示，即時進行潛在威脅的相關補救。 
- 使用[內建安全性控制](https://azure.microsoft.com/overview/security/) (包括 T-SQL、驗證、網路和金鑰管理) 取得領先業界的多層保護。 
- 利用所有雲端資料庫服務中最全面的[合規性](https://azure.microsoft.com/overview/trusted-cloud/compliance/)涵蓋範圍。 


### <a name="business-motivations"></a>商務動機

您在不同資料供應項目間的抉擇，可能會受到若干因素的影響：

- [成本](#cost)：PaaS 和 IaaS 選項都包括基底價格，其中涵蓋基礎結構和授權。 但是，使用 IaaS 選項，您需要投入更多時間和資源來管理資料庫，而在 PaaS 中，您會收到價格中包含的這些管理功能。 IaaS 可讓您在不使用資源時關閉資源以降低成本，而 PaaS 則會持續執行，除非您在需要時卸除並重新建立資源。
- [管理](#administration)：PaaS 選項會降低您管理資料庫所需投入的時間量。 不過，您可以執行的自訂管理工作和指令碼的範圍，也會受到限制。 例如，目前不支援將 CLR 用於 SQL Database，但支援用於 SQL 受控執行個體。 此外，PaaS 中沒有支援使用追蹤旗標的部署選項。
- [服務等級協定](#service-level-agreement-sla)：IaaS 和 PaaS 都提供最高業界標準的 SLA。 針對基礎架構，PaaS 選項可保證 99.99 % 的 SLA，而 IaaS 保證 99.95% 的 SLA，這表示您需要實作其他的機制來確保資料庫的可用性。 您可以藉由建立額外的 SQL 虛擬機器，並實作 SQL Server Always On 可用性群組高可用性解決方案，達到 99.99% 的 SLA。 
- [移至 Azure 的時間](#market)：Azure VM 中的 SQL Server 與您的環境完全相符，因此從內部部署移轉至 Azure VM 與將資料庫從一個內部部署伺服器移至另一個伺服器沒有什麼不同。 SQL 受控執行個體還可以實現輕鬆的移轉；但是在移轉之前可能需要套用一些變更。 


## <a name="service-comparison"></a>服務比較

   ![雲端 SQL Server 選項：IaaS 上的 SQL Server 或雲端中的 SaaS SQL Database。](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如圖所示，每個服務供應項目可依您在基礎結構中擁有的管理層級，以及依成本效益程度等特性，來進行分類。

在 Azure 中，您可以讓 SQL Server 工作負載以託管服務 ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) 形式執行，在是託管基礎結構 ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) 中執行。 在 PaaS 中，您有多個產品選項，而且每個選項都有多個服務層級。 在 PaaS 或 IaaS 之間做決定時，您必須要問的關鍵問題是，您是要自己管理資料庫、套用修補程式、進行備份，還是要將這些作業委派給 Azure？

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) 是裝載在 Azure 的關聯式資料庫即服務 (DBaaS)，其產業類別屬於「軟體即服務 (SaaS)」。 
- 最適合用於需使用最新穩定 SQL Server 功能，且開發與行銷階段有時間限制的新式雲端應用程式。 
- 一個完全受控的 SQL Server 資料庫引擎，以最新穩定版的 SQL Server Enterprise 為基礎。 SQL Database 有兩個部署選項，其建置基礎為 Microsoft 所擁有、裝載及維護的標準化硬體和軟體。 

透過 SQL Server，您可以使用需要大量設定的內建特性和功能 (內部部署或在 Azure 虛擬機器中)。 使用 SQL Database 時，您可以隨用隨付，並使用相應增加或相應放大選項以取得不需中斷的更強大功能。 SQL Database 具有一些無法在 SQL Server 中使用的額外功能，例如內建高可用性、智慧和管理功能。


Azure SQL Database 提供下列部署選項：
  - [*單一資料庫*](database/single-database-overview.md)形式，內含透過[邏輯 SQL 伺服器](database/logical-servers.md)管理的自有資源集。 單一資料庫類似 SQL Server 中的[自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 此選項適用於新的雲端應用程式的現代應用程式開發。 提供[超大規模](database/service-tier-hyperscale.md)和[無伺服器](database/serverless-tier-overview.md)選項。
  - [*彈性集區*](database/elastic-pool-overview.md)，這是透過[邏輯 SQL 伺服器](database/logical-servers.md)管理的共用資源集所屬的資料庫集合。 單一資料庫可以移入和移出彈性集區。 此選項非常適合讓使用多租用戶 SaaS 應用程式模式的新型雲端應用程式用來進行新式應用程式部署。 彈性集區可讓您以合乎成本效益的方式管理具有各種使用量模式之多個資料庫的效能。

### <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

[Azure SQL 受控執行個體](managed-instance/sql-managed-instance-paas-overview.md)屬於業界類別的*平台即服務 (PaaS)* ，對於多數的雲端移轉都非常適用。 SQL 受控執行個體是具有共用資源集的系統和使用者資料庫集合，可供進行隨即轉移。  
- 非常適用於需要在最少變更的情況下使用最新穩定 SQL Server 功能，且會遷移至雲端的新應用程式或現有內部部署應用程式。 SQL 受控執行個體類似於 [Microsoft SQL Server 資料庫引擎](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview)的執行個體，能夠為資料庫提供共用資源，以及其他執行個體範圍的功能。 
- SQL 受控執行個體支援從內部部署進行資料庫移轉，且幾乎或完全無須對資料庫進行變更。 此選項提供 Azure SQL Database 的所有 PaaS 優勢，但會新增先前只在 SQL Server VM 中提供的功能。 這包括原生虛擬網路以及與內部部署 SQL Server 接近 100% 的相容性。 SQL 受控執行個體會提供完整的 SQL Server 存取權和功能相容性，以供您將 SQL Server 遷移至 Azure。

### <a name="sql-server-on-azure-vm"></a>Azure VM 上的 SQL Server

[Azure VM 中的 SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 屬於*基礎結構即服務 (IaaS)* 產業類別，可讓您在 Azure 中完全受控的虛擬機器 (VM) 內執行 SQL Server。 
- 最適合需要 OS 層級存取的移轉和應用程式。 Azure 中的 SQL 虛擬機器可隨即轉移，適用於需要在最少或無變更的情況下快速移轉至雲端的現有應用程式。 SQL 虛擬機器會提供 SQL Server 執行個體的完整系統管理控制權，以供您移轉至 Azure。 
- 當您不想購買內部部署 SQL Server 非生產硬體時的快速開發和測試案例。 SQL 虛擬機器也會在 Microsoft 所擁有、裝載及維護的標準化硬體上執行。 使用 SQL 虛擬機器時，您可以透過隨用隨付取得已包含在 SQL Server 映像中的 SQL Server 授權，或輕鬆使用現有的授權。 您也可以視需要停止或繼續執行 VM。 
- 安裝並裝載於雲端中的 SQL Server，會在執行於 Azure 的 Windows Server 或 Linux 虛擬機器上執行，也稱為基礎結構即服務 (IaaS)。 SQL 虛擬機器是遷移內部部署 SQL Server 資料庫和應用程式的理想選項，無需進行任何資料庫變更。 所有近期的 SQL Server 版本均可安裝在 IaaS 虛擬機器中。 

    相較於 SQL Database 和 SQL 受控執行個體，最顯著的差異在於，Azure 虛擬機器上的 SQL Server 可讓您完整控制資料庫引擎。 您可以選擇開始進行維護/修補的時間，將復原模式變更為簡單或大量記錄，視需要暫停或啟動服務，而且可以完整自訂 SQL Server 資料庫引擎。 此一額外的控制，也伴隨著管理虛擬機器的附加責任。
- 已經過最佳化，適合將現有的應用程式移轉到 Azure 或者在混合式部署中將現有的內部部署應用程式擴充到雲端。 此外，您可以使用虛擬機器中的 SQL Server 開發和測試傳統的 SQL Server 應用程式。 透過 SQL 虛擬機器，您將擁有專用 SQL Server 執行個體和雲端架構 VM 的完整系統管理權限。 當組織擁有可用來維護虛擬機器的 IT 資源時，此選項會是最佳選擇。 這些功能可讓您建立高度自訂的系統，以解決應用程式的特定效能和可用性需求。

下表列出其他不同之處，但 *SQL Database 和 SQL 受控執行個體都已經過最佳化，可將佈建和管理許多資料庫的整體管理成本降到最低。* 由於您無需管理任何虛擬機器、作業系統或資料庫軟體，進行中的系統管理成本得以降到最低。 您不需要管理升級、高可用性或 [備份](database/automated-backups-overview.md)。 

一般而言，SQL Database 和 SQL 受控執行個體可能會大幅增加由單一 IT 或開發資源管理的資料庫數目。 [彈性集區](database/elastic-pool-overview.md)也支援 SaaS 多租用戶應用程式架構，其中包括租用戶隔離和調整等功能，可藉由跨資料庫共用資源來降低成本。 [SQL 受控執行個體](managed-instance/sql-managed-instance-paas-overview.md)可提供執行個體範圍功能的支援，以便輕鬆地移轉現有的應用程式，以及在資料庫之間共用資源。

### <a name="comparison-table"></a>比較表

| Azure SQL Database | Azure SQL 受控執行個體 | Azure VM 上的 SQL Server |
| :--- | :--- | :--- |
|支援大多數的內部部署資料庫層級功能。 提供最常使用的 SQL Server 功能。<br/>保證 99.995% 的可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>將所需的資源 (CPU/儲存體) 指派給個別資料庫的能力。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 支援幾乎所有的內部部署執行個體層級和資料庫層級功能。 與 SQL Server 的高度相容性。<br/>保證 99.99% 可用性。<br/>內建備份、修補、復原。<br/>資料庫引擎最新穩定版。<br/>從 SQL Server 輕鬆移轉。<br/>Azure 虛擬網路中的私人 IP 位址。<br/>內建進階智能和安全性。<br/>線上資源變更 (CPU/儲存體)。| 您對 SQL Server 引擎具有完全的控制權。 支援所有內部部署功能。<br/>可用性高達 99.99%。<br/>與內部部署 SQL Server 的相符版本完全一致。<br/>經過修正的已知資料庫引擎版本。<br/>從 SQL Server 輕鬆移轉。<br/>Azure 虛擬網路中的私人 IP 位址。<br/>您可以在 SQL Server 所在的主機上部署應用程式或服務。|
|從 SQL Server 進行移轉可能並不容易。<br/>無法使用部分 SQL Server 功能。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。<br/>[Azure 私人連結](database/private-endpoint-overview.md)的私人 IP 位址支援。|仍有極少數的 SQL Server 功能無法使用。<br/>不保證確切的維護時間 (但近乎透明)。<br/>可以只使用資料庫相容性層級來達成與 SQL Server 版本的相容性。|您需要管理備份和修補程式。<br>您需要實作自己的高可用性解決方案。<br/>變更資源 (CPU/儲存體) 時會出現停機時間|
| 最多 100 TB 的資料庫。 | 最多 8 TB。 | 具有高達 256 TB 儲存空間的 SQL Server 執行個體。 執行個體可以支援所需數量的資料庫。 |
| 內部部署應用程式可以存取 Azure SQL Database 中的資料。 | [實作原生虛擬網路](managed-instance/vnet-existing-add-subnet.md)和使用 Azure Express Route 或 VPN 閘道與內部部署環境連線。 | 透過 SQL 虛擬機器，您的應用程式將可部分在雲端中執行，部分在內部部署中執行。 例如，您可以透過 [ [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)] 將內部部署網路和 Active Directory 網域延伸到雲端。 如需有關混合式雲端解決方案的詳細資訊，請參閱[將內部部署資料解決方案擴充至雲端](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)。 |


## <a name="cost"></a>Cost

無論您是現金不足的新公司，或是在預算有限的情況下運作的已成立公司內部小組，有限資金經常會是決定主控資料庫方式的主要關鍵。 在本節中，您將了解 Azure 中與 Azure SQL 服務系列相關聯的計費和授權基本概念。  您也會了解應用程式總成本的計算。

### <a name="billing-and-licensing-basics"></a>計費和授權基本概念

目前，**SQL Database** 和 **SQL 受控執行個體**均以服務的形式銷售，並提供數個選項，而且在資源價格不同的數個服務層級中，所有資源都根據您所選擇的服務層級和計算大小，以固定費率計算每小時的費用。 如需有關目前支援服務層級、計算大小和儲存體帳戶的最新資訊，請參閱[以 DTU 為基礎的購買模型 (適用於 SQL Database)](database/service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型 (適用於 SQL Database 和 SQL 受控執行個體)](database/service-tiers-vcore.md)。

- 透過 SQL Database，您可以從基本層的每月 5 美元開始，從各種不同的價格中選擇適合您需求的服務層級，且您可以建立[彈性集區](database/elastic-pool-overview.md)而在資料庫間共用資源，以降低成本和因應使用量高峰。
- 使用 SQL 受控執行個體時，您也可以自備授權。 如需採用自己的授權的詳細資訊，請參閱 [Azure 上藉軟體保證而實現的授權行動性](https://azure.microsoft.com/pricing/license-mobility/)或使用 [Azure Hybrid Benefit 計算機](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)查看如何**節省高達 40%** 。

此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。 您可以動態調整服務層級和計算大小，以滿足應用程式的不同輸送量需求。

使用 **SQL Database** 和 **SQL 受控執行個體**時，Azure 會自動設定、修補和升級資料庫軟體，以降低您的系統管理成本。 此外，它 [內建的備份](database/automated-backups-overview.md) 功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。

透過 **Azure VM 上的 SQL**，您可以使用任何平台所提供的 SQL Server 映像 (其中包含授權)，或採用您的 SQL Server 授權。 所有支援的 SQL Server 版本 (2008R2、2012、2014、2016、2017、2019 以及 Developer、Express、Web、Standard、Enterprise) 都可供使用。 此外，自備授權版本 (BYOL) 的映像也可供使用。 使用 Azure 所提供的映像時，營運成本取決於您所選擇的 VM 大小以及 SQL Server 版本。 不論 VM 大小或 SQL Server 版本為何，您須支付 SQL Server 和 Windows 或 Linux Server 的每分鐘授權成本，以及 VM 磁碟的 Azure 儲存體成本。 每分鐘計費選項可讓您隨時使用 SQL Server，而無需購買其他 SQL Server 授權。 如果在 Azure 中採用自己的 SQL Server 授權，您僅需支付伺服器和儲存體成本。 如需採用自己的授權的詳細資訊，請參閱 [Azure 上透過軟體保證的授權機動性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，傳出的網際網路流量也會以一般 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

#### <a name="calculating-the-total-application-cost"></a>計算應用程式總成本

當您開始使用雲端平台時，執行應用程式的成本包括新開發和持續管理成本，以及公用雲端平台的服務成本。

如需價格的詳細資訊，請參閱下列資源：

- [SQL Database 和 SQL 受控執行個體的定價](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 的[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度。 透過 IaaS 和 PaaS，Azure 可以管理基礎硬體、自動複製所有資料以提供災害復原、設定及升級資料庫軟體、管理負載平衡，以及在資料中心發生伺服器故障時進行透明容錯移轉。

- 透過 **SQL Database** 和 **SQL 受控執行個體**，您可以繼續管理您的資料庫，但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例包括資料庫和登入、索引和查詢微調，以及稽核和安全性。 此外，設定其他資料中心的高可用性時，需要進行最小的設定和系統管理。
- 透過 **Azure VM 上的 SQL**，您可以完全掌控作業系統和 SQL Server 執行個體設定。 透過 VM，您可以決定何時更新/升級作業系統與資料庫軟體，以及何時安裝任何其他軟體 (例如防毒)。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 此外，您還可以控制 VM 的大小、磁碟的數目及其儲存體組態。 Azure 可讓您視需要變更 VM 大小。 如需相關資料，請參閱 [Azure 的虛擬機器和雲端服務大小](../virtual-machines/windows/sizes.md)。

## <a name="service-level-agreement-sla"></a>服務等級協定 (SLA)

對於許多 IT 部門而言，達到服務等級協定 (SLA) 的正常運作時間義務是首要任務。 在本節中，我們將瞭解 SLA 對每個資料庫主控選項的作用。

對於 **Azure SQL Database** 和 **Azure SQL 受控執行個體**，Microsoft 提供 99.99% 的可用性 SLA。 如需最新資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/sql-database/)。

對於 **Azure VM 上的 SQL**，Microsoft 提供 99.95% 的可用性 SLA (僅涵蓋虛擬機器)。 本 SLA 未涵蓋在 VM 上執行的程序 (例如 SQL Server)，而且您必須裝載一個可用性設定組中的至少兩個 VM 執行個體。 如需最新資訊，請參閱 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 如需 VM 內的資料庫高可用性 (HA)，您應在 SQL Server 中設定其中一個支援的高可用性選項，例如 [AlwaysOn 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 使用支援的高可用性選項並不提供額外的 SLA，但可讓您達成 >99.99% 的資料庫可用性。

## <a name="time-to-move-to-azure"></a><a name="market"></a>移至 Azure 的時間

當新解決方案的開發人員生產力和快速上市時間很重要時，**Azure SQL Database** 將是雲端式設計應用程式的理想解決方案。 有了程式設計 DBA 類似功能，此選項非常適合雲端架構設計人員和開發人員，因為它會降低管理基礎作業系統和資料庫的需求。

**Azure SQL 受控執行個體**可大幅簡化將現有應用程式移轉至 Azure 的作業，讓您能夠在 Azure 中迅速將已移轉的資料庫應用程式上市。

如果現有或新的應用程式需要大型資料庫，或存取 SQL Server 或 Windows/Linux 中的所有功能，而且您想要避免取得新內部部署硬體的時間和費用，則 **Azure VM 上的 SQL** 將是理想選項。 這也很適合想要依現狀將現有的內部部署應用程式和資料庫移轉至 Azure - 在不適合使用 SQL Database 或 SQL 受控執行個體的情況下。 由於您無需變更簡報、應用程式和資料層，您會在重新架構現有解決方案時節省時間和預算。 相反地，您可以將重點放在將所有解決方案移轉至 Azure，並進行 Azure 平台可能需要的某些效能最佳化作業。 如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳作法](virtual-machines/windows/performance-guidelines-best-practices.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>後續步驟

- 若要開始使用 SQL Database，請參閱[您的第一個 Azure SQL Database](database/single-database-create-quickstart.md)。
- 請參閱[您的第一個 Azure SQL 受控執行個體](managed-instance/instance-create-quickstart.md)，以開始使用 SQL 受控執行個體。 
- 請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)。
- 請參閱 [在 Azure 中佈建 SQL Server 虛擬機器](virtual-machines/windows/create-sql-vm-portal.md) 以開始使用 Azure VM 上的 SQL Server。
- [為您的內部部署資料庫識別正確的 SQL Database 或 SQL 受控執行個體 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
