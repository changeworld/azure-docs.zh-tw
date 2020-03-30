---
title: 針對 Azure Data Factory 連接器進行疑難排解
description: 瞭解如何在 Azure 資料工廠中解決連接器問題。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778221"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>針對 Azure Data Factory 連接器進行疑難排解

本文介紹 Azure 資料工廠中連接器的常見故障排除方法。
  

## <a name="azure-blob-storage"></a>Azure Blob 儲存體

### <a name="error-code--azurebloboperationfailed"></a>錯誤代碼：AzureBlob 操作失敗

- **消息**：`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**：Blob 存儲操作命中問題。

- **建議**：詳細檢查錯誤。 請參閱 blob 説明文檔https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes： 。 如果需要幫助，請聯繫存儲團隊。


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>錯誤代碼：AzureBlob 服務不返回預期資料長度

- **消息**：`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>錯誤代碼：AzureBlob 不支援多個檔到單一 Blob

- **消息**：`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>錯誤代碼：Azure 存儲操作失敗併發寫入

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>錯誤訊息：請求大小太大

- **症狀**：將資料複製到具有預設寫入批次處理大小的 Azure Cosmos DB 中，並命中錯誤 *"**請求大小太大***"。

- **原因**：Cosmos DB 將單個請求的大小限制為 2 MB。 公式為，請求大小 = 單個文檔大小 = 寫入批次處理大小。 如果文檔大小較大，則預設行為將導致請求大小過大。 您可以調整寫入批次處理大小。

- **解析度**：在複製活動接收器中，減小"寫入批次處理大小"值（預設值為 10000）。

### <a name="error-message-unique-index-constraint-violation"></a>錯誤訊息：唯一索引約束衝突

- **症狀**： 將資料複製到 Cosmos DB 時，您出現以下錯誤：

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**：有兩個可能的原因：

    - 如果使用**Insert**作為寫入行為，則此錯誤意味著來源資料具有具有相同 ID 的行/物件。

    - 如果使用**Upsert**作為寫入行為，並且為容器設置了另一個唯一鍵，則此錯誤意味著來源資料具有具有不同 ID 但定義的唯鑰相同值的行/物件。

- **解析度**： 

    - 對於原因1，將**Upsert**設置為寫入行為。
    - 對於原因 2，請確保每個文檔對定義的唯一鍵具有不同的值。

### <a name="error-message-request-rate-is-large"></a>錯誤訊息：請求速率較大

- **症狀**： 將資料複製到 Cosmos DB 時，您出現以下錯誤：

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**：使用的請求單位大於 Cosmos DB 中配置的可用 RU。 瞭解宇宙資料庫[如何從這裡](../cosmos-db/request-units.md#request-unit-considerations)計算RU。

- **解決方法**： 這裡有兩個解決方案：

    1. **將容器 RU 增加到**Cosmos DB 中更大的值，這將提高複製活動性能，但在 Cosmos DB 中會產生更多成本。 

    2. 將**writeBatchSize**減少到較小的值（如 1000），並將**並行副本**設置為較小的值（如 1），這將使複製運行性能比當前性能差，但不會在 Cosmos DB 中產生更大的成本。

### <a name="column-missing-in-column-mapping"></a>列映射中缺少列

- **症狀**：當您為 Cosmos DB 導入用於列映射的架構時，某些列將丟失。 

- **原因**：ADF 從前 10 個 Cosmos DB 文檔中推斷出架構。 如果某些列/屬性在這些文檔中沒有值，則 ADF 不會檢測到它們，因此不會顯示它們。

- **解決方法**：您可以將查詢按如下所示進行優化，以強制列在結果集中顯示，結果值為空值：（假設前 10 個文檔中缺少"不可能"列）。 或者，您可以手動添加用於映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>錯誤訊息：讀者的 Guid 代表是 CSharpLegacy

- **症狀**： 使用 UUID 欄位從 Cosmos DB MongoAPI/MongoDB 複製資料時，您點擊了以下錯誤：

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**： 在 BSON 中表示 UUID 的方法有兩種 - UuidStarard 和 UuidLegacy。 預設情況下，UuidLegacy 用於讀取資料。 如果蒙戈DB中的 UUID 資料是 Uuid標準，則您將遭遇錯誤。

- **解析度**：在 MongoDB 連接字串中，添加選項 **"uuid表示\標準**"。 有關詳細資訊，請參閱[MongoDB 連接字串](connector-mongodb.md#linked-service-properties)。
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>錯誤代碼：AdlsGen2操作失敗

- **消息**：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**： ADLS Gen2 引發錯誤指示操作失敗。

- **建議**：檢查 ADLS Gen2 引發的詳細錯誤訊息。 如果是由瞬態故障引起的，請重試。 如果您需要進一步説明，請聯繫 Azure 存儲支援，並在錯誤訊息中提供請求 ID。

- **原因**：當錯誤訊息包含"禁止"時，您使用的服務主體或託管標識可能沒有足夠的許可權來訪問 ADLS Gen2。

- **建議**： 請參閱説明文檔： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **原因**：當錯誤訊息包含"內部伺服器錯誤"時，錯誤由 ADLS Gen2 返回。

- **建議**：可能是由暫時性故障引起的，請重試。 如果問題仍然存在，請聯繫 Azure 存儲支援，並在錯誤訊息中提供請求 ID。


### <a name="error-code--adlsgen2invalidurl"></a>錯誤代碼： AdlsGen2 無效URL

- **消息**：`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>錯誤代碼： AdlsGen2 無效資料夾路徑

