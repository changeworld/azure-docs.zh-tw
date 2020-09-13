---
title: 針對 Azure Data Factory 連接器進行疑難排解
description: 了解如何針對 Azure Data Factory 中的連接器問題進行疑難排解。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 62a5f3b18d4b8329c4a15086bc23d09805b786ab
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89668890"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>針對 Azure Data Factory 連接器進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將探討 Azure Data Factory 中連接器的常見疑難排解方法。
  
## <a name="azure-blob-storage"></a>Azure Blob 儲存體

### <a name="error-code--azurebloboperationfailed"></a>錯誤碼：AzureBlobOperationFailed

- **訊息**：`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**︰Blob 儲存體作業遇到問題。

- **建議**：請檢查錯誤詳細資料。 請參閱 Blob 說明文件： https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 。 如需協助，請連絡儲存體小組。


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>錯誤碼：AzureBlobServiceNotReturnExpectedDataLength

- **訊息**：`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>錯誤碼：AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **訊息**：`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>錯誤碼：AzureStorageOperationFailedConcurrentWrite

- **訊息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>錯誤訊息：要求大小太大

- **徵兆**：您將資料複製到具有預設寫入批次大小的 Azure Cosmos DB，並遇到「**要求大小太大**」錯誤。

- **原因**︰Cosmos DB 會將一個單一要求的大小限制為 2 MB。 公式為要求大小 = 單一文件大小 * 寫入批次大小。 如果您文件大小很大，則預設行為會導致太大的要求大小。 您可以調整寫入批次大小。

- **解決方法**：在複製活動接收中，減少 [寫入批次大小] 值 (預設值為10000)。

### <a name="error-message-unique-index-constraint-violation"></a>錯誤訊息：唯一索引限制違規

- **徵兆**：將資料複製到 Cosmos DB 時，您遇到下列錯誤：

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**︰有兩個可能的原因：

    - 如果您使用 **Insert** 作為寫入行為，此錯誤表示您的來源資料具有識別碼相同的資料列/物件。

    - 如果您使用 **Upsert** 作為寫入行為，並將另一個唯一索引鍵設定為容器，此錯誤表示您的來源資料具有識別碼不同但所定義唯一索引鍵值相同的資料列/物件。

- **解決方法**： 

    - 針對原因 1，請將 **Upsert** 設定為寫入行為。
    - 針對原因 2，請確定每份文件所定義的唯一索引鍵都有不同值。

### <a name="error-message-request-rate-is-large"></a>錯誤訊息：要求率非常大

- **徵兆**：將資料複製到 Cosmos DB 時，您遇到下列錯誤：

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**︰所使用的要求單位大於 Cosmos DB 中設定的可用 RU。 從[這裡](../cosmos-db/request-units.md#request-unit-considerations)了解 Cosmos DB 如何計算 RU。

- **解決方法**：有兩個解決方案：

    1. **將容器 RU 增加**為 Cosmos DB 中較大的值，這會改善複製活動效能，不過會在 Cosmos DB 中產生更多成本。 

    2. 將 **writeBatchSize** 減少為較小的值 (例如 1000)，並將 **parallelCopies** 設定為較小的值 (例如 1)，這會使複製執行效能變得比目前更糟，但不會在 Cosmos DB 中產生更多成本。

### <a name="column-missing-in-column-mapping"></a>資料行對應中遺漏資料行

- **徵兆**：當您匯入 Cosmos DB 的結構描述以進行資料行對應時，遺失某些資料行。 

- **原因**︰ADF 會從前 10 個 Cosmos DB 文件推斷結構描述。 如果某些資料行/屬性在這些文件中沒有任何值，就無法讓 ADF 偵測到，因此不會顯示。

- **解決方法**：您可以依照下列方式調整查詢，以強制空值的資料行顯示在結果集中：(假設前 10 個文件中遺漏 "impossible" 資料行)。 或者，您可以手動新增資料行來進行對應。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>錯誤訊息：讀取器的 GuidRepresentation 是 CSharpLegacy

- **徵兆**：從具有 UUID 欄位的 Cosmos DB MongoAPI/MongoDB 複製資料時，遇到下列錯誤：

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**︰有兩種方式可代表 BSON 中的 UUID - UuidStardard 和 UuidLegacy。 根據預設，UuidLegacy 會用來讀取資料。 如果 MongoDB 中的 UUID 資料是 UuidStandard，您將會遇到錯誤。

- **解決方法**：在 MongoDB 連接字串中，新增 "**uuidRepresentation=standard**" 選項。 如需詳細資訊，請參閱 [MongoDB 連接字串](connector-mongodb.md#linked-service-properties)。
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>錯誤碼：AdlsGen2OperationFailed

- **訊息**：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**︰ADLS Gen2 擲回表示作業失敗的錯誤。

- **建議**：檢查 ADLS Gen2 擲回的詳細錯誤訊息。 如果是暫時性失敗所造成，請再試一次。 如果您需要進一步協助，請連絡 Azure 儲存體支援服務，並提供錯誤訊息中的要求識別碼。

- **原因**︰當錯誤訊息包含「禁止」時，表示您所使用的服務主體或受控識別可能沒有足夠的權限可存取 ADLS Gen2。

- **建議**：請參閱說明文件： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 。

- **原因**︰當錯誤訊息包含 'InternalServerError' 時，ADLS Gen2 會傳回錯誤。

- **建議**：這可能是因為暫時性失敗所造成，請再試一次。 如果問題持續發生，請連絡 Azure 儲存體支援服務，並提供錯誤訊息中的要求識別碼。


### <a name="error-code--adlsgen2invalidurl"></a>錯誤碼：AdlsGen2InvalidUrl

- **訊息**：`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>錯誤碼：AdlsGen2InvalidFolderPath

