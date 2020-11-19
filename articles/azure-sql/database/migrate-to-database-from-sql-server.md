---
title: 將 SQL Server 資料庫移轉至 Azure SQL Database
description: 瞭解 SQL Server 資料庫移轉至 Azure SQL Database。
keywords: database migration,sql server database migration,database migration tools,migrate database,migrate sql database,資料庫移轉,sql server 資料庫移轉,資料庫移轉工具,移轉資料庫,移轉 sql database
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: fab52fcea03a2f65c868cfac27f8a8cef115b2be
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917642"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>將 SQL Server 資料庫移轉至 Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在本文中，您將瞭解將 SQL Server 2005 或更新版本的資料庫移轉至 Azure SQL Database 的主要方法。 如需遷移至 Azure SQL 受控執行個體的詳細資訊，請參閱 [將 SQL Server 實例遷移至 AZURE sql 受控執行個體](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md)。 如需選擇遷移選項和工具以遷移至 Azure SQL 的指引，請參閱 [遷移至 AZURE sql](../migration-guides/index.yml)


## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>移轉至單一資料庫或集區資料庫

有兩種主要方法可將 SQL Server 2005 或更新版本的資料庫移轉至 Azure SQL Database。 第一種方法比較簡單，但在移轉期間需要一些可能較長期的停機時間。 第二種方法比較複雜，但可大幅免去移轉期間的停機時間。

不論是哪一種方法，您都需要使用 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) 來確定來源資料庫與 Azure SQL Database 相容。 SQL Database 正接近與 SQL Server 相關的 [功能](features-comparison.md) ，而不是與伺服器層級和跨資料庫作業相關的問題。 依賴[部分支援或未支援功能](transact-sql-tsql-differences-sql-server.md)的資料庫和應用程式需要一些[再造來修正這些不相容情況](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)，然後才能移轉 SQL Server 資料。

> [!NOTE]
> 若要將非 SQL Server 資料庫 (包括 Microsoft Access、Sybase、MySQL Oracle 和 DB2) 移轉到 Azure SQL Database，請參閱 [SQL Server 移轉小幫手](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/)。

## <a name="method-1-migration-with-downtime-during-the-migration"></a>方法 1：在移轉期間會停機的移轉作業

 如果您可以承受一些停機時間，或您正在執行生產資料庫的測試遷移以供稍後進行遷移，請使用這個方法來遷移至單一或集區資料庫。 如需教學課程，請參閱[移轉 SQL Server Database](../../dms/tutorial-sql-server-to-azure-sql.md)。

下列清單包含使用此方法來進行 SQL Server 資料庫之單一或集區資料庫移轉時的一般工作流程。 若要遷移至 SQL 受控執行個體，請參閱 [遷移至 sql 受控執行個體](../managed-instance/migrate-to-instance-from-sql-server.md)。

  ![VSSSDT 移轉圖表](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. 使用最新版的 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) \(英文\) 來[評估](/sql/dma/dma-assesssqlonprem) \(英文\) 資料庫的相容性。
2. 準備 Transact-SQL 指令碼形式的任何必要修正。
3. 針對要移轉的來源資料庫建立交易一致性複本，或是在進行移轉時，防止在來源資料庫中進行新交易。 完成後面這個選項的方法包括停用用戶端連線或建立[資料庫快照集](/sql/relational-databases/databases/create-a-database-snapshot-transact-sql)。 移轉之後，您可能能夠使用異動複寫來更新所移轉的資料庫，以反映在移轉截止點之後所發生的變更。 請參閱[使用異動移轉來進行移轉](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)。  
4. 部署 Transact-SQL 指令碼，將修正套用至資料庫複本。
5. 使用 Data Migration Assistant 將資料庫複本[遷移](/sql/dma/dma-migrateonpremsql)至 Azure SQL Database 中的新資料庫。

> [!NOTE]
> 您也可以不使用 DMA，而是使用 BACPAC 檔案。 請參閱 [Azure SQL Database 中的將 BACPAC 檔案匯入至新的資料庫](database-import.md)。

### <a name="optimizing-data-transfer-performance-during-migration"></a>將移轉期間的資料傳輸效能最佳化

下列清單包含可在匯入程序期間獲得最佳效能的建議。

- 選擇預算許可的最高服務層級和計算大小，以獲得最大傳輸效能。 移轉完成後，您可以縮小層級以節省成本。
- 盡量縮短 BACPAC 檔案和目的地資料中心之間的距離。
- 在遷移期間停用 autostatistics
- 分割區資料表與索引
- 捨棄索引檢視表，然後於移轉完成後重新建立
- 將很少查詢的歷程記錄資料移至另一個資料庫，並將此歷程記錄資料移轉至 Azure SQL Database 中的個別資料庫。 您接著可以使用[彈性查詢](elastic-query-overview.md)查詢此歷程記錄資料。

### <a name="optimize-performance-after-the-migration-completes"></a>在移轉完成後將效能最佳化

在移轉完成後，執行完整掃描以[更新統計資料](/sql/t-sql/statements/update-statistics-transact-sql)。

