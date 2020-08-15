---
title: 與 Azure 時間序列深入解析整合
titleSuffix: Azure Digital Twins
description: 請參閱如何設定從 Azure 數位 Twins 到 Azure 時間序列深入解析的事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bda07d0e14ddc630bde4fdc9c869704154c1e6cc
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236347"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>整合 Azure 數位 Twins 與 Azure 時間序列深入解析

在本文中，您將瞭解如何整合 Azure 數位 Twins 與 [Azure 時間序列深入解析 (TSI) ](../time-series-insights/overview-what-is-tsi.md)。

本文中所述的解決方案可讓您收集及分析 IoT 解決方案的歷程記錄資料。 Azure 數位 Twins 是將資料放入時間序列深入解析中的絕佳功能，因為它可讓您將多個資料流程相互關聯，並將您的資訊標準化，再將其傳送至時間序列深入解析。 

## <a name="prerequisites"></a>先決條件

在您可以設定與時間序列深入解析的關聯性之前，您需要有 **Azure 數位 Twins 實例**。 這個實例應設定為根據資料更新數位對應項資訊的能力，因為您必須更新對應項資訊幾次，才能看到時間序列深入解析中追蹤的資料。 

如果您尚未安裝此設定，您可以遵循 Azure 數位 Twins [*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)來建立它。 本教學課程將逐步引導您設定 Azure 數位 Twins 實例，以搭配虛擬 IoT 裝置來觸發數位對應項更新。

## <a name="solution-architecture"></a>方案架構

您將透過下列路徑，將時間序列深入解析附加至 Azure 數位 Twins。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="端對端案例中的 Azure 服務的觀點，反白顯示時間序列深入解析" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>建立路由並篩選對應項更新通知

每當更新對應項的狀態時，Azure 數位 Twins 實例就可以發出對應項 [更新事件](how-to-interpret-event-data.md) 。 在本節中，您將建立 Azure 數位 Twins [**事件路由**](concepts-route-events.md) ，將這些更新事件導向至 Azure [事件中樞](../event-hubs/event-hubs-about.md) 以進行進一步的處理。

Azure 數位 Twins [*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md) 會逐步解說一個案例，其中的溫度計是用來更新代表房間的數位對應項上的溫度屬性。 此模式會依賴對應項更新，而不是從 IoT 裝置轉送遙測，這可讓您彈性地變更基礎資料來源，而不需要更新您的時間序列深入解析邏輯。

