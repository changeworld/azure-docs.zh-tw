---
title: 監視 Azure IoT 中樞
description: 從這裡開始瞭解如何監視 Azure IoT 中樞
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: 577ec9e01310787dacb45a03e1efae128cbab872
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078215"
---
# <a name="monitoring-azure-iot-hub"></a>監視 Azure IoT 中樞

當您有依賴 Azure 資源的重要應用程式和商務程序時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure IoT 中樞所產生的監視資料，以及您可以如何使用 Azure 監視器的功能來分析此資料併發出警示。

## <a name="monitor-overview"></a>監視概觀

每個 IoT 中樞的 Azure 入口網站中的 **總覽** 頁面包含可提供一些使用計量的圖表，例如使用的訊息數目，以及連線到 IoT 中樞的裝置數目。

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="IoT 中樞總覽頁面上的預設度量圖表。":::

這項資訊很有用，但只代表可供 IoT 中樞使用的少量監視資料。 某些監視資料會自動收集，並且可在您建立 IoT 中樞時立即進行分析。 您可使用一些設定來啟用其他類型的資料收集。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？

Azure IoT 中樞使用 [Azure 監視器](/azure/azure-monitor/overview)來建立監視資料，這是 Azure 中的完整堆疊監視服務，除了其他雲端和內部部署中的資源之外，還提供一組完整的功能來監視您的 azure 資源。

請從 [使用 Azure 監視器來監視 Azure 資源](/azure/azure-monitor/insights/monitor-azure-resource)的文章開始，其中說明下列概念：

- Azure 監視器是什麼？
- 與監視相關聯的成本
- 在 Azure 中收集的監視資料
- 設定資料收集
- Azure 中用來分析和警示監視資料的標準工具

下列各節將說明針對 Azure IoT 中樞所收集的特定資料，並提供使用 Azure 工具來設定資料收集和分析此資料的範例。

## <a name="monitoring-data"></a>監視資料

Azure IoT 中樞會收集與 [azure 資源監視資料](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources)中所述的其他 Azure 資源相同的監視資料類型。

如需 Azure IoT 中樞所建立的計量和記錄的詳細資訊，請參閱 [監視 Azure IoT 中樞資料參考](monitor-iot-hub-reference.md) 。

> [!IMPORTANT]
> 使用 Azure 監視器資源記錄檔的 IoT 中樞服務所發出的事件，不保證是可靠或排序的。 某些事件可能會遺失，或未按順序傳遞。 資源記錄也不是即時的，而且可能需要幾分鐘的時間，才會將事件記錄到您選擇的目的地。

## <a name="collection-and-routing"></a>收集和路由

系統會自動收集和儲存平臺計量和活動記錄檔，但是可以使用診斷設定將其路由至其他位置。

在您建立診斷設定並將其路由傳送至一或多個位置之前，不會收集並儲存資源記錄。

計量和記錄可以路由傳送到數個位置，包括：
- Azure 監視器會透過相關聯的 Log Analytics 工作區來記錄存放區。 您可以使用 Log Analytics 進行分析。
- 用於封存和離線分析的 Azure 儲存體 
- 可供外部應用程式讀取的事件中樞端點，例如協力廠商 SIEM 工具。

在 Azure 入口網站中，您可以在 IoT 中樞的左側窗格中選取 [ **監視** ] 底下的 **診斷** 設定，然後選取 [ **新增診斷設定** ]，以建立您的 iot 中樞所發出的記錄和平臺計量範圍的診斷設定。

下列螢幕擷取畫面顯示診斷設定，可將資源記錄類型的連線 *作業* 和所有平臺計量路由傳送至 log Analytics 工作區。

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="IoT 中樞總覽頁面上的預設度量圖表。":::

