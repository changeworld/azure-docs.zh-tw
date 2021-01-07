---
title: 針對 Azure Data Factory 連接器進行疑難排解
description: 了解如何針對 Azure Data Factory 中的連接器問題進行疑難排解。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 68547b8fb673cd54b7c21963ede122553bbbc390
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967118"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>針對 Azure Data Factory 連接器進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將探討 Azure Data Factory 中連接器的常見疑難排解方法。
  
## <a name="azure-blob-storage"></a>Azure Blob 儲存體

### <a name="error-code-azurebloboperationfailed"></a>錯誤碼： AzureBlobOperationFailed

- **訊息**：`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**︰Blob 儲存體作業遇到問題。

- **建議**：請檢查錯誤詳細資料。 請參閱 Blob 說明文件： https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 。 如需協助，請連絡儲存體小組。


### <a name="invalid-property-during-copy-activity"></a>複製活動期間的屬性無效

- **訊息**：`Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **原因**：資料集中定義的類型與複製活動中定義的來源/接收類型不一致。

- **解決方法**：編輯資料集或管線 JSON 定義，使類型保持一致，然後重新執行部署。


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

    - **將容器 RU 增加** 為 Cosmos DB 中較大的值，這會改善複製活動效能，不過會在 Cosmos DB 中產生更多成本。 
    - 將 **writeBatchSize** 減少為較小的值 (例如 1000)，並將 **parallelCopies** 設定為較小的值 (例如 1)，這會使複製執行效能變得比目前更糟，但不會在 Cosmos DB 中產生更多成本。

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
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API) 

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>錯誤碼： CosmosDbSqlApiOperationFailed

- **訊息**：`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**： CosmosDbSqlApi 操作遇到問題。

- **建議**：請檢查錯誤詳細資料。 請參閱 [CosmosDb 說明文件](https://docs.microsoft.com/azure/cosmos-db/troubleshoot-dot-net-sdk)。 如需協助，請聯絡 CosmosDb 團隊。


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>錯誤訊息：基礎連接已關閉：無法為 SSL/TLS 安全通道建立信任關係。

- **徵兆**：複製活動失敗，並出現下列錯誤： 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
    ```

- **原因**： TLS 信號交換期間憑證驗證失敗。

- **解決** 方法：因應措施：使用分段複製來略過 ADLS GEN1 的 TLS 驗證。 您需要重現此問題並收集 netmon 追蹤，然後讓您的網路小組檢查區域網路設定。

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


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>錯誤碼： ADLSGen2OperationFailed

- **訊息**：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**︰ADLS Gen2 擲回表示作業失敗的錯誤。

- **建議**：檢查 ADLS Gen2 擲回的詳細錯誤訊息。 如果是暫時性失敗所造成，請再試一次。 如果您需要進一步協助，請連絡 Azure 儲存體支援服務，並提供錯誤訊息中的要求識別碼。

- **原因**︰當錯誤訊息包含「禁止」時，表示您所使用的服務主體或受控識別可能沒有足夠的權限可存取 ADLS Gen2。

- **建議**：請參閱說明文件： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 。

- **原因**︰當錯誤訊息包含 'InternalServerError' 時，ADLS Gen2 會傳回錯誤。

- **建議**：這可能是因為暫時性失敗所造成，請再試一次。 如果問題持續發生，請連絡 Azure 儲存體支援服務，並提供錯誤訊息中的要求識別碼。

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>要求 ADLS Gen2 帳戶的逾時錯誤

- **Message**：錯誤碼 = `UserErrorFailedBlobFSOperation` ，錯誤訊息 = `BlobFS operation failed for: A task was canceled` 。

- **原因**：此問題是因為在自我裝載的 IR 機器上發生 ADLS Gen2 接收逾時錯誤所造成。

