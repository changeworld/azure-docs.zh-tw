---
title: 針對 Linux 開發 Node.js 模組的教學課程 - Azure IoT Edge | Microsoft Docs
description: 本教學課程說明如何使用 Node.js 程式碼建立 IoT Edge 模組並將它部署到邊緣裝置
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 71b22bf9bf040abcdf513a4f8baa916930c8972e
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772213"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>教學課程：為 Linux 裝置開發及部署 Node.js IoT Edge 模組

使用 Visual Studio Code 開發 Node.js 程式碼，並將它部署到執行 Azure IoT Edge 的 Linux 裝置。

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將使用您在快速入門中建立的模擬 IoT Edge 裝置。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用 Visual Studio Code 建立 IoT Edge Node.js 模組
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發行至您的登錄中
> * 將模組部署到您的 IoT Edge 裝置
> * 檢視產生的資料

您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您的裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解決方案範圍

本教學課程示範如何使用 **Visual Studio Code** 在 **Node.js** 中開發模組，以及如何將它部署到 **Linux 裝置**。 IoT Edge 不支援適用於 Windows 裝置的 Node.js 模組。

使用下表來了解開發及部署 Node.js 模組的選項：

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![在 Linux AMD64 上將 VS Code 使用於 Node.js 模組](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![在 Linux ARM32 上將 VS Code 使用於 Node.js 模組](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Prerequisites

在開始本教學課程之前，您應該已經完成先前的教學課程，以針對 Linux 容器開發設定您的開發環境：[開發適用於 Linux 裝置的 IoT Edge 模組](tutorial-develop-for-linux.md)。 完成上述任一教學課程後，您應該會具備下列必要條件：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 Linux 裝置](quickstart-linux.md)
* 容器登錄，像是 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)。
* 已設定 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* [Docker CE](https://docs.docker.com/install/) 設定為執行 Linux 容器。

若要以 Node.js 開發 IoT Edge 模組，請在您的開發機器上安裝下列其他必要條件：

* [Node.js 和 npm](https://nodejs.org)。 npm 套件會隨著 Node.js 一起散發，這表示當您下載 Node.js 時，就會自動將 npm 安裝到電腦上。

## <a name="create-a-module-project"></a>建立模組專案

下列步驟會示範如何使用 Visual Studio Code 與 Azure IoT 工具來建立 IoT Edge Node.js 模組。

### <a name="create-a-new-project"></a>建立新專案

使用 **npm** 來建立 Node.js 解決方案範本，以供作為您的建置基礎。

1. 在 Visual Studio Code 中，選取 [檢視]   > [整合式終端機]  以開啟 VS Code 整合式終端機。

2. 在整合式終端機中，輸入下列命令來安裝適用於 Node.js Azure IoT Edge 模組的 **yeoman** 和產生器：

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. 選取 [檢視]   > [命令選擇區]  ，以開啟 VS Code 命令選擇區。

4. 在命令選擇區中，輸入並執行命令 **[Azure:** 登入]，然後遵循指示來登入您的 Azure 帳戶。 如果您已登入，則可以略過此步驟。

5. 在命令選擇區中，輸入並執行命令 [Azure IoT Edge:  新增 IoT Edge 解決方案]。 依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution**。 |
   | 選取模組範本 | 選擇 [Node.js 模組]  。 |
   | 提供模組名稱 | 將模組命名為 **NodeModule**。 |
   | 提供模組的 Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 您的容器映像會從您在上一個步驟中提供的名稱預先填入。 將 **localhost:5000** 取代為 Azure Container Registry 的登入伺服器值。 您可以在 Azure 入口網站中，從容器登錄的 [概觀] 頁面擷取登入伺服器。 <br><br>最終的映像存放庫看起來類似於：\<登錄名稱\>.azurecr.io/nodemodule。 |

   ![提供 Docker 映像存放庫](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器存放庫的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。

1. 在 VS Code 總管中，開啟 **.env** 檔案。
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱]  和 [密碼]  值來更新欄位。
3. 儲存這個檔案。

### <a name="select-your-target-architecture"></a>選取您的目標架構

目前，Visual Studio Code 可以開發適用於 Linux AMD64 和 Linux ARM32v7 裝置的 Node.js 模組。 您必須為每個解決方案都選取要作為目標的架構，因為容器是針對每個架構類型，以不同方式建置和執行。 預設值為 Linux AMD64。

1. 開啟命令選擇區並搜尋 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或選取視窗底部側邊欄的捷徑圖示。

2. 在命令選擇區中，從選項清單中選取目標架構。 針對此教學課程，我們是使用 Ubuntu 虛擬機器作為 IoT Edge 裝置，因此會保留預設 **amd64**。

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

每個範本均隨附程式碼範例，會從 **SimulatedTemperatureSensor** 模組擷取模擬感應器資料，再將資料路由傳送到 IoT 中樞。 在本節中，請新增程式碼讓 NodeModule 先分析訊息再傳送。

1. 在 VS Code 總管中，開啟 [modules]   > [NodeModule]   > [app.js]  。

2. 在所需的節點模組下面新增溫度閾值變數。 溫度閾值會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。

    ```javascript
    var temperatureThreshold = 25;
    ```

3. 使用 `FilterMessage` 函式來取代整個 `PipeMessage` 函式。

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. 在 `client.on()` 函式中，使用 `filterMessage` 函式來取代函式名稱 `pipeMessage`。

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. 將下列程式碼片段複製到 `client.open()` 函式回呼中，`else` 陳述式內的 `client.on()` 之後。 當所需屬性更新時，便會叫用此函式。

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. 儲存 app.js 檔案。

7. 在 VS Code 總管中，於 IoT Edge 解決方案工作區中開啟 **deployment.template.json** 檔案。

8. 在部署資訊清單中新增 NodeModule 模組對應項。 在 `moduleContent` 區段底部，於 `$edgeHub` 模組對應項後面插入下列 JSON 內容：

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![將模組對應項新增至部署範本](./media/tutorial-node-module/module-twin.png)

9. 儲存 deployment.template.json 檔案。

## <a name="build-and-push-your-module"></a>建置和推送模組

在上一節中，您已建立 IoT Edge 解決方案，並在 NodeModule 中新增了程式碼，以篩選出回報的機器溫度在可接受限制內的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。

1. 選取 [檢視]   > [終端機]  ，以開啟 VS Code 整合式終端機。

1. 在終端機中輸入下列命令來登入 Docker。 使用您 Azure 容器登錄中的使用者名稱、密碼和登入伺服器登入。 您可以在 Azure 入口網站中，從登錄的 [存取金鑰]  區段擷取這些值。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   您可能會收到安全性警告，建議您使用 `--password-stdin`。 雖然建議生產案例使用該最佳做法，但是不在本教學課程的討論範圍內。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 參考。

1. 在 VS Code 總管中，以滑鼠右鍵按一下 **deployment.template.json** 檔案，然後選取 [建置並推送 IoT Edge 解決方案]  。

   建置和推送命令會啟動三項作業。 首先，它會在解決方案中建立名為 **config** 的新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。

## <a name="deploy-modules-to-device"></a>將模組部署到裝置

使用 Visual Studio Code 總管和 Azure IoT Tools 擴充功能，將模組專案部署到您的 IoT Edge 裝置。 您已備妥您的案例所需的部署資訊清單，即 config 資料夾中的 **deployment.json** 檔案。 現在您只需選取要接收部署的裝置即可。

請確定您的 IoT Edge 裝置已啟動並執行。

1. 在 Visual Studio Code 總管中，展開 [Azure IoT 中樞裝置]  區段來查看您的 IoT 裝置清單。

2. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [建立單一裝置的部署]  。

3. 選取 **config** 資料夾中的 **deployment.json** 檔案，然後按一下 [選取 Edge 部署資訊清單]  。 請勿使用 deployment.template.json 檔案。

4. 按一下 [重新整理] 按鈕。 您應該會看到新的 **NodeModule** 正在與 **SimulatedTemperatureSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。

## <a name="view-the-generated-data"></a>檢視所產生的資料

在您將部署資訊清單套用至 IoT Edge 裝置後，裝置的 IoT Edge 執行階段即會收集新的部署資訊，並開始在裝置上執行。 裝置上任何執行中、但未包含在部署資訊清單中的模組都會停止。 裝置中遺漏的任何模組都會啟動。

您可以使用 Visual Studio Code 總管的 [Azure IoT 中樞裝置]  區段，檢視 IoT Edge 裝置的狀態。 請展開裝置的詳細資料，以查看已部署且執行中的模組清單。

1. 在 Visual Studio Code 總管中，以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [開始監視內建事件端點]  。

2. 檢視送達 IoT 中樞的訊息。 訊息可能需要一段時間才會送達。 IoT Edge 裝置必須接收其新的部署，並啟動所有模組。 然後，我們對 NodeModule 程式碼所做的變更會等到機器溫度達到 25 度時才會傳送訊息。 它也會將 [警示]  訊息類型新增至任何觸達該溫度閾值的訊息。

## <a name="edit-the-module-twin"></a>編輯模組對應項

我們已使用部署資訊清單中的 NodeModule 模組對應項，設定 25 度的溫度閾值。 您可以使用模組對應項來變更此功能，而不必更新模組程式碼。

1. 在 Visual Studio Code 中，展開您 IoT Edge 裝置底下的詳細資料，以查看執行中的模組。

2. 以滑鼠右鍵按一下 **NodeModule** 並選取 [編輯模組對應項]  。

3. 在所需的屬性中尋找 **TemperatureThreshold**。 將其值變更為高於最近報告溫度 5 到 10 度的新溫度。

4. 儲存模組對應項檔案。

5. 以滑鼠右鍵按一下模組對應項編輯窗格中的任意位置，然後選取 [更新模組對應項]  。

6. 監視傳入的裝置到雲端訊息。 在新的溫度閾值送達前，您應該會看到訊息停止。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 當您準備好建置自己的模組時，可以進一步了解如何[開發自己的 IoT Edge 模組](module-development.md)或如何[使用 Visual Studio Code 開發模組](how-to-vs-code-develop-module.md)。 如需 IoT Edge 模組 (包括模擬溫度模組) 的範例，請參閱 [IoT Edge 模組範例](https://github.com/Azure/iotedge/tree/master/edge-modules)。

您可以繼續進行後續教學課程，以了解 Azure IoT Edge 如何協助您部署 Azure 雲端服務，以在邊緣處理和分析資料。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [串流分析](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [自訂視覺服務](tutorial-deploy-custom-vision.md)
