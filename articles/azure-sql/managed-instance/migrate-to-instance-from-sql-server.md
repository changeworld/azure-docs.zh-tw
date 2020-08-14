---
title: 從 SQL Server 遷移至 Azure SQL 受控執行個體
description: 瞭解如何將資料庫從 SQL Server 遷移至 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: b7623a3c89f9ae4b20385caaac676b972f55f85e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209478"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>SQL Server 實例遷移至 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在本文中，您將瞭解將 SQL Server 2005 或更新版本實例遷移至 [AZURE SQL 受控執行個體](sql-managed-instance-paas-overview.md)的方法。 如需遷移至單一資料庫或彈性集區的詳細資訊，請參閱 [遷移至 SQL Database](../database/migrate-to-database-from-sql-server.md)。 如需從其他平台移轉的移轉資訊，請參閱 [Azure 資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\)。

> [!NOTE]
> 如果您想要快速啟動並試用 Azure SQL 受控執行個體，您可能會想要移至 [快速入門手冊](quickstart-content-reference-guide.md) ，而不是此頁面。

概括而言，資料庫移轉程序看起來像這樣：

![移轉程序](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [評估 SQL 受控執行個體相容性](#assess-sql-managed-instance-compatibility) ，以確保不會有任何阻礙的問題可防止您的遷移。
  
  此步驟也包括建立 [效能基準](#create-a-performance-baseline) ，以判斷來源 SQL Server 實例的資源使用量。 如果您想要部署適當大小的受控實例，並在遷移後確認效能不受影響，則需要此步驟。
- [選擇 [應用程式連接選項](connect-application-instance.md)]。
- [部署到最佳大小的受控實例](#deploy-to-an-optimally-sized-managed-instance) ，您將在其中選擇技術特性 (虛擬核心數目、記憶體) 和效能層級 (受控實例的「商務關鍵性」、「一般用途) 」。
- [選取 [遷移方法] 和 [遷移](#select-a-migration-method-and-migrate) ]，使用離線遷移來遷移資料庫， (原生備份/還原、資料庫匯入/匯出) 或線上遷移 (Azure 資料移轉服務、異動複寫) 。
- [監視應用程式](#monitor-applications) ，以確保您有預期的效能。

> [!NOTE]
> 若要將個別資料庫移轉至單一資料庫或彈性集區，請參閱將 [SQL Server 資料庫移轉至 Azure SQL Database](../database/migrate-to-database-from-sql-server.md)。

## <a name="assess-sql-managed-instance-compatibility"></a>評估 SQL 受控執行個體相容性

首先，判斷 SQL 受控執行個體是否與您應用程式的資料庫需求相容。 SQL 受控執行個體的設計，是為了讓大部分使用 SQL Server 的現有應用程式都能輕鬆地隨即轉移。 不過，有時候您可能需要尚未支援的功能或功能，而且執行因應措施的成本太高。

使用 [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) 來偵測影響 Azure SQL Database 上資料庫功能的潛在相容性問題。 如果有一些回報的封鎖問題，您可能需要考慮替代選項，例如 [AZURE VM 上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 這裡有一些範例：

- 如果您需要直接存取作業系統或檔案系統，例如使用 SQL Server 在相同的虛擬機器上安裝協力廠商或自訂代理程式。
- 如果您對仍不支援的功能有嚴格的相依性，例如 FileStream/FileTable、PolyBase 和跨實例交易。
- 如果您絕對需要保持在特定版本的 SQL Server (2012，例如) 。
- 如果您的計算需求遠低於受控實例供應專案 (一個 vCore （例如) ），而且資料庫匯總不是可接受的選項。

如果您已解決所有已識別的遷移封鎖程式，並繼續遷移至 SQL 受控執行個體，請注意某些變更可能會影響工作負載的效能：

- 必要的完整復原模式和定期自動備份排程可能會影響工作負載的效能，或是定期使用簡單/大量記錄模型或視需要停止備份的維護/ETL 動作。
- 不同的伺服器或資料庫層級設定，例如追蹤旗標或相容性層級。
- 您所使用的新功能（例如透明資料庫加密 (TDE) 或自動容錯移轉群組）可能會影響 CPU 和 IO 使用量。

SQL 受控執行個體保證99.99% 的可用性（即使在重大案例中），因此無法停用這些功能所造成的額外負荷。 如需詳細資訊，請參閱 [可能會在 SQL Server 和 AZURE SQL 受控執行個體上造成不同效能的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。

### <a name="create-a-performance-baseline"></a>建立效能基準

如果您需要將受控實例上的工作負載效能與 SQL Server 上執行的原始工作負載進行比較，您必須建立將用於比較的效能基準。

效能基準是一組參數，例如平均/最大 CPU 使用量、平均/最大磁片 IO 延遲、輸送量、IOPS、平均/最大頁面生命預期，以及 tempdb 的平均大小上限。 在遷移之後，您會想要有類似或更好的參數，因此請務必測量並記錄這些參數的基準值。 除了系統參數之外，您還需要選取一組代表性查詢或工作負載中最重要的查詢，並測量所選查詢的最小/平均/最大持續時間和 CPU 使用量。 這些值可讓您將受控實例上執行的工作負載效能，與來源 SQL Server 實例上的原始值進行比較。

您需要在 SQL Server 實例上測量的部分參數如下：

- [監視 SQL Server 實例的 CPU 使用量](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) ，並記錄平均和尖峰 CPU 使用量。
- [監視 SQL Server 實例的記憶體使用量](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) ，並判斷不同元件所使用的記憶體數量，例如緩衝集區、計畫快取、資料行存放集區、 [記憶體內部 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)等等。此外，您還應該找出「Page Life 的記憶體」效能計數器的平均值和尖峰值。
- 使用 [sys.databases dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) view 或 [效能計數器](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)，監視來源 SQL SERVER 實例的磁片 IO 使用量。
- 藉由檢查動態管理檢視或查詢存放區（如果您要從 SQL Server 2016 + 版本進行遷移），來監視工作負載和查詢效能或 SQL Server 實例。 識別工作負載中最重要查詢的平均持續時間和 CPU 使用量，以與在受控實例上執行的查詢進行比較。

> [!Note]
> 如果您注意到 SQL Server 的工作負載發生問題，例如高 CPU 使用率、記憶體不足壓力或 tempdb 或參數化問題，您應該先嘗試在來源 SQL Server 實例上加以解決，再進行基準和遷移。 將已知問題遷移至任何新的系統可能會導致非預期的結果，並使任何效能比較失效。

作為此活動的結果，您應該已記載來源系統上的 CPU、記憶體和 IO 使用量的平均和尖峰值，以及主要和工作負載中最重要查詢的平均和最大持續時間和 CPU 使用量。 您稍後應該使用這些值來比較受控實例上的工作負載效能與來源 SQL Server 實例上工作負載的基準效能。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到最佳大小的受控實例

SQL 受控執行個體是針對規劃移至雲端的內部部署工作負載而量身打造。 它引進[新的購買模型](../database/service-tiers-vcore.md)，提供更大的彈性來選取適合您工作負載的正確資源層級。 在內部部署的環境中，您可能習慣使用實體核心數目與 IO 頻寬來調整這些工作負載大小。 受控執行個體的購買模型是以虛擬核心 (vCore) 為基礎，再個別加上額外儲存體與可用 IO。 相對於目前使用的內部部署方案，VCore 模型可讓您較簡單地了解雲端中的計算需求。 這個新模型可讓您在雲端中具有正確大小的目的地環境。 以下說明一些可協助您選擇正確服務層級和特性的一般指導方針：

- 根據基準 CPU 使用量，您可以布建符合您在 SQL Server 上使用之核心數目的受控實例，但請注意，CPU 特性可能需要調整，以符合 [已安裝受控實例的 VM 特性](resource-limits.md#hardware-generation-characteristics)。
- 根據 [基準記憶體使用量]，選擇 [具有相符記憶體的服務層級](resource-limits.md#hardware-generation-characteristics)。 記憶體數量無法直接選擇，因此您必須選取具有相符 (記憶體的虛擬核心數量的受控實例，例如第5代) 中的 5.1 GB/vCore。
- 根據檔案子系統的 [基準 IO 延遲]，在 [一般用途] (延遲大於5毫秒) 和 [商務關鍵性 (延遲] 低於3毫秒) 服務層級。
- 根據基準輸送量，預先配置資料或記錄檔的大小，以取得預期的 IO 效能。

您可以在部署期間選擇計算和儲存體資源，然後在不使用 [Azure 入口網站](../database/scale-resources.md)的情況下變更應用程式的停機時間：

![受控實例大小](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

若要瞭解如何建立 VNet 基礎結構和受控實例，請參閱 [建立受控實例](instance-create-quickstart.md)。

> [!IMPORTANT]
> 請務必根據 [受控實例的 vnet 需求](connectivity-architecture-overview.md#network-requirements)，保留您的目的地 VNet 和子網。 任何相容性問題都可能會讓您無法建立新的執行個體，或使用已經建立的執行個體。 深入了解[建立新的 ](virtual-network-subnet-create-arm-template.md) 並[ 設定現有的 ](vnet-existing-add-subnet.md) 網路。

## <a name="select-a-migration-method-and-migrate"></a>選取遷移方法並進行遷移

SQL 受控執行個體的目標是需要從內部部署或 Azure VM 資料庫實施大量資料庫移轉的使用者案例。 當您需要隨即轉移定期使用實例層級和/或跨資料庫功能的應用程式後端時，它們是最佳選擇。 如果這是您的情況，您可以將整個執行個體移動至 Azure 中對應的環境，而不需要重新建構您的應用程式。

若要移動 SQL 執行個體，您需要謹慎規劃下列作業：

- 需要共置的所有資料庫的遷移， (在相同實例) 上執行的資料庫。
- 您的應用程式所相依的實例層級物件的遷移，包括登入、認證、SQL Agent 作業和運算子，以及伺服器層級的觸發程式。

SQL 受控執行個體是一項受控服務，可讓您將一些一般 DBA 活動委派至平臺（如其內建）。 因此，某些實例層級的資料不需要遷移，例如定期備份或 Always On 設定的維護工作，因為內建 [高可用性](../database/high-availability-sla.md) 。

SQL 受控執行個體支援下列資料庫移轉選項 (目前這是唯一支援的遷移方法) ：

- Azure 資料庫移轉服務-以近乎零的停機時間進行遷移。
- 原生`RESTORE DATABASE FROM URL` - 從 SQL Server 使用原生備份且需要一些停機時間。

### <a name="azure-database-migration-service"></a>Azure 資料庫移轉服務

[Azure 資料庫移轉服務](../../dms/dms-overview.md) 是完全受控的服務，其設計目的是要在停機時間最短的情況之下，從多個資料庫來源順暢地遷移至 Azure 資料平臺。 此服務可簡化將現有協力廠商和 SQL Server 資料庫移至 Azure 所需的工作。 公開預覽中的部署選項包括 Azure 虛擬機器中 Azure SQL Database 和 SQL Server 資料庫中的資料庫。 資料庫移轉服務是建議的方法，可為您的企業工作負載進行遷移。

如果您在內部部署 SQL Server 上使用 SQL Server Integration Services (SSIS) ，則資料庫移轉服務還不支援遷移儲存 SSIS 封裝 (SSISDB) 的 SSIS 目錄，但您可以在 Integration Runtime 中布建 Azure SSIS () IR Azure Data Factory，這會在受控實例中建立新的 SSISDB，讓您可以將套件重新部署到其中。 請參閱 [在 Azure Data Factory 中建立 AZURE SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

若要深入瞭解此案例和資料庫移轉服務的設定步驟，請參閱 [使用資料庫移轉服務，將您的內部部署資料庫移轉至受控實例](../../dms/tutorial-sql-server-to-managed-instance.md)。  

### <a name="native-restore-from-url"></a>從 URL 原生還原

還原原生備份 ( .bak 檔案) 從 SQL Server 實例取得， [Azure 儲存體](https://azure.microsoft.com/services/storage/)上提供，這是 SQL 受控執行個體的其中一項重要功能，可讓您快速且輕鬆地進行離線資料庫遷移。

下圖會提供程序的高階概觀：

![移轉流程](./media/migrate-to-instance-from-sql-server/migration-flow.png)

下表詳述根據您執行的來源 SQL Server 版本，可以使用的方法：

|步驟|SQL 引擎和版本|備份/還原方法|
|---|---|---|
|將備份放至 Azure 儲存體|在 2012 SP1 CU2 之前|將 .bak 檔案直接上傳至 Azure 儲存體|
||2012 SP1 CU2 - 2016|使用已被取代的 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 語法直接備份|
||2016 和更新版本|使用 [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 直接備份|
|從 Azure 儲存體還原至受控實例|[使用 SAS 認證從 URL 還原](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - 當您使用原生還原選項將受 [透明資料加密](../database/transparent-data-encryption-tde-overview.md) 保護的資料庫移轉至受控實例時，必須先遷移內部部署或 Azure VM SQL Server 的對應憑證，才能進行資料庫還原。 如需詳細步驟，請參閱將 [TDE Cert 遷移至受控實例](tde-certificate-migrate.md)。
> - 不支援系統資料庫還原。 若要遷移 (儲存在 master 或 msdb 資料庫中的實例層級物件) ，我們建議您在目的地實例上對其編寫腳本，並執行 T-sql 腳本。

如需說明如何使用 SAS 認證將資料庫備份還原至受控實例的快速入門，請參閱 [從備份還原至受控實例](restore-sample-database-quickstart.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>監視應用程式

完成遷移至受控實例之後，您應該追蹤工作負載的應用程式行為和效能。 此套裝程式含下列活動：

- [比較在受控實例上執行之工作負載的效能](#compare-performance-with-the-baseline) ，以及 [您在來源 SQL Server 實例上建立的效能基準](#create-a-performance-baseline)。
- 持續 [監視工作負載的效能](#monitor-performance) ，以找出潛在的問題和改進。

### <a name="compare-performance-with-the-baseline"></a>比較基準的效能

成功遷移之後，您需要立即採取的第一個活動，就是比較工作負載與基準工作負載效能的效能。 此活動的目標是要確認受控實例上的工作負載效能是否符合您的需求。

在大部分情況下，將資料庫移轉至受控實例會保留資料庫設定及其原始相容性層級。 可能的話，會保留原始設定，以降低與您的來源 SQL Server 實例相較之下，效能降低的風險。 如果使用者資料庫在移轉之前的相容性層級為 100 或以上，則移轉後相容性層級維持不變。 如果使用者資料庫在遷移前的相容性層級為90，則在升級的資料庫中，相容性層級會設定為100，這是受控實例中支援的最低相容性層級。 系統資料庫的相容性層級是 140。 由於遷移至受控實例實際上是遷移到最新版本的 SQL Server 資料庫引擎，因此您應該注意，您需要重新測試工作負載的效能，以避免一些令人驚訝的效能問題。

必要條件是，請確定您已完成下列活動：

- 藉由調查各種實例、資料庫、tempdb 設定和設定，將受控實例上的設定與來源 SQL Server 實例的設定對齊。 執行第一次效能比較之前，請確定您沒有變更相容性層級或加密等設定，或接受您啟用的某些新功能可能會影響某些查詢的風險。 若要減少移轉的風險，請只在進行效能監視後變更資料庫相容性層級。
- [針對一般用途來執行儲存體最佳做法指導方針](https://techcommunity.microsoft.com)，例如預先設定檔案大小以獲得更好的效能。
- 瞭解 [可能會導致受控實例和 SQL Server 之間的效能差異的主要環境差異](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)，並找出可能影響效能的風險。
- 請確定您在受控實例上保持啟用的查詢存放區和自動調整。 這些功能可讓您測量工作負載效能，並自動修正潛在的效能問題。 瞭解如何使用查詢存放區做為在資料庫相容性層級變更前後取得工作負載效能相關資訊的最佳工具，如在 [升級至較新的 SQL Server 版本期間保持效能穩定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)中所述。
一旦您已備妥可與內部部署環境盡可能比較的環境，就可以開始執行您的工作負載並測量效能。 [當您在來源 SQL Server 實例上建立工作負載量值的基準效能時](#create-a-performance-baseline)，測量程式應該包含您所測量的相同參數。
因此，您應該比較效能參數與基準，並識別重要的差異。

> [!NOTE]
> 在許多情況下，您將無法在受控實例上取得完全相符的效能，並 SQL Server。 Azure SQL 受控執行個體是 SQL Server 的資料庫引擎，但受控實例上的基礎結構和高可用性設定可能會導致一些差異。 您可能會預期某些查詢的速度較快，有些則可能較慢。 比較的目標是要確認受控實例中的工作負載效能是否符合平均) 上 SQL Server (的效能，並找出效能不符合原始效能的任何重要查詢。

效能比較的結果可能是：

- 受控實例上的工作負載效能會比 SQL Server 上的工作負載效能一致或更好。 在此情況下，您已成功確認遷移成功。
- 大部分的效能參數和工作負載中的查詢都能正常執行，但有一些例外狀況會降低效能。 在此情況下，您需要找出差異及其重要性。 如果有一些重要的查詢效能降低，您應該調查基礎 SQL 計畫是否已變更，或查詢是否遇到一些資源限制。 在此情況下，緩和措施可能是在重要查詢上套用一些提示 (例如，已變更的相容性層級、舊版基數估計工具) 直接或使用計劃指南，重建或建立可能會影響計畫的統計資料和索引。
- 與您的來源 SQL Server 實例相比，受控實例上的大部分查詢速度較慢。 在此情況下，請嘗試找出差異的根本原因，例如 [達到一些資源限制](resource-limits.md#service-tier-characteristics) ，如 IO 限制、記憶體限制、實例記錄速率限制等。如果沒有任何可能造成差異的資源限制，請嘗試變更資料庫的相容性層級，或變更資料庫設定（例如舊版基數估計），然後重新開始測試。 檢查受控實例或查詢存放區 views 所提供的建議，以識別回歸效能的查詢。

> [!IMPORTANT]
> Azure SQL 受控執行個體具有內建的自動計畫修正功能，預設為啟用。 這項功能可確保在貼上正常運作的查詢在未來不會降低。 在變更新設定之前，請確定已啟用這項功能，且您已使用舊的設定執行工作負載，以便讓受控實例瞭解基準效能和計畫。

在您取得符合需求的工作負載效能之前，請變更參數或升級服務層級，以融合為最佳設定。

### <a name="monitor-performance"></a>監視效能

SQL 受控執行個體提供許多先進的工具來進行監視和疑難排解，而且您應該使用它們來監視實例的效能。 您需要監視的某些參數包括：

- 實例上的 CPU 使用量，以判斷您所布建的虛擬核心數目是否符合您的工作負載。
- 受控實例上的頁面存留時間，以判斷 [您是否需要額外的記憶體](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)。
- 或之類 `INSTANCE_LOG_GOVERNOR` 的統計資料 `PAGEIOLATCH` 會告訴您是否有儲存 io 問題，特別是在一般用途層，您可能需要預先配置檔案以取得更佳的 IO 效能。

## <a name="leverage-advanced-paas-features"></a>運用 advanced PaaS 功能

在完全受控的平臺上，且您已確認工作負載的效能符合您 SQL Server 的工作負載效能之後，請使用自動提供的優勢做為服務的一部分。

即使在遷移期間，您不會在受控實例中進行某些變更，但您仍有機會在操作實例時開啟一些新功能，以利用最新的資料庫引擎改善。 只有在 [資料庫相容性層級變更](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)之後，才會啟用某些變更。

例如，您不需要在受控實例上建立備份，此服務會自動為您執行備份。 您無法再擔心如何排程、使用及管理備份。 SQL 受控執行個體可讓您使用 [時間點恢復 (PITR) ](../database/recovery-using-backups.md#point-in-time-restore)，還原到此保留期限內的任何時間點。 此外，您不需要擔心設定高可用性，因為內建 [高可用性](../database/high-availability-sla.md) 。

若要加強安全性，請考慮使用 [Azure Active Directory 驗證](../database/security-overview.md)、 [審核](auditing-configure.md)、 [威脅偵測](../database/advanced-data-security.md)、資料 [列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)和 [動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)。

除了先進的管理和安全性功能之外，受控實例還提供一組可協助您 [監視和微調工作負載](../database/monitor-tune-overview.md)的先進工具。 [Azure SQL 分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) 可讓您監視大型的受控實例集，並集中監視大量實例和資料庫。 受控實例中的[自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)會持續監視 SQL 計畫執行統計資料的效能，並自動修正已識別的效能問題。

## <a name="next-steps"></a>後續步驟

- 如需 Azure SQL 受控執行個體的相關資訊，請參閱 [什麼是 AZURE sql 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 如需包含從備份還原的教學課程，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 如需示範使用資料庫移轉服務進行遷移的教學課程，請參閱 [使用資料庫移轉服務將內部部署資料庫移轉至 AZURE SQL 受控執行個體](../../dms/tutorial-sql-server-to-managed-instance.md)。  
