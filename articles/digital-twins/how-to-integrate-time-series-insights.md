---
title: 與時間序列深入解析整合
titleSuffix: Azure Digital Twins
description: 請參閱如何設定從 Azure 數位 Twins 到 Azure 時間序列深入解析的事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131594"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>整合數位 Twins 與 Azure 時間序列深入解析

在此參考中，您將瞭解如何整合 Azure 數位 Twins 與[Azure 時間序列深入解析（TSI）](../time-series-insights/overview-what-is-tsi.md)。 此解決方案可讓您收集及分析 IoT 解決方案的歷程記錄資料。 Azure 數位 Twins 是將資料放入時間序列深入解析中的絕佳功能，因為它可讓您將多個資料流程相互關聯，並將您的資訊標準化，再將其傳送至時間序列深入解析。 

## <a name="solution-architecture"></a>解決方案架構

您將透過下列路徑，將時間序列深入解析附加至 Azure 數位 Twins。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="端對端案例中的 Azure 服務的觀點，反白顯示時間序列深入解析" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>必要條件

* 您需要可更新對應項資訊幾次的 Azure 數位 Twins 實例，才能查看時間序列深入解析中所追蹤的資料。 
    * 如果您沒有，請遵循 Azure 數位 Twins[*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)來設定 Azure 數位 Twins 實例和虛擬 IoT 裝置，以產生對應項變更。

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>建立路由並篩選對應項更新通知

每當更新對應項的狀態時，Azure 數位 Twins 實例就可以發出對應項[更新事件](how-to-interpret-event-data.md)。 您將會建立一個路由，將這些更新事件導向事件中樞以供進一步處理。

Azure 數位 Twins[*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)會逐步解說一個案例，其中的溫度計是用來更新附加至房間對應項的溫度屬性。 此模式會使用對應項更新，而不是從 IoT 裝置轉送遙測，這可讓您彈性地變更基礎資料來源，而不需要更新您的時間序列深入解析邏輯。

1. 建立事件中樞命名空間，它會從您的 Azure 數位 Twins 實例接收事件。 您可以使用以下的 Azure CLI 指示，或使用 Azure 入口網站：[*快速入門：使用 Azure 入口網站建立事件中樞*](../event-hubs/event-hubs-create.md)。

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. 建立事件中樞。

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. 建立具有 [傳送] 和 [接收] 許可權的[授權規則](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create)。

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. 建立端點，以將您的事件方格主題連結至 Azure 數位 Twins。

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. 在 Azure 數位 Twins 中建立路由，以將對應項更新事件傳送至您的端點。 此路由中的篩選只允許將對應項更新訊息傳遞至您的端點。

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>建立 Azure 函式 

接下來，您將從端對端教學課程（[*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)），在新的函式應用程式、函式應用程式中建立事件中樞觸發的函數。 此函式會將這些更新從其原始格式轉換為 json 修補檔，僅包含來自您 twins 的已更新和已新增值的 JSON 物件。

如需有關使用事件中樞搭配 Azure 函式的詳細資訊，請參閱[*Azure Functions Azure 事件中樞觸發程式*](../azure-functions/functions-bindings-event-hubs-trigger.md)。

在您已發佈的函式應用程式中，以下列程式碼取代函式程式碼。

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

從這裡開始，函式會將它所建立的 JSON 物件傳送至第二個事件中樞，而您將會連接到時間序列深入解析。

## <a name="send-telemetry-to-an-event-hub"></a>將遙測資料傳送至事件中樞

您現在將建立第二個事件中樞，並將您的函式設定為將其輸出串流至該事件中樞。 然後，此事件中樞會連線到時間序列深入解析。

### <a name="create-an-event-hub"></a>建立事件中樞

您可以使用以下的 Azure CLI 指示，或使用 Azure 入口網站：[*快速入門：使用 Azure 入口網站建立事件中樞*](../event-hubs/event-hubs-create.md)。

1. 從稍早準備您的事件中樞命名空間和資源組名 

2. 建立事件中樞
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. 建立具有 [傳送] 和 [接收] 許可權的[授權規則](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create)
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>設定函式

您必須在您的函式應用程式中，從較早的環境變數（包含您的事件中樞連接字串）來設定其中之一。

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>設定時間序列深入解析事件中樞連接字串

