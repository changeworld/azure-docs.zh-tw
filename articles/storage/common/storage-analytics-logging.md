---
title: Azure 儲存體分析記錄
description: 使用儲存體分析記錄有關 Azure 儲存體要求的詳細資料。 查看記錄哪些要求、記錄的儲存方式、如何啟用儲存體記錄等等。
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 5b4e2fa95b9a5eebf393d7c64feecd3997b7ecfd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280023"
---
# <a name="azure-storage-analytics-logging"></a>Azure 儲存體分析記錄

儲存體分析會記錄對儲存體服務之成功和失敗要求的詳細資訊。 這項資訊可用來監視個別要求，並診斷儲存體服務的問題。 系統會以最佳方式來記錄要求。

 根據預設，儲存體帳戶不會啟用儲存體分析記錄功能。 您可以在 [Azure 入口網站](https://portal.azure.com/)中啟用它；如需詳細資訊，請參閱[在 Azure 入口網站中監視儲存體帳戶](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用[取得 Blob 服務屬性](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) \(部分機器翻譯\)、[取得佇列服務屬性](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties) \(部分機器翻譯\) 及[取得資料表服務屬性](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) \(部分機器翻譯\) 作業，為每個服務啟用儲存體分析。

 只有在對服務端點提出要求時，才會建立記錄項目。 例如，如果儲存體帳戶在其 Blob 端點中有活動，而不是在其資料表或佇列端點中，則只會建立關於 Blob 服務的記錄。

> [!NOTE]
>  儲存體分析記錄目前僅適用於 Blob、佇列及表格服務， 儲存體分析記錄也適用於進階效能 [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) 帳戶。 不過，其不適用於使用進階效能的一般用途 v2 帳戶。

## <a name="requests-logged-in-logging"></a>記錄中記錄的要求
### <a name="logging-authenticated-requests"></a>記錄驗證要求

 系統將記錄下列類型的驗證要求：

- 成功的要求
- 失敗的要求，包括逾時、節流、網路、授權和其他錯誤
- 使用共用存取簽章 (SAS) 或 OAuth 的要求，包括失敗和成功的要求
- 分析資料的要求

  系統不會記錄儲存體分析本身所提出的要求 (例如，記錄檔的建立或刪除)。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)主題中。

### <a name="logging-anonymous-requests"></a>記錄匿名要求

 系統將記錄下列類型的匿名要求：

- 成功的要求
- 伺服器錯誤
- 用戶端與伺服器的逾時錯誤
- 失敗的 GET 要求，錯誤碼為 304 (未修改)

  系統不會記錄所有其他失敗的匿名要求。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)主題中。

## <a name="how-logs-are-stored"></a>記錄的儲存方式

所有記錄都會儲存在容器 `$logs` 的區塊 Blob 中，此容器會在啟用儲存體帳戶的儲存體分析時自動建立。 `$logs` 容器位於儲存體帳戶的 Blob 命名空間中，例如：`http://<accountname>.blob.core.windows.net/$logs`。 一旦啟用儲存體分析之後便無法刪除此容器，不過您可以刪除其內容。 如果您使用儲存體瀏覽工具來直接瀏覽容器，則會看見包含記錄資料的所有 Blob。

> [!NOTE]
>  `$logs` 容器不會在執行容器列示作業時顯示，例如「列出容器」作業。 您必須直接存取它。 例如，您可以使用「列出 Blob」方法來存取 `$logs` 容器中的 Blob。

記錄要求時，儲存體分析將會以區塊形式上傳中繼結果。 儲存體分析會定期認可這些區塊，並提供它們做為 Blob。 因為儲存體服務清空記錄寫入器的頻率，記錄資料最多可能需要一小時才會顯示在 **$logs** 容器的 Blob 中。 在同一個小時內建立的記錄可能會有重複的記錄。 您可以藉由檢查 **RequestId** 和 **Operation** 數字來判斷記錄是否重複。

