---
title: Azure 存儲分析指標（經典）
description: 瞭解如何在 Azure 存儲中使用指標。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268401"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 存儲分析指標（經典）

儲存體分析可以儲存包含與儲存體服務要求相關之彙總的交易統計資料及容量資料的度量。 報告的交易是屬於 API 作業層級以及儲存體服務層級，而報告的容量則是屬於儲存體服務層級。 度量資料可用來分析儲存體服務使用量、診斷針對儲存體服務提出之要求的問題，以及提升使用服務的應用程式效能。  

 預設會為新的儲存體帳戶啟用儲存體分析度量。 您可以在[Azure 門戶](https://portal.azure.com/)中配置指標。有關詳細資訊，請參閱[在 Azure 門戶中監視存儲帳戶](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用"設置服務屬性"操作為每個服務啟用存儲分析。  

> [!NOTE]
> 存儲分析指標可用於 Blob、佇列、表和檔服務。
> 存儲分析指標現在是經典指標。 Microsoft 建議[在 Azure 監視器中使用存儲指標](storage-metrics-in-azure-monitor.md)，而不是存儲分析指標。

## <a name="transaction-metrics"></a>交易度量  
 系統會以每小時或每分鐘的時間間隔，為每個儲存體服務和要求的 API 作業記錄完善的資料集，其中包括輸入流量/輸出流量、可用性、錯誤，以及已分類的要求百分比。 您可以在 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema) 主題中查看完整的交易詳細資料清單。  

 記錄的交易資料屬於兩個層級 - 服務層級和 API 作業層級。 在服務層級，系統每小時都會將彙總所有要求的 API 作業的統計資料寫入資料表實體，即使未對該服務提出任何要求也一樣。 在 API 作業層級，只有在該小時內對該作業提出要求時，才會將統計資料寫入實體。  

 例如，如果您在 Blob 服務上執行 **GetBlob** 作業，儲存體分析度量將會記錄要求，然後將它納入 Blob 服務和 **GetBlob** 作業的彙總資料中。 但是，如果該小時內未請求**GetBlob**操作，則實體將不會寫入*該操作的$MetricsTransactionsBlob。*  

 系統會針對使用者要求及儲存體分析本身所提出的要求記錄交易度量。 例如，會記錄儲存體分析寫入記錄和資料表實體的要求。

## <a name="capacity-metrics"></a>容量度量  

> [!NOTE]
>  容量度量目前僅適用於 Blob 服務。

 系統每日都會針對儲存體帳戶的 Blob 服務記錄容量資料，並寫入兩個資料表實體。 其中一個實體會提供使用者資料的統計資料，而另一個實體則會提供有關儲存體分析所使用之 `$logs` Blob 容器的統計資料。 *$MetricsCapacityBlob* 資料表包含下列統計資料：  

- **容量**：存儲帳戶的 Blob 服務使用的存儲量（以位元組為單位）。  
- **容器計數**：存儲帳戶的 Blob 服務中的 Blob 容器數。  
- **ObjectCount**：存儲帳戶的 Blob 服務中已提交和未提交的塊或頁面 Blob 的數量。  

  如需容量度量的詳細資訊，請參閱 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

## <a name="how-metrics-are-stored"></a>度量的儲存方式  

 對於每個儲存體服務的所有度量資料均儲存於為該服務所保留的三個資料表中：一個資料表用於交易資訊、一個資料表用於每分鐘交易資訊，而另一個資料表則用於容量資訊。 交易和每分鐘交易資訊都是由要求和回應資料所組成，而容量資訊是由儲存體使用量資料所組成。 您可以從下表所述的具名資料表存取儲存體帳戶的 Blob 服務其每小時度量、每分鐘度量和容量資料。  

|度量層級|資料表名稱|支援版本|  
|-------------------|-----------------|----------------------------|  
|每小時度量，主要位置|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|僅適用於 2013-08-15 之前的版本。 儘管目前仍支援這些名稱，但還是建議您改用下列資料表。|  
|每小時度量，主要位置|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|所有版本。 對檔服務指標的支援僅在版本 2015-04-05 及更高版本中提供。|  
|每分鐘度量，主要位置|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|所有版本。 對檔服務指標的支援僅在版本 2015-04-05 及更高版本中提供。|  
|每小時度量，次要位置|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|每分鐘度量，次要位置|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|容量 (僅限 Blob 服務)|$MetricsCapacityBlob|所有版本。|  

 在為存儲服務終結點啟用存儲分析時，將自動創建這些表。 它們通過存儲帳戶的命名空間進行訪問，例如： `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`。 指標表不顯示在清單操作中，必須通過表名稱直接存取。  

