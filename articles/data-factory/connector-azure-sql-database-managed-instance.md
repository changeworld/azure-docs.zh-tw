---
title: 將資料複製到 Azure SQL Database 受控執行個體
description: 了解如何使用 Azure Data Factory 從 Azure SQL Database 受控執行個體來回移動資料。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: cfa53d480120ec75623a6a372b258b63e6264f92
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136038"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料

本文概述如何使用 Azure Data Factory 中的複製活動，將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料。 本文是以[複製活動概觀](copy-activity-overview.md)一文作為基礎，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure SQL Database 受控執行個體連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

您可以將資料從 Azure SQL Database 受控執行個體複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到受控執行個體。 如需複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Azure SQL Database 受控執行個體連接器支援：

- 使用 SQL 驗證和 Azure Active Directory （Azure AD）應用程式權杖驗證搭配服務主體或 Azure 資源的受控識別來複製資料。
- 作為來源，使用 SQL 查詢或預存程式來抓取資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

>[!NOTE]
>此連接器目前不支援 Azure SQL Database 受控執行個體[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) 。 若要解決此情況，您可以透過自我裝載整合執行時間使用[一般 odbc 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式。 請遵循[此指導](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current)方針與 ODBC 驅動程式下載及連接字串設定。

## <a name="prerequisites"></a>Prerequisites

若要存取 Azure SQL Database 受控執行個體[公用端點](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)，您可以使用 Azure Data Factory 受控 Azure 整合執行時間。 請確定您已啟用公用端點，而且也允許網路安全性群組上的公用端點流量，讓 Azure Data Factory 可以連接到您的資料庫。 如需詳細資訊，請參閱[本指引](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)。

若要存取 Azure SQL Database 受控執行個體私用端點，請設定可存取資料庫的[自我裝載整合運行](create-self-hosted-integration-runtime.md)時間。 如果您在與受控實例相同的虛擬網路中布建自我裝載整合執行時間，請確定您的整合執行時間機器與您的受控實例位於不同的子網。 如果您在與受控實例不同的虛擬網路中布建自我裝載整合執行時間，您可以使用虛擬網路對等互連或虛擬網路對虛擬網路連線。 如需詳細資訊，請參閱[將您的應用程式連線到 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 受控執行個體連接器特定的 Azure Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure SQL Database 受控執行個體連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為**AzureSqlMI**。 | 是 |
| connectionString |這個屬性會指定使用 SQL 驗證連接到受控實例所需的**connectionString**資訊。 如需詳細資訊，請參閱下列範例。 <br/>預設通訊埠為1433。 如果您使用具有公用端點的 Azure SQL Database 受控執行個體，請明確指定埠3342。<br> 您也可以將密碼放在 Azure Key Vault 中。 如果是 SQL 驗證，請從連接字串中提取 `password` 設定。 如需詳細資訊，請參閱資料表後面的 JSON 範例，並[將認證儲存在 Azure Key Vault 中](store-credentials-in-key-vault.md)。 |是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是，當您搭配服務主體使用 Azure AD 驗證時 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為**SecureString** ，將它安全地儲存在 Azure Data Factory 中，或[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | 是，當您搭配服務主體使用 Azure AD 驗證時 |
| tenant | 指定租使用者資訊，例如您的應用程式所在的功能變數名稱或租使用者識別碼。 將滑鼠游標暫留在 Azure 入口網站的右上角來取出。 | 是，當您搭配服務主體使用 Azure AD 驗證時 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的受控實例具有公用端點，並允許 Azure Data Factory 存取它，您可以使用自我裝載整合執行時間或 Azure 整合執行時間。 如果未指定，則會使用預設的 Azure integration runtime。 |是 |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 驗證](#sql-authentication)
- [Azure AD 應用程式權杖驗證：服務主體](#service-principal-authentication)
- [Azure AD 應用程式權杖驗證：Azure 資源的受控識別](#managed-identity)

### <a name="sql-authentication"></a>SQL 驗證

**範例1：使用 SQL 驗證**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. 請遵循下列步驟，為您的受控執行個體布建[Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。

2. 從 Azure 入口網站[建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

3. [建立](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)Azure Data Factory 受控識別的登入。 在 SQL Server Management Studio (SSMS) 中，以**系統管理員**身分的 SQL Server 帳戶連線到您的受控執行個體。 在 **master** 資料庫中執行下列 T-SQL：

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. 為 Azure Data Factory 受控識別[建立自主資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 連線到您想要從中複製資料的資料庫，然後執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. 授與 Data Factory 受控識別所需的許可權，就像您一般對 SQL 使用者和其他人所做的一樣。 執行下列程式碼。 如需更多選項，請參閱[此文件](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. 在 Azure Data Factory 中設定 Azure SQL Database 受控執行個體連結服務。

**範例：使用服務主體驗證**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

### <a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以使用此受控識別來進行 Azure SQL Database 受控執行個體驗證。 指定的處理站可以使用此身分識別從您資料庫存取資料，或從您的資料庫複製資料。

若要使用受控識別驗證，請遵循下列步驟。

1. 請遵循下列步驟，為您的受控執行個體布建[Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。

2. [建立](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)Azure Data Factory 受控識別的登入。 在 SQL Server Management Studio (SSMS) 中，以**系統管理員**身分的 SQL Server 帳戶連線到您的受控執行個體。 在 **master** 資料庫中執行下列 T-SQL：

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. 為 Azure Data Factory 受控識別[建立自主資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 連線到您想要從中複製資料的資料庫，然後執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. 授與 Data Factory 受控識別所需的許可權，就像您一般對 SQL 使用者和其他人所做的一樣。 執行下列程式碼。 如需更多選項，請參閱[此文件](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. 在 Azure Data Factory 中設定 Azure SQL Database 受控執行個體連結服務。

**範例：使用受控識別驗證**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 Azure SQL Database 受控執行個體資料集所支援的屬性清單。

若要將資料複製到 Azure SQL Database 受控執行個體，並將其複製到其中，則支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為**AzureSqlMITable**。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 資料表/視圖的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有架構的資料表/視圖名稱。 此屬性支援回溯相容性。 針對新的工作負載，請使用 `schema` 和 `table`。 | 否 (來源)；是 (接收) |

**範例**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure SQL Database 受控執行個體來源和接收所支援的屬性清單。

### <a name="azure-sql-database-managed-instance-as-a-source"></a>作為來源的 Azure SQL Database 受控執行個體

若要從 Azure SQL Database 受控執行個體複製資料，複製活動的 [來源] 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為**SqlMISource**。 | 是 |
| sqlReaderQuery |此屬性使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此屬性是從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |這些是預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |
| isolationLevel | 指定 SQL 來源的交易鎖定行為。 允許的值為： **ReadCommitted** （預設值）、 **ReadUncommitted**、 **RepeatableRead**、 **Serializable**、 **Snapshot**。 如需詳細資訊，請參閱[此](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)檔。 | 否 |

**請注意下列幾點**：

- 如果為**SqlMISource**指定了**sqlReaderQuery** ，複製活動就會對受控實例來源執行此查詢，以取得資料。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果您未指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName** 屬性，資料集 JSON "structure" 區段中定義的資料行就會用來建構查詢。 `select column1, column2 from mytable` 查詢會根據受控執行個體來執行。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例：使用 SQL 查詢**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>作為接收的 Azure SQL Database 受控執行個體

> [!TIP]
> 深入瞭解支援的寫入行為、設定和最佳做法，從將[資料載入 Azure SQL Database 受控執行個體的最佳作法](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

若要將資料複製到 Azure SQL Database 受控執行個體，複製活動的 [接收] 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的 type 屬性必須設定為**SqlMISink**。 | 是 |
| writeBatchSize |要插入 SQL 資料表中*每個批次*的資料列數目。<br/>允許的值為整數的資料列數目。 根據預設，Azure Data Factory 會依據資料列大小，以動態方式決定適當的批次大小。  |否 |
| writeBatchTimeout |此屬性會指定在逾時前等待批次插入作業完成的時間。<br/>允許的值適用于 timespan。 範例是 “00:30:00”，也就是 30 分鐘。 |否 |
| preCopyScript |這個屬性會指定 SQL 查詢，讓複製活動在將資料寫入受控實例之前執行。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會*依批次叫用*。 對於只執行一次且與來源資料無關的作業（例如，刪除或截斷），請使用 `preCopyScript` 屬性。 | 否 |
| storedProcedureTableTypeParameterName |預存程式中指定之資料表類型的參數名稱。  |否 |
| sqlWriterTableType |要在預存程式中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| tableOption | 指定是否要根據來源架構，自動建立接收資料表（如果不存在）。 當 sink 指定了複製活動中所設定的預存程式或分段複製時，不支援自動建立資料表。 允許的值為： `none` （預設），`autoCreate`。 |否 |

**範例1：附加資料**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**範例2：在複製期間叫用預存程式**

深入瞭解從[SQL MI 接收叫用預存](#invoke-a-stored-procedure-from-a-sql-sink)程式的詳細資料。

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>將資料載入 Azure SQL Database 受控執行個體的最佳做法

當您將資料複製到 Azure SQL Database 受控執行個體時，可能需要不同的寫入行為：

- [附加](#append-data)：我的來源資料只有新的記錄。
- [Upsert](#upsert-data)：我的來源資料同時具有插入和更新。
- [覆寫](#overwrite-the-entire-table)：我想要每次重載整個維度資料表。
- [以自訂邏輯撰寫](#write-data-with-custom-logic)：我需要在最後插入目的地資料表之前進行額外的處理。 

請參閱各自的章節，以瞭解如何在 Azure Data Factory 和最佳作法中進行設定。

### <a name="append-data"></a>附加資料

附加資料是此 Azure SQL Database 受控執行個體接收連接器的預設行為。 Azure Data Factory 會執行大量插入，以有效率地寫入資料表。 您可以在複製活動中據以設定來源和接收器。

### <a name="upsert-data"></a>更新插入資料

**選項1：** 當您有大量資料要複製時，請使用下列方法來執行 upsert： 

- 首先，使用[臨時表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)，使用複製活動來大量載入所有記錄。 由於不會記錄對臨時表的作業，因此您可以在數秒內載入數百萬筆記錄。
- 在 Azure Data Factory 中執行預存程式活動，以套用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 語句。 使用臨時表做為來源，將所有更新或插入做為單一交易來執行。 如此一來，來回行程和記錄作業的次數就會減少。 在預存程式活動結束時，可以將臨時表截斷，以準備好進行下一個 upsert 週期。

例如，在 Azure Data Factory 中，您可以建立具有與**預存程式活動**連結之**複製活動**的管線。 前者會將來源存放區的資料複製到臨時表中，例如， **# #UpsertTempTable**，做為資料集內的資料表名稱。 然後後者會叫用預存程式，將臨時表中的來源資料合併到目標資料表，並清除臨時表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在您的資料庫中，定義具有合併邏輯的預存程式，如下列範例所示，這是從先前的預存程式活動所指向。 假設目標是包含三個數據行的**行銷**資料表： **ProfileID**、 **State**和**Category**。 根據**ProfileID**資料行執行 upsert。

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

**選項2：** 您也可以選擇在[複製活動中叫用預存](#invoke-a-stored-procedure-from-a-sql-sink)程式。 這個方法會執行來源資料表中的每個資料列，而不是在複製活動中使用 bulk insert 做為預設方法，這不適用於大規模的 upsert。

### <a name="overwrite-the-entire-table"></a>覆寫整個資料表

您可以在複製活動接收中設定**preCopyScript**屬性。 在此情況下，針對每個執行的複製活動，Azure Data Factory 先執行腳本。 然後，它會執行複製以插入資料。 例如，若要使用最新的資料來覆寫整個資料表，請在從來源大量載入新資料之前，指定要先刪除所有記錄的腳本。

### <a name="write-data-with-custom-logic"></a>使用自訂邏輯寫入資料

使用自訂邏輯撰寫資料的步驟類似于[Upsert 資料](#upsert-data)一節中所述。 當您需要在最後將來源資料插入目的地資料表之前，套用額外的處理，您可以執行下列兩個動作的其中一項： 

- 載入至臨時表，然後叫用預存程式。
- 在複製期間叫用預存程式。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

當您將資料複製到 Azure SQL Database 受控執行個體時，您也可以使用其他參數來設定及叫用使用者指定的預存程式。 預存程序功能使用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> 叫用預存程式會逐列處理資料列，而不是使用大量作業（我們不建議用於大規模複製）。 深入瞭解將[資料載入 Azure SQL Database 受控執行個體中的最佳作法](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 例如，當您想要在最後將來源資料插入目的地資料表之前，套用額外的處理。 一些額外的處理範例包括：當您想要合併資料行、查閱其他值，以及將資料插入一個以上的資料表時。

下列範例示範如何使用預存程序，對 SQL Server 資料庫中的資料表執行更新插入。 假設輸入資料和接收**行銷**資料表各有三個數據行： **ProfileID**、 **State**和**Category**。 根據**ProfileID**資料行執行 upsert，並只將它套用至名為 "ProductA" 的特定類別。

1. 在您的資料庫中，使用與**sqlWriterTableType**相同的名稱來定義資料表類型。 資料表類型的結構描述會與輸入資料所傳回的結構描述相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
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

3. 在 Azure Data Factory 中，定義複製活動中的 [ **SQL MI 接收**] 區段，如下所示：

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database 受控執行個體的資料類型對應

從 Azure SQL Database 受控執行個體複製資料或將資料複製到該處時，會使用下列從 Azure SQL Database 受控執行個體資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Azure SQL Database 受控執行個體資料類型 | Azure Data Factory 過渡期資料類型 |
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

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解有關屬性的詳細資料，請檢查[GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
