---
title: 在 Azure 突觸分析中複製並轉換資料
description: 瞭解如何將資料複製到 Azure 同步分析中,以及使用數據工廠在 Azure 突觸分析中轉換數據。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 7fb1560fb9be809d816dde7dd69f1ec8afe5649f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417569"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>使用 Azure 資料工廠複製並轉換 Azure 突觸分析(以前的 Azure SQL 資料倉儲)中的數據 

> [!div class="op_single_selector" title1="選擇您正在使用的資料工廠服務版本:"]
> * [版本1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [目前版本](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 資料工廠中的複製活動從 Azure 同步分析複製數據,以及使用資料流轉換 Azure 資料湖儲存 Gen2 中的數據。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 資料工廠中的複製活動將數據從 Azure SQL 資料套件複製到 Azure SQL 資料倉庫,並使用資料流轉換 Azure 資料湖儲存 Gen2 中的數據。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

本文概述了如何使用 Azure 資料工廠中的複製活動從 Azure 同步分析複製數據,以及使用資料流轉換 Azure 資料湖儲存 Gen2 中的數據。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Azure 同步分析連接器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)表[複製活動](copy-activity-overview.md)
- [對應資料串流](concepts-data-flow-overview.md)
- [尋找活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)

對於複製活動,此 Azure 突觸分析連接器支援以下功能:

- 使用 SQL 驗證和 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配服務主體或 Azure 資源的受控識別來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 作為接收器,使用[PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse)或[COPY 語句](#use-copy-statement)(預覽)或批量插入載入資料。 我們建議使用 PolyBase 或 COPY 語句(預覽),以更好的複製性能。

> [!IMPORTANT]
> 如果您使用Azure Data Factory Integration Runtime 複製資料，請設定 [Azure SQL 伺服器防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)，讓 Azure 服務可存取伺服器。
> 如果您使用自我裝載整合執行階段來複製資料，請將 Azure SQL 伺服器防火牆設定為允許適當的 IP 範圍。 此範圍包括用於連接到 Azure 同步分析的電腦 IP。

## <a name="get-started"></a>開始使用

> [!TIP]
> 要獲得最佳性能,請使用 PolyBase 將數據載入到 Azure 同步分析中。 [使用 PolyBase 將數據載入到 Azure 同步分析](#use-polybase-to-load-data-into-azure-sql-data-warehouse)部分具有詳細資訊。 有關用例的演練,請參閱在 Azure[資料工廠使用 15 分鐘內將 1 TB 載入到 Azure 突觸分析中](load-azure-sql-data-warehouse.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關定義特定於 Azure 同步分析連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

Azure 同步分析連結服務支援以下屬性:

| 屬性            | 描述                                                  | 必要                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | 類型屬性必須設為 **AzureSqlDW**。             | 是                                                          |
| connectionString    | 指定連接到**連接String**屬性的 Azure 同步分析實例所需的資訊。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼/服務主體金鑰放在 Azure Key Vault 中，而且，如果這是 SQL 驗證，則會從連接字串中提取 `password` 組態。 請參閱表格下方的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文深入了解詳細資料。 | 是                                                          |
| servicePrincipalId  | 指定應用程式的用戶端識別碼。                         | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| tenant              | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| connectVia          | 連線到資料儲存的[整合時](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否                                                           |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 認證](#sql-authentication)
- Azure AD 應用程式權杖驗證：[服務主體](#service-principal-authentication)
- Azure AD 應用程式權杖身份驗證[:Azure 資源的託管識別](#managed-identity)

>[!TIP]
>如果您遇到錯誤，其錯誤碼為 "UserErrorFailedToConnectToSqlServer"，以及「資料庫的工作階段限制為 XXX 並已達到。」訊息，請將 `Pooling=false` 新增至您的連接字串並再試一次。

### <a name="sql-authentication"></a>SQL 驗證

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 驗證的連結服務範例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault 中的密碼：**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

1. 從 Azure 入口網站**[建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 在 Azure 入口網站為您的 Azure SQL Server **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**，如果您尚未這麼做。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您授與受控識別系統管理員角色，請略過步驟 3 和 4。 系統管理員將擁有資料庫的完整存取權。

3. 為服務主體**[建立自主資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**。 以至少具有 ALTER ANY USER 權限的 AAD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料倉儲。 執行下列 T-SQL：
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 如同您一般對 SQL 使用者或其他人所做的一樣，**將所需的權限授與服務主體**。 運行以下代碼,或[在此處](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)引用更多選項。 如果要使用 PolyBase 載入資料,請使用學習[需要的資料庫權限](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. 在 Azure 資料工廠**中設定 Azure 同步分析連結服務**。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服務主體驗證的連結服務範例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 資源識別認證的託管識別

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的處理站。 可以使用此託管標識進行 Azure 同步分析身份驗證。 指定的處理站可以使用此身分識別來存取資料倉儲和從中來回複製資料。

要使用託管標識身份驗證,請按照以下步驟操作:

1. 在 Azure 入口網站為您的 Azure SQL Server **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**，如果您尚未這麼做。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您授與受控識別系統管理員角色，請略過步驟 3 和 4。 系統管理員將擁有資料庫的完整存取權。

2. 為資料工廠管理辨識**[建立包含的資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**。 以至少具有 ALTER ANY USER 權限的 AAD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料倉儲。 執行下列 T-SQL。 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **授予數據工廠託管標識所需的許可權**,就像您通常對 SQL 使用者和其他人所做的一樣。 運行以下代碼,或[在此處](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)引用更多選項。 如果要使用 PolyBase 載入資料,請使用學習[需要的資料庫權限](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. 在 Azure 資料工廠**中設定 Azure 同步分析連結服務**。

**範例:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 

Azure 突觸分析資料集支援以下屬性:

| 屬性  | 描述                                                  | 必要                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 資料集**的類型**屬性必須設定為**AzureSqlDWTable**。 | 是                         |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 表/視圖的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有架構的表/視圖的名稱。 此屬性支援向後相容性。 對新工作負載,請使用`schema``table`與 。 | 否 (來源)；是 (接收) |

#### <a name="dataset-properties-example"></a>資料集屬性範例

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure 突觸分析源和接收器支援的屬性清單。

### <a name="azure-synapse-analytics-as-the-source"></a>Azure 突觸分析作為來源

要從 Azure 同步分析複製資料,請將「複製活動」 的**類型**屬性設定為**SqlDWSource**。 複製活動**來源**部份支援以下屬性:

| 屬性                     | 描述                                                  | 必要 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 複製活動來源**的類型**屬性必須設定為**SqlDWSource**。 | 是      |
| sqlReaderQuery               | 使用自訂 SQL 查詢來讀取資料。 範例： `select * from MyTable`. | 否       |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 | 否       |
| storedProcedureParameters    | 預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否       |
| isolationLevel | 指定 SQL 源的事務鎖定行為。 允許的值是:**已讀回提交**( 預設) ,**沒有提交**,**可重複讀取**,**可序列化**,**快照**。 有關詳細資訊,請參閱[此文件](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)。 | 否 |

**範例：使用 SQL 查詢**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**範例：使用預存程序**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**範例儲存程序:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure 突觸分析作為接收器

Azure 資料工廠支援將數據載入到 SQL 資料倉庫的三種方法。

![SQL DW 接收器複製選項](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [使用多邊形基礎](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [使用 COPY 語句(預覽)](#use-copy-statement)
- 使用批次插入

載入資料的最快、最可擴充的方式是透過[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)或[COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)(預覽)。

若要將資料複製到「Azure SQL 資料倉儲」，請將複製活動中的接收類型設定為 **SqlDWSink**。 複製活動**接收器**部份支援以下屬性:

| 屬性          | 描述                                                  | 必要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 複製活動接收器**的類型**屬性必須設定為**SqlDWSink**。 | 是                                           |
| allowPolyBase     | 指示是否使用 PolyBase 將數據載入到 SQL 資料倉庫中。 `allowCopyCommand`不能`allowPolyBase`兩者都是真的。 <br/><br/>請參閱 [使用 PolyBase 將資料載入 Azure SQL 資料倉儲](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 一節中的條件約束和詳細資料。<br/><br/>允許的值為 **True** 和 **False** (預設值)。 | 否。<br/>使用 PolyBase 時應用。     |
| polyBaseSettings  | 一組屬性,在`allowPolybase`屬性設置為**true**時可以指定。 | 否。<br/>使用 PolyBase 時應用。 |
| 允許複製命令 | 指示是否使用[COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)(預覽)將數據載入到 SQL 資料倉儲中。 `allowCopyCommand`不能`allowPolyBase`兩者都是真的。 <br/><br/>有關約束和詳細資訊[,請參閱使用 COPY 語句將數據載入到 Azure SQL 資料倉庫](#use-copy-statement)部分。<br/><br/>允許的值為 **True** 和 **False** (預設值)。 | 否。<br>使用 COPY 時應用。 |
| 複製命令設定 | 一組屬性,在屬性設置為`allowCopyCommand`TRUE 時可以指定。 | 否。<br/>使用 COPY 時應用。 |
| writeBatchSize    | **要插入到每個批次處理**SQL 表中的行數。<br/><br/>允許的值為**整數** (資料列數目)。 默認情況下,數據工廠會根據行大小動態確定適當的批處理大小。 | 否。<br/>使用散裝插入時應用。     |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/><br/>允許的值是**時間跨度**。 範例："00:30:00" (30 分鐘)。 | 否。<br/>使用散裝插入時應用。        |
| preCopyScript     | 指定一個供「複製活動」在每次執行時將資料寫入到「Azure SQL 資料倉儲」前執行的 SQL 查詢。 使用此屬性來清除預先載入的資料。 | 否                                            |
| 表選項 | 指定是否根據源架構自動建立接收器表(如果不存在)。 在複製活動中配置暫存副本時,不支援自動表創建。 允許的值是:(`none`預設),。 `autoCreate` |否 |
| 禁用"指標收集" | 數據工廠收集 SQL 資料倉庫 DWU 等指標,以便進行複製性能優化和建議。 如果您關注此行為,請指定`true`將其關閉。 | 否 (預設值為 `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL 資料倉儲接收範例

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>使用 PolyBase 將資料載入 Azure SQL 資料倉儲

使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)是將大量數據載入到高輸送量的 Azure 同步分析中的有效方法。 使用 PolyBase 而不是預設的 BULKINSERT 機制，將可看到輸送量大幅提升。 有關用例的演練,請參閱將 1 [TB 載入到 Azure 同步分析](v1/data-factory-load-sql-data-warehouse.md)中。

* 如果源資料位於**Azure Blob、Azure 資料湖儲存第 1 代或 Azure 資料湖儲存第 2 代**中,並且**格式與 PolyBase 相容**,則可以使用複製活動直接呼叫 PolyBase,讓 Azure SQL 資料主目錄從源中提取資料。 如需詳細資料，請參閱**[使用 PolyBase 直接複製](#direct-copy-by-using-polybase)**。
* 如果您的來源資料存放區與格式不受 PolyBase 支援，您可以改用**[使用 PolyBase 分段複製](#staged-copy-by-using-polybase)** 功能。 分段複製功能也能提供更好的輸送量。 它會自動將資料轉換為與 PolyBase 相容的格式,將資料儲存在 Azure Blob 儲存中,然後調用 PolyBase 將資料載入到 SQL 資料倉庫中。

>[!TIP]
>詳細瞭解使用[PolyBase 的最佳做法](#best-practices-for-using-polybase)。

複製活動支援`polyBaseSettings`以下 PolyBase 設定:

| 屬性          | 描述                                                  | 必要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | 指定在查詢失敗前可以拒絕的資料列數目或百分比。<br/><br/>在[創建外部表(轉接-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)的參數部分中瞭解有關 PolyBase 拒絕選項的詳細資訊。 <br/><br/>允許的值為 0 (預設值)、1、2 等其他值。 | 否                                            |
| rejectType        | 指定 **rejectValue** 選項為常值或百分比。<br/><br/>允許的值是**值**(預設)和**百分比**。 | 否                                            |
| rejectSampleValue | 決定在 PolyBase 重新計算已拒絕的資料列百分比之前，所要擷取的資料列數目。<br/><br/>允許的值為 1、2 等其他值。 | 是，如果 **rejectType** 是**百分比**。 |
| useTypeDefault    | 指定當 PolyBase 從文字檔擷取資料時，如何處理分隔符號文字檔中遺漏的值。<br/><br/>從 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 的＜引數＞一節深入了解這個屬性。<br/><br/>允許的值為 **True** 和 **False** (預設值)。<br><br> | 否                                            |

### <a name="direct-copy-by-using-polybase"></a>使用 PolyBase 直接複製

SQL 資料倉儲 PolyBase 直接支援 Azure Blob、Azure 資料湖儲存第 1 代和 Azure 資料湖儲存第 2 代。 如果源數據符合本節中描述的條件,請使用 PolyBase 直接從來源資料儲存複製到 Azure 同步分析。 否則，請利用[使用 PolyBase 分段複製](#staged-copy-by-using-polybase)。

> [!TIP]
> 要有效地將資料複製到 SQL 資料倉儲,請從[Azure 資料工廠瞭解更多資訊,在將資料湖儲存與 SQL 資料主目錄一起使用時,可以更輕鬆地、更輕鬆地從資料中發現見解](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)。

如果不符合需求，Azure Data Factory 會檢查設定，並自動切換回適用於資料移動的 BULKINSERT 機制。

1. **來源服務**具有以下類型與認證方法:

    | 支援的來源資料儲存類型                             | 受支援的來源識別驗證類型                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 帳號金鑰身份驗證、管理識別身份驗證 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 服務主體驗證                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 帳號金鑰身份驗證、管理識別身份驗證 |

    >[!IMPORTANT]
    >如果 Azure 儲存設定 VNet 服務終結點,則必須使用託管識別身分驗證 - 請參閱[將 VNet 服務終結點與 Azure 儲存一起使用的影響](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 分別從 Azure Blob -[託管識別身分驗證](connector-azure-blob-storage.md#managed-identity)和 Azure[資料儲存儲存 Gen2 - 託管識別身份驗證](connector-azure-data-lake-storage.md#managed-identity)部分瞭解數據工廠中所需的配置。

2. **來源資料格式**為**Parquet、ORC**或**ORC****分隔文字**,具有以下設定:

   1. 資料夾路徑不包含通配符篩選器。
   2. 檔案名為空,或指向單個檔。 如果複製作用中指定的字元檔名,則只能為`*`或`*.*`。
   3. `rowDelimiter`是**預設值** **,\n** **、\r\n**或**\r**。
   4. `nullValue`保留為預設字串或設定為**空字串**("),並`treatEmptyAsNull`保留為預設值或設置為 true。
   5. `encodingName`保留為預設值或設定為**utf-8**。
   6. `quoteChar``escapeChar`,`skipLineCount`未指定。 PolyBase 支援跳過標頭行,可以配置`firstRowAsHeader`為 ADF 中。
   7. `compression`可以是**無壓縮****、GZip**或**Deflate**。

3. 如果來源是資料夾,`recursive`則必須在複製活動中設定為 true。

4. `wildcardFolderPath``wildcardFilename` `modifiedDateTimeEnd`,`additionalColumns`和`modifiedDateTimeStart`未指定。

>[!NOTE]
>如果來源是資料夾,請注意 PolyBase 會從資料夾及其所有子資料夾中檢索檔案,並且它不從檔案名以下劃線 (*) 或句點 (.) 開頭的檔案檢索資料,此處記錄[- 位置參數](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)。

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>使用 PolyBase 分段複製

當源數據與 PolyBase 未本機相容時,請透過臨時臨時 Azure Blob 儲存實例啟用資料複製(它不可能是 Azure 高級儲存)。 在這種情況下,Azure 數據工廠會自動轉換數據以滿足 PolyBase 的數據格式要求。 然後調用 PolyBase 將數據載入到 SQL 資料倉庫中。 最後，它會清除 Blob 儲存體中的暫存資料。 如需透過暫存 Azure Blob 儲存體執行個體複製資料的詳細資訊，請參閱[分段複製](copy-activity-performance-features.md#staged-copy)。

要使用此功能,請建立[Azure Blob 儲存連結服務](connector-azure-blob-storage.md#linked-service-properties),該服務引用具有暫存 Blob 儲存的 Azure 儲存帳戶。 然後指定複製`enableStaging`活動`stagingSettings`的 和 屬性,如以下代碼所示。

>[!IMPORTANT]
>如果暫存 Azure 儲存設定 VNet 服務終結點,則必須使用託管識別身分驗證 - 請參閱[將 VNet 服務終結點與 Azure 儲存一起使用的影響](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 從[Azure Blob 託管識別身份驗證](connector-azure-blob-storage.md#managed-identity)中瞭解數據工廠中所需的配置。

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>使用 PolyBase 的最佳做法

以下各節除了[Azure Synapse 分析的最佳實踐](../synapse-analytics/sql/best-practices-sql-pool.md)中提及的最佳實踐外,還提供了最佳實踐。

#### <a name="required-database-permission"></a>必要的資料庫權限

若要使用 PolyBase，將資料載入 SQL 資料倉儲的使用者必須具備目標資料庫的 ["CONTROL" 權限](https://msdn.microsoft.com/library/ms191291.aspx)。 達到此目標的其中一個方法是將該使用者新增為 **db_owner** 角色的成員。 在 [SQL 資料倉儲概觀](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)中了解如何執行此作業。

#### <a name="row-size-and-data-type-limits"></a>資料列大小和資料類型限制

PolyBase 負載的限制為小於 1 MB 的資料列。 它不能用於載入到 VARCHR(MAX)、NVARCHAR(MAX)或 VARBINARY(MAX)。 如需詳細資訊，請參閱 [SQL 資料倉儲服務容量限制](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

當您來源資料中的資料列大於 1 MB 時，您可能要將來源資料表垂直分割成幾個小的資料表。 務必確認每一列的大小不會超過限制。 然後,可以使用 PolyBase 載入較小的表,並在 Azure 同步分析中合併在一起。

或者,對於具有如此寬列的數據,您可以使用非 PolyBase 使用 ADF 載入數據,即關閉"允許 PolyBase"設定。

#### <a name="sql-data-warehouse-resource-class"></a>SQL 資料倉儲資源類別

若要達到最佳的可能輸送量，請透過 PolyBase 將較大型資源類別指派給要將資料載入 SQL 資料倉儲的使用者。

#### <a name="polybase-troubleshooting"></a>PolyBase, 疑難排解

**載入到十進位列**

如果來源資料以文字格式或其他非 PolyBase 相容儲存(使用暫存檔與 PolyBase),並且它包含要載入 SQL 資料主目錄十進位列中的空值,則可能會出現以下錯誤:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

解決方案是在複製活動接收器 -> PolyBase 設定中取消選擇「**使用預設類型**」選項(為 false)。 ["USE_TYPE_DEFAULT"](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)是 PolyBase 本機配置,它指定在 PolyBase 從文字檔中檢索資料時如何處理分隔文字檔中缺少的值。 

**`tableName`在 Azure 同步分析中**

下表是如何在 JSON 資料集中指定 **tableName** 屬性的範例。 其中會顯示數個結構描述和資料表名稱的組合。

| DB 結構描述 | 資料表名稱 | **tableName** JSON 屬性               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable 或 dbo.MyTable 或 [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable 或 [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] 或 [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

如果您看到下列錯誤，可能是您為 **tableName** 屬性指定的值有問題。 請參閱前面的資料表，以正確的方式指定 **tableName** JSON 屬性的值。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**包含預設值的資料行**

Data Factory 中的 PolyBase 功能目前只接受與目標資料表中相同的資料行數目。 範例是內含四個資料行的資料表，且其中一個資料行已使用預設值進行定義。 輸入資料仍需要有四個資料行。 3 個資料行的輸入資料集會產生類似下列訊息的錯誤︰

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 值是一種特殊形式的預設值。 如果資料欄可以是 Null，Blob 中該欄的輸入資料可能會是空白。 但輸入資料集中不能缺少輸入資料。 PolyBase 在 Azure 突觸分析中插入缺少值的 NULL。

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>使用 COPY 語片將資料載入 Azure SQL 資料倉儲(預覽)

SQL 資料主目錄[COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)(預覽)直接支援從 Azure Blob 和**Azure 資料儲存第 2 代**載入資料。 如果源數據符合本節中描述的條件,則可以選擇使用 ADF 中的 COPY 語句將數據載入到 Azure SQL 資料套件庫中。 Azure 資料工廠檢查設置,如果不符合條件,則失敗複製活動運行。

>[!NOTE]
>目前數據工廠僅支援從下面提到的 COPY 語句相容源的副本。

使用 COPY 語句支援以下設定:

1. **來源連結的服務和格式**具有以下類型與認證方法:

    | 支援的來源資料儲存類型                             | 支援的格式           | 受支援的來源識別驗證類型                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [分隔文字](format-delimited-text.md)             | 帳戶金鑰身份驗證、共用存取簽名身份驗證、服務主體身份驗證、託管識別身份驗證 |
    | &nbsp;                                                       | [實木 複合 地板](format-parquet.md)                    | 帳號金鑰身份驗證、分享存取簽章認證 |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | 帳號金鑰身份驗證、分享存取簽章認證 |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [分隔文字](format-delimited-text.md)<br/>[實木 複合 地板](format-parquet.md)<br/>[ORC](format-orc.md) | 帳戶金鑰身份驗證、服務主體身份驗證、託管識別身份驗證 |

    >[!IMPORTANT]
    >如果 Azure 儲存設定 VNet 服務終結點,則必須使用託管識別身分驗證 - 請參閱[將 VNet 服務終結點與 Azure 儲存一起使用的影響](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 分別從 Azure Blob -[託管識別身分驗證](connector-azure-blob-storage.md#managed-identity)和 Azure[資料儲存儲存 Gen2 - 託管識別身份驗證](connector-azure-data-lake-storage.md#managed-identity)部分瞭解數據工廠中所需的配置。

2. 格式設定與以下內容:

   1. 對於**Parquet:**`compression`可以**沒有壓縮**,**斯內皮**,或**GZip。**
   2. 對**ORC**:`compression`無法**```zlib```****沒有壓縮**, 或**Snappy**。
   3. 對**分隔文字**:
      1. `rowDelimiter`明確設定為**單個字元**或 **「\r\n」,** 不支援預設值。
      2. `nullValue`保留為預設字串或設置為**空字串**(")。
      3. `encodingName`保留為預設值或設定為**utf-8 或 utf-16**。
      4. `escapeChar`必須與`quoteChar`相同,並且不為空。
      5. `skipLineCount`保留為預設值或設置為 0。
      6. `compression`可以是**沒有壓縮**或**GZip。**

3. 如果來源是資料夾,`recursive`則必須在複製活動中設定為 true。

4. `wildcardFolderPath``wildcardFilename` `modifiedDateTimeEnd`,`additionalColumns`和`modifiedDateTimeStart`未指定。

複製活動支援`allowCopyCommand`以下 COPY 語句設定:

| 屬性          | 描述                                                  | 必要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| 預設值 | 指定 SQL DW 中每個目標列的預設值。  屬性中的預設值覆蓋資料倉庫中設置的 DEFAULT 約束,標識列不能具有預設值。 | 否 |
| 其他選項 | 將直接傳遞到 SQL DW COPY 語句的其他選項,這些選項將直接在[COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)中的"With"子句中傳遞。 根據需要引用該值以符合 COPY 語句要求。 | 否 |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>尋找活動屬性

要瞭解有關屬性的詳細資訊,請檢查[。](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>取得中繼資料活動屬性

要瞭解有關屬性的詳細資訊,請選擇[中取得中繼資料活動](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲的資料類型對應

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在映射資料流中轉換數據時,可以從 Azure 同步分析讀取和寫入表。 關於詳細資訊,請參考映射資料串流的[來源轉換](data-flow-source.md)與[接收器轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

特定於 Azure 同步分析的設定可在源轉換的 **「源選項**」選項卡中提供。 

**輸入:** 選擇是將源指向表(等效```Select * from <table-name>```項)還是輸入自定義 SQL 查詢。

**查詢**:如果在輸入欄位中選擇"查詢",請輸入源的 SQL 查詢。 此設定將覆蓋您在資料集中選擇的任何表。 **這裡不支援「按訂單」** 子句,但您可以設定完整的 SELECT FROM 語句。 您還可以使用使用者定義的表函數。 **從 udfGetData() 中選擇 *** 是 SQL 中的 UDF,用於返回表。 此查詢將生成一個源表,可用於數據流。 使用查詢也是減少用於測試或查找的行的好方法。 

* SQL 範例:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**批次處理大小**:輸入批次處理大小以將大數據塊塊到讀取中。 在資料流中,ADF 將使用此設置來設置 Spark 列緩存。 這是一個選項欄位,如果選項欄位留空,將使用 Spark 預設值。

**隔離等級**:映射數據流的 SQL 源的預設值為未提交。 您可以在此處將隔離等級變更為以下值之一:
* 讀取認可
* 讀取未認可
* 可重複讀取
* 可序列化
* 無(忽略隔離等級)

![隔離等級](media/data-flow/isolationlevel.png "隔離等級")

### <a name="sink-transformation"></a>水槽變換

特定於 Azure 同步分析的設定可在接收器轉換的 **「設定」** 選項卡中提供。

**更新方法:** 確定資料庫目標上允許的操作。 預設值是僅允許插入。 要更新、更新或刪除行,需要更改行轉換來標記這些操作的行。 對於更新、升級和刪除,必須設置鍵列或列以確定要更改的行。

**表理:** 確定在寫入之前是從目標表重新創建或刪除所有行。
* 無:對表不執行任何操作。
* 重新建立:該表將被刪除並重新創建。 動態創建新表時是必需的。
* 截取:將刪除目標表中的所有行。

**開啟暫存:** 確定在寫入 Azure 同步分析時是否使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)

**批次調整大小**:控制每個儲存桶中寫入的行數。 較大的批處理大小可改善壓縮和記憶體優化,但在緩存數據時存在記憶體異常風險。

**前 SQL 和後 SQL 文稿**:輸入將在(預處理)和之後(後處理)資料寫入 Sink 資料庫之前執行的多行 SQL 文稿

![SQL 處理文稿後處理文稿](media/data-flow/prepost1.png "SQL 處理文稿")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure 突觸分析的資料類型對應

從 Azure 同步分析複製資料或複製到 Azure 同步分析時,將使用以下映射,從 Azure 同步分析數據類型到 Azure 資料工廠臨時數據類型。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收。

>[!TIP]
>請參閱[Azure Synapse 分析一文中的表數據類型](../synapse-analytics/sql/develop-tables-data-types.md),介紹 SQL DW 支援的數據類型以及不支援的數據類型的解決方法。

| Azure 同步分析資料型態    | Data Factory 過渡期資料類型 |
| :------------------------------------ | :----------------------------- |
| BIGINT                                | Int64                          |
| BINARY                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | Datetime                       |
| Datetime                              | Datetime                       |
| datetime2                             | Datetime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| NCHAR                                 | String, Char[]                 |
| NUMERIC                               | Decimal                        |
| NVARCHAR                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Datetime                       |
| SMALLINT                              | Int16                          |
| SMALLMONEY                            | Decimal                        |
| time                                  | TimeSpan                       |
| TINYINT                               | Byte                           |
| UNIQUEIDENTIFIER                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收端的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
