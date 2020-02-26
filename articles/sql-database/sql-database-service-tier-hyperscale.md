---
title: Azure SQL Database 超大規模資料庫概觀 | Microsoft Docs
description: 本文描述 Azure SQL Database 以虛擬核心為基礎的購買模型中的超大規模服務層級，並說明其與一般用途和商務關鍵性服務層級的差異。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: efb6cd1a45ac14dcbd5b2b6d8e70f5ee096ddbd8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587272"
---
# <a name="hyperscale-service-tier"></a>超大規模資料庫服務層級

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：
- 一般目的/標準 
-  超大規模資料庫
-  業務關鍵/進階

Azure SQL Database 中的超大規模服務層是以虛擬核心為基礎的購買模型中的最新服務層。 此服務層級是可高度擴充的儲存體和計算效能層，可利用 Azure 架構以相應放大 Azure SQL Database 的儲存體和計算資源，而大幅超過一般用途和商務關鍵性服務層級的可用限制。

> 
> [!NOTE]
> 若要深入了解以虛擬核心為基礎的購買模型中的一般用途與商務關鍵服務層級，請參閱[一般目的](sql-database-service-tier-general-purpose.md)與[業務關鍵](sql-database-service-tier-business-critical.md)服務層級。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-service-tiers.md)。


## <a name="what-are-the-hyperscale-capabilities"></a>超大規模資料庫功能有哪些

Azure SQL Database 中的超大規模資料庫服務層級提供下列額外功能：

- 支援最多 100 TB 的資料庫大小
- 近乎即時的資料庫備份（根據儲存在 Azure Blob 儲存體中的檔案快照集），不論大小，不會影響計算資源的 IO  
- 快速的資料庫還原 (根據檔案快照集)，僅需數分鐘，而非數小時或數天 (不是資料作業的大小)
- 不論資料量為何，較高的記錄輸送量和較快的交易認可時間就會有較高的整體效能
- 快速相應放大 - 您可以佈建一或多個唯讀節點來卸載讀取工作負載，並用來作為熱待命
- 快速相應增加 - 您可以在有限的時間內視需要和在需要時相應增加計算資源以容納大量工作負載，然後在不需要時相應減少計算資源。

超大規模資料庫服務層級會移除傳統上會在雲端資料庫中看到的許多實際限制。 大部分其他資料庫都受限於單一節點中的可用資源，但超大規模資料庫服務層級中的資料庫沒有這類限制。 透過其彈性儲存體架構，儲存體可依需求成長。 事實上，不會建立具有所定義大小上限的超大規模資料庫服務層級。 超大規模資料庫服務層級會視需要成長，而且只會向您收取所使用容量的費用。 針對大量讀取工作負載，超大規模資料庫服務層級會視需要佈建額外的讀取複本來卸載讀取工作負載，以提供快速相應放大。

此外，建立資料庫備份或是相應增加或減少所需的時間，不再繫結到資料庫中資料的磁碟區。 超大規模資料庫服務層級幾乎可以立即予以備份。 您也可以在數分鐘內相應增加或減少數十個 TB 的資料庫。 此功能讓您不需要擔心選擇的初始設定進行方塊化處理。

