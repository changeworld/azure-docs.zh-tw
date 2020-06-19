---
title: 在 Azure SQL Edge (預覽) 中建立 T-SQL 串流作業
description: 了解如何在 Azure SQL Edge (預覽) 中建立串流分析作業
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7db7f9548a3daa86a53dd37fbe088661e8b7b17e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685171"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>在 Azure SQL Edge (預覽) 中建立串流分析作業 

本文說明如何在 Azure SQL Edge (預覽) 中建立 T-SQL 串流作業。 若要在 SQL Edge 中建立串流作業，您必須執行下列步驟

1. 建立外部串流的輸入和輸出物件
2. 將串流作業查詢定義為串流作業建立程序的一部分。

> [!NOTE]
> 若要在 Azure SQL Edge 中啟用 T-SQL 串流功能，請啟用 TF 11515 作為啟動選項，或使用 [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) 命令。 如需如何使用 mssql.conf 檔案啟用追蹤旗標的詳細資訊，請參閱[使用 mssql.conf 檔案進行設定](configure.md#configure-using-mssqlconf-file)。 在 Azure SQL Edge (預覽) 的未來更新中將會移除此需求。

## <a name="configure-an-external-stream-input-and-output-object"></a>設定外部串流的輸入和輸出物件

T-SQL 串流會使用 SQL Server 的外部資料來源功能，來定義與外部串流的串流作業輸入和輸出相關聯的資料來源。 必須透過下列 T-SQL 命令才能建立外部串流的輸入或輸出物件。

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

此外，如果使用 SQL Edge (或 SQL Server、Azure SQL) 作為輸出串流，則必須透過 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) T-SQL 命令才能定義用來存取 SQL 資料庫的認證。

### <a name="supported-input-and-output-stream-data-sources"></a>所支援的輸入和輸出串流資料來源

Azure SQL Edge 目前僅支援使用下列資料來源作為串流的輸入和輸出。

| 資料來源類型 | 輸入 | 輸出 | 描述 |
|------------------|-------|--------|------------------|
| Azure IoT Edge 中樞 | Y | Y | 要用來讀取串流資料或將串流資料寫入到 Azure IoT Edge 中樞的資料來源。 如需 Azure IoT Edge 中樞的詳細資訊，請參閱 [IoT Edge 中樞](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | N | Y | 用來將串流資料寫入到 SQL Database 的資料來源連線。 SQL Database 可以是本機 SQL Edge 資料庫、遠端 SQL Server 或 Azure SQL Database|
| Azure Blob 儲存體 | N | Y | 用來將資料寫入到 Azure 儲存體帳戶上 Blob 的資料來源。 |
| Kafka | Y | N | 用來從 Kafka 主題讀取串流資料的資料來源。 此配接器目前僅適用於 Intel/AMD 版本的 Azure SQL Edge，而不適用於 ARM64 版本的 SQL Edge。|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>範例：為 Azure IoT Edge 中樞建立外部串流的輸入/輸出物件

下列範例會為 Edge 中樞建立外部串流物件。 若要為 Azure IoT Edge 中樞建立外部串流的輸入/輸出資料來源，您必須先建立適用於 SQL 的外部檔案格式，以一併了解所要讀取/寫入資料的配置。

1. 建立格式類型為 JSON 的外部檔案格式。

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. 為 IoT Edge 中樞建立外部資料來源。 下列 T-SQL 指令碼會針對在 SQL Edge 所在相同 Docker 主機上執行的 Edge 中樞建立資料來源連線。

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. 為 IoT Edge 中樞建立外部串流物件。 下列 T-SQL 指令碼會為 Edge 中樞建立串流物件。 如果是 Edge 中樞串流物件，LOCATION 參數會是所要讀取或寫入的 Edge 中樞主題/通道名稱。

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>範例：建立外部串流物件 SQL Database

下列範例會建立本機 SQL Edge 資料庫的外部串流物件。 

1. 在資料庫上建立主要金鑰。 這是加密認證祕密的必要項目。

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. 建立資料庫範圍認證以存取 SQL Server 來源。 下列範例會針對 IDENTITY = 'username' 和 SECRET = 'password' 的外部資料來源建立認證。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. 使用 CREATE EXTERNAL DATA SOURCE 建立外部資料來源。 下列範例將：

    * 建立名為 LocalSQLOutput 的外部資料來源
    * 識別外部資料來源 (LOCATION = '<vendor>://<server>[:<port>]')。 在此範例中，其指向 SQL Edge 的本機執行個體。
    * 最後，範例會使用先前建立的認證。

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. 建立外部串流物件。 下列範例會建立指向資料庫 *MySQLDatabase* 中資料表 *dbo.TemperatureMeasurements* 的外部串流物件。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>建立串流作業和串流查詢

使用 **sys.sp_create_streaming_job** 系統預存程序來定義串流查詢，並建立串流作業。 **sp_create_streaming_job** 預存程序會採用兩個參數

- job_name - 串流作業的名稱。 串流作業名稱在整個執行個體中是唯一的。
- 陳述式 - 以[串流分析查詢語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)為基礎的串流查詢陳述式。

下列範例會建立具有一個串流查詢的串流作業。 此查詢會從 Edge 中樞讀取輸入，並寫入到資料庫中的 *dbo.TemperatureMeasurements*。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

下列範例會建立具有多個不同查詢的更複雜串流作業，包括使用內建 AnomalyDetection_ChangePoint 函式來識別溫度資料異常之處的查詢。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>啟動、停止、捨棄和監視串流作業

若要在 SQL Edge 中啟動串流作業，請執行 **sys.sp_start_streaming_job** 預存程序。 此預存程序需要所要啟動的相同串流作業 (作為輸入)。

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

若要在 SQL Edge 中停止串流作業，請執行 **sys.sp_stop_streaming_job** 預存程序。 此預存程序需要所要停止的相同串流作業 (作為輸入)。

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

若要在 SQL Edge 中捨棄 (或刪除) 串流作業，請執行 **sys.sp_drop_streaming_job** 預存程序。 此預存程序需要所要捨棄的相同串流作業 (作為輸入)。

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

若要取得 SQL Edge 中串流作業的目前狀態，請執行 **sys.sp_get_streaming_job** 預存程序。 此預存程序需要所要捨棄的相同串流作業 (作為輸入)，並且會輸出串流作業的名稱和目前狀態。

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

串流作業可以是下列任何一種狀態

| 狀態 | 描述 |
|--------| ------------|
| 建立時間 | 已建立串流作業，但尚未啟動 |
| 啟動中 | 串流作業處於正在啟動的階段 |
| 閒置 | 串流作業正在執行，但沒有任何要處理的輸入 |
| Processing | 串流作業正在執行，且正在處理輸入。 此狀態表示串流作業處於健全狀態 |
| 已降級 | 串流作業正在執行，但在處理輸入期間，發生一些非嚴重性的輸入/輸出序列化/還原序列化錯誤。 輸入作業會繼續執行，但會捨棄發生錯誤的輸入 |
| 已停止 | 串流作業已停止 |
| 失敗 | 串流作業失敗。 這通常表示處理期間發生嚴重錯誤 |

## <a name="next-steps"></a>後續步驟

- [在 Azure SQL Edge (預覽) 中檢視與串流作業相關聯的中繼資料](streaming-catalog-views.md) 
- [建立外部串流](create-external-stream-transact-sql.md)
