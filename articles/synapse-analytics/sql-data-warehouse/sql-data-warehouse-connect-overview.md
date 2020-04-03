---
title: 連接到 Synapse SQL 池
description: 連接到 SQL 池。
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
ms.openlocfilehash: 70dd1ae883ee1f44672dccb802c0e770d7676440
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619061"
---
# <a name="connect-to-synapse-sql-pool"></a>連接到 Synapse SQL 池
連接到 SQL 池。

## <a name="find-your-server-name"></a>尋找您的伺服器名稱
以下範例中的伺服器名稱sqlpoolservername.database.windows.net。 若要尋找完整的伺服器名稱：

1. 跳到[Azure 門戶](https://portal.azure.com)。
2. 點選**Azure 同步分析**。
3. 按下要連接到的 SQL 池。
4. 找出完整的伺服器名稱。
   
    ![完整伺服器名稱](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>支援的驅動程式和連接字串
SQL[ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx)池 支援[ADO.NET、ODBC、PHP](https://msdn.microsoft.com/library/jj730314.aspx)和[JDBC。](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx) [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396) 若要尋找最新版本和文件，請按一下前述的其中一個驅動程式。 

若要從 Azure 入口網站自動為您使用的驅動程式產生連接字串，按一下前述範例中的 [顯示資料庫連接字串]****。 下列一些範例顯示每個驅動程式的連接字串。

> [!NOTE]
> 請考慮將連線逾時設定為 300 秒，以便在短時間無法使用時能夠維持連線。
> 
> 

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
SQL 池在連接和物件創建期間標準化某些設置。 這些設定不能覆寫，其中包括︰

| 資料庫設定 | 值 |
|:--- |:--- |
| [ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx) |開啟 |
| [QUOTED_IDENTIFIERS](https://msdn.microsoft.com/library/ms174393.aspx) |開啟 |
| [DATEFORMAT](https://msdn.microsoft.com/library/ms189491.aspx) |mdy |
| [DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx) |7 |

## <a name="next-steps"></a>後續步驟
若要使用 Visual Studio 連接及查詢，請參閱 [使用 Visual Studio 查詢](sql-data-warehouse-query-visual-studio.md)。 要瞭解有關身份驗證選項的詳細資訊,請參閱[Azure 同步分析的身份驗證](sql-data-warehouse-authentication.md)。

