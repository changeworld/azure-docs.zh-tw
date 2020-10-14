---
title: 與 Azure SignalR Service 整合
titleSuffix: Azure Digital Twins
description: 瞭解如何使用 Azure SignalR 將 Azure 數位 Twins 遙測串流至用戶端
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0c3d3a050c0b929a3f1042b42006c289ddeb9acb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048112"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>整合 Azure 數位 Twins 與 Azure SignalR Service

在本文中，您將瞭解如何整合 Azure 數位 Twins 與 [Azure SignalR Service](../azure-signalr/signalr-overview.md)。

本文所述的解決方案可讓您將數位對應項遙測資料推送至連線的用戶端，例如單一網頁或行動應用程式。 如此一來，用戶端就會從 IoT 裝置以即時計量和狀態進行更新，而不需要輪詢伺服器或提交新的 HTTP 要求以進行更新。

## <a name="prerequisites"></a>先決條件

以下是您在繼續之前應完成的必要條件：

* 將您的解決方案與本文中的 Azure SignalR service 整合之前，您應該先完成 Azure 數位 Twins [_**教學課程：連接端對端解決方案**_](tutorial-end-to-end.md)，因為這是如何建立在其上。 本教學課程會逐步引導您設定 Azure 數位 Twins 實例，以搭配虛擬 IoT 裝置來觸發數位對應項更新。 此操作說明會使用 Azure SignalR Service 將這些更新連線至範例 web 應用程式。
    - 您將需要在教學課程中建立的 **事件方格主題** 的名稱。
