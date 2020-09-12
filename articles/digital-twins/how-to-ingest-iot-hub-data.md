---
title: 從 IoT 中樞內嵌遙測
titleSuffix: Azure Digital Twins
description: 瞭解如何從 IoT 中樞內嵌裝置遙測訊息。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8d720d77773e506a13f176723ab4583613f1e625
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291750"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>將 IoT 中樞遙測內嵌到 Azure 數位 Twins

Azure 數位 Twins 是由來自 IoT 裝置和其他來源的資料所驅動。 Azure 數位 Twins 中要使用之裝置資料的常見來源為 [IoT 中樞](../iot-hub/about-iot-hub.md)。

將資料擷取至 Azure 數位 Twins 的程式是設定可接收資料的外部計算資源，例如 [Azure](../azure-functions/functions-overview.md)函式，並使用 [DigitalTwins api](how-to-use-apis-sdks.md) 來設定屬性，或在 [數位 Twins](concepts-twins-graph.md) 上引發遙測事件。 

此操作說明文件將逐步解說撰寫可從 IoT 中樞內嵌遙測資料的 Azure 函式。

## <a name="prerequisites"></a>必要條件

繼續進行此範例之前，您必須將下列資源設定為必要條件：
* **IoT 中樞**。 如需相關指示，請參閱[此 Iot 中樞快速入門](../iot-hub/quickstart-send-telemetry-cli.md)的*建立 iot 中樞*一節。
* 具有正確許可權可呼叫數位對應項實例的**Azure 函數**。 如需相關指示，請參閱作法 [*：設定 Azure 函式來處理資料*](how-to-create-azure-function.md)。 
* 將會收到您裝置遙測的**Azure 數位 Twins 實例**。 如需指示，請參閱作法 [*：設定 Azure 數位 Twins 實例和驗證*](./how-to-set-up-instance-portal.md)。

### <a name="example-telemetry-scenario"></a>範例遙測案例

此操作說明概述如何使用 Azure 函式，將訊息從 IoT 中樞傳送至 Azure 數位 Twins。 有許多可能的設定和符合的策略可供您使用，但本文的範例包含下列部分：
* IoT 中樞內具有已知裝置識別碼的溫度計裝置。
* 代表裝置的數位對應項，具有相符的識別碼

> [!NOTE]
> 此範例會在裝置識別碼和對應的數位對應項識別碼之間使用簡單的識別碼比對，但您可以提供從裝置到其對應項 (的更精密對應，例如使用對應資料表) 。

當溫度計裝置傳送溫度遙測事件時，數位對應項的 *溫度* 屬性應會更新。 下圖概述此案例：

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="此圖表顯示流程圖。在圖表中，IoT 中樞裝置會透過 IoT 中樞將溫度遙測傳送至 Azure 函式，以更新 Azure 數位 Twins 中對應項的溫度屬性。" border="false":::

## <a name="add-a-model-and-twin"></a>新增模型和對應項

您需要有對應項才能以 IoT 中樞資訊進行更新。

模型看起來像這樣：
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

若要將 **此模型上傳至您的 twins 實例**，請開啟 Azure CLI，然後執行下列命令：

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

然後，您必須 **使用此模型建立一個對應項**。 使用下列命令建立對應項，並將0.0 設定為初始溫度值。

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

成功對應項 create 命令的輸出看起來應該像這樣：
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>建立 Azure 函式

本節使用相同的 Visual Studio 啟動步驟和 Azure 函式基本架構，從 [*操作說明：設定用來處理資料的 azure 函數*](how-to-create-azure-function.md)。 此基本架構會處理驗證並建立服務用戶端，以供您處理資料並呼叫 Azure 數位 Twins Api 以回應。 

在接下來的步驟中，您將新增特定程式碼來處理來自 IoT 中樞的 IoT 遙測事件。  

### <a name="add-telemetry-processing"></a>新增遙測處理
    
遙測事件是來自裝置的訊息形式。 新增遙測處理常式代碼的第一個步驟，是從事件方格事件中解壓縮此裝置訊息的相關部分。 

不同的裝置可能會以不同的結構來建立訊息的結構，因此這個步驟的程式碼 **取決於已連線的裝置。** 

下列程式碼顯示將遙測傳送為 JSON 的簡單裝置範例。 此範例完整地在 [*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)中進行探索。 下列程式碼會尋找傳送訊息之裝置的裝置識別碼，以及溫度值。

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

下一個程式碼範例會採用識別碼和溫度值，並使用它們來「patch」 (進行更新以) 該對應項。

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>更新您的 Azure 函式程式碼

既然您已瞭解先前範例中的程式碼，請開啟 Visual Studio，並將您的 Azure 函式程式碼取代為此範例程式碼。

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>將您的函式連線到 IoT 中樞

1. 設定中樞資料的事件目的地。 在 [Azure 入口網站](https://portal.azure.com/)中，流覽至您的 IoT 中樞實例。 在 [ **事件**] 下，為您的 Azure function 建立訂用帳戶。 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="顯示新增事件訂閱之 Azure 入口網站的螢幕擷取畫面。":::

2. 在 [ **建立事件訂** 用帳戶] 頁面中，填寫欄位，如下所示：
    1. 在 [ **名稱**] 底下，將訂用帳戶命名為您要的名稱。
    2. 在 [ **事件架構**] 下，選擇 [ **事件方格架構**]。
    3. 在 [ **系統主題名稱**] 底下，選擇唯一的名稱。
    4. 在 [ **事件種類**] 下，選擇 [ **裝置遙測** ] 作為要篩選的事件種類。
    5. 在 [ **端點詳細資料**] 底下，選取您的 Azure function 作為端點。

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="顯示事件訂用帳戶詳細資料 Azure 入口網站的螢幕擷取畫面":::

## <a name="send-simulated-iot-data"></a>傳送模擬的 IoT 資料

若要測試新的輸入函式，請使用教學課程中的裝置模擬器 [*：連接端對端解決方案*](./tutorial-end-to-end.md)。 該教學課程是以 c # 撰寫的範例專案所驅動。 範例程式碼位於： [Azure 數位 Twins 範例](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples)。 您將會在該存放庫中使用 **devicesimulator.exe** 專案。

在端對端教學課程中，完成下列步驟：
1. [*向 IoT 中樞註冊模擬裝置*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*設定並執行模擬*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>驗證您的結果

執行上述裝置模擬器時，數位對應項的溫度值將會變更。 在 Azure CLI 中，執行下列命令以查看溫度值。

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

您的輸出應該包含如下的溫度值：

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

若要查看值變更，請重複執行上述查詢命令。

## <a name="next-steps"></a>接下來的步驟

閱讀 Azure 數位 Twins 的資料輸入和輸出：
* [*概念：與其他服務整合*](concepts-integration.md)
