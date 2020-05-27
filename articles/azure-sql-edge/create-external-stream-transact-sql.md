---
title: CREATE EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge (預覽)
description: 了解 Azure SQL Edge (預覽) 中的 CREATE EXTERNAL STREAM 陳述式
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595397"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

EXTERNAL STREAM 物件具有輸入和輸出的雙重用途。 其既可作為輸入以查詢來自事件擷取服務 (例如 Azure 事件中樞或 IoT 中樞) 的串流資料，也可作為輸出以指定串流查詢所得結果的儲存位置和方式。

您也可以指定和建立 EXTERNAL STREAM 來作為事件中樞或 Blob 儲存體等服務的輸出和輸入。 這種作法可用於鏈結案例，也就是某個串流查詢將結果保存到外部串流作為輸出，而從相同外部串流讀取來的另一個串流查詢則作為輸入。 


## <a name="syntax"></a>語法

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>引數


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**：指定資料來源中實際資料或位置的名稱。 如果是 Edge 中樞或 Kafka 串流物件，LOCATION 會指定要讀取或寫入的 Edge 中樞或 Kafka 主題所擁有的名稱。
- **INPUT_OPTIONS**：針對作為串流查詢輸入的事件中樞和 IoT 中樞等服務，將選項指定為索引鍵/值組
    - CONSUMER_GROUP：事件中樞和 IoT 中樞會限制一個取用者群組可擁有的讀者數量 (最多 5 個)。 將此欄位保留空白則會使用 '$Default' 取用者群組。
      - 適用於事件中樞和 IoT 中樞
    - TIME_POLICY：描述當延遲的事件或順序不正確的事件超過其容錯值時，是要捨棄事件還是調整事件的時間。
      - 適用於事件中樞和 IoT 中樞
    - LATE_EVENT_TOLERANCE：可接受的最大時間延遲。 延遲代表事件的時間戳記與系統時鐘之間的差異。
      - 適用於事件中樞和 IoT 中樞
    - OUT_OF_ORDER_EVENT_TOLERANCE：事件在從輸入前往串流查詢時，可能不會按順序抵達。 您可以依現狀接受這些事件，或者也可以選擇暫停一定時間來將其重新排序。
      - 適用於事件中樞和 IoT 中樞
- **OUTPUT_OPTIONS**：針對輸出至串流查詢的受支援服務，將選項指定為索引鍵/值組 
  - REJECT_POLICY：DROP | RETRY 指定在發生資料轉換錯誤時的資料錯誤處理原則。 
    - 適用於所有受支援的輸出 
  - MINIMUM_ROWS：  
    寫入到輸出的每個批次所需包含的資料列數量下限。 針對 Parquet，每個批次都會建立新的檔案。 
    - 適用於所有受支援的輸出 
  - MAXIMUM_TIME：  
    每個批次的等候時間上限。 在此時間後，即使未符合資料列下限需求，也會將批次寫入輸出。 
    - 適用於所有受支援的輸出 
  - PARTITION_KEY_COLUMN：  
    用於分割區索引鍵的資料行。 
    - 適用於事件中樞和服務匯流排主題 
  - PROPERTY_COLUMNS：  
    要附加到訊息作為自訂屬性的輸出資料行名稱逗點分隔清單 (如有提供)。  
    - 適用於事件中樞和服務匯流排主題和佇列 
  - SYSTEM_PROPERTY_COLUMNS：  
    要在服務匯流排訊息上填入之系統屬性名稱和輸出資料行的 JSON 格式名稱/值對集合。 例如 { "MessageId":   "column1", "PartitionKey": "column2"} 
    - 適用於服務匯流排主題和佇列 
  - PARTITION_KEY：  
    包含資料分割索引鍵的輸出資料行名稱。 在構成實體主索引鍵第一個部分的指定資料表內，資料分割索引鍵是資料分割的唯一識別碼。 大小最高為 1 KB 的字串值。 
    - 適用於資料表儲存體和 Azure Function 
  - ROW_KEY：  
    包含資料列索引鍵的輸出資料行名稱。 資料列索引鍵是指定資料分割內實體的唯一識別碼。 其可構成實體主索引鍵的第二個部分。 資料列索引鍵是大小可能高達 1 KB 的字串值。 
    - 適用於資料表儲存體和 Azure Function 
  - BATCH_SIZE：  
    這代表資料表儲存體的交易數目，最多可達 100 筆記錄。 針對 Azure Functions，這代表每個呼叫傳送至函式的批次大小 (以位元組為單位) - 預設為 256 kB。 
    - 適用於資料表儲存體和 Azure Function 
  - MAXIMUM_BATCH_COUNT：  
    針對 Azure 函式，每個呼叫傳送給函式的最大事件數目 - 預設為 100。 針對 SQL Database，這代表每個大量插入交易所傳送的記錄數目上限 - 預設為 10,000。 
    - 適用於 SQL Database 和 Azure 函式 
  - STAGING_AREA：Blob 儲存體的 EXTERNAL DATA SOURCE 物件。用於將高輸送量資料內嵌至 SQL 資料倉儲的暫存區域。 
    - 適用於 SQL 資料倉儲


