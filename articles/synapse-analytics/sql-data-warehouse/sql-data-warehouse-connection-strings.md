---
title: 連接字串
description: Synapse SQL 池的連接字串
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
ms.openlocfilehash: 4c3f73fb763fa28ac826ebb97c3c325a2408542c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633583"
---
# <a name="connection-strings-for-synapse-sql-pool"></a>Synapse SQL 池的連接字串
您可以使用多個不同的應用程式協定連線到 SQL 池,例如[,ADO.NET、ODBC、PHP](https://msdn.microsoft.com/library/jj730314.aspx)[PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)和[ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx)[JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx)。 以下是每個通訊協定連接字串的一些範例。  您也可以使用 Azure 入口網站來建立連接字串。  

若要使用 Azure 入口網站建置連接字串，請瀏覽至您資料庫的刀鋒視窗，在 [程式集]** 底下，按一下 [顯示資料庫連接字串]**。

## <a name="sample-adonet-connection-string"></a>範例 ADO.NET 連接字串
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>範例 ODBC 連接字串
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>範例 PHP 連接字串
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>範例 JDBC 連接字串
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> 請考慮將連線逾時設定為 300 秒，以便在短時間無法使用時能夠維持連線。
> 
> 

## <a name="next-steps"></a>後續步驟
要開始使用 Visual Studio 和其他應用程式查詢 SQL 池,請參閱[使用 Visual Studio 查詢](sql-data-warehouse-query-visual-studio.md)。