- **建議**： 

    - 盡可能將自我裝載 IR 電腦和目標 ADLS Gen2 帳戶放在相同的區域中。 這可以避免隨機的逾時錯誤，並提供更好的效能。

    - 檢查是否有任何特殊的網路設定，例如 ExpressRoute，並確定網路具有足夠的頻寬。 當整體頻寬低時，建議您降低自我裝載的 IR 並行作業設定，如此可避免跨多個並行作業的網路資源競爭。

    - 如果檔案大小為「中」或「小」，則針對非二進位複本使用較小的區塊大小，以減少這類逾時錯誤。 請參閱 [Blob 儲存體 Put 區塊](https://docs.microsoft.com/rest/api/storageservices/put-block)。

       若要指定自訂區塊大小，您可以在 [json 編輯器] 中編輯屬性：
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Azure 檔案儲存體

### <a name="error-code--azurefileoperationfailed"></a>錯誤碼： AzureFileOperationFailed

- **訊息**：`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **原因**： Azure 檔案儲存體操作遇到問題。

- **建議**：請檢查錯誤詳細資料。 請參閱 Azure 檔案說明文件： https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes 。 如果您需要協助，請洽詢儲存體小組。


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>錯誤碼：SqlFailedToConnect

- **訊息**：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**： Azure SQL：如果錯誤訊息包含 "SqlErrorNumber = 47073"，表示連線設定中的公用網路存取遭到拒絕。

- **建議**：在 Azure SQL 防火牆上，將 [拒絕公用網路存取] 選項設定為 [否]。 深入瞭解 https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access 。

- **原因**： azure Sql：如果錯誤訊息包含 SQL 錯誤碼（例如 "SqlErrorNumber = [errorcode]"），請參閱 Azure sql 疑難排解指南。

- **建議**：深入瞭解 https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues 。

- **原因**：檢查埠1433是否在防火牆允許清單中。

- **建議**：遵循下列參考檔： https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- 。

- **原因**︰如果錯誤訊息包含 "SqlException"，SQL Database 會擲回錯誤，指出某些特定的作業失敗。

- **建議**：如需詳細資料，請依下列參考檔中的 SQL 錯誤碼搜尋： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如果您需要進一步的協助，請連絡 Azure SQL 支援服務。

- **原因**：如果這是暫時性問題 (例如，instable 網路連線) ，請在活動原則中新增重試以減輕風險。

- **建議**：遵循此參考檔： https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy 。

- **原因**︰如果錯誤訊息包含「具有 IP 位址 '...' 的用戶端不能存取伺服器」，而且您正嘗試連接線到 Azure SQL Database，這通常是由 Azure SQL Database 防火牆問題所造成。

- **建議**：在 Azure SQL Server 防火牆設定中，請啟用 [允許 Azure 服務和資源存取此伺服器] 選項。 參考文件： https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure 。


### <a name="error-code--sqloperationfailed"></a>錯誤碼：SqlOperationFailed

- **訊息**：`A database operation failed. Please search error to get more details.`

- **原因**︰如果錯誤訊息包含 "SqlException"，SQL Database 會擲回錯誤，指出某些特定的作業失敗。

- **建議**：如果 SQL 錯誤不清楚，請嘗試將資料庫變更為最新的相容性層級 '150'。 如此可能會擲回最新版本的 SQL 錯誤。 請參閱 [詳細資料](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)檔。

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

- **解決方案**： 
    - 將資料行寬度縮減為小於 1 MB。
    - 或藉由停用 Polybase 來使用大量插入方法。


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>錯誤訊息：使用 HTTP 條件式標頭的指定條件不符

- **徵兆**：您可以使用 SQL 查詢從 Azure Synapse Analytics 中提取資料，並遇到下列錯誤：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**：查詢 Azure 儲存體中的外部資料表時，Azure Synapse Analytics 遇到問題。

- **解決方法**：在 SSMS 中執行相同的查詢，並檢查您是否看到相同的結果。 如果是，請開啟支援票證來 Azure Synapse Analytics，並提供您的 Azure Synapse Analytics 伺服器和資料庫名稱，以進行進一步的疑難排解。


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>效能層級很低，導致複製失敗

- **徵兆**：將資料複製到 Azure SQL Database 時發生錯誤訊息： `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**：正在使用 Azure SQL Database s1，這種情況下會達到 IO 限制。

- **解決方法**：升級 Azure SQL Database 效能層級以修正問題。 


### <a name="sql-table-cannot-be-found"></a>找不到 SQL 資料表 

- **徵兆**：將資料從混合式複製到內部內部部署 SQL Server 資料表時發生錯誤：`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**：目前的 SQL 帳戶沒有足夠的許可權可執行 .net SqlBulkCopy 所發出的要求 WriteToServer。

- **解決** 方式：切換至更具特殊許可權的 SQL 帳戶。


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>錯誤訊息：字串或二進位資料會被截斷

- **徵兆**：將資料複製到內部內部部署/Azure SQL Server 資料表時發生錯誤： 

- **原因**： Cx Sql 資料表架構定義有一或多個資料行的長度少於預期。

- **解決** 方式：請嘗試下列步驟來修正此問題：

    1. 套用 SQL 接收 [容錯](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)，特別是「redirectIncompatibleRowSettings」，以針對哪些資料列有問題進行疑難排解。

        > [!NOTE]
        > 請注意，容錯可能會導致額外的執行時間，這可能會導致更高的成本。

    2. 使用 SQL 資料表架構資料行長度再次檢查重新導向的資料，以查看需要更新)  (的資料行。

    3. 據以更新資料表架構。


## <a name="azure-table-storage"></a>Azure 表格儲存體

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>錯誤碼： AzureTableDuplicateColumnsFromSource

- **訊息**：`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **原因**： SQL 查詢與聯結或非結構化 csv 檔案可能很常見

- **建議**：再次檢查來源資料行，並據以修正。


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>錯誤碼： DB2DriverRunFailed

- **訊息**：`Error thrown from driver. Sql code: '%code;'`

- **原因**：如果錯誤訊息包含 "SQLSTATE = 51002 SQLCODE =-805"，請參閱本檔中的秘訣： https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties

- **建議**：嘗試設定 "packageCollection" 屬性中的 "NullID"


## <a name="delimited-text-format"></a>分隔符號文字格式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>錯誤碼：DelimitedTextColumnNameNotAllowNull

- **訊息**：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**︰在活動中設定 'firstRowAsHeader' 時，第一個資料列會用來作為資料行名稱。 此錯誤表示第一個資料列包含空的值。 例如： ' ColumnA，ColumnB '。

- **建議**：檢查第一個資料列，如果有空白值，請修正此值。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>錯誤碼：DelimitedTextMoreColumnsThanDefined

- **訊息**：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因**：有問題的資料列資料行計數大於第一個資料列的資料行計數。 這可能是因為資料問題或資料行分隔符號/引號字元設定不正確所造成。

- **建議**：取得錯誤訊息中的資料列計數，檢查資料列的資料行並修正資料。

- **原因**：如果預期的資料行計數在錯誤訊息中為 "1"，可能是您指定了錯誤的壓縮或格式設定。 因此 ADF 剖析您的檔案 (s) 不正確。

- **建議**：請檢查格式設定，以確定其符合您的來源檔案。

- **原因**︰如果您的來源是資料夾，則指定資料夾下的檔案可能有不同的結構描述。

- **建議**：請確定指定資料夾下的檔案具有相同結構描述。


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>錯誤碼：DynamicsCreateServiceClientError

- **訊息**：`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因**︰這是 Dynamics 伺服器端的暫時性問題。

- **建議**：重新執行管線。 如果持續失敗，請嘗試減少平行處理原則。 如果仍然失敗，請連絡 Dynamics 支援服務。


### <a name="columns-are-missing-when-previewingimporting-schema"></a>預覽/匯入架構時遺漏資料行

- **徵兆**：匯入架構或預覽資料時，某些資料行將會遺失。 錯誤訊息： `The valid structure information (column name and type) are required for Dynamics source.`

- **原因**：此問題基本上是設計的，因為 ADF 無法顯示前10筆記錄中沒有任何值的資料行。 請確定您新增的資料行格式正確。 

- **建議**：在 [對應] 索引標籤中手動加入資料行。


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>錯誤碼： DynamicsMissingTargetForMultiTargetLookupField

- **訊息**：`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**：目標資料行不存在於來源或資料行對應中。

- **建議**：1。 請確定來源包含目標資料行。 2. 在資料行對應中新增目標資料行。 請確認接收器資料行的模式為 "{fieldName} @EntityReference "。


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>錯誤碼： DynamicsInvalidTargetForMultiTargetLookupField

- **訊息**：`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **原因**：提供了錯誤的機構名稱做為多重目標查閱欄位的目標實體。

- **建議**：為多重目標查閱欄位提供有效的機構名稱。


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>錯誤碼： DynamicsInvalidTypeForMultiTargetLookupField

- **訊息**：`The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**：目標資料行中的值不是字串

- **建議**：在多重目標查閱目標資料行中提供有效的字串。


### <a name="error-code--dynamicsfailedtorequetserver"></a>錯誤碼： DynamicsFailedToRequetServer

- **訊息**：`The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **原因**： dynamics server instable 或無法存取，或網路發生問題。

- **建議**：檢查網路連接或查看 dynamics server 記錄檔以取得詳細資料。 請聯絡 dynamics 支援以取得進一步協助。
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>錯誤碼： FtpFailedToConnectToFtpServer

- **訊息**：`Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **原因**：可能會對 ftp 伺服器使用不正確的連結服務類型，例如使用 SFTP 連結服務連接到 ftp 伺服器。

- **建議**：檢查目標伺服器的埠。 FTP 預設會使用埠21。


## <a name="http"></a>Http

### <a name="error-code--httpfilefailedtoread"></a>錯誤碼： HttpFileFailedToRead

- **訊息**：`Failed to read data from http server. Check the error from http server：%message;`

- **原因**：當 Azure Data Factory 與 HTTP 伺服器通訊，但 HTTP 要求作業失敗時，就會發生此錯誤。

- **建議**：檢查 HTTP 狀態碼訊息中的錯誤訊息，並修正遠端伺服器問題。


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>錯誤碼： ArgumentOutOfRangeException

- **訊息**：`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**：在 ADF 中，從 0001-01-01 00:00:00 到 9999-12-31 23:59:59 的範圍支援日期時間值。 不過，Oracle 支援較大範圍的 DateTime 值 (例如 BC 世紀或 min/sec>59) （導致 ADF 失敗）。

- **建議**： 

    執行 `select dump(<column name>)` 以檢查 Oracle 中的值是否在 ADF 的範圍內。 

    如果您想要知道結果中的位元組序列，請檢查 https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle 。


## <a name="orc-format"></a>Orc 格式

### <a name="error-code--orcjavainvocationexception"></a>錯誤碼： OrcJAVAInvocationException

- **訊息**：`An error occurred when invoking java, message: %javaException;.`

- **原因**︰當錯誤訊息包含 'java.lang.OutOfMemory'、'Java heap space' 和 'doubleCapacity' 時，通常是因為舊版整合執行階段中的記憶體管理問題。

- **建議**：如果您使用自我裝載的 Integration Runtime，建議您升級至最新版本。

- **原因**︰當錯誤訊息包含 'java.lang.OutOfMemory' 時，表示整合執行階段沒有足夠的資源可處理檔案。

- **建議**：限制整合執行階段上的並行執行數目。 針對自我裝載整合執行階段，請擴展到記憶體等於或大於 8 GB 的強大機器。

- **原因**︰當錯誤訊息包含 'NullPointerReference' 時，可能是暫時性錯誤。

- **建議**：重試。 若問題持續發生，請連絡支援。

- **原因**：當錯誤訊息包含 ' BufferOverflowException ' 時，可能是暫時性錯誤。

- **建議**：重試。 若問題持續發生，請連絡支援。

- **原因**：當錯誤訊息中包含 "發生 classcastexception:org. org.apache.hadoop.hive.serde2.columnar.lazybinarycolumnarserde............. HiveCharWritable 通常，它是由在 JAVA 執行時間中無法妥善處理來源資料所造成。

- **建議**：此為數據問題。 請嘗試使用字串，而不是 orc 格式資料的 char/Varchar。

### <a name="error-code--orcdatetimeexceedlimit"></a>錯誤碼： OrcDateTimeExceedLimit

- **訊息**：`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**：如果日期時間值為 ' 0001-01-01 00:00:00 '，可能是因為 Julian 行事曆和西曆之間的差異所造成。 https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **建議**：檢查刻度值，並避免使用日期時間值 ' 0001-01-01 00:00:00 '。


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

- **訊息**：`File is not a valid Parquet file.`

- **原因**︰Parquet 檔案問題。

- **建議**：檢查輸入是否為有效的 Parquet 檔。


### <a name="error-code--parquetnotsupportedtype"></a>錯誤碼：ParquetNotSupportedType

- **訊息**：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**： Azure Data Factory 中不支援 Parquet 格式。

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

- **訊息**：`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因**︰不支援的支援案例

- **建議**：'ParquetInterpretFor' 不能是 'sparkSql'。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>錯誤碼：ParquetUnsupportFileLevelCompressionOption

- **訊息**：`File level compression is not supported for Parquet.`

- **原因**︰不支援的支援案例

- **建議**：移除承載中的 'CompressionType'。


### <a name="error-code--usererrorjniexception"></a>錯誤碼： UserErrorJniException

- **訊息**：`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**：無法建立 JVM，因為某些不合法的 (全域) 引數已設定。

- **建議**：登入裝載自我裝載 IR 之 **每個節點** 的電腦。 檢查系統變數是否已正確設定，如下所示： `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` 。 重新開機所有 IR 節點，然後重新執行管線。


### <a name="arithmetic-overflow"></a>算術溢位

- **徵兆**：複製 Parquet 檔案時發生錯誤訊息： `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**：從 Oracle 複製檔案至 Parquet 時，目前只支援整數部分的精確度 <= 38 和整數部分的長度 <= 20。 

- **解決** 方式：您可以將具有這類問題的資料行轉換成 VARCHAR2，以作為因應措施。


### <a name="no-enum-constant"></a>無列舉常數

- **徵兆**：將資料複製到 Parquet 格式時發生錯誤訊息： `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 或： `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` 。

- **原因**： 

    問題可能是因為空白字元或不支援的字元 (例如， {} ( # A2\n\t =) 在資料行名稱中，因為 Parquet 不支援這種格式。 

    例如， *contoso (測試)* 的資料行名稱將會從程式 [代碼](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java)剖析括弧中的型別 `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` 。 因為沒有這類「測試」類型，所以會引發此錯誤。

    若要檢查支援的類型，您可以在 [這裡](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)查看。

- **解決方案**： 

    - 請再次檢查接收器資料行名稱中是否有空白字元。

    - 再次檢查具有空白字元的第一個資料列是否當做資料行名稱使用。

    - 再次檢查支援的類型 OriginalType。 請嘗試避免這些特殊 `,;{}()\n\t=` 的符號。 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>錯誤碼： RestSinkCallFailed

- **訊息**：`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**：當 Azure Data Factory 透過 HTTP 通訊協定與 Rest 端點交談，且要求作業失敗時，就會發生此錯誤。

- **建議**：檢查 HTTP 狀態碼訊息中的錯誤訊息，並修正遠端伺服器問題。

### <a name="unexpected-network-response-from-rest-connector"></a>來自 REST 連接器的非預期網路回應

- **徵兆**：端點有時候會收到非預期的回應 (400/401/403/500) 來自 REST 連接器。

- **原因**：當您在建立 HTTP 要求時，REST 來源連接器會使用連結服務/資料集/複製來源的 URL 和 HTTP 方法/標頭/主體作為參數。 問題很可能是因為一個或多個指定參數中的一些錯誤所造成。

- **解決方案**： 
    - 在 cmd 視窗中使用「捲曲」以檢查參數是否為原因或不是 (**接受** ，而且應該一律包含 **使用者代理程式** 標頭) ：
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      如果命令傳回相同的非預期回應，請使用 ' 捲曲 ' 修正上述參數，直到它傳回預期的回應為止。 

      此外，您也可以使用 ' 捲曲--help ' 來更先進地使用命令。

    - 如果只有 ADF REST 連接器傳回非預期的回應，請洽詢 Microsoft 支援服務，以取得進一步的疑難排解。
    
    - 請注意，「捲曲」可能不適合用來重現 SSL 憑證驗證問題。 在某些情況下，已順利執行 ' 捲曲 ' 命令，而不會發生任何 SSL 憑證驗證問題。 但在瀏覽器中執行相同的 URL 時，不會實際傳回任何 SSL 憑證來讓用戶端建立與伺服器的信任。

      針對上述案例，建議使用 **Postman** 和 **Fiddler** 等工具。


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>錯誤碼： SftpOperationFail

- **訊息**：`Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**： Sftp 操作遇到問題。

- **建議**：檢查 SFTP 的詳細錯誤。


### <a name="error-code--sftprenameoperationfail"></a>錯誤碼： SftpRenameOperationFail

- **訊息**：`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**：您的 SFTP 伺服器不支援重新命名暫存檔案。

- **建議**：在複製接收中將 "useTempFileRename" 設定為 false，以停用上傳至暫存檔案。


### <a name="error-code--sftpinvalidsftpcredential"></a>錯誤碼： SftpInvalidSftpCredential

- **訊息**：`Invalid Sftp credential provided for '%type;' authentication type.`

- **原因**：已從 AKV/SDK 提取私用金鑰內容，但未正確編碼。

- **建議**：  

    如果私密金鑰內容來自 AKV，而且如果客戶將它直接上傳至 SFTP 連結服務，則原始金鑰檔可以運作

    請參閱 https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication ，privateKey 內容是 Base64 編碼的 SSH 私用金鑰內容。

    請使用 base64 編碼來編碼 **原始私密金鑰檔案的整個內容** ，並將編碼的字串儲存至 AKV。 如果您按一下 [從檔案上傳]，就可以在 SFTP 連結服務上使用原始私密金鑰檔案。

    以下是用來產生字串的一些範例：

    - 使用 c # 程式碼：
    ```
    byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
    string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
    ```

    - 使用 Python 程式碼：
    ```
    import base64
    rfd = open(r'{Private Key Path}', 'rb')
    keyContent = rfd.read()
    rfd.close()
    print base64.b64encode(Key Content)
    ```

    - 使用協力廠商 base64 轉換工具

        https://www.base64encode.org/建議使用類似的工具。

- **原因**：選擇了錯誤的索引鍵內容格式

- **建議**：  

    PKCS # 8 格式 SSH 私密金鑰 (開頭為「-----開始加密的私密金鑰-----」 ) 目前不支援在 ADF 中存取 SFTP 伺服器。 

    執行下列命令，將金鑰轉換為傳統的 SSH 金鑰格式 (開頭為 "-----開始 RSA 私密金鑰-----" ) ：

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**：不正確認證或私用金鑰內容

- **建議**：請使用 WinSCP 之類的工具再次檢查，以查看您的金鑰檔或密碼是否正確。

### <a name="sftp-copy-activity-failed"></a>SFTP 複製活動失敗

- **徵兆**：錯誤碼： UserErrorInvalidColumnMappingColumnNotFound。 錯誤訊息： `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **原因**：來源不包含名為 "AccMngr" 的資料行。

- **解決** 方式：再次檢查資料集的設定方式，方法是對應目的地資料集資料行，以確認是否有這類「AccMngr」資料行。


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>錯誤碼： SftpFailedToConnectToSftpServer

- **訊息**：`Failed to connect to Sftp server '%server;'.`

- **原因**：如果錯誤訊息包含「通訊端讀取作業在30000毫秒後超時」，可能是因為 SFTP 伺服器使用了不正確的連結服務類型，例如使用 FTP 連結服務連接到 sftp 伺服器。

- **建議**：檢查目標伺服器的埠。 SFTP 預設使用埠22。

- **原因**：如果錯誤訊息包含「伺服器回應不包含 SSH 通訊協定識別」，可能的原因之一是 SFTP 伺服器已節流連接。 ADF 會建立多個連線，以平行方式從 SFTP 伺服器下載，有時會點擊 SFTP 伺服器節流。 實際上，不同的伺服器會在點擊節流時傳回不同的錯誤。

- **建議**：  

    將 SFTP 資料集的並行連線數上限指定為1，然後重新執行複製。 如果成功通過，我們可以確定節流是原因。

    如果您想要提升低輸送量，請洽詢 SFTP 系統管理員以增加並行連接計數限制，或將以下 IP 新增至允許清單：

    - 如果您是使用受控 IR，請新增 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
      或者，如果您不想將大型的 IP 範圍清單新增到 SFTP 伺服器允許清單中，也可以安裝自我裝載 IR。

    - 如果您使用自我裝載 IR，請將已安裝 SHIR 的電腦 IP 新增至允許清單。


## <a name="sharepoint-online-list"></a>SharePoint Online 清單

### <a name="error-code--sharepointonlineauthfailed"></a>錯誤碼： SharePointOnlineAuthFailed

- **訊息**：`The access token generated failed, status code: %code;, error message: %message;.`

- **原因**：服務主體識別碼和金鑰可能未正確設定。

- **建議**：檢查已註冊的應用程式 (服務主體識別碼) 和金鑰是否已正確設定。


## <a name="xml-format"></a>Xml 格式

### <a name="error-code--xmlsinknotsupported"></a>錯誤碼： XmlSinkNotSupported

- **訊息**：`Write data in xml format is not supported yet, please choose a different format!`

- **原因**：在複製活動中使用 Xml 資料集做為接收資料集

- **建議**：使用不同格式的資料集做為複製接收。


### <a name="error-code--xmlattributecolumnnameconflict"></a>錯誤碼： XmlAttributeColumnNameConflict

- **訊息**：`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**：使用了造成衝突的屬性前置詞。

- **建議**：設定 "attributePrefix" 屬性的不同值。


### <a name="error-code--xmlvaluecolumnnameconflict"></a>錯誤碼： XmlValueColumnNameConflict

- **訊息**：`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**：已使用其中一個子專案名稱做為元素值的資料行名稱。

- **建議**：設定 "valueColumn" 屬性的不同值。


### <a name="error-code--xmlinvalid"></a>錯誤碼： XmlInvalid

- **訊息**：`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**：輸入 xml 檔案的格式不正確。

- **建議**：更正 xml 檔案，使其格式正確。


## <a name="general-copy-activity-error"></a>一般複製活動錯誤

### <a name="error-code--jrenotfound"></a>錯誤碼：JreNotFound

- **訊息**：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**︰自我裝載整合執行階段找不到 Java 執行階段。 需要 Java 執行階段才能讀取特定來源。

- **建議**：檢查您的整合執行階段環境，參考文件： https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>錯誤碼：WildcardPathSinkNotSupported

- **訊息**：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**︰接收資料集不支援萬用字元。

- **建議**：檢查接收資料集，並將路徑修正為不包含萬用字元值。


### <a name="fips-issue"></a>FIPS 問題

- **徵兆**：啟用 FIPS 的自我裝載 Integration Runtime 電腦上的複製活動失敗，並出現錯誤訊息 `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 。 使用 Azure Blob、SFTP 等連接器複製資料時，可能會發生這種情況。

- **原因**： FIPS (聯邦資訊處理標準) 會定義一組允許使用的密碼編譯演算法。 當電腦上已啟用 FIPS 模式時，複製活動所依賴的某些密碼編譯類別會在某些情況下遭到封鎖。

- **解決** 方式：您可以從 [這篇文章](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)瞭解 Windows 中的 fips 模式目前的情況，並評估是否可以在自我裝載的 Integration Runtime 電腦上停用 fips。

    另一方面，如果您只想讓 Azure Data Factory 略過 FIPS，讓活動執行成功，您可以遵循下列步驟：

    1. 開啟安裝自我裝載 Integration Runtime 的資料夾，通常是在底下 `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` 。

    2. 開啟 "diawp.exe.config"，加入如下所 `<enforceFIPSPolicy enabled="false"/>` `<runtime>` 示的區段。

        ![停用 FIPS](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. 重新開機自我裝載的 Integration Runtime 電腦。


## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](/answers/topics/azure-data-factory.html)
*  [Data Factory 的 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
