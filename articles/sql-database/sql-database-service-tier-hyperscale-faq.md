---
title: Azure SQL 資料庫超大規模常見問題解答
description: 回答客戶針對超大規模資料庫服務層級中的 Azure SQL 資料庫 (通常稱為「超大規模」資料庫) 經常提出的問題。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268622"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL 資料庫超大規模常見問題解答

本文為考慮 Azure SQL 資料庫超大規模服務層中的資料庫的客戶提供常見問題解答，在此常見問題解答的其餘部分中稱為 Hyperscale。 本文介紹了超大規模支援的方案和與超大規模相容的功能。

- 此常見問題集適用於大致了解超大規模資料庫服務層級，而想就其特定的問題與顧慮尋求解答的讀者。
- 此常見問題解答不是指南或回答有關如何使用超大規模資料庫的問題。 有關超量程的介紹，我們建議您參考[Azure SQL 資料庫超大規模](sql-database-service-tier-hyperscale.md)文檔。

## <a name="general-questions"></a>一般問題

### <a name="what-is-a-hyperscale-database"></a>什麼是超大規模資料庫

超大規模資料庫是超大規模資料庫服務層級之中採用超大規模資料庫相應放大儲存體技術的 Azure SQL 資料庫。 超大規模資料庫最多可支援 100 TB 的資料，並提供高輸送量和高效能，以及快速調整以因應工作負載需求的能力。 擴展對應用程式是透明的 ， 連接、查詢處理等與任何其他 Azure SQL 資料庫一樣工作。

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>哪些資源類型和購買模型支援超大規模資料庫

超大規模資料庫服務層級僅適用於 Azure SQL Database 中的單一資料庫，且採用的必須是以虛擬核心為基礎的購買模型。  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>超大規模資料庫服務層級與一般用途和商務關鍵服務層級有何不同

基於 vCore 的服務層根據資料庫可用性和存儲類型、性能和最大大小進行區分，如下表所述。

| | 資源類型 | 一般用途 |  超大規模資料庫 | 業務關鍵 |
|:---:|:---:|:---:|:---:|:---:|
| **適用對象** |全部|提供以預算為導向且平衡的計算與儲存體選項。|大部分的商業工作負載。 自動縮放存儲大小高達 100 TB，快速垂直和水準計算縮放，快速恢復資料庫。|具有高事務速率和低 IO 延遲的 OLTP 應用程式。 使用多個同步更新的副本提供對故障和快速容錯移轉的最高彈性。|
|  **資源類型** ||單一資料庫/彈性集區/受控執行個體 | 單一資料庫 | 單一資料庫/彈性集區/受控執行個體 |
| **計算大小**|單一資料庫/彈性集區 * | 1 到 80 個虛擬核心 | 1 到 80 個虛擬核心* | 1 到 80 個虛擬核心 |
| |受控執行個體 | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 | N/A | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 |
| **儲存體類型** | 全部 |進階遠端儲存體 (每個執行個體) | 與本機 SSD 快取分離的儲存體 (每個執行個體) | 超快速本機 SSD 儲存體 (每個執行個體) |
| **儲存體大小** | 單一資料庫/彈性集區 *| 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **IOPS** | 單一資料庫 | 每個虛擬核心 500 IOPS，且 IOPS 上限為 7000 | 超大規模是一種多層架構，具有多個級別的緩存。 有效的 IOPS 將取決於工作負載。 | 5000 IOPS，IOPS 上限為 200,000|
| | 受控執行個體 | 取決於檔案大小 | N/A | 1375 IOPS/vCore |
|**可用性**|全部|1 個副本，沒有讀取橫向擴展，沒有本機快取 | 多個副本，最多 4 個讀取橫向擴展，部分本機快取 | 3 個副本，1 個讀取橫向擴展，區域冗余 HA，完全本機存放區 |
|**備份**|全部|RA-GRS，7-35 天保留期（預設為 7 天）| RA-GRS，7 天保留，恒定時間點恢復 （PITR） | RA-GRS，7-35 天保留期（預設為 7 天） |

\*超大規模服務層不支援彈性池

### <a name="who-should-use-the-hyperscale-service-tier"></a>誰應使用超大規模資料庫服務層級

