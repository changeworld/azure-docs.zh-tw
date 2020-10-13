---
title: 開始使用跨資料庫查詢
description: 如何使用具有垂直分割資料庫的彈性資料庫查詢
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 089cc8f22248a2b7152afaf951f6028a05682648
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443320"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>開始使用跨資料庫查詢 (垂直資料分割) (預覽)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 彈性資料庫查詢 (預覽) 可讓您執行使用單一連接點跨越多個資料庫的 T-SQL 查詢。 本文適用於[垂直分割的資料庫](elastic-query-vertical-partitioning.md)。  

完成時，您將：了解如何設定和使用 Azure SQL Database 以執行跨越多個相關資料庫的查詢。

如需關於彈性資料庫查詢功能的詳細資訊，請參閱 [Azure SQL Database 彈性資料庫查詢概觀](elastic-query-overview.md)。

## <a name="prerequisites"></a>必要條件

必須具備 ALTER ANY EXTERNAL DATA SOURCE 權限。 這個權限包含在 ALTER DATABASE 權限中。 需有 ALTER ANY EXTERNAL DATA SOURCE 權限，才能參考基礎資料來源。

## <a name="create-the-sample-databases"></a>建立範例資料庫

首先，在相同或不同的伺服器中建立兩個資料庫、 **客戶** 和 **訂單**。

在 **Orders** 資料庫上執行下列查詢，以建立 **OrderInformation** 資料表及輸入範例資料。

```tsql
CREATE TABLE [dbo].[OrderInformation](
    [OrderID] [int] NOT NULL,
    [CustomerID] [int] NOT NULL
    )
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
```

現在，在 **Customers** 資料庫上執行下列查詢，以建立 **CustomerInformation** 資料表及輸入範例資料。

```tsql
CREATE TABLE [dbo].[CustomerInformation](
    [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NULL,
    [Company] [varchar](50) NULL
    CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
)
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')
```

## <a name="create-database-objects"></a>建立資料庫物件

### <a name="database-scoped-master-key-and-credentials"></a>資料庫範圍的主要金鑰和認證

1. 在 Visual Studio 中開啟 SQL Server Management Studio 或 SQL Server Data Tools
2. 連接至 Orders 資料庫，並執行下列 T-SQL 命令：

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';  
    ```

    "username" 和 "password" 應該是用來登入 Customers 資料庫的使用者名稱和密碼。
    目前不支援使用 Azure Active Directory 與彈性查詢進行驗證。

### <a name="external-data-sources"></a>外部資料來源

若要建立外部資料來源，請在 Orders 資料庫上執行下列命令：

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = RDBMS,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'Customers',
    CREDENTIAL = ElasticDBQueryCred,
) ;
```

### <a name="external-tables"></a>外部資料表

在符合 CustomerInformation 資料表定義的 Orders 資料庫上，建立外部資料表：

```tsql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NOT NULL,
    [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>執行範例彈性資料庫 T-SQL 查詢

在您已定義外部資料來源和外部資料表後，您現在即可使用 T-SQL 來查詢外部資料表。 在 Orders 資料庫上執行此查詢：

```tsql
SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
FROM OrderInformation
INNER JOIN CustomerInformation
ON CustomerInformation.CustomerID = OrderInformation.CustomerID
```

## <a name="cost"></a>Cost

目前，彈性資料庫查詢功能算在 Azure SQL Database 的成本內。  

如需定價資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database)。

## <a name="next-steps"></a>後續步驟

* 如需彈性查詢的概觀，請參閱[彈性查詢概觀](elastic-query-overview.md)。
* 如需垂直資料分割之資料的語法和範例查詢，請參閱[查詢垂直資料分割的資料](elastic-query-vertical-partitioning.md)
* 如需水平資料分割 (分區化) 教學課程，請參閱[開始使用彈性查詢進行水平資料分割 (分區化)](elastic-query-getting-started.md)。
* 如需水平資料分割之資料的語法和範例查詢，請參閱[查詢水平資料分割的資料](elastic-query-horizontal-partitioning.md)
* 如需會在單一遠端 Azure SQL Database 或一組在水平資料分割配置中作為分區之資料庫上執行 Transact-SQL 陳述式的預存程序，請參閱 [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714)。
