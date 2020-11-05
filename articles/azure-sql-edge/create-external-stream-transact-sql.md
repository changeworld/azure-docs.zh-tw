---
title: " (Transact-sql) 建立外部資料流-Azure SQL Edge"
description: 瞭解 Azure SQL Edge 中的 CREATE EXTERNAL STREAM 語句
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 92658584030fa83da067eceab391d9bba2f034c0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392294"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

外部資料流物件具有輸入和輸出資料流程的雙重用途。 它可用來做為輸入，以便從事件內嵌服務（例如 Azure 事件中樞、Azure IoT 中樞 (或 Edge 中樞) 或 Kafka）查詢串流資料，或使用它作為輸出，以指定從串流查詢儲存結果的位置和方式。

您也可以指定和建立 EXTERNAL STREAM 來作為事件中樞或 Blob 儲存體等服務的輸出和輸入。 這可促進串流查詢將結果保存到外部資料流作為輸出的連結案例，並將另一個串流查詢從相同的外部資料流讀取為輸入。

Azure SQL Edge 目前僅支援使用下列資料來源作為串流的輸入和輸出。

| 資料來源類型 | 輸入 | 輸出 | 描述 |
|------------------|-------|--------|------------------|
| Azure IoT Edge 中樞 | Y | Y | 用來讀取和寫入串流資料至 Azure IoT Edge 中樞的資料來源。 如需詳細資訊，請參閱 [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)。|
| SQL Database | N | Y | 用來將串流資料寫入到 SQL Database 的資料來源連線。 資料庫可以是 Azure SQL Edge 中的本機資料庫，也可以是 SQL Server 或 Azure SQL Database 中的遠端資料庫。|
| Kafka | Y | N | 用來從 Kafka 主題讀取串流資料的資料來源。 ARM64 版本的 Azure SQL Edge 無法使用 Kafka 支援。|



## <a name="syntax"></a>語法

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>引數

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION** ：指定資料來源中實際資料或位置的名稱。 
   - 若為 Edge 中樞或 Kafka 資料流程物件，location 會指定要讀取或寫入的 Edge 中樞或 Kafka 主題的名稱。
   - 若為 SQL stream 物件 (SQL Server，Azure SQL Database 或 Azure SQL Edge) 位置會指定資料表的名稱。 如果資料流程是在與目的地資料表相同的資料庫和架構中建立的，則只會後綴資料表名稱。 否則，您必須完整限定 ( # B0 database_name. schema_name. table_name) 資料表名稱。
   - 針對 Azure Blob 儲存體 stream 物件位置是指在 Blob 容器內使用的路徑模式。 如需此功能的詳細資訊，請參閱 (/articles/stream-analytics/stream-analytics-define-outputs.md # blob-儲存體-和-azure-gen2) 

- **INPUT_OPTIONS** ：將選項指定為服務的索引鍵/值組，例如作為串流查詢輸入的 Kafka、IoT Edge 中樞
    - 分割區：為主題定義的資料分割數目。 可以使用的最大分割區數目限制為32。
      - 適用于 Kafka 輸入資料流程
    - CONSUMER_GROUP：事件中樞和 IoT 中樞會限制一個取用者群組可擁有的讀者數量 (最多 5 個)。 將此欄位保留空白則會使用 '$Default' 取用者群組。
      - 保留供日後使用。 不適用於 Azure SQL Edge。  
    - TIME_POLICY：描述當延遲的事件或順序不正確的事件超過其容錯值時，是要捨棄事件還是調整事件的時間。
      - 保留供日後使用。 不適用於 Azure SQL Edge。
    - LATE_EVENT_TOLERANCE：可接受的最大時間延遲。 延遲代表事件的時間戳記與系統時鐘之間的差異。
      - 保留供日後使用。 不適用於 Azure SQL Edge。
    - OUT_OF_ORDER_EVENT_TOLERANCE：事件在從輸入前往串流查詢時，可能不會按順序抵達。 您可以依現狀接受這些事件，或者也可以選擇暫停一定時間來將其重新排序。
      - 保留供日後使用。 不適用於 Azure SQL Edge。
        
- **OUTPUT_OPTIONS** ：針對輸出至串流查詢的受支援服務，將選項指定為索引鍵/值組 
  - REJECT_POLICY：DROP | RETRY 指定在發生資料轉換錯誤時的資料錯誤處理原則。 
    - 適用於所有受支援的輸出 
  - MINIMUM_ROWS：  
    寫入到輸出的每個批次所需包含的資料列數量下限。 針對 Parquet，每個批次都會建立新的檔案。 
    - 適用於所有受支援的輸出 
  - MAXIMUM_TIME：  
    每一批次的等候時間上限，以分鐘為單位。 在此時間後，即使未符合資料列下限需求，也會將批次寫入輸出。 
    - 適用於所有受支援的輸出 
  - PARTITION_KEY_COLUMN：  
    用於分割區索引鍵的資料行。 
    - 保留供日後使用。 不適用於 Azure SQL Edge。
  - PROPERTY_COLUMNS：  
    要附加到訊息作為自訂屬性的輸出資料行名稱逗點分隔清單 (如有提供)。  
    - 保留供日後使用。 不適用於 Azure SQL Edge。 
  - SYSTEM_PROPERTY_COLUMNS：  
    要在服務匯流排訊息上填入之系統屬性名稱和輸出資料行的 JSON 格式名稱/值對集合。 例如 { "MessageId":   "column1", "PartitionKey": "column2"} 
    - 保留供日後使用。 不適用於 Azure SQL Edge。 
  - PARTITION_KEY：  
    包含資料分割索引鍵的輸出資料行名稱。 在構成實體主索引鍵第一個部分的指定資料表內，資料分割索引鍵是資料分割的唯一識別碼。 大小最高為 1 KB 的字串值。 
    - 保留供日後使用。 不適用於 Azure SQL Edge。
  - ROW_KEY：  
    包含資料列索引鍵的輸出資料行名稱。 資料列索引鍵是指定資料分割內實體的唯一識別碼。 其可構成實體主索引鍵的第二個部分。 資料列索引鍵是大小可能高達 1 KB 的字串值。 
    - 保留供日後使用。 不適用於 Azure SQL Edge。
  - BATCH_SIZE：  
    這代表資料表儲存體的交易數目，最多可達 100 筆記錄。 針對 Azure Functions，這代表每個呼叫傳送至函式的批次大小 (以位元組為單位) - 預設為 256 kB。 
    - 保留供日後使用。 不適用於 Azure SQL Edge。 
  - MAXIMUM_BATCH_COUNT：  
    針對 Azure 函式，每個呼叫傳送給函式的最大事件數目 - 預設為 100。 針對 SQL Database，這代表每個大量插入交易所傳送的記錄數目上限 - 預設為 10,000。 
    - 適用于所有以 SQL 為基礎的輸出 
  - STAGING_AREA：將高輸送量資料內嵌至 Azure Synapse Analytics 的暫存區域，將外部資料源物件移至 Blob 儲存體 
    - 保留供日後使用。 不適用於 Azure SQL Edge。


## <a name="examples"></a>範例

### <a name="example-1---edgehub"></a>範例 1 - EdgeHub

輸入：輸入或輸出<br>

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
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>範例 2 - Azure SQL Database、Azure SQL Edge、SQL Server

輸入：輸出<br>

語法：

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>範例 3 - Kafka

輸入：輸入<br>

語法：
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>另請參閱

- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md)