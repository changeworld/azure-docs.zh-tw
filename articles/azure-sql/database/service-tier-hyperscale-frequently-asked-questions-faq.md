---
title: Azure SQL Database 超大規模常見問題
description: 客戶在超大規模服務層級 SQL Database （通常稱為超大規模資料庫）中詢問有關資料庫之常見問題的解答。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 4ea1982e7545f4ac39a5ecd15dc9e19a582ae31c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459626"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database 超大規模常見問題
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文針對考慮 Azure SQL Database 超大規模服務層中的資料庫的客戶，提供常見問題的解答，在此常見問題的其餘部分稱為超大規模。 本文說明超大規模支援的案例，以及與超大規模相容的功能。

- 此常見問題集適用於大致了解超大規模資料庫服務層級，而想就其特定的問題與顧慮尋求解答的讀者。
- 此常見問題並非 guidebook 或回答有關如何使用超大規模資料庫的問題。 如需超大規模的簡介，建議您參閱 [Azure SQL Database 超大規模](service-tier-hyperscale.md) 檔。

## <a name="general-questions"></a>一般問題

### <a name="what-is-a-hyperscale-database"></a>什麼是超大規模資料庫

超大規模資料庫是超大規模服務層中 SQL Database 的資料庫，由超大規模的向外延展儲存體技術所支援。 超大規模資料庫最多可支援 100 TB 的資料，並提供高輸送量和高效能，以及快速調整以因應工作負載需求的能力。 調整對應用程式而言是透明的（連線能力、查詢處理等），其運作方式與 Azure SQL Database 中的任何其他資料庫相同。

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>哪些資源類型和購買模型支援超大規模資料庫

超大規模資料庫服務層級僅適用於 Azure SQL Database 中的單一資料庫，且採用的必須是以虛擬核心為基礎的購買模型。  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>超大規模資料庫服務層級與一般用途和商務關鍵服務層級有何不同

以 vCore 為基礎的服務層級會根據資料庫可用性和儲存體類型、效能和大小上限來區別，如下表所述。

| | 資源類型 | 一般用途 |  超大規模資料庫 | 業務關鍵 |
|:---:|:---:|:---:|:---:|:---:|
| **適用對象** |全部|提供以預算為導向且平衡的計算與儲存體選項。|大部分的商業工作負載。 自動調整儲存體大小，最高可達 100 TB、快速垂直和水準計算規模調整、快速的資料庫還原。|具有高交易率和低 IO 延遲的 OLTP 應用程式。 使用多個同步更新的複本，針對失敗提供最高的復原能力，並快速進行容錯移轉|
|  **資源類型** ||SQL Database/SQL 受控執行個體 | 單一資料庫 | SQL Database/SQL 受控執行個體 |
| **計算大小**|SQL Database * | 1 到 80 個虛擬核心 | 1 到 80 個虛擬核心* | 1 到 80 個虛擬核心 |
| **計算大小**|SQL 受控執行個體 | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 | N/A | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 |
| **儲存體類型** | 全部 |進階遠端儲存體 (每個執行個體) | 與本機 SSD 快取分離的儲存體 (每個執行個體) | 超快速本機 SSD 儲存體 (每個執行個體) |
| **儲存體大小** | SQL Database *| 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| **儲存體大小** | SQL 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **IOPS** | 單一資料庫 | 每個虛擬核心 500 IOPS，且 IOPS 上限為 7000 | 超大規模是多層式架構，可在多個層級進行快取。 有效的 IOPS 將視工作負載而定。 | 5000 IOPS，IOPS 上限為 200,000|
| **IOPS** | SQL 受控執行個體 | 視檔案大小而定 | N/A | 1375 IOPS/vCore |
|**可用性**|全部|1個複本、無讀取相應放大、沒有本機快取 | 多個複本，最多4個讀取相應放大，部分本機快取 | 3個複本、1個讀取相應放大、區域冗余 HA、完整本機儲存體 |
|**備份**|全部|GRS，7-35 天保留 (預設為7天) | GRS，7天保留，常數時間點恢復 (PITR)  | GRS，7-35 天保留 (預設為7天)  |

\* 超大規模服務層級不支援彈性集區

### <a name="who-should-use-the-hyperscale-service-tier"></a>誰應使用超大規模資料庫服務層級

