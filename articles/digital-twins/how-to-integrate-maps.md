---
title: 使用 Azure 數位 Twins 來更新 Azure 地圖服務室內地圖
titleSuffix: Azure Digital Twins
description: 請參閱如何建立可使用對應項圖形和 Azure 數位 Twins 通知的 Azure 函式，以更新 Azure 地圖服務中顯示的資訊。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 6aad6201136bb925d5e094de115cc7274cc7872a
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131407"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>使用 Azure 數位 Twins 來更新 Azure 地圖服務室內地圖

本文逐步解說使用 Azure 數位 Twins 資料來更新*室內地圖*上顯示的資訊時，使用[Azure 地圖服務](../azure-maps/about-azure-maps.md)所需的步驟。 Azure 數位 Twins 會儲存您 IoT 裝置關聯性的圖形，並將遙測路由傳送至不同的端點，使其成為在地圖上更新資訊重迭的絕佳服務。

本操作說明將涵蓋：

1. 設定您的 Azure 數位 Twins 實例，以將對應項更新事件傳送至[Azure Functions](../azure-functions/functions-overview.md)中的函式。
2. 建立 Azure 函式以更新 Azure 地圖服務室內地圖功能 stateset。
3. 如何在 Azure 數位 Twins 圖形中儲存地圖識別碼和功能 stateset 識別碼。

### <a name="prerequisites"></a>必要條件

* 遵循 Azure 數位 Twins[*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)。
    * 您將會使用額外的端點和路由來擴充此對應項。 您也會從該教學課程將另一個函式新增至函數應用程式。 
* 遵循 Azure 地圖服務[*教學課程：使用 Azure 地圖服務 Creator 建立室內地圖*](../azure-maps/tutorial-creator-indoor-maps.md)，以建立具有*功能 stateset*的 Azure 地圖服務室內地圖。
    * [功能 statesets](../azure-maps/creator-indoor-maps.md#feature-statesets)是指派給資料集功能（例如房間或設備）的動態屬性（狀態）集合。 在上述 Azure 地圖服務教學課程中，功能 stateset 會儲存您將顯示在地圖上的房間狀態。
    * 您將需要功能*STATESET 識別碼*和 Azure 地圖服務訂用帳戶*識別碼*。

### <a name="topology"></a>拓撲

下圖說明本教學課程中的室內 maps 整合元素如何融入較大的端對端 Azure 數位 Twins 案例。

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="在端對端案例中的 Azure 服務的觀點，反白顯示室內 Maps 整合片段" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>在 twins 更新時建立函式以更新對應

首先，您會在 Azure 數位 Twins 中建立路由，以將所有對應項更新事件轉送到事件方格主題。 然後，您將使用 Azure 函式來讀取這些更新訊息，並更新 Azure 地圖服務中的功能 stateset。 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>建立路由並篩選對應項更新通知

每當更新對應項的狀態時，Azure 數位 Twins 實例就可以發出對應項更新事件。 Azure 數位 Twins[*教學課程：連接上述的端對端解決方案*](./tutorial-end-to-end.md)會逐步解說一個案例，其中的溫度計是用來更新附加至房間對應項的溫度屬性。 您將會訂閱 twins 的更新通知，並使用該資訊來更新您的對應，以擴充該解決方案。

此模式會直接讀取房間對應項，而不是 IoT 裝置，這可讓您彈性地變更溫度的基礎資料來源，而不需要更新您的對應邏輯。 例如，您可以新增多個溫度計，或將此房間設定為與另一個房間共用溫度計，而不需要更新您的對應邏輯。

1. 建立事件方格主題，它會從您的 Azure 數位 Twins 實例接收事件。
    ```azurecli
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. 建立端點，以將您的事件方格主題連結至 Azure 數位 Twins。
    ```azurecli
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. 在 Azure 數位 Twins 中建立路由，以將對應項更新事件傳送至您的端點。
    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function-to-update-maps"></a>建立 Azure 函數來更新對應

您將會從端對端教學課程（[*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)），在函式應用程式內建立事件方格觸發函數。 此函式會將這些通知解壓縮，並將更新傳送至 Azure 地圖服務功能 stateset，以更新一個房間的溫度。 

如需參考資訊，請參閱下列檔： [*Azure Functions 的 Azure Event Grid 觸發程式*](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger)。

將函式程式碼取代為下列程式碼。 它只會篩選出空間 twins 的更新、讀取更新過的溫度，並將該資訊傳送至 Azure 地圖服務。

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in your map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

您必須在函數應用程式中設定兩個環境變數。 其中一個是您[Azure 地圖服務的主要訂](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)用帳戶金鑰，而其中一個是您[AZURE 地圖服務的 stateset 識別碼](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)。

```azurecli-interactive
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-ID> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### <a name="view-live-updates-on-your-map"></a>在地圖上查看即時更新

若要查看即時更新溫度，請遵循下列步驟：

1. 從 Azure 數位 Twins[*教學課程：連接端對端解決方案*](tutorial-end-to-end.md) **，開始傳送模擬的 IoT**資料。 這項操作的指示位於[*設定和執行模擬*](././tutorial-end-to-end.md#configure-and-run-the-simulation)一節中。
2. 使用[ **Azure 地圖服務室內**模組](../azure-maps/how-to-use-indoor-module.md)來轉譯在 Azure 地圖服務 Creator 建立的室內地圖。
    1. 複製下列範例中的 HTML：使用室內 maps 教學課程的[*室內 Maps 模組*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module)一節[*：使用 Azure 地圖服務室內 maps 模組*](../azure-maps/how-to-use-indoor-module.md)至本機檔案。
    1. 將本機 HTML 檔案中的*tilesetId*和*statesetID*取代為您的值。
    1. 在您的瀏覽器中開啟該檔案。

這兩個範例都會在相容的範圍內傳送溫度，因此您應該會在地圖上看到房間121更新的色彩大約每30秒一次。

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="顯示房間121彩色橙色的辦公室地圖":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>將地圖資訊儲存在 Azure 數位 Twins

既然您已有硬式編碼的解決方案來更新地圖服務資訊，您可以使用 Azure 數位 Twins 圖形來儲存更新室內地圖所需的所有資訊。 這會分別包含每個對應和位置的 stateset 識別碼、對應訂用帳戶識別碼和功能識別碼。 

此特定範例的解決方案會牽涉到更新每個最上層的空間，使其具有 stateset ID 和 maps 訂用帳戶識別碼屬性，並更新每個房間以具有功能識別碼。 在初始化對應項圖形時，您必須設定這些值一次，然後針對每個對應項更新事件查詢這些值。

視拓撲的設定而定，您可以將這三個屬性儲存在與地圖的資料細微性相互關聯的不同層級。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何管理、升級和從 twins 圖形取得資訊，請參閱下列參考：

* [*如何：管理數位 twins*](./how-to-manage-twin.md)
* [*如何：查詢對應項圖形*](./how-to-query-graph.md)