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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255830"
---
# <a name="hyperscale-service-tier"></a>超大規模資料庫服務層級

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：
- 一般目的/標準 
-  超大規模資料庫
-  業務關鍵/進階

Azure SQL Database 中的超大規模服務層是以虛擬核心為基礎的購買模型中的最新服務層。 此服務層級是可高度擴充的儲存體和計算效能層，可利用 Azure 架構以相應放大 Azure SQL Database 的儲存體和計算資源，而大幅超過一般用途和商務關鍵性服務層級的可用限制。

> 
> [!NOTE]
> 有關基於 vCore 的採購模型中的通用和業務關鍵型服務層的詳細資訊，請參閱[通用](sql-database-service-tier-general-purpose.md)和[業務關鍵型](sql-database-service-tier-business-critical.md)服務層。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-service-tiers.md)。


## <a name="what-are-the-hyperscale-capabilities"></a>超大規模資料庫功能有哪些

Azure SQL Database 中的超大規模資料庫服務層級提供下列額外功能：

- 支援最多 100 TB 的資料庫大小
- 幾乎即時的資料庫備份（基於存儲在 Azure Blob 存儲中的檔快照），無論大小大小，對計算資源沒有 IO 影響  
- 快速的資料庫還原 (根據檔案快照集)，僅需數分鐘，而非數小時或數天 (不是資料作業的大小)
- 不論資料量為何，較高的記錄輸送量和較快的交易認可時間就會有較高的整體效能
- 快速相應放大 - 您可以佈建一或多個唯讀節點來卸載讀取工作負載，並用來作為熱待命
- 快速相應增加 - 您可以在有限的時間內視需要和在需要時相應增加計算資源以容納大量工作負載，然後在不需要時相應減少計算資源。

超大規模資料庫服務層級會移除傳統上會在雲端資料庫中看到的許多實際限制。 大部分其他資料庫都受限於單一節點中的可用資源，但超大規模資料庫服務層級中的資料庫沒有這類限制。 透過其彈性儲存體架構，儲存體可依需求成長。 事實上，不會建立具有所定義大小上限的超大規模資料庫服務層級。 超大規模資料庫服務層級會視需要成長，而且只會向您收取所使用容量的費用。 針對大量讀取工作負載，超大規模資料庫服務層級會視需要佈建額外的讀取複本來卸載讀取工作負載，以提供快速相應放大。

此外，建立資料庫備份或是相應增加或減少所需的時間，不再繫結到資料庫中資料的磁碟區。 超大規模資料庫服務層級幾乎可以立即予以備份。 您也可以在數分鐘內相應增加或減少數十個 TB 的資料庫。 此功能讓您不需要擔心選擇的初始設定進行方塊化處理。

