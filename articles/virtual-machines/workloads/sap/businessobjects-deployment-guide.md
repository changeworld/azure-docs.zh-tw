---
title: 在 Azure 上部署 SAP BusinessObjects BI 平臺 |Microsoft Docs
description: 在 Azure 上規劃、部署及設定 SAP BusinessObjects BI 平臺
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: ee7cc1c81bd35bf2b51c712c3a60a046435ee700
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951278"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Azure 上的 SAP BusinessObjects BI 平台規劃與實作指南

## <a name="overview"></a>概觀

本指南的目的是要提供在 Azure 上規劃、部署及設定 SAP BusinessObjects BI 平臺的指導方針，也稱為 SAP BOBI 平臺。 本指南旨在涵蓋與 SAP BOBI Platform 相關的常見 Azure 服務和功能。 本指南不是所有可能設定選項的詳盡清單。 其中涵蓋一般部署案例的常見解決方案。

本指南的目的不是要取代標準的 SAP BOBI Platform 安裝和系統管理指南、作業系統或任何資料庫檔案。

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>在 Azure 上規劃和執行 SAP BusinessObjects BI 平臺

Microsoft Azure 提供廣泛的服務，包括計算、儲存體、網路功能，以及其他許多企業都能建立其應用程式，而不需要冗長的採購週期。 Azure 虛擬機器 (VM) 協助公司根據其商務需求，為不同的 SAP 應用程式（例如 SAP NetWeaver 型應用程式、SAP Hybris、sap BusinessObjects BI 平臺）部署隨選和可擴充的計算資源。 Azure 也支援跨單位連線能力，讓公司能夠將 Azure 虛擬機器整合到其內部部署網域、私人雲端和 SAP 系統內容。

本檔提供 Azure 上的 SAP BusinessObjects BI 平臺規劃和實行考慮的指引。 它會補充 SAP 安裝檔和 SAP 附注，其中代表安裝和部署 SAP BOBI 的主要資源。

### <a name="architecture-overview"></a>架構概觀

SAP BusinessObjects BI 平臺是一個獨立的系統，可存在於單一 Azure 虛擬機器上，或可調整為多個執行不同元件的 Azure 虛擬機器叢集。 SAP BOBI Platform 包含六個概念層：用戶端層、Web 層、管理層、儲存層、處理層和資料層。  (如需每一層的詳細資訊，請參閱 [SAP BusinessObjects 商務智慧平臺](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) 說明入口網站) 中的系統管理員指南。 以下是每個層級的高階詳細資料：

- **用戶端層：** 它包含所有與 BI 平臺互動的桌面用戶端應用程式，以提供不同類型的報告、分析和系統管理功能。
- **Web 層：** 它包含部署至 JAVA web 應用程式伺服器的 web 應用程式。 Web 應用程式可透過網頁瀏覽器提供 BI 平臺功能給終端使用者。
- **管理層：** 它會協調並控制製作 BI 平臺的所有元件。 它包含中央管理伺服器 (CMS) 以及事件伺服器和相關聯的服務
- **儲存層：** 它負責處理檔案，例如檔和報表。 它也會處理報表快取，以便在使用者存取報表時儲存系統資源。
- **處理層：** 它會分析資料，並產生報表和其他輸出類型。 這是唯一存取包含報表資料之資料庫的層級。
- **資料層：** 它是由裝載 CMS 系統資料庫和審核資料存放區的資料庫伺服器所組成。

SAP BI 平臺是由一組在一或多部主機上執行的伺服器所組成。 您必須根據大小、商務需求和環境類型，選擇正確的部署策略。 針對小型安裝（例如開發或測試），您可以針對 web 應用程式伺服器、資料庫伺服器和所有 BI 平臺伺服器使用單一 Azure 虛擬機器。 如果您是使用資料庫即服務 (從 Azure DBaaS) 供應專案，資料庫伺服器將會與其他元件分開執行。 針對中等和大型安裝，您可以讓伺服器在多部 Azure 虛擬機器上執行。

在下圖中，會顯示 Azure 虛擬機器上 SAP BOBI Platform 大規模部署的架構，其中每個元件都會散發並放在可用性設定組中，如果發生服務中斷時，就可以維持容錯移轉。

