---
title: 跨相應放大的雲端資料庫報告
description: 如何設定水平資料分割的彈性查詢
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: ced546f8f4375433d9fcd59f7ce46f9604f72921
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443125"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>跨相應放大的雲端資料庫報告 (預覽)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![跨分區查詢][1]

分區化資料庫跨相應放大的資料層發佈資料列。 所有參與的資料庫都有相同的結構描述，也稱為水平資料分割。 使用彈性查詢，您可以建立跨越分區化資料庫中所有資料庫的報告。

如需快速入門，請參閱 [跨相應放大雲端資料庫的報告](elastic-query-getting-started.md)。

如需非分區化資料庫，請參閱 [對不同結構描述的雲端資料庫執行查詢](elastic-query-vertical-partitioning.md)。

## <a name="prerequisites"></a>必要條件

* 使用彈性資料庫用戶端程式庫，建立分區對應。 請參閱 [分區對應管理](elastic-scale-shard-map-management.md)。 或使用 [開始使用彈性資料庫工具](elastic-scale-get-started.md)中的範例應用程式。
* 或者，請參閱 [轉換現有的資料庫以使用彈性資料庫工具](elastic-convert-to-use-elastic-tools.md)。
* 使用者必須擁有 ALTER ANY EXTERNAL DATA SOURCE 權限。 這個權限包含在 ALTER DATABASE 權限中。
* 需有 ALTER ANY EXTERNAL DATA SOURCE 權限，才能參考基礎資料來源。

## <a name="overview"></a>概觀

這些陳述式可在彈性查詢資料庫中建立分區化資料層的中繼資料表示法。

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 建立資料庫範圍的主要金鑰和認證

彈性查詢使用認證連接到遠端資料庫。  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> 請確定 *" \<username\> "* 不包含任何 *" \@ servername"* 尾碼。

## <a name="12-create-external-data-sources"></a>1.2 建立外部資料來源

語法：

```sql
<External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
        (TYPE = SHARD_MAP_MANAGER,
                   LOCATION = '<fully_qualified_server_name>',
        DATABASE_NAME = ‘<shardmap_database_name>',
        CREDENTIAL = <credential_name>,
        SHARD_MAP_NAME = ‘<shardmapname>’
               ) [;]
```

### <a name="example"></a>範例

```sql
CREATE EXTERNAL DATA SOURCE MyExtSrc
WITH
(
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
);
```

擷取目前的外部資料來源清單︰

```sql
select * from sys.external_data_sources;
```

外部資料來源會參考分區對應。 彈性查詢會接著使用外部資料來源和基礎分區對應來列舉參與資料層的資料庫。
在彈性查詢處理期間，使用相同的認證來讀取分區對應和存取分區上的資料。

## <a name="13-create-external-tables"></a>1.3 建立外部資料表

語法：  

```sql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <sharded_external_table_options> ) }
) [;]  

<sharded_external_table_options> ::=
  DATA_SOURCE = <External_Data_Source>,
  [ SCHEMA_NAME = N'nonescaped_schema_name',]
  [ OBJECT_NAME = N'nonescaped_object_name',]
  DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN
```

**範例**

```sql
CREATE EXTERNAL TABLE [dbo].[order_line](
     [ol_o_id] int NOT NULL,
     [ol_d_id] tinyint NOT NULL,
     [ol_w_id] int NOT NULL,
     [ol_number] tinyint NOT NULL,
     [ol_i_id] int NOT NULL,
     [ol_delivery_d] datetime NOT NULL,
     [ol_amount] smallmoney NOT NULL,
     [ol_supply_w_id] int NOT NULL,
     [ol_quantity] smallint NOT NULL,
      [ol_dist_info] char(24) NOT NULL
)

WITH
(
    DATA_SOURCE = MyExtSrc,
     SCHEMA_NAME = 'orders',
     OBJECT_NAME = 'order_details',
    DISTRIBUTION=SHARDED(ol_w_id)
);
```

從目前的資料庫擷取外部資料表清單：

```sql
SELECT * from sys.external_tables;
```

捨棄外部資料表：

```sql
DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]
```

### <a name="remarks"></a>備註

DATA\_SOURCE 子句會定義用於外部資料表的外部資料來源 (分區對應)。  

SCHEMA\_NAME 和 OBJECT\_NAME 子句會將外部資料表定義對應至不同結構描述中的資料表。 如果省略，即會假設遠端物件的結構描述為 "dbo" 並假設其名稱與所定義的外部資料表名稱相同。 如果您的遠端資料表名稱已存在於您要建立外部資料表的資料庫中，這會很有用。 例如，您想要定義外部資料表以取得向外延展之資料層上目錄檢視或 DMV 的彙總檢視。 由於目錄檢視和 DMV 已經存在於本機，所以您無法將其名稱使用於外部資料表定義。 您需改為在 SCHEMA\_NAME 和/或 OBJECT\_NAME 子句中，使用不同的名稱以及使用目錄檢視名稱或 DMV 名稱。 (請參閱下面的範例)。

