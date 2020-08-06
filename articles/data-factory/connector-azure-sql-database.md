---
title: 複製和轉換 Azure SQL Database 中的資料
description: 瞭解如何在 Azure SQL Database 中複製資料，以及如何使用 Azure Data Factory 在 Azure SQL Database 中轉換資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 2df9324c87f13bf757abb314690eb1afa602ee5e
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800308"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure Data Factory 複製和轉換 Azure SQL Database 中的資料

> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 版本："]
>
> - [第 1 版](v1/data-factory-azure-sql-connector.md)
> - [目前的版本](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，從 Azure SQL Database 複製資料，以及使用資料流程來轉換 Azure SQL Database 中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure SQL Database 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)資料表的[複製活動](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

針對複製活動，此 Azure SQL Database 連接器支援下列功能：

- 使用 SQL 驗證和 Azure Active Directory 來複製資料 (Azure AD) 使用服務主體的應用程式權杖驗證，或 Azure 資源的受控識別。
- 作為來源，使用 SQL 查詢或預存程式來抓取資料。
- 作為接收，會根據來源架構，自動建立目的地資料表（如果不存在的話）。在複製期間，將資料附加至資料表，或使用自訂邏輯叫用預存程式。

>[!NOTE]
> 此連接器目前不支援 Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) 。 若要解決此情況，您可以透過自我裝載整合執行時間使用[一般 odbc 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式。 若要深入瞭解，請[使用 Always Encrypted](#using-always-encrypted)一節。 

> [!IMPORTANT]
> 如果您使用 Azure 整合執行時間來複製資料，請設定[伺服器層級防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，讓 Azure 服務可以存取伺服器。
> 如果您使用自我裝載整合執行時間來複製資料，請將防火牆設定為允許適當的 IP 範圍。 此範圍包括用來連接到 Azure SQL Database 的電腦 IP。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 連接器特定的 Azure Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是支援 Azure SQL Database 已連結服務的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**AzureSqlDatabase**。 | 是 |
| connectionString | 指定連接到**connectionString**屬性的 Azure SQL Database 實例所需的資訊。 <br/>您也可以將密碼或服務主體金鑰放在 Azure Key Vault 中。 如果是 SQL 驗證，請 `password` 從連接字串中提取設定。 如需詳細資訊，請參閱資料表後面的 JSON 範例，並[將認證儲存在 Azure Key Vault 中](store-credentials-in-key-vault.md)。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是，當您搭配服務主體使用 Azure AD 驗證時 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為**SecureString** ，將它安全地儲存在 Azure Data Factory 中，或[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | 是，當您搭配服務主體使用 Azure AD 驗證時 |
| tenant | 指定租使用者資訊，例如您的應用程式所在的功能變數名稱或租使用者識別碼。 將滑鼠游標暫留在 Azure 入口網站右上角，即可加以擷取。 | 是，當您搭配服務主體使用 Azure AD 驗證時 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure 整合執行階段 (IR) 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 若未指定，則會使用預設 Azure Integration Runtime。 | 否 |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 驗證](#sql-authentication)
- [Azure AD 應用程式權杖驗證：服務主體](#service-principal-authentication)
- [Azure AD 應用程式權杖驗證：適用于 Azure 資源的受控識別](#managed-identity)

>[!TIP]
>如果您遇到錯誤碼為 "UserErrorFailedToConnectToSqlServer" 的錯誤，並出現類似「資料庫的會話限制為 XXX 且已達到」的訊息，請將新增 `Pooling=false` 至您的連接字串，然後再試一次。

### <a name="sql-authentication"></a>SQL 驗證

**範例：使用 SQL 驗證**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例： Azure Key Vault 中的密碼**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>服務主體驗證

若要使用以服務主體為基礎的 Azure AD 應用程式權杖驗證，請遵循下列步驟：

1. 從 Azure 入口網站 [建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 如果您尚未這麼做，請在 Azure 入口網站上為您的伺服器布建[Azure Active Directory 系統管理員](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)。 Azure AD 系統管理員必須是 Azure AD 使用者或 Azure AD 群組，但不能是服務主體。 此步驟必須完成，如此您才可以在下一個步驟中使用 Azure AD 身分識別，為服務主體建立自主資料庫使用者。

3. 為服務主體 [建立自主資料庫使用者](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) 。 使用 SQL Server Management Studio 之類的工具（具有至少具有 ALTER ANY USER 許可權的 Azure AD 身分識別），連接到您想要從中複製資料的資料庫。 執行下列 T-SQL：
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 如同您一般對 SQL 使用者或其他人所做的一樣，將所需的權限授與服務主體。 執行下列程式碼。 如需更多選項，請參閱[此文件](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. 在 Azure Data Factory 中，設定 Azure SQL Database 連結服務。

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服務主體驗證的連結服務範例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以使用此受控識別來進行 Azure SQL Database 驗證。 指定的處理站可以使用此身分識別從您資料庫存取資料，或從您的資料庫複製資料。

若要使用受控識別驗證，請遵循下列步驟。

1. 如果您尚未這麼做，請在 Azure 入口網站上為您的伺服器布建[Azure Active Directory 系統管理員](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)。 Azure AD 系統管理員可以是 Azure AD 的使用者或 Azure AD 群組。 如果您授與受控識別系統管理員角色，請略過步驟 3 和 4。 系統管理員具有資料庫的完整存取權。

2. 為 Azure Data Factory 受控識別[建立自主資料庫使用者](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 之類的工具（具有至少具有 ALTER ANY USER 許可權的 Azure AD 身分識別），連接到您想要從中複製資料的資料庫。 執行下列 T-SQL：
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 授與 Data Factory 受控識別所需的許可權，就像您一般對 SQL 使用者和其他人所做的一樣。 執行下列程式碼。 如需更多選項，請參閱[此文件](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. 在 Azure Data Factory 中，設定 Azure SQL Database 連結服務。

**範例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)。

Azure SQL Database 資料集支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的**類型**屬性必須設定為**AzureSqlTable**。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 資料表/檢視的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有結構描述的資料表/檢視名稱。 支援此屬性是基於回溯相容性。 對於新的工作負載，請使用 `schema` 和 `table`。 | 否 (來源)；是 (接收) |

### <a name="dataset-properties-example"></a>資料集屬性範例

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 本節提供 Azure SQL Database 來源和接收所支援屬性的清單。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database 作為來源

若要從 Azure SQL Database 複製資料，複製活動的 [**來源**] 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的**類型**屬性必須設定為**AzureSqlSource**。 "SqlSource" 類型仍然支援回溯相容性。 | 是 |
| sqlReaderQuery | 此屬性使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 | 否 |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 | 否 |
| storedProcedureParameters | 預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程式參數的名稱和大小寫。 | 否 |
| isolationLevel | 指定 SQL 來源的異動鎖定行為。 允許的值為： **ReadCommitted**、 **ReadUncommitted**、 **RepeatableRead**、 **Serializable**、 **Snapshot**。 如果未指定，則會使用資料庫的預設隔離等級。 如需詳細資訊，請參閱[這篇文件](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)。 | 否 |

**注意事項：**

- 如果為**AzureSqlSource**指定了**sqlReaderQuery** ，複製活動就會針對 Azure SQL Database 來源執行此查詢，以取得資料。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果您未指定**sqlReaderQuery**或**sqlReaderStoredProcedureName**，則會使用資料集 JSON 的 "structure" 區段中所定義的資料行來建立查詢。 查詢會 `select column1, column2 from mytable` 針對 Azure SQL Database 執行。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

#### <a name="sql-query-example"></a>SQL 查詢範例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>預存程序範例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>預存程序定義

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database 作為接收

> [!TIP]
> 深入瞭解支援的寫入行為、設定和最佳做法，從將[資料載入 Azure SQL Database 的最佳作法](#best-practice-for-loading-data-into-azure-sql-database)。

若要將資料複製到 Azure SQL Database，複製活動的 [**接收**] 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的**type**屬性必須設定為**AzureSqlSink**。 "SqlSink" 類型仍然支援回溯相容性。 | 是 |
| preCopyScript | 指定要在將資料寫入 Azure SQL Database 之前，要執行之複製活動的 SQL 查詢。 每一複製回合只會叫用此查詢一次。 使用此屬性來清除預先載入的資料。 | 否 |
| tableOption | 指定是否要根據來源架構，[自動建立接收資料表](copy-activity-overview.md#auto-create-sink-tables)（如果不存在）。 <br>當 sink 指定預存程式時，不支援自動建立資料表。 <br>允許的值包為：`none` (預設) 或 `autoCreate`。 | 否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會*依批次叫用*。 對於只執行一次且與來源資料無關的作業（例如，刪除或截斷），請使用 `preCopyScript` 屬性。<br>請參閱從[SQL 接收器叫用預存](#invoke-a-stored-procedure-from-a-sql-sink)程式的範例。 | 否 |
| storedProcedureTableTypeParameterName |預存程式中指定之資料表類型的參數名稱。  |否 |
| sqlWriterTableType |要在預存程式中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| writeBatchSize | 要插入 SQL 資料表中*每個批次*的資料列數目。<br/> 允許的值為**整數** (資料列數目)。 根據預設，Azure Data Factory 會依據資料列大小，以動態方式決定適當的批次大小。 | 否 |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/> 允許的值為**時間範圍**。 例如，"00:30:00" (30 分鐘的) 。 | 否 |
| disableMetricsCollection | Data Factory 會收集統計資料，例如複製效能優化和建議的 Azure SQL Database Dtu。 如果您擔心此行為，請指定 `true` 將其關閉。 | 否 (預設值為 `false`) |

**範例1：附加資料**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>將資料載入 Azure SQL Database 的最佳做法

當您將資料複製到 Azure SQL Database 時，可能需要不同的寫入行為：

- [附加](#append-data)：我的來源資料只有新的記錄。
- [Upsert](#upsert-data)：我的來源資料同時具有插入和更新。
- [覆寫](#overwrite-the-entire-table)：我想要每次重載整個維度資料表。
- [以自訂邏輯撰寫](#write-data-with-custom-logic)：我需要在最後插入目的地資料表之前進行額外的處理。

請參閱各自的章節，瞭解如何在 Azure Data Factory 和最佳作法中進行設定。

### <a name="append-data"></a>附加資料

附加資料是此 Azure SQL Database 接收連接器的預設行為。 Azure Data Factory 會執行大量插入，以有效率地寫入資料表。 您可以在複製活動中據以設定來源和接收器。

### <a name="upsert-data"></a>更新插入資料

**選項1：** 當您有大量資料要複製時，您可以使用複製活動，將所有記錄大量載入至臨時表，然後執行預存程式活動，在一次的過程中套用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 語句。 

複製活動目前不支援將資料載入資料庫臨時表。 使用多個活動的組合來設定它是一種最先進的方式，請參閱[優化 Azure SQL Database 大量 Upsert 案例](https://github.com/scoriani/azuresqlbulkupsert)。 以下顯示使用永久資料表做為預備環境的範例。

例如，在 Azure Data Factory 中，您可以建立具有與**預存程式活動**連結之**複製活動**的管線。 前者會將來源存放區的資料複製到 Azure SQL Database 臨時表中，例如**UpsertStagingTable**，做為資料集內的資料表名稱。 然後後者會叫用預存程式，將來源資料從臨時表合併至目標資料表，並清除臨時表。

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

**選項2：** 您可以選擇在[複製活動中叫用預存](#invoke-a-stored-procedure-from-a-sql-sink)程式。 這個方法會執行每個批次 (由 `writeBatchSize` 來源資料表中的屬性) 所控制，而不是在複製活動中使用 bulk insert 做為預設方法。

**選項3：** 您可以使用提供內建插入/upsert/更新方法的[對應資料流程](#sink-transformation)。

### <a name="overwrite-the-entire-table"></a>覆寫整個資料表

您可以在複製活動接收中設定**preCopyScript**屬性。 在此情況下，針對每個執行的複製活動，Azure Data Factory 先執行腳本。 然後，它會執行複製以插入資料。 例如，若要使用最新的資料來覆寫整個資料表，請在從來源大量載入新資料之前，指定要先刪除所有記錄的腳本。

### <a name="write-data-with-custom-logic"></a>使用自訂邏輯寫入資料

使用自訂邏輯撰寫資料的步驟類似于[Upsert 資料](#upsert-data)一節中所述。 當您需要在最後將來源資料插入目的地資料表之前套用額外的處理時，您可以載入至臨時表，然後叫用預存程式活動，或叫用複製活動接收中的預存程式來套用資料，或使用對應資料流程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

當您將資料複製到 Azure SQL Database 時，您也可以在來源資料表的每個批次上，使用其他參數來設定及叫用使用者指定的預存程式。 預存程式功能會利用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 例如，當您想要在最後將來源資料插入目的地資料表之前，套用額外的處理。 一些額外的處理範例是當您想要合併資料行、查閱其他值，並插入多個資料表時。

下列範例示範如何使用預存程序，對 Azure SQL Database 中的資料表執行 upsert。 假設輸入資料和接收**行銷**資料表各有三個數據行： **ProfileID**、 **State**和**Category**。 根據**ProfileID**資料行執行 upsert，並只將它套用至名為 "ProductA" 的特定類別。

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中轉換資料時，您可以從 Azure SQL Database 讀取和寫入資料表。 如需詳細資訊，請參閱對應資料流程中的[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

[來源] 轉換的 [**來源選項**] 索引標籤中提供 Azure SQL Database 特定的設定。

**Input：** 選取您是要將來源指向資料表 (相當於 ```Select * from <table-name>```) 還是要輸入自訂的 SQL 查詢。

**查詢**：如果您在 [輸入] 欄位中選取 [查詢]，請對於您的來源輸入 SQL 查詢。 此設定會覆寫您在資料集中選擇的任何資料表。 這裡不支援 **Order By** 子句，但您可以設定完整的 SELECT FROM 陳述式。 您也可使用使用者定義的資料表函數。 **select * from udfGetData()** 是 SQL 中傳回資料表的 UDF。 此查詢會產生您可以在資料流程中使用的來源資料表。 使用查詢也是縮減資料列以進行測試或查閱的絕佳方式。

- SQL 範例：```Select * from MyTable where customerId > 1000 and customerId < 2000```

**批次大小**：輸入批次大小，以將大量資料分成多次讀取。

**隔離等級**：對應資料流程中 SQL 來源的預設值為「未認可」。 您可以在這裡將隔離等級變更為下列其中一個值：

- 讀取認可
- 讀取未認可
- 可重複讀取
- 可序列化
- None (忽略隔離等級)

![隔離等級](media/data-flow/isolationlevel.png "隔離等級")

### <a name="sink-transformation"></a>接收轉換

[接收] 轉換的 [**設定**] 索引標籤中提供 Azure SQL Database 特定的設定。

**Update 方法：** 決定您的資料庫目的地所允許的作業。 預設僅允許插入。 若要更新、upsert 或刪除資料列，必須使用 alter-row 轉換來標記這些動作的資料列。 對於更新、更新插入和刪除，必須設定索引鍵資料行，以決定要改變哪一個資料列。

![索引鍵資料行](media/data-flow/keycolumn.png "索引鍵資料行")

ADF 會使用您在此處挑選為金鑰的資料行名稱，做為後續更新（upsert、刪除的一部分）。 因此，您必須挑選存在於接收對應中的資料行。 如果您不想要將值寫入此索引鍵資料行，請按一下 [略過寫入索引鍵資料行]。

**資料表動作：** 決定在寫入之前，是否要重新建立或移除目的地資料表中的所有資料列。

- 無：資料表不會執行任何動作。
- 重新建立：資料表會遭到捨棄並重新建立。 如果要動態建立新的資料表，則為必要。
- Truncate：會移除目標資料表中的所有資料列。

**批次大小**：控制要在每個值區中寫入的資料列數目。 較大的批次大小會改善壓縮和記憶體優化，但會導致在快取資料時發生記憶體例外狀況的風險。

**前置和後置 SQL 指令碼**：輸入將在寫入至您的接收資料庫之前 (前置處理) 和之後 (後置處理) 將執行的多行 SQL 指令碼

![前置和後置 SQL 處理指令碼](media/data-flow/prepost1.png "SQL 處理指令碼")

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database 的資料類型對應

當資料從或複製到 Azure SQL Database 時，會使用下列從 Azure SQL Database 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Azure SQL Database 資料類型 | Azure Data Factory 過渡期資料類型 |
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
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> 針對對應至 Decimal 過渡型別的資料類型，目前的複製活動最多支援28個精確度。 如果您有有效位數大於28的資料，請考慮在 SQL 查詢中轉換成字串。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要了解關於屬性的詳細資料，請參閱 [GetMetadata 活動](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>使用 Always Encrypted

當您使用[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)將資料從/複製到 Azure SQL Database 時，請使用[一般 ODBC 連接器](connector-odbc.md)，並透過自我裝載 Integration Runtime SQL Server ODBC 驅動程式。 此 Azure SQL Database 連接器目前不支援 Always Encrypted。 

具體而言：

1. 設定自我裝載的 Integration Runtime （如果您沒有的話）。 如需詳細資訊，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md)一文。

2. 從[這裡](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-current)下載適用于 SQL Server 的64位 ODBC 驅動程式，並將安裝在 Integration Runtime 機上。 若要深入瞭解此驅動程式的運作方式，請[使用 Always Encrypted 搭配 ODBC driver for SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current#using-the-azure-key-vault-provider)。

3. 建立具有 ODBC 類型的連結服務，以連接到您的 SQL 資料庫，請參閱下列範例：

    - 若要使用**SQL 驗證**：指定 ODBC 連接字串，如下所示，然後選取 [**基本**身份驗證] 以設定使用者名稱和密碼。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - 若要使用**Data Factory 受控識別驗證**： 

        1. 遵循相同的[必要條件](#managed-identity)來建立受控識別的資料庫使用者，並在您的資料庫中授與適當的角色。
        2. 在 [連結服務] 中，指定如下所示的 ODBC 連接字串，然後選取 [**匿名**驗證]，因為連接字串本身會指出 `Authentication=ActiveDirectoryMsi` 。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. 據以 ODBC 類型建立資料集和複製活動。 若要深入瞭解，請參閱[ODBC connector](connector-odbc.md)文章。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
