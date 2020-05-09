---
title: 教學課程從 Azure Data Lake Storage 載入資料
description: 使用 PolyBase 外部資料表，從 Synapse SQL 的 Azure Data Lake Storage 載入資料。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f26aafc771998ea73d1a4f97f0e960a94f6775c3
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626712"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>從 Azure Data Lake Storage 載入 SQL 分析的資料

本指南概述如何使用 PolyBase 外部資料表從 Azure Data Lake Storage 載入資料。 雖然您可以對儲存在 Data Lake Storage 中的資料執行臨機操作查詢，但建議您匯入資料以獲得最佳效能。

> [!NOTE]  
> 載入的替代方案是目前處於公開預覽狀態的[COPY 語句](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。  COPY 語句提供最大的彈性。 若要提供有關 COPY 語句的意見反應，請將電子郵件傳送至下列sqldwcopypreview@service.microsoft.com通訊群組清單：。
>
> [!div class="checklist"]
>
> * 建立從 Data Lake Storage 載入所需的資料庫物件。
> * 連接到 Data Lake Storage 目錄。
> * 將資料載入資料倉儲。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>開始之前

開始本教學課程之前，請下載並安裝最新版的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS)。

若要執行此教學課程，您需要：

* SQL 集區。 請參閱[建立 SQL 集區和查詢資料](create-data-warehouse-portal.md)。
* Data Lake Storage 帳戶。 請參閱[開始使用 Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 針對此儲存體帳戶，您必須設定或指定下列其中一個要載入的認證：儲存體帳戶金鑰、Azure 目錄應用程式使用者，或對儲存體帳戶具有適當 RBAC 角色的 AAD 使用者。

## <a name="create-a-credential"></a>建立認證

使用 AAD 傳遞進行驗證時，您可以略過本節並繼續進行「建立外部資料源」。 使用 AAD 傳遞時，不需要建立或指定資料庫範圍認證，但請確定您的 AAD 使用者具有儲存體帳戶的適當 RBAC 角色（儲存體 Blob 資料讀取者、參與者或擁有者角色）。 [這裡](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260)會概述更多詳細資料。

若要存取您的 Data Lake Storage 帳戶，您必須建立資料庫主要金鑰來加密您的認證密碼。 接著，您要建立資料庫範圍認證來儲存您的秘密。 使用服務主體（Azure 目錄應用程式使用者）進行驗證時，資料庫範圍認證會儲存 AAD 中設定的服務主體認證。 您也可以使用資料庫範圍認證來儲存 Gen2 的儲存體帳戶金鑰。

若要使用服務主體連接到 Data Lake Storage，您必須**先**建立 Azure Active Directory 應用程式、建立存取金鑰，並將該應用程式的存取權授與 Data Lake Storage 帳戶。 如需指示，請參閱[使用 Active Directory 向 Azure Data Lake Storage 進行驗證](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

以擁有控制層級許可權的使用者登入您的 SQL 集區，並針對您的資料庫執行下列 SQL 語句：

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>建立外部資料來源

使用此 [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 命令以儲存資料的位置。 如果您要使用 AAD 傳遞進行驗證，則不需要 CREDENTIAL 參數。 如果您要使用服務端點的受控識別進行驗證，請遵循此[檔](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-synapse-analytics-polybase)來設定外部資料源。

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>設定資料格式

若要從 Data Lake Storage 匯入資料，您需要指定外部檔案格式。 此物件會定義檔案在 Data Lake Storage 中的寫入方式。
如需完整清單，請查閱我們的 T-SQL 文件＜[CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)＞

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>建立外部資料表

您在指定資料來源和檔案格式之後，便可以開始建立外部資料表。 外部資料表是您與外部資料進行互動的方式。 位置參數可以指定檔案或目錄。 如果指定目錄，目錄中的所有檔案都會載入。

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>外部資料表考量

建立外部資料表很簡單，但是有一些必須討論的細微差異。

外部資料表是強型別。 這表示內嵌的每個資料列都必須滿足資料表結構描述定義。
如果資料列不符合結構描述定義，載入時就會拒絕該資料列。

REJECT_TYPE 和 REJECT_VALUE 選項可讓您定義最終的資料表中必須出現多少資料列或多少百分比的資料。 在載入期間，如果達到拒絕值，載入即失敗。 資料列遭拒最常見的原因是結構描述定義不相符。 例如，如果檔案中的資料是字串，卻對資料行指定不正確的整數結構描述，則會無法載入每個資料列。

Data Lake Storage Gen1 使用角色型存取控制 (RBAC) 來控制資料存取。 這表示服務主體必須具有在位置參數中所定義之目錄，以及最終目錄和檔案之子系的讀取權限。 這可讓 PolyBase 驗證及載入該資料。

## <a name="load-the-data"></a>載入資料

若要從 Data Lake Storage 載入資料，請使用[CREATE TABLE AS SELECT （transact-sql）](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語句。

CTAS 建立新的資料表，並將選取陳述式的結果填入該資料表。 CTAS 定義新資料表，以使它擁有和選取陳述式之結果相同的資料行和資料類型。 如果您選取外部資料表上的所有資料行，則新資料表會是外部資料表中資料行和資料類型的複本。

在此範例中，我們會從我們的外部資料表 DimProduct_external 建立一個名為 DimProduct 的雜湊分散式資料表。

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```

## <a name="optimize-columnstore-compression"></a>最佳化資料行存放區壓縮

根據預設，資料表會定義為叢集資料行存放區索引。 載入完成後，某些資料列可能不會被壓縮為資料行存放區。  有許多原因會導致發生此情況。 若要深入了解，請參閱[管理資料行存放區索引](sql-data-warehouse-tables-index.md)。

若要最佳化載入後的查詢效能和資料行存放區壓縮，請重建資料表以強制資料行存放區索引對所有資料列進行壓縮。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>最佳化統計資料

您最好在載入後立刻建立單一資料行統計資料。 針對統計資料，您將會有一些選項。 例如，如果您在每個資料行上建立單一資料行統計資料，可能會需要很長的時間才能重建所有統計資料。 如果您知道某些資料行不會被包含在查詢述詞中，您可以略過為那些資料行建立統計資料。

如果您決定要在每個資料表的每個資料行上建立單一資料行統計資料，您可以使用[統計資料](sql-data-warehouse-tables-statistics.md)一文中的預存程序程式碼範例 `prc_sqldw_create_stats`。

下列範例為建立統計資料的好起點。 它會在維度資料表中的每個資料行上，以及在事實資料表中的每個聯結資料行上建立單一資料行統計資料。 您之後隨時可以將單一或多個資料行統計資料新增到其他事實資料表資料行上。

## <a name="achievement-unlocked"></a>成就解鎖！

您已成功將資料載入資料倉儲。 太棒了！

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立外部資料表來定義儲存在 Data Lake Storage Gen1 中的資料結構，然後使用 PolyBase CREATE TABLE AS SELECT 陳述式將資料載入資料倉儲。

您進行了下列事項：
> [!div class="checklist"]
>
> * 已建立從 Data Lake Storage 載入所需的資料庫物件。
> * 已連接到 Data Lake Storage 目錄。
> * 已將資料載入資料倉儲。
>

載入資料是使用 Azure Synapse 分析來開發資料倉儲解決方案的第一個步驟。 請參閱我們的開發資源。

> [!div class="nextstepaction"]
> [瞭解如何開發資料倉儲的資料表](sql-data-warehouse-tables-overview.md)