![Azure 上的 SAP BusinessObjects BI 平臺架構](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>架構詳細資料

- 負載平衡器

  在 SAP BOBI 多重實例部署中，Web 應用程式伺服器 (或 web 層) 正在兩部以上的主機上執行。 若要將使用者負載平均分散到 web 伺服器上，您可以在使用者和 web 伺服器之間使用負載平衡器。 在 Azure 中，您可以使用 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 或 [Azure 應用程式閘道](../../../application-gateway/overview.md) 來管理 web 伺服器的流量。

- Web 應用程式伺服器

  Web 服務器裝載 SAP BOBI 平臺的 web 應用程式，例如 CMC 和 BI 啟動面板。 若要達到 web 伺服器的高可用性，您必須部署至少兩個 web 應用程式伺服器來管理冗余和負載平衡。 在 Azure 中，這些 web 應用程式伺服器可以放在可用性設定組或可用性區域中，以獲得更好的可用性。

  Tomcat 是適用于 SAP BI 平臺的預設 web 應用程式。 若要達到 tomcat 的高可用性，請在 Azure 中使用 [靜態成員資格攔截](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) 器來啟用會話複寫。 它可確保即使 tomcat 服務中斷，使用者仍可存取 SAP BI web 應用程式。

  > [!Important]
  > 根據預設，Tomcat 會使用多播 IP 和埠進行叢集化，Azure (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)) 不支援此功能。

- BI 平臺伺服器

  BI 平臺伺服器包含屬於 SAP BOBI 應用程式的所有服務 (管理層、處理層和儲存層) 。 當 web 伺服器收到要求時，它會偵測到每個 BI 平臺伺服器 (具體而言，就是叢集中的所有 CMS 伺服器) 並自動平衡其要求負載。 如果其中一個 BI 平臺主機失敗，網頁伺服器會自動將要求傳送至其他主機。

  若要達到 BI 平臺的高可用性或冗余，您必須在至少兩部 Azure 虛擬機器中部署應用程式。 根據大小調整，您可以調整您的 BI 平臺，以在更多 Azure 虛擬機器上執行。

