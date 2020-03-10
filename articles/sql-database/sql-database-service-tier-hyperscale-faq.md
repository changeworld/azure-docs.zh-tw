---
title: Azure SQL Database 超大規模資料庫常見問題
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359838"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database 超大規模資料庫常見問題

這篇文章針對考慮在 Azure SQL Database 超大規模資料庫服務層級中之資料庫的客戶，提供常見問題的解答，這只是本常見問題其餘部分所提及的超大規模資料庫。 本文說明超大規模資料庫支援的案例，以及與超大規模資料庫相容的功能。

- 此常見問題集適用於大致了解超大規模資料庫服務層級，而想就其特定的問題與顧慮尋求解答的讀者。
- 此常見問題不是 guidebook 或回答有關如何使用超大規模資料庫資料庫的問題。 如需超大規模資料庫的簡介，建議您參閱[Azure SQL Database 超大規模資料庫](sql-database-service-tier-hyperscale.md)檔。

## <a name="general-questions"></a>一般問題

### <a name="what-is-a-hyperscale-database"></a>什麼是超大規模資料庫

超大規模資料庫是超大規模資料庫服務層級之中採用超大規模資料庫相應放大儲存體技術的 Azure SQL 資料庫。 超大規模資料庫最多可支援 100 TB 的資料，並提供高輸送量和高效能，以及快速調整以因應工作負載需求的能力。 調整對應用程式而言是透明的–連線能力、查詢處理等工作，與任何其他 Azure SQL 資料庫一樣。

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>哪些資源類型和購買模型支援超大規模資料庫

超大規模資料庫服務層級僅適用於 Azure SQL Database 中的單一資料庫，且採用的必須是以虛擬核心為基礎的購買模型。  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>超大規模資料庫服務層級與一般用途和商務關鍵服務層級有何不同

以 vCore 為基礎的服務層級會根據資料庫可用性和儲存體類型、效能和最大大小來區分，如下表所述。

| | 資源類型 | 一般用途 |  超大規模資料庫 | 業務關鍵 |
|:---:|:---:|:---:|:---:|:---:|
| **適用對象** |全部|提供以預算為導向且平衡的計算與儲存體選項。|大部分的商業工作負載。 自動調整儲存體大小，最高可達 100 TB，快速垂直和水準計算縮放，快速資料庫還原。|具有高交易率和低 IO 延遲的 OLTP 應用程式。 為失敗提供最高的復原能力，並使用多個同步更新的複本快速容錯移轉。|
|  **資源類型** ||單一資料庫/彈性集區/受控執行個體 | 單一資料庫 | 單一資料庫/彈性集區/受控執行個體 |
| **計算大小**|單一資料庫/彈性集區 * | 1 到 80 個虛擬核心 | 1 到 80 個虛擬核心* | 1 到 80 個虛擬核心 |
| |受控執行個體 | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 | N/A | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 |
| **儲存體類型** | 全部 |進階遠端儲存體 (每個執行個體) | 與本機 SSD 快取分離的儲存體 (每個執行個體) | 超快速本機 SSD 儲存體 (每個執行個體) |
| **儲存體大小** | 單一資料庫/彈性集區 *| 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **IOPS** | 單一資料庫 | 每個虛擬核心 500 IOPS，且 IOPS 上限為 7000 | 超大規模資料庫是多層式架構，在多個層級進行快取。 有效的 IOPS 將視工作負載而定。 | 5000 IOPS，IOPS 上限為 200,000|
| | 受控執行個體 | 視檔案大小而定 | N/A | 1375 IOPS/vCore |
|**可用性**|全部|1個複本，沒有讀取相應放大，沒有本機快取 | 多個複本，最多4個讀取相應放大，部分本機快取 | 3個複本，1個讀取相應放大，區域-多餘 HA，完整本機儲存體 |
|**備份**|全部|RA-GRS，7-35 天保留（預設為7天）| RA-GRS，7天保留期，固定時間點恢復（PITR） | RA-GRS，7-35 天保留（預設為7天） |

超大規模資料庫服務層級中不支援 \* 彈性集區

### <a name="who-should-use-the-hyperscale-service-tier"></a>誰應使用超大規模資料庫服務層級

