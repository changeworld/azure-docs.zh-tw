---
title: 教學課程 - 使用 Azure IoT Edge 開發 Windows 的 C# 模組
description: 本教學課程說明如何使用 C# 程式碼建立 IoT Edge 模組，並將其部署到 Windows IoT Edge 裝置。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 24a63a167f640646b9e07126a038663f4da3e23b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449605"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>教學課程：開發適用於 Windows 裝置的 C# IoT Edge 模組

使用 Visual Studio 開發 C# 程式碼，並將其部署到執行 Azure IoT Edge 的 Windows 裝置。

您可以使用 Azure IoT Edge 模組來部署程式碼，直接在 IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用 Visual Studio 建立以 C# SDK 為基礎的 IoT Edge 模組。
> * 使用 Visual Studio 和 Docker 建立 Docker 映像，並將其發佈至您的登錄中。
> * 將模組部署到您的 IoT Edge 裝置。
> * 檢視產生的資料。

您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解決方案範圍

本教學課程示範如何使用 **Visual Studio 2019** 以 **C#** 開發模組，並將其部署至 **Windows 裝置**。 如果您要開發適用於 Linux 裝置的模組，請改為移至[開發適用於 Linux 裝置的 C# IoT Edge 模組](tutorial-csharp-module.md)。