超大規模服務層級適用于擁有大型內部部署 SQL Server 資料庫的客戶，而且想要移至雲端來將其應用程式現代化，或是已使用 Azure SQL Database 並想要大幅擴充資料庫成長潛力的客戶。 超大規模資料庫也適用於想要兼顧高效能和高延展性的客戶。 使用超大規模資料庫，您將可：

- 最高達 100 TB 的資料庫大小
- 快速資料庫備份，不論資料庫大小 (備份都以儲存體快照集為基礎) 
- 無論資料庫大小為何，都能快速還原資料庫 (從儲存體快照集進行還原) 
- 記錄輸送量較高，而不考慮資料庫大小和虛擬核心數目
- 使用一或多個唯讀複本（用於讀取卸載和熱待命）來讀取相應放大。
- 快速相應增加計算 (依常數時間) 以充分因應大量工作負載中，然後再依常數時間相應減少。 例如，這類似于 P6 與 P11 之間的相應增加和減少，但速度更快，因為這不是資料作業的大小。

### <a name="what-regions-currently-support-hyperscale"></a>目前有哪些區域支援超大規模資料庫

超大規模服務層級目前適用于 [Azure SQL Database 超大規模總覽](service-tier-hyperscale.md#regions)下所列的區域。

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>我可以針對每部伺服器建立多個超大規模資料庫

是。 如需每一部伺服器的超大規模資料庫數目的詳細資訊和限制，請參閱 [SQL Database 伺服器上單一和集區資料庫的資源限制](resource-limits-logical-server.md)。

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>超大規模資料庫的效能特性有哪些

超大規模架構可提供高效能和輸送量，同時支援大型資料庫的大小。

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>超大規模資料庫有何延展性

超大規模可根據您的工作負載需求提供快速的擴充性。

- **相應增加/減少**

  您可以使用超大規模，以資源（例如 CPU 和記憶體）來擴大主要計算大小，然後以固定時間調整規模。 由於儲存體是共用的，因此相應增加和相應減少並非資料作業的大小。  
- **相應縮小/相應放大**

  透過超大規模，您也可以布建一或多個額外的計算複本，以便用來服務您的讀取要求。 這表示您可以使用這些額外的計算複本作為唯讀複本，將讀取工作負載從主要計算卸載。 除了唯讀之外，這些複本也可做為容錯移轉時的主要待命。

  布建每個額外的計算複本都可以在固定時間內完成，而且是一項線上作業。 您可以將 `ApplicationIntent` 連接字串上的引數設定為，以連接到這些額外的唯讀計算複本 `ReadOnly` 。 任何與 `ReadOnly` 應用程式意圖的連接都會自動路由傳送至其中一個額外的唯讀計算複本。

## <a name="deep-dive-questions"></a>深入問題

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>我可以在單一伺服器中混用超大規模與單一資料庫

是，您可以。

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>使用「超大規模資料庫」時是否需要變更應用程式設計模型

否，您的應用程式設計模型應保持原狀。 您可以像平常一樣使用您的連接字串，以及其他與超大規模資料庫互動的一般方式。

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>超大規模資料庫中的預設交易隔離等級為何

在主要複本上，預設的交易隔離等級為 RCSI (讀取認可快照隔離) 。 在讀取相應放大次要複本上，預設隔離等級為 Snapshot。

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>我可以將我的內部部署或 IaaS SQL Server 授權帶入超大規模

是，[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 適用於「超大規模資料庫」。 每個 SQL Server Standard 核心可對應至 1 個超大規模資料庫虛擬核心。 每個 SQL Server Enterprise 核心可對應至 4 個超大規模資料庫虛擬核心。 次要複本不需要 SQL 授權。 Azure Hybrid Benefit 價格將會自動套用到讀取相應放大 (次要) 複本。

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>超大規模設計的工作負載種類

超大規模支援所有 SQL Server 工作負載，但主要是針對 OLTP 優化。 您也可以將混合式 (HTAP) 和分析 (資料超市) 工作負載。

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>如何在 Azure Synapse Analytics 與 Azure SQL Database 超大規模之間進行選擇

如果您目前使用 SQL Server 作為資料倉儲來執行互動式分析查詢，超大規模是絕佳的選項，因為您可以將小型和中型的資料倉儲 (例如，以較低的成本) 高達 100 TB 的數 TB，且您可以將 SQL Server 資料倉儲工作負載遷移至超大規模，只需進行最少的 T-sql 程式碼變更。

如果您是以複雜的查詢和持續的擴大速率（大於 100 MB/s）來執行資料分析，或使用平行資料倉儲 (PDW) 、Teradata 或其他大量平行處理 (MPP) 資料倉儲，Azure Synapse Analytics 可能是最佳選擇。
  
## <a name="hyperscale-compute-questions"></a>超大規模計算問題

### <a name="can-i-pause-my-compute-at-any-time"></a>我是否可隨時暫停計算

但目前不能，不過您可以調整計算和複本數目，以降低非尖峰時間的成本。

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>我可以為記憶體密集型工作負載布建具有額外 RAM 的計算複本

否。 若要有更多的 RAM，您需要升級至更高的計算大小。 如需詳細資訊，請參閱[超大規模資料庫儲存體和計算大小](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)。

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>我可以布建不同大小的多個計算複本

否。

### <a name="how-many-read-scale-out-replicas-are-supported"></a>支援的讀取相應放大複本數目

超大規模資料庫會使用一個讀取相應放大複本來建立 (兩個複本，包括預設的主要) 。 您可以使用 [Azure 入口網站](https://portal.azure.com) 或 [REST API](/rest/api/sql/databases/createorupdate)，來調整0和4之間的唯讀複本數目。

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>為了達到高可用性，我需要布建額外的計算複本

在超大規模資料庫中，會在儲存層級提供資料復原。 您只需要一個複本來提供復原功能。 計算複本失效時，將會自動建立新複本，且不會遺失資料。

但如果只有一個複本，則在容錯移轉之後可能需要一些時間才能在新複本中建置本機快取。 在快取重建階段，資料庫會直接從頁面伺服器中提取資料，而導致儲存體延遲和效能降低的查詢效能。

針對需要高可用性的要徑任務應用程式，但影響最少的容錯移轉，您應該布建至少2個計算複本，包括主要計算複本。 這是預設組態。 如此一來，就可以使用熱待命複本作為容錯移轉目標。

## <a name="data-size-and-storage-questions"></a>資料大小和儲存體問題

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>超大規模支援的最大資料庫大小

100 TB。

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>「超大規模資料庫」的交易記錄大小為何

「超大規模資料庫」的交易記錄可說是無限制的。 您無須擔心記錄輸送量偏高的系統上是否會有記錄空間不足的問題。 不過，記錄產生速率可能會受到節流處理，以持續大量寫入工作負載。 尖峰持續的記錄產生速率是 100 MB/s。

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>`tempdb`當我的資料庫成長時，我的調整規模是否成長

您的 `tempdb` 資料庫位於本機 SSD 儲存體上，並依您布建的計算大小按比例調整大小。 您 `tempdb` 已經過優化，可提供最大的效能優勢。 `tempdb` 大小無法設定，並且會為您管理。

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>我的資料庫大小會自動成長，還是必須管理資料檔案的大小

您的資料庫大小會在您插入/擷取更多資料時自動成長。

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>超大規模支援或開頭的最小資料庫大小為何

40 GB。 建立超大規模資料庫的起始大小為 10 GB。 然後，它會每隔10分鐘開始增加 10 GB，直到達到 40 GB 的大小。 這兩個 10 GB 的 chucks 會在不同的頁面伺服器中配置，以提供更多 IOPS 和更高的 i/o 平行處理原則。 由於這項優化的原因，即使您選擇小於 40 GB 的初始資料庫大小，資料庫也會自動成長到至少 40 GB。

### <a name="in-what-increments-does-my-database-size-grow"></a>資料庫大小成長的遞增量為何

每個資料檔案都會成長 10 GB。 多個資料檔案可能會同時成長。

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>超大規模本機或遠端的儲存體

在「超大規模資料庫」中，資料檔案會儲存在 Azure 標準儲存體中。 資料會在本機 SSD 儲存體上，在接近計算複本的頁面伺服器上完整快取。 此外，計算複本在本機 SSD 和記憶體中都有資料快取，以降低從遠端頁面伺服器中提取資料的頻率。

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>我是否可管理或定義「超大規模資料庫」的檔案或檔案群組

否。 資料檔案會自動加入。 建立其他檔案群組的常見原因不適用於超大規模儲存體架構。

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>我是否可為資料庫佈建資料成長的強制上限

否。

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>如何使用超大規模來配置資料檔案

資料檔案是由頁面伺服器所控制，每個資料檔案都有一個頁面伺服器。 當資料大小增加時，就會加入資料檔案和相關聯的頁面伺服器。

### <a name="is-database-shrink-supported"></a>是否支援資料庫縮減

否。

### <a name="is-data-compression-supported"></a>支援資料壓縮

是，包括資料列、分頁和資料行存放區壓縮。

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>如果我有很大的資料表，資料表的資料是否會分散到多個資料檔案

是。 與指定的資料表相關聯的資料頁面可能會移至多個資料檔案中，但全都屬於相同的檔案群組。 SQL Server 使用 [比例填滿策略](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) ，將資料散發到資料檔案。

## <a name="data-migration-questions"></a>資料移轉問題

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>我可以將 Azure SQL Database 中的現有資料庫移至超大規模服務層級

是。 您可以將 Azure SQL Database 中的現有資料庫移至超大規模。 這是單向的遷移。 您無法將資料庫從超大規模資料庫移到另一個服務層級中。 針對概念證明 (Poc) ，建議您建立資料庫的複本，並將複本遷移至超大規模。 

將現有資料庫移至超大規模所需的時間包含複製資料的時間，以及在複製資料時重新執行源資料庫所做變更的時間。 資料複製時間與資料大小成正比。 如果在較低的寫入活動期間進行移動，則重新執行變更的時間將會較短。
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>我可以將超大規模資料庫移至其他服務層級

否。 目前，您無法將超大規模資料庫移至另一個服務層級。

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 超大規模尚不支援某些 Azure SQL Database 功能，包括但不限於長期備份保留。 在您將資料庫移轉至「超大規模資料庫」後，這些功能將會停止運作。  我們預期這些限制是暫時性的。

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>我可以將內部部署 SQL Server 資料庫或雲端虛擬機器中的 SQL Server 資料庫移至超大規模

是。 您可以使用所有現有的遷移技術來遷移至超大規模，包括異動複寫，以及任何其他資料移動技術 (大量複製、Azure Data Factory Azure Databricks、SSIS) 。 另請參閱 [Azure 資料庫移轉服務](../../dms/dms-overview.md)，其支援許多遷移案例。

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>從內部部署或虛擬機器環境遷移至超大規模時，我的停機時間為何，以及如何將它最小化

遷移至超大規模的停機時間與您將資料庫移轉至其他 Azure SQL Database 服務層級的停機時間相同。 移轉大小未超過 10 TB 的資料庫時，您可以使用[異動複寫](replication-to-sql-database.md#data-migration-scenario
)盡可能縮短停機時間。 針對非常大型的資料庫 (10 + TB) ，您可以考慮使用 ADF、Spark 或其他資料移動技術來遷移資料。

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>將 X 數量的資料帶入超大規模需要花費多少時間

超大規模能夠取用 100 MB/s 的新的/變更資料，但將資料移入 Azure SQL Database 中資料庫所需的時間也會受到可用的網路輸送量、來源讀取速度和目標資料庫服務等級目標的影響。

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>我可以從 blob 儲存體讀取資料，並進行快速載入 (例如 Azure Synapse Analytics 的 Polybase) 

您可以讓用戶端應用程式從 Azure 儲存體讀取資料，並將資料載入至超大規模資料庫 (就像您可以在 Azure SQL Database) 中的任何其他資料庫一樣。 Azure SQL Database 目前不支援 Polybase。 除了提供快速載入之外，您還可以使用[Azure Data Factory](../../data-factory/index.yml)，或使用 spark [connector for SQL](spark-connector.md) [Azure Databricks](/azure/azure-databricks/)中的 spark 作業。 SQL 的 Spark 連接器支援大量插入。

您也可以使用 BULK INSERT 或 OPENROWSET 從 Azure Blob 存放區大量讀取資料：大量 [存取 Azure Blob 儲存體中資料的範例](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)。

「超大規模資料庫」不支援簡單復原或大量記錄模式。 需要完整復原模式，才能提供高可用性和時間點復原。 不過，相較于其他 Azure SQL Database 服務層級，超大規模記錄架構可提供更佳的資料內嵌速度。

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>超大規模是否允許布建多個節點，以平行擷取大量資料

否。 超大規模是對稱式多重處理 (SMP) 架構，而不是大量平行處理 (MPP) 或多宿主架構。 您只能建立多個複本以相應放大唯讀工作負載。

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>最舊的 SQL Server 版本支援遷移至超大規模

SQL Server 2005。 如需詳細資訊，請參閱[移轉至單一資料庫或集區資料庫](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database)。 若要了解相容性問題，請參閱[解決資料庫移轉相容性問題](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)。

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>超大規模支援從其他資料來源（例如 Amazon Aurora、MySQL、于 postgresql、Oracle、DB2 和其他資料庫平臺）進行遷移

是。 [Azure 資料庫移轉服務](../../dms/dms-overview.md) 支援許多遷移案例。

## <a name="business-continuity-and-disaster-recovery-questions"></a>商務持續性和災害復原問題

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>針對超大規模資料庫提供哪些 Sla

請參閱 [Azure SQL Database 的 SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)。 額外的次要計算複本可提高可用性，最高可達99.99%，適用于具有兩個或多個次要計算複本的資料庫。

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>是由 Azure SQL Database 管理的資料庫備份

是。

### <a name="how-often-are-the-database-backups-taken"></a>資料庫備份多久執行一次

超大規模資料庫沒有傳統的完整、差異和記錄備份。 相反地，會有資料檔案的一般儲存體快照集。 所產生的記錄只會在設定的保留期限內依原樣保留，讓還原到保留期間內的任何時間點。

### <a name="does-hyperscale-support-point-in-time-restore"></a>超大規模支援時間點還原

是。

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>在超大規模中進行資料庫還原時，復原點目標 (RPO) /Recovery 時間目標 (RTO) 

RPO 為0分鐘。大部分的還原作業會在60分鐘內完成，不管資料庫大小為何。 針對較大的資料庫，還原時間可能會更長，且資料庫在還原時間點之前和最高都有重大的寫入活動。

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>資料庫備份是否會影響我的主要或次要複本上的計算效能

否。 備份由儲存子系統管理，並利用儲存體快照集。 它們不會影響使用者工作負載。

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>我可以使用超大規模資料庫執行異地還原

是。 完全支援異地還原。 不同于時間點還原，異地還原需要資料大小的作業。 資料檔案會以平行方式複製，因此此作業的持續時間主要取決於資料庫中最大檔案的大小，而不是資料庫總大小。 如果在與源資料庫的區域 [配對](../../best-practices-availability-paired-regions.md) 的 Azure 區域中還原資料庫，則異地還原時間會大幅縮短。

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>我可以使用超大規模資料庫設定異地複寫

目前沒有。

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>我可以採取超大規模資料庫備份，並將它還原到我的內部部署伺服器，或在 VM 的 SQL Server

否。 超大規模資料庫的儲存格式與 SQL Server 的任何發行版本不同，您無法控制備份或存取它們。 若要將您的資料移出超大規模資料庫，您可以使用任何資料移動技術（亦即 Azure Data Factory、Azure Databricks、SSIS 等）來將資料解壓縮。

## <a name="cross-feature-questions"></a>跨功能問題

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 超大規模不支援某些 Azure SQL Database 功能，包括但不限於長期備份保留。 在您將資料庫移轉至「超大規模資料庫」後，這些功能將會停止運作。

### <a name="will-polybase-work-with-hyperscale"></a>Polybase 是否可與超大規模搭配運作

否。 Azure SQL Database 不支援 Polybase。

### <a name="does-hyperscale-have-support-for-r-and-python"></a>超大規模是否支援 R 和 Python

目前沒有。

### <a name="are-compute-nodes-containerized"></a>是否為計算節點容器化

否。 超大規模處理常式會在 [Service Fabric](https://azure.microsoft.com/services/service-fabric/) 節點 (vm) ，而不是在容器中執行。

## <a name="performance-questions"></a>效能問題

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>我可以在超大規模資料庫中推送多少寫入輸送量

針對任何超大規模計算大小，交易記錄輸送量上限設為 100 MB/秒。 達到此速率的能力取決於多個因素，包括但不限於工作負載類型、用戶端設定，以及在主要計算複本上具有足夠的計算容量，以此費率產生記錄。

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>我可以在最大的計算上取得多少 IOPS

IOPS 和 IO 延遲會根據工作負載模式而有所不同。 如果要存取的資料快取在計算複本上，您會看到類似于本機 SSD 的 IO 效能。

### <a name="does-my-throughput-get-affected-by-backups"></a>備份是否會影響到輸送量

否。 計算會與儲存層分離。 這可消除備份的效能影響。

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>當我布建額外的計算複本時，我的輸送量會受到影響

由於儲存體是共用的，且主要與次要計算複本之間不會有直接的實體複寫，因此，主要複本上的輸送量不會因為新增次要複本而直接受到影響。 不過，我們可能會在主要複本上節流持續寫入的工作負載，以便在次要複本和頁面伺服器上套用記錄，以避免次要複本上的讀取效能不佳。

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>如何? 診斷超大規模資料庫中的效能問題並進行疑難排解

針對大部分的效能問題（特別是未以儲存體效能為基礎的問題），則適用常見的 SQL 診斷和疑難排解步驟。 針對超大規模特定的儲存體診斷，請參閱 [SQL 超大規模效能疑難排解診斷](hyperscale-performance-diagnostics.md)。

## <a name="scalability-questions"></a>延展性問題

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>擴大和縮小計算複本需要多久的時間

相應增加或減少計算通常需要最多2分鐘的時間，不論資料大小為何。

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>我的資料庫在相應增加/減少作業進行時是否會離線

否。 相應增加和相應減少會在線上執行。

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>進行調整作業時是否可能發生連線中斷

相應增加或減少會導致在調整作業結束時，現有的連接中斷。 新增次要複本並不會導致連接中斷。

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>計算複本是否自動向上和向下調整為自動或使用者觸發的作業

使用者。 並非自動的。  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>我的資料庫和 RBPEX 快取的大小 `tempdb` 也會隨著計算擴充

是。 `tempdb`當核心數目增加時，計算節點上的資料庫和[RBPEX](service-tier-hyperscale.md#distributed-functions-architecture)快取大小會自動擴大。

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>我可以布建多個主要計算複本，例如多宿主系統，其中多個主要計算標頭可驅動更高層級的平行存取

否。 只有主要計算複本會接受讀取/寫入要求。 次要計算複本只接受唯讀要求。

## <a name="read-scale-out-questions"></a>讀取相應放大問題

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>我可以布建多少次要計算複本

我們預設會為超大規模資料庫建立一個次要複本。 如果您想要調整複本數目，您可以使用 [Azure 入口網站](https://portal.azure.com) 或 [REST API](/rest/api/sql/databases/createorupdate)。

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>如何? 連接到這些次要計算複本

您可以將 `ApplicationIntent` 連接字串上的引數設定為，以連接到這些額外的唯讀計算複本 `ReadOnly` 。 任何標記為的連接 `ReadOnly` 都會自動路由傳送至其中一個額外的唯讀計算複本。 如需詳細資訊，請參閱 [使用唯讀複本來卸載唯讀查詢工作負載](read-scale-out.md)。

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>如何? 使用 SSMS 或其他用戶端工具來驗證是否已成功連線至次要計算複本？

您可以執行下列 T-SQL 查詢： `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` 。
`READ_ONLY`如果您連接到唯讀次要複本，而且 `READ_WRITE` 您已連接至主要複本，則結果為。 請注意，資料庫內容必須設定為超大規模資料庫的名稱，而不是設定為 `master` 資料庫。

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>我可以建立讀取相應放大複本的專用端點

否。 您只能藉由指定來連接到讀取相應放大複本 `ApplicationIntent=ReadOnly` 。

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>系統是否會對讀取工作負載進行智慧型負載平衡

否。 具有唯讀意圖的新連接會重新導向至任意的讀取相應放大複本。

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>我可以在主要複本之外，獨立擴大/縮小次要計算複本

否。 次要計算複本也會當做高可用性容錯移轉目標使用，因此它們必須具有與主要複本相同的設定，才能在容錯移轉後提供預期的效能。

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>我 `tempdb` 的主要計算和其他次要計算複本是否有不同的大小調整

否。 `tempdb`系統會根據計算大小布建來設定您的資料庫，您的次要計算複本大小與主要計算相同。

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>我可以在次要計算複本上新增索引和視圖

否。 超大規模資料庫具有共用儲存體，也就是說，所有計算複本都會看到相同的資料表、索引和 views。 如果您想要針對次要資料庫上的讀取優化其他索引，則必須將它們加入至主要複本上。

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>主要與次要計算複本之間有多少延遲

從交易在主資料庫上認可到可在次要上讀取的時間進行的資料延遲，取決於目前的記錄產生速率、交易大小、複本上的負載，以及其他因素。 小型交易的一般資料延遲時間是數十毫秒，不過資料延遲沒有上限。 給定次要複本上的資料一律是交易一致的。 不過，在指定的時間點，資料延遲可能會因不同的次要複本而不同。 需要立即讀取認可資料的工作負載應該在主要複本上執行。

## <a name="next-steps"></a>後續步驟

如需超大規模服務層級的詳細資訊，請參閱 [超大規模服務層級](service-tier-hyperscale.md)。