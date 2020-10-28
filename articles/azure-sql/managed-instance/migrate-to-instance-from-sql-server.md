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
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: 7e7775f289e0221862d11c585ae85a5b0bc6cc27
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788545"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>SQL Server 實例遷移至 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在本文中，您將瞭解將 SQL Server 2005 或更新版本實例遷移至 [AZURE SQL 受控執行個體](sql-managed-instance-paas-overview.md)的方法。 如需遷移至單一資料庫或彈性集區的相關資訊，請參閱 [遷移至 SQL Database](../database/migrate-to-database-from-sql-server.md)。 如需從其他平台移轉的移轉資訊，請參閱 [Azure 資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\)。

> [!NOTE]
> 如果您想要快速啟動並試用 Azure SQL 受控執行個體，您可能會想要移至 [快速入門手冊](quickstart-content-reference-guide.md) ，而不是此頁面。

概括而言，資料庫移轉程序看起來像這樣：

![移轉程序](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [評估 SQL 受控執行個體的相容性](#assess-sql-managed-instance-compatibility) ，您應該確定沒有任何阻礙的問題可防止您的遷移。
  
  此步驟也包含建立 [效能基準](#create-a-performance-baseline) ，以判斷來源 SQL Server 實例上的資源使用量。 如果您想要部署適當大小的受控實例，並在遷移後確認效能不受影響，則需要此步驟。
- [選擇應用程式連線選項](connect-application-instance.md)。
- [部署到最佳大小的受控實例](#deploy-to-an-optimally-sized-managed-instance) ，您將在其中選擇技術特性 (虛擬核心數目、記憶體數量) 和效能層級 (業務關鍵、一般用途) 受控實例。
- [選取 [遷移方法]，然後](#select-a-migration-method-and-migrate) 使用離線遷移 (原生備份/還原、資料庫匯入/匯出) 或線上遷移 (Azure 資料移轉服務的異動複寫) ，來遷移您的資料庫。
- [監視應用程式](#monitor-applications) ，以確保您有預期的效能。

> [!NOTE]
> 若要將個別的資料庫移轉至單一資料庫或彈性集區，請參閱將 [SQL Server 資料庫移轉至 Azure SQL Database](../database/migrate-to-database-from-sql-server.md)。

## <a name="assess-sql-managed-instance-compatibility"></a>評估 SQL 受控執行個體相容性

首先，判斷 SQL 受控執行個體是否與您應用程式的資料庫需求相容。 SQL 受控執行個體的設計目的是要針對大部分使用 SQL Server 的現有應用程式，提供簡單的隨即轉移。 不過，您有時可能需要的功能不受支援，而且執行因應措施的成本太高。

使用 [Data Migration Assistant](/sql/dma/dma-overview) 來偵測 Azure SQL Database 上影響資料庫功能的潛在相容性問題。 如果有一些回報的封鎖問題，您可能需要考慮替代選項，例如 [AZURE VM 上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 以下是一些範例：

- 如果您需要直接存取作業系統或檔案系統，例如在與 SQL Server 的相同虛擬機器上安裝協力廠商或自訂代理程式。
- 如果您對仍不支援的功能具有嚴格的相依性，例如 FileStream/FileTable、PolyBase 和跨實例交易。
- 如果您絕對需要保持在特定版本的 SQL Server (2012，例如) 。
- 如果您的計算需求遠低於受控實例供應專案 (一個 vCore，例如) ，而且資料庫匯總不是可接受的選項。

如果您已解決所有已識別的遷移封鎖程式，並且繼續遷移至 SQL 受控執行個體，請注意，某些變更可能會影響工作負載的效能：

- 如果您定期使用簡單/大量記錄模型或視需要停止備份，強制完整復原模式和定期自動備份排程可能會影響工作負載的效能或維護/ETL 動作。
- 不同的伺服器或資料庫層級設定，例如追蹤旗標或相容性層級。
- 您使用的新功能（例如透明資料庫加密 (TDE) 或自動容錯移轉群組）可能會影響 CPU 和 IO 的使用量。

SQL 受控執行個體可保證99.99% 的可用性（即使在重大情況下），因此無法停用這些功能所造成的額外負荷。 如需詳細資訊，請參閱 [SQL Server 和 AZURE SQL 受控執行個體可能會導致不同效能的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。

### <a name="create-a-performance-baseline"></a>建立效能基準

如果您需要將受控實例上的工作負載效能與在 SQL Server 上執行的原始工作負載進行比較，您必須建立將用於比較的效能基準。

效能基準是一組參數，例如平均/最大 CPU 使用量、平均/最大磁片 IO 延遲、輸送量、IOPS、平均/最大頁面壽命預期和 tempdb 的平均大小上限。 在遷移之後，您會想要有類似或更好的參數，因此請務必測量並記錄這些參數的基準值。 除了系統參數之外，您還需要在工作負載中選取一組具代表性的查詢或最重要的查詢，以及測量所選查詢的最小/平均/最大持續時間和 CPU 使用量。 這些值可讓您比較在受控實例上執行的工作負載與來源 SQL Server 實例上的原始值的效能。

您需要在 SQL Server 實例上測量的一些參數如下：

- [監視 SQL Server 實例上的 cpu 使用量](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) ，並記錄平均和尖峰 cpu 使用量。
- [監視 SQL Server 實例上的記憶體使用量](/sql/relational-databases/performance-monitor/monitor-memory-usage) ，並判斷不同元件所使用的記憶體數量，例如緩衝集區、計畫快取、資料行存放區集區、 [記憶體內部 OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)等等。此外，您應該會找到 Page Life memory 效能計數器的平均值和尖峰值。
- 使用 [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) view 或 [效能計數器](/sql/relational-databases/performance-monitor/monitor-disk-usage)，監視來源 SQL Server 實例上的磁片 IO 使用量。
- 藉由檢查動態管理檢視或查詢存放區（如果您要從 SQL Server 2016 + 版本進行遷移），來監視工作負載和查詢效能或 SQL Server 實例。 識別您工作負載中最重要查詢的平均持續時間和 CPU 使用量，以將其與在受控實例上執行的查詢進行比較。

> [!Note]
> 如果您注意到您的工作負載發生任何問題 SQL Server 例如高 CPU 使用率、常數記憶體壓力或 tempdb 或參數化問題，您應該先嘗試在來源 SQL Server 實例上解決它們，再進行基準和遷移。 將已知問題遷移至任何新系統可能會導致非預期的結果，並使任何效能比較失效。

作為此活動的結果，您應該已記載來源系統上 CPU、記憶體和 IO 使用量的平均和尖峰值，以及主要和工作負載中最重要查詢的平均和最大持續時間和 CPU 使用率。 您稍後應該使用這些值，將受控實例上工作負載的效能與來源 SQL Server 實例上工作負載的基準效能進行比較。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到最佳大小的受控實例

SQL 受控執行個體是針對規劃移至雲端的內部部署工作負載量身打造的。 它引進[新的購買模型](../database/service-tiers-vcore.md)，提供更大的彈性來選取適合您工作負載的正確資源層級。 在內部部署的環境中，您可能習慣使用實體核心數目與 IO 頻寬來調整這些工作負載大小。 受控執行個體的購買模型是以虛擬核心 (vCore) 為基礎，再個別加上額外儲存體與可用 IO。 相對於目前使用的內部部署方案，VCore 模型可讓您較簡單地了解雲端中的計算需求。 這個新模型可讓您在雲端中具有正確大小的目的地環境。 以下說明一些可協助您選擇正確服務層級和特性的一般指導方針：

- 根據基準 CPU 使用量，您可以布建符合您在 SQL Server 上使用之核心數目的受控實例，並記住，可能需要調整 CPU 特性以符合 [已安裝受控實例的 VM 特性](resource-limits.md#hardware-generation-characteristics)。
- 根據基準記憶體使用量，選擇 [具有相符記憶體的服務層](resource-limits.md#hardware-generation-characteristics)。 無法直接選擇記憶體數量，因此您必須選取具有相符記憶體的虛擬核心數量的受控實例 (例如，第5代) 中的 5.1 GB/vCore。
- 根據檔案子系統的基準 IO 延遲，選擇一般用途 (延遲大於5毫秒) 和業務關鍵 (延遲低於3毫秒) 服務層級。
- 根據基準輸送量，預先配置資料或記錄檔的大小，以取得預期的 IO 效能。

您可以在部署時選擇計算和儲存體資源，然後在不使用 [Azure 入口網站](../database/scale-resources.md)為您的應用程式產生停機時間的情況下進行變更：

![受控實例大小調整](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

若要瞭解如何建立 VNet 基礎結構和受控實例，請參閱 [建立受控實例](instance-create-quickstart.md)。

> [!IMPORTANT]
> 請務必根據 [受控實例 vnet 需求](connectivity-architecture-overview.md#network-requirements)來保留目的地 VNet 和子網。 任何相容性問題都可能會讓您無法建立新的執行個體，或使用已經建立的執行個體。 深入了解[建立新的 ](virtual-network-subnet-create-arm-template.md) 並[ 設定現有的 ](vnet-existing-add-subnet.md) 網路。

## <a name="select-a-migration-method-and-migrate"></a>選取遷移方法並遷移

SQL 受控執行個體的目標是需要從內部部署或 Azure VM 資料庫執行大量資料庫移轉的使用者案例。 當您需要轉移定期使用實例層級和/或跨資料庫功能的應用程式後端時，它們是最佳選擇。 如果這是您的情況，您可以將整個執行個體移動至 Azure 中對應的環境，而不需要重新建構您的應用程式。

若要移動 SQL 執行個體，您需要謹慎規劃下列作業：

- 所有需要共置的資料庫，都必須在相同的實例) 上執行 (的遷移。
- 遷移應用程式相依的實例層級物件，包括登入、認證、SQL Agent 作業和運算子，以及伺服器層級的觸發程式。

SQL 受控執行個體是一項受控服務，可讓您將一些一般 DBA 活動委派至平臺，因為這些活動是內建的。 因此，某些實例層級的資料並不需要遷移，例如定期備份或 Always On 設定的維護工作，因為這是內建的 [高可用性](../database/high-availability-sla.md) 。

SQL 受控執行個體支援下列資料庫移轉選項 (目前這些是唯一支援的遷移方法) ：

- Azure 資料庫移轉服務-幾乎零停機時間的遷移。
- 原生`RESTORE DATABASE FROM URL` - 從 SQL Server 使用原生備份且需要一些停機時間。

### <a name="azure-database-migration-service"></a>Azure 資料庫移轉服務

[Azure 資料庫移轉服務](../../dms/dms-overview.md) 是一個完全受控的服務，其設計目的是要讓多個資料庫來源順暢地從多個資料庫來源遷移到 Azure 資料平臺。 此服務可簡化將現有協力廠商和 SQL Server 資料庫移至 Azure 所需的工作。 公開預覽的部署選項包含 Azure SQL Database 中的資料庫，以及 Azure 虛擬機器中的 SQL Server 資料庫。 針對您的企業工作負載，建議使用資料庫移轉服務遷移方法。

如果您在內部部署 SQL Server 使用 SQL Server Integration Services (SSIS) ，資料庫移轉服務還不支援將 ssis 目錄 (儲存 SSIS 套件的 SSISDB) 遷移，但您可以在 Azure-SSIS Integration Runtime 中布建 () IR Azure Data Factory，以在受控實例中建立新的 SSISDB，讓您可以將套件重新部署到其中。 請參閱 [Azure Data Factory 中的建立 Azure-SSIS IR](../../data-factory/create-azure-ssis-integration-runtime.md)。

若要深入瞭解此案例和資料庫移轉服務的設定步驟，請參閱 [使用資料庫移轉服務，將您的內部部署資料庫移轉至受控實例](../../dms/tutorial-sql-server-to-managed-instance.md)。  

### <a name="native-restore-from-url"></a>從 URL 原生還原

將原生備份還原 ( .bak 檔案) 取自 [Azure 儲存體](https://azure.microsoft.com/services/storage/)所提供的 SQL Server 實例，這是 SQL 受控執行個體的主要功能之一，可讓您快速輕鬆地進行離線資料庫遷移。

下圖會提供程序的高階概觀：

![圖表顯示 SQL Server 有一個標示為 [備份]/[上傳至 URL] Azure 儲存體的箭號，以及另一個標示為 [從 Azure 儲存體至 SQL 受控執行個體的 URL 還原] 的第二個箭號。](./media/migrate-to-instance-from-sql-server/migration-flow.png)

下表詳述根據您執行的來源 SQL Server 版本，可以使用的方法：

|步驟|SQL 引擎和版本|備份/還原方法|
|---|---|---|
|將備份放至 Azure 儲存體|2012 SP1 之前的 CU2|直接將 .bak 檔案上傳至 Azure 儲存體|
||2012 SP1 CU2 - 2016|使用已被取代的 [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql) 語法直接備份|
||2016 和更新版本|使用 [WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 直接備份|
|從 Azure 儲存體還原至受控實例|[使用 SAS 認證從 URL 還原](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - 當您使用原生還原選項將受 [透明資料加密](../database/transparent-data-encryption-tde-overview.md) 保護的資料庫移轉到受控實例時，來自內部部署或 Azure VM SQL Server 的對應憑證必須在資料庫還原之前進行遷移。 如需詳細步驟，請參閱[將 TDE 憑證移轉至受控執行個體](tde-certificate-migrate.md)。
> - 不支援系統資料庫還原。 若要遷移 (儲存在 master 或 msdb 資料庫) 中的實例層級物件，建議您將它們編寫腳本，並在目的地實例上執行 T-sql 腳本。

如需顯示如何使用 SAS 認證將資料庫備份還原至受控實例的快速入門，請參閱 [從備份還原至受控實例](restore-sample-database-quickstart.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>監視應用程式

完成遷移至受控實例後，您應該追蹤工作負載的應用程式行為和效能。 此流程包含下列活動：

- 使用[您在來源 SQL Server 實例上建立的效能基準，](#create-a-performance-baseline)[比較在受控實例上執行的工作負載效能](#compare-performance-with-the-baseline)。
- 持續 [監視您工作負載的效能](#monitor-performance) ，以找出潛在的問題和改進。

### <a name="compare-performance-with-the-baseline"></a>比較效能與基準

在成功遷移之後，您需要立即採取的第一個活動是將工作負載的效能與基準工作負載效能進行比較。 此活動的目標是要確認受控實例上的工作負載效能符合您的需求。

在多數情況下，將資料庫移轉至受控實例會保留資料庫設定和原始的相容性層級。 可能的話，會保留原始設定，以降低某些效能降低與您的來源 SQL Server 實例相較之下的風險。 如果使用者資料庫在移轉之前的相容性層級為 100 或以上，則移轉後相容性層級維持不變。 如果使用者資料庫的相容性層級在遷移前為90，則在升級的資料庫中，相容性層級會設定為100，這是受控實例中支援的最低相容性層級。 系統資料庫的相容性層級是 140。 由於遷移至受控實例的工作實際上是遷移至最新版的 SQL Server database engine，因此您應該注意，您需要重新測試工作負載的效能，以避免某些令人驚訝的效能問題。

請確定您已完成下列活動：

- 藉由調查不同的實例、資料庫、tempdb 設定和設定，將受控實例上的設定與來源 SQL Server 實例的設定對齊。 在執行第一個效能比較之前，請確定您未變更相容性層級或加密等設定，或接受您啟用的某些新功能可能會影響某些查詢的風險。 若要減少移轉的風險，請只在進行效能監視後變更資料庫相容性層級。
- [針對一般用途實行儲存體最佳作法指導方針](https://techcommunity.microsoft.com)，例如預先設定檔案大小以取得較佳的效能。
- 深入瞭解 [可能會造成受控實例與 SQL Server 之間效能差異的主要環境差異](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)，並找出可能影響效能的風險。
- 請確定您已在受控實例上保持啟用查詢存放區和自動調整。 這些功能可讓您測量工作負載效能，並自動修正潛在的效能問題。 瞭解如何使用查詢存放區作為最佳工具，以在資料庫相容性層級變更之前和之後取得工作負載效能的相關資訊，如在 [升級至較新的 SQL Server 版本期間保持效能穩定性](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)所述。
當您準備好可與內部部署環境比較的環境之後，您就可以開始執行工作負載並測量效能。 [當您在來源 SQL Server 實例上建立工作負載量值的基準效能時](#create-a-performance-baseline)，測量程式應包含您所測量的相同參數。
因此，您應該將效能參數與基準進行比較，並找出重大的差異。

> [!NOTE]
> 在許多情況下，您將無法在受控實例上取得完全相符的效能，並 SQL Server。 Azure SQL 受控執行個體是 SQL Server 的資料庫引擎，但受控實例上的基礎結構和高可用性設定可能會造成一些差異。 您可能會預期某些查詢的速度較快，有些則可能會較慢。 比較的目標是要確認受控實例中的工作負載效能符合平均) 上 SQL Server (的效能，並找出效能不符合原始效能的任何重要查詢。

效能比較的結果可能是：

- 受控實例上的工作負載效能，與 SQL Server 上的工作負載效能一致或更好。 在此情況下，您已成功確認遷移成功。
- 大部分的效能參數和工作負載中的查詢都能正常運作，但有些例外狀況會降低效能。 在此情況下，您需要找出差異及其重要性。 如果有一些重要的查詢效能降低，您應該調查基礎 SQL 計畫是否已變更，或是查詢是否達到某些資源限制。 在此情況下，緩和措施可能是在重要查詢上套用一些提示 (例如，變更的相容性層級、舊版基數估算器) 直接或使用計劃指南、重建或建立可能會影響計畫的統計資料和索引。
- 相較于來源 SQL Server 實例，大部分的查詢在受控實例上的速度較慢。 在此情況下，請嘗試找出差異的根本原因，例如 [達到某些資源限制](resource-limits.md#service-tier-characteristics) ，例如 IO 限制、記憶體限制、實例記錄檔速率限制等等。如果沒有可能造成差異的資源限制，請嘗試變更資料庫的相容性層級，或變更資料庫設定，例如舊版基數估計，然後重新開機測試。 請查看受控實例或查詢存放區 views 提供的建議，以找出回歸效能的查詢。

> [!IMPORTANT]
> Azure SQL 受控執行個體有預設啟用的內建自動方案修正功能。 這項功能可確保在貼上正常運作的查詢不會在未來降級。 在變更新的設定之前，請確定已啟用這項功能，而且您已使用舊的設定來執行工作負載夠長的時間，以便讓受控實例瞭解基準效能和計畫。

變更參數或升級服務層級，以會合至最佳設定，直到您取得符合需求的工作負載效能為止。

### <a name="monitor-performance"></a>監視效能

SQL 受控執行個體提供許多用於監視和疑難排解的 advanced tools，而且您應該使用它們來監視實例的效能。 您需要監視的一些參數如下：

- 實例上的 CPU 使用量，以判斷您布建的虛擬核心數目是否符合您的工作負載。
- 您受控實例上的頁面壽命預期，以判斷 [您是否需要額外的記憶體](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)。
- 或之類 `INSTANCE_LOG_GOVERNOR` 的統計資料 `PAGEIOLATCH` 會指出您是否有儲存體 IO 問題（尤其是在一般用途層上），您可能需要預先配置檔案，以取得更好的 IO 效能。

## <a name="leverage-advanced-paas-features"></a>利用 advanced PaaS 功能

當您在完全受控的平臺上，並確認工作負載效能符合 SQL Server 工作負載效能時，請使用在服務中自動提供的優點。

即使您未在遷移期間對受控實例進行某些變更，在您操作實例以利用最新的資料庫引擎改善時，很可能會開啟一些新功能。 只有當 [資料庫相容性層級變更](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)時，才會啟用某些變更。

例如，您不需要在受控實例上建立備份-服務會自動為您執行備份。 您無法再擔心如何排程、使用及管理備份。 SQL 受控執行個體可讓您使用 [時間點復原 (PITR) ](../database/recovery-using-backups.md#point-in-time-restore)來還原到此保留期間內的任何時間點。 此外，您不需要擔心如何設定高可用性，因為內建 [高可用性](../database/high-availability-sla.md) 。

若要加強安全性，請考慮使用 [Azure Active Directory 驗證](../database/security-overview.md)、 [審核](auditing-configure.md)、 [威脅偵測](../database/azure-defender-for-sql.md)、資料 [列層級安全性](/sql/relational-databases/security/row-level-security)和 [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)。

除了先進的管理和安全性功能之外，受控實例還提供一組可協助您 [監視和調整工作負載](../database/monitor-tune-overview.md)的 advanced tools。 [Azure SQL 分析](../../azure-monitor/insights/azure-sql.md) 可讓您監視大量的受控實例，並集中監視大量的實例和資料庫。 受控實例中的[自動調整](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)會持續監視 SQL 計畫執行統計資料的效能，並自動修正已識別的效能問題。

## <a name="next-steps"></a>後續步驟

- 如需 Azure SQL 受控執行個體的詳細資訊，請參閱 [什麼是 AZURE sql 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 如需包含從備份進行還原的教學課程，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 如需顯示使用資料庫移轉服務進行遷移的教學課程，請參閱 [使用資料庫移轉服務，將您的內部部署資料庫移轉至 AZURE SQL 受控執行個體](../../dms/tutorial-sql-server-to-managed-instance.md)。