使用下表來了解開發 C# 模組並將其部署到 Windows 裝置的選項：

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 開發** | ![在 VS Code 中開發適用於 WinAMD64 的 C# 模組](./media/tutorial-c-module/green-check.png) | ![在 Visual Studio 中開發適用於 WinAMD64 的 C# 模組](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 偵錯** |   | ![在 Visual Studio 中對適用於 WinAMD64 的 C# 模組進行偵錯](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Prerequisites

在開始本教學課程之前，您應該已經完成先前的教學課程 ([開發適用於 Windows 裝置的 IoT Edge 模組](tutorial-develop-for-windows.md)) 來設定您的開發環境。 完成該教學課程之後，您已經具備下列必要條件：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 Windows 裝置](quickstart.md)。
* 容器登錄，像是 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)。
* 已設定 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 擴充功能的 [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。
* 已設定要執行 Windows 容器的 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。

> [!TIP]
> 如果您使用 Visual Studio 2017 (15.7 或更高版本)，請從 Visual Studio 市集下載並安裝適用於 VS 2017 的 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

## <a name="create-a-module-project"></a>建立模組專案

下列步驟會使用 Visual Studio 和 Azure IoT Edge Tools 擴充功能來建立 IoT Edge 模組專案。 建立專案範本後，新增程式碼，以便模組根據其報告的屬性篩選出訊息。

### <a name="create-a-new-project"></a>建立新專案

Azure IoT Edge Tools 會針對 Visual Studio 中所有支援的 IoT Edge 模組語言，提供專案範本。 這些範本具有您部署運作中模組以測試 IoT Edge 時需要的所有檔案和程式碼，或可成為您利用自己的商務邏輯自訂此範本的起點。

1. 啟動 Visual Studio 2019，然後選取 [建立新專案]  。

2. 搜尋 [IoT Edge]  ，然後選擇 [Azure IoT Edge (Windows amd64)]  專案。 按 [下一步]  。

   ![建立新的 Azure IoT Edge 專案](./media/tutorial-csharp-module-windows/new-project.png)

3. 將專案和解決方案重新命名為 **CSharpTutorialApp** 之類的描述性項目。 按一下 [建立]  以建立專案。

   ![設定新的 Azure IoT Edge 專案](./media/tutorial-csharp-module-windows/configure-project.png)

4. 使用下列值設定專案：

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取範本 | 選取 [C# 模組]  。 |
   | 模組專案名稱 | 將模組命名為 **CSharpModule**。 |
   | Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會從模組專案名稱值預先填入容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的**登入伺服器**值。 您可以在 Azure 入口網站中，從容器登錄的概觀頁面擷取登入伺服器。 <br><br> 最終的映像存放庫看起來類似於：\<registry name\>.azurecr.io/csharpmodule。 |

   ![針對目標裝置、模組類型和容器登錄設定您的專案](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. 選取 [新增]  以建立專案。

### <a name="add-your-registry-credentials"></a>新增登錄認證

部署資訊清單會與 IoT Edge 執行階段共用您的容器登錄認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。 使用您 Azure 容器登錄的 [存取金鑰]  區段中的認證。

1. 在 Visual Studio 方案總管中，開啟 **deployment.template.json** 檔案。

2. 在 $edgeAgent 所需的屬性中尋找 **registryCredentials** 屬性。 其中應該會從您在建立專案時提供的資訊自動填入您的登錄位址，而使用者名稱和密碼欄位應該會包含變數名稱。 例如：

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. 開啟模組解決方案中的 **.env** 檔案。 (該檔案依預設會隱藏於方案總管中，因此您可能需要選取 [顯示所有檔案]  按鈕加以顯示。).env 檔案應該包含您在 deployment.template. json 檔案中看到的相同使用者名稱和密碼變數。

4. 從您的 Azure Container Registry 新增 [使用者名稱]  和 [密碼]  值。

5. 將變更儲存至 .env 檔案。

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

預設模組程式碼會透過輸入佇列接收訊息，並透過輸出佇列傳遞這些訊息。 讓我們新增一些額外的程式碼，讓模組在邊緣處理訊息，然後再將它們轉送到 IoT 中樞。 更新模組，以分析每則訊息中的溫度資料，而且只有在溫度超過特定閾值時，才會將訊息傳送到 IoT 中樞。

1. 在 Visual Studio 中，開啟 **CSharpModule** > **Program.cs**。

2. 在 **CSharpModule** 命名空間頂端，為稍後會用到的類型新增三個 **using** 陳述式：

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. 將 **temperatureThreshold** 變數新增至 counter 變數之後的 [Program]  類別。 temperatureThreshold 變數會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. 將 [MessageBody]  、[Machine]  和 [Ambient]  類別新增至變數宣告之後的 [Program]  類別。 這些類別會定義內送郵件本文的預期結構描述。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

5. 尋找 **Init** 方法。 此方法會建立並設定 **ModuleClient** 物件，其可讓模組連線至本機 Azure IoT Edge 執行階段，以便傳送和接收訊息。 程式碼也會註冊回撥，以透過 **input1** 端點接收來自 IoT Edge 中樞的訊息。

   以下列程式碼取代整個 Init 方法：

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   此更新後的 Init 方法仍會使用 ModuleClient 設定對 IoT Edge 執行階段的連線，但也會增加新功能。 它會讀取模組對應項的所需屬性以擷取 **temperatureThreshold** 值。 然後，它會建立回呼，以接聽模組對應項所需屬性的任何未來更新。 透過此回呼，您可以在遠端更新模組對應項中的溫度閾值，而變更將會併入模組中。

   已更新的 Init 方法也會變更現有的 **SetInputMessageHandlerAsync** 方法。 在範例程式碼中，*input1* 上的傳入訊息會利用 *PipeMessage* 函式進行處理，但我們想要將它變更為使用我們將在下列步驟中建立的 *FilterMessages* 函式。

6. 將新的 **onDesiredPropertiesUpdate** 方法新增至 [Program]  類別。 此方法會從模組對應項接收所需的屬性，並會更新 **temperatureThreshold** 變數以符合該屬性。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. 移除範例 **PipeMessage** 方法，並將它取代為新的 **FilterMessages** 方法。 每當模組從 IoT Edge 中樞接收到訊息時，就會呼叫此方法。 它會篩選所報告溫度低於 (透過模組對應項所設定) 之溫度閾值的訊息。 針對具有設定為 [警示]  之值的訊息，此方法也會將 **MessageType** 屬性新增至該訊息。

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. 儲存 Program.cs 檔案。

9. 開啟 IoT Edge 解決方案中的 **deployment.template.json** 檔案。 此檔案會告訴 IoT Edge 代理程式要部署哪些模組 (在此情況下為 **SimulatedTemperatureSensor** 和 **CSharpModule**)，並告知 IoT Edge 中樞如何在其間路由傳送訊息。

10. 在部署資訊清單中新增 **CSharpModule** 模組對應項。 在 **modulesContent** 區段底部，於 **$edgeHub** 模組對應項後面插入下列 JSON 內容：

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![將模組對應項新增至部署範本](./media/tutorial-csharp-module-windows/module-twin.png)

11. 儲存 deployment.template.json 檔案。

## <a name="build-and-push-your-module"></a>建置和推送模組

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **CSharpModule**，以篩選掉報告的機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。

1. 在您的開發機器上使用下列命令登入 Docker。 使用您的 Azure 容器登錄中的使用者名稱、密碼和登入伺服器。 您可以在 Azure 入口網站中，從登錄的 [存取金鑰]  區段擷取這些值。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   您可能會收到安全性警告，建議您使用 `--password-stdin`。 雖然建議生產案例使用該最佳做法，但是不在本教學課程的討論範圍內。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 參考。

2. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下您想要建置的專案名稱。 預設名稱為 **AzureIotEdgeApp1**，且您要建置 Windows 模組，因此擴充功能應該為 **Windows.Amd64**。

3. 選取 [建置和推送 IoT Edge 模組]  。

   建置和推送命令會啟動三項作業。 首先，它會在解決方案中建立名為 **config** 的新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。

## <a name="deploy-modules-to-device"></a>將模組部署到裝置

使用 Visual Studio Cloud Explorer 和 Azure IoT Edge Tools 擴充功能，將模組專案部署到您的 IoT Edge 裝置。 您已備妥您的案例所需的部署資訊清單，即 config 資料夾中的 **deployment.windows-amd64.json** 檔案。 現在您只需選取要接收部署的裝置即可。

請確定您的 IoT Edge 裝置已啟動並執行。

1. 在 Visual Studio Cloud Explorer 中，展開資源以查看您的 IoT 裝置清單。

2. 以滑鼠右鍵按一下您要接收部署的 IoT Edge 裝置名稱。

3. 選取 [建立部署]。

4. 在檔案總管中，選取您解決方案的 config 資料夾中的 **deployment.windows-amd64.json** 檔案。

5. 重新整理 Cloud Explorer，以查看列在您裝置之下的已部署模組。

## <a name="view-generated-data"></a>檢視產生的資料

在您將部署資訊清單套用至 IoT Edge 裝置後，裝置的 IoT Edge 執行階段即會收集新的部署資訊，並開始在裝置上執行。 裝置上任何執行中、但未包含在部署資訊清單中的模組都會停止。 裝置中遺漏的任何模組都會啟動。

您可以在訊息送達 IoT 中樞時，使用 IoT Edge Tools 擴充功能檢視訊息。

1. 在 Visual Studio Cloud Explorer 中，選取您的 IoT Edge 裝置名稱。

2. 在 [動作] 清單中，選取 [開始監視內建事件端點]。

3. 檢視送達 IoT 中樞的訊息。 訊息可能需要一段時間才會送達，因為我們對 CSharpModule 程式碼所做的變更會等到機器溫度達到 25 度時才會傳送訊息。 它也會將 [警示] 訊息類型新增至任何觸達該溫度閾值的訊息。

   ![檢視送達 IoT 中樞的訊息](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>編輯模組對應項

我們已使用 CSharpModule 模組對應項來設定 25 度的溫度閾值。 您可以使用模組對應項來變更此功能，而不必更新模組程式碼。

1. 在 Visual Studio 中，開啟 **deployment.windows-amd64.json** 檔案。 (並非 deployment.template 檔案。 如果您在方案總管中沒看到 config 檔案中的部署資訊清單，請選取總管工具列中的 [顯示所有檔案] 圖示。)

2. 尋找 CSharpModule 對應項，然後將 **temperatureThreshold** 參數的值變更為高於最近報告溫度 5 到 10 度的新溫度。

3. 儲存 **deployment.windows-amd64.json** 檔案。

4. 請再次依照部署步驟，將更新後的部署資訊清單套用到您的裝置。

5. 監視傳入的裝置到雲端訊息。 在新的溫度閾值送達前，您應該會看到訊息停止。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，可以刪除您在本文中使用的本機設定和 Azure 資源，以避免產生費用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 當您準備好建置自己的模組時，可以進一步了解如何[開發自己的 IoT Edge 模組](module-development.md)或如何[使用 Visual Studio 開發模組](how-to-visual-studio-develop-module.md)。 如需 IoT Edge 模組 (包括模擬溫度模組) 的範例，請參閱 [IoT Edge 模組範例](https://github.com/Azure/iotedge/tree/master/edge-modules)。

您可以繼續進行後續教學課程，以了解 Azure IoT Edge 如何協助您部署 Azure 雲端服務，以在邊緣處理和分析資料。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [串流分析](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [自訂視覺服務](tutorial-deploy-custom-vision.md)