如需使用 Azure 入口網站、CLI 或 PowerShell 建立診斷設定的詳細程式，請參閱 [建立診斷設定以收集 Azure 中的平臺記錄和計量](/azure/azure-monitor/platform/diagnostic-settings) 。 當您建立診斷設定時，可以指定要收集的記錄類別。 Azure IoT 中樞的類別列在 [[監視 Azure IoT 中樞資料參考](monitor-iot-hub-reference.md#resource-logs)] 的 [資源記錄檔] 底下。

將 IoT 中樞平臺計量路由至其他位置時，請注意：

- 下列平臺計量無法透過診斷設定匯出： *已連線的裝置 (預覽)* 和 *裝置總數 (預覽)* 。

- 多維度計量（例如某些 [路由計量](monitor-iot-hub-reference.md#routing-metrics)）目前會匯出為跨維度值匯總的簡維單一維度計量。 如需詳細資訊，請參閱 [將平臺計量匯出至其他位置](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations)。

## <a name="analyzing-metrics"></a>分析計量

您可以從 [ **Azure 監視器** ] 功能表開啟 [ **計量** ]，以使用計量瀏覽器來分析來自其他 Azure 服務的計量 Azure IoT 中樞計量。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started)。

在 Azure 入口網站中，您可以在 IoT 中樞的左側窗格中選取 [ **監視** ] 底下的 **計量** ，以依預設將計量瀏覽器範圍設定為 iot 中樞所發出的平臺計量：

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="IoT 中樞總覽頁面上的預設度量圖表。":::

如需針對 Azure IoT 中樞收集的平臺度量清單，請參閱 [監視 Azure IoT 中樞資料參考中的計量](monitor-iot-hub-reference.md#metrics)。 如需針對所有 Azure 服務收集的平臺度量清單，請參閱 [支援的 Azure 監視器計量](/azure/azure-monitor/platform/metrics-supported)。

針對以計數單位收集的 IoT 中樞平臺計量，某些匯總可能無法使用或無法使用。 若要深入瞭解，請參閱 [監視 Azure IoT 中樞資料參考中支援的](monitor-iot-hub-reference.md#supported-aggregations)匯總。

某些 IoT 中樞計量（例如 [路由計量](monitor-iot-hub-reference.md#routing-metrics)）是多維度。 針對這些計量，您可以根據維度將 [篩選](/azure/azure-monitor/platform/metrics-charts#apply-filters-to-charts) 和 [分割](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart) 套用至您的圖表。

## <a name="analyzing-logs"></a>分析記錄

Azure 監視器記錄檔中的資料會儲存在資料表中，其中每個資料表都有自己的唯一屬性集。 這些資料表中的資料會與 Log Analytics 工作區相關聯，並可在 Log Analytics 中查詢。 若要深入瞭解 Azure 監視器記錄，請參閱 Azure 監視器檔中的 [Azure 監視器記錄檔總覽](/azure/azure-monitor/platform/data-platform-logs) 。 

若要將資料路由傳送至 Azure 監視器記錄，您必須建立診斷設定，以將資源記錄或平臺計量傳送至 Log Analytics 工作區。 若要深入瞭解，請參閱 [收集和路由](#collection-and-routing)。

在 Azure 入口網站中，您可以在 IoT 中樞的左側窗格中選取 [ **監視** ] 底下的 **[記錄檔** ]，依預設針對您的 iot 中樞 Azure 監視器記錄檔中所收集的記錄和計量執行 Log Analytics 查詢。

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="IoT 中樞總覽頁面上的預設度量圖表。":::

如需 Log Analytics Azure 監視器記錄和可查詢的資料表清單，請參閱 [監視 Azure IoT 中樞資料參考中 Azure 監視器記錄資料表](monitor-iot-hub-reference.md#azure-monitor-logs-tables)。

Azure 監視器中的所有資源記錄都有相同的欄位，後面接著服務特定的欄位。 一般架構 [Azure 監視器資源記錄架構](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-schema#top-level-resource-logs-schema)中所述。 您可以在 [監視 Azure IoT 中樞資料參考的資源記錄檔中](monitor-iot-hub-reference.md#resource-logs)，找到針對 Azure IoT 中樞收集的資源記錄的架構和類別。

[活動記錄](/azure/azure-monitor/platform/activity-log)是 Azure 中的平臺記錄，可提供訂用帳戶層級事件的見解。 您可以單獨加以查看，或將它路由傳送至 Azure 監視器記錄檔，您可以在其中使用 Log Analytics 來進行更複雜的查詢。  

將 IoT 中樞平臺計量路由傳送至 Azure 監視器記錄時，請注意：

- 下列平臺計量無法透過診斷設定匯出： *已連線的裝置 (預覽)* 和 *裝置總數 (預覽)* 。

- 多維度計量（例如某些 [路由計量](monitor-iot-hub-reference.md#routing-metrics)）目前會匯出為跨維度值匯總的簡維單一維度計量。 如需詳細資訊，請參閱 [將平臺計量匯出至其他位置](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations)。

如需使用 IoT 中樞的一些常見查詢，請參閱 [範例 Kusto 查詢](#sample-kusto-queries)。 如需使用 Log Analytics 查詢的詳細資訊，請參閱 [Azure 監視器中的記錄查詢總覽](/azure/azure-monitor/log-query/log-query-overview)。

### <a name="sdk-version-in-iot-hub-logs"></a>IoT 中樞記錄中的 SDK 版本

IoT 中樞資源記錄中的某些作業會傳回 `sdkVersion` 其物件中的屬性 `properties` 。 針對這些作業，當裝置或後端應用程式使用其中一個 Azure IoT Sdk 時，此屬性會包含所使用之 SDK 的相關資訊、SDK 版本，以及 SDK 執行所在的平臺。 下列範例顯示 `sdkVersion` [`deviceConnect`](monitor-iot-hub-reference.md#connections) 使用 Node.js 裝置 SDK 時，為作業發出的屬性： `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` 。 以下是針對 .NET (c # ) SDK 所發出的值範例： `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` 。

下表顯示用於不同 Azure IoT Sdk 的 SDK 名稱：

| SdkVersion 屬性中的 SDK 名稱 | 語言 |
|----------|----------|
| .NET | .NET (C#) |
| microsoft azure. 裝置 | .NET (c # ) service SDK |
| microsoft azure. 用戶端 | .NET (c # ) 裝置 SDK |
| >iothubclient | C 或 Python v1 (已淘汰) 裝置 SDK |
| iothubserviceclient | C 或 Python v1 (已淘汰) service SDK |
| azure-iot-裝置-iothub-.py | Python 裝置 SDK |
| azure-iot-device | Node.js 裝置 SDK |
| azure-iothub | Node.js 服務 SDK |
| iothub-java-用戶端 | JAVA 裝置 SDK |
| iothub.. a. sdk | JAVA 服務 SDK |
| .com. iot-裝置-用戶端 | JAVA 裝置 SDK |
| .com. iot-服務-用戶端 | JAVA 服務 SDK |
| C | 內嵌 C |
| C + (OSSimplified = Azure RTO)  | Azure RTOS |

當您對 IoT 中樞資源記錄執行查詢時，可以解壓縮 SDK 版本屬性。 例如，下列查詢會從連接作業所傳回的屬性中，解壓縮 SDK 版本屬性 (和裝置識別碼) 。 這兩個屬性會連同作業的時間以及裝置所連線的 IoT 中樞資源識別碼，寫入結果中。

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>範例 Kusto 查詢

> [!IMPORTANT]
> 當您從 [IoT 中樞] 功能表選取 [ **記錄** ] 時，會開啟 Log Analytics，並將查詢範圍設定為目前的 IoT 中樞。 這表示記錄查詢只會包含來自該資源的資料。 如果您想要執行的查詢包含來自其他 IoT 中樞的資料，或來自其他 Azure 服務的資料，請從 [ **Azure 監視器** ] 功能表中選取 [ **記錄** ]。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](/azure/azure-monitor/log-query/scope/)。

以下是您可以用來協助監視 IoT 中樞的查詢。

- 連線錯誤：找出裝置連接錯誤。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- 節流錯誤：找出造成節流錯誤的最多要求裝置。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- 無作用端點：依回報問題的次數，找出無效或狀況不良的端點，以及原因。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- 錯誤摘要：依類型在所有作業中的錯誤計數。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- 最近連線的裝置： IoT 中樞在指定的時間週期內看到連線的裝置清單。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- 裝置的 SDK 版本：裝置的清單及其 SDK 版本，適用于裝置連線或裝置到雲端對應項作業。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>從 Azure 事件中樞讀取記錄

在透過診斷設定來設定事件記錄功能之後，您可以建立會將記錄讀出的應用程式，以便根據記錄中的資訊來採取行動。 以下範例程式碼會從事件中樞擷取記錄：

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>警示

當您在監視資料中找到重要的條件時，Azure 監視器警示會主動通知您。 它們可讓您識別並解決您的系統中的問題，然後客戶才會注意到這些問題。 您可以設定 [計量](/azure/azure-monitor/platform/alerts-metric-overview)、 [記錄](/azure/azure-monitor/platform/alerts-unified-log)和 [活動記錄](/azure/azure-monitor/platform/activity-log-alerts)的警示。 不同類型的警示有其優點和缺點。

根據平臺計量建立警示規則時，請注意，針對以計數單位收集的 IoT 中樞平臺計量，某些匯總可能無法使用或無法使用。 若要深入瞭解，請參閱 [監視 Azure IoT 中樞資料參考中支援的](monitor-iot-hub-reference.md#supported-aggregations)匯總。

## <a name="next-steps"></a>下一步

- 如需 [服務名稱] 所建立的計量、記錄和其他重要值的參考，請參閱 [監視 Azure IoT 中樞資料參考](monitor-iot-hub-reference.md) 。

- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](/azure/azure-monitor/insights/monitor-azure-resource)。
