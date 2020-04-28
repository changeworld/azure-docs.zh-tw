---
title: 教學課程 - 使用 Azure IoT Edge 自訂 Java 模組的教學課程
description: 本教學課程說明如何使用 Java 程式碼建立 IoT Edge 模組，並部署至邊緣裝置。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: d8ea58dca8235b6dfc49c14c519dd44dabdf0592
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733073"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>教學課程：開發適用於 Linux 裝置的 Java IoT Edge 模組

您可以使用 Azure IoT Edge 模組來部署程式碼，直接在 IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於在 [Linux](quickstart-linux.md) 中的模擬裝置上部署 Azure IoT Edge 的快速入門中。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用 Visual Studio Code 來根據 Azure IoT Edge maven 範本套件和 Azure IoT Java 裝置 SDK，建立 IoT Edge Java 模組。
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發佈至您的登錄中。
> * 將模組部署到您的 IoT Edge 裝置。
> * 檢視產生的資料。

您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解決方案範圍

本教學課程示範如何使用 **Visual Studio Code** 以 **Java** 開發模組，以及如何將其部署到 **Linux 裝置**。 IoT Edge 不支援適用於 Windows 裝置的 Java 模組。

使用下表來了解開發及部署 Java 模組的選項：

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![在 Linux AMD64 上將 VS Code 使用於 Java 模組](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![在 Linux ARM32 上將 VS Code 使用於 Java 模組](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Prerequisites

在開始本教學課程之前，您應該已經完成先前的教學課程，以針對 Linux 容器開發設定您的開發環境：[開發適用於 Linux 裝置的 IoT Edge 模組](tutorial-develop-for-linux.md)。 完成上述任一教學課程後，您應該會具備下列必要條件：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 Linux 裝置](quickstart-linux.md)
* 容器登錄，像是 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)。
* 已設定 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* [Docker CE](https://docs.docker.com/install/) 設定為執行 Linux 容器。

若要以 Java 開發 IoT Edge 模組，請在您的開發機器上安裝下列其他必要條件： 

* 適用於 Visual Studio Code 的 [Java 擴充套件](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) (英文)。
* [Java SE 開發套件 10](https://aka.ms/azure-jdks) (英文)，並[設定 `JAVA_HOME` 環境變數](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) (英文) 指向 JDK 安裝。
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>建立模組專案

下列步驟會建立一個 IoT Edge 模組專案，此專案會以 Azure IoT Edge maven 範本套件和 Azure IoT Java 裝置 SDK 為基礎。 您需使用 Visual Studio Code 和 Azure IoT 工具來建立此專案。

### <a name="create-a-new-project"></a>建立新專案

建立可讓您使用自己的程式碼自訂的 Java 解決方案範本。

1. 在 Visual Studio Code 中，選取 [檢視]   > [命令選擇區]  ，以開啟 VS Code 命令選擇區。

2. 在命令選擇區中，輸入並執行命令 Azure IoT Edge：**新增 IoT Edge 解決方案**。 依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution**。 |
   | 選取模組範本 | 選擇 [Java 模組]  。 |
   | 提供 groupId 值 | 輸入群組識別碼值，或接受預設值 **com.edgemodule**。 |
   | 提供模組名稱 | 將模組命名為 **JavaModule**。 |
   | 提供模組的 Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 您的容器映像會從您在上一個步驟中提供的名稱預先填入。 將 **localhost:5000** 取代為 Azure Container Registry 的登入伺服器值。 您可以在 Azure 入口網站中，從容器登錄的 [概觀] 頁面擷取登入伺服器。 <br><br>最終的映像存放庫看起來類似於：\<登錄名稱\>.azurecr.io/javamodule。 |

   ![提供 Docker 映像存放庫](./media/tutorial-java-module/repository.png)

如果您第一次建立 Java 模組，可能需要幾分鐘的時間下載 maven 套件。 當解決方案準備就緒時，VS Code 視窗會載入您的 IoT Edge 解決方案工作區。 解決方案工作區包含五個最上層元件：

* [modules]  資料夾包含您模組的 Java 程式碼，以及用來將模組建置成容器映像的 Docker 檔案。
* **\.env** 檔案會儲存您的容器登錄認證。
* **Deployment.template.json** 檔案包含 IoT Edge 執行階段用來在裝置上部署模組的資訊。
* **deployment.debug.template.json** 檔案包含模組的偵錯版本。
* 您在本教學課程中將不會編輯 **\.vscode** 資料夾或 **\.gitignore** 檔案。

如果您在建立解決方案時未指定容器登錄，但接受了預設的 localhost:5000 值，則不會有 \.env 檔案。

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器登錄的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。

1. 在 VS Code 總管中，開啟 .env 檔案。
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱]  和 [密碼]  值來更新欄位。
3. 儲存這個檔案。

### <a name="select-your-target-architecture"></a>選取您的目標架構

目前，Visual Studio Code 可以開發適用於 Linux AMD64 和 Linux ARM32v7 裝置的 Java 模組。 您必須為每個解決方案都選取要作為目標的架構，因為容器是針對每個架構類型，以不同方式建置和執行。 預設值為 Linux AMD64。

1. 開啟命令選擇區並搜尋 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或選取視窗底部側邊欄的捷徑圖示。

2. 在命令選擇區中，從選項清單中選取目標架構。 針對此教學課程，我們是使用 Ubuntu 虛擬機器作為 IoT Edge 裝置，因此會保留預設 **amd64**。

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

1. 在 VS Code 總管中，開啟 [modules]   > [JavaModule]   > [src]   > [main]   > [java]   > [com]   > [edgemodule]   > [App.java]  。

2. 在檔案頂端新增下列程式碼，以匯入新的參考類別。

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;

    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;

    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. 將下列定義新增至 **App** 類別。 此變數會設定溫度閾值。 所測得的機器溫度在超出此值之後才會回報給「IoT 中樞」。

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. 將 **MessageCallbackMqtt** 的執行方法換成下列程式碼。 每當模組從 IoT Edge 中樞收到 MQTT 訊息時，就會呼叫此方法。 它會篩選所報告溫度低於 (透過模組對應項所設定) 之溫度閾值的訊息。

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;

            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. 將下列兩個靜態內部類別新增至 **App** 類別。 這些類別會在模組對應項的預期屬性變更時更新 tempThreshold 變數。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }

    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. 在 **main** 方法的 **client.open()** 後面新增下列幾行，以訂閱模組對應項的更新。

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. 儲存 App.java 檔案。