- **消息**：`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>錯誤代碼：AdlsGen2操作失敗併發寫入

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>錯誤代碼：AdlsGen2逾時錯誤

- **消息**：`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>錯誤訊息：遠端伺服器返回錯誤： （403） 禁止

- **症狀**： 複製活動失敗，出現以下錯誤： 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**：一個可能的原因是您使用的服務主體或託管標識沒有訪問特定資料夾/檔的許可權。

- **解決方法**：對需要複製的所有資料夾和子資料夾授予相應的許可權。 請參閱[此文檔](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>錯誤訊息：無法使用服務主體獲取訪問權杖。 ADAL 錯誤：service_unavailable

- **症狀**： 複製活動失敗，出現以下錯誤：

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**：當 Azure 活動目錄擁有的服務權杖伺服器 （STS） 不可用，即忙得無法處理請求時，它將返回 HTTP 錯誤 503。 

- **解決方法**：幾分鐘後重新運行複製活動。
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL 資料倉儲/Azure SQL 資料庫/SQL 伺服器

### <a name="error-code--sqlfailedtoconnect"></a>錯誤代碼：SqlFailed 連接到

- **消息**：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**：如果錯誤訊息包含"SqlException"，SQL 資料庫將引發錯誤，指示某些特定操作失敗。

- **建議**：請在此參考文檔中按 SQL 錯誤代碼搜索更多詳細資訊： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors。 如果您需要其他説明，請與 Azure SQL 支援部門聯繫。

- **原因**：如果錯誤訊息包含"具有 IP 位址的用戶端"...'不允許訪問伺服器"，並且您嘗試連接到 Azure SQL 資料庫，通常是由 Azure SQL 資料庫防火牆問題引起的。

- **建議**：在 Azure SQL Server 防火牆配置中，啟用"允許 Azure 服務和資源訪問此伺服器"選項。 參考文檔： https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>錯誤代碼：Sql操作失敗

- **消息**：`A database operation failed. Please search error to get more details.`

- **原因**：如果錯誤訊息包含"SqlException"，SQL 資料庫將引發錯誤，指示某些特定操作失敗。

- **建議**：如果 SQL 錯誤不明確，請嘗試將資料庫更改為最新的相容性級別"150"。 它可以引發最新版本的 SQL 錯誤。 請參閱詳細資訊文檔： https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat。
        有關解決 SQL 問題的疑難排解，請在此參考文檔中搜索 SQL 錯誤代碼https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors以瞭解更多詳細資訊： 。 如果您需要其他説明，請與 Azure SQL 支援部門聯繫。

- **原因**：如果錯誤訊息包含"Pdw管理到本機InteropException"，通常是由於源列和接收器列大小不匹配造成的。

- **建議**：檢查源列和接收器列的大小。 如果您需要其他説明，請與 Azure SQL 支援部門聯繫。

- **原因**：如果錯誤訊息包含"無效操作異常"，則通常是由無效輸入資料引起的。

- **建議**：要確定遇到問題的行，請在複製活動上啟用容錯功能，該功能可以將有問題的行重定向到存儲以進行進一步調查。 參考文檔： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>錯誤代碼：Sql 未經授權訪問

- **消息**：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：憑據不正確或登錄帳戶無法訪問 SQL 資料庫。

- **建議**：檢查登錄帳戶是否有足夠的許可權訪問 SQL 資料庫。


### <a name="error-code--sqlopenconnectiontimeout"></a>錯誤代碼：SqlOpen連接逾時

- **消息**：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**：可能是 SQL 資料庫瞬態故障。

- **建議**：請重試更新具有較大連接逾時值的連結服務連接字串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>錯誤代碼：SqlAutocreateTable 類型映射失敗

- **消息**：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**：自動創建表無法滿足源要求。

- **建議**：在"映射"中更新列類型，或在目標伺服器中手動創建接收器表。


### <a name="error-code--sqldatatypenotsupported"></a>錯誤代碼：不支援 SqlDataType

- **消息**：`A database operation failed. Check the SQL errors.`

- **原因**：如果問題發生在 SQL 源上，並且錯誤與 SqlDateTime 溢出有關，則資料值超過邏輯類型範圍（1/1/1753 12：00：00 AM - 12/31/9999 11：59：59 PM）。

- **建議**：在源 SQL 查詢或複製活動列映射中將類型轉換為字串，將列類型更改為"String"。

- **原因**：如果問題發生在 SQL 接收器上，並且錯誤與 SqlDateTime 溢出有關，則資料值超過接收器表中允許的範圍。

- **建議**：將相應的列類型更新為接收器表中的"datetime2"類型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>錯誤代碼：SqlInvalidDb預存程序

- **消息**：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的預存程序無效。 這可能是由於預存程序不返回任何資料造成的。

- **建議**：通過 SQL 工具驗證預存程序。 確保預存程序可以返回資料。


### <a name="error-code--sqlinvaliddbquerystring"></a>錯誤代碼：SqlInvalidDb查詢字串

- **消息**：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查詢無效。 這可能是由於查詢不返回任何資料造成的

- **建議**：按 SQL 工具驗證 SQL 查詢。 確保查詢可以返回資料。


### <a name="error-code--sqlinvalidcolumnname"></a>錯誤代碼：SqlInvalidcolumn 名稱

- **消息**：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：找不到列。 可能的配置錯誤。

- **建議**：驗證查詢中的列、資料集中的"結構"和活動中的"映射"。


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>錯誤代碼：SqlColumnName不匹配通過區分大小寫

- **消息**：`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>錯誤代碼：SqlBatchWriteTimetimeout

- **消息**：`Timeouts in SQL write operation.`

- **原因**：可能是 SQL 資料庫瞬態故障。

- **建議**：請重試。 如果重新提供問題，請與 Azure SQL 支援部門聯繫。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>錯誤代碼：SqlBatchWrite 交易失敗

- **消息**：`SQL transaction commits failed`

- **原因**：如果異常詳細資訊不斷告訴事務超時，則集成運行時和資料庫之間的網路延遲高於預設閾值 30 秒。

- **建議**：更新 Sql 連結的服務連接字串，其"連接逾時"值等於 120 或更高，然後重新運行活動。

- **原因**：如果異常詳細資訊間歇性地告訴 sql 連接斷開，則可能只是暫時性網路故障或 SQL 資料庫端問題

- **建議**：請重試活動並查看 SQL 資料庫端指標。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>錯誤代碼：SqlBulkCopy 無效列長度

- **消息**：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**：由於從 bcp 用戶端接收無效列長度，SQL 批量複製失敗。

- **建議**：要確定遇到問題的行，請在複製活動上啟用容錯功能，該功能可以將有問題的行重定向到存儲以進行進一步調查。 參考文檔： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>錯誤代碼：SqlConnection 已關閉

- **消息**：`The connection is closed by SQL Database.`

- **原因**：當高併發運行和伺服器終止連接時，SQL 資料庫將關閉 SQL 連接。

- **建議**：遠端伺服器關閉了 SQL 連接。 請再試一次。 如果重新提供問題，請與 Azure SQL 支援部門聯繫。


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>錯誤代碼：SqlcreateTable 失敗不支援的類型

- **消息**：`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>錯誤訊息：從字串轉換為唯一識別碼時轉換失敗

- **症狀**：當您使用暫存副本和 PolyBase 將資料從表格資料來源（如 SQL Server）複製到 Azure SQL 資料倉儲時，您會遭遇以下錯誤：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**：Azure SQL 資料倉儲庫基礎無法將空字串轉換為 GUID。

- **解析度**：在"複製活動接收器"中，在"多邊基"設置下，將"**使用類型預設值**"選項設置為 false。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>錯誤訊息：預期資料類型：DECIMAL（x，x），違規值

- **症狀**：當您使用暫存副本和 PolyBase 將資料從表格資料來源（如 SQL Server）複製到 SQL DW 時，您會遭遇以下錯誤：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**：Azure SQL 資料倉儲庫基礎無法將空字串（空值）插入到十進位列中。

- **解析度**：在"複製活動接收器"中，在"多邊基"設置下，將"**使用類型預設值**"選項設置為 false。

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>錯誤訊息：JAVA 異常消息：HdfsBridge：：創建記錄閱讀器

- **症狀**：使用 PolyBase 將資料複製到 Azure SQL 資料倉儲，並點擊以下錯誤：

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**：可能的原因是架構（總列寬度）過大（大於 1 MB）。 通過添加所有列的大小來檢查目標 SQL DW 表的架構：

    - int -> 4 位元組
    - 比吉特 -> 8 位元組
    - 瓦爾查爾（n），字元（n），二進位（n），Varbinary（n） -> n 位元組
    - 恩瓦爾查爾（n），Nchar（n） -> n_2 位元組
    - 日期 -> 6 位元組
    - 日期時間/（2），小日期時間 -> 16 位元組
    - 日期時間偏移 -> 20 位元組
    - 十進位 -> 19 位元組
    - 浮動 -> 8 位元組
    - 貨幣 -> 8 位元組
    - 小錢 - > 4 位元組
    - 真實 -> 4 位元組
    - 小位元組 - > 2 位元組
    - 時間 -> 12 位元組
    - 小巧 -> 1 位元組

- **解析度**：將列寬度減小到小於 1 MB

- 或者通過禁用多邊形基使用批量插入方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>錯誤訊息：未滿足使用 HTTP 條件標頭指定的條件

- **症狀**：使用 SQL 查詢從 Azure SQL 資料倉儲中提取資料，並命中以下錯誤：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**：Azure SQL 資料倉儲在 Azure 存儲中查詢外部表時出現問題。

- **解決方法**：在 SSMS 中運行相同的查詢，並檢查是否看到相同的結果。 若是如此，請開啟 Azure SQL 資料倉儲的支援票證，並提供您的 SQL DW 伺服器和資料庫名稱，進一步進行疑難排解。
            

## <a name="delimited-text-format"></a>分隔的文字格式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>錯誤代碼：分隔文本列名不允許無效

- **消息**：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**：在活動中設置"第一行"時，第一行將用作列名。 此錯誤表示第一行包含空值。 例如："列A，列B"。

- **建議**：檢查第一行，並在有空值時修復該值。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>錯誤代碼：分隔文本更多列符，已定義

- **消息**：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **原因**：有問題的行的列計數大於第一行的列計數。 這可能是由資料問題或不正確的列分隔符號/報價字元設置引起的。

- **建議**：請在錯誤訊息中獲取行計數，檢查行的列並修復資料。

- **原因**：如果錯誤訊息中的預期列計數為"1"，則可能是您指定的壓縮或格式設置錯誤，從而導致 ADF 錯誤地分析檔。

- **建議**：檢查格式設置，以確保它與原始檔案匹配。

- **原因**：如果源是資料夾，則指定資料夾下的檔可能具有不同的架構。

- **建議**：確保給定資料夾下的檔具有相同的架構。


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>錯誤代碼：分隔文本不正確的行限制器

- **消息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>錯誤代碼：分隔文本太大列計數

- **消息**：`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>錯誤代碼：分隔文本無效設置

- **消息**：`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>動態 365/通用資料服務/動態 CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>錯誤代碼：動態創建服務用戶端錯誤

- **消息**：`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因**：這是動態伺服器端的暫時性問題。

- **建議**：重新運行管道。 如果繼續失敗，請嘗試降低並行性。 如果仍然失敗，請聯繫動態支援。



## <a name="json-format"></a>JSON 格式

### <a name="error-code--jsoninvalidarraypathdefinition"></a>錯誤代碼：Json 無效陣列路徑定義

- **消息**：`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>錯誤代碼：JsonEmptyJObjectData

- **消息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>錯誤代碼：JsonNullValueInPath定義

- **消息**：`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>錯誤代碼：Json 不支援的分層複雜值

- **消息**：`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>錯誤代碼：Json衝突分區資料架構

- **消息**：`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>錯誤代碼：Json 無效資料格式

- **消息**：`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>錯誤代碼： Json 無效資料混合陣列和物件

- **消息**：`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>鑲木地板格式

### <a name="error-code--parquetjavainvocationexception"></a>錯誤代碼：ParquetJAVAin調用異常

- **消息**：`An error occurred when invoking java, message: %javaException;.`

- **原因**：當錯誤訊息包含"java.lang.OutMemory"、"JAVA 堆空間"和"雙容量"時，通常是舊版本的集成運行時的記憶體管理問題。

- **建議**：如果您使用的是自託管集成運行時，並且版本早于 3.20.7159.1，建議升級到最新版本。

- **原因**：當錯誤訊息包含"java.lang.OutMemory"時，集成運行時沒有足夠的資源來處理檔。

- **建議**：限制集成運行時的併發運行。 對於自託管集成運行時，可擴展到記憶體等於或大於 8 GB 的強大電腦。

- **原因**：當錯誤訊息包含"NullPointer 參考"時，可能是暫時性錯誤。

- **建議**：請重試。 如果問題仍然存在，請聯繫支援人員。


### <a name="error-code--parquetinvalidfile"></a>錯誤代碼：Parquet 無效檔

- **消息**：`File is not a valid parquet file.`

- **原因**：鑲木地板檔問題。

- **建議**：檢查輸入是有效的鑲木地板檔。


### <a name="error-code--parquetnotsupportedtype"></a>錯誤代碼：Parquet 不支援類型

- **消息**：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**：Azure 資料工廠不支援鑲木地板格式。

- **建議**：仔細檢查來源資料。 請參閱文檔： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>錯誤代碼：鑲木地板十進位精度刻度

- **消息**：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**：嘗試分析數位精度和比例，但沒有提供此類資訊。

- **建議**： "源"不返回正確的精度和比例。 檢查問題列精度和比例。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>錯誤代碼：鑲木地板無效十進位精度刻度

- **消息**：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**：架構無效。

- **建議**：檢查問題列精度和比例。


### <a name="error-code--parquetcolumnnotfound"></a>錯誤代碼： ParquetColumn 未找到

- **消息**：`Column %column; does not exist in Parquet file.`

- **原因**：源架構與接收器架構不匹配。

- **建議**：檢查"活動"中的"映射"。 確保源列可以映射到右接收器列。


### <a name="error-code--parquetinvaliddataformat"></a>錯誤代碼：Parquet 無效資料格式

- **消息**：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**：資料無法轉換為映射中指定的類型。

- **建議**：在複製活動列映射中仔細檢查來源資料或指定此列的正確資料類型。 請參閱文檔： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>錯誤代碼：ParquetDataCountNotMatchColumnCount

- **消息**：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**：源列計數和接收器列計數不匹配

- **建議**：雙檢查源列計數與"映射"中的接收器列計數相同。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>錯誤代碼：鑲木地板類型不匹配列型

- **消息**： 資料類型 %srcType;與給定列類型 %dstType 不匹配;在列"%列索引;"。

- **原因**：來源資料無法轉換為在接收器中定義的類型化資料

- **建議**：請在映射.sink 中指定正確的類型。


### <a name="error-code--parquetbridgeinvaliddata"></a>錯誤代碼：鑲木地板無效資料

- **消息**：`%message;`

- **原因**： 資料值超過限制

- **建議**：請重試。 如果問題仍然存在，請聯繫我們。


### <a name="error-code--parquetunsupportedinterpretation"></a>錯誤代碼：無支援的"鑲木地板"

- **消息**：`The given interpretation '%interpretation;' of parquet format is not supported.`

- **原因**： 不支援方案

- **建議**：'參數解釋為'不應該是'火花Sql'。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>錯誤代碼：Parquet 不支援檔級別壓縮選項

- **消息**：`File level compression is not supported for Parquet.`

- **原因**： 不支援方案

- **建議**：刪除有效負載中的"壓縮類型"。



## <a name="general-copy-activity-error"></a>常規複製活動錯誤

### <a name="error-code--jrenotfound"></a>錯誤代碼： JreNotFound

- **消息**：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**：自託管的集成運行時找不到 JAVA 運行時。 讀取特定源需要 JAVA 運行時。

- **建議**：檢查集成運行時環境，參考文檔：https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>錯誤代碼：不支援萬用字元路徑

- **消息**：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**：接收器資料集不支援萬用字元。

- **建議**：檢查接收器資料集並修復沒有萬用字元值的路徑。


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>錯誤代碼：映射無效屬性與空值

- **消息**：`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>錯誤代碼：映射無效屬性與名稱路徑和 ordinal

- **消息**：`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>錯誤代碼：映射重複性Ordinal

- **消息**：`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>錯誤代碼：映射不正確"無效"，用於查找問題柱

- **消息**：`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>後續步驟

有關更多故障排除説明，請嘗試以下資源：

*  [資料工廠博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [資料工廠功能請求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 視頻](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [資料工廠的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有關資料工廠的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
            
