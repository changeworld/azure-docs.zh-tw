---
title: 什麼是超大規模服務層級？
description: 本文說明 Azure SQL Database 中以 vCore 為基礎的購買模型中的超大規模服務層級，並說明其與一般用途和業務關鍵服務層級有何不同。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/19/2020
ms.openlocfilehash: 547e56dbc72e283b6c186380a01580982e029a64
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216635"
---
# <a name="hyperscale-service-tier"></a>超大規模資料庫服務層級

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：

- 一般目的/標準
- 超大規模資料庫
- 業務關鍵/進階

Azure SQL Database 中的超大規模服務層是以虛擬核心為基礎的購買模型中的最新服務層。 此服務層級是可高度擴充的儲存體和計算效能層，可利用 Azure 架構以相應放大 Azure SQL Database 的儲存體和計算資源，而大幅超過一般用途和商務關鍵性服務層級的可用限制。

> [!NOTE]
>
> - 如需 vCore 為基礎的購買模型中的一般用途和業務關鍵服務層級的詳細資訊，請參閱 [一般用途](service-tier-general-purpose.md) 和 [業務關鍵](service-tier-business-critical.md) 服務層級。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](purchasing-models.md)。
> - 超大規模服務層級目前僅適用于 Azure SQL Database，而不是 Azure SQL 受控執行個體。

## <a name="what-are-the-hyperscale-capabilities"></a>超大規模資料庫功能有哪些

Azure SQL Database 中的超大規模資料庫服務層級提供下列額外功能：

- 支援最多 100 TB 的資料庫大小
- 近乎即時的資料庫 (備份會根據儲存在 Azure Blob 儲存體中的檔案快照集) ，不論大小為何，都不會對計算資源造成任何 IO 影響  
- 快速的資料庫還原 (根據檔案快照集)，僅需數分鐘，而非數小時或數天 (不是資料作業的大小)
- 不論資料量為何，較高的記錄輸送量和較快的交易認可時間就會有較高的整體效能
- 快速相應放大 - 您可以佈建一或多個唯讀節點來卸載讀取工作負載，並用來作為熱待命
- 快速擴大-您可以長期調整計算資源，以在需要時容納大量的工作負載，然後在不需要時調整計算資源。

超大規模資料庫服務層級會移除傳統上會在雲端資料庫中看到的許多實際限制。 大部分其他資料庫都受限於單一節點中的可用資源，但超大規模資料庫服務層級中的資料庫沒有這類限制。 透過其彈性儲存體架構，儲存體可依需求成長。 事實上，並不會使用定義的大小上限來建立超大規模資料庫。 超大規模資料庫會視需要成長，您只需支付所使用的容量。 針對大量讀取工作負載，超大規模資料庫服務層級會視需要佈建額外的讀取複本來卸載讀取工作負載，以提供快速相應放大。

此外，建立資料庫備份或是相應增加或減少所需的時間，不再繫結到資料庫中資料的磁碟區。 超大規模資料庫服務層級幾乎可以立即予以備份。 您也可以在數分鐘內相應增加或減少數十個 TB 的資料庫。 此功能讓您不需要擔心選擇的初始設定進行方塊化處理。

