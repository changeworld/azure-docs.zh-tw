---
title: 容量限制-Azure Synapse 分析（舊稱 SQL DW）
description: Azure Synapse 中各種 SQL 分析元件允許的最大值。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a225c375d877ae44c2b21ea8e79e31f17db36878
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198179"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Azure Synapse 分析（先前為 SQL DW）容量限制

Azure Synapse 的各種元件所允許的最大值。

## <a name="workload-management"></a>工作負載管理

| 類別 | 描述 | 最大需求 |
|:--- |:--- |:--- |
| [資料倉儲單位 (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |單一 SQL 集區（資料倉儲）單位的最大 DWU | Gen1：DW6000<br></br>Gen2：DW30000c |
| [資料倉儲單位 (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |每一部伺服器的預設 DTU |54,000<br></br>根據預設，每個 SQL server （例如 myserver.database.windows.net）的 DTU 配額為54000，最多可達 DW5000c。 此配額僅是安全限制。 您可以藉由[建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)，並選取「配額」 做為要求類型來增加配額。  若要計算 DTU 需求，請將7.5 乘以所需的總 DWU，或將9.5 乘以所需的總 cDWU。 例如，<br></br>DW6000 x 7.5 = 45,000 DTU<br></br>DW5000c x 9.5 = 47500 Dtu。<br></br>您可以在入口網站的 [SQL Server] 選項中檢視目前的 DTU 耗用量。 已暫停和未暫停的資料庫都會計入 DTU 配額。 |
| 資料庫連接 |並行開啟的會話數上限 |1024<br/><br/>並行開啟的會話數目會根據選取的 DWU 而有所不同。 DWU600c 和更新版本支援最多1024個開啟的會話。 DWU500c 和以下支援最大並行開啟會話限制512。 請注意，可同時執行的查詢數目有所限制。 超過並行存取限制時，要求會進入內部佇列以等待處理。 |
| 資料庫連接 |準備陳述式的最大記憶體 |20 MB |
| [工作負載管理](resource-classes-for-workload-management.md) |並行查詢數目上限 |128<br/><br/>  最多會執行128個並行查詢，而且剩餘的查詢將會排入佇列。<br/><br/>將使用者指派給較高的資源類別，或當[資料倉儲單位](memory-concurrency-limits.md)設定降低時，並行查詢的數目可能會降低。 系統總是會允許某些查詢 (例如 DMV 查詢) 執行，而不影響並行查詢限制。 如需並行查詢執行的詳細資訊，請參閱[並行最大值](memory-concurrency-limits.md)一文。 |
| [tempdb](sql-data-warehouse-tables-temporary.md) |GB 上限 |每個 DW100c 399 GB。 因此，在 DWU1000c，tempdb 的大小會調整為 3.99 TB。 |
||||

## <a name="database-objects"></a>資料庫物件

| 類別 | 描述 | 最大需求 |
|:--- |:--- |:--- |
| 資料庫 |大小上限 | Gen1：在磁碟上壓縮後 240 TB。 此空間與 tempdb 或記錄檔空間無關，因此此空間為供永久資料表專用。  叢集資料行存放區壓縮估計為 5 X。  當所有資料表都是叢集資料行存放區 (預設的資料表類型) 時，這個壓縮可讓資料庫成長約 1 PB。 <br/><br/> Gen2：資料行存放區資料表的無限制儲存。  資料庫的 Rowstore 部分仍然限制在磁片上壓縮的 240 TB。 |
| Table |大小上限 |資料行存放區資料表的大小不限。 <br>60 TB，適用于在磁片上壓縮的 rowstore 資料表。 |
| Table |每個資料庫的資料表數 | 100,000 |
| Table |資料表的資料行數 |1024 個資料行 |
| Table |每個資料行的位元組 |相依於資料行[資料類型](sql-data-warehouse-tables-data-types.md)。 若為字元資料類型，最大限制可以儲存最多 2 GB 的分頁（資料列溢位）儲存空間。  非 Unicode 字元（例如 char 或 Varchar limit）在資料頁中是8000，資料頁中的 Unicode 字元（例如 Nchar 或 Nvarchar limit）是4000。  使用資料頁儲存體大小來提升效能。 |
| Table |每個資料列的位元組，已定義的大小 |8060 個位元組<br/><br/>每個資料列的位元組數目計算方式和使用頁面壓縮的 SQL Server 所使用的方式相同。 如同 SQL Server，支援資料列溢位儲存體，可讓**可變長度**資料行以非資料列的方式推送。 可變長度的資料列會發送至超出資料列，只有 24 位元組的根會儲存在主要記錄中。 如需詳細資訊，請參閱[超過 8-KB 的資料列溢位資料](https://msdn.microsoft.com/library/ms186981.aspx)。 |
| Table |每個資料表的資料分割 |15,000<br/><br/>為了獲得高效能，建議在仍能支援業務需求的情況下，將您需要的資料分割數目降至最低。 隨著資料分割數目增加，資料定義語言 (DDL) 和資料操作語言 (DML) 作業的負荷會加重，導致效能變慢。 |
| Table |每個資料分割界限值的字元。 |4000 |
| Index |每個資料表的非叢集索引。 |50<br/><br/>僅適用於資料列存放區資料表。 |
| Index |每個資料表的叢集索引。 |1<br><br/>適用於資料列存放區資料表和資料行存放區資料表。 |
| Index |索引鍵的大小。 |900 個位元組。<br/><br/>僅適用於資料列存放區索引。<br/><br/>建立索引時，如果資料行中的現有資料沒有超過 900 個位元組，就可以在 varchar 資料行上建立大小上限超過 900 個位元組的索引。 不過，後續在資料行上執行 INSERT 或 UPDATE 動作時，如果總計大小超過 900 個位元組，將會失敗。 |
| Index |每個索引的索引鍵資料行。 |16<br/><br/>僅適用於資料列存放區索引。 叢集資料行存放區索引包含所有資料行。 |
| 統計資料 |結合資料行值的大小。 |900 個位元組。 |
| 統計資料 |每個統計資料物件的資料行。 |32 |
| 統計資料 |每個資料表的資料行上建立的統計資料。 |30,000 |
| 預存程序 |最大巢狀層級。 |8 |
| 檢視 |每個檢視的資料行數 |1,024 |
||||

## <a name="loads"></a>載入

| 類別 | 描述 | 最大需求 |
|:--- |:--- |:--- |
| PolyBase 載入 |每列 MB 數 |1<br/><br/>Polybase 會載入小於 1 MB 的資料列。 不支援將 LOB 資料類型載入具有叢集資料行存放區索引（CCI）的資料表。<br/><br/> |
||||

## <a name="queries"></a>查詢

| 類別 | 描述 | 最大需求 |
|:--- |:--- |:--- |
| 查詢 |使用者資料表上已排入佇列的查詢。 |1000 |
| 查詢 |系統檢視表上的並行查詢。 |100 |
| 查詢 |系統檢視表上已排入佇列的查詢 |1000 |
| 查詢 |參數個數上限 |2098 |
| Batch |最大容量 |65,536*4096 |
| SELECT 結果 |每個資料列的資料行 |4096<br/><br/>在 SELECT 結果中，每個資料列一律不超過 4096 個資料行。 不保證一定可以有 4096 個。 如果查詢計畫需要暫存資料表，可能會限定每個資料表最多 1024 個資料行。 |
| SELECT |巢狀子查詢 |32<br/><br/>SELECT 陳述式中一律不超過 32 個巢狀子查詢。 不保證一定可以有 32 個。 例如，JOIN 可以將子查詢加入查詢計畫中。 子查詢的數目也受限於可用記憶體。 |
| SELECT |每個 JOIN 的資料行 |1024 個資料行<br/><br/>JOIN 中一律不超過 1024 個資料行。 不保證一定可以有 1024 個。 如果 JOIN 計畫需要比 JOIN 結果更多資料行的暫存資料表，暫存資料表會受限於 1024 的限制。 |
| SELECT |每個 GROUP BY 資料行的位元組。 |8060<br/><br/>GROUP BY 子句中的資料行最多可以有 8060 個位元組。 |
| SELECT |每個 ORDER BY 資料行的位元組 |8060 個位元組<br/><br/>ORDER BY 子句中的資料行最多可以有 8060 個位元組 |
| 每個陳述式的識別項 |參考的識別項個數 |65,535<br/><br/> 查詢的單一運算式中可包含的識別碼數目有限。 超過此數字會導致 SQL Server 錯誤 8632。 如需詳細資訊，請參閱 [內部錯誤：到達運算式服務的限制](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)。 |
| 字串常值 | 陳述式中的字串常值數目 | 20,000 <br/><br/>查詢的單一運算式中的字串常數數目有限。 超過此數字會導致 SQL Server 錯誤 8632。|
||||

## <a name="metadata"></a>中繼資料

| 系統檢視表 | 最大資料列數 |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |最近 1000 個 SQL 要求的 DMS 背景工作角色總數。 |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |儲存在 sys.dm_pdw_exec_requests 中的最近 1000 個 SQL 要求的步驟總數。 |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |儲存在 sys.dm_pdw_exec_requests 中的最近 1000 個 SQL 要求。 |
|||

## <a name="next-steps"></a>後續步驟

如需使用 Azure [Synapse 的建議](cheat-sheet.md)，請參閱功能提要。
