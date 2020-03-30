---
title: T-SQL 陳述式
description: 指向 SQL 分析中支援的 T-SQL 語句文檔的連結。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 53609049f69f0a3d50430ef512a5bf4a8ffd6d82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351361"
---
# <a name="t-sql-statements-supported-in-sql-analytics"></a>SQL 分析中支援的 T-SQL 語句
指向 SQL 分析中支援的 T-SQL 語句文檔的連結。

## <a name="data-definition-language-ddl-statements"></a>資料定義語言 (DDL) 陳述式
* [更改資料庫](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [更改重要性視圖](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest)（預覽） 
* [ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [更改表](https://msdn.microsoft.com/library/ms190273.aspx)
* [CREATE COLUMNSTORE INDEX](https://msdn.microsoft.com/library/gg492153.aspx)
* [創建資料庫](https://msdn.microsoft.com/library/mt204021.aspx)
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
* [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [創建外部表](https://msdn.microsoft.com/library/dn935021.aspx)
* [創建函數](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [在選擇時創建具體視圖](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)（預覽） 
* [CREATE PROCEDURE](https://msdn.microsoft.com/library/ms187926.aspx)
* [CREATE SCHEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [創建表](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [創建視圖](https://msdn.microsoft.com/library/ms187956.aspx)
* [創建工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/mt146367.aspx)
* [DROP EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/mt146379.aspx)
* [DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [DROP PROCEDURE](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISTICS](https://msdn.microsoft.com/library/ms175075.aspx)
* [掉落表](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [DROP VIEW](https://msdn.microsoft.com/library/ms173492.aspx)
* [丟棄工作負載分類器](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [重 命名](https://msdn.microsoft.com/library/mt631611.aspx)
* [設置RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [截圖表](https://msdn.microsoft.com/library/ms177570.aspx)
* [更新統計資料](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>資料操作語言 (DML) 陳述式
* [刪除](https://msdn.microsoft.com/library/ms189835.aspx)
* [插入](https://msdn.microsoft.com/library/ms174335.aspx)
* [更新](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>資料庫主控台命令
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE（](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)預覽）
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC 放大縮小字體功能 放大縮小字體](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql)功能
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>查詢陳述式
* [選擇](https://msdn.microsoft.com/library/ms189499.aspx)
* [帶common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT 和 INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [解釋](https://msdn.microsoft.com/library/mt631615.aspx)
* [從](https://msdn.microsoft.com/library/ms177634.aspx)
* [使用 PIVOT 和 UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROUP BY](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [按](https://msdn.microsoft.com/library/ms188385.aspx)
* [選項](https://msdn.microsoft.com/library/ms190322.aspx)
* [聯盟](https://msdn.microsoft.com/library/ms180026.aspx)
* [哪裡](https://msdn.microsoft.com/library/ms188047.aspx)
* [返回頁首](https://msdn.microsoft.com/library/ms189463.aspx)
* [別名](https://msdn.microsoft.com/library/mt631614.aspx)
* [搜尋條件](https://msdn.microsoft.com/library/ms173545.aspx)
* [子查詢](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>安全性陳述式
* 權限：[GRANT](https://msdn.microsoft.com/library/ms187965.aspx)、[DENY](https://msdn.microsoft.com/library/ms188338.aspx)、[REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTER AUTHORIZATION](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTER CERTIFICATE](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTER DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTER MASTER KEY](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* [BACKUP CERTIFICATE](https://msdn.microsoft.com/library/ms178578.aspx)
* [CLOSE MASTER KEY](https://msdn.microsoft.com/library/ms188387.aspx)
* [CREATE CERTIFICATE](https://msdn.microsoft.com/library/ms187798.aspx)
* [CREATE DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb677241.aspx)
* [創建登錄](https://msdn.microsoft.com/library/ms189751.aspx)
* [創建主金鑰](https://msdn.microsoft.com/library/ms174382.aspx)
* [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx)
* [創建使用者](https://msdn.microsoft.com/library/ms173463.aspx)
* [DROP CERTIFICATE](https://msdn.microsoft.com/library/ms179906.aspx)
* [DROP DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [DROP MASTER KEY](https://msdn.microsoft.com/library/ms180071.aspx)
* [DROP ROLE](https://msdn.microsoft.com/library/ms174988.aspx)
* [DROP USER](https://msdn.microsoft.com/library/ms189438.aspx)
* [OPEN MASTER KEY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>後續步驟
有關詳細資訊參考，請參閱[SQL 分析 中的 T-SQL 語言元素](sql-data-warehouse-reference-tsql-language-elements.md)，以及 SQL Analytics[中的系統檢視](sql-data-warehouse-reference-tsql-system-views.md)。
