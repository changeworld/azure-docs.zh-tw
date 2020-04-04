---
title: 使用 sqlcmd 連線
description: 使用 sqlcmd 命令列實用程式連接到和查詢 Synapse SQL 池。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cfb1b7558f8d7675009a0ebc729cc1a560f03d12
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633384"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>使用 sqlcmd 連線到 Synapse SQL 池
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用 [sqlcmd][sqlcmd] 命令行實用程式連接到和查詢 SQL 池。  

## <a name="1-connect"></a>1. 連線
要開始使用 [sqlcmd][sqlcmd],請打開命令提示符並輸入**sqlcmd,** 然後輸入 SQL 池資料庫的連接字串串。 連接字串需要下列參數：

* **伺服器 (-S)：** 採用 `<`Server Name`>`.database.windows.net 格式的伺服器
* **資料庫 (-d):** 資料庫名稱。
* **開啟報價識別碼 (-I):** 必須啟用已引用的標識符才能連接到 SQL 池實例。

若要使用 SQL Server 驗證，您需要新增使用者名稱/密碼參數︰

* **使用者 (-U)：**`<`使用者`>`表單中的伺服器使用者
* **密碼 (-P)：** 與使用者相關聯的密碼。

例如，您的連接字串看起來可能如下所示︰

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

若要使用 Azure Active Directory 整合式驗證，您需要新增 Azure Active Directory 參數︰

* **Azure Active Directory 驗證 (-G)：** 使用 Azure Active Directory 進行驗證

例如，您的連接字串看起來可能如下所示︰

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> 您需要使用 Active Directory [啟用 Azure Active Directory 驗證](sql-data-warehouse-authentication.md) 以進行驗證。
> 
> 

## <a name="2-query"></a>2. 查詢
連線之後，您可以對執行個體發出任何支援的 Transact-SQL 陳述式。  此範例會在互動模式中提交查詢。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

後續的這些範例會說明如何使用 -Q 選項或以管線將 SQL 傳送到 sqlcmd，來執行批次模式查詢。

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>後續步驟
關於 sqlcmd 中可用選項的詳細資訊,請參閱[sqlcmd 文件](https://msdn.microsoft.com/library/ms162773.aspx)。