8. 在 VS Code 總管中，於 IoT Edge 解決方案工作區中開啟 **deployment.template.json** 檔案。

9. 將 **JavaModule** 模組對應項新增至部署資訊清單。 在 **moduleContent** 區段底部，於 **$edgeHub** 模組對應項後面插入下列 JSON 內容：

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![將模組對應項新增至部署範本](./media/tutorial-java-module/module-twin.png)

10. 儲存 deployment.template.json 檔案。

## <a name="build-and-push-your-module"></a>建置和推送模組

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **JavaModule**，以篩選掉所回報機器溫度低於可接受限制的訊息。 現在，請將解決方案建置成容器映像，並將其推送到容器登錄。

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

4. 按一下 [重新整理] 按鈕。 您應該會看到新的 **JavaModule** 正在與 **SimulatedTemperatureSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。  

## <a name="view-the-generated-data"></a>檢視所產生的資料

在您將部署資訊清單套用至 IoT Edge 裝置後，裝置的 IoT Edge 執行階段即會收集新的部署資訊，並開始在裝置上執行。 裝置上任何執行中、但未包含在部署資訊清單中的模組都會停止。 裝置中遺漏的任何模組都會啟動。

您可以使用 Visual Studio Code 總管的 [Azure IoT 中樞裝置]  區段，檢視 IoT Edge 裝置的狀態。 請展開裝置的詳細資料，以查看已部署且執行中的模組清單。

1. 在 Visual Studio Code 總管中，以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [開始監視內建事件端點]  。

2. 檢視送達 IoT 中樞的訊息。 訊息可能需要一段時間才會送達。 IoT Edge 裝置必須接收其新的部署，並啟動所有模組。 然後，我們對 JavaModule 程式碼所做的變更會等到機器溫度達到 25 度時才會傳送訊息。 它也會將 [警示]  訊息類型新增至任何觸達該溫度閾值的訊息。

## <a name="edit-the-module-twin"></a>編輯模組對應項

我們已使用部署資訊清單中的 JavaModule 模組對應項，設定 25 度的溫度閾值。 您可以使用模組對應項來變更此功能，而不必更新模組程式碼。

1. 在 Visual Studio Code 中，展開您 IoT Edge 裝置底下的詳細資料，以查看執行中的模組。

2. 以滑鼠右鍵按一下 **JavaModule** 並選取 [編輯模組對應項]  。

3. 在所需的屬性中尋找 **TemperatureThreshold**。 將其值變更為高於最近報告溫度 5 到 10 度的新溫度。

4. 儲存模組對應項檔案。

5. 以滑鼠右鍵按一下模組對應項編輯窗格中的任意位置，然後選取 [更新模組對應項]  。

6. 監視傳入的裝置到雲端訊息。 在新的溫度閾值送達前，您應該會看到訊息停止。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 IoT Edge 模組，其可篩選您 IoT Edge 裝置所產生的原始資料。 當您準備好建置自己的模組時，可以進一步了解如何[開發 IoT Edge 模組](module-development.md)或如何[使用 Visual Studio Code 開發模組](how-to-vs-code-develop-module.md)。 如需程式碼範例 (包括模擬溫度模組)，請參閱 [IoT Edge 模組範例](https://github.com/Azure/iotedge/tree/master/edge-modules)。

繼續進行後續教學課程，以了解 Azure IoT Edge 如何協助您部署 Azure 雲端服務，以在邊緣處理和分析資料。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [串流分析](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [自訂視覺服務](tutorial-deploy-custom-vision.md)
