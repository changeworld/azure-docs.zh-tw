---
title: 教學從 Azure 資料的資料儲存載入資料
description: 使用 PolyBase 外部表從 Azure 數據湖儲存載入資料以進行 Synapse SQL。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7d599ce121b4c53662b91e5aab94130b0f3f4458
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583944"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>載入 Azure 資料湖儲存中的資料,用於 Synapse SQL

本指南概述了如何使用 PolyBase 外部表從 Azure 數據湖存儲載入數據。 儘管您可以對數據存儲在數據湖存儲中的數據運行臨時查詢,但我們建議導入數據以獲得最佳性能。 

> [!NOTE]  
> 載入的替代為目前在公共預覽的[COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)。  COPY 語句提供了最大的靈活性。 要提供有關 COPY 語句的回饋,請傳送電子郵件至以下通訊sqldwcopypreview@service.microsoft.com群組清單: 。
>
> [!div class="checklist"]

> * 創建從數據湖儲存載入所需的資料庫物件。
> * 連接到數據湖存儲目錄。
> * 將數據載入到數據倉庫中。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>開始之前
開始本教學課程之前，請下載並安裝最新版的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

若要執行此教學課程，您需要：

* SQL 池。 請參考[SQL 池與查詢資料](create-data-warehouse-portal.md)。
* 數據湖存儲帳戶。 請參考[啟動的 Azure 資料儲存](../../data-lake-store/data-lake-store-get-started-portal.md)。 對於此儲存帳戶,您需要配置或指定要載入的以下認證之一:儲存帳戶金鑰、Azure 目錄應用程式使用者或具有相應 RBAC 角色的 AAD 使用者。 

##  <a name="create-a-credential"></a>建立認證
使用 AAD 直通驗證時,可以跳過此部分並繼續"創建外部數據源」。 使用 AAD 傳遞時不需要建立或指定資料庫作用域認證,但請確保 AAD 使用者具有儲存帳戶的相應 RBAC 角色(儲存 Blob 資料讀取器、參與者或擁有者角色)。 更多細節[將在這裡](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260)概述。 

要存取資料湖儲存帳戶,您需要創建資料庫主金鑰來加密認證認證。 然後創建資料庫作用域憑據來儲存機密。 使用服務主體(Azure 目錄應用程式使用者)進行身份驗證時,資料庫作用域憑據將存儲在 AAD 中設置的服務主體認證。 您還可以使用資料庫作用域憑據存儲 Gen2 的儲存帳戶密鑰。

要使用服務主體連接到資料湖存儲,必須**首先**創建 Azure 活動目錄應用程式、創建存取金鑰以及授予應用程式對資料湖存儲帳戶的存取許可權。 有關說明,請參閱[使用活動目錄對 Azure 資料湖儲存進行身份認證](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)。

使用具有 CONTROL 等級權限的使用者登入 SQL 池,並針對資料庫執行以下 SQL 語句:

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

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
使用此 [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) 命令以儲存資料的位置。 如果使用 AAD 直通進行身份驗證,則不需要「憑據」參數。 

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
要從資料儲存庫導入資料,需要指定外部檔案格式。 此物件定義檔在資料湖存儲中寫入的方式。
如需完整清單，請查閱我們的 T-SQL 文件＜[CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)＞

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
要從資料儲存載入資料,請使用[「創建表作為選擇」(轉算-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)語句。 

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
默認情況下,表定義為群集列存儲索引。 載入完成後，某些資料列可能不會被壓縮為資料行存放區。  有許多原因會導致發生此情況。 若要深入了解，請參閱[管理資料行存放區索引](sql-data-warehouse-tables-index.md)。

若要最佳化載入後的查詢效能和資料行存放區壓縮，請重建資料表以強制資料行存放區索引對所有資料列進行壓縮。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>最佳化統計資料
您最好在載入後立刻建立單一資料行統計資料。 針對統計資料，您將會有一些選項。 例如，如果您在每個資料行上建立單一資料行統計資料，可能會需要很長的時間才能重建所有統計資料。 如果您知道某些資料行不會被包含在查詢述詞中，您可以略過為那些資料行建立統計資料。

如果您決定要在每個資料表的每個資料行上建立單一資料行統計資料，您可以使用[統計資料](sql-data-warehouse-tables-statistics.md)一文中的預存程序程式碼範例 `prc_sqldw_create_stats`。

下列範例為建立統計資料的好起點。 它會在維度資料表中的每個資料行上，以及在事實資料表中的每個聯結資料行上建立單一資料行統計資料。 您之後隨時可以將單一或多個資料行統計資料新增到其他事實資料表資料行上。

## <a name="achievement-unlocked"></a>成就解鎖！
您已成功將資料載入資料主目錄中。 太棒了！

## <a name="next-steps"></a>後續步驟 
在本教學課程中，您已建立外部資料表來定義儲存在 Data Lake Storage Gen1 中的資料結構，然後使用 PolyBase CREATE TABLE AS SELECT 陳述式將資料載入資料倉儲。 

您進行了下列事項：
> [!div class="checklist"]
>
> * 創建從數據湖儲存載入所需的資料庫物件。
> * 連接到數據湖存儲目錄。
> * 將數據載入到數據倉庫中。
>

載入資料是使用 Azure 同步分析開發數據倉庫解決方案的第一步。 請參閱我們的開發資源。

> [!div class="nextstepaction"]
> [瞭解如何為資料主目錄開發表](sql-data-warehouse-tables-overview.md)
