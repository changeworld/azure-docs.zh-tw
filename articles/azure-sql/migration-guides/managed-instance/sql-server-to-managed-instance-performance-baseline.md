---
title: SQL Server 至 SQL 受控執行個體-效能分析
description: 瞭解如何在將 SQL Server 資料庫移轉至 Azure SQL 受控執行個體時，建立並比較效能基準。
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: c47e4c1278f222feac35a2c6ab0b067c916c0217
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326840"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>遷移效能： SQL Server 至 SQL 受控執行個體效能分析
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

建立效能基準，以將 SQL 受控執行個體上工作負載的效能與 SQL Server 上執行的原始工作負載進行比較。 

## <a name="create-a-baseline"></a>建立基準

在理想的情況下，效能在遷移後很類似或更好，所以請務必在來源上測量並記錄基準效能值，然後將它們與目標環境進行比較。 效能基準是一組參數，可定義來源上的平均工作負載。 

選取一組重要的查詢，並代表您的商務工作負載。 測量並記錄這些查詢的最小/平均/最大持續時間與 CPU 使用量，以及來源伺服器上的效能計量，例如平均/最大 CPU 使用量、平均/最大磁片 IO 延遲、輸送量、IOPS、平均/最大頁面存留期，以及 tempdb 的平均大小上限。 

下列資源可協助定義效能基準： 

   - [監視 CPU 使用量 ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [監視記憶體使用量](/sql/relational-databases/performance-monitor/monitor-memory-usage)  並判斷不同元件使用的記憶體數量，例如緩衝集區、計畫快取、資料行存放區集區、 [記憶體內部 OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)等等。此外，您應該會找到 Page Life memory 效能計數器的平均值和尖峰值。 
   - 使用 [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   view 或 [效能計數器](/sql/relational-databases/performance-monitor/monitor-disk-usage)，監視來源 SQL SERVER 實例上的磁片 IO 使用量。 
   - 藉由檢查動態管理檢視 (或查詢存放區（如果您要從 SQL Server 2016 和更新版本) 進行遷移），來監視工作負載和查詢效能。 識別工作負載中最重要查詢的平均持續時間和 CPU 使用量。 

在遷移之前，應該先解決來源 SQL Server 的任何效能問題。 將已知問題遷移至任何新系統可能會導致非預期的結果，並使任何效能比較失效。 


## <a name="compare-performance"></a>比較效能 

在您定義基準之後，請比較目標 SQL 受控執行個體上的類似工作負載效能。 為了精確度，SQL 受控執行個體環境很重要，可盡可能地與 SQL Server 環境比較。 

有 SQL 受控執行個體基礎結構差異，讓比對效能完全不太可能。 某些查詢的執行速度可能會比預期更快，其他查詢的執行速度可能會比較慢。 這項比較的目標是要確認受控實例中的工作負載效能符合平均) 的 SQL Server (效能，以及找出效能不符合原始效能的任何重要查詢。 

效能比較可能會導致下列結果： 

- 受控實例上的工作負載效能比來源 SQL Server 上的工作負載效能一致或更好。 在此情況下，您已成功確認遷移成功。 

- 工作負載中的大部分效能參數和查詢都會如預期般執行，但有些例外狀況會導致效能降低。 在此情況下，請找出差異及其重要性。 如果有一些重要的查詢效能降低，請調查基礎 SQL 計畫是否已變更，或是查詢是否達到資源限制。 您可以藉由對重要查詢套用一些提示來減輕這個問題 (例如，變更相容性層級、舊版基數估算器) 直接或使用計劃指南。 確定統計資料和索引在這兩個環境中都是最新且相等的。 

- 相較于來源 SQL Server 實例，大部分的查詢在受控實例上的速度較慢。 在此情況下，請嘗試找出差異的根本原因，例如 [達到某些資源限制](../../managed-instance/resource-limits.md#service-tier-characteristics) ，例如 IO、記憶體或實例記錄檔速率限制。 如果沒有造成差異的資源限制，請嘗試變更資料庫的相容性層級，或變更資料庫設定（例如舊版基數估計值），然後重新執行測試。 請查看受控實例或查詢存放區 views 提供的建議，以找出具有回歸效能的查詢。 

SQL 受控執行個體有預設啟用的內建自動計畫修正功能。 這項功能可確保過去正常運作的查詢不會在未來降級。 如果未啟用此功能，請使用舊的設定執行工作負載，讓 SQL 受控執行個體可以瞭解效能基準。 然後，啟用此功能，並使用新的設定重新執行工作負載。 

變更您的測試參數或升級至較高的服務層級，以達到符合您需求的工作負載效能最佳設定。 

## <a name="monitor-performance"></a>監視效能 

SQL 受控執行個體提供用於監視和疑難排解的 advanced tools，您應該使用它們來監視實例的效能。 一些要監視的重要計量如下： 

- 實例上的 CPU 使用量，以判斷您布建的虛擬核心數目是否符合您的工作負載。 
- 您受控實例上的頁面壽命預期，以判斷您是否需要 [額外的記憶體](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)。
-  識別儲存體 IO 問題 INSTANCE_LOG_GOVERNOR 或 PAGEIOLATCH 等統計資料，尤其是在一般用途層上，您可能需要預先配置檔案，以取得更好的 IO 效能。 


## <a name="considerations"></a>考量  

在比較效能時，請考慮下列事項： 

- 來源與目標之間的設定相符。 驗證不同的實例、資料庫和 tempdb 設定在兩個環境之間是相等的。 設定、相容性層級、加密設定、追蹤旗標等的差異都可以扭曲效能。 

- 儲存體會根據 [最佳作法](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)進行設定。 例如，基於一般用途，您可能需要預先設定檔案大小，以改善效能。 

- 有一些 [主要的環境差異](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) ，可能會導致受控實例與 SQL Server 之間的效能差異。 找出與您的環境相關的風險，而這些風險可能會對效能問題造成影響。 

- 您應在 SQL 受控執行個體上啟用查詢存放區和自動調整，因為它們可協助您測量工作負載效能，並自動減輕潛在的效能問題。 



## <a name="next-steps"></a>後續步驟

如需將新的 Azure SQL 受控執行個體環境優化的詳細資訊，請參閱下列資源： 

- [如何識別 Azure SQL 受控執行個體上的工作負載效能與 SQL Server 不同？](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [SQL 受控執行個體和 SQL Server 之間效能差異的主要原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [適用于 Azure SQL 受控執行個體 (一般用途) 的儲存體效能最佳做法和考慮 ](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Azure SQL 受控執行個體的即時效能監視 (這是封存的，這是預期的目標嗎？ ) ](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)