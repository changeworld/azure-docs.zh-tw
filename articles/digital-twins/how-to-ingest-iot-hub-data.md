---
title: 從 IoT 中樞內嵌遙測
titleSuffix: Azure Digital Twins
description: 請參閱如何從 IoT 中樞內嵌裝置遙測訊息。
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 351f7ed131d545d2aa83df753cac3f26e76e4ccb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725846"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>將 IoT 中樞遙測內嵌到 Azure 數位 Twins

Azure 數位 Twins 是由 IoT 裝置和其他來源的資料所驅動。 要在 Azure 數位 Twins 中使用之裝置資料的常見來源是[IoT 中樞](../iot-hub/about-iot-hub.md)。

在預覽期間，將資料內嵌到 Azure 數位 Twins 的程式是設定外部計算資源（例如[Azure](../azure-functions/functions-overview.md)函式），以接收資料並使用[選取 api](how-to-use-apis-sdks.md)來設定屬性，或據以在[數位 Twins](concepts-twins-graph.md)上引發遙測事件。 

本操作說明文件將逐步解說如何撰寫可從 IoT 中樞內嵌遙測的 Azure 函式。

## <a name="example-telemetry-scenario"></a>範例遙測案例

本操作說明概述如何使用 Azure function，將訊息從 IoT 中樞傳送至 Azure 數位 Twins。 有許多可能的設定和相符的策略可供您使用，但本文的範例包含下列部分：
* IoT 中樞中的溫度計裝置，具有已知的裝置識別碼。
* 代表裝置的數位對應項，具有相符的識別碼
* 代表房間的數位對應項

> [!NOTE]
> 這個範例會在裝置識別碼與對應的數位對應項識別碼之間使用簡單的識別碼比對，但是可以提供更複雜的對應，從裝置到其對應項（例如與對應資料表）。

當溫度計裝置傳送溫度遙測事件時，*房間*對應項的*溫度*屬性應會更新。 若要進行這項操作，您將會從裝置上的遙測事件對應到數位對應項上的屬性 setter。 您將使用對應項[圖形](concepts-twins-graph.md)中的拓撲資訊來尋找*房間*對應項，然後您可以設定對應項的屬性。 在其他情況下，使用者可能會想要在相符的對應項上設定屬性（在此範例中，識別碼為*123*的對應項）。 Azure 數位 Twins 可讓您有很大的彈性可決定如何將遙測資料對應至 Twins。 

下圖概述此案例：

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="IoT 中樞裝置會透過 IoT 中樞、事件方格或系統主題，將溫度遙測傳送至 Azure 函式，以更新 Azure 數位 Twins 中 twins 的溫度屬性。" border="false":::

## <a name="prerequisites"></a>必要條件

繼續執行此範例之前，您必須先完成下列必要條件。
1. 建立 IoT 中樞。 如需相關指示，請參閱[本 IoT 中樞快速入門](../iot-hub/quickstart-send-telemetry-cli.md)的*建立 IoT 中樞*一節。
2. 建立至少一個 Azure 函式，以處理來自 IoT 中樞的事件。 請參閱[如何：設定用來處理資料的 azure](how-to-create-azure-function.md)函式，以建立可連線至 Azure 數位 Twins 並呼叫 Azure 數位 Twins API 功能的基本 Azure 函式。 本操作說明的其餘部分將會根據此功能來建立。
3. 設定中樞資料的事件目的地。 在 [ [Azure 入口網站](https://portal.azure.com/)中，流覽至您的 IoT 中樞實例。 在 [*事件*] 底下，為您的 Azure function 建立訂用帳戶。 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure 入口網站：新增事件訂用帳戶":::

4. 在 [*建立事件訂*用帳戶] 頁面中，填寫欄位，如下所示：
   * 在 [*事件訂閱詳細資料*] 底下，將訂用帳戶命名為您想要的
   * 在 [*事件種類*] 底下，選擇 [*裝置遙測*] 做為要篩選的事件種類
      - 如有需要，請將篩選新增至其他事件種類。
   * 在 [*端點詳細資料*] 底下，選取您的 Azure 函式作為端點

## <a name="create-an-azure-function-in-visual-studio"></a>在 Visual Studio 中建立 Azure 函式

本節使用相同的 Visual Studio 啟動步驟和 Azure 函式基本架構，從[如何：設定 azure 函數來處理資料](how-to-create-azure-function.md)。 此基本架構會處理驗證並建立服務用戶端，以供您處理資料和呼叫 Azure 數位 Twins Api 以進行回應。 

基本架構函式的核心如下：

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

在接下來的步驟中，您將會在其中新增特定程式碼，以便處理來自 IoT 中樞的 IoT 遙測事件。  

## <a name="add-telemetry-processing"></a>新增遙測處理

遙測事件的形式來自裝置的訊息。 新增遙測處理常式代碼的第一個步驟，是從事件方格事件中，將此裝置訊息的相關部分解壓縮。 

不同的裝置可能會以不同的方式來結構其訊息，因此此步驟的程式碼取決於已連線的裝置。 

下列程式碼顯示將遙測當做 JSON 傳送的簡單裝置範例。 此範例會解壓縮傳送訊息之裝置的裝置識別碼，以及溫度值。

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

回想一下，此練習的目的是要更新對應項圖形內的*房間*溫度。 這表示訊息的目標不是與此裝置相關聯的數位對應項，而是其父系的*房間*對應項。 您可以使用上述程式碼，從遙測訊息中解壓縮的裝置識別碼值來尋找父系對應項。

若要這麼做，請使用 Azure 數位 Twins Api 來存取裝置的連入關聯性，代表對應項（在此案例中，其識別碼與裝置相同）。 從傳入的關聯性中，您可以使用下列程式碼片段來尋找父系的識別碼。

下列程式碼片段顯示如何抓取對應項的傳入關聯性：

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

對應項的父系位於關聯性的*SourceId*屬性中。

代表裝置只具有單一內送關聯性的對應項模型相當常見。 在此情況下，您可能會挑選傳回的第一個（且唯一的）關聯性。 如果您的模型允許此對應項有多個關聯性類型，您可能需要進一步指定，以從多個連入關聯性中選擇。 執行這項操作的常見方式是依挑選關聯性 `RelationshipName` 。 

當您擁有代表*房間*的父系對應項識別碼之後，就可以「修補」（進行更新）該對應項。 若要這麼做，請使用下列程式碼：

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>完整的 Azure function 程式碼

使用先前範例中的程式碼，以下是內容中的整個 Azure 函數：

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

用來尋找連入關聯性的公用程式函式：
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

以及用來修補對應項的公用程式函式：
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

現在您已有一個 Azure 函式，可供讀取和解讀來自 IoT 中樞的案例資料。

## <a name="debug-azure-function-apps-locally"></a>在本機上對 Azure 函數應用程式進行 Debug

您可以在本機使用事件方格觸發程式來進行 Azure 函數的偵測。 如需這方面的詳細資訊，請參閱在[本機偵錯工具事件方格觸發](../azure-functions/functions-debug-event-grid-trigger-local.md)程式。

## <a name="next-steps"></a>後續步驟

閱讀 Azure 數位 Twins 的資料輸入和輸出：
* [概念：與其他服務整合](concepts-integration.md)
