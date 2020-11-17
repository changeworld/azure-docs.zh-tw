---
title: 複製活動中的會話記錄
description: 瞭解如何在 Azure Data Factory 中啟用「複製活動」中的會話記錄。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: e56a840da07a2f6e966867699506f0122a0e7956
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593634"
---
#  <a name="session-log-in-copy-activity"></a>複製活動中的會話記錄

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您可以在複製活動中記錄複製的檔案名，這可協助您進一步確保資料不只能成功地從來源複製到目的地存放區，也可以在來源和目的地存放區之間保持一致，方法是檢查複製活動會話記錄中複製的檔案。  

當您在複製活動中啟用容錯設定以略過錯誤的資料時，也可以記錄略過的檔案和略過的資料列。  您可以從「 [複製活動」中的容錯](copy-activity-fault-tolerance.md)取得更多詳細資料。 

## <a name="configuration"></a>組態
下列範例會提供 JSON 定義，以啟用複製活動中的會話記錄： 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | 當設定為 true 時，您將有機會記錄複製的檔案、略過的檔案或略過的資料列。  | True<br/>FALSE (預設值) | 否
logLevel | "Info" 將會記錄所有複製的檔案、略過的檔案和略過的資料列。 「警告」會將略過的檔案和略過的資料列記錄。  | Info<br/>警告 (預設) | 否
enableReliableLogging | 若為 true，則會在每個檔案複製到目的地之後，以可靠模式複製活動立即清除記錄檔。  當您使用複製活動中啟用可靠的記錄模式來複製大量檔案時，您應該預期複製輸送量會受到影響，因為每個檔案複製都需要雙重寫入作業。 其中一個要求是目的地存放區，另一個要求是記錄儲存體存放區。  以最佳方式複製活動會在一段時間內將記錄排清一批記錄，其中的複製輸送量將會比較不受影響。 這種模式不保證記錄的完整性和時效性，因為在複製活動失敗時，最後一個批次的記錄檔事件尚未排清到記錄檔。 目前，您會看到複製到目的地的一些檔案不會記錄下來。  | True<br/>FALSE (預設值) | 否
logLocationSettings | 一組屬性，可用來指定要儲存會話記錄的位置。 | | 否
linkedServiceName | 可儲存工作階段記錄檔的 [Azure Blob 儲存體](connector-azure-blob-storage.md#linked-service-properties)連結服務或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)。 | `AzureBlobStorage` 或 `AzureBlobFS` 類型連結服務的名稱，其代表您要用來儲存記錄檔的執行個體。 | 否
path | 記錄檔的路徑。 | 指定您想要儲存記錄檔的路徑。 如不提供路徑，服務會為您建立容器。 | 否


## <a name="monitoring"></a>監視

### <a name="output-from-copy-activity"></a>複製活動的輸出
複製活動完全執行之後，您可以從每個複製活動執行的輸出查看記錄檔的路徑。 您可以從路徑中找到記錄檔： `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` 。  記錄檔將會是 csv 檔案。 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

### <a name="the-schema-of-the-log-file"></a>記錄檔的架構

以下是記錄檔的架構。

資料行 | 描述 
-------- | -----------  
時間戳記 | ADF 讀取、寫入或略過物件時的時間戳記。
層級 | 此項目的記錄層級。 它可以是「警告」或「資訊」。
OperationName | 每個物件上的 ADF 複製活動操作行為。 它可以是 ' FileRead '、' FileWrite '、' FileSkip ' 或 ' TabularRowSkip '。
OperationItem | 檔案名或略過的資料列。
訊息 | 如果檔案已從來源存放區讀取，或寫入目的地存放區，則會顯示詳細資訊。 這也可能是略過檔案或資料列的原因。

以下是記錄檔的範例。 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
從上述記錄檔中，您可以看到 sample1.csv 已略過，因為無法驗證其在來源與目的地存放區之間是一致的。 您可以深入了解 sample1.csv 會不一致是因為當 ADF 複製活動同時複製時，其他應用程式會變更此檔案。 您也可以看到 sample2.csv 已成功從來源複製到目的地存放區。

您可以使用多個分析引擎來進一步分析記錄檔。  下列幾個範例可讓您使用 SQL 查詢來分析記錄檔，方法是將 csv 記錄檔匯入到可 SessionLogDemo 資料表名稱的 SQL database。  

-   給我複製的檔案清單。 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   提供在特定時間範圍內複製的檔案清單。 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   使用複製的時間和中繼資料，為我提供一個特定的檔案。 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   為我提供一份清單，其中包含在某個時間範圍內複製其中繼資料的檔案。 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   給我略過的檔案清單。 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   告訴我特定檔案略過的原因。 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   為我提供略過的檔案清單，原因如下：「blob 檔案不存在」。 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   提供需要最長時間才能複製的檔案名。
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動容錯](copy-activity-fault-tolerance.md)
- [複製活動資料一致性](copy-activity-data-consistency.md)