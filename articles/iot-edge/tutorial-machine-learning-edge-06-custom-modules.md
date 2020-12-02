---
title: 教學課程：建立和部署自訂模組 - Azure IoT Edge 上的機器學習服務
description: 本教學課程說明如何建立和部署 IoT Edge 模組，以透過機器學習模型處理來自分葉裝置的資料，然後將深入解析傳送至 IoT 中樞。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 199da0586a061bccdf8a6ff8a1f53df2f703512f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959436"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>教學課程：建立和部署自訂 IoT Edge 模組

在此文章中，我們會建立三個 IoT Edge 模組，以接收來自分葉 IoT 裝置的訊息、透過您的機器學習模型執行資料，然後將深入解析轉送到 IoT 中樞。

IoT Edge 中樞可促進模組對模組的通訊。 使用 IoT Edge 中樞作為訊息代理程式，讓模組彼此保持獨立。 模組只需指定它們要在其中接受訊息的輸入，以及要將訊息寫入其中的輸出。

我們希望 IoT Edge 裝置為我們完成四件事：

* 接收來自分葉裝置的資料。
* 針對傳送資料的裝置預測剩餘使用年限 (RUL)。
* 將具有裝置 RUL 的訊息傳送至 IoT 中樞。 只有當 RUL 低於指定的層級時，才能修改此函式以傳送資料。
* 將分葉裝置資料儲存至 IoT Edge 裝置上的本機檔案。 這個資料檔案會定期上傳到 IoT 中樞，以精簡機器學習模型的定型。 比起持續不斷的訊息串流處理，使用檔案上傳更符合成本效益。

為了完成這些工作，我們將使用三個自訂模組：

* **RUL 分類器：** 我們在 [定型和部署 Azure Machine Learning 模型](tutorial-machine-learning-edge-04-train-model.md)中建立的 turboFanRulClassifier 模組是一個標準的機器學習模組，其會公開稱為 "amlInput" 的輸入和稱為 "amlOutput" 的輸出。 "amlInput" 預期其輸入看起來與我們傳送至以 ACI 為基礎的 Web 服務的輸入完全一樣。 同樣地，"amlOutput" 會傳回與 Web 服務相同的資料。

* **Avro 寫入器：** 此模組會接收有關 "avroModuleInput" 輸入的訊息，並以 Avro 格式將訊息保存於磁碟上，以便稍後上傳到 IoT 中樞。

* **路由器模組：** 路由器模組會接收來自下游分葉裝置的訊息，然後將訊息格式化並傳送至分類器。 此模組接著會接收來自分類器的訊息，然後將訊息轉送到 Avro 寫入器模組。 最後，模組只會將 RUL 預測傳送到 IoT 中樞。

  * 輸入：
    * **deviceInput：** 接收來自分葉裝置的訊息
    * **rulInput：** 接收來自 "amlOutput" 的訊息

  * 輸出：
    * **classify：** 將訊息傳送至 "amlInput"
    * **writeAvro：** 將訊息傳送至 "avroModuleInput"
    * **toIotHub：** 將訊息傳送至 $upstream，其會將訊息傳遞到已連線的 IoT 中樞

下圖顯示適用於完整解決方案的模組、輸入、輸出和 IoT Edge 中樞路由：