- 檔案存放庫伺服器 (FRS) 

  檔案存放庫伺服器包含所有已建立的報表和其他 BI 檔。 在多重實例部署中，BI 平臺伺服器會在多個虛擬機器上執行，而且每個 VM 都應該可以存取這些報告和其他 BI 檔。 因此，必須在所有 BI 平臺伺服器之間共用檔案系統。

  在 Azure 中，您可以使用 [Azure Premium](../../../storage/files/storage-files-introduction.md) 檔案或適用于檔案存放庫伺服器的 [azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 。 這兩個 Azure 服務都有內建的冗余。

  > [!Important]
  > 適用于 Azure 檔案儲存體的 SMB 通訊協定已正式運作，但 Azure 檔案儲存體的 NFS 通訊協定支援目前為預覽狀態。 如需詳細資訊，請參閱 [Azure 檔案儲存體的 NFS 4.1 支援現供預覽](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- CMS & 審核資料庫
  
  SAP BOBI Platform 需要資料庫來儲存其系統資料（稱為 CMS 資料庫）。 它是用來儲存 BI 平臺資訊，例如使用者、伺服器、資料夾、檔、設定和驗證詳細資料。

  Azure 提供 [MySQL 資料庫](https://azure.microsoft.com/en-us/services/mysql/) 和 [Azure SQL database 的 Azure SQL database](https://azure.microsoft.com/en-us/services/sql-database/) 即服務 (DBaaS) 供應專案，可用於 CMS 資料庫和 Audit database。 因為這是 PaaS 供應專案，所以客戶不必擔心資料庫的作業、可用性和維護。 客戶也可以針對 CMS 選擇自己的資料庫，並根據其商務需求來審核存放庫。

## <a name="support-matrix"></a>支援矩陣

本節說明不同 SAP BOBI 元件的可支援性，例如 SAP BusinessObjects BI Platform 版本、作業系統和 Azure 中的資料庫。

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平臺

Azure 基礎結構即服務 (IaaS) 可讓您在 Azure 計算上部署和設定 SAP BusinessObjects BI 平臺。 它支援下列 SAP BOBI Platform 版本-

- SAP BusinessObjects BI 平臺4。3
- SAP BusinessObjects BI Platform 4.2 SP04 +
- SAP BusinessObjects BI Platform 4.1 SP05 起 +

SAP BI 平臺會在不同的作業系統和資料庫上執行。 您可以在 SAP BOBI 的 [產品可用性對照表](https://apps.support.sap.com/sap/support/pam) 中，找到作業系統和資料庫版本之間 sap BOBI 平臺的可支援性。

### <a name="operating-system"></a>作業系統

Azure 支援下列適用于 SAP BusinessObjects BI 平臺部署的作業系統。

- Microsoft Windows 伺服器
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL) 

產品可用性對照表中所列的作業系統版本 [ (適用于 SAP BUSINESSOBJECTS BI 平臺的 PAM) ](https://support.sap.com/pam) ，只要它們相容于在 Azure 基礎結構上執行就可支援。

### <a name="databases"></a>資料庫

BI 平臺需要適用于 CMS 和審核資料存放區的資料庫，這些資料庫可以安裝在 [SAP 產品可用性對照表](https://support.sap.com/pam) 所列的任何支援資料庫中，包括：

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) 只 (Windows 上的 SAP BOBI Platform 支援的資料庫) 

  它是完全受控的 SQL Server database engine，以 SQL Server 的最新穩定 Enterprise Edition 為基礎。 Azure SQL database 會處理大部分的資料庫管理功能，例如升級、修補和監視，而不需要使用者介入。 有了 Azure SQL Database，您可為 Azure 中的應用程式與解決方案建立高度可用且高效能的資料儲存層。 如需詳細資訊，請參閱 [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) 檔。

- [適用於 MySQL 的 Azure 資料庫](https://azure.microsoft.com/en-us/services/mysql/) (遵循與在 SAP PAM 中針對 MySQL AB 所述的相同相容性方針) 

  它是由 MySQL 社區版驅動的關係資料庫服務。 身為完全受控的資料庫即服務 (DBaaS) 的供應專案，可透過可預測的效能和動態擴充性來處理任務關鍵性工作負載。 它有內建的高可用性、自動備份、軟體修補、自動失敗偵測，以及時間點還原最多35天，可大幅減少作業工作。 如需詳細資訊，請參閱 [適用於 MySQL 的 Azure 資料庫](../../../mysql/overview.md) 檔。

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (版本和限制，請參閱 SAP 附注 [2039619](https://launchpad.support.sap.com/#/notes/2039619)) 

- MaxDB

本檔說明使用 Azure SQL Database 和 **Linux 上的 SAP BOBI 平臺搭配適用於 MySQL 的 Azure 資料庫** 在 **Windows 上部署 sap BOBI platform** 的指導方針。 這也是我們在 Azure 上執行 SAP BusinessObjects BI 平臺的建議方法。

## <a name="sizing"></a>調整大小

調整大小是一種程式，用來決定有效率地執行應用程式的硬體需求。 針對 SAP BOBI Platform，您必須使用名為 [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)的 SAP 調整大小工具來調整大小。 此工具會根據輸入提供 sap，然後需要對應至適用于 SAP 的認證 Azure 虛擬機器類型。 SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) 提供支援的 sap 產品和 Azure VM 類型的清單，以及 sap。 如需調整大小的詳細資訊，請參閱 [SAP BI 調整大小指南](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons)。

針對 SAP BOBI Platform 的儲存體需求，Azure 提供不同類型的 [受控磁碟](../../managed-disks-overview.md)。 針對 SAP BOBI 安裝目錄，建議使用高階受控磁片以及在虛擬機器上執行的資料庫，依照 [適用于 SAP 工作負載的 DBMS 部署](dbms_guide_general.md)中提供的指導方針進行。

Azure 支援兩種適用于 SAP BOBI Platform 資料層的 DBaaS 供應專案- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database) 在 Windows) 上執行的 (Bi 應用程式，以及在 Linux 和 Windows (上執行的 [適用於 MySQL 的 Azure 資料庫](https://azure.microsoft.com/en-us/services/mysql)) bi 應用程式。 因此，根據大小調整結果，您可以選擇最適合您需求的購買模型。

> [!Tip]
> 如需快速調整大小的參考，請考慮 800 sap = 1 vCPU，同時將 SAP BOBI Platform 資料庫層的 SAP 結果對應至 Azure 資料庫即服務 (Azure SQL Database 或適用於 MySQL 的 Azure 資料庫) 。

### <a name="sizing-models-for-azure-sql-database"></a>Azure SQL database 的大小調整模型

Azure SQL Database 提供下列三種購買模型：

- 以 vCore 為基礎

  它可讓您選擇虛擬核心數目、記憶體數量，以及儲存體的數量和速度。 以虛擬核心為基礎的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。 此模型適用于價值彈性、控制和透明度的客戶。

  VCore 模型中提供三種 [服務層級選項](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) ，包括-一般用途、業務關鍵和超大規模。 服務層級會定義與可用性和嚴重損壞修復相關的儲存體架構、空間、i/o 限制和商務持續性選項。 以下是每個服務層級選項的高階詳細資料-

  1. **一般用途** 服務層級最適合用於商務工作負載。 它提供了預算導向、平衡且可調整的計算和儲存體選項。 如需詳細資訊，請參閱 [資源選項和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)。
  2. **商務關鍵** 服務層級可讓商務應用程式使用數個隔離的複本，為失敗提供最高的復原能力，並為每個資料庫複本提供最高的 i/o 效能。 如需詳細資訊，請參閱 [資源選項和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)。
  3. **超大規模** 服務層級最適合具有可高度擴充的儲存體和讀取規模需求的商務工作負載。 它可讓您設定多個獨立的資料庫複本，以提供更高的失敗彈性。 如需詳細資訊，請參閱 [資源選項和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)。

- 以 DTU 為基礎

  以 DTU 為基礎的購買模型提供三個服務層級的計算、記憶體和 i/o 資源混合，以支援輕量與繁重的資料庫工作負載。 各層內的計算大小分別提供這些資源的不同混用方式，而且您可以為其新增額外的儲存體資源。 它最適合想要簡單、預先設定資源選項的客戶。

  以 DTU 為基礎的購買模型中的[服務層](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)，是以一系列計算大小來區分，其中包含固定數量的內含儲存體、固定的備份保留期間，以及固定價格。

- 無伺服器

  無伺服器模型可根據工作負載需求自動調整計算，並以每秒使用的計算量來計費。 無伺服器計算層會在只有儲存體計費時，于非使用期間自動暫停資料庫，並在活動傳回時自動繼續資料庫。 如需詳細資訊，請參閱 [資源選項和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。
  
  它更適合間歇性、無法預測的使用量，以及一段時間內的平均計算使用率。 因此，此模型可用於非實際執行的 SAP BOBI 部署。

> [!Note]
> 針對 SAP BOBI，使用以 vCore 為基礎的模型很方便，並根據商務需求選擇一般用途或業務關鍵服務層級。

### <a name="sizing-models-for-azure-database-for-mysql"></a>適用于 MySQL 的 Azure 資料庫大小調整模型

適用於 MySQL 的 Azure 資料庫隨附三個不同的定價層。 它們是以虛擬核心中的計算量、每個 vCore 的記憶體，以及用來儲存日期的儲存體技術來區分。 以下是選項的高階詳細資料，如需不同屬性的詳細資訊，請參閱適用於 MySQL 的 Azure 資料庫的 [定價層](../../../mysql/concepts-pricing-tiers.md) 。

- 基本

  它適用于需要輕量計算和 i/o 效能的目標工作負載。

- 一般用途

  它適用于需要平衡的計算和記憶體的大部分商務工作負載，並具有可調整的 i/o 輸送量。

- 記憶體最佳化

  適用于需要記憶體內部效能以取得更快速交易處理和更高並行的高效能資料庫工作負載。

> [!Note]
> 針對 SAP BOBI，使用以商務工作負載為基礎的一般用途或記憶體優化定價層會很方便。

## <a name="azure-resources"></a>Azure 資源

### <a name="choosing-regions"></a>選擇區域

Azure 區域是一或多個資料中心集合，其中包含要執行及裝載不同 Azure 服務的基礎結構。 此基礎結構包含許多節點，這些節點可作為計算節點或儲存體節點，或執行網路功能。 並非所有區域都提供相同的服務。

SAP BI 平臺包含不同的元件，這些元件可能需要特定的 VM 類型、儲存體（例如 Azure 檔案儲存體或 Azure NetApp Files）或「資料庫即服務」， (DBaaS 可能在某些區域中無法使用的資料層) 。 您可以查看 VM 類型、Azure 儲存體類型，或區域網站所 [提供產品](https://azure.microsoft.com/en-us/global-infrastructure/services/) 中其他 Azure 服務的確切資訊。 如果您已經在 Azure 上執行 SAP 系統，可能是您已找出您的區域。 在這種情況下，您必須先調查這些區域中是否有必要的服務，以決定 SAP BI 平臺的架構。

### <a name="availability-zones"></a>可用性區域

可用性區域與 Azure 區域實際上是兩個各自獨立的地點。 每個可用性區域都是由一或多個配備獨立電源、冷卻及網路的資料中心所組成。

若要在 SAP BI 平臺的每個層級達到高可用性，您可以藉由實施高可用性架構（可在 Azure 中提供最佳 SLA），將 Vm 分散在可用性區域之間。 針對 Azure 中的虛擬機器 SLA，請檢查最新版本的 [虛擬機器 sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

針對資料層，Azure 資料庫即服務 (DBaaS) 服務預設會提供高可用性架構。 您只需要選取區域和服務固有的高可用性、冗余和復原功能，就能減少計畫和未計畫中斷的資料庫停機時間，而不需要設定任何其他元件。 如需 Azure 上支援之 DBaaS 供應專案的 SLA 詳細資訊，請參閱 Azure SQL Database 適用於 MySQL 的 Azure 資料庫和[高](../../../azure-sql/database/high-availability-sla.md)可用性的[高可用性](../../../mysql/concepts-high-availability.md)。

### <a name="availability-sets"></a>可用性設定組

可用性設定組是一種邏輯群組功能，可在部署時將虛擬機器 (VM) 資源隔離。 Azure 可確保您在可用性設定組中所放置的 Vm 會跨多部實體伺服器、電腦架、儲存單位和網路交換器執行。 如果發生硬體或軟體失敗，則只有一部分的 Vm 會受到影響，且您的整體解決方案仍可運作。 因此，當虛擬機器放置於可用性設定組時，Azure 網狀架構控制器會將 Vm 分散到不同的 [容錯](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) 網域和 [升級](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) 網域，以防止所有 vm 因為基礎結構維護或單一容錯網域內的失敗而無法存取。

SAP BI 平臺包含許多不同的元件，而在設計架構時，您必須確保每個元件都有任何中斷的彈性。 您可以將每個元件的 Azure 虛擬機器放在可用性設定組內，以達成此目的。 請記住，當您在一個可用性設定組內混用不同 VM 系列的 Vm 時，您可能會遇到問題，而導致您無法在這類可用性設定組中包含特定的 VM 類型。 因此，Web 應用程式的 BI 應用程式、適用于 SAP BI 平臺的 BI 應用程式會在架構總覽中醒目提示。

此外，Azure 縮放單位內的 Azure 可用性設定組可使用的更新和容錯網域數目是有限的。 因此，如果您持續將 Vm 新增至單一可用性設定組，兩個或多個 Vm 最後會在相同的容錯或更新網域中結束。 如需詳細資訊，請參閱適用于 SAP 檔的 Azure 虛擬機器規劃和執行的 [Azure 可用性設定組](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) 一節。

若要瞭解 Azure 可用性設定組的概念，以及可用性設定組與容錯網域和升級網域之間的關係，請參閱 [管理可用性](../../manage-availability.md) 文章。

> [!Important]
> Azure 可用性區域和 Azure 可用性設定組的概念互斥。 也就是說，您可以將一對或多個 VM 部署到特定可用性區域或 Azure 可用性設定組。 但不能同時部署到兩者。

### <a name="virtual-machines"></a>虛擬機器

Azure 虛擬機器是一項服務供應專案，可讓您將自訂映射部署至 Azure，作為基礎結構即服務 (IaaS) 實例。 它提供隨選計算和儲存體，來裝載、調整及管理 Web 應用程式和連線的應用程式，藉此簡化維護和操作應用程式的作業。

Azure 提供各種虛擬機器來滿足您所有的應用程式需求。 但對於 SAP 工作負載而言，Azure 已將選取範圍縮小至適用于 SAP 工作負載和 SAP Hana 工作負載的不同 VM 系列。 如需深入瞭解，請參閱 [Azure 部署支援的 SAP 軟體](sap-supported-product-on-azure.md)。

根據 SAP BI 平臺的大小調整，您需要將您的需求對應至 azure 虛擬機器，在 Azure 中支援 SAP 產品。 SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) 是很好的起點，可針對 Windows 和 Linux 上的 SAP 產品列出支援的 Azure VM 類型。 另外一點要注意的一點是，除了單純支援的 VM 類型之外，您還需要檢查這些 VM 類型是否可在特定區域中使用。 您可以在 [ [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/) ] 頁面上檢查 VM 類型的可用性。 若要選擇定價模型，您可以參考 [適用于 SAP 工作負載的 Azure 虛擬機器](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>儲存體

Azure 儲存體是 Azure 管理的雲端服務，可提供高度可用、安全、持久、可調整且可重複的儲存體。 某些儲存體類型在 SAP 案例中的用途有限。 但有數種 Azure 儲存體類型很適合用於特定的 SAP 工作負載案例。 如需詳細資訊，請參閱 [Sap 工作負載指南的 Azure 儲存體類型](planning-guide-storage.md) ，因為它會強調適用于 sap 的不同儲存體選項。

Azure 儲存體有不同的儲存體類型可供客戶使用，而在 [Azure 中有哪些可用磁片類型](../../disks-types.md)的詳細資料，可在文章中閱讀。 SAP BOBI 平臺會使用下列 Azure 儲存體來建立應用程式-

- Azure 受控磁片

  它是由 Azure 管理的區塊層級儲存體磁片區。 當您在 Azure 虛擬機器上安裝時，可以使用適用于 SAP BOBI Platform 應用程式伺服器和資料庫的磁片。 有不同類型的 [Azure 受控磁碟](../../managed-disks-overview.md) 可供使用，但建議您針對 SAP BOBI Platform 應用程式和資料庫使用 [Premium ssd](../../disks-types.md#premium-ssd) 。

  在下列範例中，Premium Ssd 可用於 BOBI Platform 安裝目錄。 針對安裝在虛擬機器上的資料庫，您可以根據指導方針，將受控磁片用於資料和記錄磁片區。 CMS 和 Audit 資料庫通常很小，而且不會有與其他 SAP OLTP/OLAP 資料庫相同的儲存體效能需求。

- Azure Premium Files 或 Azure NetApp Files

  在 SAP BOBI 平臺中，檔案存放庫伺服器 (FRS) 指的是磁碟目錄，其中會儲存報表、universe 和連線等內容，而這些內容會由該系統的所有應用程式伺服器使用。 [Azure Premium files](../../../storage/files/storage-files-introduction.md) 或 [azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 儲存體可作為 SAP BOBI applications FRS 的共用檔案系統。 因為此儲存體供應專案不適用於所有區域，請參閱 [區域網站提供的產品](https://azure.microsoft.com/en-us/global-infrastructure/services/) ，以找出最新的資訊。

  如果您的區域中無法使用此服務，您可以建立 NFS 伺服器，讓您可以將檔案系統與 SAP BOBI 應用程式共用。 但您也必須考慮其高可用性。

![Azure 上的 SAP BusinessObjects BI 平臺儲存體版面配置](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>網路功能

SAP BOBI 是一種不保存任何商務資料的報告和分析 BI 平臺。 如此一來，系統就會從提取所有資料的地方連接到其他資料庫伺服器，並提供使用者見解。 Azure 提供網路基礎結構，可讓您使用 SAP BI 平臺（例如，連線到內部部署系統、不同虛擬網路中的系統和其他系統）來實現所有案例的對應。 如需詳細資訊，請檢查 [MICROSOFT AZURE SAP 工作負載的網路](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)功能。

針對資料庫即服務供應專案，任何新建立的資料庫 (Azure SQL Database 或適用於 MySQL 的 Azure 資料庫) 都會有封鎖所有外部連接的防火牆。 若要允許從 BI 平臺虛擬機器存取 DBaaS 服務，您必須指定一或多個伺服器層級防火牆規則，以啟用對 DBaaS 伺服器的存取。 如需詳細資訊，請參閱 Azure SQL Database 的適用於 MySQL 的 Azure 資料庫和[網路存取控制](../../../azure-sql/database/network-access-controls-overview.md)的[防火牆規則](../../../mysql/concepts-firewall-rules.md)一節。

## <a name="next-steps"></a>後續步驟

- [Linux 上的 SAP BusinessObjects BI 平臺部署](businessobjects-deployment-guide-linux.md)
- [適用於 SAP 的 Azure 虛擬機器規劃和實作](planning-guide.md)
- [適用於 SAP 的 Azure 虛擬機器部署](deployment-guide.md)
- [適用於 SAP 的 Azure 虛擬機器 DBMS 部署](dbms-guide.md)