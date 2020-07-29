---
title: 在 Azure SQL Edge 中建立 T-sql 串流作業（預覽）
description: 瞭解如何在 Azure SQL Edge （預覽）中建立串流分析作業。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 346a59f085e766fef09d73b9e7baa03dad510148
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321712"
---
# <a name="create-an-azure-stream-analytics-job-in-azure-sql-edge-preview"></a>在 Azure SQL Edge （預覽）中建立 Azure 串流分析作業 

本文說明如何在 Azure SQL Edge （預覽）中建立 T-sql 串流作業。 您會建立外部資料流輸入和輸出物件，然後定義串流作業查詢，做為串流作業建立的一部分。

> [!NOTE]
> 若要在 Azure SQL Edge 中啟用 T-sql 串流功能，請啟用 TF 11515 做為啟動選項，或使用[DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)命令。 如需有關如何使用 mssql 檔案來啟用追蹤旗標的詳細資訊，請參閱[使用 mssql](configure.md#configure-by-using-an-mssqlconf-file)檔案進行設定。

## <a name="configure-the-external-stream-input-and-output-objects"></a>設定外部資料流輸入和輸出物件

T-sql 串流會使用 SQL Server 的外部資料源功能來定義與外部資料流輸入和串流作業輸出相關聯的資料來源。 使用下列 T-sql 命令來建立外部資料流輸入或輸出物件：

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

此外，如果使用 Azure SQL Edge、SQL Server 或 Azure SQL Database 作為輸出資料流程，您就需要[建立資料庫範圍認證（transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 此 T-sql 命令會定義用來存取資料庫的認證。

### <a name="supported-input-and-output-stream-data-sources"></a>支援的輸入和輸出資料流程資料來源

Azure SQL Edge 目前僅支援使用下列資料來源作為串流的輸入和輸出。

| 資料來源類型 | 輸入 | 輸出 | 說明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge 中樞 | Y | Y | 用來讀取和寫入串流資料到 Azure IoT Edge 中樞的資料來源。 如需詳細資訊，請參閱[IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)。|
| SQL Database | N | Y | 用來將串流資料寫入到 SQL Database 的資料來源連線。 資料庫可以是 Azure SQL Edge 中的本機資料庫，或是 SQL Server 或 Azure SQL Database 中的遠端資料庫。|
| Kafka | Y | N | 用來從 Kafka 主題讀取串流資料的資料來源。 此介面卡目前僅適用于 Intel 或 AMD 版本的 Azure SQL Edge。 不適用於 ARM64 版本的 Azure SQL Edge。|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>範例：建立 Azure IoT Edge 中樞的外部資料流輸入/輸出物件

下列範例會建立 Azure IoT Edge 中樞的外部資料流物件。 若要建立 Azure IoT Edge 中樞的外部資料流輸入/輸出資料來源，您必須先建立要讀取或寫入之資料配置的外部檔案格式。

1. 建立 JSON 類型的外部檔案格式。

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. 建立 Azure IoT Edge 中樞的外部資料源。 下列 T-sql 腳本會建立與 Azure SQL Edge 在相同 Docker 主機上執行之 IoT Edge 中樞的資料來源連接。

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. 建立 Azure IoT Edge 中樞的外部資料流物件。 下列 T-sql 腳本會建立 IoT Edge 中樞的資料流程物件。 如果是 IoT Edge 中樞資料流程物件，LOCATION 參數就是要讀取或寫入的 IoT Edge 中樞主題或通道的名稱。

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>範例：建立要 Azure SQL Database 的外部資料流物件

下列範例會在 Azure SQL Edge 中建立本機資料庫的外部資料流物件。 

1. 在資料庫上建立主要金鑰。 這是加密認證祕密的必要項目。

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. 建立資料庫範圍認證以存取 SQL Server 來源。 下列範例會建立外部資料源的認證，其中包含 IDENTITY = username 和 SECRET = password。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. 使用 CREATE EXTERNAL DATA SOURCE 建立外部資料來源。 下列範例將：

    * 建立名為*LocalSQLOutput*的外部資料源。
    * 識別外部資料來源 (LOCATION = '<vendor>://<server>[:<port>]')。 在此範例中，它會指向 Azure SQL Edge 的本機實例。
    * 使用先前建立的認證。

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. 建立外部資料流物件。 下列範例會建立指向資料表 dbo 的外部資料流物件 *。TemperatureMeasurements*，在資料庫*MySQLDatabase*中。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>範例：建立 Kafka 的外部資料流物件

下列範例會在 Azure SQL Edge 中建立本機資料庫的外部資料流物件。 這個範例假設 kafka 伺服器已設定為匿名存取。 

1. 使用 CREATE EXTERNAL DATA SOURCE 建立外部資料來源。 下列範例將：

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. 建立 kafka 輸入的外部檔案格式。 下列範例會建立具有 GZipped 壓縮的 JSON 檔案格式。 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```
    
3. 建立外部資料流物件。 下列範例會建立指向 Kafka 主題的外部資料流物件 `*TemperatureMeasurement*` 。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',     
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>建立串流作業和串流查詢

使用 `sys.sp_create_streaming_job` 系統預存程式來定義串流查詢，並建立串流作業。 `sp_create_streaming_job`預存程式會採用下列參數：

- `job_name`：串流作業的名稱。 串流作業名稱在整個執行個體中是唯一的。
- `statement`：[串流分析查詢以語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)為基礎的串流查詢語句。

下列範例會使用一個串流查詢來建立簡單的串流作業。 此查詢會從 IoT Edge 中樞讀取輸入，並將寫入 `dbo.TemperatureMeasurements` 資料庫中的。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

下列範例會使用多個不同的查詢來建立更複雜的串流作業。 這些查詢包含一個使用內建 `AnomalyDetection_ChangePoint` 函數來識別溫度資料中的異常。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>啟動、停止、卸載和監視串流作業

若要在 Azure SQL Edge 中啟動串流作業，請執行 `sys.sp_start_streaming_job` 預存程式。 預存程式需要開始的串流作業名稱，做為輸入。

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

若要停止串流作業，請執行 `sys.sp_stop_streaming_job` 預存程式。 預存程式需要停止的串流作業名稱，做為輸入。

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

若要卸載（或刪除）串流作業，請執行 `sys.sp_drop_streaming_job` 預存程式。 預存程式需要卸載的串流作業名稱，做為輸入。

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

若要取得串流作業的目前狀態，請執行 `sys.sp_get_streaming_job` 預存程式。 預存程式需要卸載的串流作業名稱，做為輸入。 它會輸出串流作業的名稱和目前狀態。

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

串流作業可以具有下列任何一種狀態：

| 狀態 | 描述 |
|--------| ------------|
| 建立時間 | 串流作業已建立，但尚未啟動。 |
| 啟動中 | 串流作業處於正在啟動的階段。 |
| 閒置 | 串流作業正在執行，但沒有任何輸入可處理。 |
| Processing | 串流作業正在執行，且正在處理輸入。 此狀態表示串流作業處於健全狀態。 |
| 已降級 | 串流作業正在執行，但在輸入處理期間發生一些非嚴重錯誤。 輸入作業會繼續執行，但會捨棄發生錯誤的輸入。 |
| 已停止 | 串流作業已停止。 |
| 失敗 | 串流工作失敗。 這通常表示處理期間發生嚴重錯誤。 |

## <a name="next-steps"></a>後續步驟

- [在 Azure SQL Edge (預覽) 中檢視與串流作業相關聯的中繼資料](streaming-catalog-views.md) 
- [建立外部資料流](create-external-stream-transact-sql.md)
