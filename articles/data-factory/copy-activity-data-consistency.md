---
title: 複製活動中的資料一致性驗證
description: 了解如何在 Azure Data Factory 的複製活動中啟用資料一致性驗證。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: 3591bfe046fa1c3e1e55aa49a0ae3ad698bc57b3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593666"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>複製活動中的資料一致性驗證

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您將資料從來源移至目的地存放區時，Azure Data Factory 複製活動會提供一個選項，讓您執行額外的資料一致性驗證，以確保資料不只會成功從來源複製到目的地存放區，還會在來源與目的地存放區之間驗證為一致。 當資料移動期間發現不一致的檔案時，您可以中止複製活動，或透過啟用容錯設定來繼續複製其餘部分，以略過不一致的檔案。 您可以啟用複製活動中的會話記錄檔設定，以取得略過的檔案名。 您可以參考「 [複製活動」中的會話記錄](copy-activity-log.md) ，以取得更多詳細資料。

## <a name="supported-data-stores-and-scenarios"></a>支援的資料存放區和案例

-   除了 FTP、sFTP 和 HTTP 以外的所有連接器都支援資料一致性驗證。 
-   暫存複製案例不支援資料一致性驗證。
-   複製二進位檔案時，只有在複製活動中設定 ' PreserveHierarchy ' 行為時，才可使用資料一致性驗證。
-   在啟用資料一致性驗證的單一複製活動中複製多個二進位檔案時，您可以選擇是否要中止複製活動或繼續複製其餘部分，方法是啟用容錯設定以略過不一致的檔案。 
-   在啟用資料一致性驗證的單一複製活動中複製資料表時，如果從來源讀取的資料列數與複製到目的地的資料列數目，以及略過的不相容資料列數目不同，則複製活動會失敗。


## <a name="configuration"></a>組態
下列範例提供 JSON 定義，以啟用複製活動中的資料一致性驗證： 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | 如果您針對此屬性設定為 true，則在複製二進位檔案時，複製活動會針對從來源複製到目的地存放區的每個二進位檔案，檢查檔案大小、lastModifiedDate 和 MD5 總和檢查碼，以確保來源和目的地存放區之間的資料一致性。 複製表格式資料時，複製活動會在作業完成後檢查總數據列計數，以確保從來源讀取的資料列總數，與複製到目的地的資料列數目，以及略過的不相容資料列數目相同。 請注意，啟用此選項會影響複製效能。  | True<br/>FALSE (預設值) | 否
dataInconsistency | SkipErrorFile 屬性包中的其中一個索引鍵/值組，用來判斷是否要略過不一致的檔案。 <br/> -True：您想要藉由略過不一致的檔案來複製其餘部分。<br/> -False：當找到不一致的檔案時，您想要中止複製活動。<br/>請注意，只有當您複製二進位檔案，並將 validateDataConsistency 設為 True 時，這個屬性才有效。  | True<br/>FALSE (預設值) | 否
logSettings | 可指定的一組屬性，可讓會話記錄檔記錄已略過的檔案。 | | 否
linkedServiceName | 可儲存工作階段記錄檔的 [Azure Blob 儲存體](connector-azure-blob-storage.md#linked-service-properties)連結服務或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)。 | `AzureBlobStorage` 或 `AzureBlobFS` 類型連結服務的名稱，其代表您要用來儲存記錄檔的執行個體。 | 否
path | 記錄檔的路徑。 | 指定您想要儲存記錄檔的路徑。 如不提供路徑，服務會為您建立容器。 | 否

>[!NOTE]
>- 將二進位檔案從複製或複製到 Azure Blob 或 Azure Data Lake Storage Gen2 時，ADF 會利用 [Azure BLOB API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) 和 [Azure Data Lake Storage Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers)來封鎖層級的 MD5 總和檢查碼驗證。 如果 ContentMD5 檔案上的檔案存在於 Azure Blob 上，或 Azure Data Lake Storage Gen2 作為資料來源，則 ADF 也會在讀取檔案之後，進行檔案層級的 MD5 總和檢查碼驗證。 將檔案複製到 Azure Blob 或 Azure Data Lake Storage Gen2 為數據目的地之後，ADF 會將 ContentMD5 寫入 Azure Blob 或 Azure Data Lake Storage Gen2，以供下游應用程式進一步取用以進行資料一致性驗證。
>- ADF 會在任何存放區間複製二進位檔案時進行檔案大小驗證。

## <a name="monitoring"></a>監視

### <a name="output-from-copy-activity"></a>複製活動的輸出
複製活動完全執行之後，您可以從每個複製活動執行的輸出查看資料一致性驗證的結果：

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
您可以從 [dataConsistencyVerification 屬性] 查看資料一致性驗證的詳細資料。

**VerificationResult** 的值： 
-   **已驗證** ：已確認您複製的資料在來源與目的地存放區之間是一致的。 
-   **NotVerified** ：您複製的資料尚未驗證為一致，因為您尚未在複製活動中啟用 validateDataConsistency。 
-   **不支援** ：您複製的資料尚未驗證為一致，因為此特定的複製組不支援資料一致性驗證。 

**InconsistentData** 的值： 
-   **已找到** ：ADF 複製活動發現不一致的資料。 
-   **已略過** ：ADF 複製活動已找到不一致的資料並加以略過。 
-   **無** ：ADF 複製活動尚未找到任何不一致的資料。 這可能是因為您在來源與目的地存放區之間的資料已驗證為一致，或因為您已在複製活動中停用 validateDataConsistency。 

### <a name="session-log-from-copy-activity"></a>複製活動的工作階段記錄

如果您設定記錄不一致的檔案，可以在此路徑中找到記錄檔：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。  記錄檔將會是 csv 檔案。 

記錄檔的結構描述如下所示：

資料行 | 描述 
-------- | -----------  
時間戳記 | ADF 略過不一致檔案時的時間戳記。
層級 | 此項目的記錄層級。 在顯示檔案略過的項目中，其會處於 [警告] 層級。
OperationName | 每個檔案上的 ADF 複製活動作業行為。 其將為 'FileSkip'，可指定要略過的檔案。
OperationItem | 要略過的檔案名稱。
訊息 | 說明為何略過檔案的詳細資訊。

記錄檔的範例如下所示： 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
從上述記錄檔中，您可以看到 sample1.csv 已略過，因為無法驗證其在來源與目的地存放區之間是一致的。 您可以深入了解 sample1.csv 會不一致是因為當 ADF 複製活動同時複製時，其他應用程式會變更此檔案。 



## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動容錯](copy-activity-fault-tolerance.md)