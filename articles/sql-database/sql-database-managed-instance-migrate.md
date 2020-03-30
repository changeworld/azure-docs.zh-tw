---
title: 從 SQL Server 遷移到託管實例
description: 瞭解如何將資料庫從 SQL Server 實例遷移到 Azure SQL 資料庫 - 託管實例。
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
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208936"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>將 SQL Server 遷移至 Azure SQL Database 受控執行個體

在本文中，您將瞭解將 SQL Server 2005 或更高版本實例遷移到[Azure SQL 資料庫託管實例](sql-database-managed-instance.md)的方法。 如需移轉至單一資料庫或彈性集區的相關資訊，請參閱[移轉至單一或集區資料庫](sql-database-cloud-migrate.md)。 如需從其他平台移轉的移轉資訊，請參閱 [Azure 資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\)。

> [!NOTE]
> 如果要快速啟動並嘗試託管實例，可能需要轉到[快速入門手冊](sql-database-managed-instance-quickstart-guide.md)，而不是此頁面。 

概括而言，資料庫移轉程序看起來像這樣：

![移轉程序](./media/sql-database-managed-instance-migration/migration-process.png)

- [評估託管實例相容性](#assess-managed-instance-compatibility)，確保不存在阻止遷移的問題。
  - 此步驟還包括創建[性能基線](#create-performance-baseline)，以確定源 SQL Server 實例上的資源使用方式。 如果希望 o 部署大小合適的託管實例並驗證遷移後的性能是否不受影響，則需要此步驟。
- [選擇應用連接選項](sql-database-managed-instance-connect-app.md)
- [部署到大小最佳的託管實例](#deploy-to-an-optimally-sized-managed-instance)，您將選擇託管實例的技術特徵（vCore 數量、記憶體量）和性能層（業務關鍵、通用）。
- [選擇遷移方法，並使用](#select-migration-method-and-migrate)離線遷移（本機備份/還原、資料庫導入/匯出）或連線遷移（資料移轉服務、異動複寫）遷移資料庫的位置遷移。
- [監視應用程式](#monitor-applications)以確保具有預期性能。

> [!NOTE]
> 若要將個別資料庫遷移至單一資料庫或彈性集區，請參閱[將 SQL Server 資料庫遷移至 Azure SQL Database](sql-database-single-database-migrate.md)。

## <a name="assess-managed-instance-compatibility"></a>評估受控執行個體的相容性

首先，判斷受控執行個體是否與您應用程式的資料庫需求相容。 受控執行個體部署選項旨在為大部分內部部署或虛擬機器上使用 SQL Server 的現有應用程式，提供簡單的隨即轉移。 不過，有時候您可能需要尚不支援的功能，而且實作因應措施的成本非常高。

使用[資料移轉小幫手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview)，可偵測影響 Azure SQL Database 資料庫功能的潛在相容性問題。 DMA 尚不支援將受控執行個體做為移轉目的地，但建議您針對 Azure SQL Database 執行評估，並針對產品文件，仔細檢閱提報的功能同位和相容性問題清單。 請參閱[Azure SQL 資料庫功能](sql-database-features.md)以檢查託管實例中未阻止程式的一些已報告的阻止問題，因為阻止遷移到 Azure SQL 資料庫的大多數阻止問題已使用託管實例刪除。 例如跨資料庫查詢、相同執行個體內的跨資料庫交易、其他 SQL 來源的連結伺服器、CLR、全域暫存資料表、執行個體層級檢視、Service Broker 等功能皆可在受控執行個體中使用。

若受控制執行個體部署選項並未移除一些回報的執行問題，您可能需要考慮替代選項，例如 [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 以下是一些範例：

- 如果您需要直接存取作業系統或檔案系統，例如在具有 SQL Server 的相同虛擬機器上安裝第三方或自訂代理程式。
- 如果您的執行個體與尚不支援的功能有緊密相依性，例如 FileStream / FileTable、PolyBase 及跨執行個體交易等功能。
- 如果絕對需要停留在 SQL Server 的特定版本（例如 2012）。
- 如果您的計算需求遠低於受控執行個體提供的功能 (例如，只需要一個虛擬核心)，而且資料庫彙總不是可接受的選項。

如果已解析所有標識的遷移阻止程式並繼續遷移到託管實例，請注意，某些更改可能會影響工作負荷的性能：
- 如果您定期使用簡單/批量記錄的模型或按需停止備份，則強制性完全恢復模型和常規自動備份計畫可能會影響工作負載或維護/ETL 操作的性能。
- 不同的伺服器或資料庫級別配置，如跟蹤標誌或相容性級別
- 您正在使用的新功能（如透明資料庫加密 （TDE） 或自動容錯移轉組可能會影響 CPU 和 IO 使用率。

託管實例即使在關鍵方案中也能保證 99.99% 的可用性，因此無法禁用這些功能造成的開銷。 有關詳細資訊，請參閱可能導致[SQL Server 和託管實例性能不同的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。

### <a name="create-performance-baseline"></a>創建性能基準

如果需要將託管實例上工作負荷的性能與 SQL Server 上運行的原始工作負荷進行比較，則需要創建將用於比較的性能基準。 

性能基線是一組參數，如平均/最大 CPU 使用率、平均/最大磁片 IO 延遲、輸送量、IOPS、平均/最大頁預期壽命、tempdb 的平均最大大小。 您希望在遷移後具有類似甚至更好的參數，因此測量和記錄這些參數的基線值非常重要。 除了系統參數之外，還需要選擇一組有代表性的查詢或工作負荷中最重要的查詢，並測量所選查詢的最小/平均/最大持續時間、CPU 使用率。 這些值使您能夠將託管實例上運行的工作負荷性能與源 SQL Server 上的原始值進行比較。

需要在 SQL Server 實例上測量的一些參數包括： 
- [監視 SQL Server 實例上的 CPU 使用方式](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)，並記錄平均和峰值 CPU 使用率。
- [監視 SQL Server 實例上的記憶體使用方式](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage)，並確定不同元件（如緩衝集區、計畫緩存、列存儲池、[記憶體中 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)等）使用的記憶體量。此外，您應該找到頁面預期壽命記憶體效能計數器的平均值和峰值。
- 使用[sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)視圖或[效能計數器](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)監視源 SQL Server 實例上的磁片 IO 使用方式。
- 監視工作負荷和查詢性能或 SQL Server 實例，通過檢查動態管理檢視或查詢存儲（如果您要從 SQL Server 2016+ 版本遷移）。 確定工作負荷中最重要的查詢的平均持續時間和 CPU 使用率，以將其與託管實例上運行的查詢進行比較。

> [!Note]
> 如果您發現 SQL Server 上的工作負載出現任何問題，例如 CPU 使用率高、記憶體壓力恒定、tempdb 或參數化問題，則應嘗試在獲取基線和遷移之前在源 SQL Server 實例上解決這些問題。 將知道問題遷移到任何新系統，會導致意外結果，並使任何性能比較無效。

作為此活動的結果，您應該在源系統上記錄 CPU、記憶體和 IO 使用率的平均值和峰值值，以及工作負載中主要查詢和最關鍵的查詢的平均和最大持續時間和 CPU 使用率。 以後應使用這些值來比較託管實例上工作負荷的性能與源 SQL Server 上工作負荷的基準性能。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到最佳大小的受控執行個體

受控執行個體專為打算移至雲端的內部工作負載量身訂做。 它引進[新的購買模型](sql-database-service-tiers-vcore.md)，提供更大的彈性來選取適合您工作負載的正確資源層級。 在內部部署的環境中，您可能習慣使用實體核心數目與 IO 頻寬來調整這些工作負載大小。 受控執行個體的購買模型是以虛擬核心 (vCore) 為基礎，再個別加上額外儲存體與可用 IO。 相對於目前使用的內部部署方案，VCore 模型可讓您較簡單地了解雲端中的計算需求。 這個新模型可讓您在雲端中具有正確大小的目的地環境。 此處介紹了一些可以説明您選擇正確服務層和特徵的一般準則：
- 根據基準 CPU 使用率，您可以預配與 SQL Server 上正在使用的核心數相匹配的託管實例，並考慮到可能需要縮放 CPU 特徵以匹配[安裝託管實例的 VM 特徵](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
- 根據基線記憶體使用方式選擇[具有匹配記憶體的服務層](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。 不能直接選擇記憶體量，因此您需要選擇具有匹配記憶體的 vCore 量的託管實例（例如，第 5 代中的 5.1 GB/vCore）。 
- 根據檔子系統的基準 IO 延遲選擇通用（延遲大於 5ms）和業務關鍵型服務層（延遲小於 3 毫秒）。
- 基於基準輸送量預先分配資料或日誌檔的大小，以獲得預期的 IO 性能。

您可以在部署時選擇計算和存儲資源，然後在以後更改資源，而無需使用[Azure 門戶](sql-database-scale-resources.md)為應用程式引入停機時間：

![受控執行個體大小](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

要瞭解如何創建 VNet 基礎結構和託管實例，請參閱[創建託管實例](sql-database-managed-instance-get-started.md)。

> [!IMPORTANT]
> 請務必始終按照[託管實例 VNet 的要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)保持目標 VNet 和子網。 任何相容性問題都可能會讓您無法建立新的執行個體，或使用已經建立的執行個體。 深入了解[建立新的 ](sql-database-managed-instance-create-vnet-subnet.md) 並[ 設定現有的 ](sql-database-managed-instance-configure-vnet-subnet.md) 網路。

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

要瞭解有關 DMS 的此方案和配置步驟的詳細資訊，請參閱[使用 DMS 將本機資料庫遷移到託管實例](../dms/tutorial-sql-server-to-managed-instance.md)。  

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
> - 使用原生還原選項將受到[透明資料加密](transparent-data-encryption-azure-sql.md)保護的資料庫遷移到受控執行個體時，來自內部部署或 IaaS SQL Server 的對應憑證必須在資料庫還原之前進行遷移。 有關詳細步驟，請參閱[將 TDE 證書遷移到託管實例](sql-database-managed-instance-migrate-tde-certificate.md)
> - 不支援系統資料庫還原。 若要移轉執行個體層級物件 (儲存在 master 或 msdb 資料庫中)，我們建議透過指令碼來找出這些物件，並在目的地執行個體上執行 T-SQL 指令碼。

有關如何使用 SAS 憑據將資料庫備份還原到託管實例的快速入門，請參閱[從備份還原到託管實例](sql-database-managed-instance-get-started-restore.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>監視應用程式

完成對託管實例的遷移後，應跟蹤工作負荷的應用程式行為和性能。 此過程包括以下活動：
- [將託管實例上運行的工作負荷的性能](#compare-performance-with-the-baseline)與[您在源 SQL Server 上創建的性能基線](#create-performance-baseline)進行比較。
- 持續[監控工作負載的性能](#monitor-performance)，以確定潛在問題和改進。

### <a name="compare-performance-with-the-baseline"></a>將性能與基線進行比較

成功遷移後需要立即執行的第一個活動是比較工作負載的性能和基準工作負載性能。 此活動的目標是確認託管實例上的工作負載性能是否滿足您的需要。 

在大多數情況下，資料庫移轉到託管實例可保留資料庫設置及其原始相容性級別。 盡可能保留原始設置，以降低與源 SQL Server 相比性能下降的風險。 如果使用者資料庫在移轉之前的相容性層級為 100 或以上，則移轉後相容性層級維持不變。 如果使用者資料庫在移轉之前的相容性層級為 90，那在升級後的資料庫中，相容性層級會設定為 100，這是受控執行個體中能支援的最低相容性層級。 系統資料庫的相容性層級是 140。 由於遷移到託管實例實際上遷移到最新版本的 SQL Server 資料庫引擎，因此您應該知道需要重新測試工作負載的性能，以避免出現一些令人驚訝的性能問題。

作為先決條件，請確保您已完成以下活動：
- 通過調查各種實例、資料庫、temdb 設置和配置，在託管實例上的設置與源 SQL Server 實例中的設置保持一致。 在運行第一次性能比較之前，請確保沒有更改相容性級別或加密等設置，或者接受啟用的某些新功能可能會影響某些查詢的風險。 若要減少移轉的風險，請只在進行效能監視後變更資料庫相容性層級。
- 為[通用實現存儲最佳實踐指南](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525)，例如預先分配檔案大小以獲得更好的性能。
- 瞭解[可能導致託管實例和 SQL Server 之間性能差異的關鍵環境差異]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)，並確定可能影響性能的風險。
- 請確保在託管實例上保持啟用的查詢存儲和自動調優。 這些功能使您能夠測量工作負載性能並自動解決潛在的性能問題。 瞭解如何使用查詢存儲作為獲取資料庫相容性級別更改前後工作負載性能資訊的最佳工具，如[升級到較新的 SQL Server 版本期間保持性能穩定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)中所述。
準備好與本地環境盡可能相媲美的環境後，就可以開始運行工作負荷並衡量性能。 測量過程應包括您在[源 SQL Server 上創建工作負載度量的基準性能時測量的相同](#create-performance-baseline)參數。
因此，您應該將性能參數與基線進行比較，並確定關鍵差異。

> [!NOTE]
> 在許多情況下，您將無法在託管實例和 SQL 伺服器上獲得完全符合的性能。 託管實例是 SQL Server 資料庫引擎，但託管實例上的基礎結構和高可用性配置可能會帶來一些差異。 您可能期望某些查詢速度更快，而其他查詢可能較慢。 比較的目的是驗證託管實例中的工作負載性能是否與 SQL Server 上的性能（平均）相匹配，並標識是否有任何關鍵查詢與性能不匹配的原始性能。

性能比較的結果可能是：
- 託管實例上的工作負載性能與 SQL Server 上的工作負載性能保持一致或更好。 在這種情況下，您已成功確認遷移成功。
- 工作負荷中的大多數性能參數和查詢工作正常，但性能下降的例外情況除外。 在這種情況下，您需要確定差異及其重要性。 如果有一些重要查詢的性能下降，則應調查基礎 SQL 計畫是否已更改或查詢達到一些資源限制。 在這種情況下，緩解可能是直接或使用計劃指南、重建或創建可能影響計畫的統計資訊和索引，對關鍵查詢（例如更改相容性級別、遺留基數估計器）應用一些提示。 
- 與源 SQL Server 相比，託管實例上的大多數查詢速度較慢。 在這種情況下，嘗試確定差異的根本原因，例如[達到某些資源限制]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics)，如 IO 限制、記憶體限制、實例日誌速率限制等。如果沒有可能導致差異的資源限制，請嘗試更改資料庫的相容性級別或更改資料庫設置（如舊基數估計）並重新啟動測試。 查看託管實例或查詢存儲視圖提供的建議，以確定性能倒退的查詢。

> [!IMPORTANT]
> 託管實例具有預設情況下啟用的內置自動計畫更正功能。 此功能可確保在粘貼中工作正常的查詢將來不會降級。 請確保此功能已啟用，並且在更改新設置之前，您已使用舊設置執行工作負載足夠長的時間，以使託管實例能夠瞭解基準性能和計畫。

更改參數或升級服務層以收斂到最佳配置，直到獲得適合您需求的工作負載性能。

### <a name="monitor-performance"></a>監視效能

託管實例提供了許多用於監視和故障排除的高級工具，您應該使用它們來監視實例的性能。 您需要監視的一些參數包括：
- 實例上的 CPU 使用率來確定預配的 vCore 數量是否適合您的工作負載。
- 託管實例上的頁面壽命期望值確定[是否需要額外的記憶體](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)。
- 等待類似`INSTANCE_LOG_GOVERNOR`或`PAGEIOLATCH`將告訴您是否具有存儲 IO 問題的統計資訊，尤其是在通用層，您可能需要預先分配檔以獲得更好的 IO 性能。

## <a name="leverage-advanced-paas-features"></a>利用高級 PaaS 功能

在完全託管的平臺上驗證工作負載性能是否匹配 SQL Server 工作負載性能後，請利用作為 SQL 資料庫服務的一部分自動提供的優勢。 

即使您在遷移期間未對託管實例進行某些更改，在操作實例時也很有可能打開某些新功能，以利用最新的資料庫引擎改進。 僅當[資料庫相容性級別發生更改](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)後，才會啟用某些更改。


比方說，您不需要在受控執行個體上建立備份 (服務會自動為您執行備份)。 您無法再擔心如何排程、使用及管理備份。 託管實例使您能夠使用[時間點恢復 （PITR）](sql-database-recovery-using-backups.md#point-in-time-restore)還原到此保留期內的任何時間點。 此外，您不需要擔心如何設定為高可用性，因為[高可用性](sql-database-high-availability.md)已內建。

為了加強安全性，請考慮使用[Azure 活動目錄身份驗證](sql-database-security-overview.md)、[審核](sql-database-managed-instance-auditing.md)、[威脅檢測](sql-database-advanced-data-security.md)、[行級安全和](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)[動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)。

除了高級管理和安全功能外，託管實例還提供一組高級工具，可説明您[監視和調整工作負載](sql-database-monitor-tune-overview.md)。 [Azure SQL 分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql)使您能夠監視大量託管實例，並集中監視大量實例和資料庫。 託管實例中的[自動調優](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)持續監視 SQL 計畫執行統計資訊的性能，並自動修復已識別的性能問題。

## <a name="next-steps"></a>後續步驟

- 有關託管實例的資訊，請參閱[什麼是託管實例？](sql-database-managed-instance.md)
- 有關包含從備份還原的教程，請參閱[創建託管實例](sql-database-managed-instance-get-started.md)。
- 有關使用 DMS 進行遷移的教程，請參閱[使用 DMS 將本機資料庫遷移到託管實例](../dms/tutorial-sql-server-to-managed-instance.md)。  
