---
title: 教學課程：將 Azure 函式部署為模組 - Azure IoT Edge
description: 在本教學課程中，您會將 Azure 函式部署為 IoT Edge 模組，然後將它部署至邊緣裝置。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1f4b3387375b52b28600590a099884277f3de63e
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943022"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>教學課程：將 Azure 函式部署為 IoT Edge 模組

您可以使用 Azure Functions 來部署程式碼，直接在 Azure IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署 Azure 函式，能在模擬的 IoT Edge 裝置上篩選感應器資料。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於在 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 中的模擬裝置上部署 Azure IoT Edge 的快速入門中。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用 Visual Studio Code 來建立 Azure 函式。
> * 使用 VS Code 和 Docker 建立 Docker 映像，並將其發佈至您的容器登錄。
> * 從容器登錄將模組部署到您的 IoT Edge 裝置。
> * 檢視已篩選的資料。

<center>

![圖表 - 教學課程架構：階段和部署函式模組](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Azure IoT Edge 上的 Azure Function 模組目前為[公開預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您於此教學課程中建立的 Azure 函式，能夠篩選由您裝置所產生的溫度資料。 函式只有在溫度超過指定的閾值時，才會將訊息往上游傳送至 Azure IoT 中樞。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

在開始本教學課程之前，您應該已經完成先前的教學課程，以針對 Linux 容器開發設定您的開發環境：[開發適用於 Linux 裝置的 IoT Edge 模組](tutorial-develop-for-linux.md)。 完成該教學課程之後，您應該會具備下列必要條件：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 Linux 裝置](quickstart-linux.md)
* 容器登錄，像是 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)。
* 已設定 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* [Docker CE](https://docs.docker.com/install/) 設定為執行 Linux 容器。

若要以 Azure Functions 開發 IoT Edge 模組，請在您的開發機器上安裝下列其他必要條件：

* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-function-project"></a>建立函式專案

您在必要條件中安裝的「適用於 Visual Studio Code 的 Azure IoT 工具」，可提供管理功能以及一些程式碼範本。 在本節中，您會使用 Visual Studio Code 建立包含 Azure 函式的 IoT Edge 解決方案。

### <a name="create-a-new-project"></a>建立新專案

建立您可以使用自己的程式碼自訂的 C# 函式解決方案範本。

1. 在您的開發電腦上開啟 Visual Studio Code。

2. 選取 [檢視]   > [命令選擇區]  ，以開啟 VS Code 命令選擇區。

3. 在命令選擇區中，輸入並執行命令 Azure IoT Edge：**新增 IoT Edge 解決方案**。 依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱 (例如 **FunctionSolution**)，或接受預設值。 |
   | 選取模組範本 | 選擇 [Azure Functions - C#]  。 |
   | 提供模組名稱 | 將模組命名為 **CSharpFunction**。 |
   | 提供模組的 Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會預先填入上一個步驟的容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的登入伺服器值。 您可以在 Azure 入口網站中，從容器登錄的 [概觀] 頁面擷取登入伺服器。 最終字串看起來會類似於：\<登錄名稱\>.azurecr.io/CSharpFunction。 |

   ![提供 Docker 映像存放庫](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器登錄的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。

1. 在 VS Code 總管中，開啟 .env 檔案。
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱]  和 [密碼]  值來更新欄位。
3. 儲存這個檔案。

### <a name="select-your-target-architecture"></a>選取您的目標架構

目前，Visual Studio Code 可以開發適用於 Linux AMD64 和 Linux ARM32v7 裝置的 C 模組。 您必須為每個解決方案都選取要作為目標的架構，因為容器是針對每個架構類型，以不同方式建置和執行。 預設值為 Linux AMD64。

1. 開啟命令選擇區並搜尋 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或選取視窗底部側邊欄的捷徑圖示。

2. 在命令選擇區中，從選項清單中選取目標架構。 針對此教學課程，我們是使用 Ubuntu 虛擬機器作為 IoT Edge 裝置，因此會保留預設 **amd64**。

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

讓我們新增一些額外的程式碼，讓模組在邊緣處理訊息，然後再將它們轉送到 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [模組]   > [CSharpFunction]   > [CSharpFunction.cs]  。

1. 以下列程式碼取代 **CSharpFunction.cs** 檔案的內容。 此程式碼會接收有關環境和機器溫度的遙測，並只會在機器溫度高於所定義的閾值時，才將訊息轉送到 IoT 中樞。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. 儲存檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在上一節中，您已建立 IoT Edge 解決方案 並修改 **CSharpFunction**，以篩選出回報的機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。

在本節中，您第二次提供容器登錄的認證 (第一次是 IoT Edge 解決方案的 **.env** 檔案)，藉由從開發電腦本機登入，讓 Visual Studio Code 可以將映像推送至您的登錄。

1. 選取 [檢視]   > [終端機]  ，以開啟 VS Code 整合式終端機。

2. 在整合式終端機中輸入下列命令，以登入您的容器登錄。 請使用您先前從 Azure 容器登錄複製而來的使用者名稱和登入伺服器。

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    當系統提示您輸入密碼時，請貼上容器登錄的密碼 (密碼不會顯示在終端機視窗中)，然後按 **Enter** 鍵。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. 在 VS Code 總管中，以滑鼠右鍵按一下 deployment.template.json 檔案，然後選取 [建置並推送 IoT Edge 解決方案]  。

當您指示 Visual Studio Code 建置解決方案時，它會先擷取部署範本中的資訊，再於名為 **config** 的新資料夾中，產生 deployment.json 檔案。然後，它會在整合式終端機中執行兩個命令：`docker build` 和 `docker push`。 建置命令會建立您的程式碼並將函式容器化。 然後，推送命令會將程式碼推送至您在初始化解決方案時所指定的容器登錄。

## <a name="view-your-container-image"></a>檢視容器映像

當容器映像推送至您的容器登錄時，Visual Studio Code 會輸出成功訊息。 如果您想要確認本身作業成功，您可以檢視登錄中的映像。

1. 在 Azure 入口網站中，瀏覽到您的 Azure 容器登錄。
2. 選取 [存放庫]  。
3. 您應該會在清單中看到 **csharpfunction** 存放庫。 選取此存放庫可查看詳細資料。
4. 在 [標記]  區段中，您應該會看到 **0.0.1-amd64** 標記。 此標記表示您所建置的映像版本和平台。 這些值設定於 CSharpFunction 資料夾內的 module.json 檔案中。

## <a name="deploy-and-run-the-solution"></a>部署並執行解決方案

您可以使用 Azure 入口網站將函式模組部署到 IoT Edge 裝置，如同您在快速入門中所做的一樣。 您也可以從 Visual Studio Code 內部署和監視模組。 下列幾節會使用先決條件中所列的適用於 VS Code 的 Azure IoT 工具。 請立即安裝擴充功能 (如果您尚未安裝)。

1. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置]  區段。

2. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [建立單一裝置的部署]  。

3. 瀏覽至 **CSharpFunction** 所在的解決方案資料夾。 開啟 config 資料夾、選取 **deployment.json** 檔案，然後選擇 [選取 Edge 部署資訊清單]  。

4. 重新整理 [Azure IoT 中樞裝置]  區段。 您應該會看到新的 **CSharpFunction** 正在與 **SimulatedTemperatureSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。 新模組可能需要一些時間才會出現。 IoT Edge 裝置必須從 IoT 中樞擷取其新的部署資訊、啟動新容器，然後向 IoT 中樞回報狀態。

   ![在 VS Code 中檢視已部署的模組](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>檢視所產生的資料

您可以在命令選擇區中執行 Azure IoT Hub:**開始監視內建事件端點**。

您也可以篩選檢視來查看所有從特定裝置送達 IoT 中樞的訊息。 請以滑鼠右鍵按一下 [Azure IoT 中樞裝置]  區段中的裝置，然後選取 [開始監視內建事件端點]  。

若要停止監視訊息，請在命令選擇區中執行命令 Azure IoT Hub:**停止監視內建事件端點**。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 Azure 函式模組，可篩選您 IoT Edge 裝置所產生的原始資料。 當您準備好要建置自己的模組時，可以深入了解如何[使用適用於 Visual Studio Code 的 Azure IoT Edge 開發](how-to-vs-code-develop-module.md)。

請繼續進行後續教學課程，以了解 Azure IoT Edge 有什麼其他方法，可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [使用 Azure 串流分析中的浮動視窗尋找平均值](tutorial-deploy-stream-analytics.md)
