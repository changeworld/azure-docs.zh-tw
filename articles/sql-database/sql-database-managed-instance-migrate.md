---
title: 從 SQL Server 遷移至受控實例
description: 瞭解如何將資料庫從 SQL Server 實例遷移至 Azure SQL Database 管理的實例。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 1af0161edb0f833cdd14d8157e6edd9644e21467
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100272"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>將 SQL Server 遷移至 Azure SQL Database 受控執行個體

在本文中，您將瞭解將 SQL Server 2005 或更新版本實例遷移至[Azure SQL Database 受控實例](sql-database-managed-instance.md)的方法。 如需移轉至單一資料庫或彈性集區的相關資訊，請參閱[移轉至單一或集區資料庫](sql-database-cloud-migrate.md)。 如需從其他平台移轉的移轉資訊，請參閱 [Azure 資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\)。

> [!NOTE]
> 如果您想要快速啟動並嘗試受控執行個體，您可能會想要移至 [[快速入門手冊](sql-database-managed-instance-quickstart-guide.md)]，而不是此頁面。 

概括而言，資料庫移轉程序看起來像這樣：

![移轉程序](./media/sql-database-managed-instance-migration/migration-process.png)

- [評估受控實例的相容性](#assess-managed-instance-compatibility)，您應該在此確保不會有封鎖問題可防止您的遷移。
  - 此步驟也包括建立[效能基準](#create-performance-baseline)，以判斷來源 SQL Server 實例的資源使用量。 如果您想要部署適當大小的受控執行個體並確認遷移後的效能不受影響，則需要此步驟。
- [選擇應用程式連線選項](sql-database-managed-instance-connect-app.md)
- [部署到最佳大小的受控實例](#deploy-to-an-optimally-sized-managed-instance)，您將在其中選擇受控執行個體的技術特性（虛擬核心數目、記憶體數量）和效能層級（業務關鍵、一般用途）。
- [選取 [遷移方法] 和 [遷移](#select-migration-method-and-migrate)]，使用離線遷移（原生備份/還原、資料庫 importe/匯出）或線上遷移（資料移轉服務、異動複寫）來遷移資料庫。
- [監視應用程式](#monitor-applications)，以確保您有預期的效能。

> [!NOTE]
> 若要將個別資料庫遷移至單一資料庫或彈性集區，請參閱[將 SQL Server 資料庫遷移至 Azure SQL Database](sql-database-single-database-migrate.md)。

## <a name="assess-managed-instance-compatibility"></a>評估受控執行個體的相容性

首先，判斷受控執行個體是否與您應用程式的資料庫需求相容。 受控執行個體部署選項旨在為大部分內部部署或虛擬機器上使用 SQL Server 的現有應用程式，提供簡單的隨即轉移。 不過，有時候您可能需要尚未支援的功能或功能，而且執行因應措施的成本太高。

使用[資料移轉小幫手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview)，可偵測影響 Azure SQL Database 資料庫功能的潛在相容性問題。 DMA 尚不支援將受控執行個體做為移轉目的地，但建議您針對 Azure SQL Database 執行評估，並針對產品文件，仔細檢閱提報的功能同位和相容性問題清單。 請參閱[Azure SQL Database 功能](sql-database-features.md)以檢查是否有一些在受控實例中無法封鎖的已報告封鎖問題，因為導致無法遷移 Azure SQL Database 的大部分封鎖問題都已透過受控實例移除。 例如跨資料庫查詢、相同執行個體內的跨資料庫交易、其他 SQL 來源的連結伺服器、CLR、全域暫存資料表、執行個體層級檢視、Service Broker 等功能皆可在受控執行個體中使用。

若受控制執行個體部署選項並未移除一些回報的執行問題，您可能需要考慮替代選項，例如 [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 以下是一些範例：

- 如果您需要直接存取作業系統或檔案系統，例如在具有 SQL Server 的相同虛擬機器上安裝第三方或自訂代理程式。
- 如果您的執行個體與尚不支援的功能有緊密相依性，例如 FileStream / FileTable、PolyBase 及跨執行個體交易等功能。
- 如果絕對需要保持在特定版本的 SQL Server （例如，2012）。
- 如果您的計算需求遠低於受控執行個體提供的功能 (例如，只需要一個虛擬核心)，而且資料庫彙總不是可接受的選項。

如果您已解決所有已識別的遷移封鎖器，並繼續遷移至受控執行個體，請注意某些變更可能會影響工作負載的效能：
- 必要的完整復原模式和定期自動備份排程可能會影響工作負載的效能，或是定期使用簡單/大量記錄模型或視需要停止備份的維護/ETL 動作。
- 不同的伺服器或資料庫層級設定，例如追蹤旗標或相容性層級
- 您所使用的新功能（例如透明資料庫加密（TDE）或自動容錯移轉群組）可能會影響 CPU 和 IO 使用量。

受控執行個體保證99.99% 的可用性（即使在重大案例中），因此無法停用這些功能所造成的額外負荷。 如需詳細資訊，請參閱[可能會在 SQL Server 和受控執行個體上造成不同效能的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。

### <a name="create-performance-baseline"></a>建立效能基準

如果您需要將受控執行個體上工作負載的效能與 SQL Server 上執行的原始工作負載進行比較，您必須建立將用於比較的效能基準。 

效能基準是一組參數，例如平均/最大 CPU 使用量、平均/最大磁片 IO 延遲、輸送量、IOPS、平均/最大分頁生命週期，以及 tempdb 的平均大小上限。 在遷移之後，您會想要有類似或更好的參數，因此請務必測量並記錄這些參數的基準值。 除了系統參數以外，您還需要選取一組代表查詢或工作負載中最重要的查詢，並測量所選查詢的最小/平均/最大持續時間、CPU 使用量。 這些值可讓您比較在受控執行個體上執行的工作負載效能與來源 SQL Server 上的原始值。

您需要在 SQL Server 實例上測量的部分參數如下： 
- [監視 SQL Server 實例的 CPU 使用量](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)，並記錄平均和尖峰 CPU 使用量。
- [監視 SQL Server 實例的記憶體使用量](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage)，並判斷不同元件所使用的記憶體數量，例如緩衝集區、計畫快取、資料行存放集區、[記憶體內部 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)等等。此外，您應該會找到 [Page Life 期望值 memory] 效能計數器的平均值和尖峰值。
- 使用[sys.databases dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) view 或[效能計數器](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)，監視來源 SQL SERVER 實例的磁片 IO 使用量。
- 藉由檢查動態管理檢視或查詢存放區（如果您要從 SQL Server 2016 + 版本進行遷移），來監視工作負載和查詢效能或 SQL Server 實例。 識別工作負載中最重要查詢的平均持續時間和 CPU 使用量，以與受控執行個體上執行的查詢進行比較。

> [!Note]
> 如果您注意到 SQL Server 的工作負載有任何問題，例如高 CPU 使用率、記憶體壓力、tempdb 或參數化問題，您應該先嘗試在來源 SQL Server 實例上加以解決，再進行基準和遷移。 將已知問題遷移至任何新的系統 migh 會導致非預期的結果，並使任何效能比較失效。

作為此活動的結果，您應該已記載來源系統上的 CPU、記憶體和 IO 使用量的平均和尖峰值，以及主要和工作負載中最重要查詢的平均和最大持續時間和 CPU 使用量。 您稍後應該使用這些值來比較受控執行個體的工作負載效能與來源 SQL Server 上工作負載的基準效能。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到最佳大小的受控執行個體

受控執行個體專為打算移至雲端的內部工作負載量身訂做。 它引進[新的購買模型](sql-database-service-tiers-vcore.md)，提供更大的彈性來選取適合您工作負載的正確資源層級。 在內部部署的環境中，您可能習慣使用實體核心數目與 IO 頻寬來調整這些工作負載大小。 受控執行個體的購買模型是以虛擬核心 (vCore) 為基礎，再個別加上額外儲存體與可用 IO。 相對於目前使用的內部部署方案，VCore 模型可讓您較簡單地了解雲端中的計算需求。 這個新模型可讓您在雲端中具有正確大小的目的地環境。 以下說明一些可協助您選擇正確服務層級和特性的一般指導方針：
- 根據基準 CPU 使用量，您可以布建符合您在 SQL Server 上使用之核心數目的受控執行個體，但請注意，CPU 特性可能需要調整，以符合[安裝受控執行個體的 VM 特性](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
- 根據 [基準記憶體使用量]，選擇[具有相符記憶體的服務層級](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。 記憶體數量無法直接選擇，因此您必須選取具有相符記憶體的虛擬核心數量的受控執行個體（例如第5代中的 5.1 GB/vCore）。 
- 根據檔案子系統的基準 IO 延遲，選擇一般用途（延遲大於5毫秒）和商務關鍵服務層級（延遲時間小於3毫秒）。
- 根據基準輸送量，預先配置資料或記錄檔的大小，以取得預期的 IO 效能。

您可以在部署期間選擇計算和儲存體資源，然後在不使用[Azure 入口網站](sql-database-scale-resources.md)的情況下變更應用程式的停機時間：

![受控執行個體大小](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

若要瞭解如何建立 VNet 基礎結構和受控實例，請參閱[建立受控實例](sql-database-managed-instance-get-started.md)。

> [!IMPORTANT]
> 請務必讓目的地 VNet 和子網永遠符合[受控實例的 vnet 需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 任何相容性問題都可能會讓您無法建立新的執行個體，或使用已經建立的執行個體。 深入了解[建立新的 ](sql-database-managed-instance-create-vnet-subnet.md) 並[ 設定現有的 ](sql-database-managed-instance-configure-vnet-subnet.md) 網路。

## <a name="select-migration-method-and-migrate"></a>選取移轉方法並進行遷移

受控執行個體部署選項鎖定的是需要將大量資料庫從內部部署或 IaaS 資料庫實作移轉的使用者案例。 如果您需要隨即轉移定期使用執行個體層級和/或跨資料庫功能的應用程式後端，那麼這是最佳選擇。 如果這是您的情況，您可以將整個執行個體移動至 Azure 中對應的環境，而不需要重新建構您的應用程式。

若要移動 SQL 執行個體，您需要謹慎規劃下列作業：

- 移轉必須共置的所有資料庫 (在相同執行個體上執行)
- 移轉您應用程式依賴的執行個體層級物件，包括登入、認證、SQL Agent 作業和運算子，以及伺服器層級觸發程序。

受控執行個體是受控的服務，可讓您將一些固定的 DBA 活動委派至平台，因為這些活動已內建。 因此，某些執行個體層級的資料就不需要遷移，例如，定期備份的維護作業或 Alwayson 組態，因為已內建[高可用性](sql-database-high-availability.md)。

受控執行個體支援下列資料庫移轉選項 (這些是目前唯一支援的移轉方法)：

- Azure 資料庫移轉服務 - 幾乎零停機時間的移轉。
- 原生`RESTORE DATABASE FROM URL` - 從 SQL Server 使用原生備份且需要一些停機時間。

### <a name="azure-database-migration-service"></a>Azure 資料庫移轉服務

[Azure 資料庫移轉服務 (DMS)](../dms/dms-overview.md) 是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 此服務可簡化將現有第三方和 SQL Server 資料庫移動至 Azure 時所需的工作。 公開預覽中的部署選項包括 Azure SQL Database 中的資料庫，以及 Azure 虛擬機器中的 SQL Server 資料庫。 DMS 是移轉企業工作負載的建議方法。

如果您在內部部署 SQL Server 上使用 SQL Server Integration Services (SSIS)，DMS 尚未支援移轉 SSIS 目錄 (SSISDB)，該目錄儲存 SSIS 套件，但是您可以在 Azure Data Factory (ADF) 中佈建 Azure-SSIS Integration Runtime (IR)，這樣會在受控執行個體中建立新的 SSISDB，然後您可以將套件重新部署至其中，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

若要深入瞭解適用于 DMS 的此案例和設定步驟，請參閱[使用 dms 將內部部署資料庫移轉至受控實例](../dms/tutorial-sql-server-to-managed-instance.md)。  

### <a name="native-restore-from-url"></a>從 URL 原生還原

從 SQL Server 內部部署環境或[虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 產生的原生備份 (.bak 檔案) (可從 [Azure 儲存體](https://azure.microsoft.com/services/storage/)取得) 進行還原，是受控執行個體部署選項的重要功能之一，讓您可以快速且輕鬆地進行離線資料庫移轉。

下圖會提供程序的高階概觀：

![移轉流程](./media/sql-database-managed-instance-migration/migration-flow.png)

下表詳述根據您執行的來源 SQL Server 版本，可以使用的方法：

|步驟|SQL 引擎和版本|備份 / 還原方法|
|---|---|---|
|將備份放至 Azure 儲存體|SQL 2012 SP1 CU2 之前的版本|直接將 .bak 檔案上傳到 Azure 儲存體|
||2012 SP1 CU2 - 2016|使用已被取代的 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 語法直接備份|
||2016 和更新版本|使用 [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 直接備份|
|從 Azure 儲存體還原至受控執行個體|[使用 SAS 認證從 URL 還原](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - 使用原生還原選項將受到[透明資料加密](transparent-data-encryption-azure-sql.md)保護的資料庫遷移到受控執行個體時，來自內部部署或 IaaS SQL Server 的對應憑證必須在資料庫還原之前進行遷移。 如需詳細步驟，請參閱[將 TDE Cert 遷移至受控實例](sql-database-managed-instance-migrate-tde-certificate.md)
> - 不支援系統資料庫還原。 若要移轉執行個體層級物件 (儲存在 master 或 msdb 資料庫中)，我們建議透過指令碼來找出這些物件，並在目的地執行個體上執行 T-SQL 指令碼。

如需說明如何使用 SAS 認證將資料庫備份還原至受控實例的快速入門，請參閱[從備份還原至受控實例](sql-database-managed-instance-get-started-restore.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>監視應用程式

完成遷移至受控執行個體之後，您應該追蹤工作負載的應用程式行為和效能。 此套裝程式含下列活動：
- [比較在受控執行個體上執行之工作負載的效能](#compare-performance-with-the-baseline)，以及[您在來源 SQL Server 上建立的效能基準](#create-performance-baseline)。
- 持續[監視工作負載的效能](#monitor-performance)，以找出潛在的問題和改進。

### <a name="compare-performance-with-the-baseline"></a>比較基準的效能

成功遷移之後，您需要立即採取的第一個活動，就是比較工作負載與基準工作負載效能的效能。 此活動的目標是要確認您受控執行個體上的工作負載效能是否符合您的需求。 

資料庫移轉至受控執行個體會在大部分情況下保持資料庫設定及其原始相容性層級。 可能的話，會保留原始設定，以降低與您的來源 SQL Server 相較之下，某些效能降低的風險。 如果使用者資料庫在移轉之前的相容性層級為 100 或以上，則移轉後相容性層級維持不變。 如果使用者資料庫在移轉之前的相容性層級為 90，那在升級後的資料庫中，相容性層級會設定為 100，這是受控執行個體中能支援的最低相容性層級。 系統資料庫的相容性層級是 140。 由於遷移至受控執行個體實際上是遷移至最新版本的 SQL Server 資料庫引擎，因此您應該注意，您需要重新測試工作負載的效能，以避免一些令人驚訝的效能問題。

必要條件是，請確定您已完成下列活動：
- 藉由調查各種實例、資料庫、temdb 設定和設定，將受控執行個體的設定與來源 SQL Server 實例的設定對齊。 執行第一次效能比較之前，請確定您沒有變更相容性層級或加密等設定，或接受您啟用的某些新功能可能會影響某些查詢的風險。 若要減少移轉的風險，請只在進行效能監視後變更資料庫相容性層級。
- [針對一般用途執行儲存體最佳作法指導方針](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525)，例如預先設定檔案大小以獲得更好的效能。
- 瞭解[可能造成受控執行個體和 SQL Server 之間的效能差異的主要環境差異]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)，並找出可能會影響效能的風險。
- 請確定您在受控執行個體上保持 [已啟用] 查詢存放區和 [自動調整]。 這些功能可讓您測量工作負載效能，並自動修正潛在的效能問題。 瞭解如何使用查詢存放區做為在資料庫相容性層級變更前後取得工作負載效能相關資訊的最佳工具，如在[升級至較新的 SQL Server 版本期間保持效能穩定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)中所述。
一旦您已備妥可與內部部署環境盡可能比較的環境，就可以開始執行您的工作負載並測量效能。 [當您在來源 SQL Server 上建立工作負載量值的基準效能時](#create-performance-baseline)，測量程式應該包含您測量的相同參數。
因此，您應該比較效能參數與基準，並識別重要的差異。

> [!NOTE]
> 在許多情況下，您將無法取得受控執行個體和 SQL Server 上完全相符的效能。 受控執行個體是 SQL Server 的資料庫引擎，但受控執行個體上的基礎結構和高可用性設定可能會帶來一些差異。 您可能會預期某些查詢的速度較快，而有些則可能較慢。 比較的目標是要驗證受控執行個體中的工作負載效能是否符合 SQL Server 的效能（平均），並識別是否有任何重大查詢的效能不符合您的原始效能。

效能比較的結果可能是：
- 受控執行個體上的工作負載效能會對齊或更佳，SQL Server 上的工作負載效能。 在此情況下，您已成功確認遷移成功。
- 大部分的效能參數和工作負載中的查詢都可以正常執行，但有一些例外狀況會降低效能。 在此情況下，您需要找出差異及其重要性。 如果有一些重要的查詢效能降低，您應該調查基礎 SQL 計畫是否已變更，或查詢是否遇到一些資源限制。 在此情況下，緩和措施可能是直接或使用計劃指南，在重要查詢（例如變更的相容性層級、舊版基數估計工具）上套用一些提示，重建或建立可能會影響計畫的統計資料和索引。 
- 與您的來源 SQL Server 相較之下，大部分查詢的受控執行個體會變慢。 在此情況下，請嘗試找出差異的根本原因，例如[達到一些資源限制]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics)，如 IO 限制、記憶體限制、實例記錄速率限制等。如果沒有任何可能造成差異的資源限制，請嘗試變更資料庫的相容性層級，或變更資料庫設定（例如舊版基數估計），然後重新開始測試。 請參閱受控執行個體或查詢存放區 views 提供的建議，以識別回歸效能的查詢。

> [!IMPORTANT]
> 受控執行個體具有依預設啟用的內建自動計畫更正功能。 這項功能可確保在貼上正常運作的查詢在未來不會降低。 在變更新設定之前，請確定已啟用這項功能，而且您已使用舊的設定來執行工作負載的時間長度已足夠，以便讓受控執行個體瞭解基準效能和計畫。

在您取得符合需求的工作負載效能之前，請變更參數或升級服務層級，以融合為最佳設定。

### <a name="monitor-performance"></a>監視效能

受控執行個體提供許多先進的工具來進行監視和疑難排解，您應該使用它們來監視實例的效能。 您需要監視的某些參數包括：
- 實例上的 CPU 使用量，以判斷您所布建的虛擬核心數目是否符合您的工作負載。
- 受控執行個體上的頁面生命週期，以判斷[您是否需要額外的記憶體](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)。
- 如或`PAGEIOLATCH`之類`INSTANCE_LOG_GOVERNOR`的等候統計資料會告訴您有儲存體 IO 問題，特別是在一般用途層中，您可能需要預先配置檔案以取得更佳的 IO 效能。

## <a name="leverage-advanced-paas-features"></a>運用 advanced PaaS 功能

一旦您在完全受控的平臺上，而且您已確認工作負載的效能符合您的 SQL Server 工作負載效能，請利用 SQL Database 服務中自動提供的優點。 

即使在遷移期間未在受控實例中進行某些變更，您還是有機會在操作實例時開啟一些新功能，以充分利用最新的資料庫引擎改善。 只有在[資料庫相容性層級變更](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)之後，才會啟用某些變更。


比方說，您不需要在受控執行個體上建立備份 (服務會自動為您執行備份)。 您無法再擔心如何排程、使用及管理備份。 受控實例可讓您使用「[時間點恢復」（PITR）](sql-database-recovery-using-backups.md#point-in-time-restore)來還原到此保留期限內的任何時間點。 此外，您不需要擔心如何設定為高可用性，因為[高可用性](sql-database-high-availability.md)已內建。

若要加強安全性，請考慮使用[Azure Active Directory 驗證](sql-database-security-overview.md)、[審核](sql-database-managed-instance-auditing.md)、[威脅偵測](sql-database-advanced-data-security.md)、資料[列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)和[動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)）。

除了先進的管理和安全性功能之外，受控執行個體還提供一組可協助您[監視和微調工作負載](sql-database-monitor-tune-overview.md)的先進工具。 [AZURE SQL 分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql)可讓您監視大型受控實例集，並集中監視大量實例和資料庫。 受控執行個體中的[自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)會持續監視 SQL 計畫執行統計資料的效能，並自動修正已識別的效能問題。

## <a name="next-steps"></a>後續步驟

- 如需受控實例的相關資訊，請參閱[什麼是受控實例？](sql-database-managed-instance.md)。
- 如需包含從備份還原的教學課程，請參閱[建立受控實例](sql-database-managed-instance-get-started.md)。
- 如需示範使用 DMS 來進行遷移的教學課程，請參閱[使用 dms 將內部部署資料庫移轉至受控實例](../dms/tutorial-sql-server-to-managed-instance.md)。  