## <a name="method-2-use-transactional-replication"></a>方法 2：使用異動複寫

當您無法在進行遷移時，從生產環境中移除 SQL Server 資料庫時，您可以使用 SQL Server 異動複寫做為您的遷移解決方案。 若要使用此方法，來源資料庫必須符合[異動複寫需求](./replication-to-sql-database.md)且與 Azure SQL Database 相容。 如需使用 AlwaysOn 的 SQL 複寫相關資訊，請參閱[設定 AlwaysOn 可用性群組 (SQL Server) 的複寫](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)。

若要使用此解決方案，您可以將 Azure SQL Database 中的資料庫設定為您想要遷移之 SQL Server 實例的訂閱者。 異動複寫散發者會在新交易繼續進行的同時，從要被同步處理的資料庫 (發行者) 同步處理資料。

使用異動複寫時，您的資料或架構所做的所有變更都會顯示在 Azure SQL Database 的資料庫中。 當同步處理完成且您已準備好遷移之後，請變更應用程式的連接字串，以將其指向您的資料庫。 當異動複寫清空留在來源資料庫上的所有變更，並且您的所有應用程式都指向 Azure DB 之後，您便可以將異動複寫解除安裝。 您在 Azure SQL Database 中的資料庫現在是您的生產系統。

 ![SeedCloudTR 圖表](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> 您也可以使用異動複寫以移轉來源資料庫的子集。 您複寫至 Azure SQL Database 的發佈可以限制為複寫的資料庫中資料表的子集。 針對要複寫的每一個資料表，您可以將資料限制在資料列的子集和 (或) 資料行的子集。

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>使用異動複寫工作流程移轉到 SQL Database

> [!IMPORTANT]
> 使用 SQL Server Management Studio 的最新版本，以與 Azure 和 SQL Database 的更新保持同步。 舊版 SQL Server Management Studio 無法將 SQL Database 設定為訂閱者。 [更新 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。

1. 設定散發套件
   - [使用 SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [使用 Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. 建立發佈
   - [使用 SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [使用 Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. 建立訂閱
   - [使用 SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [使用 Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

一些秘訣和移轉至 SQL Database 的差異

- 使用本機散發者
  - 這麼做會影響伺服器的效能。
  - 如果無法接受效能影響，您可以使用另一部伺服器，但它會增加管理和系統管理的複雜度。
- 當選取快照集資料夾時，請確定您選取的資料夾足以容納要複寫的每個資料表 BCP。
- 建立快照集會鎖定相關聯的資料表，直到完成為止，因此請適當地排程快照集。
- Azure SQL Database 僅支援推送訂用帳戶。 您只能從來源資料庫新增訂閱者。

## <a name="resolving-database-migration-compatibility-issues"></a>解決資料庫移轉相容性問題

您可能會遇到各種不同的相容性問題，這取決於源資料庫的 SQL Server 版本，以及您要遷移之資料庫的複雜度。 舊版 SQL Server 有更多的相容性問題。 除了使用您選擇之搜尋引擎的目標網際網路搜尋之外，請使用下列資源︰

- [Azure SQL Database 中不支援的 SQL Server 資料庫功能](transact-sql-tsql-differences-sql-server.md)
- [SQL Server 2016 中已停止的 Database Engine 功能](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server)
- [SQL Server 2014 中已停止的 Database Engine 功能](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?viewFallbackFrom=sql-server-2014)
- [SQL Server 2012 中已停止的 Database Engine 功能](/previous-versions/sql/sql-server-2012/ms144262(v=sql.110))
- [SQL Server 2008 R2 中已終止的資料庫引擎功能](/previous-versions/sql/sql-server-2008-r2/ms144262(v=sql.105))
- [SQL Server 2005 中已終止的資料庫引擎功能](/previous-versions/sql/sql-server-2005/ms144262(v=sql.90))

除了搜尋網際網路和使用這些資源，另請使用 [Azure SQL Database 的 Microsoft 問答頁](/answers/topics/azure-sql-database.html) (英文) 或 [StackOverflow](https://stackoverflow.com/) (英文)。

> [!IMPORTANT]
> Azure SQL 受控執行個體可讓您以最短的無相容性問題，遷移現有的 SQL Server 實例和其資料庫。 請參閱 [什麼是受控實例](../managed-instance/sql-managed-instance-paas-overview.md)。

## <a name="next-steps"></a>後續步驟

- 使用 Azure SQL EMEA 工程師部落格上的指令碼來[監視移轉期間的 tempdb 使用量](/archive/blogs/azuresqlemea/lesson-learned-10-monitoring-tempdb-usage)。
- 使用 Azure SQL EMEA 工程師部落格上的指令碼來[監視移轉時資料庫的交易記錄檔空間](/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database)。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)。
- 如需移轉後的 UTC 時間處理相關資訊，請參閱[修改當地時區的預設時區](/archive/blogs/azuresqlemea/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone)。
- 如需移轉後變更資料庫預設語言的相關資訊，請參閱[如何變更 Azure SQL Database 的預設語言](/archive/blogs/azuresqlemea/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database)。