1. 首先，建立事件中樞命名空間，它會從您的 Azure 數位 Twins 實例接收事件。 您可以使用以下的 Azure CLI 指示，或使用 Azure 入口網站： [*快速入門：使用 Azure 入口網站建立事件中樞*](../event-hubs/event-hubs-create.md)。

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. 在命名空間內建立事件中樞。

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. 建立具有 [傳送] 和 [接收] 許可權的 [授權規則](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) 。

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. 建立 Azure 數位 Twins [端點](concepts-route-events.md#create-an-endpoint) ，以將您的事件方格主題連結至您的 Azure 數位 Twins 實例。

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. 在 Azure 數位 Twins 中建立 [路由](concepts-route-events.md#create-an-event-route) ，以將對應項更新事件傳送至您的端點。 此路由中的篩選只允許將對應項更新訊息傳遞至您的端點。

    ```azurecli
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

在繼續之前，請記下您的 *事件中樞命名空間* 和 *資源群組*，因為您將在本文稍後使用它們來建立另一個事件中樞。

## <a name="create-an-azure-function"></a>建立 Azure 函式 

接下來，您將在函式應用程式內建立事件中樞觸發的函數。 您可以使用在端對端教學課程中建立的函數應用程式 ([*教學課程：將端對端解決方案*](./tutorial-end-to-end.md) 連線) 或您自己的方案。 

此函式會將這些對應項更新事件從其原始格式轉換為 json 物件，其中僅包含來自您 twins 的已更新和已新增的值。

如需有關使用事件中樞搭配 Azure 函式的詳細資訊，請參閱 [*Azure Functions Azure 事件中樞觸發程式*](../azure-functions/functions-bindings-event-hubs-trigger.md)。

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
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
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

稍後，您也會設定一些環境變數，讓此函式用來連線到您自己的事件中樞。

## <a name="send-telemetry-to-an-event-hub"></a>將遙測資料傳送至事件中樞

您現在將建立第二個事件中樞，並將您的函式設定為將其輸出串流至該事件中樞。 然後，此事件中樞會連線到時間序列深入解析。

### <a name="create-an-event-hub"></a>建立事件中樞

若要建立第二個事件中樞，您可以使用以下的 Azure CLI 指示，或使用 Azure 入口網站： [*快速入門：使用 Azure 入口網站來建立事件中樞*](../event-hubs/event-hubs-create.md)。

1. 準備本文稍早所述的 *事件中樞命名空間* 和 *資源組* 名

2. 建立新的事件中樞
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. 建立具有 [傳送] 和 [接收] 許可權的[授權規則](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create)
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>設定函式

接下來，您必須從先前的函式應用程式中設定環境變數，其中包含您已建立之事件中樞的連接字串。

### <a name="set-the-twins-event-hub-connection-string"></a>設定 Twins 事件中樞連接字串

1. 使用您在上面為 Twins 中樞建立的授權規則，取得 Twins [事件中樞連接字串](../event-hubs/event-hubs-get-connection-string.md)。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. 使用您取得的連接字串，以在包含連接字串的函數應用程式中建立應用程式設定：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>設定時間序列深入解析事件中樞連接字串

1. 使用您在上面為時間序列深入解析中樞建立的授權規則，取得 TSI [事件中樞連接字串](../event-hubs/event-hubs-get-connection-string.md)：

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 在您的函數應用程式中，建立包含連接字串的應用程式設定：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>建立並連接時間序列深入解析實例

接下來，您將設定時間序列深入解析實例，以接收來自第二個事件中樞的資料。 請遵循下列步驟，如需此程式的詳細資訊，請參閱 [*教學課程：設定 Azure 時間序列深入解析 GEN2 PAYG 環境*](../time-series-insights/tutorials-set-up-tsi-environment.md)。

1. 在 Azure 入口網站中，開始建立時間序列深入解析資源。 
    1. 選取 [ **PAYG (預覽]) ** 定價層。
    2. 您將需要為此環境選擇 **時間序列識別碼** 。 您的時間序列識別碼最多可以有三個值，您會在時間序列深入解析中用來搜尋資料。 在本教學課程中，您可以使用 **$dtId**。 閱讀更多有關在 [*選擇時間序列識別碼的最佳作法*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)中選取識別碼值的資訊。
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="時間序列深入解析環境的建立入口網站 UX。已選取 [PAYG (預覽]) 定價層，而 [時間序列識別碼] 屬性名稱為 $dtId":::

2. 選取 **[下一步：事件來源]** ，然後選取上述的事件中樞資訊。 您也必須建立新的事件中樞取用者群組。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="時間序列深入解析環境事件來源的建立入口網站 UX。您要使用上述的事件中樞資訊來建立事件來源。您也會建立新的取用者群組。":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>開始將 IoT 資料傳送至 Azure 數位 Twins

若要開始將資料傳送至時間序列深入解析，您必須開始更新 Azure 數位 Twins 中的數位對應項屬性，並變更資料值。 使用 [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) 對應項 update 命令。

如果您使用端對端教學課程 ([*教學課程：將端對端解決方案*](tutorial-end-to-end.md) 連線) 以協助進行環境設定，您可以從範例執行 *devicesimulator.exe* 專案來開始傳送模擬的 IoT 資料。 這些指示位於教學課程的 [*設定和執行模擬*](tutorial-end-to-end.md#configure-and-run-the-simulation) 一節中。

## <a name="visualize-your-data-in-time-series-insights"></a>在時間序列深入解析中將資料視覺化

現在，資料應該流向您的時間序列深入解析實例，並準備好進行分析。 請遵循下列步驟來探索傳入的資料。

1. 在 [Azure 入口網站](https://portal.azure.com) 中開啟您的時間序列深入解析實例 (您可以在入口網站的搜尋列) 中搜尋實例的名稱。 流覽實例總覽中顯示的 *時間序列深入解析總管 URL* 。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="在時間序列深入解析環境的 [總覽] 索引標籤中，選取 [時間序列深入解析 explorer URL]":::

2. 在 [explorer] 中，您會看到來自左側的 Azure 數位 Twins 的三個 twins。 選取 [ _**thermostat67**_]，選取 [ **溫度**]，然後點擊 [ **新增**]。

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="選取 * * thermostat67 * *，選取 [溫度] * *，然後按 [新增] * *":::

3. 您現在應該會看到控溫器的初始溫度讀數，如下所示。 針對 *room21* 和 *floor1*，會更新相同的溫度讀數，而您可以將這些資料流程以串聯方式視覺化。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="初始溫度資料會在 TSI explorer 中繪製成圖形。這是介於68和85之間的隨機值行":::

4. 如果您讓模擬更長時間執行，您的視覺效果看起來會像這樣：
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="每個對應項的溫度資料會以三個不同色彩的平行程式碼進行圖形化。":::

## <a name="next-steps"></a>後續步驟

在時間序列深入解析中，數位 twins 預設會儲存為一般階層，但可以使用模型資訊和多層式組織來擴充。 若要深入瞭解此程式，請閱讀： 

* [*教學課程：定義和套用模型*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

您可以撰寫自訂邏輯，使用已儲存在 Azure 數位 Twins 中的模型和圖形資料，自動提供這項資訊。 若要深入瞭解如何管理、升級和從 twins 圖形取得資訊，請參閱下列參考：

* [*如何：管理數位對應項*](./how-to-manage-twin.md)
* [*如何：查詢對應項圖形*](./how-to-query-graph.md)