![IoT Edge 三個模組架構圖](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

此文章中的步驟通常是由雲端開發人員執行的。

## <a name="prerequisites"></a>必要條件

此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 本系列中的每篇文章皆以先前文章中的工作為基礎。 如果您是被直接引導至此文章，請參閱本系列中的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="create-a-new-iot-edge-solution"></a>建立新的 IoT Edge 解決方案

在執行我們的第二個 Azure Notebooks 期間，我們建立並發佈了包含 RUL 模型的容器映像。 Azure Machine Learning 會在映像建立過程中封裝該模型，讓該映像能夠部署為 Azure IoT Edge 模組。

在此步驟中，我們將使用 “Azure Machine Learning” 模組來建立 Azure IoT Edge 解決方案，並將該模組指向我們使用 Azure Notebooks 發佈的映像。

1. 開啟開發 VM 的遠端桌面工作階段。

1. 在 Visual Studio Code 中，開啟資料夾 **C:\\source\\IoTEdgeAndMlSample**。

1. 以滑鼠右鍵按一下總管面板 (在空白處)，然後選取 [新增 IoT Edge 解決方案]。

    ![建立新的 IoT Edge 解決方案](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. 接受預設的解決方案名稱 **EdgeSolution**。

1. 選擇 [Azure Machine Learning] 作為模組範本。

1. 將模組命名為 **turbofanRulClassifier**。

1. 選擇您的機器學習工作區。 此工作區為 **turboFanDemo** 工作區，您建立於 [教學課程：定型和部署 Azure Machine Learning 模型](tutorial-machine-learning-edge-04-train-model.md)

1. 選取您在執行 Azure Notebook 時建立的映像。

1. 查看解決方案並注意已建立的檔案：

   * **deployment.template.json：** 此檔案包含解決方案中每個模組的定義。 此檔案中有三個需要注意的區段：

     * **登錄認證：** 定義一組您要在解決方案中使用的自訂容器登錄。 現在，它應該包含來自您機器學習工作區的登錄，這是儲存您 Azure Machine Learning 映像的位置。 您可以有任意數目的容器登錄，但為求簡化，我們將針對所有模組使用這一個登錄。

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **模組：** 這個區段包含一組與此解決方案一起使用的使用者定義模組。 turbofanRulClassifier 模組定義會指向您容器登錄中的映像。 當我們在解決方案中新增更多模組時，它們將顯示於此區段中。

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **路由：** 我們將在此教學課程中大量使用路由。 路由會定義模組彼此通訊的方式。 由範本所定義的現有路由與我們需要的路由不相符。 刪除 `turbofanRulClassifierToIoTHub` 路由。

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.json：** 這個檔案是 deployment.template.json 的偵錯版本。 一般來說，我們應該將此檔案與 deployment.template.json 檔案的內容保持同步，但是在本教學課程中並不需要這麼做。

   * **.env：** 您應該在這個檔案中提供使用者名稱和密碼來存取登錄。

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. 在 Visual Studio Code 總管中，以滑鼠右鍵按一下 deployment.template.json 檔案，然後選取 [建置 IoT Edge 解決方案]。

1. 請注意，此命令會建立含有 deployment.amd64.json 檔案的 config 資料夾。 此檔案是適用於解決方案的具體部署範本。

## <a name="add-router-module"></a>新增路由器模組

接下來，會將路由器模組新增至我們的解決方案。 路由器模組會為我們的解決方案處理數個任務：

* **接收來自分葉裝置的訊息：** 當訊息從下游裝置送達 IoT Edge 裝置時，路由器模組會接收該訊息，並開始協調訊息的路由。
* **將訊息傳送至 RUL 分類器模組：** 接收到來自下游裝置的新訊息時，路由器模組會將該訊息轉換為 RUL 分類器所預期的格式。 路由器會將訊息傳送到 RUL 分類器以進行 RUL 預測。 一旦分類器做出預測之後，就會將訊息送回路由器模組。
* **將 RUL 訊息傳送至 IoT 中樞：** 當路由器接收到來自分類器的訊息時，它會轉換訊息，以便只包含基本資訊、裝置識別碼和 RUL，然後將簡略的訊息傳送至 IoT 中樞。 只有當 RUL 預測低於閾值時 (例如，當 RUL 少於 100 個週期時)，進一步精簡 (我們目前尚未完成) 才會將訊息傳送至 IoT 中樞。 使用這種方式篩選會減少訊息量，並降低 IoT 中樞的成本。
* **將訊息傳送至 Avro 寫入器模組：** 為了保留下游裝置所傳送的所有資料，路由器模組會將接收自分類器的完整訊息傳送至 Avro 寫入器模組，其將使用 IoT 中樞檔案上傳來保存和上傳資料。

路由器模組是解決方案的重要部分，可確保以正確的順序處理訊息。

### <a name="create-the-module-and-copy-files"></a>建立模組和複製檔案

1. 在 Visual Studio Code 中，以滑鼠右鍵按一下 [模組] 資料夾，然後選擇 [新增 IoT Edge 模組]。

1. 選擇 [C# 模組] 做為模組範本。

1. 將模組命名為 **turbofanRouter**。

1. 當系統提示您提供 Docker 映像存放庫時，請使用來自機器學習工作區的登錄 (您可以在 *deployment.template.json* 檔案的 registryCredentials 節點中找到此登錄)。 此值是登錄的完整位址，例如 **\<your registry\>.azurecr.io/turbofanrouter**。

    > [!NOTE]
    > 在此文章中，我們會使用 Azure Machine Learning 工作區所建立的 Azure Container Registry。 這純粹為了方便使用。 我們已建立新的容器登錄，並在該處發佈了模組。

1. 在終端機中，使用命令提示字元殼層，將檔案從範例模組複製到解決方案中。

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. 接受覆寫 program.cs 檔案的提示。

### <a name="build-router-module"></a>建置路由器模組

1. 在 Visual Studio Code 中，選取 [終端機] > [設定預設的建置工作]。

1. 選取 [從範本建立 tasks.json 檔案]。

1. 選取 [.NET Core]。

1. 將 tasks.json 檔案的內容取代為下列程式碼。

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. 儲存並關閉 tasks.json。

1. 使用 `Ctrl + Shift + B` 或 [終端機] > [執行建置工作] 來執行組建。

### <a name="set-up-module-routes"></a>設定模組路由

如先前所述，IoT Edge 執行階段會使用 *deployment.template.json* 檔案中所設定的路由，來管理相依性較低之模組間的通訊。 在此節中，我們將向下鑽研如何設定 turbofanRouter 模組的路由。 我們將先討論輸入路由，然後再討論輸出。

#### <a name="inputs"></a>輸入

1. 在 Program.cs 的 Init() 方法中，我們會針對模組註冊兩個回呼：

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. 第一個回呼會接聽傳送到 **deviceInput** 接收器的訊息。 您可以從上圖中看到我們想要將來自任意分葉裝置的訊息路由傳送到此輸入。 在 *deployment.template.json* 檔案中新增路由，以告知 Edge 中樞，將 IoT Edge 裝置所接收到且不是由 IoT Edge 模組所傳送的任何訊息路由傳送到 turbofanRouter 模組上稱為 "deviceInput" 的輸入：

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. 接下來，針對從 rulClassifier 模組到 turbofanRouter 模組的訊息新增路由：

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>輸出

在 $edgeHub 路由參數中額外新增四個路由，以處理來自路由器模組的輸出。

1. Program.cs 會定義 SendMessageToClassifier() 方法，以使用模組用戶端，利用路由來將訊息傳送至 RUL 分類器：

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() 會使用模組用戶端，透過路由，只將裝置的 RUL 資料傳送到 IoT 中樞：

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() 會使用模組用戶端，利用已新增至 avroFileWriter 模組的 RUL 資料來傳送訊息。

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() 在上游的 IoT 中樞傳送失敗訊息，以便稍後可路由傳送它們。

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

將所有路由放在一起之後，您的 “$edgeHub” 節點應該看起來類似下列 JSON：

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > 新增 turbofanRouter 模組會額外建立下列路由：`turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`。 移除此路由，僅在您的 deployment.template.json 檔案中保留上方所列的路由。

## <a name="add-avro-writer-module"></a>新增 Avro 寫入器模組

Avro 寫入器模組在我們的解決方案中有兩個任務，即儲存訊息和上傳檔案。

* **儲存訊息**：當 Avro 寫入器模組接收訊息時，它會以 Avro 格式將訊息寫入至本機檔案系統。 我們會使用繫結裝載，其會將目錄 (在此案例中為 /data/avrofiles) 裝載到模組容器中的路徑。 此裝載讓模組能夠寫入到本機路徑 (/avrofiles)，而且能夠直接從 IoT Edge 裝置存取那些檔案。

* **上傳檔案**：Avro 寫入器模組會使用 Azure IoT 中樞檔案上傳功能，將檔案上傳至 Azure 儲存體帳戶。 一旦檔案成功上傳之後，模組就會從磁碟中刪除該檔案。

### <a name="create-module-and-copy-files"></a>建立模組和複製檔案

1. 在 Visual Studio Code 中，選取 View > 命令選擇區，然後搜尋並選取 **Python：Select Interpreter**。

1. 選取您已安裝的 Python 3.7 版或更新版本。

1. 在 Visual Studio Code 中，以滑鼠右鍵按一下 [模組] 資料夾，然後選擇 [新增 IoT Edge 模組]。

1. 選擇 [Python 模組]。

1. 將模組命名為 `avroFileWriter`。

1. 系統提示您提供 Docker 映像存放庫時，使用您在新增路由器模組時所使用的相同登錄。

1. 將來自範例模組的檔案複製到解決方案。

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. 接受 main.py 的覆寫。

1. 請注意，已將 filemanager.py 和 schema.py 新增至解決方案，並已新增 main.py。

> [!NOTE]
> 當您開啟 Python 檔案時，系統可能會提示您安裝 pylint。 您不需要安裝 linter，就能完成此教學課程。

### <a name="bind-mount-for-data-files"></a>適用於資料檔案的繫結裝載

如先前所述，寫入器模組依賴既存的繫結裝載，以將 Avro 檔案寫入裝置的檔案系統中。

#### <a name="add-directory-to-device"></a>將目錄新增至裝置

1. 在 Azure 入口網站中，如果您的 IoT Edge 裝置 VM 不在執行中，請加以啟動。 使用 SSH 與其連線。 連線需要您可從 Azure 入口網站中 VM 概觀頁面複製的 DNS 名稱。

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. 登入之後，建立將保留已儲存分葉裝置訊息的目錄。

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. 更新目錄權限，讓容器可以寫入該目錄。

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. 驗證目錄現在具備使用者、群組和擁有者的寫入 (w) 權限。

   ```bash
   ls -la /data
   ```

   ![Avrofiles 的目錄權限](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>將目錄新增至模組

為了將目錄新增至模組的容器，我們將修改與 avroFileWriter 模組相關聯的 Dockerfile。 有三個與模組相關聯的 Dockerfile：Dockerfile.amd64、Dockerfile.amd64.debug 及 Dockerfile.arm32v7。 假使我們希望對 arm32 裝置進行偵錯或部署，這些檔案就應該保持同步。 此文章將只著重於 Dockerfile.amd64。

1. 在您的開發 VM 上，開啟 **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64** 檔案。

1. 修改檔案，讓它看起來類似下列範例：

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` 和 `chown` 命令會指示 Docker 建置流程在映像中建立名為 /avrofiles 的最上層目錄，然後讓 moduleuser 成為該目錄的擁有者。 在使用 `useradd` 命令來將模組使用者新增至映像之後，以及在將內容切換至 moduleuser (USER moduleuser) 之前，務必插入這些命令。

1. 如有需要，請對 Dockerfile.amd64.debug 和 Dockerfile.arm32v7 進行相對應的變更。

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>將繫結設定新增至 avroFileWriter

建立繫結的最後一個步驟是使用繫結資訊來更新 deployment.template.json (和 deployment.debug.template.json) 檔案。

1. 開啟 deployment.template.json。

2. 新增 `Binds` 參數以使容器目錄 /avrofiles 指向 Edge 裝置上的本機目錄，藉以修改 avroFileWriter 的模組定義。 您的模組定義應該符合此範例：

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>適用於存取 config.yaml 的繫結裝載

我們需要針對寫入器模組新增多個繫結。 此繫結會為模組與授與存取權，以便從 IoT Edge 裝置上的 /etc/iotedge/config.yaml 檔案讀取連接字串。 我們需要此連接字串來建立 IoTHubClient，如此便可呼叫 upload\_blob\_async 方法來將檔案上傳到 IoT 中樞。 新增此繫結的步驟類似上一節中的步驟。

#### <a name="update-directory-permission"></a>更新目錄權限

1. 使用 SSH 連線到 IoT Edge 裝置。

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. 為 config.yaml 檔案新增讀取權限。

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. 驗證權限已正確設定。

   ```bash
   ls -la /etc/iotedge/
   ```

1. 確保 config.yaml 的權限為 **-r-r-r-** 。

#### <a name="add-directory-to-module"></a>將目錄新增至模組

1. 在您的開發機器上，開啟 **Dockerfile.amd64** 檔案。

1. 在檔案中額外新增一組 `mkdir` 和 `chown` 命令，使其看起來如下：

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. 對 Dockerfile.amd64.debug 和 Dockerfile.arm32v7 進行相對應的變更。

#### <a name="update-the-module-configuration"></a>更新模組設定

1. 開啟 **deployment.template.json** 檔案。

1. 在 `Binds` 參數中新增第二行以使容器目錄 (/app/iotconfig) 指向裝置 (/etc/iotedge) 上的本機目錄，藉以修改 avroFileWriter 的模組定義。

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. 對 deployment.debug.template.json 進行相對應的變更。

## <a name="install-dependencies"></a>安裝相依性

寫入器模組依存於兩個 Python 程式庫：fastavro 和 PyYAML。 我們需要在開發機器上安裝相依性，並指示 Docker 建置流程，將其安裝於我們的模組映像中。

### <a name="pyyaml"></a>PyYAML

1. 在您的開發機器上，開啟 `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` 檔案，並在檔案中的新行新增 "pyyaml"。

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. 開啟 **Dockerfile.amd64** 檔案，並新增 `pip install` 命令來升級 setuptools。

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. 在命令提示字元中，將 pyyaml 安裝到您的開發電腦。

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. 在 requirements.txt 中，將 fastavro 新增於 pyyaml 之後。

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. 將 fastavro 安裝到您的開發電腦。

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>重新設定 IoT 中樞

藉由在系統中引進 IoT Edge 裝置和模組，我們已變更對於要基於何種用途來將哪些資料傳送至中樞的期望。 我們需要在中樞重新設定路由來處理我們新的實境。

> [!NOTE]
> 我們建議在部署模組之前先重新設定中樞，因為必須針對 avroFileWriter 模組正確設定某些中樞設定 (特別是檔案上傳)，才能使其正確執行。

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>在 IoT 中樞設定適用於 RUL 訊息的路由

適當地使用路由器和分類器，我們預期要接收僅包含裝置的裝置識別碼和 RUL 預測的一般訊息。 我們想要將 RUL 資料路由傳送至它自己的儲存位置，而我們可在其中監視裝置的狀態、建置報表，並依需要引發警示。 在此同時，我們希望仍由尚未連結到 IoT Edge 裝置之分葉裝置直接傳送的所有裝置資料，都能繼續路由傳送到目前的儲存位置。

#### <a name="create-a-rul-message-route"></a>建立 RUL 訊息路由

1. 在 Azure 入口網站中，巡覽至您的 IoT 中樞。

1. 從左窗格功能表的 [訊息] 底下，選取 [訊息路由]。

1. 在 [路由] 索引標籤上，選取 [新增]。

1. 將路由命名為 **RulMessageRoute**。

1. 選取 [端點] 選取器右邊的 [新增端點]，然後選擇 [儲存體]。

1. 在 [新增儲存體端點] 頁面上，將端點命名為 **ruldata**。

1. 選取 [挑選容器]。

1. 在 [儲存體帳戶] 頁面上，尋找您在本教學課程中使用的儲存體帳戶，其名稱類似 **iotedgeandml\<unique suffix\>** 。

1. 選取 [ruldata] 容器，然後按一下 [選取]。

1. 回到 [新增儲存體端點] 頁面上，選取 [建立] 以建立儲存體端點。

1. 回到 [新增路由] 頁面上，針對 [路由查詢]，使用下列查詢來取代 `true`：

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. 依序展開 [測試] 區段和 [訊息本文] 區段。 使用我們預期的這個訊息範例來取代訊息本文：

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. 選取 [測試路由]。 如果測試成功，您就會看到「訊息與查詢相符」。

1. 按一下 [檔案] 。

#### <a name="update-turbofandevicedatatostorage-route"></a>更新 turbofanDeviceDataToStorage 路由

我們不想將新的預測資料路由傳送到舊的儲存位置，因此需更新路由來防止它。

1. 在 IoT 中樞的 [訊息路由] 頁面中，選取 [路由] 索引標籤。

1. 選取 [turbofanDeviceDataToStorage]，或提供給您初始裝置資料路由的任何名稱。

1. 更新路由查詢以執行下列動作

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. 依序展開 [測試] 區段和 [訊息本文] 區段。 使用我們預期的這個訊息範例來取代訊息：

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. 選取 [測試路由]。 如果測試成功，您就會看到「訊息與查詢相符」。

1. 選取 [儲存]。

### <a name="configure-file-upload"></a>設定檔案上傳

設定 IoT 中樞檔案上傳功能，讓檔案寫入器模組能夠將檔案上傳至儲存體。

1. 從 IoT 中樞左窗格功能表的 [訊息] 底下，選擇 [檔案上傳]。

1. 選取 [Azure 儲存體容器]。

1. 從清單中選取您的儲存體帳戶。

1. 選取以 **azureml-blobstore** 開頭的容器，並以 GUID 附加，然後按一下 [選取]。

1. 選取 [儲存]。 入口網站會在儲存完成時通知您。

> [!Note]
> 我們並未針對此教學課程開啟上傳通知，但請參閱[接收檔案上傳通知](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification)，以了解如何處理檔案上傳通知的詳細說明。

## <a name="build-publish-and-deploy-modules"></a>建置、發佈和部署模組

既然我們已進行設定變更，我們已準備好建置映像，並將其發佈到我們的 Azure 容器登錄。 建置流程會使用 deployment.template.json 檔案來判斷需要建置哪些模組。 每個模組的設定 (包括版本) 均可在模組資料夾的 module.json 檔案中找到。 建置流程會先在符合可於 module.json 檔案中找到之目前設定的 Dockerfile 上執行 Docker 組建來建立映像。 接著，它會使用與 module.json 檔案中相符的版本標記，從 module.json 檔案將映像發佈到登錄。 最後，會產生設定特有的部署資訊清單 (例如 deployment.amd64.json)，而我們會將其部署到 IoT Edge 裝置。 IoT Edge 裝置會從部署資訊清單讀取資訊，並根據指示來下載模組、設定路由，以及設定任何所需的屬性。 這種部署方法會產生兩個您應注意副作用：

* **部署延隔時間：** 由於 IoT Edge 執行階段必須先辨識對其所需屬性所做的變更，然後才能開始重新設定，因此，它在您部署模組之後可能需要一段時間，直到執行階段挑選它們並開始更新 IoT Edge 裝置為止。

* **模組版本事項：** 如果您使用與上一個模組相同的版本來將新版本的模組容器發佈至您的容器登錄，則執行階段將不會下載新版本的模組。 它會將本機映像的版本標記和來自部署資訊清單的所需映像進行比較。 如果那些版本相符，執行階段就不會採取任何動作。 因此，每當您希望部署新變更時，務必要遞增模組的版本。 針對您想要變更的模組，在 module.json 檔案中，變更 **標記** 屬性下方的 **版本** 屬性，藉以遞增版本。 接著，建置和發佈模組。

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>建置和發佈

1. 在您的開發 VM 上，如果 Docker 不在執行中，請加以啟動。

1. 在 Visual Studio Code 中，使用命令提示字元來啟動新的終端機，並登入您的 Azure Container Registry (ACR)。

  您可以在 Azure 入口網站中找到必要的使用者名稱、密碼和登入伺服器值。 容器登錄名稱的格式為 "turbofandemo\<unique id\>"。 從左窗格功能表的 [設定] 下，選取 [存取金鑰] 以進行查看。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. 在 Visual Studio Code 中，以滑鼠右鍵按一下 deployment.template.json，然後選擇 [建置並推送 IoT Edge 解決方案]。

### <a name="view-modules-in-the-registry"></a>在登錄中檢視模組

成功完成組建之後，我們將能夠使用 Azure 入口網站來檢閱已發佈的模組。

1. 開啟本教學課程的 Azure Container Registry。 容器登錄名稱的格式為 "turbofandemo\<unique id\>"。 

1. 從左窗格功能表的 [服務] 底下，選取 [存放庫]。

1. 請注意，您建立的這兩個模組 (**avrofilewriter** 和 **turbofanrouter**) 會顯示為存放庫。

1. 選取 [turbofanrouter]，並注意您已發佈一個標記為 0.0.1-amd64 的映像。

   ![檢視第一個標記的 turbofanrouter 版本](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>將模組部署到 IoT Edge 裝置

我們已在解決方案中建立並設定模組，現在要將模組部署到 IoT Edge 裝置。

1. 在 Visual Studio Code 中，以滑鼠右鍵按一下 config 資料夾中的 **deployment.amd64.json** 檔案。

1. 選擇 [建立單一裝置的部署]。

1. 選擇您的 IoT Edge 裝置 **aaTurboFanEdgeDevice**。

1. 在 Visual Studio Code 總管中，重新整理 [Azure IoT 中樞裝置] 面板。 您應該會看到這三個新模組已部署但尚未執行。

1. 數分鐘後再重新整理，而您將會看到模組正在執行中。

   ![在 Visual Studio Code 中檢視執行中的模組](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> 模組可能需要數分鐘的時間，才能啟動並進入穩定的執行中狀態。 在這段時間，您可能看到模組會在嘗試建立與 IoT Edge 中樞模組的連線時啟動並停止。

## <a name="diagnosing-failures"></a>診斷失敗

在此節中，我們將分享一些技術來了解一或多個模組中出了什麼問題。 通常可以先從 Visual Studio Code 中的狀態發現失敗。

### <a name="identify-failed-modules"></a>識別失敗的模組

* **Visual Studio Code：** 查看 [Azure IoT 中樞裝置] 面板。 如果大部分的模組都處於執行中狀態，但其中有一個停止，則您需要進一步調查那個停止的模組。 如果所有模組都長時間處於停止狀態，則也可能表示失敗。

* **Azure 入口網站：** 藉由巡覽至您在入口網站中的 IoT 中樞，然後尋找裝置詳細資料頁面 (在 IoT Edge 下方，向下鑽研您的裝置)，您可能會發現模組已向 IoT 中樞回報錯誤，或者從未回報任何錯誤。

### <a name="diagnosing-from-the-device"></a>從裝置診斷

藉由登入 IoT Edge 裝置 (在我們的案例中為 Linux VM)，您可以獲得授權來存取有關模組狀態的大量資訊。 我們使用的主要機制是 Docker 命令，讓我們能夠檢查裝置上的容器和映像。

1. 登入您的 IoT Edge 裝置：

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. 列出所有執行中的容器。 我們預期會看到每個模組的容器，並含有對應至該模組的名稱。 此外，此命令也會針對包含版本的容器列出完全相同的映像，因此可與您的預期相符。 您也可以將命令列中的 “container” 取代為 “image”，以列出映像。

   ```bash
   sudo docker container ls
   ```

1. 取得容器的記錄。 此命令會輸出已在容器中寫入至 StdErr 和 StdOut 的所有內容。 此命令適用於已啟動但之後基於某些原因而終止的容器。 它還能用來了解 edgeAgent 或 edgeHub 容器發生了什麼事。

   ```bash
   sudo docker container logs <container id>
   ```

1. 檢查容器。 此命令會產生大量有關映像的資訊。 您可以根據要尋找的內容來篩選資料。 例如，如果您想要查看 avroFileWriter 上的繫結是否正確，您可以使用下列命令：

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. 連線到執行中的容器。 如果您想要在容器執行期間檢查該容器，則此命令很實用：

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>後續步驟

在此文章中，我們在 Visual Studio Code 中建立了 IoT Edge 解決方案，其中包含三個模組：分類器、路由器和檔案寫入器/上傳程式。 我們會設定路由，以允許模組在 Edge 裝置上互相通訊。 我們已修改 Edge 裝置的設定，並更新了 Dockerfile 來安裝相依性，以及將繫結裝載新增至模組的容器。 

接下來，我們更新了 IoT 中樞的設定，根據類型來路由傳送訊息，以及處理檔案上傳。 當一切就緒之後，我們已將模組部署到 IoT Edge 裝置，並確保模組已正確執行。

繼續閱讀下一篇文章，以開始傳送資料，並查看您運作中的解決方案。

> [!div class="nextstepaction"]
> [透過透明閘道傳送資料](tutorial-machine-learning-edge-07-send-data-to-hub.md)