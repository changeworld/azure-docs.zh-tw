---
title: 使用 sqlcmd 連線到 Synapse SQL
description: 使用 sqlcmd 命令列公用程式來連線及查詢無伺服器 SQL 集區和專用 SQL 集區。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 88ee95789bdc1c6ee9884a021067318caab203d4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451642"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>使用 sqlcmd 連線到 Synapse SQL

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

您可以使用 [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 命令列公用程式來連線及查詢 Synapse SQL 內的無伺服器 SQL 集區和專用 SQL 集區。  

## <a name="1-connect"></a>1.連線
若要開始使用 [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)，請開啟命令提示字元，然後輸入 **sqlcmd** 並在後面加上 Synapse SQL 資料庫的連接字串。 連接字串需要下列參數：

* **伺服器 (-S)：** 採用 `<`Server Name`>`.database.windows.net 格式的伺服器
* **資料庫 (-d)：** 資料庫名稱
* **啟用引號識別碼 (-I)：** 必須啟用引號識別碼，才能連接到 Synapse SQL 執行個體

若要使用 SQL Server 驗證，您需要新增使用者名稱和密碼參數︰

* **使用者 (-U)：** `<`使用者`>`表單中的伺服器使用者
* **密碼 (-P)：** 與使用者相關聯的密碼

您的連接字串可能類似下列範例︰

**無伺服器 SQL 集區**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**專用 SQL 集區**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

若要使用 Azure Active Directory 整合式驗證，您需要新增 Azure Active Directory 參數︰

* **Azure Active Directory 驗證 (-G)：** 使用 Azure Active Directory 進行驗證

您的連接字串可能類似下列其中一個範例︰

**無伺服器 SQL 集區**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**專用 SQL 集區**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> 您需要使用 Active Directory [啟用 Azure Active Directory 驗證](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 以進行驗證。

## <a name="2-query"></a>2.查詢

### <a name="use-dedicated-sql-pool"></a>使用專用 SQL 集區

連線之後，您可以對執行個體發出任何支援的 [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) 陳述式。 此範例會在互動模式中提交查詢：

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

針對專用 SQL 集區，後續的範例會說明如何使用 -Q 選項或以管線將 SQL 傳送到 sqlcmd，來執行批次模式查詢：

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>使用無伺服器 SQL 集區

連線之後，您可以對執行個體發出任何支援的 [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) 陳述式。  下列範例會以互動模式提交查詢：

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

針對無伺服器 SQL 集區，後續範例會示範如何使用 -Q 選項或以管線將 SQL 傳送到 sqlcmd，來執行批次模式查詢：

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>後續步驟

如需 sqlcmd 選項的詳細資訊，請參閱 [sqlcmd 文件](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。