1. 針對您在上面為時間序列深入解析中樞建立的授權規則，取得[事件中樞連接字串](../event-hubs/event-hubs-get-connection-string.md)：

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. 在您的函數應用程式中，建立包含連接字串的應用程式設定：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>設定 Twins 事件中樞連接字串

1. 針對您在上面為 twins 中樞建立的授權規則，取得[事件中樞連接字串](../event-hubs/event-hubs-get-connection-string.md)。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 在您的函數應用程式中，建立包含連接字串的應用程式設定：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>建立並連接時間序列深入解析實例

接下來，您將設定時間序列深入解析實例，以接收來自第二個事件中樞的資料。 如需此程式的詳細資訊，請參閱[*教學課程：設定 AZURE 時間序列深入解析 Gen2 PAYG 環境*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. 在 Azure 入口網站中，開始建立時間序列深入解析資源。 
    1. 選取 [ **PAYG （預覽）** ] 定價層。
    2. 您將需要為此環境選擇時間序列識別碼。 您的時間序列識別碼最多可以有三個值，您會在時間序列深入解析中用來搜尋資料。 在本教學課程中，您可以使用 **$dtId**。 閱讀更多有關在[*選擇時間序列識別碼的最佳作法*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)中選取識別碼值的資訊。
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="時間序列深入解析環境的建立入口網站 UX。已選取 [PAYG （預覽）] 定價層，而 [時間序列識別碼] 屬性名稱為 $dtId":::

2. 選取 **[下一步：事件來源]** ，然後選取上述的事件中樞資訊。 您也必須建立新的事件中樞取用者群組。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="時間序列深入解析環境事件來源的建立入口網站 UX。您要使用上述的事件中樞資訊來建立事件來源。您也會建立新的取用者群組。":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>開始將 IoT 資料傳送至 Azure 數位 Twins

若要開始將資料傳送至時間序列深入解析，您必須開始更新具有變更資料值的數位對應項屬性。 使用[az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update)對應項 update 命令。

如果您使用端對端教學課程來協助進行環境設定，您可以 `DeviceSimulator` 從 Azure 數位 Twins[*教學課程：連接端對端解決方案來執行*](tutorial-end-to-end.md)專案，以開始傳送模擬的 IoT 資料。 這些指示位於教學課程的[*設定和執行模擬*](tutorial-end-to-end.md#configure-and-run-the-simulation)一節中。

## <a name="visualize-your-data-in-time-series-insights"></a>在時間序列深入解析中將資料視覺化

現在，資料應該流向您的時間序列深入解析實例，並準備好進行分析。 請遵循下列步驟來探索傳入的資料。

1. 在 Azure 入口網站中開啟您的時間序列深入解析實例。 流覽總覽中所示的時間序列深入解析總管 URL。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="在時間序列深入解析環境的 [總覽] 索引標籤中，按一下 [時間序列深入解析 explorer URL]":::

2. 在 [explorer] 中，您會看到您的三個 twins 顯示在左側。 按一下 [ **thermostat67**]，選取 [ **patch_value**]，然後按一下 [**新增**]。

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="按一下 * * thermostat67 * *，選取 [溫度] * *，然後按一下 [新增] * *":::

3. 您現在應該會看到控溫器的初始溫度讀數，如下所示。 針對 room21 和 floor1，會更新相同的溫度讀數，而您可以將這些資料流程以串聯方式視覺化。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="初始溫度資料會在 TSI explorer 中繪製成圖形。這是介於68和85之間的隨機值行":::

4. 如果您讓模擬更長時間執行，您的視覺效果看起來會像這樣：
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="每個對應項的溫度資料會以三個不同色彩的平行程式碼進行圖形化。":::

## <a name="next-steps"></a>後續步驟

在時間序列深入解析中，數位 twins 預設會儲存為一般階層，但可以使用模型資訊和多層式組織來擴充。 若要深入瞭解此程式，請閱讀： 

* [*教學課程：定義和套用模型*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

您可以撰寫自訂邏輯，使用已儲存在 Azure 數位 Twins 中的模型和圖形資料，自動提供這項資訊。 若要深入瞭解如何管理、升級和從 twins 圖形取得資訊，請參閱下列參考：

* [*如何：管理數位對應項*](./how-to-manage-twin.md)
* [*如何：查詢對應項圖形*](./how-to-query-graph.md)