如需超大規模資料庫服務層級計算大小的詳細資訊，請參閱[服務層級特性](sql-database-service-tiers-vcore.md#service-tiers)。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>誰應該考慮使用超大規模資料庫服務層級

Hyperscale 服務層適用于大多數業務工作負載，因為它具有高度的靈活性和高性能，具有獨立可擴展的計算和存儲資源。 能夠自動擴展高達 100 TB 的存儲，對於以下客戶來說，這是一個很好的選擇：

- 在本地擁有大型資料庫，並希望通過遷移到雲來實現應用程式的現代化
- 已在雲中，並且受其他服務層的最大資料庫大小限制（1-4 TB） 的限制
- 資料庫較小，但需要快速垂直和水準計算擴展、高性能、即時備份和快速資料庫還原。

Hyperscale 服務層支援從純 OLTP 到純分析的廣泛 SQL Server 工作負載，但它主要針對 OLTP 和混合事務和分析處理 （HTAP） 工作負載進行了優化。

> [!IMPORTANT]
> 彈性集區不支援超大規模資料庫服務層級。

## <a name="hyperscale-pricing-model"></a>超大規模資料庫定價模型

超大規模資料庫服務層級僅在[虛擬核心模型](sql-database-service-tiers-vcore.md)中提供。 為配合新的架構，定價模型會與一般用途或業務關鍵服務層級稍微不同：

- **計算**：

  超大規模資料庫計算單位價格是按每個複本計算。 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 價格會自動套用到讀取縮放複本。 預設情況下，我們每個超大規模資料庫創建一個主副本和一個唯讀副本。  使用者可以將包括主副本在內的副本總數從 1-5 調整。

- **存放裝置**：

  您設定超大規模資料庫時，不需要指定資料大小上限。 在超大規模層中，您需要根據實際分配為資料庫收取存儲費用。 存儲自動分配在 40 GB 和 100 TB 之間，以 10 GB 的增量 10 GB 分配。 如果需要，多個資料檔案可以同時增長。 創建起始大小為 10 GB 的超大規模資料庫，每 10 分鐘開始增加 10 GB，直到達到 40 GB 的大小。

如需有關超大規模定價的詳細資訊，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>分散式函式架構

傳統資料庫引擎將所有資料管理功能都集中到一個位置/處理序 (因此，生產環境中的分散式資料庫現在會有多份整合型資料引擎)，但超大規模資料庫不同，會具有不同的查詢處理引擎，而各種資料引擎的語意都會與提供資料長期儲存和持久性的元件不同。 因此，只要需要，就可以順暢地相應放大儲存體容量 (初始目標為 100 TB)。 唯讀副本共用相同的存儲元件，因此無需資料副本即可啟動新的可讀副本。 

下圖說明超大規模資料庫中不同類型的節點：

![架構](./media/sql-database-hyperscale/hyperscale-architecture.png)

超大規模資料庫包含以下不同類型的元件：

### <a name="compute"></a>計算

計算節點就是關聯式引擎的所在位置，因此會發生所有語言元素、查詢處理等等。 所有與超大規模資料庫的使用者互動都是透過這些計算節點進行。 計算節點具有 SSD 快取 (在上圖中標示為 RBPEX (復原緩衝集區延伸模組))，可減少擷取資料頁面所需的網路來回行程次數。 有一個主要計算節點可以處理所有讀寫工作負載和交易。 有一或多個次要計算節點作為容錯移轉的熱待命節點，以及作為用於卸載讀取工作負載的唯讀計算節點 (如果想要使用此功能)。

### <a name="page-server"></a>頁面伺服器

頁面伺服器是代表相應放大儲存引擎的系統。  每部頁面伺服器都負責資料庫中的一部分頁面。  名義上，每個頁面伺服器控制 128 GB 和 1 TB 的資料。 未在多部頁面伺服器上共用資料 (在保留以獲得備援和可用性的複本外部)。 頁面伺服器的工作是隨需提供計算節點的資料庫頁面，並隨著交易更新資料而更新頁面。 從記錄服務播放記錄檔記錄，以保持最新的頁面伺服器。 頁面伺服器也會維護 SSD 快取以提升效能。 資料頁面的長期儲存體會保存在 Azure 儲存體中，以獲得額外的可靠性。

### <a name="log-service"></a>日誌服務

日誌服務接受主計算副本中的日誌記錄，將它們保存在持久緩存中，並將日誌記錄轉發到其餘計算副本（以便它們可以更新其緩存）以及相關的頁面伺服器，以便可以更新資料那裡。 這樣，來自主計算副本的所有資料更改都通過日誌服務傳播到所有輔助計算副本和頁面伺服器。 最後，日誌記錄被推送到 Azure 存儲中的長期存儲，Azure 存儲幾乎是無限的存儲存儲庫。 此機制消除了頻繁截斷日誌的需要。 日誌服務還具有本機快取，以加快對日誌記錄的存取速度。

### <a name="azure-storage"></a>Azure 儲存體

Azure 存儲包含資料庫中的所有資料檔案。 頁面伺服器使 Azure 存儲中的資料檔案保持最新。 此存儲用於備份目的，以及用於 Azure 區域之間的複製。 備份使用資料檔案的存儲快照實現。 無論資料大小如何，使用快照還原操作都很快。 資料可以在資料庫的備份保留期內還原到任何時間點。

## <a name="backup-and-restore"></a>備份與還原

備份基於檔快照，因此幾乎是暫態的。 存儲和計算分離能夠將備份/還原操作向下推送到存儲層，以減輕主計算副本的處理負擔。 因此，資料庫備份不會影響主計算節點的性能;同樣，還原是通過還原到檔快照來完成的，因此不是資料操作的大小。 還原是一個恒定時間操作，甚至多個 TB 資料庫也可以在幾分鐘內恢復，而不是幾小時或幾天。 通過還原現有備份創建新資料庫還利用了此功能：為開發或測試目的創建資料庫副本，即使是 TB 大小的資料庫，也只需幾分鐘即可完成。

## <a name="scale-and-performance-advantages"></a>規模和效能優點

超大規模資料庫架構可以快速加速/減速其他唯讀計算節點，因而允許大規模讀取功能，也可以釋出主要計算節點來提供更多寫入要求。 此外，基於超大規模資料庫架構的共用儲存體架構，也可以快速相應增加/減少計算節點。

## <a name="create-a-hyperscale-database"></a>創建超大規模資料庫

可以使用[Azure 門戶](https://portal.azure.com)[、T-SQL、PowerShell](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)或[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase)[CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)創建超大規模資料庫。 超大規模資料庫僅使用[基於 vCore 的採購模型](sql-database-service-tiers-vcore.md)可用。

下列 T-SQL 命令會建立超大規模資料庫。 您必須在 `CREATE DATABASE` 陳述式中指定版本和服務目標。 有關有效服務目標清單，請參閱[資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4)。

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
這將在具有 4 個內核的 Gen5 硬體上創建一個超大規模資料庫。

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>將現有的 Azure SQL Database 遷移至超大規模資料庫服務層級

您可以使用[Azure 門戶](https://portal.azure.com)[、T-SQL、PowerShell](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)或[CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)將現有的[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase)Azure SQL 資料庫移動到超大規模。 此時，這是單向遷移。 除了匯出和導入資料之外，無法將資料庫從 Hyperscale 移動到其他服務層。 對於概念證明 （POC），我們建議複製生產資料庫，並將副本遷移到 Hyperscale。 將現有的 Azure SQL 資料庫遷移到超大規模層是資料操作的大小。

下列 T-SQL 命令會將資料庫移至超大規模資料庫服務層級。 您必須在 `ALTER DATABASE` 陳述式中指定版本和服務目標。

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>連線到超大規模資料庫的讀取縮放複本

在超大規模資料庫中，用戶端`ApplicationIntent`提供的連接字串中的參數指示連接是路由到寫入副本還是唯讀輔助副本。 如果 `ApplicationIntent` 設為 `READONLY`，且資料庫沒有次要複本，連線將會路由至主要複本，並預設為 `ReadWrite` 行為。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

超大規模輔助副本都相同，使用與主副本相同的服務等級目標。 如果存在多個輔助副本，則工作負載將分佈在所有可用的輔助副本中。 每個輔助副本都獨立更新，因此不同的副本相對於主副本可能有不同的資料延遲。

## <a name="database-high-availability-in-hyperscale"></a>資料庫超大規模高可用性

與所有其他服務層一樣，Hyperscale 可確保提交事務的資料持久性，而不考慮計算副本的可用性。 主副本不可用導致的停機時間範圍取決於容錯移轉的類型（計畫與計畫外），以及是否存在至少一個輔助副本。 在計畫容錯移轉（即維護事件）中，系統在啟動容錯移轉之前創建新的主副本，或者使用現有的輔助副本作為容錯移轉目標。 在計畫外容錯移轉（即主副本上的硬體故障）中，如果存在，系統將使用輔助副本作為容錯移轉目標，或者從可用計算容量池創建新的主副本。 在後一種情況下，由於創建新主副本需要額外的步驟，停機時間持續時間更長。

對於超大規模 SLA，請參閱[Azure SQL 資料庫的 SLA。](https://azure.microsoft.com/support/legal/sla/sql-database/)

## <a name="disaster-recovery-for-hyperscale-databases"></a>超大規模資料庫的災害復原

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>將超大規模資料庫還原到其他地理位置
如果需要將 Azure SQL 資料庫超大規模資料庫還原到當前託管的區域以外的區域，作為災害復原操作或演練、重定位或任何其他原因的一部分，主要方法是對資料庫進行地理還原。  這涉及的步驟與將任何其他 AZURE SQL DB 還原到其他區域的步驟完全相同：
1. 如果目的地區域中尚未有適當的伺服器，則在目的地區域中創建 SQL 資料庫伺服器。  此伺服器應由與原始（源）伺服器相同的訂閱擁有。
2. 按照頁面的[地理還原](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore)主題中有關從自動備份還原 Azure SQL 資料庫的說明進行操作。

> [!NOTE]
> 由於源和目標位於單獨的區域中，因此資料庫無法與源資料庫共用快照存儲，就像在非地理還原中那樣，後者完成得非常快。 對於超大規模資料庫的地理還原，即使目標位於異地複製存儲的配對區域，它也是資料大小操作。  這意味著進行地理還原需要時間與要還原的資料庫的大小成比例。  如果目標位於配對區域中，則副本將位於區域內，該區域將比跨區域副本快得多，但仍是資料大小操作。

## <a name="available-regions"></a><a name=regions></a>可用區域

Azure SQL 資料庫超大規模層當前在以下區域可用：

- 澳大利亞東部
- 澳大利亞東南部
- 巴西南部
- 加拿大中部
- 美國中部
- 中國東部 2
- 中國北部 2
- 東亞
- 美國東部
- 東我們 2
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

如果要在未列為受支援的區域創建超大規模資料庫，可以通過 Azure 門戶發送載入請求。 有關說明，請參閱[請求 Azure SQL 資料庫增加](quota-increase-request.md)指令的配額。 提交請求時，請使用以下準則：

- 使用[其他配額請求](quota-increase-request.md#other)SQL 資料庫配額類型。
- 在文本詳細資訊中，添加計算 SKU/總計內核，包括可讀副本。
- 還指定估計的結核病。

## <a name="known-limitations"></a>已知限制

這些是截至 GA 的超大規模服務層的當前限制。  我們正在積極努力消除盡可能多的這些限制。

| 問題 | 描述 |
| :---- | :--------- |
| 邏輯伺服器的"管理備份"窗格不顯示將從 SQL 伺服器篩選超大規模資料庫  | Hyperscale 具有單獨的管理備份的方法，因此長期保留和時間點備份保留設置不適用 /無效。 據此，超大規模資料庫不會出現在 [管理備份] 窗格中。 |
| 時間點還原 | 將資料庫移轉到 Hyperscale 服務層後，不支援在遷移之前還原到時間點。|
| 將非超大規模資料庫還原到超大規模，反之亦然 | 不能將超大規模資料庫還原到非超大規模資料庫，也不能將非超大規模資料庫還原到超大規模資料庫中。|
| 如果資料庫具有大於 1 TB 的一個或多個資料檔案，則遷移失敗 | 在某些情況下，可以通過將大型檔縮小到小於 1 TB 來解決此問題。 如果在遷移過程中遷移正在使用的資料庫，請確保沒有檔大於 1 TB。 使用以下查詢確定資料庫檔案的大小。 `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| 受控執行個體 | 超大規模資料庫當前不支援 Azure SQL 資料庫託管實例。 |
| 彈性集區 |  SQL 資料庫超大規模當前不支援彈性池。|
| 移轉至超大規模資料庫模目前是單向作業 | 一旦資料庫遷移至超大規模資料庫後，就無法直接遷移至非超大規模資料庫服務層級。 目前，將資料庫從超大規模遷移到非超大規模的唯一方法是使用 BACPAC 檔或其他資料移動技術（批量複製、Azure 資料工廠、Azure 資料塊、SSIS 等）匯出/導入。|
| 具有持久記憶體中物件的資料庫的遷移 | 超大規模僅支援非持久性記憶體內物件（表類型、本機 SP 和函數）。  在將資料庫移轉到超大規模服務層之前，必須刪除持久性記憶體內表和其他物件並將其重新創建為非記憶體中物件。|
| 變更追蹤 | 更改跟蹤當前處於公共預覽版中，可在新資料庫或現有超大規模資料庫上啟用。 |
| 地理複製  | 您還不能為 Azure SQL 資料庫超大規模配置異地複製。 |
| 資料庫複製 | 您還不能使用資料庫副本在 Azure SQL 超大規模中創建新資料庫。 |
| TDE/AKV 集成 | Azure SQL 資料庫超大規模尚不支援使用 Azure 金鑰保存庫（通常稱為自帶金鑰或 BYOK）的透明資料庫加密，但完全支援具有服務託管金鑰的 TDE。 |
|智慧資料庫功能 | 除了"強制計畫"選項外，Hyperscale 上還不支援所有其他自動調優選項：選項可能看起來已啟用，但不會提出任何建議或操作。 |
|查詢效能深入解析 | 超大規模資料庫當前不支援查詢性能見解。 |
| 壓縮資料庫 | 超大規模資料庫當前不支援 DBCC SHRINK 資料庫或 DBCC SHRINKFILE。 |
| 資料庫完整性檢查 | 超大規模資料庫當前不支援 DBCC CHECKDB。 有關 Azure SQL 資料庫中的資料完整性的詳細資訊，請參閱[Azure SQL 資料庫中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。 |

## <a name="next-steps"></a>後續步驟

- 如需超大規模資料庫的常見問題集，請參閱[關於超大規模資料庫的常見問題集](sql-database-service-tier-hyperscale-faq.md)。
- 有關服務層的資訊，請參閱[服務層](sql-database-service-tiers.md)
- 如需伺服器和訂用帳戶層級的限制資訊，請參閱[邏輯伺服器上的資源限制概觀](sql-database-resource-limits-logical-server.md)。
- 如需單一資料庫的購買模型限制相關資訊，請參閱[適用於單一資料庫的 Azure SQL Database 以虛擬核心為基礎的購買模型限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