- **訊息**：`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>錯誤碼：AdlsGen2OperationFailedConcurrentWrite

- **訊息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>錯誤碼： AdlsGen2TimeoutError

- **訊息**：`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>錯誤訊息：基礎連接已關閉：無法為 SSL/TLS 安全通道建立信任關係。

- **徵兆**：複製活動失敗，並出現下列錯誤： 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **原因**： TLS 信號交換期間憑證驗證失敗。

- **解決**方法：因應措施：使用分段複製來略過 ADLS GEN1 的 TLS 驗證。 您需要重現此問題並收集 netmon 追蹤，然後讓您的網路小組檢查區域網路設定。

    ![針對 ADLS Gen1 進行疑難排解](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>錯誤訊息：遠端伺服器傳回錯誤：(403) 禁止

- **徵兆**：複製活動因下列錯誤而失敗： 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**︰其中一個可能原因是您使用的服務主體或受控識別沒有存取特定資料夾/檔案的權限。

- **解決方法**：對您需要複製的所有資料夾和子資料夾，授與對應的權限。 請參閱[此文件](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>錯誤訊息：無法使用服務主體取得存取權杖。 ADAL 錯誤：service_unavailable

- **徵兆**：複製活動因下列錯誤而失敗：

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**︰當 Azure Active Directory 所擁有的服務權杖伺服器 (Service Token Server，STS) 無法使用時 (亦即，過於忙碌而無法處理要求時)，其會傳回 HTTP 錯誤 503。 

- **解決方法**：在數分鐘後重新執行複製活動。
                  

## <a name="azure-synapse-analytics-formerly-sql-data-warehouseazure-sql-databasesql-server"></a>Azure Synapse Analytics (先前的 SQL 資料倉儲) /Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>錯誤碼：SqlFailedToConnect

- **訊息**：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**︰如果錯誤訊息包含 "SqlException"，SQL Database 會擲回錯誤，指出某些特定的作業失敗。

- **建議**：如需詳細資料，請依下列參考檔中的 SQL 錯誤碼搜尋： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如果您需要進一步的協助，請連絡 Azure SQL 支援服務。

- **原因**︰如果錯誤訊息包含「具有 IP 位址 '...' 的用戶端不能存取伺服器」，而且您正嘗試連接線到 Azure SQL Database，這通常是由 Azure SQL Database 防火牆問題所造成。

- **建議**：在邏輯 SQL server 防火牆設定中，啟用 [允許 Azure 服務和資源存取此伺服器] 選項。 參考文件： https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure 。


### <a name="error-code--sqloperationfailed"></a>錯誤碼：SqlOperationFailed

- **訊息**：`A database operation failed. Please search error to get more details.`

- **原因**︰如果錯誤訊息包含 "SqlException"，SQL Database 會擲回錯誤，指出某些特定的作業失敗。

- **建議**：如果 SQL 錯誤不清楚，請嘗試將資料庫變更為最新的相容性層級 '150'。 如此可能會擲回最新版本的 SQL 錯誤。 請參閱 [詳細資料](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)檔。

    如需針對 SQL 問題進行疑難排解，請在此參考文件中以 SQL 錯誤碼進行搜尋，以取得詳細資訊： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如果您需要進一步的協助，請連絡 Azure SQL 支援服務。

- **原因**︰如果錯誤訊息包含 "PdwManagedToNativeInteropException"，通常是因為來源與接收資料行的大小不相符所造成。

- **建議**：檢查來源和接收資料行的大小。 如果您需要進一步的協助，請連絡 Azure SQL 支援服務。

- **原因**︰如果錯誤訊息包含 "InvalidOperationException"，通常是因為無效的輸入資料所造成。

- **建議**：若要找出發生問題的資料列，請在複製活動上啟用容錯功能，以將有問題的資料列重新導向至儲存體，以進行進一步調查。 參考文件： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。



### <a name="error-code--sqlunauthorizedaccess"></a>錯誤碼：SqlUnauthorizedAccess

- **訊息**：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**︰認證不正確，或登入帳戶無法存取 SQL Database。

- **建議**：檢查登入帳戶是否有足夠的權限可以存取 SQL Database。


### <a name="error-code--sqlopenconnectiontimeout"></a>錯誤碼：SqlOpenConnectionTimeout

- **訊息**：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**︰可能是 SQL Database 的暫時性失敗。

- **建議**：以較大的連接逾時值重試更新連結服務連接字串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>錯誤碼：SqlAutoCreateTableTypeMapFailed

- **訊息**：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**︰自動建立資料表無法符合來源需求。

- **建議**：更新 [對應] 中的資料行類型，或以手動方式在目標伺服器中建立接收資料表。


### <a name="error-code--sqldatatypenotsupported"></a>錯誤碼：SqlDataTypeNotSupported

- **訊息**：`A database operation failed. Check the SQL errors.`

- **原因**︰如果問題發生在 SQL 來源上，而且錯誤與 SqlDateTime 溢位相關，則資料值會超過邏輯類型範圍 (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM)。

- **建議**：將類型轉換為來源 SQL 查詢中的字串，或在複製活動資料行對應中，將資料行類型變更為 'String'。

- **原因**︰如果問題發生在 SQL 接收上，而且錯誤與 SqlDateTime 溢位相關，則資料值會超過接收資料表中允許的範圍。

- **建議**：將對應的資料行類型更新為接收資料表中的 'datetime2' 類型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>錯誤碼：SqlInvalidDbStoredProcedure

- **訊息**：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**︰指定的預存程序無效。 這可能是因為預存程序未傳回任何資料所造成。

- **建議**：驗證各個 SQL 工具的預存程序。 請確定預存程序可以傳回資料。


### <a name="error-code--sqlinvaliddbquerystring"></a>錯誤碼：SqlInvalidDbQueryString

- **訊息**：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**︰指定的 SQL 查詢無效。 這可能是因為查詢未傳回任何資料所造成

- **建議**：驗證各個 SQL 工具的 SQL 查詢。 確定查詢可以傳回資料。


### <a name="error-code--sqlinvalidcolumnname"></a>錯誤碼：SqlInvalidColumnName

- **訊息**：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**︰找不到資料行。 可能是設定錯誤。

- **建議**：確認查詢中的資料行、資料集中的「結構」和活動中的「對應」。


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>錯誤碼：SqlColumnNameMismatchByCaseSensitive

- **訊息**：`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>錯誤碼：SqlBatchWriteTimeout

- **訊息**：`Timeouts in SQL write operation.`

- **原因**︰可能是 SQL Database 的暫時性失敗。

- **建議**：重試。 如果問題重現，請連絡 Azure SQL 支援服務。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>錯誤碼：SqlBatchWriteTransactionFailed

- **訊息**：`SQL transaction commits failed`

- **原因**︰如果例外狀況詳細資料經常顯示交易逾時，則表示整合執行階段與資料庫之間的網路延遲高於 30 秒的預設閾值。

- **建議**：將 SQL 連結服務連接字串的「連線逾時」值更新為 120 或更高，然後重新執行活動。

- **原因**︰如果例外狀況詳細資料不時顯示 sqlconnection 已中斷，則可能是暫時性的網路失敗，或是 SQL Database 端的問題

- **建議**：重試活動並查看 SQL Database 端計量。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>錯誤碼：SqlBulkCopyInvalidColumnLength

- **訊息**：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**︰因為接收到來自 bcp 用戶端的無效資料行長度，所以 SQL 大量複製失敗。

- **建議**：若要找出發生問題的資料列，請在複製活動上啟用容錯功能，以將有問題的資料列重新導向至儲存體，以進行進一步調查。 參考文件： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlconnectionisclosed"></a>錯誤碼：SqlConnectionIsClosed

- **訊息**：`The connection is closed by SQL Database.`

- **原因**︰並行執行數過高且伺服器終止連線時，SQL Database 會關閉 SQL 連線。

- **建議**：遠端伺服器關閉 SQL 連線。 重試。 如果問題重現，請連絡 Azure SQL 支援服務。


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>錯誤碼：SqlCreateTableFailedUnsupportedType

- **訊息**：`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>錯誤訊息：從字元字串轉換到 uniqueidentifier 時失敗

- **徵兆**：當您使用分段複製和 PolyBase 將資料從表格式資料來源 (例如 SQL Server) 複製到 Azure Synapse Analytics 時，您會遇到下列錯誤：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**： Azure Synapse Analytics PolyBase 無法將空字串轉換成 GUID。

- **解決方法**：在複製活動接收的 [Polybase 設定] 底下，將 [**使用類型預設值**] 選項設定為 false。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>錯誤訊息：預期的資料類型：DECIMAL(x,x)，違規值

- **徵兆**：當您使用分段複製和 PolyBase 將資料從表格式資料來源 (例如 SQL Server) 複製到 Azure Synapse Analytics 時，您會遇到下列錯誤：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**： Azure Synapse Analytics Polybase 無法將空字串插入至小數資料行 (null) 值。

- **解決方法**：在複製活動接收的 [Polybase 設定] 底下，將 [**使用類型預設值**] 選項設定為 false。

### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>錯誤訊息： JAVA 例外狀況訊息： HdfsBridge：： CreateRecordReader

- **徵兆**：您使用 PolyBase 將資料複製到 Azure Synapse Analytics，並遇到下列錯誤：

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**︰可能原因是結構描述 (總資料行寬度) 太大 (大於 1 MB)。 藉由新增所有資料行的大小，檢查目標 Azure Synapse Analytics 資料表的架構：

    - Int -> 4 個位元組
    - Bigint -> 8 個位元組
    - Varchar(n),char(n),binary(n), varbinary(n) -> n 個位元組
    - Nvarchar(n), nchar(n) -> n*2 個位元組
    - Date -> 6 個位元組
    - Datetime/(2), smalldatetime -> 16 個位元組
    - Datetimeoffset -> 20 個位元組
    - Decimal -> 19 個位元組
    - Float -> 8 個位元組
    - Money -> 8 個位元組
    - Smallmoney -> 4 個位元組
    - Real -> 4 個位元組
    - Smallint -> 2 個位元組
    - Time -> 12 個位元組
    - Tinyint -> 1 個位元組

- **解決方法**：將資料行寬度減少為小於 1 MB

- 或藉由停用 Polybase 來使用大量插入方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>錯誤訊息：使用 HTTP 條件式標頭的指定條件不符

- **徵兆**：您可以使用 SQL 查詢從 Azure Synapse Analytics 中提取資料，並遇到下列錯誤：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**：查詢 Azure 儲存體中的外部資料表時，Azure Synapse Analytics 遇到問題。

- **解決方法**：在 SSMS 中執行相同的查詢，並檢查您是否看到相同的結果。 如果是，請開啟支援票證來 Azure Synapse Analytics，並提供您的 Azure Synapse Analytics 伺服器和資料庫名稱，以進行進一步的疑難排解。
            

## <a name="delimited-text-format"></a>分隔符號文字格式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>錯誤碼：DelimitedTextColumnNameNotAllowNull

- **訊息**：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**︰在活動中設定 'firstRowAsHeader' 時，第一個資料列會用來作為資料行名稱。 此錯誤表示第一個資料列包含空的值。 例如： ' ColumnA，，ColumnB '。

- **建議**：檢查第一個資料列，如果有空白值，請修正此值。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>錯誤碼：DelimitedTextMoreColumnsThanDefined

- **訊息**：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **原因**︰問題資料列的資料行計數大於第一個資料列的資料行計數。 這可能是因為資料問題或資料行分隔符號/引號字元設定不正確所造成。

- **建議**：取得錯誤訊息中的資料列計數，檢查資料列的資料行並修正資料。

- **原因**︰如果預期的資料行計數在錯誤訊息中是 "1"，表示您可能指定了錯誤的壓縮或格式設定，這會導致 ADF 不當剖析您的檔案。

- **建議**：請檢查格式設定，以確定其符合您的來源檔案。

- **原因**︰如果您的來源是資料夾，則指定資料夾下的檔案可能有不同的結構描述。

- **建議**：請確定指定資料夾下的檔案具有相同結構描述。


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>錯誤碼：DelimitedTextIncorrectRowDelimiter

- **訊息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>錯誤碼：DelimitedTextTooLargeColumnCount

- **訊息**：`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>錯誤碼：DelimitedTextInvalidSettings

- **訊息**：`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>錯誤碼：DynamicsCreateServiceClientError

- **訊息**：`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因**︰這是 Dynamics 伺服器端的暫時性問題。

- **建議**：重新執行管線。 如果持續失敗，請嘗試減少平行處理原則。 如果仍然失敗，請連絡 Dynamics 支援服務。



## <a name="json-format"></a>JSON 格式

### <a name="error-code--jsoninvalidarraypathdefinition"></a>錯誤碼：JsonInvalidArrayPathDefinition

- **訊息**：`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>錯誤碼：JsonEmptyJObjectData

- **訊息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>錯誤碼：JsonNullValueInPathDefinition

- **訊息**：`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>錯誤碼：JsonUnsupportedHierarchicalComplexValue

- **訊息**：`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>錯誤碼：JsonConflictPartitionDiscoverySchema

- **訊息**：`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>錯誤碼：JsonInvalidDataFormat

- **訊息**：`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>錯誤碼：JsonInvalidDataMixedArrayAndObject

- **訊息**：`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Parquet 格式

### <a name="error-code--parquetjavainvocationexception"></a>錯誤碼：ParquetJavaInvocationException

- **訊息**：`An error occurred when invoking java, message: %javaException;.`

- **原因**︰當錯誤訊息包含 'java.lang.OutOfMemory'、'Java heap space' 和 'doubleCapacity' 時，通常是因為舊版整合執行階段中的記憶體管理問題。

- **建議**：如果您使用自我裝載的 Integration Runtime，且版本早于3.20.7159.1，建議您升級至最新版本。

- **原因**︰當錯誤訊息包含 'java.lang.OutOfMemory' 時，表示整合執行階段沒有足夠的資源可處理檔案。

- **建議**：限制整合執行階段上的並行執行數目。 針對自我裝載整合執行階段，請擴展到記憶體等於或大於 8 GB 的強大機器。

- **原因**︰當錯誤訊息包含 'NullPointerReference' 時，可能是暫時性錯誤。

- **建議**：重試。 若問題持續發生，請連絡支援。


### <a name="error-code--parquetinvalidfile"></a>錯誤碼：ParquetInvalidFile

- **訊息**：`File is not a valid parquet file.`

- **原因**︰Parquet 檔案問題。

- **建議**：請檢查輸入是否為有效的 parquet 檔案。


### <a name="error-code--parquetnotsupportedtype"></a>錯誤碼：ParquetNotSupportedType

- **訊息**：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**︰Azure Data Factory 中不支援 parquet 格式。

- **建議**：再次檢查來源資料。 請參閱文件： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>錯誤碼：ParquetMissedDecimalPrecisionScale

- **訊息**：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**︰嘗試剖析數值的精確度和小數位數，但無法取得這類資訊。

- **建議**：'Source' 未傳回正確的精確度和小數位數。 檢查問題資料行的精確度和小數位數。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>錯誤碼：ParquetInvalidDecimalPrecisionScale

- **訊息**：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**︰結構描述無效。

- **建議**：檢查問題資料行的精確度和小數位數。


### <a name="error-code--parquetcolumnnotfound"></a>錯誤碼：ParquetColumnNotFound

- **訊息**：`Column %column; does not exist in Parquet file.`

- **原因**︰來源結構描述與接收結構描述不相符。

- **建議**：檢查 'activity' 中的 'mappings'。 請確定來源資料行可以對應到正確的接收資料行。


### <a name="error-code--parquetinvaliddataformat"></a>錯誤碼：ParquetInvalidDataFormat

- **訊息**：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**︰無法將資料轉換成 mappings.source 中指定的類型。

- **建議**：請在複製活動資料行對應中，再次檢查來源資料，或為此資料行指定正確的資料類型。 請參閱文件： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>錯誤碼：ParquetDataCountNotMatchColumnCount

- **訊息**：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**︰來源資料行計數和接收資料行計數不符

- **建議**：再次確認來源資料行計數是否與 'mapping' 中的接收資料行計數相同。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>錯誤碼：ParquetDataTypeNotMatchColumnType

- **訊息**：資料類型 %srcType; 不符合指定的資料行 '%columnIndex;'上的資料行類型 %dstType;。

- **原因**︰來源中的資料無法轉換成接收中定義的類型

- **建議**：在對應中指定正確的類型。


### <a name="error-code--parquetbridgeinvaliddata"></a>錯誤碼：ParquetBridgeInvalidData

- **訊息**：`%message;`

- **原因**︰資料值超過限制

- **建議**：重試。 若問題持續發生，請連絡我們。


### <a name="error-code--parquetunsupportedinterpretation"></a>錯誤碼：ParquetUnsupportedInterpretation

- **訊息**：`The given interpretation '%interpretation;' of parquet format is not supported.`

- **原因**︰不支援的支援案例

- **建議**：'ParquetInterpretFor' 不能是 'sparkSql'。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>錯誤碼：ParquetUnsupportFileLevelCompressionOption

- **訊息**：`File level compression is not supported for Parquet.`

- **原因**︰不支援的支援案例

- **建議**：移除承載中的 'CompressionType'。



## <a name="general-copy-activity-error"></a>一般複製活動錯誤

### <a name="error-code--jrenotfound"></a>錯誤碼：JreNotFound

- **訊息**：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**︰自我裝載整合執行階段找不到 Java 執行階段。 需要 Java 執行階段才能讀取特定來源。

- **建議**：檢查您的整合執行階段環境，參考文件： https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>錯誤碼：WildcardPathSinkNotSupported

- **訊息**：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**︰接收資料集不支援萬用字元。

- **建議**：檢查接收資料集，並將路徑修正為不包含萬用字元值。


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>錯誤碼：MappingInvalidPropertyWithEmptyValue

- **訊息**：`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>錯誤碼：MappingInvalidPropertyWithNamePathAndOrdinal

- **訊息**：`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>錯誤碼：MappingDuplicatedOrdinal

- **訊息**：`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>錯誤碼：MappingInvalidOrdinalForSinkColumn

- **訊息**：`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory 的 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
            
