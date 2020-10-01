---
title: 將 CSV 檔案中的資料載入 (bcp) 的資料庫
description: 對於較小的資料大小，請使用 bcp 將資料匯入 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: e59cc056d92206f13b746f040f3563c6c6e0af04
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91616766"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>將資料從 CSV 載入 Azure SQL Database 或 SQL 受控執行個體 (一般檔案) 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

您可以使用 bcp 命令列公用程式，將 CSV 檔案中的資料匯入 Azure SQL Database 或 Azure SQL 受控執行個體。

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您需要：

* Azure SQL Database 中的資料庫
* 已安裝的 bcp 命令列公用程式
* 已安裝的 sqlcmd 命令列公用程式

您可以從 [Microsoft 下載中心][Microsoft Download Center]下載 bcp 和 sqlcmd 公用程式。

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII 或 UTF-16 格式的資料

如果您使用您自己的資料嘗試本教學課程，您的資料必須使用 ASCII 或 UTF-16 編碼，因為 bcp 不支援 UFT-8。

## <a name="1-create-a-destination-table"></a>1. 建立目的地資料表

在 SQL Database 中定義做為目的地資料表的資料表。 資料表中的資料行必須對應到資料檔的每一個資料列中的資料。

若要建立資料表，請開啟命令提示字元並使用 sqlcmd.exe 執行下列命令︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. 建立源資料檔案

開啟 [記事本]，將下列幾行資料複製到新的文字檔，然後將此檔案儲存到本機暫存目錄 C:\Temp\DimDate2.txt。 此資料是 ASCII 格式。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(選擇性) 若要從 SQL Server 資料庫匯出您自己的資料，請開啟命令提示字元並執行下列命令。 使用您自己的資訊取代 TableName、ServerName、DatabaseName、Username 和 Password。

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. 載入資料

若要載入資料，請開啟命令提示字元並執行下列命令，使用您自己的資訊取代 ServerName、DatabaseName、Username 和 Password。

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

使用此命令來確認已正確載入資料

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

結果應該如下所示：

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>後續步驟

若要移轉 SQL Server 資料庫，請參閱 [SQL Server 資料庫移轉](database/migrate-to-database-from-sql-server.md)。

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
