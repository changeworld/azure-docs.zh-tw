---
title: 將資料複製到 SQL Server
description: 深入瞭解如何使用 Azure Data Factory，在內部部署或 Azure VM 中 SQL Server 資料庫來回移動資料。
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
ms.date: 07/15/2020
ms.openlocfilehash: 4e9fc2634b2ed565c02176f22b48adccdec60ce6
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170282"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SQL Server 複製資料

> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 版本："]
> * [第 1 版](v1/data-factory-sqlserver-connector.md)
> * [目前的版本](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料從 SQL Server 資料庫複製到其中。 它是以[複製活動總覽](copy-activity-overview.md)一文為基礎，提供複製活動的一般總覽。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 SQL Server 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

您可以將資料從 SQL Server 資料庫複製到任何支援的接收資料存放區。 或者，您可以將資料從任何支援的來源資料存放區複製到 SQL Server 資料庫。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SQL Server 連接器支援：

- SQL Server 2005 版和更新版本。
- 使用 SQL 或 Windows 驗證來複製資料。
- 作為來源，使用 SQL 查詢或預存程式來抓取資料。
- 作為接收，會根據來源架構，自動建立目的地資料表（如果不存在的話）。在複製期間，將資料附加至資料表，或使用自訂邏輯叫用預存程式。 

不支援[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) 。

>[!NOTE]
>此連接器目前不支援 SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) 。 若要解決此情況，您可以使用[一般 odbc 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式。 請遵循[此指導](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)方針與 ODBC 驅動程式下載及連接字串設定。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SQL Server 資料庫連接器的特定 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SQL Server 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為**SqlServer**。 | 是 |
| connectionString |指定使用 SQL 驗證或 Windows 驗證連接到 SQL Server 資料庫所需的**connectionString**資訊。 請參考下列範例。<br/>您也可以將密碼放在 Azure Key Vault 中。 如果是 SQL 驗證，請 `password` 從連接字串中提取設定。 如需詳細資訊，請參閱資料表後面的 JSON 範例，並[將認證儲存在 Azure Key Vault 中](store-credentials-in-key-vault.md)。 |是 |
| userName |如果您使用 Windows 驗證，請指定使用者名稱。 範例為 **domainname\\username**。 |否 |
| 密碼 |針對您為使用者名稱指定的使用者帳戶指定密碼。 將此欄位標記為**SecureString** ，將它安全地儲存在 Azure Data Factory 中。 或者，可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 若未指定，則會使用預設 Azure Integration Runtime。 |否 |

>[!TIP]
>如果您遇到錯誤碼為 "UserErrorFailedToConnectToSqlServer" 的錯誤，並出現類似「資料庫的會話限制為 XXX 且已達到」的訊息，請將新增 `Pooling=false` 至您的連接字串，然後再試一次。

**範例1：使用 SQL 驗證**

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

**範例2：在 Azure Key Vault 中使用 SQL 驗證搭配密碼**

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

**範例3：使用 Windows 驗證**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 SQL Server 資料集所支援的屬性清單。

若要將資料從和複製到 SQL Server 資料庫，支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為**SqlServerTable**。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 資料表/檢視的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有結構描述的資料表/檢視名稱。 支援此屬性是基於回溯相容性。 對於新的工作負載，請使用 `schema` 和 `table`。 | 否 (來源)；是 (接收) |

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SQL Server 來源和接收端所支援的屬性清單。

### <a name="sql-server-as-a-source"></a>SQL Server 做為來源

若要從 SQL Server 複製資料，請將複製活動中的來源類型設定為 **SqlSource**。 複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為**SqlSource**。 | 是 |
| sqlReaderQuery |使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此屬性是從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |這些是預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程式參數的名稱和大小寫。 |否 |
| isolationLevel | 指定 SQL 來源的異動鎖定行為。 允許的值為： **ReadCommitted**、 **ReadUncommitted**、 **RepeatableRead**、 **Serializable**、 **Snapshot**。 如果未指定，則會使用資料庫的預設隔離等級。 如需詳細資訊，請參閱[這篇文件](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)。 | 否 |

**注意事項：**

- 如果為**SqlSource**指定了**sqlReaderQuery** ，複製活動就會針對 SQL Server 來源執行此查詢，以取得資料。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果您未指定**sqlReaderQuery**或**sqlReaderStoredProcedureName**，則會使用資料集 JSON 的 "structure" 區段中所定義的資料行來建立查詢。 查詢會 `select column1, column2 from mytable` 針對 SQL Server 執行。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例：使用 SQL 查詢**

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

**範例：使用預存程式**

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

### <a name="sql-server-as-a-sink"></a>做為接收器的 SQL Server

> [!TIP]
> 深入瞭解支援的寫入行為、設定和最佳做法，從將[資料載入 SQL Server 的最佳作法](#best-practice-for-loading-data-into-sql-server)。

若要將資料複製到 SQL Server，請將複製活動中的接收器類型設定為 **SqlSink**。 複製活動的 [接收] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的 type 屬性必須設定為**SqlSink**。 | 是 |
| preCopyScript |這個屬性會指定要在將資料寫入 SQL Server 之前，要執行之複製活動的 SQL 查詢。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| tableOption | 指定是否要根據來源架構，[自動建立接收資料表](copy-activity-overview.md#auto-create-sink-tables)（如果不存在）。 當 sink 指定了複製活動中所設定的預存程式或分段複製時，不支援自動建立資料表。 允許的值包為：`none` (預設) 或 `autoCreate`。 |否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會*依批次叫用*。 對於只執行一次且與來源資料無關的作業（例如，刪除或截斷），請使用 `preCopyScript` 屬性。<br>請參閱從[SQL 接收器叫用預存](#invoke-a-stored-procedure-from-a-sql-sink)程式的範例。 | 否 |
| storedProcedureTableTypeParameterName |預存程式中指定之資料表類型的參數名稱。  |否 |
| sqlWriterTableType |要在預存程式中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| writeBatchSize |要插入 SQL 資料表中*每個批次*的資料列數目。<br/>允許的值為整數的資料列數目。 根據預設，Azure Data Factory 會依據資料列大小，以動態方式決定適當的批次大小。 |否 |
| writeBatchTimeout |此屬性會指定在逾時前等待批次插入作業完成的時間。<br/>允許的值適用于 timespan。 例如，"00:30:00" 代表30分鐘。 如果未指定任何值，則 timeout 會預設為 "02:00:00"。 |否 |

**範例1：附加資料**

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
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**範例2：在複製期間叫用預存程式**

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

當您將資料複製到 SQL Server 時，可能需要不同的寫入行為：

- [附加](#append-data)：我的來源資料只有新的記錄。
- [Upsert](#upsert-data)：我的來源資料同時具有插入和更新。
- [覆寫](#overwrite-the-entire-table)：我想要每次重載整個維度資料表。
- [以自訂邏輯撰寫](#write-data-with-custom-logic)：我需要在最後插入目的地資料表之前進行額外的處理。

請參閱各自的章節，以瞭解如何在 Azure Data Factory 和最佳作法中進行設定。

### <a name="append-data"></a>附加資料

附加資料是此 SQL Server 接收連接器的預設行為。 Azure Data Factory 會執行大量插入，以有效率地寫入資料表。 您可以在複製活動中據以設定來源和接收器。

### <a name="upsert-data"></a>更新插入資料

**選項1：** 當您有大量資料要複製時，您可以使用複製活動，將所有記錄大量載入至臨時表，然後執行預存程式活動，在一次的過程中套用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver15)或 INSERT/UPDATE 語句。 

複製活動目前不支援將資料載入資料庫臨時表。 使用多個活動的組合來設定它是一種最先進的方式，請參閱[優化 SQL Database 大量 Upsert 案例](https://github.com/scoriani/azuresqlbulkupsert)。 以下顯示使用永久資料表做為預備環境的範例。

例如，在 Azure Data Factory 中，您可以建立具有與**預存程式活動**連結之**複製活動**的管線。 前者會將來源存放區的資料複製到 SQL Server 臨時表中，例如**UpsertStagingTable**，做為資料集內的資料表名稱。 然後後者會叫用預存程式，將來源資料從臨時表合併至目標資料表，並清除臨時表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在您的資料庫中，定義具有合併邏輯的預存程式，如下列範例所示，這是從先前的預存程式活動所指向。 假設目標是包含三個數據行的**行銷**資料表： **ProfileID**、 **State**和**Category**。 根據**ProfileID**資料行執行 upsert。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**選項2：** 您可以選擇在[複製活動中叫用預存](#invoke-a-stored-procedure-from-a-sql-sink)程式。 這個方法會在來源資料表中執行每個批次（由屬性所控制 `writeBatchSize` ），而不是在複製活動中使用 bulk insert 做為預設方法。

### <a name="overwrite-the-entire-table"></a>覆寫整個資料表

您可以在複製活動接收中設定**preCopyScript**屬性。 在此情況下，針對每個執行的複製活動，Azure Data Factory 先執行腳本。 然後，它會執行複製以插入資料。 例如，若要使用最新的資料來覆寫整個資料表，請在從來源大量載入新資料之前，指定要先刪除所有記錄的腳本。

### <a name="write-data-with-custom-logic"></a>使用自訂邏輯寫入資料

使用自訂邏輯撰寫資料的步驟類似于[Upsert 資料](#upsert-data)一節中所述。 當您需要在最後將來源資料插入目的地資料表之前套用額外處理時，您可以載入至臨時表，然後叫用預存程式活動，或叫用複製活動接收中的預存程式來套用資料。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

當您將資料複製到 SQL Server 資料庫時，您也可以在來源資料表的每個批次上，使用其他參數來設定及叫用使用者指定的預存程式。 預存程式功能會利用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 例如，當您想要在最後將來源資料插入目的地資料表之前，套用額外的處理。 一些額外的處理範例是當您想要合併資料行、查閱其他值，並插入多個資料表時。

下列範例示範如何使用預存程序，對 SQL Server 資料庫中的資料表執行更新插入。 假設輸入資料和接收**行銷**資料表各有三個數據行： **ProfileID**、 **State**和**Category**。 根據**ProfileID**資料行執行 upsert，並只將它套用至名為 "ProductA" 的特定類別。

1. 在您的資料庫中，使用與**sqlWriterTableType**相同的名稱來定義資料表類型。 資料表類型的結構描述會與輸入資料所傳回的結構描述相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在您的資料庫中，使用與**sqlWriterStoredProcedureName**相同的名稱來定義預存程式。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 預存程式中資料表類型的參數名稱與資料集中定義的**tableName**相同。

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

3. 在 Azure Data Factory 中，定義複製活動中的**SQL 接收**區段，如下所示：

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

## <a name="data-type-mapping-for-sql-server"></a>SQL Server 的資料類型對應

當您將資料從和複製到 SQL Server 時，會使用下列從 SQL Server 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

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
| 文字 |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 針對對應至 Decimal 過渡型別的資料類型，目前的複製活動最多支援28個精確度。 如果您的資料需要大於 28 個有效位數，請考慮轉換成 SQL 查詢中的字串。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要了解關於屬性的詳細資料，請參閱 [GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>使用 Always Encrypted

當您使用[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15)將資料從/複製到 SQL Server 時，請使用[一般 ODBC 連接器](connector-odbc.md)，並透過自我裝載 Integration Runtime SQL Server ODBC 驅動程式。 此 SQL Server 連接器目前不支援 Always Encrypted。 

具體而言：

1. 設定自我裝載的 Integration Runtime （如果您沒有的話）。 如需詳細資訊，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md)一文。

2. 從[這裡](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15)下載適用于 SQL Server 的64位 ODBC 驅動程式，並將安裝在 Integration Runtime 機上。 若要深入瞭解此驅動程式的運作方式，請[使用 Always Encrypted 搭配 ODBC driver for SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-ver15#using-the-azure-key-vault-provider)。

3. 建立具有 ODBC 類型的已連結服務，以連接到您的 SQL 資料庫。 若要使用 SQL 驗證，請指定如下所示的 ODBC 連接字串，然後選取 [**基本**身份驗證] 以設定使用者名稱和密碼。

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. 據以 ODBC 類型建立資料集和複製活動。 若要深入瞭解，請參閱[ODBC connector](connector-odbc.md)文章。

## <a name="troubleshoot-connection-issues"></a>連線問題疑難排解

1. 將您的 SQL Server 實例設定為接受遠端連線。 啟動**SQL Server Management Studio**，以滑鼠右鍵按一下 [**伺服器**]，然後選取 [**屬性**]。 從清單中選取 [**連接**]，然後選取 [**允許此伺服器的遠端連線**] 核取方塊。

    ![啟用遠端連線](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    如需詳細步驟，請參閱[設定遠端存取服務器設定選項](https://msdn.microsoft.com/library/ms191464.aspx)。

2. 啟動**SQL Server 組態管理員**。 展開您想要之執行個體的 [SQL Server 網路組態]****，然後選取 [MSSQLSERVER 的通訊協定]****。 通訊協定會出現在右窗格中。 以滑鼠右鍵按一下 [ **tcp/ip** ]，然後選取 [**啟用**]，以啟用 tcp/ip。

    ![啟用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    如需啟用 TCP/IP 通訊協定的詳細資訊和替代方式，請參閱[啟用或停用伺服器網路通訊協定](https://msdn.microsoft.com/library/ms191294.aspx)。

3. 在相同的視窗中，按兩下 [ **tcp/ip** ] 以啟動 [ **tcp/ip 屬性**] 視窗。
4. 切換至 [ **IP 位址**] 索引標籤。向下滾動以查看 [ **IPAll** ] 區段。 記下**TCP 埠**。 預設值為**1433**。
5. 在電腦上建立 **Windows 防火牆規則** ，來允許透過此連接埠的連入流量。 
6. **驗證**連線：若要使用完整名稱連接到 SQL Server，請使用來自不同電腦的 SQL Server Management Studio。 例如 `"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