超大規模資料庫服務層級適用于擁有大型內部部署 SQL Server 資料庫，而且想要移至雲端或已使用 Azure SQL Database 的客戶現代化其應用程式的客戶，而且想要大幅擴充資料庫成長的可能性。 超大規模資料庫也適用於想要兼顧高效能和高延展性的客戶。 使用超大規模資料庫，您將可：

- 資料庫大小上限為 100 TB
- 快速資料庫備份（不論資料庫大小為何）（備份是以儲存體快照集為基礎）
- 快速資料庫還原，不論資料庫大小（從儲存體快照集還原）
- 記錄輸送量較高，不論資料庫大小和虛擬核心數目
- 使用一或多個唯讀複本讀取相應放大，用於讀取卸載和作為熱待命。
- 快速相應增加計算 (依常數時間) 以充分因應大量工作負載中，然後再依常數時間相應減少。 這類似于 P6 和 P11 之間的相應增加和減少，例如，但速度快很多，因為這不是資料作業的大小。

### <a name="what-regions-currently-support-hyperscale"></a>目前有哪些區域支援超大規模資料庫

超大規模資料庫服務層級目前可用於 [ [Azure SQL Database 超大規模資料庫總覽](sql-database-service-tier-hyperscale.md#regions)] 底下所列的區域。

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>是否可為每個邏輯伺服器建立多個超大規模資料庫

是。 如需每個邏輯伺服器的超大規模資料庫數目限制的詳細資訊，請參閱[適用於邏輯伺服器上之單一和集區資料庫的 SQL Database 資源限制](sql-database-resource-limits-logical-server.md)。

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>超大規模資料庫資料庫的效能特性為何

超大規模資料庫架構可提供高效能和輸送量，同時支援大型資料庫大小。 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>超大規模資料庫有何延展性

超大規模資料庫會根據您的工作負載需求提供快速的擴充性。

- **相應增加/減少**

  透過超大規模資料庫，您可以根據 CPU 和記憶體之類的資源來相應增加主要計算大小，然後再以固定時間相應減少規模。 由於儲存體是共用的，因此相應增加和相應減少並非資料作業的大小。  
- **相應縮小/相應放大**

  透過超大規模資料庫，您也可以布建一或多個額外的計算複本，以便用來為您的讀取要求提供服務。 這表示您可以使用這些額外的計算複本做為唯讀複本，以從主要計算中卸載讀取工作負載。 除了唯讀以外，這些複本也會做為從主要容錯移轉時的熱待命。

  這些額外計算複本的布建可在固定時間內完成，而且是一項線上作業。 您可以將連接字串上的 `ApplicationIntent` 引數設定為 `ReadOnly`，以連線至這些額外的唯讀計算複本。 任何與 `ReadOnly` 應用程式意圖的連線都會自動路由至其中一個額外的唯讀計算複本。

## <a name="deep-dive-questions"></a>深入探討問題

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>我可以在單一邏輯伺服器中混用超大規模資料庫與單一資料庫

是，您可以這麼做。

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>使用「超大規模資料庫」時是否需要變更應用程式設計模型

否，您的應用程式設計模型應保持原狀。 您可以像平常一樣使用您的連接字串，以及其他與超大規模資料庫資料庫互動的一般方式。

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>超大規模資料庫資料庫中的預設交易隔離等級為何

在主要複本上，預設交易隔離等級為 RCSI （讀取認可快照集隔離）。 在讀取相應放大次要複本上，預設隔離等級為 Snapshot。

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>我可以將內部部署或 IaaS SQL Server 授權帶入超大規模資料庫

是，[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 適用於「超大規模資料庫」。 每個 SQL Server Standard 核心可對應至 1 個超大規模資料庫虛擬核心。 每個 SQL Server Enterprise 核心可對應至 4 個超大規模資料庫虛擬核心。 次要複本不需要 SQL 授權。 Azure Hybrid Benefit 價格會自動套用至讀取相應放大（次要）複本。

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>超大規模資料庫設計的工作負載種類

超大規模資料庫支援所有 SQL Server 的工作負載，但它主要是針對 OLTP 優化。 您也可以帶入混合式（HTAP）和分析（資料超市）工作負載。

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>如何在 Azure SQL 資料倉儲和 Azure SQL Database 超大規模資料庫之間做選擇

如果您目前使用 SQL Server 做為資料倉儲來執行互動式分析查詢，超大規模資料庫是很好的選擇，因為您可以用較低的成本裝載小型和中等大小的資料倉儲（例如數 TB 到 100 TB），而且您可以遷移 SQL Server 資料 war以最少的 T-sql 程式碼變更，將工作負載 ehouse 至超大規模資料庫。

如果您使用複雜查詢的大規模執行資料分析，且持續的內階速率高於 100 MB/s，或使用平行資料倉儲（PDW）、Teradata 或其他大量平行處理（MPP）資料倉儲，則 SQL 資料倉儲可能是最佳選擇。
  
## <a name="hyperscale-compute-questions"></a>超大規模資料庫計算問題

### <a name="can-i-pause-my-compute-at-any-time"></a>我是否可隨時暫停計算

不過，此時您可以相應縮小計算和數目的複本，以降低非尖峰時間的成本。

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>我可以為需要大量記憶體的工作負載布建具有額外 RAM 的計算複本

否。 若要有更多的 RAM，您需要升級至更高的計算大小。 如需詳細資訊，請參閱[超大規模資料庫儲存體和計算大小](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)。

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>我可以布建不同大小的多個計算複本

否。

### <a name="how-many-read-scale-out-replicas-are-supported"></a>支援的讀取相應放大複本數目

預設會使用一個讀取相應放大複本（兩個複本，包括主要）來建立超大規模資料庫資料庫。 您可以使用[Azure 入口網站](https://portal.azure.com)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)，來調整0到4之間的唯讀複本數目。

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>為了達到高可用性，我需要布建額外的計算複本

在超大規模資料庫資料庫中，會在儲存體層級提供資料復原。 您只需要一個複本來提供復原功能。 計算複本失效時，將會自動建立新複本，且不會遺失資料。

但如果只有一個複本，則在容錯移轉之後可能需要一些時間才能在新複本中建置本機快取。 在快取重建階段，資料庫會直接從頁面伺服器提取資料，因而導致儲存延遲較高，並降低查詢效能。

對於需要高可用性且最少容錯移轉影響的任務關鍵性應用程式，您應該布建至少2個計算複本，包括主要計算複本。 這是預設組態。 如此一來，就可以使用熱待命複本作為容錯移轉目標。

## <a name="data-size-and-storage-questions"></a>資料大小和儲存體問題

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>超大規模資料庫支援的資料庫大小上限為何

100 TB。

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>「超大規模資料庫」的交易記錄大小為何

「超大規模資料庫」的交易記錄可說是無限制的。 您無須擔心記錄輸送量偏高的系統上是否會有記錄空間不足的問題。 不過，記錄產生速率可能會受到節流，以持續主動寫入工作負載。 尖峰持續記錄產生速率為 100 MB/秒。

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>我的 `tempdb` 會隨著資料庫成長而調整

您的 `tempdb` 資料庫位於本機 SSD 儲存體，且大小會按比例調整為您布建的計算大小。 您的 `tempdb` 已優化，可提供最大的效能優勢。 `tempdb` 大小不是可設定的，而且會為您管理。

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>我的資料庫大小會自動成長，還是必須管理資料檔案的大小

您的資料庫大小會在您插入/擷取更多資料時自動成長。

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>超大規模資料庫支援或開頭的最小資料庫大小為何

40 GB。 建立超大規模資料庫資料庫，其起始大小為 10 GB。 然後，它會每隔10分鐘開始成長 10 GB，直到達到 40 GB 的大小為止。 這 10 GB chucks 中的每一個都是配置在不同的頁面伺服器中，以提供更多的 IOPS 和更高的 i/o 平行處理原則。 由於這項優化，即使您選擇小於 40 GB 的初始資料庫大小，資料庫也會自動成長到至少 40 GB。

### <a name="in-what-increments-does-my-database-size-grow"></a>資料庫大小成長的遞增量為何

每個資料檔案會成長 10 GB。 多個資料檔案可能會同時成長。

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>是超大規模資料庫本機或遠端的儲存體

在「超大規模資料庫」中，資料檔案會儲存在 Azure 標準儲存體中。 在接近計算複本的頁面伺服器上，本機 SSD 儲存體上會完整快取資料。 此外，計算複本具有本機 SSD 和記憶體中的資料快取，以減少從遠端頁面伺服器提取資料的頻率。

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>我是否可管理或定義「超大規模資料庫」的檔案或檔案群組

否。 資料檔案會自動新增。 建立其他檔案群組的常見原因不適用於超大規模資料庫儲存體架構。

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>我是否可為資料庫佈建資料成長的強制上限

否。

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>如何使用超大規模資料庫配置資料檔案

資料檔案是由頁面伺服器所控制，每個資料檔案各有一頁伺服器。 隨著資料大小的增加，會加入資料檔案和相關聯的頁面伺服器。

### <a name="is-database-shrink-supported"></a>是否支援資料庫縮減

否。

### <a name="is-data-compression-supported"></a>支援資料壓縮

是，包括資料列、頁面和資料行存放區壓縮。

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>如果我有很大的資料表，資料表的資料是否會分散到多個資料檔案

是。 與指定的資料表相關聯的資料頁面可能會移至多個資料檔案中，但全都屬於相同的檔案群組。 SQL Server 使用[比例填滿策略](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy)，將資料散發到資料檔案。

## <a name="data-migration-questions"></a>資料移轉問題

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>是否可將現有的 Azure SQL 資料庫移至超大規模資料庫服務層級

是。 您可以將現有的 Azure SQL 資料庫移至「超大規模資料庫」。 這是單向的遷移。 您無法將資料庫從超大規模資料庫移到另一個服務層級中。 對於概念證明（Poc），建議您建立資料庫的複本，並將複本遷移至超大規模資料庫。
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>我可以將超大規模資料庫資料庫移至其他服務層級

否。 目前，您無法將超大規模資料庫資料庫移至另一個服務層級。

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 超大規模資料庫尚不支援某些 Azure SQL Database 功能，包括但不限於長期的備份保留。 在您將資料庫移轉至「超大規模資料庫」後，這些功能將會停止運作。  我們希望這些限制是暫時性的。

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>我可以將內部部署 SQL Server 資料庫或雲端虛擬機器中的 SQL Server 資料庫移至超大規模資料庫

是。 您可以使用所有現有的遷移技術來遷移至超大規模資料庫，包括異動複寫，以及任何其他資料移動技術（大量複製、Azure Data Factory、Azure Databricks、SSIS）。 另請參閱[Azure 資料庫移轉服務](../dms/dms-overview.md)，其支援許多遷移案例。

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>我在從內部部署或虛擬機器環境遷移至超大規模資料庫時的停機時間，以及如何將它最小化

遷移至超大規模資料庫的停機時間與將資料庫移轉至其他 Azure SQL Database 服務層級時的停機時間相同。 移轉大小未超過 10 TB 的資料庫時，您可以使用[異動複寫](replication-to-sql-database.md#data-migration-scenario
)盡可能縮短停機時間。 對於非常大型的資料庫（10 + TB），您可以考慮使用 ADF、Spark 或其他資料移動技術來遷移資料。

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>將 X 個數據量帶入超大規模資料庫需要多久的時間

超大規模資料庫能夠耗用 100 MB/秒的新/變更資料，但將資料移至 Azure SQL 資料庫所需的時間也會受到可用的網路輸送量、來源讀取速度和目標資料庫服務等級目標的影響。

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>我可以從 blob 儲存體讀取資料並執行快速載入（例如 SQL 資料倉儲中的 Polybase）

您可以讓用戶端應用程式從 Azure 儲存體讀取資料，並將資料載入至超大規模資料庫資料庫（就像您可以與任何其他 Azure SQL 資料庫一樣）。 Azure SQL Database 目前不支援 Polybase。 若要提供快速載入，您可以使用[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)，或使用 spark [connector for SQL](sql-database-spark-connector.md) [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)中的 spark 作業。 SQL 的 Spark 連接器支援大量插入。

您也可以使用 BULK INSERT 或 OPENROWSET，從 Azure Blob 存放區大量讀取資料： [Azure Blob 儲存體中的資料大量存取的範例](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)。

「超大規模資料庫」不支援簡單復原或大量記錄模式。 需要完整復原模式才能提供高可用性和時間點恢復。 不過，相較于其他 Azure SQL Database 服務層，超大規模資料庫記錄架構提供更佳的資料內嵌速率。

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>超大規模資料庫允許布建多個節點，以進行大量資料的平行內嵌

否。 超大規模資料庫是對稱式多重處理（SMP）架構，而且不是大量平行處理（MPP）或多宿主架構。 您只能建立多個複本以相應放大唯讀工作負載。

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>最舊的 SQL Server 版本支援遷移至超大規模資料庫

SQL Server 2005。 如需詳細資訊，請參閱[移轉至單一資料庫或集區資料庫](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)。 若要了解相容性問題，請參閱[解決資料庫移轉相容性問題](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)。

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>超大規模資料庫支援從其他資料來源（例如 Amazon Aurora、MySQL、于 postgresql、Oracle、DB2 和其他資料庫平臺）進行遷移

是。 [Azure 資料庫移轉服務](../dms/dms-overview.md)支援許多遷移案例。

## <a name="business-continuity-and-disaster-recovery-questions"></a>商務持續性和嚴重損壞修復問題

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>針對超大規模資料庫資料庫提供了哪些 Sla

請參閱[Azure SQL Database 的 SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)。 其他次要計算複本會增加可用性，最高可達99.99%，適用于有兩個或多個次要計算複本的資料庫。

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL Database 服務是否會為我管理資料庫備份

是。

### <a name="how-often-are-the-database-backups-taken"></a>資料庫備份多久執行一次

超大規模資料庫資料庫沒有傳統的完整、差異和記錄備份。 相反地，資料檔案有一般的儲存體快照集。 所產生的記錄檔只會在設定的保留期限內保持為「保留」，允許還原到保留期限內的任何時間點。

### <a name="does-hyperscale-support-point-in-time-restore"></a>超大規模資料庫支援時間點還原

是。

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>超大規模資料庫中資料庫還原的復原點目標（RPO）/Recovery 時間目標（RTO）為何

RPO 為0分鐘。RTO 目標小於10分鐘，不論資料庫大小為何。 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>資料庫備份是否會影響我的主要或次要複本上的計算效能

否。 備份是由儲存子系統所管理，並利用儲存體快照集。 它們不會影響使用者工作負載。

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>我可以使用超大規模資料庫資料庫執行異地還原

是。 完全支援異地還原。 不同于還原時間點，地理還原需要資料大小的作業。 資料檔案會平行複製，因此此作業的持續時間主要取決於資料庫中最大檔案的大小，而不是資料庫總大小。 如果在與源資料庫區域[配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)的 Azure 區域中還原資料庫，地理還原時間將會大幅縮短。

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>我可以使用超大規模資料庫資料庫來設定異地複寫

目前沒有。

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>我可以進行超大規模資料庫資料庫備份，並將它還原到我的內部部署伺服器，或在 VM 的 SQL Server 上

否。 超大規模資料庫資料庫的儲存格式與 SQL Server 的任何發行版本不同，而且您不能控制備份或存取它們。 若要將資料從超大規模資料庫資料庫取出，您可以使用任何資料移動技術（也就是 Azure Data Factory、Azure Databricks、SSIS 等）來解壓縮資料。

## <a name="cross-feature-questions"></a>跨功能問題

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 某些 Azure SQL Database 功能在超大規模資料庫中不受支援，包括但不限於長期的備份保留。 在您將資料庫移轉至「超大規模資料庫」後，這些功能將會停止運作。

### <a name="will-polybase-work-with-hyperscale"></a>Polybase 會與超大規模資料庫搭配使用

否。 Azure SQL Database 不支援 Polybase。

### <a name="does-hyperscale-have-support-for-r-and-python"></a>超大規模資料庫是否支援 R 和 Python

目前沒有。

### <a name="are-compute-nodes-containerized"></a>是容器化的計算節點

否。 超大規模資料庫進程會在[Service Fabric](https://azure.microsoft.com/services/service-fabric/)節點（vm）上執行，而不是在容器中執行。

## <a name="performance-questions"></a>效能問題

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>我可以在超大規模資料庫資料庫中推送多少寫入輸送量

針對任何超大規模資料庫計算大小，交易記錄輸送量上限會設為 100 MB/s。 達到此速率的能力取決於多個因素，包括但不限於工作負載類型、用戶端設定，以及在主要計算複本上具有足夠的計算容量，以此速率產生記錄。

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>我可以在最大的計算上取得多少 IOPS

IOPS 和 IO 延遲會根據工作負載模式而有所不同。 如果要存取的資料會快取在計算複本上，您會看到與本機 SSD 類似的 IO 效能。

### <a name="does-my-throughput-get-affected-by-backups"></a>備份是否會影響到輸送量

否。 計算與儲存層分離。 這可消除備份的效能影響。

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>當我布建額外的計算複本時，我的輸送量會受到影響

由於儲存體是共用的，而且主要和次要計算複本之間不會進行直接的實體複寫，因此主要複本上的輸送量不會受到新增次要複本的直接影響。 不過，我們可能會在主要複本上節流持續的主動寫入工作負載，以允許次要複本和頁面伺服器上的記錄套用，以避免次要複本上的讀取效能不佳。

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>如何? 診斷超大規模資料庫資料庫中的效能問題並進行疑難排解

對於大部分的效能問題，特別是不是以儲存體效能為基礎的問題，常見的 SQL Server 診斷和疑難排解步驟適用。 如需超大規模資料庫特定的儲存體診斷，請參閱[SQL 超大規模資料庫效能疑難排解診斷](sql-database-hyperscale-performance-diagnostics.md)。

## <a name="scalability-questions"></a>擴充性問題

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>相應增加和相應減少計算複本需要多久的時間

無論資料大小為何，相應增加或減少計算都應該花費5-10 分鐘的時間。

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>我的資料庫在相應增加/減少作業進行時是否會離線

否。 相應增加和相應減少會在線上執行。

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>進行調整作業時是否可能發生連線中斷

相應增加或減少會導致在調整作業結束時，會卸載現有的連接。 新增次要複本不會導致連接中斷。

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>計算複本的相應增加和減少是自動或使用者觸發的作業

使用者。 並非自動的。  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>當計算相應增加時，我的 `tempdb` 資料庫的大小也會隨之成長

是。 當計算成長時，`tempdb` 資料庫會自動相應增加。  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>我可以布建多個主要計算複本，例如多主機系統，其中多個主要計算標頭可以驅動較高層級的平行存取

否。 只有主要計算複本會接受讀取/寫入要求。 次要計算複本只接受唯讀要求。

## <a name="read-scale-out-questions"></a>讀取相應放大問題

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>我可以布建多少次要計算複本

我們預設會針對超大規模資料庫資料庫建立一個次要複本。 如果您想要調整複本數目，您可以使用[Azure 入口網站](https://portal.azure.com)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)來執行此動作。

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>如何? 連接到這些次要計算複本

您可以將連接字串上的 `ApplicationIntent` 引數設定為 `ReadOnly`，以連線至這些額外的唯讀計算複本。 任何標示為 `ReadOnly` 的連線都會自動路由至其中一個額外的唯讀計算複本。  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>如何? 驗證是否已使用 SSMS 或其他用戶端工具成功連線到次要計算複本？

您可以執行下列 T-SQL 查詢： `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`。
如果您連接到唯讀次要複本，則結果會 `READ_ONLY`，如果您連接到主要複本，則會 `READ_WRITE`。 請注意，資料庫內容必須設定為超大規模資料庫資料庫的名稱，而不是設為 `master` 資料庫。

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>我可以為讀取相應放大複本建立專用端點嗎

否。 您只能藉由指定 `ApplicationIntent=ReadOnly`來連線到讀取相應放大複本。

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>系統是否會對讀取工作負載進行智慧型負載平衡

否。 具有唯讀意圖的新連線會重新導向至任意讀取相應放大複本。

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>我可以獨立于主要複本之外相應增加/減少次要計算複本

否。 次要計算複本也會用來作為高可用性容錯移轉目標，因此，它們必須具有與主資料庫相同的設定，才能在容錯移轉後提供預期的效能。

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>我的主要計算和其他次要計算複本的 `tempdb` 大小是否不同

否。 您的 `tempdb` 資料庫是根據計算大小布建而設定，次要計算複本的大小與主要計算相同。

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>我可以在次要計算複本上新增索引和視圖嗎

否。 超大規模資料庫資料庫具有共用儲存體，這表示所有計算複本都會看到相同的資料表、索引和 views。 如果您想要針對次要複本上的讀取優化的其他索引，您必須將它們新增至主要複本。

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>主要和次要計算複本之間有多少延遲

從交易在主要複本上認可的時間開始，到它在次要上可見時的資料延遲取決於目前的記錄產生速率。 一般資料延遲是以低毫秒為單位。

## <a name="next-steps"></a>後續步驟

如需超大規模資料庫服務層級的詳細資訊，請參閱[超大規模資料庫服務層級](sql-database-service-tier-hyperscale.md)。