## <a name="examples"></a>範例

### <a name="example-1---edgehub"></a>範例 1 - EdgeHub

輸入：輸入或輸出<br>
參數：
- 輸入或輸出
  - Alias 
  - 事件序列化格式 
  - 編碼 
- 僅限輸入： 
  - 事件壓縮類型 

語法：

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>範例 2 - Azure SQL Database、Azure SQL Edge、SQL Server

輸入：輸出<br>
參數：
- 輸出別名  
- 資料庫 (SQL Database 的必備參數) 
- 伺服器 (SQL Database 的必備參數) 
- 使用者名稱 (SQL Database 的必備參數) 
- 密碼 (SQL Database 的必備參數) 
- Table 
- 將所有輸入分割區合併成單一寫入或繼承上一個查詢步驟或輸入的分割區配置 (SQL Database 的必備參數) 
- 批次計數上限 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>範例 3 - Kafka

輸入：輸入<br>
參數：

- Kafka 啟動程序伺服器 
- Kafka 主題名稱 
- 來源分割區計數 

語法：

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>範例 4 - Blob 儲存體

輸入：輸入或輸出<br>
參數：
- 輸入或輸出：
  - Alias 
  - 儲存體帳戶 
  - 儲存體帳戶金鑰 
  - 容器 
  - 路徑模式 
  - 日期格式 
  - 時間格式 
  - 事件序列化格式 
  - 編碼 
- 僅限輸入： 
  - 分割區 (輸入) 
  - 事件壓縮類型 (輸入) 
- 僅限輸出： 
  - 資料列下面 (輸出) 
  - 時間上限 (輸出) 
  - 驗證模式 (輸出) 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>範例 5 - 事件中樞

輸入：輸入或輸出<br>
參數：
- 輸入或輸出：
  - Alias 
  - 服務匯流排命名空間 
  - 事件中樞名稱 
  - 事件中樞原則名稱 
  - 事件中樞原則金鑰 
  - 事件序列化格式 
  - 編碼 
- 僅限輸入： 
  - 事件中樞取用者群組 
  - 事件壓縮類型 
- 僅限輸出： 
  - 分割區索引鍵資料行 
  - 屬性資料行 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>範例 6 - IoT 中樞

輸入：輸入<br>
參數：

- 輸入別名 
- IoT 中樞 
- 端點 
- 共用存取原則名稱 
- 共用存取原則金鑰 
- 取用者群組 
- 事件序列化格式 
- 編碼 
- 事件壓縮類型 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>範例 7 - Azure Synapse Analytics (先前稱為 SQL 資料倉儲)

輸入：輸出<br>
參數：
- 輸出別名 
- 資料庫 
- 伺服器 
- 使用者名稱 
- 密碼 
- Table 
- 暫存區域 (適用於 COPY) 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>範例 8 - 資料表儲存體

輸入：輸出<br>
參數：
- 輸出別名 
- 儲存體帳戶 
- 儲存體帳戶金鑰 
- 資料表名稱 
- 資料分割索引鍵 
- 列索引鍵 
- 批次大小 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>範例 9 - 服務匯流排主題 (與佇列相同的屬性)

輸入：輸出<br>
參數：
- 輸出別名 
- 服務匯流排命名空間 
- 主題名稱 
- 主題原則名稱 
- 主題原則金鑰 
- 屬性資料行 
- 系統屬性資料行 
- 事件序列化格式 
- 編碼 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>範例 10 - Cosmos DB

輸入：輸出<br>
參數：
- 輸出別名 
- 帳戶識別碼 
- 帳戶金鑰 
- 資料庫 
- 容器名稱 
- 文件識別碼 


語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>範例 11 - Power BI

輸入：輸出<br>
參數：
- 輸出別名 
- 資料集名稱 
- 資料表名稱 


語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>範例 12 - Azure 函式

輸入：輸出<br>
參數：
- 函式應用程式 
- 函式 
- Key 
- 批次大小上限 
- 批次計數上限 

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>另請參閱

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

