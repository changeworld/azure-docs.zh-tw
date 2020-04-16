---
title: 複製資料到 SQL 伺服器與從 SQL 伺服器複製
description: 瞭解如何透過使用 Azure 資料工廠將資料移入或移入或移入本地的 SQL Server 資料庫或 Azure VM 中。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 063ac32c98d4eb64b676247c0a16f98fa7d1702d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416693"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>使用 Azure 資料工廠將資料複製到/從 SQL 伺服器複製

> [!div class="op_single_selector" title1="選擇使用的 Azure 資料工廠版本:"]
> * [版本 1](v1/data-factory-sqlserver-connector.md)
> * [目前版本](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 數據工廠中的複製活動將數據從 SQL Server 資料庫複製到 SQL Server 資料庫。 它基於[複製活動概述](copy-activity-overview.md)文章,該文章概述了複製活動。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 SQL Server 連接器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)

您可以將資料從 SQL Server 資料庫複製到任何受支援的接收體資料儲存。 或者,您可以將數據從任何受支援的源數據儲存複製到 SQL Server 資料庫。 有關複製活動支援為源或接收體的清單,請參閱[支援資料儲存](copy-activity-overview.md#supported-data-stores-and-formats)表。

具體而言，這個 SQL Server 連接器支援：

- SQL Server 版本 2016、2014、2012、2008 R2、2008 和 2005。
- 使用 SQL 或 Windows 驗證來複製資料。
- 作為源,使用 SQL 查詢或儲存過程檢索數據。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

[不支援 SQL Server Express 資料庫](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017)。

>[!NOTE]
>此連線器現在不支援 SQL Server[永遠加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)。 為了解決這些問題,可以使用通用[ODBC 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式。 有關 ODBC 驅動程式下載和連接字串配置,請按照[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)操作。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定於 SQL Server 資料庫連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

SQL Server 連結服務支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定**為 SqlServer**。 | 是 |
| connectionString |使用 SQL 認證或 Windows 身份驗證指定連接到 SQL Server 資料庫所需的**連接字串**資訊。 請參考下列範例。<br/>您還可以在 Azure 密鑰保管庫中輸入密碼。 如果是 SQL 身份驗證,則`password`從連接 字串中拔出配置。 有關詳細資訊,請參閱[Azure 密鑰保管庫中表和存儲認證](store-credentials-in-key-vault.md)據之後的 JSON 範例。 |是 |
| userName |如果使用 Windows 身份驗證,請指定使用者名。 範例為 **domainname\\username**。 |否 |
| 密碼 |為使用者名指定的使用者帳戶指定密碼。 將此欄位標記為 **「安全字串」 以**將其安全地儲存在 Azure 資料工廠中。 或者,您可以[參考儲存在 Azure 金鑰保管庫中的秘密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果未指定,則使用預設的 Azure 整合執行時。 |否 |

>[!TIP]
>如果錯誤代碼「使用者錯誤失敗到ConnectToSqlServer」和「資料庫的作業階段限制為 XXX 且已到達」之類的消息出錯,請`Pooling=false`添加到 連接字串中,然後重試。

**範例 1:使用 SQL 認證**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 2:在 Azure 金鑰保管庫中使用 SQL 身份驗證與密碼**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 3:使用 Windows 認證**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義數據集的節和屬性的完整清單,請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 SQL Server 數據集支援的屬性清單。

要將資料從 SQL Server 資料庫複製到 SQL Server 資料庫,支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為**SqlServerTable**。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 表/視圖的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有架構的表/視圖的名稱。 此屬性支援向後相容性。 對新工作負載,請使用`schema``table`與 。 | 否 (來源)；是 (接收) |

**範例**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SQL Server 源和接收器支援的屬性清單。

### <a name="sql-server-as-a-source"></a>SQL 伺服器作為來源

若要從 SQL Server 複製資料，請將複製活動中的來源類型設定為 **SqlSource**。 複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動來源的類型屬性設定為**SqlSource**。 | 是 |
| sqlReaderQuery |使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此屬性是從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |這些是預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須與存儲過程參數的名稱和大小寫匹配。 |否 |
| isolationLevel | 指定 SQL 源的事務鎖定行為。 允許的值是:**已讀回提交**( 預設) ,**沒有提交**,**可重複讀取**,**可序列化**,**快照**。 有關詳細資訊,請參閱[此文件](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)。 | 否 |

**需要注意的要點:**

- 如果**SQLReaderQuery**為**SqlSource**指定,則複製活動將針對 SQL Server 源執行此查詢以獲取資料。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果不指定**sqlReaderQuery**或**sqlReader 儲存過程名稱**,則數據集 JSON 的「結構」部分中定義的列用於構造查詢。 查詢`select column1, column2 from mytable`針對 SQL 伺服器運行。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例:使用 SQL 查詢**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**範例:使用儲存程序**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**預存程序定義**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL 伺服器作為接收器

> [!TIP]
> 詳細瞭解支援的寫入行為、配置以及[將數據載入到 SQL Server 的最佳實務](#best-practice-for-loading-data-into-sql-server)中的最佳做法。

若要將資料複製到 SQL Server，請將複製活動中的接收器類型設定為 **SqlSink**。 複製活動的 [接收] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設定為**SqlSink**。 | 是 |
| writeBatchSize |*要插入到 SQL 表中的行數。*<br/>允許的值為整數的資料列數目。 默認情況下,Azure 數據工廠會根據行大小動態確定適當的批處理大小。 |否 |
| writeBatchTimeout |此屬性會指定在逾時前等待批次插入作業完成的時間。<br/>允許的值用於時間跨度。 例如"00:30:00"30 分鐘。 如果未指定值,超時將預設為"02:00:00"。 |否 |
| preCopyScript |此屬性指定複製活動在將資料寫入 SQL Server 之前運行的 SQL 查詢。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會*依批次叫用*。 此選項, 會在執行一次與源資料無關的操作(例如,刪除或截圖)`preCopyScript`請使用屬性 。 | 否 |
| 儲存程序表型態參數名稱 |儲存過程中指定的表類型的參數名稱。  |否 |
| sqlWriterTableType |要在存儲過程中使用的表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| 表選項 | 指定是否根據源架構自動建立接收器表(如果不存在)。 當接收器指定儲存過程或在複製活動中設定暫存副本時,不支援自動表創建。 允許的值是:(`none`預設),。 `autoCreate` |否 |

**範例 1:新增資料**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**範例 2:在複製期間呼叫儲存程序**

若要了解更多詳細資料，請參閱[叫用 SQL 接收中的預存程序](#invoke-a-stored-procedure-from-a-sql-sink)。

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>將資料載入 SQL Server 的最佳做法

將資料複製到 SQL Server 時,可能需要不同的寫入行為:

- [追加:](#append-data)我的源數據只有新記錄。
- [Upsert](#upsert-data):我的源數據同時包含插入和更新。
- [覆蓋](#overwrite-the-entire-table):我想每次重新載入整個維度表。
- [用自定義邏輯編寫](#write-data-with-custom-logic):在最終插入目標表之前,我需要額外的處理。

有關如何在 Azure 數據工廠中配置的各部分和最佳實踐,請參閱相應的部分。

### <a name="append-data"></a>附加資料

追加資料是此 SQL Server 接收器連接器的默認行為。 Azure 數據工廠執行批量插入以高效地寫入表。 您可以在複製活動中相應地配置源和接收器。

### <a name="upsert-data"></a>更新插入資料

**選項 1:** 當您有大量要複製的資料時,請使用以下方法執行 upsert: 

- 首先,使用[臨時表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)使用複製活動批量載入所有記錄。 由於未記錄針對臨時表的操作,因此可以在幾秒鐘內載入數百萬條記錄。
- 在 Azure 資料工廠中運行儲存過程活動以應用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 語句。 使用臨時表作為源執行所有更新或作為單個事務插入。 這樣,往返次數和日誌操作就會減少。 在存儲過程活動結束時,可以截斷臨時表以準備下一個上升週期。

例如,在 Azure 資料工廠中,您可以建立具有「**複製」 活動**與**儲存過程活動**連結的管道。 前者將來源儲存中的資料複製到資料庫臨時表中,例如 **_#UpsertTempTable,** 作為資料集中的表名。 然後,後者調用存儲過程將臨時表的源數據合併到目標表中並清理臨時表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在資料庫中,使用 MERGE 邏輯定義儲存過程,如以下範例,該示例從以前的儲存過程活動中指向。 假設目標是具有三列**的行銷**表:**設定檔代碼、****狀態**與**類別**。 根據 **「設定檔 ID」** 列執行向上操作。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**選項 2:** 您可以選擇[複製活動中呼叫儲存 。](#invoke-a-stored-procedure-from-a-sql-sink) 此方法運行源表中的每一行,而不是使用批量插入作為複製活動中的預設方法,這不適用於大規模 upsert。

### <a name="overwrite-the-entire-table"></a>覆寫整個表

您可以在複製活動接收器中設定**預寫文本**屬性。 在這種情況下,對於運行的每個複製活動,Azure 數據工廠首先運行腳本。 然後運行複製以插入數據。 例如,要用最新資料覆蓋整個表,請指定一個腳本,首先刪除所有記錄,然後再從源批量載入新資料。

### <a name="write-data-with-custom-logic"></a>使用自訂邏輯寫入資料

使用自訂邏輯寫入數據的步驟與[Upsert 資料](#upsert-data)部分中描述的步驟類似。 當您需要在最終將源資料插入目標表之前應用額外處理時,對於大規模,可以執行以下兩項操作之一: 

- 載入到臨時表,然後調用存儲過程。 
- 在複製期間調用存儲過程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

將資料複製到 SQL Server 資料庫時,還可以配置和調用使用者指定的儲存過程,並包含其他參數。 儲存程序使用[表數值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> 調用存儲過程逐行處理數據,而不是使用批量操作,我們不建議進行大規模複製。 從將數據[載入 SQL Server 的最佳實務中](#best-practice-for-loading-data-into-sql-server)瞭解更多資訊。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 例如,您希望在最終將源數據插入目標表之前應用額外處理。 當要合並列、查找其他值並將數據插入到多個表中時,將介紹一些額外的處理示例。

下列範例示範如何使用預存程序，對 SQL Server 資料庫中的資料表執行更新插入。 假設輸入資料與接收器**行銷**表各有三列:**設定檔案 ID、****狀態**與**類別**。 根據**ProfileID**列執行 upsert,並且僅將其應用於名為"ProductA"的特定類別。

1. 在資料庫中,定義與**sqlWriterTableType**同名的表類型。 資料表類型的結構描述會與輸入資料所傳回的結構描述相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在資料庫中,定義與**sqlWriter 儲存過程名稱**相同的儲存過程。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 儲存過程中表類型的參數名稱與在數據集中定義的**表名稱**相同。

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. 在 Azure 資料工廠中,在複製活動中定義**SQL 接收器**部分,如下所示:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL 伺服器的資料類型對應

從 SQL Server 複製資料時,將使用以下映射從 SQL Server 資料類型到 Azure 數據工廠臨時資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| SQL Server 資料類型 | Azure Data Factory 過渡期資料類型 |
|:--- |:--- |
| BIGINT |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 針對對應至 Decimal 過渡期類型的資料類型，Azure Data Factory 目前支援最多 28 個有效位數。 如果您的資料需要大於 28 個有效位數，請考慮轉換成 SQL 查詢中的字串。

## <a name="lookup-activity-properties"></a>尋找活動屬性

要瞭解有關屬性的詳細資訊,請檢查[。](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>取得中繼資料活動屬性

要瞭解有關屬性的詳細資訊,請選擇[中取得中繼資料活動](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>連線問題疑難排解

1. 將 SQL Server 實例設定為接受遠端連接。 啟動**SQL 伺服器管理工作室**,右鍵按**下伺服器**,並選擇**屬性**。 從清單中選擇 **「連線**」,然後選擇「**允許遠端連接到此伺服器**」複選框。

    ![啟用遠端連線](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    關於詳細步驟,請參考[設定遠端存取伺服器設定選項](https://msdn.microsoft.com/library/ms191464.aspx)。

2. 啟動**SQL 伺服器設定管理員**。 展開您想要之執行個體的 [SQL Server 網路組態]****，然後選取 [MSSQLSERVER 的通訊協定]****。 協議顯示在右側窗格中。 通過右鍵按一下 TCP/IP 並選擇**啟用****TCP/IP 啟用 TCP/IP。**

    ![啟用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    有關詳細資訊和啟用 TCP/IP 協定的替代方法,請參閱[啟用或停用伺服器網路協定](https://msdn.microsoft.com/library/ms191294.aspx)。

3. 在同一視窗中,按兩下**TCP/IP**以啟動**TCP/IP 屬性**視窗。
4. 切換到**IP 位址**選項卡。向下捲動以查看**IPAll**部分。 記下**TCP 連接埠**。 預設值為**1433**。
5. 在電腦上建立 **Windows 防火牆規則** ，來允許透過此連接埠的連入流量。 
6. **驗證連接**:要使用完全限定的名稱連接到 SQL Server,請使用其他電腦中的 SQL 伺服器管理工作室。 例如 `"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>後續步驟
有關 Azure 資料工廠複製活動支援為來源及接收器的資料儲存清單,請參考[資料儲存](copy-activity-overview.md#supported-data-stores-and-formats)。