如需超大規模資料庫服務層級計算大小的詳細資訊，請參閱[服務層級特性](service-tiers-vcore.md#service-tiers)。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>誰應該考慮使用超大規模資料庫服務層級

超大規模服務層級適用于大部分的商務工作負載，因為它可提供絕佳的彈性和高效能，並提供可獨立調整的計算和儲存體資源。 利用自動調整儲存體最高達 100 TB 的能力，對於下列客戶而言，這是很好的選擇：

- 在內部部署環境中使用大型資料庫，並想要藉由移至雲端來現代化其應用程式
- 已在雲端中，並受到其他服務層級的資料庫大小上限 (1-4 TB 的限制) 
- 擁有較小的資料庫，但需要快速的垂直和水準計算調整、高效能、立即備份，以及快速的資料庫還原。

超大規模服務層級支援廣泛的 SQL Server 工作負載，從純 OLTP 到純分析，但主要是針對 OLTP 和混合式交易和分析處理進行優化， (HTAP) 工作負載。

> [!IMPORTANT]
> 彈性集區不支援超大規模資料庫服務層級。

## <a name="hyperscale-pricing-model"></a>超大規模資料庫定價模型

超大規模資料庫服務層級僅在[虛擬核心模型](service-tiers-vcore.md)中提供。 為配合新的架構，定價模型會與一般用途或業務關鍵服務層級稍微不同：

- **計算**：

  超大規模資料庫計算單位價格是按每個複本計算。 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 價格會自動套用到讀取縮放複本。 根據預設，我們會為每個超大規模資料庫建立一個主要複本和一個唯讀複本。  使用者可以調整包含主要自1-5 的複本總數。

- **存放裝置**：

  您設定超大規模資料庫時，不需要指定資料大小上限。 在超大規模層中，您需支付資料庫儲存空間的費用（以實際配置為基礎）。 儲存體會在 40 GB 和 100 TB 之間自動設定，以 10 GB 為單位遞增。 如有需要，多個資料檔案可能會同時成長。 建立超大規模資料庫的起始大小為 10 GB，且每隔10分鐘就會開始增加 10 GB，直到達到 40 GB 的大小。

如需有關超大規模定價的詳細資訊，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>分散式函式架構

傳統資料庫引擎將所有資料管理功能都集中到一個位置/處理序 (因此，生產環境中的分散式資料庫現在會有多份整合型資料引擎)，但超大規模資料庫不同，會具有不同的查詢處理引擎，而各種資料引擎的語意都會與提供資料長期儲存和持久性的元件不同。 因此，只要需要，就可以順暢地相應放大儲存體容量 (初始目標為 100 TB)。 唯讀複本會共用相同的儲存體元件，因此不需要複製任何資料來啟動新的可讀取複本。

下圖說明超大規模資料庫中不同類型的節點：

![架構](./media/service-tier-hyperscale/hyperscale-architecture.png)

超大規模資料庫包含下列不同類型的元件：

### <a name="compute"></a>計算

計算節點是關聯式引擎所在的位置。 這是發生語言、查詢和交易處理的地方。 所有與超大規模資料庫的使用者互動都是透過這些計算節點進行。 計算節點具有 SSD 快取 (在上圖中標示為 RBPEX (復原緩衝集區延伸模組))，可減少擷取資料頁面所需的網路來回行程次數。 有一個主要計算節點可以處理所有讀寫工作負載和交易。 有一或多個次要計算節點作為容錯移轉的熱待命節點，以及作為用於卸載讀取工作負載的唯讀計算節點 (如果想要使用此功能)。

在超大規模計算節點上執行的資料庫引擎與其他 Azure SQL Database 服務層級相同。 當使用者與超大規模計算節點上的 database engine 互動時，支援的介面區和引擎行為會與其他服務層級相同，但 [已知的限制](#known-limitations)除外。

### <a name="page-server"></a>頁面伺服器

頁面伺服器是代表相應放大儲存引擎的系統。  每部頁面伺服器都負責資料庫中的一部分頁面。  名義上，每個頁面伺服器最多可以控制 128 GB 或最多 1 TB 的資料。 在頁面伺服器複本以外的頁面伺服器複本上，不會有任何資料共用 (，而這些複本會保留以提供冗余和可用性) 。 頁面伺服器的工作是隨需提供計算節點的資料庫頁面，並隨著交易更新資料而更新頁面。 頁面伺服器會從記錄服務播放記錄檔記錄，以保持最新狀態。 頁面伺服器也會維護涵蓋以 SSD 為基礎的快取，以增強效能。 資料頁面的長期儲存體會保存在 Azure 儲存體中，以獲得額外的可靠性。

### <a name="log-service"></a>記錄服務

記錄服務會接受來自主要計算複本的記錄檔記錄，並將它們保存在永久性快取中，然後將記錄檔記錄轉送至其餘的計算複本 (讓它們可以更新其快取) 以及相關的頁面伺服器 () ，以便將資料更新至該處。 如此一來，來自主要計算複本的所有資料變更都會透過記錄服務傳播到所有次要計算複本和頁面伺服器。 最後，記錄檔記錄會推送至 Azure 儲存體的長期儲存空間，這是幾乎無限儲存的儲存機制。 這種機制可免除頻繁記錄截斷的需求。 記錄服務也有本機記憶體和 SSD 快取，以加速存取記錄檔記錄。

### <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體包含資料庫中的所有資料檔案。 頁面伺服器會將資料檔案保留在 Azure 儲存體的最新狀態。 此儲存體可用於備份用途，也可用於 Azure 區域之間的複寫。 備份是使用資料檔案的儲存體快照集來執行。 不論資料大小為何，使用快照集的還原作業都是快速的。 資料可還原到資料庫備份保留期間內的任何時間點。

## <a name="backup-and-restore"></a>備份與還原

備份是以檔案快照集為基礎，因此幾乎是瞬間的。 儲存體和計算分隔可讓您將備份/還原作業推送至儲存層，以降低主要計算複本的處理負擔。 因此，資料庫備份不會影響主要計算節點的效能。 同樣地，「時間點復原」 (PITR) 是藉由還原至檔案快照集來完成，因為這不是資料作業的大小。 在相同的 Azure 區域中還原超大規模資料庫是一項固定時間的作業，甚至可以在數分鐘內還原多 tb 的資料庫，而不是數小時或數天的時間。 藉由還原現有的備份來建立新的資料庫也會利用此功能：建立資料庫複本以供開發或測試之用（即使是多 tb 的資料庫），在幾分鐘內就會雖可行。

如需超大規模資料庫的異地還原，請參閱將 [超大規模資料庫還原到不同的區域](#restoring-a-hyperscale-database-to-a-different-region)。

## <a name="scale-and-performance-advantages"></a>規模和效能優點

超大規模資料庫架構可以快速加速/減速其他唯讀計算節點，因而允許大規模讀取功能，也可以釋出主要計算節點來提供更多寫入要求。 此外，基於超大規模資料庫架構的共用儲存體架構，也可以快速相應增加/減少計算節點。

## <a name="create-a-hyperscale-database"></a>建立超大規模資料庫

您可以使用 [Azure 入口網站](https://portal.azure.com)、 [t-sql](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql)、 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase)或 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)來建立超大規模資料庫。 超大規模資料庫只適用于以 [vCore 為基礎的購買模型](service-tiers-vcore.md)。

下列 T-SQL 命令會建立超大規模資料庫。 您必須在 `CREATE DATABASE` 陳述式中指定版本和服務目標。 請參閱 [資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) 以取得有效服務目標清單。

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

這將會在具有四個核心的第5代硬體上建立超大規模資料庫。

## <a name="upgrade-existing-database-to-hyperscale"></a>將現有的資料庫升級至超大規模

您可以使用 [Azure 入口網站](https://portal.azure.com)、 [t-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)、 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase)或 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)，將 Azure SQL Database 中的現有資料庫移至超大規模。 這次，這是單向的遷移。 您無法將資料庫從超大規模移至另一個服務層級，而不是藉由匯出和匯入資料。 針對 (Poc) 的概念證明，建議您建立生產資料庫的複本，並將複本遷移至超大規模。 將 Azure SQL Database 中的現有資料庫移轉至超大規模層是資料作業的大小。

下列 T-SQL 命令會將資料庫移至超大規模資料庫服務層級。 您必須在 `ALTER DATABASE` 陳述式中指定版本和服務目標。

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>連線到超大規模資料庫的讀取縮放複本

在超大規模資料庫中， `ApplicationIntent` 用戶端所提供之連接字串中的引數會指定連接是要路由至寫入複本還是唯讀次要複本。 如果 `ApplicationIntent` 設定為 `READONLY` 且資料庫沒有次要複本，連接將會路由傳送至主要複本，並預設為 `ReadWrite` 行為。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

超大規模次要複本全都相同，並使用與主要複本相同的服務等級目標。 如果有多個次要複本，則會將工作負載分散到所有可用的次要資料庫。 每個次要複本都會個別更新。 因此，不同的複本可能會有不同于主要複本的資料延遲。

## <a name="database-high-availability-in-hyperscale"></a>超大規模中的資料庫高可用性

如同所有其他服務層級，超大規模可保證已認可交易的資料持久性，而不考慮計算複本的可用性。 因為主要複本變成無法使用所造成的停機時間，取決於已規劃的容錯移轉類型 (計畫與未計畫的) ，以及至少有一個次要複本存在。 在規劃的容錯移轉 (也就是維護事件) 中，系統會先建立新的主要複本，然後再起始容錯移轉，或是使用現有的次要複本作為容錯移轉目標。 在未規劃的容錯移轉 (也就是主要複本) 的硬體故障時，系統會使用次要複本作為容錯移轉目標（如果有的話），或從可用計算容量集區建立新的主要複本。 在後者的情況下，停機時間會比較長，因為建立新的主要複本需要額外的步驟。

如需超大規模 SLA，請參閱 [Azure SQL Database 的 sla](https://azure.microsoft.com/support/legal/sla/sql-database/)。

## <a name="disaster-recovery-for-hyperscale-databases"></a>超大規模資料庫的嚴重損壞修復

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>將超大規模資料庫還原至不同區域

如果您需要將 Azure SQL Database 中的超大規模資料庫還原至目前所裝載的區域以外的區域，做為損毀修復作業或切入、重新配置或任何其他原因的一部分，主要方法是進行資料庫的異地還原。 這包含與您在 SQL Database 中將任何其他資料庫還原至不同區域時所用的完全相同步驟：

1. 如果您還沒有適當的伺服器，請在目的地區域中建立 [伺服器](logical-servers.md) 。  此伺服器應與原始 (源) 伺服器擁有相同的訂用帳戶。
2. 請依照頁面的 [異地還原](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) 主題中的指示，從自動備份還原 Azure SQL Database 中的資料庫。

> [!NOTE]
> 因為來源和目標位於不同的區域，所以資料庫無法與源資料庫共用快照集儲存體，就像在非異地還原中一樣，這會非常快速完成。 在異地還原超大規模資料庫的情況下，它將會是資料大小的作業，即使目標位於異地複寫儲存體的配對區域中也是如此。  這表示執行異地還原的時間會與要還原的資料庫大小成正比。  如果目標在配對的區域中，則複本將會在區域內，其速度會比跨區域複製快很多，但仍會是資料大小的作業。

## <a name="available-regions"></a><a name=regions></a>可用區域

Azure SQL Database 超大規模層適用于所有區域，但預設為啟用，如下所欄區域所提供。
如果您想要在未列為支援的區域中建立超大規模資料庫，您可以透過 Azure 入口網站傳送登入要求。 如需相關指示，請參閱 [Azure SQL Database 的要求配額增加](quota-increase-request.md) 以取得指示。 提交您的要求時，請使用下列指導方針：

- 使用 [區域存取](quota-increase-request.md#region) SQL Database 配額類型。
- 在 [文字詳細資料] 中，新增計算 SKU/總核心數，包括可讀取的複本。
- 也請指定估計的 TB。

啟用的區域：
- 澳大利亞東部
- 澳大利亞東南部
- 澳大利亞中部
- 巴西南部
- 加拿大中部
- 美國中部
- 中國東部 2
- 中國北部 2
- 東亞
- 美國東部
- 美國東部2
- 法國中部
- 德國中西部
- 日本東部
- 日本西部
- 南韓中部
- 南韓南部
- 美國中北部
- 北歐
- 挪威東部
- 挪威西部
- 南非北部
- 美國中南部
- 東南亞
- 瑞士西部
- 英國南部
- 英國西部
- US DoD 中部
- US DoD 東部
- Us Govt 亞利桑那州
- US Govt 德克薩斯州
- 美國中西部
- 西歐
- 美國西部
- 美國西部 2

## <a name="known-limitations"></a>已知的限制

這些是超大規模服務層級目前在 GA 的限制。  我們正積極地盡可能移除這些限制。

| 問題 | 描述 |
| :---- | :--------- |
| 伺服器的 [管理備份] 窗格不會顯示超大規模資料庫。 這些將會從視圖進行篩選。  | 超大規模有個別的方法可管理備份，因此不適用 Long-Term 保留和時間點備份保留設定。 因此，超大規模資料庫不會出現在 [管理備份] 窗格中。<br><br>針對從其他 Azure SQL Database 服務層級遷移至超大規模的資料庫，系統會在源資料庫的 [備份保留](automated-backups-overview.md#backup-retention) 期間內保留預先遷移備份。 這些備份可以用來將源資料庫 [還原](recovery-using-backups.md#programmatic-recovery-using-automated-backups) 到遷移之前的時間點。|
| 時間點還原 | 非超大規模資料庫無法還原為超大規模資料庫，且超大規模資料庫無法還原為非超大規模資料庫。 針對已藉由變更其服務層級遷移至超大規模的非超大規模資料庫，請在遷移之前，還原至資料庫的備份保留期限內的時間[點。](recovery-using-backups.md#programmatic-recovery-using-automated-backups) 還原的資料庫將不會超大規模。 |
| 如果資料庫有一或多個資料檔案大於 1 TB，則遷移會失敗 | 在某些情況下，可能可以藉由將大型檔案壓縮為小於 1 TB 來解決此問題。 如果遷移過程中正在使用的資料庫，請確定沒有任何檔案超過 1 TB。 您可以使用下列查詢來判斷資料庫檔案的大小。 `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL 受控執行個體 | 超大規模資料庫目前不支援 Azure SQL 受控執行個體。 |
| 彈性集區 |  超大規模目前不支援彈性集區。|
| 移轉至超大規模資料庫模目前是單向作業 | 一旦資料庫移轉至超大規模之後，就無法直接遷移至非超大規模服務層級。 目前將資料庫從超大規模遷移至非超大規模的唯一方法，是使用 bacpac 檔案或其他資料移動技術來匯出/匯入 (大量複製、Azure Data Factory、Azure Databricks、SSIS 等 ) Bacpac 匯出/匯入、從 Azure 入口網站使用 [AzSqlDatabaseExport](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseexport) 或 [AzSqlDatabaseImport](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseimport)、從 Azure CLI 使用 [az sql db 匯出](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-export) 和 [az sql db 匯入](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-import)，以及從 [REST API](https://docs.microsoft.com/rest/api/sql/databases%20-%20import%20export) 不受支援。 使用 SSMS 和 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 18.4 版和更新版本可支援較小超大規模資料庫的 Bacpac 匯入/匯出 (高達 200 GB 的) 。 針對較大的資料庫，bacpac 匯出/匯入可能需要很長的時間，而且可能會因各種原因而失敗。|
| 使用 In-Memory OLTP 物件遷移資料庫 | 超大規模支援 In-Memory OLTP 物件的子集，包括記憶體優化資料表類型、資料表變數和原生編譯模組。 不過，當要遷移的資料庫中有任何種類的 In-Memory OLTP 物件時，不支援從高階和業務關鍵服務層級遷移至超大規模。 若要將這類資料庫移轉至超大規模，必須卸載所有 In-Memory OLTP 物件及其相依性。 遷移資料庫之後，就可以重新建立這些物件。 超大規模目前不支援持久性和非持久性記憶體優化資料表，且必須重新建立為磁片資料表。|
| 異地複寫  | 您還無法設定 Azure SQL Database 超大規模的異地複寫。 |
| 資料庫複製 | 超大規模上的資料庫複製現在處於公開預覽狀態。 |
| TDE/AKV 整合 | 使用 Azure Key Vault (的透明資料庫加密通常稱為「自備金鑰」或「BYOK) 」，目前處於公開預覽狀態。 |
| 智慧型資料庫功能 | 除了 [強制執行計畫] 選項之外，超大規模上還不支援所有其他自動調整選項：可能會顯示選項已啟用，但不會有任何建議或動作。 |
| 查詢效能深入解析 | 超大規模資料庫目前不支援查詢效能深入解析。 |
| 壓縮資料庫 | 超大規模資料庫目前不支援 DBCC SHRINKDATABASE 或 DBCC SHRINKFILE。 |
| 資料庫完整性檢查 | 超大規模資料庫目前不支援 DBCC CHECKDB。 DBCC CHECKFILEGROUP 和 DBCC CHECKTABLE 可以用來做為因應措施。 如需 Azure SQL Database 中資料完整性管理的詳細資訊，請參閱 [Azure SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) 。 |

## <a name="next-steps"></a>後續步驟

- 如需超大規模資料庫的常見問題集，請參閱[關於超大規模資料庫的常見問題集](service-tier-hyperscale-frequently-asked-questions-faq.md)。
- 如需服務層的詳細資訊，請參閱 [服務層級](purchasing-models.md)
- 如需有關伺服器和訂閱層級之限制的詳細資訊，請參閱 [伺服器上的資源限制總覽](resource-limits-logical-server.md) 。
- 如需單一資料庫的購買模型限制相關資訊，請參閱[適用於單一資料庫的 Azure SQL Database 以虛擬核心為基礎的購買模型限制](resource-limits-vcore-single-databases.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](features-comparison.md)。
 
