---
title: Azure 儲存體分析計量 (傳統)
description: 了解如何在 Azure 儲存體中使用儲存體分析計量。 瞭解交易和容量計量、計量的儲存方式、啟用計量等等。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 7d7db5a756e5d75cb4f9719f54d95f9cee1e8d2f
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828042"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 儲存體分析計量 (傳統)

Azure 儲存體使用儲存體分析解決方案儲存包含儲存體服務要求相關彙總的交易統計資料及容量資料的計量。 交易會在 API 作業層級和儲存體服務層級報告。 容量會在儲存體服務層級報告。 計量資料可以用來：
- 分析儲存體服務使用量。
- 診斷對儲存體服務提出的要求所發生的問題。
- 改善使用服務的應用程式所達到的效能。

 預設會為新的儲存體帳戶啟用儲存體分析度量。 您可以在 [Azure 入口網站](https://portal.azure.com/)中設定這些計量。 如需詳細資訊，請參閱 [在 Azure 入口網站中監視儲存體帳戶](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用 [設定服務屬性] 作業，為各服務啟用儲存體分析。  

> [!NOTE]
> 儲存體分析計量可供 Azure Blob 儲存體、Azure 佇列儲存體、Azure 資料表儲存體和 Azure 檔案儲存體使用。
> 儲存體分析計量現在是傳統計量。 我們建議您[在 Azure 監視器中使用儲存體計量](monitor-storage.md)，而不是儲存體分析計量。

## <a name="transaction-metrics"></a>交易度量  
 系統會以每小時或每分鐘的時間間隔，為每個儲存體服務和要求的 API 作業記錄完善的資料集，其中包括輸入流量和輸出流量、可用性、錯誤，以及已分類的要求百分比。 如需完整的交易詳細資料清單，請參閱[儲存體分析計量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

 記錄的交易資料屬於服務層級和 API 作業層級。 在服務層級，系統每小時都會將彙總所有要求的 API 作業的統計資料寫入資料表實體，即使未對該服務提出任何要求也一樣。 在 API 作業層級，只有在該小時內對該作業提出要求時，才會將統計資料寫入實體。  

 例如，如果您在 Blob 服務上執行 **GetBlob** 作業，儲存體分析計量將會記錄要求，然後將它納入 Blob 服務和 **GetBlob** 作業的彙總資料中。 如果在該時段內未要求 **GetBlob** 作業，則不會將實體寫入該作業的 *$MetricsTransactionsBlob*。  

 系統會針對使用者要求及儲存體分析本身所提出的要求記錄交易計量。 例如，會記錄儲存體分析寫入記錄和資料表實體的要求。

## <a name="capacity-metrics"></a>容量度量  

> [!NOTE]
>  容量計量目前僅適用於 Blob 服務。

 系統每日都會針對儲存體帳戶的 Blob 服務記錄容量資料，並寫入兩個資料表實體。 其中一個實體會提供使用者資料的統計資料，而另一個實體則會提供有關儲存體分析所使用之 `$logs` Blob 容器的統計資料。 *$MetricsCapacityBlob* 資料表包含下列統計資料：  

- **Capacity**：儲存體帳戶 Blob 服務所使用的儲存體量 (位元組)。  
- **ContainerCount**：儲存體帳戶的 Blob 服務中的 Blob 容器數目。  
- **ObjectCount**：儲存體帳戶的 Blob 服務中的認可及未認可區塊或分頁 Blob 的數目。  

  如需容量計量的詳細資訊，請參閱[儲存體分析計量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

## <a name="how-metrics-are-stored"></a>度量的儲存方式  

 每個儲存體服務的所有計量資料都會儲存在對於該服務保留的三份資料表。 一個資料表用於交易資訊，一個資料表用於分鐘交易資訊，另一個資料表用於容量資訊。 交易和每分鐘交易資訊都是由要求和回應資料所組成。 容量資訊是由儲存體使用量資料所組成。 儲存體帳戶的 Blob 服務的每小時計量、每分鐘計量及容量，均可從下表中具有相對應名稱的資料表中取得。  

|度量層級|資料表名稱|支援版本|  
|-------------------|-----------------|----------------------------|  
|每小時度量，主要位置|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|僅 2013 年 8 月 15 日之前的版本。 儘管目前仍支援這些名稱，但還是建議您改用下列資料表。|  
|每小時度量，主要位置|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|所有版本。 2015 年 4 月 5 日的版本以及更新版本才提供檔案服務計量的支援。|  
|每分鐘度量，主要位置|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|所有版本。 2015 年 4 月 5 日的版本以及更新版本才提供檔案服務計量的支援。|  
|每小時度量，次要位置|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|每分鐘度量，次要位置|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|容量 (僅限 Blob 服務)|$MetricsCapacityBlob|所有版本。|  

 針對儲存體服務端點啟用儲存體分析時，即會自動建立這些資料表。 您可以透過儲存體帳戶的命名空間存取它們，例如：`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`。 計量資料表不會出現在清單作業中，而且必須直接透過資料表名稱存取。

## <a name="enable-metrics-by-using-the-azure-portal"></a>使用 Azure 入口網站啟用計量功能
依照下列步驟在 [Azure 入口網站](https://portal.azure.com)中啟用計量功能：

1. 移至您的儲存體帳戶。
1. 選取 [功能表] 窗格中的 [診斷設定 (傳統)]。
1. 確定 [狀態] 設為 [開啟]。
1. 對於您想要監視的服務選取計量。
1. 指定保留原則，以表示要保留計量與記錄資料的時間。
1. 選取 [儲存]。

[Azure 入口網站](https://portal.azure.com)目前無法讓您在儲存體帳戶中設定分鐘計量。 您必須使用 PowerShell 或以程式設計方式啟用分鐘計量。

## <a name="enable-storage-metrics-by-using-powershell"></a>使用 PowerShell 啟用儲存體計量  
您可以在本機電腦上使用 PowerShell 來設定儲存體帳戶中的儲存體計量，做法是使用 Azure PowerShell Cmdlet **Get-AzStorageServiceMetricsProperty** 來擷取目前的設定。 使用 **Set-AzStorageServiceMetricsProperty** 變更目前的設定。  

控制儲存體計量的 Cmdlet 會使用下列參數：  

* **ServiceType**：可能值為 **Blob**、**Queue**、**Table** 和 **File**。
* **MetricsType**：可能的值為 **Hour** 和 **Minute**。  
* **MetricsLevel**：可能的值包括：
   * **無**：關閉監視。
   * **服務**：可收集針對 Blob、佇列、資料表和檔案服務彙總的計量 (例如入口流量和出口流量、可用性、延遲和成功百分比)。
   * **ServiceAndApi**：除了服務計量之外，在 Azure 儲存體服務 API 中也針對每個儲存體作業收集相同的計量集合。

例如，下列命令會在您的儲存體帳戶中，為 Blob 服務開啟每分鐘計量功能，並將保留期間設為五天： 

> [!NOTE]
> 此命令假設您已使用 `Connect-AzAccount` 命令登入 Azure 訂閱。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* 將 `<resource-group-name>` 預留位置值取代為您的資源群組名稱。      
* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。



下列命令會擷取您預設儲存體帳戶中 Blob 服務的目前每小時度量層級和保留天數：  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂閱，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

## <a name="enable-storage-metrics-programmatically"></a>以程式設計方式啟用儲存體計量  
除了使用 Azure 入口網站或 Azure PowerShell Cmdlet 來控制「儲存體計量」，您也可使用其中一個 Azure 儲存體 API。 例如，若您使用 .NET 語言，則可使用 Azure 儲存體用戶端程式庫。  

**CloudBlobClient**、**CloudQueueClient**、**CloudTableClient** 和 **CloudFileClient** 類別全都有方法，例如以 **ServiceProperties** 物件作為參數的 **SetServiceProperties** 和 **SetServicePropertiesAsync**。 您可以使用 **ServiceProperties** 物件來設定「儲存體計量」。 例如，下列 C# 程式碼片段顯示如何變更每小時佇列計量的計量層級和保留期間：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

如需使用 .NET 語言來設定「儲存體計量」的詳細資訊，請參閱 [.NET 的 Azure 儲存體用戶端程式庫](https://msdn.microsoft.com/library/azure/mt347887.aspx)。  

如需使用 REST API 設定「儲存體計量」的一般資訊，請參閱[啟用及設定儲存體分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)。  

##  <a name="view-storage-metrics"></a>檢視儲存體計量  
在您設定儲存體分析計量監視您的儲存體帳戶後，儲存體分析會將計量記錄在您儲存體帳戶中一組已知資料表中。 您可以在 [Azure 入口網站](https://portal.azure.com)中將圖表設定為檢視每小時計量：

1. 移至 [Azure 入口網站](https://portal.azure.com)中您的儲存體帳戶。
1. 針對您要檢視其計量的服務，選取 [功能表] 窗格中的 [計量 (傳統)]。
1. 選取您要設定的圖表。
1. 在 [編輯圖表] 窗格中，選取 [時間範圍]、[圖表類型] 與您要在圖表中顯示的計量。

在 Azure 入口網站的儲存體帳戶功能表窗格之中的 [監視 (傳統)] 區段，您可以設定[警示規則](#metrics-alerts)。 例如，您可以傳送電子郵件警示，在特定的計量達到特定值時通知您。

如果要下載長期儲存體的計量，或在本機加以分析，您必須使用工具或撰寫程式碼來讀取資料表。 您必須下載每分鐘度量以進行分析。 如果您在儲存體帳戶中列出所有資料表，則資料表就不會出現，但您可以直接依名稱存取它們。 許多儲存體瀏覽工具可以感知這些資料表，讓您能夠直接檢視它們。 如需可用工具的清單，請參閱 [Azure 儲存體用戶端工具](/azure/storage/storage-explorers)。

|計量|資料表名稱|注意| 
|-|-|-|  
|每小時度量|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|在 2013 年 8 月 15 日前的版本中，這些資料表也稱為：<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> 從 2015 年 4 月 5 日的版本開始，提供檔案服務的計量。|  
|每分鐘度量|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|只能使用 PowerShell 或以程式設計方式啟用。<br /><br /> 從 2015 年 4 月 5 日的版本開始，提供檔案服務的計量。|  
|Capacity|$MetricsCapacityBlob|僅限 Blob 服務。|  

如需這些資料表的結構描述有關的完整詳細資料，請參閱[儲存體分析計量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)。 下列資料列範例只會顯示可用的資料行子集，但可說明儲存體計量儲存這些計量資訊之方式的一些重要功能：  

|PartitionKey|RowKey|時間戳記|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|可用性|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

在這個每分鐘計量資料範例中，資料分割索引鍵會在每分鐘解析中使用時間。 資料列索引鍵可識別儲存在資料列中的資訊類型。 資訊是由存取類型和要求類型組成：  

-   存取類型是**使用者**或**系統**，其中**使用者**是指對儲存體服務所提出的所有使用者要求，而**系統**是指對儲存體分析所提出的要求。  
-   要求類型就是**全部** (在此情況下它是摘要行)，或者它會識別特定的 API，例如 **QueryEntity** 或 **UpdateEntity**。  

這些範例資料會顯示單一分鐘 (從上午 11:00 開始) 的所有記錄，因此 **QueryEntities** 要求的數目加上 **QueryEntity** 要求的數目再加上 **UpdateEntity** 要求的數目總數為七個。 總數會在 **user:All** 資料列上顯示。 同樣地，您可以藉由計算 ((143.8 * 5) + 3 + 9)/7，在 **user:All** 資料列上衍生平均的端對端延遲 104.4286。  

## <a name="metrics-alerts"></a>計量警示
建議您考慮在 [Azure 入口網站](https://portal.azure.com)中設定警示，以便自動通知您有關儲存體服務行為的重要變更。 若使用儲存體總管工具來下載此計量資料 (以使用分隔字元分隔的形式)，您可以使用 Microsoft Excel 來分析資料。 如需可用的儲存體總管工具清單，請參閱 [Azure 儲存體用戶端工具](/azure/storage/storage-explorers)。 您可以在 [警示 (傳統)] 窗格 (可從 [儲存體帳戶] 功能表窗格中的 [監視 (傳統)] 下存取) 中設定警示。

> [!IMPORTANT]
> 發生儲存體事件與記錄對應的每小時或分鐘計量資料之間可能會有延遲。 就分鐘計量而言，可能會一次寫入數分鐘的資料。 這個問題可能導致將來自較早分鐘的交易彙總至目前分鐘的交易。 發生這個問題時，警示服務可能不會擁有所設定警示間隔的所有可用計量資料，這可能導致非預期地觸發警示。
>

## <a name="access-metrics-data-programmatically"></a>以程式設計方式存取計量資料  
下列清單顯示 C# 程式碼範例，其會針對某個分鐘範圍存取每分鐘計量，並將結果顯示在主控台視窗中。 程式碼範例會使用 Azure 儲存體用戶端程式庫 4.x 版或更高的版本，其中包含可簡化存取儲存體中之計量資料表的 **CloudAnalyticsClient** 類別。  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
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

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
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

## <a name="billing-on-storage-metrics"></a>儲存體計量的計費
寫入要求以建立度量的資料表實體，會以適用於所有 Azure 儲存體作業的標準費率來收費。  

用戶端對於計量資料的讀取和刪除要求也會以標準費率來計費。 如果您已設定資料保留原則，就不需要在 Azure 儲存體刪除舊的計量資料時付費。 如果您刪除分析資料，則您的帳戶必須支付刪除作業的費用。  

計量資料表所使用的容量也會計費。 使用下列資訊來計算用於儲存計量資料的容量：  

-   如果服務每小時會使用每個服務中的每種 API，若您啟用服務層級和 API 層級摘要，則每小時大約有 148 KB 的資料將儲存於計量交易資料表中。  
-   如果服務每小時會使用每個服務中的每種 API，若您僅啟用服務層級摘要，則每小時大約有 12 KB 的資料將儲存於計量交易資料表中。  
-   假設您已選擇加入記錄，Blob 的容量資料表每天會新增兩個資料列。 這個情節表示此資料表的大小每天會增加大約 300 個位元組。

## <a name="next-steps"></a>後續步驟
* [監視儲存體帳戶](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [儲存體分析計量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [儲存體分析記錄](storage-analytics-logging.md)