* 在您的電腦上安裝 [**Node.js**](https://nodejs.org/) 。

您也可以繼續使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

## <a name="solution-architecture"></a>方案架構

您將透過下列路徑將 Azure SignalR Service 附加至 Azure 數位 Twins。 圖中的 A、B 和 C 區段取自 [端對端教學](tutorial-end-to-end.md)課程的架構圖表，在此操作說明中，您將藉由新增區段 D 來建立。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。" lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>下載範例應用程式

首先，下載所需的範例應用程式。 您將需要下列兩項：
* [**Azure 數位 Twins 範例**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)：此範例包含 *AdtSampleApp* ，其中包含兩個 azure 函式，可在 azure 數位 Twins 實例周圍移動資料 (您可以在 [*教學課程：連接端對端解決方案*](tutorial-end-to-end.md)) 中更詳細地瞭解此案例。 它也包含 *devicesimulator.exe* 範例應用程式，可模擬 IoT 裝置，每秒產生新的溫度值。 
    - 流覽至範例連結並按 [ *下載 ZIP* ] 按鈕，將範例的複本下載到您的電腦，如 _**Azure_Digital_Twins_samples.zip**_。 將資料夾解壓縮。
* [**SignalR 整合 web 應用程式範例**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)：這是範例回應 web 應用程式，會從 azure SignalR 服務取用 Azure 數位 Twins 遙測資料。
    -  流覽至範例連結並按 [ *下載 ZIP* ] 按鈕，將範例的複本下載到您的電腦，如 _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_。 將資料夾解壓縮。

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

讓瀏覽器視窗保持開啟 Azure 入口網站，因為您將在下一節中再次使用它。

## <a name="configure-and-run-the-azure-functions-app"></a>設定及執行 Azure Functions 應用程式

在本節中，您將設定兩個 Azure 函數：
* **negotiate** -HTTP 觸發程式函數。 它會使用 *>signalrconnectioninfo* 輸入系結來產生並傳回有效的連接資訊。
* **廣播** - [事件方格](../event-grid/overview.md) 觸發程式函式。 它會透過事件方格接收 Azure 數位 Twins 遙測資料，並使用您在上一個步驟中建立之 *SignalR* 實例的輸出系結，將訊息廣播至所有連線的用戶端應用程式。

首先，移至開啟 Azure 入口網站的瀏覽器，然後完成下列步驟，以取得您已設定之 SignalR 實例的 **連接字串** 。 您將需要它來設定函數。
1. 確認已成功建立您稍早部署的 SignalR Service 實例。 您可以在入口網站頂端的 [搜尋] 方塊中搜尋其名稱，以完成此動作。 選取該執行個體以開啟它。

1. 從 [實例] 功能表選取 [索引 **鍵** ]，以查看 SignalR Service 實例的連接字串。

1. 選取圖示以複製主要連接字串。

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。" lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

接著，啟動 Visual Studio (或您所選擇) 的其他程式碼編輯器，然後在 *Azure_Digital_Twins_samples > ADTSampleApp* 資料夾中開啟程式碼方案。 然後，執行下列步驟以建立函式：

1. 在*SampleFunctionsApp*專案中，建立名為**SignalRFunctions.cs**的新 c # 井類。

1. 以下列程式碼取代類別檔案的內容：

    ```C#
    using System;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System.Collections.Generic;
    
    namespace SampleFunctionsApp
    {
        public static class SignalRFunctions
        {
            public static double temperature;
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo GetSignalRInfo(
                [HttpTrigger(AuthorizationLevel.Anonymous, "post")] HttpRequest req,
                [SignalRConnectionInfo(HubName = "dttelemetry")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static Task SendMessage(
                [EventGridTrigger] EventGridEvent eventGridEvent,
                [SignalR(HubName = "dttelemetry")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {
                JObject eventGridData = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
    
                log.LogInformation($"Event grid message: {eventGridData}");
    
                var patch = (JObject)eventGridData["data"]["patch"][0];
                if (patch["path"].ToString().Contains("/Temperature"))
                {
                    temperature = Math.Round(patch["value"].ToObject<double>(), 2);
                }
    
                var message = new Dictionary<object, object>
                {
                    { "temperatureInFahrenheit", temperature},
                };
        
                return signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { message }
                    });
            }
        }
    }
    ```

1. 在 Visual Studio 的 *封裝管理員主控台* 視窗，或您電腦上 *Azure_Digital_Twins_samples \adtsampleapp\samplefunctionsapp* 資料夾中的任何命令視窗中，執行下列命令以將 `SignalRService` NuGet 套件安裝到專案中：
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    這應該可解決類別中的任何相依性問題。

接下來，使用「*連接端對端解決方案*」教學課程的「 [*發行應用程式*」一節](tutorial-end-to-end.md#publish-the-app)所述的步驟，將您的函式發佈至 Azure。 您可以將它發佈至您在端對端教學課程 >prereq 中使用的相同 app service/函式應用程式，或建立新的應用程式，但您可能會想要使用相同的應用程式來將重複項降至最低。 此外，請使用下列步驟來完成應用程式發佈：
1. 收集 *negotiate* 函數的 **HTTP 端點 URL**。 若要這樣做，請移至 Azure 入口網站的函式 [應用](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) 程式頁面，然後從清單中選取您的函數應用程式。 在 [應用程式] 功能表中選取 [函式]，然後選擇 [ *negotiate* *] 功能。*

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

    點擊 *取得* 函式 URL，然後 **透過 _/api_ 複製值 (不包含最後一個 _/negotiate？_) **。 您稍後將會用到。

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

1. 最後，使用下列 Azure CLI 命令，將您先前的 Azure SignalR **連接字串** 新增至函式的應用程式設定。 如果您的[電腦上已安裝](/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI，命令可以在[Azure Cloud Shell](https://shell.azure.com)中執行，或在本機執行：
 
    ```azurecli
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    此命令的輸出會列印為您的 Azure function 設定的所有應用程式設定。 `AzureSignalRConnectionString`在清單底部尋找，以確認它已加入。

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

#### <a name="connect-the-function-to-event-grid"></a>將函式連線至事件方格

接下來，將 *廣播* Azure 函式訂閱至您在教學課程中建立的 **事件方格主題** [*：連接端對端解決方案*](tutorial-end-to-end.md) >prereq。 這可讓遙測資料透過事件方格主題，從 *thermostat67* 對應項流動至函式，以便將它廣播到所有用戶端。

若要這樣做，您將從事件方格主題建立 **事件方格訂** 用帳戶，並將您的 *廣播* Azure 函式作為端點。

在 [Azure 入口網站](https://portal.azure.com/)中，導覽至您的事件方格主題，方法是在頂端的搜尋列中搜尋其名稱。 選取 [+ 事件訂用帳戶]。

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

在 [建立事件訂用帳戶] 頁面中，填寫下列欄位 (不會提及依預設填入的欄位)：
* 「事件訂用帳戶詳細資料」 > [名稱]：命名事件訂用帳戶。
* 「端點詳細資料」 > [端點類型]：從功能表選項中選取「Azure 函式」。
* 「端點詳細資料」 > [端點]：按「選取端點」 連結。 這會開啟「選取 Azure 函式」 視窗：
    - 填入您的**訂**用帳戶、**資源群組**、**函數應用程式****和函**式 (*廣播*) 。 在您選取訂用帳戶後，其中有些可能會自動填入。
    - 按 [確認選取項目]。

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

回到「建立事件訂用帳戶」 頁面，按 [建立]。

## <a name="configure-and-run-the-web-app"></a>設定並執行 web 應用程式

在這一節中，您將會看到作用中的結果。 首先，您會啟動 **模擬裝置範例應用程式** ，以透過您的 Azure 數位 Twins 實例傳送遙測資料。 然後，您將設定 **範例用戶端 web 應用程式** ，以連線到您已設定的 Azure SignalR 流程。 之後，您應該能夠看到資料即時更新範例 web 應用程式。

### <a name="run-the-device-simulator"></a>執行裝置模擬器

在端對端教學課程的必要條件中，您 [已將裝置模擬器設定](tutorial-end-to-end.md#configure-and-run-the-simulation) 為透過 IoT 中樞和 Azure 數位 Twins 實例來傳送資料。

現在，您只需要啟動模擬器專案，它位於 *Azure_Digital_Twins_samples > devicesimulator.exe > devicesimulator.exe .sln*。 如果您是使用 Visual Studio，可以開啟專案，然後在工具列中使用這個按鈕來執行它：

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

主控台視窗會開啟並顯示模擬的溫度遙測訊息。 這些是透過您的 Azure 數位 Twins 實例來傳送，然後由 Azure 函式和 SignalR 來挑選。

您在此主控台中不必執行任何其他動作，但在完成後續步驟時，請保持其執行狀態。

### <a name="configure-the-sample-client-web-app"></a>設定範例用戶端 web 應用程式

接下來，使用下列步驟來設定 **SignalR 整合 web 應用程式範例** ：
1. 使用 Visual Studio 或您選擇的任何程式碼編輯器，開啟您在[*必要條件*](#prerequisites)一節中下載的解壓縮_**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ 資料夾。

1. 開啟 *src/App.js* 檔案，然後將中的 URL 取代 `HubConnectionBuilder` 為您稍早儲存的 **NEGOTIATE** 函式的 HTTP 端點 URL：

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. 在 Visual Studio 的 *開發人員命令提示* 字元或電腦上的任何命令視窗中，流覽至 *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* 資料夾。 執行下列命令以安裝相依節點套件：

    ```cmd
    npm install
    ```

接下來，在 Azure 入口網站中設定函數應用程式的許可權：
1. 在 Azure 入口網站的 [ [函數應用程式](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) ] 頁面中，選取您的函式應用程式實例。
1. 在 [實例] 功能表中向下移動，然後選取 [ *CORS*]。 在 [CORS] 頁面上，將 `http://localhost:3000` 其輸入空白方塊中，以新增為允許的來源。 核取 [ *啟用存取控制-允許認證* ] 和 [點擊 *儲存*] 的核取方塊。

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

### <a name="see-the-results"></a>查看結果

若要查看作用中的結果，請啟動 **SignalR 整合 web 應用程式範例**。 您可以從 *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* 位置的任何主控台視窗執行此命令，方法是執行下列命令：

```cmd
npm start
```

這會開啟執行範例應用程式的瀏覽器視窗，此視窗會顯示視覺溫度量測計。 應用程式執行之後，您應該會開始看到來自裝置模擬器的溫度遙測值，該模擬器會透過 Azure 數位 Twins 即時反映到 web 應用程式。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="在端對端案例中，Azure 服務的觀點。描述從裝置流入 IoT 中樞的資料，透過 Azure 函式 (箭號 B) 至 Azure 數位 Twins 實例 (一節) ，然後透過事件方格移至另一個 Azure 函式來處理 (箭號 C) 。區段 D 會顯示從箭號 C 中的相同事件方格流向標示為「廣播」之 Azure 函式的資料。「廣播」會與標示為「negotiate」的另一個 Azure 函式通訊，而且「廣播」和「協商」會與電腦裝置通訊。":::

## <a name="clean-up-resources"></a>清除資源

如果您不再需要本文中建立的資源，請遵循下列步驟來刪除這些資源。 

使用 Azure Cloud Shell 或本機 Azure CLI，您可以使用 [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令刪除資源群組中的所有 Azure 資源。 移除資源群組也會移除 .。。
* 從端對端教學課程 (的 Azure 數位 Twins 實例) 
* 從端對端教學課程 (IoT 中樞和中樞裝置註冊) 
* 事件方格主題和相關聯的訂閱
* Azure Functions 應用程式，包含所有三個函式和相關聯的資源（例如儲存體）
* Azure SignalR 實例

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 資源群組和其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 

```azurecli
az group delete --name <your-resource-group>
```

如果您要刪除 Azure 數位 Twins 實例，也可以使用下列命令，刪除您在端對端教學課程中為它建立的 Azure AD 應用程式註冊：

```azurecli
az ad app delete --id <your-application-ID>
```

最後，刪除您下載到本機電腦 (*Azure_Digital_Twins_samples.zip* 和 *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*) 的專案範例資料夾。

## <a name="next-steps"></a>後續步驟

在本文中，您會使用 SignalR 設定 Azure 函式，以將 Azure 數位 Twins 遙測事件廣播至範例用戶端應用程式。

接下來，請深入瞭解 Azure SignalR Service：
* [*什麼是 Azure SignalR 服務？*](../azure-signalr/signalr-overview.md)

或閱讀更多有關使用 Azure Functions Azure SignalR Service 驗證的詳細資訊：
* [*Azure SignalR 服務驗證*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)