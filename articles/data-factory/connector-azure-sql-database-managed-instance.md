---
title: 將資料複製到 Azure SQL 資料庫託管實例中
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
ms.openlocfilehash: 11f4005e802e2a584b21903bfead2c6b9701f065
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238746"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料

本文概述如何使用 Azure Data Factory 中的複製活動，將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料。 它基於["複製活動概述](copy-activity-overview.md)"一文，其中概述了複製活動。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Azure SQL 資料庫託管實例連接器：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)

您可以將資料從 Azure SQL Database 受控執行個體複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到受控執行個體。 如需複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Azure SQL Database 受控執行個體連接器支援：

- 使用 SQL 身份驗證和 Azure 活動目錄 （Azure AD） 應用程式權杖身份驗證使用 Azure 資源的服務主體或託管標識複製資料。
- 作為源，使用 SQL 查詢或預存程序檢索資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

>[!NOTE]
>Azure SQL 資料庫託管實例["始終加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)"現在不受此連接器的支援。 為了解決這些問題，您可以通過自託管的集成運行時使用[通用 ODBC 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式。 有關 ODBC 驅動程式下載和連接字串配置，請按照[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current)操作。

## <a name="prerequisites"></a>Prerequisites

要訪問 Azure SQL 資料庫託管實例[公共終結點](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)，可以使用 Azure 資料工廠管理的 Azure 集成運行時。 請確保啟用公共終結點，並且允許在網路安全性群組中使用公共終結點流量，以便 Azure 資料工廠可以連接到資料庫。 有關詳細資訊，請參閱[本指南](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)。

要訪問 Azure SQL 資料庫託管實例專用終結點，請設置可以訪問資料庫[的自託管集成運行時](create-self-hosted-integration-runtime.md)。 如果將自託管集成運行時預配到與託管實例相同的虛擬網路中，請確保集成運行時電腦與託管實例位於不同的子網中。 如果在與託管實例不同的虛擬網路中預配自託管集成運行時，則可以使用虛擬網路對等互連或虛擬網路連接到虛擬網路連接。 如需詳細資訊，請參閱[將您的應用程式連線到 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 Azure SQL 資料庫託管實例連接器的 Azure 資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure SQL Database 受控執行個體連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設置為**AzureSqlMI**。 | 是 |
| connectionString |此屬性指定使用 SQL 身份驗證連接到託管實例所需的**連接String**資訊。 如需詳細資訊，請參閱下列範例。 <br/>預設的連接埠為 1433。 如果將 Azure SQL 資料庫託管實例與公共終結點一起使用，請顯式指定埠 3342。<br> 您還可以在 Azure 金鑰保存庫中輸入密碼。 如果是 SQL 身份驗證，則從連接`password`字串中拔出配置。 有關詳細資訊，請參閱[Azure 金鑰保存庫中表和存儲憑據](store-credentials-in-key-vault.md)之後的 JSON 示例。 |是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是，當您使用具有服務主體的 Azure AD 身份驗證時 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為**SecureString，** 以將其安全地存儲在 Azure 資料工廠中，或[引用存儲在 Azure 金鑰保存庫中的機密](store-credentials-in-key-vault.md)。 | 是，當您使用具有服務主體的 Azure AD 身份驗證時 |
| tenant | 指定應用程式所在的租戶資訊，如功能變數名稱或租戶 ID。 通過將滑鼠懸停在 Azure 門戶的右上角來檢索它。 | 是，當您使用具有服務主體的 Azure AD 身份驗證時 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果託管實例具有公共終結點並允許 Azure 資料工廠訪問它，則可以使用自託管的集成運行時或 Azure 集成運行時。 如果未指定，則使用預設的 Azure 集成運行時。 |是 |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 身份驗證](#sql-authentication)
- [Azure AD 應用程式權杖身份驗證：服務主體](#service-principal-authentication)
- [Azure AD 應用程式權杖驗證：Azure 資源的受控識別](#managed-identity)

### <a name="sql-authentication"></a>SQL 驗證

**示例 1：使用 SQL 身份驗證**

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

**示例 2：在 Azure 金鑰保存庫中使用 SQL 身份驗證與密碼**

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

1. 按照以下步驟[為託管實例預配 Azure 活動目錄管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。

2. 從 Azure 入口網站[建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

3. 為 Azure 資料工廠託管標識[創建登錄名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。 在 SQL Server Management Studio (SSMS) 中，以**系統管理員**身分的 SQL Server 帳戶連線到您的受控執行個體。 在 **master** 資料庫中執行下列 T-SQL：

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. 為 Azure 資料工廠託管標識[創建包含的資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 連線到您想要從中複製資料的資料庫，然後執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. 授予資料工廠託管標識所需的許可權，就像您通常對 SQL 使用者和其他人所做的一樣。 執行下列程式碼。 如需更多選項，請參閱[此文件](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. 在 Azure 資料工廠中配置 Azure SQL 資料庫託管實例連結服務。

**示例：使用服務主體身份驗證**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 資源身份驗證的託管標識

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 可以使用此託管標識進行 Azure SQL 資料庫託管實例身份驗證。 指定的處理站可以使用此身分識別從您資料庫存取資料，或從您的資料庫複製資料。

要使用託管標識身份驗證，請按照以下步驟操作。

1. 按照以下步驟[為託管實例預配 Azure 活動目錄管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。

2. 為 Azure 資料工廠託管標識[創建登錄名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。 在 SQL Server Management Studio (SSMS) 中，以**系統管理員**身分的 SQL Server 帳戶連線到您的受控執行個體。 在 **master** 資料庫中執行下列 T-SQL：

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. 為 Azure 資料工廠託管標識[創建包含的資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 連線到您想要從中複製資料的資料庫，然後執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. 授予資料工廠託管標識所需的許可權，就像您通常對 SQL 使用者和其他人所做的一樣。 執行下列程式碼。 如需更多選項，請參閱[此文件](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. 在 Azure 資料工廠中配置 Azure SQL 資料庫託管實例連結服務。

**示例：使用託管標識身份驗證**

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

要將資料複製到 Azure SQL 資料庫託管實例中，支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設置為**AzureSqlMITable**。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 表/視圖的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有架構的表/視圖的名稱。 此屬性支援向後相容性。 對於新的工作負載，請使用`schema``table`和 。 | 否 (來源)；是 (接收) |

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

要從 Azure SQL 資料庫託管實例複製資料，複製活動源部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須將複製活動源的類型屬性設置為**SqlMISource**。 | 是 |
| sqlReaderQuery |此屬性使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此屬性是從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |這些是預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |
| isolationLevel | 指定 SQL 源的事務鎖定行為。 允許的值是：**已讀提交**（預設）、**未提交**、**可重複讀取**、**可序列化**、**快照**。 有關詳細資訊，請參閱[此文檔](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)。 | 否 |

**請注意下列幾點：**

- 如果**sqlReaderQuery**為**SqlMISource**指定，則複製活動將針對託管實例源運行此查詢以獲取資料。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果您未指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName** 屬性，資料集 JSON "structure" 區段中定義的資料行就會用來建構查詢。 `select column1, column2 from mytable` 查詢會根據受控執行個體來執行。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**示例：使用 SQL 查詢**

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

**示例：使用預存程序**

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
> 詳細瞭解將[資料載入到 Azure SQL 資料庫託管實例的最佳實踐](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)中支援的寫入行為、配置和最佳實踐。

要將資料複製到 Azure SQL 資料庫託管實例，複製活動接收器部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設置為**SqlMISink**。 | 是 |
| writeBatchSize |*要插入到 SQL 表中的行數。*<br/>允許的值為整數的資料列數目。 預設情況下，Azure 資料工廠會根據行大小動態確定適當的批次處理大小。  |否 |
| writeBatchTimeout |此屬性會指定在逾時前等待批次插入作業完成的時間。<br/>允許的值用於時間跨度。 範例是 “00:30:00”，也就是 30 分鐘。 |否 |
| preCopyScript |此屬性指定一個 SQL 查詢，用於在將資料寫入託管實例之前運行的副本活動。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會*依批次叫用*。 對於僅運行一次且與來源資料無關的操作（例如，刪除或截圖）請使用 屬性`preCopyScript`。 | 否 |
| 預存程序表類型參數名稱 |預存程序中指定的表類型的參數名稱。  |否 |
| sqlWriterTableType |要在預存程序中使用的表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| 表選項 | 指定是否根據源架構自動創建接收器表（如果不存在）。 當接收器指定預存程序或在複製活動中配置暫存副本時，不支援自動表創建。 允許的值是：（`none`預設），。 `autoCreate` |否 |

**示例 1：追加資料**

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

**示例 2：在複製期間調用預存程序**

從 SQL [MI 接收器調用預存程序](#invoke-a-stored-procedure-from-a-sql-sink)中瞭解有關的詳細資訊。

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>將資料載入到 Azure SQL 資料庫託管實例的最佳做法

將資料複製到 Azure SQL 資料庫託管實例時，可能需要不同的寫入行為：

- [追加：](#append-data)我的來源資料只有新記錄。
- [Upsert](#upsert-data)：我的來源資料同時包含插入和更新。
- [覆蓋](#overwrite-the-entire-table)：我想每次重新載入整個維度表。
- [用自訂邏輯編寫](#write-data-with-custom-logic)：在最終插入目標表之前，我需要額外的處理。 

有關如何在 Azure 資料工廠中配置的各部分和最佳實踐，請參閱相應的部分。

### <a name="append-data"></a>附加資料

追加資料是此 Azure SQL 資料庫託管實例接收器介面的預設行為。 Azure 資料工廠執行批量插入以高效地寫入表。 您可以在複製活動中相應地配置源和接收器。

### <a name="upsert-data"></a>更新插入資料

**選項 1：** 當您有大量要複製的資料時，請使用以下方法執行 upsert： 

- 首先，使用[臨時表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)使用複製活動批量載入所有記錄。 由於未記錄針對臨時表的操作，因此可以在幾秒鐘內載入數百萬條記錄。
- 在 Azure 資料工廠中運行預存程序活動以應用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 語句。 使用臨時表作為源執行所有更新或作為單個事務插入。 這樣，往返次數和日誌操作就會減少。 在預存程序活動結束時，可以截斷臨時表以準備下一個上升週期。

例如，在 Azure 資料工廠中，您可以創建具有"**複製"活動**與**預存程序活動**連結的管道。 前者將源存儲中的資料複製到臨時表中，例如 **，#UpsertTempTable**，作為資料集中的表名。 然後，後者調用預存程序將臨時表的來源資料合併到目標表中並清理臨時表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在資料庫中，使用 MERGE 邏輯定義預存程序，如以下示例，該示例從以前的預存程序活動中指向。 假設目標是具有三列**的行銷**表：**設定檔 ID、****狀態**和**類別**。 根據 **"設定檔 ID"** 列執行向上操作。

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

**選項 2：** 您還可以選擇[在複製活動中調用預存程序](#invoke-a-stored-procedure-from-a-sql-sink)。 此方法運行源表中的每一行，而不是使用批量插入作為複製活動中的預設方法，這不適用於大規模 upsert。

### <a name="overwrite-the-entire-table"></a>覆蓋整個表

您可以在複製活動接收器中配置**預複製腳本**屬性。 在這種情況下，對於運行的每個複製活動，Azure 資料工廠首先運行腳本。 然後運行複製以插入資料。 例如，要用最新資料覆蓋整個表，請指定一個腳本，首先刪除所有記錄，然後再從源批量載入新資料。

### <a name="write-data-with-custom-logic"></a>使用自訂邏輯寫入資料

使用自訂邏輯寫入資料的步驟與[Upsert 資料](#upsert-data)部分中描述的步驟類似。 當您需要在最終將來源資料插入目標表之前應用額外處理時，對於大規模，可以執行以下兩項操作之一： 

- 載入到臨時表，然後調用預存程序。
- 在複製期間調用預存程序。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

將資料複製到 Azure SQL 資料庫託管實例時，還可以配置和調用使用者指定的預存程序，並包含其他參數。 預存程序功能利用了[表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> 調用預存程序逐行處理資料，而不是使用批量操作，我們不建議進行大規模複製。 從將資料[載入到 Azure SQL 資料庫託管實例的最佳做法中](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)瞭解詳細資訊。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 例如，您希望在最終將來源資料插入目標表之前應用額外處理。 當要合併列、查找其他值並將資料插入到多個表中時，將介紹一些額外的處理示例。

下列範例示範如何使用預存程序，對 SQL Server 資料庫中的資料表執行更新插入。 假設輸入資料和接收器**市場行銷**表各有三列：**設定檔 ID、****狀態**和**類別**。 根據**ProfileID**列執行 upsert，並且僅將其應用於名為"ProductA"的特定類別。

1. 在資料庫中，定義與**sqlWriterTableType**同名的表類型。 資料表類型的結構描述會與輸入資料所傳回的結構描述相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在資料庫中，定義與**sqlWriter預存程序名稱**相同的預存程序。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 預存程序中表類型的參數名稱與在資料集中定義的**表名稱**相同。

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

3. 在 Azure 資料工廠中，在複製活動中定義**SQL MI 接收器**部分，如下所示：

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

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>獲取中繼資料活動屬性

要瞭解有關屬性的詳細資訊，請選中[獲取中繼資料活動](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>後續步驟
有關 Azure 資料工廠中複製活動支援為源和接收器的資料存儲清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)。
