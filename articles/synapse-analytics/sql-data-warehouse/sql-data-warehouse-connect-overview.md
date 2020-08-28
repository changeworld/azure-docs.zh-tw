---
title: 連接到 Synapse SQL 集區
description: 連線到 SQL 集區。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-csharp
ms.openlocfilehash: ab169f7b814dedbc63f3cb6cddbfb822bdb57ab9
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016439"
---
# <a name="connect-to-synapse-sql-pool"></a>連接到 Synapse SQL 集區

連線到 SQL 集區。

## <a name="find-your-server-name"></a>尋找您的伺服器名稱

下列範例中的伺服器名稱是 sqlpoolservername.database.windows.net。 若要尋找完整的伺服器名稱：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [ **Azure Synapse Analytics**]。
3. 按一下您想要連接的 SQL 集區。
4. 找出完整的伺服器名稱。

   ![完整伺服器名稱](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>支援的驅動程式和連接字串

SQL 集區支援 [ADO.NET](/dotnet/framework/data/adonet?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)、 [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、 [PHP](/sql/connect/php/overview-of-the-php-sql-driver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)和 [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 若要尋找最新版本和文件，請按一下前述的其中一個驅動程式。

若要從 Azure 入口網站自動為您使用的驅動程式產生連接字串，按一下前述範例中的 [顯示資料庫連接字串]。 下列一些範例顯示每個驅動程式的連接字串。

> [!NOTE]
> 請考慮將連線逾時設定為 300 秒，以便在短時間無法使用時能夠維持連線。

### <a name="adonet-connection-string-example"></a>ADO.NET 連接字串範例

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>ODBC 連接字串範例

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>PHP 連接字串範例

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>JDBC 連接字串範例

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>連線設定

SQL 集區會在連接和物件建立期間將某些設定標準化。 這些設定不能覆寫，其中包括︰

| 資料庫設定 | 值 |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |開啟 |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |開啟 |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="next-steps"></a>後續步驟

若要使用 Visual Studio 連接及查詢，請參閱 [使用 Visual Studio 查詢](sql-data-warehouse-query-visual-studio.md)。 若要深入瞭解驗證選項，請參閱 [Azure Synapse Analytics 的驗證](sql-data-warehouse-authentication.md)。