## <a name="enable-metrics-using-the-azure-portal"></a>使用 Azure 門戶啟用指標
按照以下步驟在[Azure 門戶](https://portal.azure.com)中啟用指標：

1. 瀏覽至儲存體帳戶。
1. 從 **"功能表"** 窗格中選擇**診斷設置（經典）。**
1. 確定 [狀態]**** 設為 [開啟]****。
1. 選取您要監視之服務的計量。
1. 指定保留原則，以表示要保留計量與記錄資料的時間。
1. 選取 [儲存]****。

[Azure 入口網站](https://portal.azure.com)目前無法讓您在儲存體帳戶中設定每分鐘計量功能，您必須使用 PowerShell 或以程式設計方式啟用每分鐘計量功能。

## <a name="enable-storage-metrics-using-powershell"></a>使用 PowerShell 啟用存儲指標  
您可以使用本地電腦上的 PowerShell 配置存儲帳戶中的存儲指標，使用 Azure PowerShell Cmdlet **Get-AzStorageServiceMetrics 屬性**來檢索當前設置，以及 Cmdlet **Set-AzStorageServiceMetrics 屬性**來更改當前設置。  

控制儲存體度量的 Cmdlet 會使用下列參數：  

* **服務類型**， 可能的值是**Blob、****佇列**、**表**和**檔**。
* **指標類型**，可能的值是**小時**和**分鐘**。  
* **指標級別**， 可能的值是：
* **無**：關閉監視。
* **服務**：收集指標，如入口/出口、可用性、延遲和成功百分比，這些指標被聚合為 Blob、佇列、表和檔服務。
* **ServiceAndApi**： 除了服務指標之外，為 Azure 存儲服務 API 中的每個存儲操作收集相同的指標集。

例如，以下命令在存儲帳戶中的 Blob 服務的分鐘指標上切換，保留期設置為五天： 

> [!NOTE]
> 此命令假定您已使用 命令`Connect-AzAccount`登錄到 Azure 訂閱。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* 將`<resource-group-name>`預留位置值替換為資源組的名稱。
        
* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。



下列命令會擷取您預設儲存體帳戶中 Blob 服務的目前每小時度量層級和保留天數：  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱： [如何安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

## <a name="enable-storage-metrics-programmatically"></a>以程式設計方式啟用存儲指標  
除了使用 Azure 門戶或 Azure PowerShell Cmdlet 來控制存儲指標外，還可以使用 Azure 存儲 API 之一。 例如，若您使用 .NET 語言，則可使用儲存體用戶端程式庫。  

**類雲BlobClient、****雲佇列用戶端**、**雲桌用戶端**和**雲檔用戶端**都有集**服務屬性**和**集服務屬性Async**等方法，以**服務屬性**物件作為參數。 您可以使用 **ServiceProperties** 物件來設定「儲存體度量」。 例如，下列 C# 程式碼片段顯示如何變更每小時佇列度量的度量層級和保留期間：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

有關使用 .NET 語言配置存儲指標的詳細資訊，請參閱[.NET 的存儲用戶端庫](https://msdn.microsoft.com/library/azure/mt347887.aspx)。  

有關使用 REST API 配置存儲指標的一般資訊，請參閱[啟用和配置存儲分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)。  

##  <a name="viewing-storage-metrics"></a>檢視儲存體度量  
在您設定儲存體分析計量監視您的儲存體帳戶後，儲存體分析會將計量記錄在您儲存體帳戶中一組已知資料表中。 您可以將圖表配置為在[Azure 門戶](https://portal.azure.com)中查看每小時指標：

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的儲存體帳戶。
1. 在 **"功能表"** 邊欄選項卡中為要查看其指標的服務選擇**指標（經典**）。
1. 按一下要配置的圖表。
1. 在 [編輯圖表]**** 刀鋒視窗中，選取 [時間範圍]****、[圖表類型]**** 與您要在圖表中顯示的計量。

在 Azure 門戶中存儲帳戶功能表邊欄選項卡的 **"監視（經典）"** 部分中，可以配置[警報規則](#metrics-alerts)，例如發送電子郵件警報以在特定指標達到特定值時通知您。

如果要下載用於長期存儲的指標或在本地分析指標，則必須使用工具或編寫一些代碼來讀取表。 您必須下載每分鐘度量以進行分析。 如果您在儲存體帳戶中列出所有資料表，則資料表就不會出現，但您可以直接依名稱存取它們。 許多存儲流覽工具都瞭解這些表，並使您能夠直接查看它們（有關可用工具清單，請參閱[Azure 存儲用戶端工具](/azure/storage/storage-explorers)）。

||||  
|-|-|-|  
|**計量**|**表名稱**|**注意**|  
|每小時度量|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|在 2013 年 8 月 15 日前的版本中，這些資料表也稱為：<br /><br /> $MetricsTransactionsBlob <br /><br /> $MetricsTransactionsTable<br /><br />  $MetricsTransactionsQueue<br /><br /> 檔服務的指標從版本 2015-04-05 開始提供。|  
|每分鐘度量|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|只能使用 PowerShell 或以程式設計方式啟用。<br /><br /> 檔服務的指標從版本 2015-04-05 開始提供。|  
|Capacity|$MetricsCapacityBlob|僅限 Blob 服務。|  

您可以在 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)上找到這些資料表之結構描述的完整詳細資料。 下列資料列範例只會顯示可用的資料行子集，但可說明儲存體度量儲存這些度量資訊之方式的一些重要功能：  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**行鍵**|**時間 戳**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**可用性**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

在這個每分鐘度量資料範例中，資料分割索引鍵會在每分鐘解析中使用時間。 資料列索引鍵會識別資料列中儲存的資訊類型，而這是由兩部分的資訊 (存取類型及要求類型) 所組成：  

-   存取類型不是 **user** 就是 **system**，其中 **user** 是指對儲存體服務的所有使用者要求，而 **system** 是指「儲存體分析」所提出的要求。  

-   要求類型為 **all** (此時是一個摘要資料行)，或可是別特定 API (例如 **QueryEntity** 或 **UpdateEntity**)。  

以上範例資料顯示單一分鐘的所有記錄 (從上午 11:00 開始)，所以 **QueryEntities** 要求數目加上 **QueryEntity** 要求數目加上 **UpdateEntity** 要求數目等於 7，這就是 **user:All** 資料列上顯示的總計。 同樣地，您可以計算 ((143.8 * 5) + 3 + 9)/7，在 **user:All** 資料列上導出平均端對端延遲 104.4286。  

## <a name="metrics-alerts"></a>計量警示
應考慮在[Azure 門戶](https://portal.azure.com)中設置警報，以便自動通知您存儲服務行為的重要更改。 若使用儲存體總管工具來下載此計量資料 (以使用分隔字元分隔的形式)，您可以使用 Microsoft Excel 來分析資料。 如需可用的儲存體總管工具清單，請參閱 [Azure 儲存體用戶端工具](/azure/storage/storage-explorers)。 您可以在 **"警報（經典）"** 邊欄選項卡中配置警報，可在"存儲帳戶"功能表邊欄選項卡中的 **"監視（經典）"** 下訪問警報。

> [!IMPORTANT]
> 發生儲存體事件與記錄對應的每小時或分鐘計量資料之間可能會有延遲。 就分鐘計量而言，可能會一次寫入數分鐘的資料。 這可能導致將來自較早分鐘的交易彙總至目前分鐘的交易。 當發生這種情況時，警示服務可能不會擁有所設定警示間隔的所有可用計量資料，這可能導致非預期地觸發警示。
>

## <a name="accessing-metrics-data-programmatically"></a>以程式設計方式存取度量資料  
下列清單顯示 C# 程式碼範例，其會針對某個分鐘範圍存取每分鐘度量，並將結果顯示在主控台視窗中。 代碼示例使用 Azure 存儲用戶端庫版本 4.x 或更高版本，其中包括簡化訪問存儲中的指標表的**CloudAnalyticsClient**類。  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>對存儲指標計費  
寫入要求以建立度量的資料表實體，會以適用於所有 Azure 儲存體作業的標準費率來收費。  

用戶端讀取和刪除指標資料的請求也可以按標準費率計費。 如果您已設定資料保留原則，就不需要在 Azure 儲存體刪除舊的度量資料時付費。 不過，如果您刪除分析資料，則您的帳戶必須支付刪除作業的費用。  

指標表使用的容量也是計費的。 您可以使用以下內容來估計用於存儲指標資料的容量量：  

-   如果服務利用每個服務中的每個 API 每小時，則如果啟用了服務和 API 級別的摘要，則大約每小時在指標事務表中存儲大約 148KB 的資料。  
-   如果服務在每一小時內利用服務中的每個 API，則如果啟用了服務等級摘要，則大約每小時在指標事務表中存儲大約 12KB 的資料。  
-   Blob 的容量表每天添加兩行，前提是您已選擇登錄日誌。 這意味著，此表的大小每天增加約 300 位元組。

## <a name="next-steps"></a>後續步驟
* [如何監視儲存體帳戶](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [儲存體分析記錄](storage-analytics-logging.md)