DISTRIBUTION 子句會指定用於此資料表的資料散發。 查詢處理器會利用 DISTRIBUTION 子句中提供的資訊來建置最有效率的查詢計劃。

1. **分區化** 表示資料會在資料庫之間水準分割。 用於資料散發的分割索引鍵是 **<sharding_column_name>** 參數。
2. 「**已**複寫」表示資料表的相同複本存在於每個資料庫上。 您必須負責確保複本在所有資料庫上都相同。
3. **四捨五入 \_** 配置資源表示使用應用程式相依的散發方法，以水準方式分割資料表。

**資料層參考**：外部資料表 DDL 指的是外部資料來源。 外部資料源會指定分區對應，以提供外部資料表，以找出您資料層中的所有資料庫所需的資訊。

### <a name="security-considerations"></a>安全性考量

可存取外部資料表的使用者可以在外部資料來源定義中所提供的認證下，自動取得基礎遠端資料表的存取權。 避免透過外部資料來源認證提高不想提高的權限。 針對外部資料表使用 GRANT 或 REVOKE，如同一般資料表一般。  

一旦您已定義外部資料來源和外部資料表，現在您可以對外部資料表使用完整的 T-SQL。

## <a name="example-querying-horizontal-partitioned-databases"></a>範例︰查詢水平資料分割的資料庫

下列查詢會在倉儲、訂單和訂單行之間執行三向聯結，並使用數個匯總和選擇性篩選。 它會假設 (1) 水準資料分割 (分區化) 和 (2) 倉儲識別碼資料行分區化倉儲、訂單和訂單行，且彈性查詢可以在分區上共置聯結，並以平行方式處理分區上查詢的昂貴部分。

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>用於遠端 T-SQL 執行的預存程序：sp\_execute_remote

彈性查詢也會介紹可供直接存取分區的預存程序。 預存程序稱為 [sp\_execute\_remote](https://msdn.microsoft.com/library/mt703714)，可用來在遠端資料庫上執行遠端預存程序或 T-SQL 程式碼。 它需要以下參數：

* 資料來源名稱 (nvarchar)：RDBMS 類型的外部資料來源名稱。
* 查詢 (nvarchar)：對每個分區執行的 T-SQL 查詢。
* 參數宣告 (nvarchar) - 選用：含有查詢參數 (如 sp_executesql) 中所用參數的資料類型定義的字串。
* 參數值清單 - 選用：以逗號分隔的參數值清單 (如 sp_executesql)。

sp\_execute\_remote 會使用叫用參數中提供的外部資料來源，在遠端資料庫上執行指定的 T-SQL 陳述式。 它會使用外部資料來源的認證連接 shardmap 管理員資料庫和遠端資料庫。  

範例：

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>工具的連線能力

使用一般 SQL Server 連接字串，將您的應用程式、BI 和資料整合工具連接到資料庫，並使用您的外部資料表定義。 請確定 SQL Server 可支援做為您的工具的資料來源。 然後像任何其他連接到工具的 SQL Server 資料庫一樣，參考彈性查詢資料庫，並如同本機資料表一樣，從您的工具或應用程式使用外部資料表。

## <a name="best-practices"></a>最佳作法

* 確定已將彈性查詢端點資料庫授與 shardmap 資料庫的存取權，並透過 SQL Database 防火牆取得所有分區。  
* 驗證或強制執行外部資料表所定義的資料分佈。 如果您的實際資料分佈與資料表定義中指定的散發不同，您的查詢可能會產生非預期的結果。
* 彈性查詢目前不會執行分區刪除，因為分區化索引鍵的述詞允許安全地排除處理某些分區。
* 彈性查詢最適合可在分區完成大部分運算的查詢。 使用可在分區上評估的選擇性篩選述詞，或在所有分區上以資料分割對齊方式來聯結資料分割索引鍵，通常可以獲得最佳查詢效能。 其他查詢模式可能需要從分區載入大量資料至前端節點，但效能可能不佳。

## <a name="next-steps"></a>後續步驟

* 如需彈性查詢的概觀，請參閱[彈性查詢概觀](elastic-query-overview.md)。
* 若要開始撰寫程式碼，請參閱 [開始使用跨資料庫查詢 (垂直資料分割)](elastic-query-getting-started-vertical.md)。
* 如需垂直資料分割之資料的語法和範例查詢，請參閱[查詢垂直資料分割的資料](elastic-query-vertical-partitioning.md)
* 如需水平資料分割 (分區化) 教學課程，請參閱[開始使用彈性查詢進行水平資料分割 (分區化)](elastic-query-getting-started.md)。
* 如需會在單一遠端 Azure SQL Database 或一組在水平資料分割配置中作為分區之資料庫上執行 Transact-SQL 陳述式的預存程序，請參閱 [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714)。

<!--Image references-->
[1]: ./media/elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