Hyperscale 服務層適用于具有大型本地 SQL Server 資料庫並希望通過遷移到雲來實現應用程式現代化的客戶，或適用于已在使用 Azure SQL 資料庫並希望顯著擴展資料庫增長的潛力。 超大規模資料庫也適用於想要兼顧高效能和高延展性的客戶。 使用超大規模資料庫，您將可：

- 資料庫大小高達 100 TB
- 無論資料庫大小如何，快速備份資料庫（備份基於存儲快照）
- 快速資料庫還原，無論資料庫大小如何（還原來自存儲快照）
- 無論資料庫大小和 vCore 的數量如何，日誌輸送量都更高
- 使用一個或多個唯讀副本讀取橫向擴展，用於讀取卸載和作為熱備用副本。
- 快速相應增加計算 (依常數時間) 以充分因應大量工作負載中，然後再依常數時間相應減少。 例如，這類似于在 P6 和 P11 之間上下擴展，但速度要快得多，因為這不是資料操作的大小。

### <a name="what-regions-currently-support-hyperscale"></a>目前有哪些區域支援超大規模資料庫

超大規模服務層目前在[Azure SQL 資料庫超大規模概述](sql-database-service-tier-hyperscale.md#regions)下列出的區域中可用。

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>是否可為每個邏輯伺服器建立多個超大規模資料庫

是。 如需每個邏輯伺服器的超大規模資料庫數目限制的詳細資訊，請參閱[適用於邏輯伺服器上之單一和集區資料庫的 SQL Database 資源限制](sql-database-resource-limits-logical-server.md)。

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>超大規模資料庫的性能特徵是什麼？

超大規模體系結構提供高性能和輸送量，同時支援大型資料庫大小。 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>超大規模資料庫有何延展性

超大規模可根據您的工作負載需求提供快速可擴充性。

- **相應增加/減少**

  使用 Hyperscale，您可以根據 CPU 和記憶體等資源來擴展主計算大小，然後在恒定時間內縮小規模。 由於儲存體是共用的，因此相應增加和相應減少並非資料作業的大小。  
- **相應縮小/相應放大**

  使用 Hyperscale，您還可以預配一個或多個可用於為讀取請求提供服務的其他計算副本。 這意味著您可以將這些額外的計算副本用作唯讀副本，以便從主計算中卸載讀取工作負荷。 除了唯讀之外，這些副本還充當主副本容錯移轉時的熱備用副本。

  每個附加計算副本的預配都可以在恒定的時間內完成，並且是線上作業。 通過將連接字串上的`ApplicationIntent`參數設置為，可以連接到這些額外的唯讀計算副本。 `ReadOnly` 具有`ReadOnly`應用程式意圖的任何連接都將自動路由到其他唯讀計算副本之一。

## <a name="deep-dive-questions"></a>深度潛水問題

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>我可以將超大規模和單個資料庫混合到單個邏輯伺服器中嗎？

 是，您可以這麼做。

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>使用「超大規模資料庫」時是否需要變更應用程式設計模型

否，您的應用程式設計模型應保持原狀。 您可以像往常一樣使用連接字串和其他常規方式與超大規模資料庫進行交互。

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>超大規模資料庫中的預設值是交易隔離等級

在主副本上，預設交易隔離等級為 RCSI（讀取已提交快照隔離）。 在"讀取橫向擴展"輔助副本上，預設隔離等級為快照。

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>我可以將本地或 IaaS SQL 伺服器許可證帶到超大規模嗎？

是，[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 適用於「超大規模資料庫」。 每個 SQL Server Standard 核心可對應至 1 個超大規模資料庫虛擬核心。 每個 SQL Server Enterprise 核心可對應至 4 個超大規模資料庫虛擬核心。 次要複本不需要 SQL 授權。 Azure 混合權益價格將自動應用於讀取橫向擴展（輔助）副本。

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Hyperscale 專為哪種工作負載而設計

超大規模支援所有 SQL Server 工作負載，但它主要針對 OLTP 進行了優化。 您還可以帶來混合 （HTAP） 和分析（資料集市）工作負載。

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>如何在 Azure SQL 資料倉儲和 Azure SQL 資料庫超大規模之間進行選擇

如果您當前使用 SQL Server 作為資料倉儲運行互動式分析查詢，Hyperscale 是一個不錯的選擇，因為您可以以更低的成本託管中小型資料倉儲（例如幾 TB 高達 100 TB），並且可以遷移 SQL Server 資料以最少的 T-SQL 代碼更改將工作負載倉庫到超大規模。

如果您大規模地運行資料分析，查詢複雜，持續引入率高於 100 MB/s，或者使用並行資料倉儲 （PDW）、Teradata 或其他大規模並行處理 （MPP） 資料倉儲，SQL 資料倉儲可能是最佳選擇。
  
## <a name="hyperscale-compute-questions"></a>超大規模計算問題

### <a name="can-i-pause-my-compute-at-any-time"></a>我是否可隨時暫停計算

但是，此時無法擴展計算和副本數量，以降低非尖峰時間的成本。

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>是否可以為記憶體密集型工作負載預配具有額外 RAM 的計算副本

否。 若要有更多的 RAM，您需要升級至更高的計算大小。 如需詳細資訊，請參閱[超大規模資料庫儲存體和計算大小](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)。

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>我可以預配多個不同大小的計算副本嗎？

否。

### <a name="how-many-read-scale-out-replicas-are-supported"></a>支援多少讀取橫向擴展副本

預設情況下，使用一個讀取橫向擴展副本（包括主副本）創建超縮放資料庫。 您可以使用[Azure 門戶](https://portal.azure.com)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)縮放 0 到 4 之間的唯讀副本數。

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>為獲得高可用性，是否需要預配其他計算副本

在超大規模資料庫中，資料恢復能力在存儲級別提供。 您只需要一個副本來提供恢復能力。 計算複本失效時，將會自動建立新複本，且不會遺失資料。

但如果只有一個複本，則在容錯移轉之後可能需要一些時間才能在新複本中建置本機快取。 在緩存重建階段，資料庫直接從頁面伺服器提取資料，從而導致更高的存儲延遲和降低查詢性能。

對於需要高可用性且容錯移轉影響最小的任務關鍵型應用，應預配至少 2 個計算副本，包括主計算副本。 這是預設組態。 這樣，就有一個熱備用副本，用作容錯移轉目標。

## <a name="data-size-and-storage-questions"></a>資料大小和存儲問題

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>超大規模支援的最大資料庫大小是多少？

100 TB。

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>「超大規模資料庫」的交易記錄大小為何

「超大規模資料庫」的交易記錄可說是無限制的。 您無須擔心記錄輸送量偏高的系統上是否會有記錄空間不足的問題。 但是，日誌生成速率可能會限制連續積極寫入工作負載。 峰值持續日誌生成速率為 100 MB/s。

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>我的`tempdb`擴展是否隨著資料庫的增長而擴大

資料庫`tempdb`位於本地 SSD 存儲上，大小與預配的計算大小成比例。 優化`tempdb`了您的性能優勢。 `tempdb`大小不可配置，並為您管理。

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>我的資料庫大小是自動增長，還是必須管理資料檔案的大小

您的資料庫大小會在您插入/擷取更多資料時自動成長。

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Hyperscale 支援或啟動的最小資料庫大小是什麼？

40 GB。 創建起始大小為 10 GB 的超大規模資料庫。 然後，它開始每 10 分鐘增加 10 GB，直到達到 40 GB 的大小。 這 10 GB 夾頭中的每一個都分配在不同的頁面伺服器中，以便提供更多 IOPS 和更高的 I/O 並行性。 由於這種優化，即使您選擇的初始資料庫大小小於 40 GB，資料庫將自動增長到至少 40 GB。

### <a name="in-what-increments-does-my-database-size-grow"></a>資料庫大小成長的遞增量為何

每個資料檔案增加 10 GB。 多個資料檔案可能會同時增長。

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>存儲在超大規模本地或遠端

在「超大規模資料庫」中，資料檔案會儲存在 Azure 標準儲存體中。 資料完全緩存在本地 SSD 存儲上，位於靠近計算副本的頁面伺服器上。 此外，計算副本在本地 SSD 和記憶體中具有資料緩存，以減少從遠端頁面伺服器獲取資料的頻率。

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>我是否可管理或定義「超大規模資料庫」的檔案或檔案群組

否。 資料檔案將自動添加。 創建其他檔組的常見原因不適用於超大規模存儲體系結構。

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>我是否可為資料庫佈建資料成長的強制上限

否。

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>如何使用超大規模佈局資料檔案

資料檔案由頁面伺服器控制，每個資料檔案一個頁面伺服器。 隨著資料大小的增加，將添加資料檔案和相關頁面伺服器。

### <a name="is-database-shrink-supported"></a>是否支援資料庫縮減

否。

### <a name="is-data-compression-supported"></a>是否支援資料壓縮

是，包括行、頁和列存儲壓縮。

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>如果我有很大的資料表，資料表的資料是否會分散到多個資料檔案

是。 與指定的資料表相關聯的資料頁面可能會移至多個資料檔案中，但全都屬於相同的檔案群組。 SQL Server 使用[比例填充策略](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy)將資料分發到資料檔案上。

## <a name="data-migration-questions"></a>資料移轉問題

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>是否可將現有的 Azure SQL 資料庫移至超大規模資料庫服務層級

是。 您可以將現有的 Azure SQL 資料庫移至「超大規模資料庫」。 這是單向遷移。 您無法將資料庫從超大規模資料庫移到另一個服務層級中。 對於概念證明 （POC），我們建議您創建資料庫的副本並將副本遷移到 Hyperscale。
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>我可以將超大規模資料庫移動到其他服務層嗎？

否。 此時，無法將超大規模資料庫移動到其他服務層。

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 Hyperscale 中還不支援某些 Azure SQL 資料庫功能，包括但不限於長期備份保留。 在您將資料庫移轉至「超大規模資料庫」後，這些功能將會停止運作。  我們預計這些限制是暫時的。

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>是否可以將本地 SQL Server 資料庫或雲虛擬機器中的 SQL Server 資料庫移動到超大規模

是。 您可以使用所有現有的遷移技術遷移到 Hyperscale，包括異動複寫和任何其他資料移動技術（批量複製、Azure 資料工廠、Azure 資料塊、SSIS）。 另請參閱[Azure 資料庫移轉服務](../dms/dms-overview.md)，該服務支援許多遷移方案。

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>在從本地或虛擬機器環境遷移到 Hyperscale 期間，我的停機時間是什麼，以及如何將其最小化

遷移到 Hyperscale 的停機時間與將資料庫移轉到其他 Azure SQL 資料庫服務層時的停機時間相同。 移轉大小未超過 10 TB 的資料庫時，您可以使用[異動複寫](replication-to-sql-database.md#data-migration-scenario
)盡可能縮短停機時間。 對於非常大的資料庫（10+ TB），您可以考慮使用 ADF、Spark 或其他資料移動技術遷移資料。

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>將 X 量資料引入超大規模需要多少時間

Hyperscale 能夠消耗 100 MB/s 的新/更改資料，但將資料移動到 Azure SQL 資料庫所需的時間也受可用網路輸送量、源讀取速度和目標資料庫服務等級目標的影響。

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>是否可以從 Blob 存儲中讀取資料並執行快速載入（如 SQL 資料倉儲中的 Polybase）

可以讓用戶端應用程式從 Azure 存儲讀取資料，並將資料載入載入到超大規模資料庫中（就像使用任何其他 Azure SQL 資料庫一樣）。 Azure SQL 資料庫中當前不支援聚基。 作為提供快速載入的替代方法，可以使用 Azure[資料工廠](https://docs.microsoft.com/azure/data-factory/)，或者在[Azure 資料塊](https://docs.microsoft.com/azure/azure-databricks/)中使用 Spark 作業和[SQL 的 Spark 連接器](sql-database-spark-connector.md)。 SQL 的 Spark 連接器支援大量插入。

還可以使用 BULK INSERT 或 OPENROWSET：Azure [Blob 存儲中批量訪問資料的示例](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)，從 Azure Blob 存儲中批量讀取資料。

「超大規模資料庫」不支援簡單復原或大量記錄模式。 需要完全恢復模型來提供高可用性和時間點恢復。 但是，與其他 Azure SQL 資料庫服務層相比，超大規模日誌體系結構提供更好的資料引入速率。

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>超大規模是否允許預配多個節點以並行引入大量資料

否。 Hyperscale 是一種對稱多處理 （SMP） 體系結構，不是大規模並行處理 （MPP） 或多主機體系結構。 您只能建立多個複本以相應放大唯讀工作負載。

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>遷移到超大規模時支援的最早 SQL Server 版本是什麼？

SQL Server 2005。 如需詳細資訊，請參閱[移轉至單一資料庫或集區資料庫](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)。 若要了解相容性問題，請參閱[解決資料庫移轉相容性問題](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)。

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>超大規模是否支援從其他資料來源（如 Amazon Aurora、MySQL、PostgreSQL、Oracle、DB2 和其他資料庫平臺）進行遷移

是。 [Azure 資料庫移轉服務](../dms/dms-overview.md)支援許多遷移方案。

## <a name="business-continuity-and-disaster-recovery-questions"></a>業務連續性和災害復原問題

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>為超大規模資料庫提供 SL

[有關 Azure SQL 資料庫，請參閱 SLA。](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) 對於具有兩個或多個輔助計算副本的資料庫，其他輔助計算副本可提高可用性，高達 99.99%。

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL Database 服務是否會為我管理資料庫備份

是。

### <a name="how-often-are-the-database-backups-taken"></a>資料庫備份多久執行一次

超大規模資料庫沒有傳統的完整、差異和記錄備份。 相反，有資料檔案的常規存儲快照。 生成的日誌只需在配置的保留期內按原樣保留，允許在保留期內還原到任何時間點。

### <a name="does-hyperscale-support-point-in-time-restore"></a>超大規模支援點是否及時恢復

是。

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>超大規模中資料庫還原的復原點目標 （RPO）/恢復時間目標 （RTO） 是什麼

RPO 為 0 分鐘。RTO 目標不到 10 分鐘，無論資料庫大小如何。 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>資料庫備份是否會影響主副本或輔助副本的計算性能

否。 備份由儲存子系統管理，並利用存儲快照。 它們不會影響使用者工作負載。

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>是否可以使用超大規模資料庫執行地理還原

是。 完全支援地理還原。 與時間點還原不同，地理還原需要資料大小操作。 資料檔案是並行複製的，因此此操作的持續時間主要取決於資料庫中最大檔的大小，而不是資料庫的總大小。 如果在 Azure 區域中還原資料庫，與源資料庫的區域[配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)，則異地還原時間將大大縮短。

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>是否可以使用超大規模資料庫設置異地複製

目前沒有。

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>是否可以進行超大規模資料庫備份並將其還原到本機伺服器或 VM 中的 SQL Server 上

否。 超大規模資料庫的存儲格式不同于任何發佈的 SQL Server 版本，您無法控制備份或有權訪問備份。 要將資料從超大規模資料庫中取出，可以使用任何資料移動技術（即 Azure 資料工廠、Azure 資料塊、SSIS 等）提取資料。

## <a name="cross-feature-questions"></a>交叉功能問題

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 超大規模不支援某些 Azure SQL 資料庫功能，包括但不限於長期備份保留。 在您將資料庫移轉至「超大規模資料庫」後，這些功能將會停止運作。

### <a name="will-polybase-work-with-hyperscale"></a>聚基將配合超大規模

否。 Azure SQL 資料庫中不支援多基。

### <a name="does-hyperscale-have-support-for-r-and-python"></a>超大規模是否支援 R 和 Python

目前沒有。

### <a name="are-compute-nodes-containerized"></a>正在容器化計算節點

否。 超大規模進程在[服務結構](https://azure.microsoft.com/services/service-fabric/)節點 （VM） 上運行，而不是在容器中運行。

## <a name="performance-questions"></a>性能問題

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>我可以在超大規模資料庫中推送多少寫入輸送量

對於任何超大規模計算大小，事務日誌輸送量上限設置為 100 MB/s。 實現此速率的能力取決於多種因素，包括但不限於工作負載類型、用戶端配置，以及主計算副本上有足夠的計算容量以此速率生成日誌。

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>在最大的計算上，我有多少 IOPS

IOPS 和 IO 延遲將因工作負載模式而異。 如果正在訪問的資料緩存在計算副本上，您將看到與本地 SSD 類似的 IO 性能。

### <a name="does-my-throughput-get-affected-by-backups"></a>備份是否會影響到輸送量

否。 計算與存儲層分離。 這消除了備份的性能影響。

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>在預配其他計算副本時，我的輸送量是否受到影響

由於存儲是共用的，並且主計算副本和輔助計算副本之間沒有直接的物理複製，因此主副本上的輸送量不會因添加輔助副本而直接影響。 但是，我們可能會限制主副本上連續主動寫入工作負載，以允許日誌應用於輔助副本和頁面伺服器上以趕上，以避免輔助副本的讀取性能差。

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>如何診斷和解決超大規模資料庫中的性能問題

對於大多數性能問題（尤其是未植根于存儲性能的問題），應用常見的 SQL Server 診斷和故障排除步驟。 有關超大規模特定的存儲診斷，請參閱[SQL 超大規模性能故障排除診斷](sql-database-hyperscale-performance-diagnostics.md)。

## <a name="scalability-questions"></a>可擴充性問題

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>上下擴展計算副本需要多長時間

無論資料大小如何，向上或向下擴展計算都可能需要 5-10 分鐘。

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>我的資料庫在相應增加/減少作業進行時是否會離線

否。 相應增加和相應減少會在線上執行。

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>進行調整作業時是否可能發生連線中斷

向上或向下擴展會導致在縮放操作結束時發生容錯移轉時刪除現有連接。 添加輔助副本不會導致連接丟棄。

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>是計算副本的向上和向下縮放自動操作還是最終使用者觸發的操作

使用者。 並非自動的。  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>隨著計算的放大，`tempdb`資料庫的大小是否也會增加

是。 隨著`tempdb`計算的增長，資料庫將自動擴展。  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>是否可以預配多個主計算副本（如多主系統），其中多個主計算頭可以驅動更高級別的併發性

否。 只有主計算副本接受讀/寫請求。 輔助計算副本僅接受唯讀請求。

## <a name="read-scale-out-questions"></a>讀取橫向擴展問題

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>我可以預配多少輔助計算副本

預設情況下，我們為超大規模資料庫創建一個輔助副本。 如果要調整副本的數量，可以使用[Azure 門戶](https://portal.azure.com)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)執行此操作。

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>如何連接到這些輔助計算副本

通過將連接字串上的`ApplicationIntent`參數設置為，可以連接到這些額外的唯讀計算副本。 `ReadOnly` 標記的任何`ReadOnly`連接都將自動路由到其他唯讀計算副本之一。  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>如何驗證是否已成功連接到使用 SSMS 或其他用戶端工具的輔助計算副本？

您可以執行以下 T-SQL 查詢： `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`。
結果是`READ_ONLY`，如果您連接到唯讀輔助副本，並且`READ_WRITE`是否連接到主副本。 請注意，資料庫上下文必須設置為超大規模資料庫的名稱，`master`而不是資料庫。

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>是否可以為讀取橫向擴展複本建立專用終結點

否。 只能通過指定`ApplicationIntent=ReadOnly`連接到讀取橫向擴展副本。

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>系統是否會對讀取工作負載進行智慧型負載平衡

否。 具有唯讀意圖的新連接將重定向到任意的讀取橫向擴展副本。

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>是否可以獨立于主副本向上/向下縮放輔助計算副本

否。 輔助計算副本也用作高可用性容錯移轉目標，因此它們需要具有與主副本相同的配置，以便在容錯移轉後提供預期性能。

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>我的主要計算和其他`tempdb`輔助計算副本的尺寸不同嗎？

否。 資料庫`tempdb`根據計算大小預配進行配置，輔助計算副本的大小與主計算相同。

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>是否可以在輔助計算副本上添加索引和視圖

否。 超大規模資料庫具有共用存儲，這意味著所有計算副本都看到相同的表、索引和視圖。 如果希望針對輔助索引的讀取優化其他索引，則必須在主索引上添加它們。

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>主計算副本和輔助計算副本之間會有多少延遲

從在主資料庫上提交事務到在次要資料庫上可見的時間，資料延遲取決於當前的日誌生成速率。 典型的資料延遲在低毫秒內。

## <a name="next-steps"></a>後續步驟

有關超大規模服務層的詳細資訊，請參閱[超大規模服務層](sql-database-service-tier-hyperscale.md)。
