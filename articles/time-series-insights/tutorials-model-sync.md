---
title: Azure 數位 Twins 與時間序列深入解析之間的模型同步處理 |Microsoft Docs
description: 用來將 ADT 中的資產模型轉譯為 Azure TSI 中資產模型的最佳作法和工具
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: dpalled
ms.openlocfilehash: c3948a5bdfce583384992fb87bf40e9e7251974d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342374"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Azure Digital Twins 與 Azure 時間序列深入解析 Gen2 之間的模型同步處理

本文說明用來將 Azure 數位 Twins 中的資產模型轉譯 (ADT) 至 Azure 時間序列深入解析 (TSI) 中資產模型的最佳作法和工具。  本文是兩部分教學課程系列的第二部分，說明 Azure 數位 Twins 與 Azure 時間序列深入解析的整合。 Azure 數位 Twins 與時間序列深入解析的整合，可讓您封存和追蹤數位 Twins 的遙測和計算屬性歷程記錄。 這一系列的教學課程的目標是開發人員在使用 Azure 數位 Twins 來整合時間序列深入解析。 第1部分說明  [如何建立資料管線，以將實際的時間序列資料從 Azure 數位 Twins 帶到時間序列深入](https://docs.microsoft.com/azure/digital-twins/how-to-integrate-time-series-insights) 解析，而這是教學課程系列的第二個部分說明 Azure 數位 Twins 與時間序列深入解析之間的資產模型同步處理。 本教學課程說明選擇和建立時間序列識別碼的命名慣例 (TS 識別碼) 的最佳作法，並在時間序列模型中手動建立階層)  (的。

## <a name="choosing-a-time-series-id"></a>選擇時間序列識別碼

時間序列識別碼是唯一識別碼，用來識別時間序列深入解析中的資產。 時間序列資料 (從欄位遙測，也就是時間值組) 使用 TSID 下所列的變數來表示。 在 Azure 數位 Twins 中，對應項屬性和遙測會用來表示對應項的狀態，以及對應項分別產生的測量值。 從目前的 TSM 設計，Tsid 必須是唯一的。 在 Azure 數位 Twins 中使用 twins 的對應項識別碼，或結合屬性或遙測名稱，一律會在 TSM 中建立唯一的 TS 識別碼。 在一般情況下， **_`<Twin ID>`_** 將會是 TSID，而屬性和遙測名稱將會是 TSM 中的變數。 不過，在某些情況下，我們偏好使用複合索引鍵格式來簡維時間序列深入解析中的資產階層，例如 **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** 。 我們來看一個範例來說明之後的案例。 請考慮建立模型化為對應項且具有對應項識別碼 Room22 的房間。 其溫度設定屬性會轉譯為 **_TSID Room22_TempSetting_** 和溫度測量，以轉譯為 TSM 中的 **_Room22_TempMea_** 。

[![選擇時間序列識別碼](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>CoNtextualizing 時間序列

在時間序列深入解析中，對脈絡化資料 (大多是空間) 在時間序列深入解析中都是透過資產階層來達成，而且也可透過時間序列深入解析瀏覽器中的樹狀檢視，用來輕鬆地流覽資料。 時間序列類型和階層是使用時間序列模型來定義， (在時間序列深入解析中的 TSM) 。 TSM 中的類型有助於定義變數，而階層層級和實例域值是用來在時間序列深入解析瀏覽器中建立樹狀檢視。 如需有關 TSM 的詳細資訊，請參閱 [線上時間序列深入解析檔](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview)。

在 Azure 數位 Twins 中，資產之間的連接會使用對應項關聯性來表示。 對應項關聯性只是連線資產的圖形。 不過，在時間序列深入解析中，資產之間的關聯性本質上是階層式的。 也就是，資產會共用父子類型的 od 關聯性，並使用樹狀結構來表示。 若要將 Azure 數位 Twins 中的關聯性資訊轉譯為時間序列深入解析階層，我們需要選擇 Azure 數位 Twins 的相關階層式關聯性。 Azure 數位 Twins 使用稱為數位對應項定義語言 (DTDL) 的開放式標準模型語言。 在 DTDL 模型中，會使用稱為 JSON-LD 的 JSON 變數來描述。 如需規格的完整詳細資訊，請參閱 [DTDL 檔](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 。

[![資產之間的連接](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>在時間序列深入解析中將 Azure 數位 Twins 中的圖表表示轉譯為樹狀結構

本教學課程的下列各節將介紹一些核心案例，其中會以手動方式將 Azure 數位 Twins 中的圖形結構轉譯為時間序列深入解析中的樹狀結構。

範例系統：本教學課程會使用下列範例來說明下面討論的概念。 它是一個簡單的虛構大樓管理系統，其中有一個樓層和兩個房間。 它有三個恒溫器、，每個房間都有一個，而另一個則是樓層共同。 此外，它也有一個水流程計量，它會透過房間之間的管道連接，測量從 Room21 到 Room22 的水流程。 查看 twins 之間的空間關聯性時，它有兩種類型的關聯性。

1. 最常見的階層式關聯性。 例如，Building40-> Floor01-> FloorTS *-> 溫度
1. 不是一般的迴圈關聯性。 例如，從 Building40 開始，FlowMtr 可以透過兩個不同的路徑來追蹤。

   1. Building40-> Floor01-> Room21-> FlowMtr *-> 流程
   1. Building40-> Floor01-> Room22-> FlowMtr *-> 流程  
      其中「流程」是測量 Room21 與 Room22 之間的水流程的實際遙測

> [!Note]
>
> `*` FloorTS 代表 FloorThermoStat，而 FlowMtr 代表 Flow 計量，通常選擇為 TSID。 溫度和流程是在時間序列深入解析中參考為變數的原始遙測。

假設目前的時間序列深入解析限制，其中一個資產無法在多個分支中表示，下列各節將說明時間序列深入解析中階層式和迴圈關聯性的模型。

## <a name="case-1-hierarchical-parent-child-relationship"></a>案例1：階層式 (父子式) 關聯性

這是 twins 之間最常見的關聯性類型。 下圖說明建立純父子式關聯性的模型。 實例欄位和 TSID 衍生自對應項識別碼，如下所示。 雖然可以使用時間序列深入解析 explorer 手動更新實例欄位，但以下的「使用 Api 更新實例欄位」一節說明如何使用 Azure 函式來接聽 Azure 數位 Twins 中的模型變更，以及更新時間序列深入解析中的實例欄位。

[![對應對應項識別碼](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![對應對應項識別碼2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>案例2：迴圈關聯性

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>在時間序列深入解析中，Azure 數位 Twins 與單一階層關聯性的迴圈關聯性

假設 TSID 必須是唯一的，而且只能在一個階層中表示，則此案例代表 _' FlowMtr '_ ，其遙測資料 _‘Flow’_ 位於對應項 _' Room21 '_ 下的遙測。 未來當時間序列深入解析可支援在 TSM 中多次表示時間序列時，遙測「_流程_」會以「_房間 21_ 」和「_房間 22_ 」表示

下列螢幕擷取畫面顯示如何以手動方式將 Azure 數位 Twins 中的對應項識別碼對應至 TSM 中的實例欄位，以及在時間序列深入解析中產生的階層。

[![在 Azure 數位 Twins 中對應對應項識別碼](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>使用重複專案將 Azure 數位 Twins 中的多個階層迴圈關聯到時間序列深入解析中的多個階層

本教學課程的第1部分說明用戶端程式如何 (Azure 函式) 協助將遙測資料從 IoT 中樞或其他事件來源傳輸到 Azure 數位 Twins。 這種方法建議使用相同的用戶端程式來更新父 twins 的相關屬性。 在指定的範例中，從 IoT 中樞讀取 FlowMtr 遙測，並在 FlowMtr 對應項中更新屬性 "Flow" 時，程式也可以在來源的所有可能父 twins 中更新對應的屬性。 在我們的範例中，它會是「outflowmea」 (使用 Room21 的「流出」關聯性) 屬性和 Room22 的 "inflowmea" 屬性來識別。  下列螢幕擷取畫面顯示時間序列深入解析瀏覽器的最終使用者體驗。 您必須注意，我們會採取這種方法，讓資料重複。

[![時間序列深入解析總管](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

下列程式碼片段顯示用戶端應用程式如何使用 Azure 數位 Twins Api 來流覽對應項關聯性。

> [!Note]
>
> 此程式碼片段範例假設讀者熟悉本教學課程的 [第01部分](https://docs.microsoft.com/azure/digital-twins/tutorial-end-to-end#set-up-the-sample-function-app) ，並在 "ProcessHubToDTEvents" 函式內進行這段程式碼變更。

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>使用 Api 更新實例欄位

本教學課程的這一節說明如何使用時間序列深入解析模型 Api，以程式設計方式接聽 Azure 數位 Twins 中的模型變更，例如建立、刪除 Twins 或變更 Twins 和更新實例欄位和階層之間的關聯性。 此更新時間序列深入解析模型的方法通常會透過 Azure 函式來達成。 在 Azure 數位 Twins 中，事件通知（例如對應項新增或刪除）可以路由傳送下游服務，例如事件中樞，接著可以將這些服務送到 Azure 函式。 如需事件路由和篩選的進一步詳細資料，請參閱 [這裡](https://docs.microsoft.com/azure/digital-twins/how-to-manage-routes-portal)。  本節其餘部分將說明如何使用 Azure 函式中的時間序列深入解析模型 Api 來更新時間序列深入解析模型，以回應對應項新增 (一種模型變更) 在 Azure 數位 Twins 中。

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>接收和識別對應項新增事件通知

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>建立時間序列深入解析用戶端和新增實例詳細資料

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>將階層資訊套用至實例

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>後續步驟

本教學課程系列的第三個教學課程說明如何使用時間序列深入解析 Api，從 Azure 數位 Twins 查詢歷程記錄資料。 這是進行中的工作，而且區段會在準備就緒時進行更新。 在此同時，建議讀者參考 [時間序列深入解析資料查詢 API 檔](https://docs.microsoft.com/azure/time-series-insights/concepts-query-overview)。