如需超大規模資料庫服務層級計算大小的詳細資訊，請參閱[服務層級特性](sql-database-service-tiers-vcore.md#service-tiers)。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>誰應該考慮使用超大規模資料庫服務層級

超大規模資料庫服務層級適用于大多數的商務工作負載，因為它可透過可獨立調整的計算和儲存體資源，提供絕佳的彈性和高效能。 有了自動調整儲存體大小上限為 100 TB 的功能，對於下列情況而言，這是很好的選擇：

- 有大型資料庫內部部署，並想要移至雲端以將其應用程式現代化
- 已在雲端中，並受到其他服務層級的最大資料庫大小限制（1-4 TB）
- 有較小的資料庫，但需要快速的垂直和水準計算調整、高效能、立即備份和快速資料庫還原。

超大規模資料庫服務層級支援廣泛的 SQL Server 工作負載，從純粹 OLTP 到純粹的分析，但它主要是針對 OLTP 和混合式交易和分析處理（HTAP）工作負載進行優化。

> [!IMPORTANT]
> 彈性集區不支援超大規模資料庫服務層級。

## <a name="hyperscale-pricing-model"></a>超大規模資料庫定價模型

超大規模資料庫服務層級僅在[虛擬核心模型](sql-database-service-tiers-vcore.md)中提供。 為配合新的架構，定價模型會與一般用途或業務關鍵服務層級稍微不同：

- **計算**：

  超大規模資料庫計算單位價格是按每個複本計算。 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 價格會自動套用到讀取縮放複本。 根據預設，我們會為每個超大規模資料庫資料庫建立一個主要複本和一個唯讀複本。  使用者可以調整複本總數，包括從1-5 到的主要複本。

- **儲存體**：

  您設定超大規模資料庫時，不需要指定資料大小上限。 在超大規模資料庫層中，您會根據實際配置向您的資料庫收取儲存體費用。 儲存體會自動設定在 40 GB 和 100 TB 之間，10 gb 增量為 10 gb。 如有需要，多個資料檔案可能會同時成長。 建立超大規模資料庫資料庫時，其起始大小為 10 GB，且每隔10分鐘會開始增加 10 GB，直到達到 40 GB 的大小為止。

如需有關超大規模定價的詳細資訊，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>分散式函式架構

傳統資料庫引擎將所有資料管理功能都集中到一個位置/處理序 (因此，生產環境中的分散式資料庫現在會有多份整合型資料引擎)，但超大規模資料庫不同，會具有不同的查詢處理引擎，而各種資料引擎的語意都會與提供資料長期儲存和持久性的元件不同。 因此，只要需要，就可以順暢地相應放大儲存體容量 (初始目標為 100 TB)。 唯讀複本會共用相同的儲存元件，因此不需要進行任何資料複製即可啟動新的可讀取複本。 

下圖說明超大規模資料庫中不同類型的節點：

![架構](./media/sql-database-hyperscale/hyperscale-architecture.png)

超大規模資料庫資料庫包含下列不同類型的元件：

### <a name="compute"></a>計算

計算節點就是關聯式引擎的所在位置，因此會發生所有語言元素、查詢處理等等。 所有與超大規模資料庫的使用者互動都是透過這些計算節點進行。 計算節點具有 SSD 快取 (在上圖中標示為 RBPEX (復原緩衝集區延伸模組))，可減少擷取資料頁面所需的網路來回行程次數。 有一個主要計算節點可以處理所有讀寫工作負載和交易。 有一或多個次要計算節點作為容錯移轉的熱待命節點，以及作為用於卸載讀取工作負載的唯讀計算節點 (如果想要使用此功能)。

### <a name="page-server"></a>頁面伺服器

頁面伺服器是代表相應放大儲存引擎的系統。  每部頁面伺服器都負責資料庫中的一部分頁面。  名義上，每個頁面伺服器都會控制 128 GB 和 1 TB 的資料。 未在多部頁面伺服器上共用資料 (在保留以獲得備援和可用性的複本外部)。 頁面伺服器的工作是隨需提供計算節點的資料庫頁面，並隨著交易更新資料而更新頁面。 從記錄服務播放記錄檔記錄，以保持最新的頁面伺服器。 頁面伺服器也會維護 SSD 快取以提升效能。 資料頁面的長期儲存體會保存在 Azure 儲存體中，以獲得額外的可靠性。

### <a name="log-service"></a>記錄服務

記錄服務會接受來自主要計算複本的記錄檔記錄、將它們保存在永久性快取中，然後將記錄檔記錄轉送至其餘的計算複本（讓他們可以更新其快取）以及相關的頁面伺服器，以便更新資料。但是. 如此一來，來自主要計算複本的所有資料變更都會透過記錄服務傳播至所有次要計算複本和頁面伺服器。 最後，記錄檔記錄會在 Azure 儲存體中推送至長期儲存，這是一個幾乎無限的儲存體存放庫。 這種機制不需要經常截斷記錄。 記錄服務也具有本機快取，以加速存取記錄檔記錄。

### <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體包含資料庫中的所有資料檔案。 頁面伺服器會將 Azure 儲存體的資料檔案保持在最新狀態。 此儲存體用於備份目的，也用於 Azure 區域之間的複寫。 備份是使用資料檔案的儲存體快照集來執行。 不論資料大小為何，使用快照集的還原作業都是快速的。 資料可以還原到資料庫的備份保留期限內的任何時間點。

## <a name="backup-and-restore"></a>備份與還原

備份是以檔案快照集為基礎，因此幾乎瞬間完成。 儲存體和計算區隔可讓您將備份/還原作業向下推送至儲存層，以降低主要計算複本的處理負擔。 因此，資料庫備份不會影響主要計算節點的效能;同樣地，還原會藉由還原到檔案快照集來完成，因此這不是資料作業的大小。 Restore 是一種持續時間的作業，甚至可以在數分鐘內還原多 tb 的資料庫，而不需要數小時或數天。 藉由還原現有的備份來建立新的資料庫也會利用這項功能：建立用於開發或測試用途的資料庫複本，甚至是大小 tb 的資料庫，在數分鐘內就會雖可行。

## <a name="scale-and-performance-advantages"></a>規模和效能優點

超大規模資料庫架構可以快速加速/減速其他唯讀計算節點，因而允許大規模讀取功能，也可以釋出主要計算節點來提供更多寫入要求。 此外，基於超大規模資料庫架構的共用儲存體架構，也可以快速相應增加/減少計算節點。

## <a name="create-a-hyperscale-database"></a>建立超大規模資料庫資料庫

您可以使用[Azure 入口網站](https://portal.azure.com)、 [t-sql](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)、 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase)或[CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)來建立超大規模資料庫資料庫。 超大規模資料庫資料庫只能使用[vCore 為基礎的購買模型](sql-database-service-tiers-vcore.md)。

下列 T-SQL 命令會建立超大規模資料庫。 您必須在 `CREATE DATABASE` 陳述式中指定版本和服務目標。 如需有效服務目標的清單，請參閱[資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4)。

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
這會在具有4個核心的第5代硬體上建立超大規模資料庫資料庫。

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>將現有的 Azure SQL Database 遷移至超大規模資料庫服務層級

您可以使用[Azure 入口網站](https://portal.azure.com)、 [t-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)、 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase)或[CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)，將現有的 Azure SQL 資料庫移至超大規模資料庫。 這一次，這是單向的遷移。 除了匯出和匯入資料之外，您無法將資料庫從超大規模資料庫移至另一個服務層級。 對於概念證明（Poc），我們建議您製作生產資料庫的複本，並將複本遷移至超大規模資料庫。 將現有的 Azure SQL database 遷移至超大規模資料庫層是資料作業的大小。

下列 T-SQL 命令會將資料庫移至超大規模資料庫服務層級。 您必須在 `ALTER DATABASE` 陳述式中指定版本和服務目標。

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>連線到超大規模資料庫的讀取縮放複本

在超大規模資料庫資料庫中，用戶端所提供之連接字串中的 `ApplicationIntent` 引數會指示連接是否路由傳送至寫入複本或唯讀次要複本。 如果 `ApplicationIntent` 設為 `READONLY`，且資料庫沒有次要複本，連線將會路由至主要複本，並預設為 `ReadWrite` 行為。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

超大規模資料庫次要複本全都相同，使用與主要複本相同的服務等級目標。 如果存在一個以上的次要複本，則工作負載會分散到所有可用的次要資料庫。 每個次要複本都會獨立更新，因此不同的複本可能會有不同于主要複本的資料延遲。

## <a name="database-high-availability-in-hyperscale"></a>超大規模資料庫中的資料庫高可用性

如同所有其他服務層級，不論計算複本可用性為何，超大規模資料庫保證認可交易的資料持久性。 由於主要複本變成無法使用而造成停機的範圍，取決於容錯移轉的類型（已規劃與未計畫），以及至少有一個次要複本存在。 在計畫的容錯移轉（也就是維護事件）中，系統會在起始容錯移轉之前建立新的主要複本，或使用現有的次要複本做為容錯移轉目標。 在未計畫的容錯移轉（也就是主要複本上的硬體故障）中，系統會使用次要複本做為容錯移轉目標（如果有的話），或從可用計算容量的集區建立新的主要複本。 在後者的情況下，停機持續時間會因為建立新的主要複本所需的額外步驟而較長。

如需超大規模資料庫 SLA，請參閱[Azure SQL Database 的 sla](https://azure.microsoft.com/support/legal/sla/sql-database/)。

## <a name="disaster-recovery-for-hyperscale-databases"></a>超大規模資料庫資料庫的嚴重損壞修復

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>將超大規模資料庫資料庫還原到不同的地理位置
如果您需要將 Azure SQL Database 超大規模資料庫 DB 還原到目前所裝載的區域以外的地區，做為嚴重損壞修復作業或演練、重新配置或任何其他原因的一部分，主要方法是執行資料庫的異地還原。  這牽涉到的步驟與將任何其他 AZURE SQL DB 還原至不同區域時所使用的步驟完全相同：
1. 如果您還沒有適當的伺服器，請在目的地區域中建立 SQL Database 伺服器。  此伺服器應由與原始（來源）伺服器相同的訂用帳戶所擁有。
2. 依照從自動備份還原 Azure SQL 資料庫頁面上的「[地理還原](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore)」主題中的指示進行。

> [!NOTE]
> 因為來源和目標位於不同的區域，所以資料庫無法與源資料庫共用快照集儲存體，就像在非異地還原一樣，這樣會非常快速地完成。 在超大規模資料庫資料庫的異地還原案例中，即使目標位於異地複寫儲存體的配對區域中，也會是資料的大小作業。  這表示進行異地還原的時間會與要還原之資料庫的大小成正比。  如果目標是在配對的區域中，則複本將會在區域內，其速度會明顯比跨區域複製快，但它仍會是資料大小的作業。

## <a name=regions></a>可用區域

Azure SQL Database 超大規模資料庫層目前可在下欄區域使用：

- 澳大利亞東部
- 澳大利亞東南部
- 巴西南部
- 加拿大中部
- 美國中部
- 中國東部 2
- 中國北部 2
- 東亞
- 美國東部
- 美國東部2
- 法國中部
- 日本東部
- 日本西部
- 南韓中部
- 南韓南部
- 美國中北部
- 北歐
- 南非北部
- 美國中南部
- 東南亞
- 英國南部
- 英國西部
- 西歐
- 美國西部
- 美國西部 2

如果您想要在未列為支援的區域中建立超大規模資料庫資料庫，您可以透過 Azure 入口網站傳送上架要求。 如需指示，請參閱[Azure SQL Database 的要求配額增加](quota-increase-request.md)以取得指示。 提交您的要求時，請使用下列指導方針：

- 使用 [[其他配額要求](quota-increase-request.md#other)] SQL 資料庫配額類型。
- 在 [文字詳細資料] 中，新增計算 SKU/總核心數，包括可讀取的複本。
- 也請指定估計的 TB。

## <a name="known-limitations"></a>已知限制

這些是超大規模資料庫服務層級目前對正式推出的限制。  我們正努力盡可能移除這些限制。

| 問題 | 描述 |
| :---- | :--------- |
| 邏輯伺服器的 [管理備份] 窗格不會顯示超大規模資料庫資料庫將會從 SQL server 篩選  | 超大規模資料庫有不同的管理備份方法，因此長期保留和時間點備份保留設定不會套用/失效。 據此，超大規模資料庫不會出現在 [管理備份] 窗格中。 |
| 時間點還原 | 一旦將資料庫移轉至超大規模資料庫服務層級之後，就不支援在遷移之前還原至時間點。|
| 將非超大規模資料庫 DB 還原至超大規模資料庫，反之亦然 | 您無法將超大規模資料庫資料庫還原至非超大規模資料庫資料庫，也不能將非超大規模資料庫資料庫還原至超大規模資料庫資料庫。|
| 如果資料庫的一個或多個資料檔案大於 1 TB，則遷移會失敗 | 在某些情況下，您可以將大型檔案壓縮為小於 1 TB，藉此解決此問題。 如果在遷移程式期間遷移正在使用的資料庫，請確定沒有任何檔案大於 1 TB。 使用下列查詢來判斷資料庫檔案的大小。 `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| 受控執行個體 | 超大規模資料庫資料庫目前不支援 Azure SQL Database 受控執行個體。 |
| 彈性集區 |  SQL Database 超大規模資料庫目前不支援彈性集區。|
| 移轉至超大規模資料庫模目前是單向作業 | 一旦資料庫遷移至超大規模資料庫後，就無法直接遷移至非超大規模資料庫服務層級。 目前，將資料庫從超大規模資料庫移轉至非超大規模資料庫的唯一方法，是使用 BACPAC 檔案或其他資料移動技術（大量複製、Azure Data Factory、Azure Databricks、SSIS 等等）來匯出/匯入。|
| 使用持續性記憶體內建物件來遷移資料庫 | 超大規模資料庫僅支援非持續性記憶體內建物件（資料表類型、原生 SPs 和函數）。  在將資料庫移轉至超大規模資料庫服務層級之前，必須先卸載持續性記憶體內部資料表和其他物件，並將其重新建立為非記憶體中的物件。|
| 變更追蹤 | 變更追蹤目前處於公開預覽狀態，而且可以在新的或現有的超大規模資料庫資料庫上啟用。 |
| 異地複寫  | 您還無法為 Azure SQL Database 超大規模資料庫設定異地複寫。 |
| 資料庫複製 | 您還不能在 Azure SQL 超大規模資料庫中使用資料庫複製來建立新的資料庫。 |
| TDE/AKV 整合 | Azure SQL Database 超大規模資料庫尚不支援使用 Azure Key Vault （通常稱為自備金鑰或 BYOK）的透明資料庫加密，不過，已完全支援具有服務管理金鑰的 TDE。 |
|智慧型資料庫功能 | 除了 [強制計畫] 選項之外，超大規模資料庫上還不支援所有其他自動調整選項：選項可能會顯示為已啟用，但不會有任何建議或動作。 |
|查詢效能深入解析 | 超大規模資料庫資料庫目前不支援查詢效能深入解析。 |
| 壓縮資料庫 | 超大規模資料庫資料庫目前不支援 DBCC SHRINKDATABASE 或 DBCC SHRINKFILE。 |
| 資料庫完整性檢查 | 超大規模資料庫資料庫目前不支援 DBCC CHECKDB。 如需 Azure SQL Database 中資料完整性管理的詳細資訊，請參閱[Azure SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。 |

## <a name="next-steps"></a>後續步驟

- 如需超大規模資料庫的常見問題集，請參閱[關於超大規模資料庫的常見問題集](sql-database-service-tier-hyperscale-faq.md)。
- 如需服務層級的資訊，請參閱[服務層級](sql-database-service-tiers.md)
- 如需伺服器和訂用帳戶層級的限制資訊，請參閱[邏輯伺服器上的資源限制概觀](sql-database-resource-limits-logical-server.md)。
- 如需單一資料庫的購買模型限制相關資訊，請參閱[適用於單一資料庫的 Azure SQL Database 以虛擬核心為基礎的購買模型限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