如果您每小時有大量的記錄資料 (具有多個檔案)，則可以使用 Blob 中繼資料，藉由檢查 Blob 中繼資料欄位來判斷記錄所包含的資料。 這也很有用，因為在資料寫入記錄檔時，有時會發生延遲：Blob 中繼資料可提供比 Blob 名稱更精確的 Blob 內容指示。

大多數儲存體瀏覽工具可讓您檢視 Blob 的中繼資料；您可也以使用 PowerShell 或以程式設計方式讀取此資訊。 下列 PowerShell 程式碼片段示範如何依名稱篩選記錄 Blob 的清單來指定時間，以及如何依中繼資料進行篩選以識別包含**寫入**作業的那些記錄。  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

如需以程式設計方式列出 Blob 的詳細資訊，請參閱[列舉 Blob 資源](https://msdn.microsoft.com/library/azure/hh452233.aspx) \(部分機器翻譯\) 及[設定和擷取 Blob 資源的屬性和中繼資料](https://msdn.microsoft.com/library/azure/dd179404.aspx) \(部分機器翻譯\)。  

### <a name="log-naming-conventions"></a>記錄檔命名慣例

 每個記錄檔的寫入格式如下：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表描述記錄檔名稱中的每個屬性：

|屬性|描述|
|---------------|-----------------|
|`<service-name>`|儲存體服務的名稱。 例如：`blob`、`table` 或 `queue`|
|`YYYY`|記錄檔的四位數年份。 例如： `2011`|
|`MM`|記錄檔的兩位數月份。 例如： `07`|
|`DD`|記錄檔的兩位數日期。 例如： `31`|
|`hh`|表示記錄檔開始小時的二位數字 (24 小時制國際標準時間格式)。 例如： `18`|
|`mm`|表示記錄檔開始分鐘的二位數字。 **注意：** 目前的儲存體分析版本中不支援此值，其值一律為 `00`。|
|`<counter>`|以零起始的六位數計數器，表示在一小時內針對儲存體服務產生的記錄檔 Blob 數目。 此計數器會從 `000000` 開始。 例如： `000001`|

 以下是結合上述範例的完整範例記錄檔名稱：

 `blob/2011/07/31/1800/000001.log`

 以下是可用來存取上述記錄檔的範例 URI：

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 記錄儲存體要求時，產生的記錄檔名稱會與完成所要求之作業的小時相互關聯。 例如，如果 GetBlob 要求於 2011/7/31 下午 6:30 完成，記錄檔寫入時會包含下列前置詞：`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>記錄中繼資料

 所有的記錄檔 Blob 都會與中繼資料一同儲存，可用來識別 Blob 包含哪些記錄資料。 下表描述每個中繼資料屬性：

|屬性|描述|
|---------------|-----------------|
|`LogType`|描述記錄檔是否包含關於讀取、寫入或刪除作業的資訊。 此值可包含一個類型或是所有這三種類型的組合 (以逗號分隔)。<br /><br /> 範例 1：`write`<br /><br /> 範例 2：`read,write`<br /><br /> 範例 3：`read,write,delete`|
|`StartTime`|記錄檔項目的最早記錄時間，格式為 `YYYY-MM-DDThh:mm:ssZ`。 例如： `2011-07-31T18:21:46Z`|
|`EndTime`|記錄檔項目的最晚記錄時間，格式為 `YYYY-MM-DDThh:mm:ssZ`。 例如： `2011-07-31T18:22:09Z`|
|`LogVersion`|記錄檔格式的版本。|

 下列清單顯示使用上述範例的完整範例中繼資料：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>啟用儲存體記錄

您可以使用 Azure 入口網站、PowerShell 和儲存體 SDK 來啟用儲存體記錄。

### <a name="enable-storage-logging-using-the-azure-portal"></a>使用 Azure 入口網站來啟用儲存體記錄  

在 Azure 入口網站中，使用 [診斷設定 (傳統)] 刀鋒視窗來控制儲存體記錄，您可以從儲存體帳戶 [功能表] 刀鋒視窗的 [監視 (傳統)] 區段存取。

您可以指定要記錄的儲存體服務，以及所記錄資料的保留期間 (以天為單位)。  

### <a name="enable-storage-logging-using-powershell"></a>使用 PowerShell 啟用儲存體記錄  

 您可以在本機電腦上使用 PowerShell 來設定儲存體帳戶中的儲存體記錄，方法是使用 Azure PowerShell Cmdlet **AzStorageServiceLoggingProperty** 來取得目前的設定，並使用 Cmdlet **設定 AzStorageServiceLoggingProperty** 來變更目前的設定。  

 控制「儲存體記錄」的 Cmdlet 會使用 **LoggingOperations** 參數，該參數是一個包含所要記錄要求類型清單 (以逗號分隔) 的字串。 三個可能的要求類型包含**讀取**、**寫入**和**刪除**。 若要關閉記錄功能，請將值 **none** 使用於 **LoggingOperations** 參數。  

 下列命令針對預設儲存體帳戶中佇列服務的讀取、寫入和刪除要求開啟記錄功能 (保留期間設為五天)：  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 下列命令針對預設儲存體帳戶中的資料表服務關閉記錄功能：  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[如何安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

### <a name="enable-storage-logging-programmatically"></a>以程式設計方式啟用儲存體記錄  

 除了使用 Azure 入口網站或 Azure PowerShell Cmdlet 來控制儲存體記錄，您也可以使用其中一個 Azure 儲存體 API。 例如，若您使用 .NET 語言，則可使用儲存體用戶端程式庫。  

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

---


 如需使用 .NET 語言來設定儲存體記錄的詳細資訊，請參閱[儲存體用戶端程式庫參考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。  

 如需使用 REST API 設定儲存體記錄的一般資訊，請參閱[啟用及設定儲存體分析](https://msdn.microsoft.com/library/azure/hh360996.aspx) \(部分機器翻譯\)。  

## <a name="download-storage-logging-log-data"></a>下載儲存體記錄的記錄資料

 若要檢視和分析記錄資料，您應將包含您有興趣之記錄資料的 Blob 下載到本機電腦。 很多儲存體瀏覽工具可讓您從儲存體帳戶下載 Blob；您也可以使用 Azure 儲存體團隊提供的命令列 Azure 複製工具 [AzCopy](storage-use-azcopy-v10.md) 來下載記錄資料。  
 
>[!NOTE]
> `$logs` 容器未與事件方格整合，因此當您寫入記錄檔時，不會收到通知。 

 若要確保下載有興趣的記錄資料以及避免多次下載相同的記錄資料：  

-   將日期和時間命名慣例使用於包含記錄資料的 Blob，以追蹤您已下載進行分析的 Blob，進而避免多次重複下載相同的資料。  

-   使用 Blob (包含記錄資料) 上的中繼資料來識別 Blob 所持有記錄資料的特定期間，以識別您需要下載的確切 Blob。  

若要開始使用 AzCopy，請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md) 

下列範例顯示如何下載佇列服務的記錄資料，時間從 2014 年 5 月 20 日上午 09、上午 10 點，以及上午 11 開始。

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

若要深入了解如何下載特定檔案，請參閱[下載特定檔案](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files) \(部分機器翻譯\)。

當您下載記錄資料後，即可檢視檔案中的記錄項目。 這些記錄檔會使用分隔的文字格式，許多記錄讀取工具都可以剖析 (如需詳細資訊，請參閱 [Microsoft Azure 儲存體) 的監視、診斷和疑難排解](storage-monitoring-diagnosing-troubleshooting.md) 指南。 不同的工具有不同的功能可格式化、篩選、排序及搜尋記錄檔的內容。 如需儲存體記錄的記錄檔格式和內容的詳細資訊，請參閱[儲存體分析記錄格式](/rest/api/storageservices/storage-analytics-log-format)和[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。

## <a name="next-steps"></a>後續步驟

* [儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [儲存體分析計量 (傳統)](storage-analytics-metrics.md)
