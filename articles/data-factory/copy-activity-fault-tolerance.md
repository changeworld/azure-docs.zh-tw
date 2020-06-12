---
title: Azure Data Factory 中複製活動的容錯
description: 了解如何跳過不相容的資料，以在 Azure Data Factory 的複製活動中新增容錯。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: a44703aabc35131cf040892999409173638437a7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658782"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory 中複製活動的容錯
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-copy-activity-fault-tolerance.md)
> * [目前的版本](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您將資料從來源複製到目的地存放區時，Azure Data Factory 複製活動會提供特定層級的容錯，以避免在資料移動過程中的失敗而中斷。 例如，您要將數百萬個資料列從來源複製到目的地存放區，其中主索引鍵已在目的地資料庫中建立，但是來源資料庫並未定義任何主索引鍵。 當您從來源將重複的資料列複製到目的地時，您會遇到目的地資料庫上的 PK 違規失敗。 目前，複製活動提供兩種方法來處理這類錯誤： 
- 一旦發生任何失敗後，您就可以中止複製活動。 
- 您可以啟用容錯來略過不相容的資料，從而繼續複製其餘部分。 例如，在此情況下，略過重複的資料列。 此外，您可以在複製活動中啟用工作階段記錄，以記錄略過的資料。 

## <a name="copying-binary-files"></a>複製二進位檔案 

ADF 在複製二進位檔案時支援下列容錯案例。 在下列案例中，您可以選擇中止複製活動或繼續複製其餘部分：

1. 其他應用程式會同時刪除 ADF 所要複製的檔案。
2. 某些特定資料夾或檔案不允許 ADF 存取，因為這些檔案或資料夾的 ACL 所需權限等級高於 ADF 中設定的連線資訊。
3. 如果您在 ADF 中啟用資料一致性驗證設定，則來源與目的地存放區之間的一或多個檔案不會驗證為一致。

### <a name="configuration"></a>組態 
當您在儲存體存放區之間複製二進位檔案時，可以啟用容錯，如下所示： 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true 
    }, 
    "validateDataConsistency": true, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2", 
            "type": "LinkedServiceReference" 
            }, 
        "path": "sessionlog/" 
     } 
} 
```
屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | -------- 
skipErrorFile | 一組屬性，可指定您在資料移動期間想要略過的失敗類型。 | | 否
fileMissing | SkipErrorFile 屬性包中的其中一個機碼值組，用來決定是否要略過檔案，而這些檔案會在 ADF 同時複製時，由其他應用程式刪除。 <br/> -True：您想要略過其他應用程式所刪除的檔案來複製其餘部分。 <br/> -False：您想要在資料移動過程中，從來源存放區刪除任何檔案之後，中止複製活動。 <br/>請注意，此屬性預設會設定為 true。 | True(default) <br/>False | 否
fileForbidden | SkipErrorFile 屬性包中的其中一個機碼值組，用來決定當那些檔案或資料夾的 ACL 需要比 ADF 中設定的連線更高權限等級時，是否要略過特定的檔案。 <br/> -True：您想要略過檔案以複製其餘部分。 <br/> -False：您想要在取得資料夾或檔案的權限問題後中止複製活動。 | True <br/>False (預設) | 否
dataInconsistency | SkipErrorFile 屬性包中的其中一個機碼值組，用來決定您是否想要略過來源與目的地存放區之間不一致的資料。 <br/> -True：您想要略過不一致的資料以複製其餘部分。 <br/> -False：您想要在發現不一致的資料後中止複製活動。 <br/>請注意，只有當您將 validateDataConsistency 設定為 True 時，這個屬性才有效。 | True <br/>False (預設) | 否
logStorageSettings  | 當您想要記錄略過的物件名稱時，可指定的一組屬性。 | &nbsp; | 否
linkedServiceName | 可儲存工作階段記錄檔的 [Azure Blob 儲存體](connector-azure-blob-storage.md#linked-service-properties)連結服務或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)。 | `AzureBlobStorage` 或 `AzureBlobFS` 類型連結服務的名稱，以代表您要用來儲存記錄檔的執行個體。 | 否
path | 記錄檔的路徑。 | 指定用來儲存記錄檔的路徑。 如不提供路徑，服務會為您建立容器。 | 否

### <a name="monitoring"></a>監視 

#### <a name="output-from-copy-activity"></a>複製活動的輸出
您可以透過每個複製活動執行的輸出，取得要讀取、寫入和略過的檔案數目。 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>複製活動的工作階段記錄

如果您設定記錄略過的檔案名稱，您可以在此路徑中找到記錄檔：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

記錄檔必須是 csv 檔案。 記錄檔的結構描述如下所示：

資料行 | 描述 
-------- | -----------  
時間戳記 | ADF 略過檔案時的時間戳記。
層級 | 此項目的記錄層級。 在顯示檔案略過的項目中，其會處於 [警告] 層級。
OperationName | 每個檔案上的 ADF 複製活動作業行為。 其將為 'FileSkip'，可指定要略過的檔案。
OperationItem | 要略過的檔案名稱。
訊息 | 說明為何略過檔案的詳細資訊。

記錄檔的範例如下所示： 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
從上述記錄檔中，您可以看到已略過 bigfile.csv，因為當 ADF 複製此檔案時，另一個應用程式會刪除此檔案。 且已略過 3_nopermission.txt，因為 ADF 會因權限問題而不允許存取它。


## <a name="copying-tabular-data"></a>複製表格式資料 

### <a name="supported-scenarios"></a>支援的案例
複製活動支援三種案例，以偵測、跳過並記錄不相容的表格式資料：

- **來源資料類型和接收原生類型之間的不相容**。 

    例如：使用包含三種 INT 類型資料行的結構描述定義，從 Blob 儲存體中的 CSV 檔案將資料複製到 SQL 資料庫。 包含數值資料的 CSV 檔案資料列 (例如 123,456,789) 會成功複製到接收存放區。 不過，包含非數值的資料列 (例如 123,456, abc) 會偵測為不相容並加以跳過。

- **來源與接收之間的資料行數目不相符**。

    例如：使用包含六個資料行的結構描述定義，從 Blob 儲存體中的 CSV 檔案將資料複製到 SQL 資料庫。 包含六個資料行的 CSV 檔案資料列會成功複製到接收存放區。 包含多於六個資料行的 CSV 檔案資料列會偵測為不相容，並加以跳過。

- **寫入 SQL Server/Azure SQL Database/Azure Cosmos DB**　時發生主索引鍵違規。

    例如：從 SQL Server 將資料複製到 SQL 資料庫。 會在接收 SQL 資料庫中定義主索引鍵，但是在來源 SQL Server 中不會定義這類主索引鍵。 無法將來源中的重複資料列複製到接收。 複製活動只會將來源資料中的第一個資料列複製到接收。 包含重複主索引鍵值的後續來源資料列會偵測為不相容，並加以跳過。

>[!NOTE]
>- 如需使用 PolyBase 將資料載入到 SQL 資料倉儲，請透過複製活動中的 "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" 來指定拒絕原則，以設定 PolyBase 的原生容錯設定。 如往常，您仍然可以啟用將 PolyBase 不相容的資料列重新導向到 Blob 或 ADLS 的功能，如下所示。
>- 當複製活動設定為叫用 [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)時，不會套用此功能。
>- 當複製活動設定為叫用 [SQL 接收中儲存的程序](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)時，不會套用此功能。

### <a name="configuration"></a>組態
下列範例提供的 JSON 定義，可設定在複製活動中略過不相容的資料列：

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logStorageSettings": { 
    "linkedServiceName": { 
        "referenceName": "ADLSGen2", 
        "type": "LinkedServiceReference" 
        }, 
    "path": "sessionlog/" 
    } 
}, 
```

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 指定是否要在複製期間略過不相容的資料列。 | True<br/>FALSE (預設值) | 否
logStorageSettings | 當您想要記錄不相容的資料列時，可指定的一組屬性。 | &nbsp; | 否
linkedServiceName | [Azure Blob 儲存體](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 的連結服務，儲存包含略過之資料列的記錄。 | `AzureBlobStorage` 或 `AzureBlobFS` 類型連結服務的名稱，以代表您要用來儲存記錄檔的執行個體。 | 否
path | 包含略過之資料列的記錄檔路徑。 | 指定需要用來記錄不相容資料的路徑。 如不提供路徑，服務會為您建立容器。 | 否

### <a name="monitor-skipped-rows"></a>監視略過的資料列
複製活動執行完成之後，您會在複製活動的輸出中看到略過的資料列數目：

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

如果您設定記錄不相容的資料列，可以在此路徑中找到記錄檔：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

記錄檔將會是 csv 檔案。 記錄檔的結構描述如下所示：

資料行 | 描述 
-------- | -----------  
時間戳記 | ADF 略過不相容資料列時的時間戳記
層級 | 此項目的記錄層級。 如果此項目顯示略過的資料列，其就會處於「警告」層級
OperationName | 每個資料列上的 ADF 複製活動作業行為。 其將為 'TabularRowSkip'，可指定已略過特定不相容的資料列
OperationItem | 從來源資料存放區中略過的資料列。
訊息 | 說明為何此特定資料列不相容的詳細資訊。


記錄檔內容範例如下所示：

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

從上述範例記錄檔中，由於來源到目的地存放區的類型轉換問題，您可以看到已略過一個資料列「data1、data2、data3」。 由於來源到目的地存放區的 PK 違規問題，已略過另一個資料列「data4、data5、data6」。 


## <a name="copying-tabular-data-legacy"></a>複製表格式資料 (舊版)：

以下是啟用容錯僅供複製表格式資料的舊版方式。 如果您要建立新的管線或活動，建議您改為從[這裡](#copying-tabular-data)開始。

### <a name="configuration"></a>組態
下列範例提供的 JSON 定義，可設定在複製活動中略過不相容的資料列：

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 指定是否要在複製期間略過不相容的資料列。 | True<br/>FALSE (預設值) | 否
redirectIncompatibleRowSettings | 當您想要記錄不相容的資料列時，可指定的一組屬性。 | &nbsp; | 否
linkedServiceName | [Azure 儲存體](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) 的連結服務，儲存包含跳過之資料列的記錄。 | `AzureStorage` 或 `AzureDataLakeStore` 類型連結服務的名稱，以代表您需要用來儲存記錄檔的執行個體。 | 否
path | 包含跳過之資料列的記錄檔路徑。 | 指定需要用來記錄不相容資料的路徑。 如不提供路徑，服務會為您建立容器。 | 否

### <a name="monitor-skipped-rows"></a>監視略過的資料列
複製活動執行完成之後，您會在複製活動的輸出中看到略過的資料列數目：

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
如果您設定記錄不相容的資料列，您可以在此路徑中找到記錄檔：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

記錄檔只能是 csv 檔案。 若有需要，系統會使用逗號做為資料行分隔符號，將目前跳過的原始資料記錄下來。 記錄檔中除了原始的來源資料之外，還額外新增了「ErrorCode」與「ErrorMessage」兩個資料行，可在其中查看不相容問題的根本原因。 系統會以雙引號標註 ErrorCode 與 ErrorMessage。 

記錄檔內容範例如下所示：

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動效能](copy-activity-performance.md)


