---
title: 教學課程 - 使用 Azure IoT Edge 將自訂視覺分類器部署至裝置
description: 在本教學課程中，了解如何使用自訂視覺和 IoT Edge，讓電腦視覺模型以容器的形式執行。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4854e61b646c0ca2a2070d676e3efc5cfbac6b9b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044576"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>教學課程：使用自訂視覺服務在邊緣執行影像分類

Azure IoT Edge 可藉由將工作負載從雲端移至邊緣，來提升 IoT 解決方案的效率。 這項功能非常適合用於會處理大量資料的服務，例如電腦視覺模型。 [自訂視覺服務](../cognitive-services/custom-vision-service/overview.md)可讓您建置自訂影像分類器，並部署至裝置作為容器。 一起使用這兩項服務，您就可以從影像或影片串流中找到深入解析，而不必先將所有資料傳出網站。 自訂視覺所提供的分類器會比較影像與經過訓練的模型，以產生深入解析。

例如，IoT Edge 裝置上的自訂視覺無法判斷高速公路的車流量較平常高或低，也無法判斷停車場的某一排是否有停車位。 這些深入解析可與其他服務共用以便採取動作。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用電腦視覺建置影像分類器。
> * 開發 IoT Edge 模組來查詢裝置上的自訂視覺 Web 伺服器。
> * 將影像分類器的結果傳送至 IoT 中樞。

<center>

![圖表 - 教學課程架構、階段和部署分類器](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

>[!TIP]
>此教學課程是 [Raspberry Pi 3 上的自訂視覺和 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) \(英文\) 範例專案的簡化版本。 此教學課程是設計成在雲端 VM 上執行，並使用靜態影像來定型和測試影像分類器，這對於剛開始評估 IoT Edge 社的自訂視覺的人非常有用。 範例專案使用實體硬體並設定即時相機摘要來定型和測試影像分類器，這對於想要嘗試更詳細的真實案例的人非常有用。

在開始此教學課程之前，您應該已經完成先前的教學課程，以針對 Linux 容器開發設定您的環境：[開發適用於 Linux 裝置的 IoT Edge 模組](tutorial-develop-for-linux.md)。 完成該教學課程之後，您應該會具備下列必要條件：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 Linux 裝置](quickstart-linux.md)
* 容器登錄，像是 [Azure Container Registry](../container-registry/index.yml)。
* 已設定 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* [Docker CE](https://docs.docker.com/install/) 設定為執行 Linux 容器。

若要以自訂視覺服務開發 IoT Edge 模組，請在您的開發機器上安裝下列其他必要條件：

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [適用於 Visual Studio Code 的 Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-python.python) \(英文\)

## <a name="build-an-image-classifier-with-custom-vision"></a>使用電腦視覺建置影像分類器

若要建置影像分類器，您必須建立自訂視覺專案，並提供訓練影像。 如需您在本節所採取步驟的詳細資訊，請參閱[如何使用自訂視覺建置分類器](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md)。

完成影像分類器的建置和訓練後，您可以將它匯出為 Docker 容器，並部署到 IoT Edge 裝置。

### <a name="create-a-new-project"></a>建立新專案

1. 在網頁瀏覽器中，瀏覽到[自訂視覺網頁](https://customvision.ai/) \(英文\)。

2. 選取 [登入]  ，然後使用存取 Azure 資源時所用的同一個帳戶來登入。

3. 選取 [新增專案]  。

4. 使用下列值建立專案：

   | 欄位 | 值 |
   | ----- | ----- |
   | 名稱 | 提供專案名稱，例如 **EdgeTreeClassifier**。 |
   | 描述 | 選擇性的專案描述。 |
   | 資源 | 選取包含自訂視覺服務資源的其中一個 Azure 資源群組，或如果您尚未新增，請**建立新項目**。 |
   | 專案類型 | **分類** |
   | 分類類型 | **多類別 (每個影像一個標記)** |
   | 網域 | **一般 (精簡)** |
   | 匯出功能 | **基本平台 (Tensorflow、CoreML、ONNX...)** |

5. 選取 [建立專案]  。

### <a name="upload-images-and-train-your-classifier"></a>上傳影像並訓練分類器

建立影像分類器需要用到一組訓練影像以及測試影像。

1. 從 [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) 存放庫將影像範例複製或下載到本機開發機器。

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. 返回自訂視覺專案，並選取 [新增影像]  。

3. 瀏覽至您複製到本機的 git 存放庫，然後瀏覽至第一個影像資料夾 **Cognitive-CustomVision-Windows / Samples / Images / Hemlock**。 將資料夾中的 10 個影像全都選取，然後 [開啟]  。

4. 對這一組影像新增**鐵杉**標記，然後按 **ENTER 鍵**來套用標記。

5. 選取 [上傳 10 個檔案]  。

   ![將 hemlock 標記檔案上傳到自訂視覺](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. 當影像上傳成功時，選取 [完成]  。

7. 再次選取 [新增影像]  。

8. 瀏覽至第二個影像資料夾 **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**。 將資料夾中的 10 個影像全都選取，然後 [開啟]  。

9. 對這一組影像新增**日本櫻**標記，然後按 **ENTER 鍵**來套用標記。

10. 選取 [上傳 10 個檔案]  。 當影像上傳成功時，選取 [完成]  。

11. 當這兩組影像都加上標記並上傳時，選取 [訓練]  來訓練分類器。

### <a name="export-your-classifier"></a>匯出分類器

1. 分類器完成訓練後，選取分類器 [效能] 頁面上的 [匯出]  。

   ![匯出定型的影像分類器](./media/tutorial-deploy-custom-vision/export.png)

2. 選取 [DockerFile]  平台。 

3. 選取 [Linux]  版本。  

4. 選取 [匯出]  。 

   ![匯出為具有 Linux 容器的 DockerFile](./media/tutorial-deploy-custom-vision/export-2.png)

5. 匯出完成時，選取 [下載]  並將 .zip 套件儲存在電腦本機上。 從套件解壓縮所有檔案。 您會使用這些檔案來建立含有影像分類伺服器的 IoT Edge 模組。 

當您進行到這邊時，便已完成自訂視覺專案的建立和訓練程序。 您會在下一節使用匯出的檔案，但 [自訂視覺] 網頁已結束。 

## <a name="create-an-iot-edge-solution"></a>建立 IoT Edge 解決方案

現在，開發機器上已有影像分類器容器版本的檔案。 在這一節，您會設定影像分類器容器，使其以 IoT Edge 模組的形式來執行。 您也會建立第二個模組，以便與影像分類器一起部署。 第二個模組會在分類器張貼要求，並以訊息的形式將結果傳送到 IoT 中樞。 

### <a name="create-a-new-solution"></a>建立新解決方案

解決方案可讓您以邏輯方式來開發及組織單一 IoT Edge 部署的多個模組。 解決方案會包含一或多個模組的程式碼，以及用來宣告要如何在 IoT Edge 裝置上設定這些模組的部署資訊清單。 

1. 在 Visual Studio Code 中，選取 [檢視]   > [命令選擇區]  ，以開啟 VS Code 命令選擇區。 

1. 在命令選擇區中，輸入並執行命令 Azure IoT Edge：**新增 IoT Edge 解決方案**。 在命令選擇區中提供下列資訊，以建立解決方案： 

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱 (例如 **CustomVisionSolution**)，或接受預設值。 |
   | 選取模組範本 | 選擇 [Python 模組]  。 |
   | 提供模組名稱 | 將模組命名為 **classifier**。<br><br>此模組名稱必須是小寫。 IoT Edge 在參考模組時會區分大小寫，而且此解決方案所使用的程式庫會將所有要求的格式設為小寫。 |
   | 提供模組的 Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會預先填入上一個步驟的容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的**登入伺服器**值。 您可以在 Azure 入口網站中，從容器登錄的概觀頁面擷取登入伺服器。<br><br>最終字串看起來會類似於： **\<registry name\>.azurecr.io/classifier**。 |
 
   ![提供 Docker 映像存放庫](./media/tutorial-deploy-custom-vision/repository.png)

Visual Studio Code 視窗會載入 IoT Edge 解決方案工作區。

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器登錄的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。

IoT Edge 擴充功能會嘗試從 Azure 提取您的容器登錄認證，並將這些認證填入到環境檔案中。 請查看您的認證是否已包含其中。 如果沒有，請立即新增：

1. 在 VS Code 總管中，開啟 .env 檔案。
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱]  和 [密碼]  值來更新欄位。
3. 儲存這個檔案。

### <a name="select-your-target-architecture"></a>選取您的目標架構

目前，Visual Studio Code 可以開發適用於 Linux AMD64 和 Linux ARM32v7 裝置的模組。 您必須為每個解決方案都選取要作為目標的架構，因為容器是針對每個架構類型，以不同方式建置和執行。 預設值為 Linux AMD64，這是我們將在此教學課程中使用的架構。 

1. 開啟命令選擇區並搜尋 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或選取視窗底部側邊欄的捷徑圖示。 

2. 在命令選擇區中，從選項清單中選取目標架構。 針對此教學課程，我們是使用 Ubuntu 虛擬機器作為 IoT Edge 裝置，因此會保留預設 **amd64**。 

### <a name="add-your-image-classifier"></a>新增影像分類器

Visual Studio Code 中的 Python 模組範本包含一些程式碼範例，可供您執行來測試 IoT Edge。 在此案例中，您不會使用該程式碼。 相反地，請使用本節中的步驟，將程式碼範例取代為您先前匯出的影像分類器容器。 

1. 在檔案總管中，瀏覽至所下載並解壓縮的自訂視覺套件。 從解壓縮的套件中複製所有內容。 其中應該有兩個資料夾 **app** 和 **azureml**，以及兩個檔案 **Dockerfile** 和 **README**。 

2. 在檔案總管中，瀏覽至您對 Visual Studio Code 指示作為 IoT Edge 解決方案建立位置的目錄。 

3. 開啟 classifier 模組資料夾。 如果您在上一節使用了建議名稱，則資料夾結構的樣貌會是 **CustomVisionSolution / modules / classifier**。 

4. 將其中的檔案貼到 **classifier** 資料夾。 

5. 返回 Visual Studio Code 視窗。 解決方案工作區現在應該會在模組資料夾中顯示影像分類器檔案。 

   ![解決方案工作區與影像分類器檔案](./media/tutorial-deploy-custom-vision/workspace.png)

6. 開啟 classifier 資料夾中的 **module.json** 檔案。 

7. 更新 **platforms** 參數以指向您新增的新 Dockerfile，並移除 AMD64 以外的所有選項，這是我們在此教學課程中唯一使用的架構。 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. 儲存您的變更。 

### <a name="create-a-simulated-camera-module"></a>建立模擬觀景窗模組

在實際的自訂視覺部署中，您會有可提供即時影像或影片串流的觀景窗。 在此案例中，您會藉由建置模組來將測試影像傳送給影像分類器，以模擬觀景窗。 

#### <a name="add-and-configure-a-new-module"></a>新增並設定新的模組

在本節中，您會在相同的 CustomVisionSolution 中新增模組，並提供程式碼以建立模擬觀景窗。 

1. 在同一個 Visual Studio Code 視窗中，使用命令選擇區來執行 [Azure IoT Edge:新增 IoT Edge 模組] 命令來新增更多項目。 在命令選擇區中，為新模組提供下列資訊： 

   | Prompt | 值 | 
   | ------ | ----- |
   | 選取部署範本檔案 | 選取 CustomVisionSolution 資料夾中的 **deployment.template.json** 檔案。 |
   | 選取模組範本 | 選取 [Python 模組] |
   | 提供模組名稱 | 將模組命名為 **cameraCapture** |
   | 提供模組的 Docker 映像存放庫 | 將 **localhost:5000** 取代為 Azure Container Registry 的**登入伺服器**值。<br><br>最終字串看起來會類似於： **\<registryname\>.azurecr.io/cameracapture**。 |

   VS Code 視窗會在解決方案工作區中載入新模組，並更新 deployment.template.json 檔案。 現在您應該會看到兩個模組資料夾：classifier 和 cameraCapture。 

2. 開啟 **modules** / **cameraCapture** 資料夾中的 **main.py** 檔案。 

3. 將整個檔案取代為下列程式碼。 此程式碼範例會將 POST 要求傳送至 classifier 模組中所執行的影像處理服務。 我們會在此模組容器中提供影像範例以供要求使用。 然後，它會將回應封裝為 IoT 中樞訊息並傳送至輸出佇列。  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. 儲存 **main.py** 檔案。 

5. 開啟 **requirements.txt** 檔案。 

6. 為程式庫新增一行以便納入容器中。

   ```Text
   requests
   ```

7. 儲存 **requirements.txt** 檔案。


#### <a name="add-a-test-image-to-the-container"></a>在容器中新增測試影像

我們將會使用單一測試影像，而不會使用實際的觀景窗來為此案例提供影像饋送。 您稍早在本教學課程中為了獲得訓練影像而下載的 GitHub 存放庫中會有測試影像。 

1. 瀏覽至測試影像，其位置是 **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**。 

2. 複製 **test_image.jpg** 

3. 瀏覽至 IoT Edge 解決方案目錄，並在 **modules** / **cameraCapture** 資料夾中貼上測試影像。 影像所在的資料夾，應該會和您在上一節所編輯的 main.py 檔案相同。 

4. 在 Visual Studio Code 中，開啟 cameraCapture 模組的 **Dockerfile.amd64** 檔案。

5. 在建立工作目錄 `WORKDIR /app` 的程式行後面，新增下列程式碼：

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. 儲存 Dockerfile。

### <a name="prepare-a-deployment-manifest"></a>準備部署資訊清單

到目前為止，您已在本教學課程中訓練了自訂視覺模型來分類樹木影像，並已將該模型封裝為 IoT Edge 模組。 然後，您建立了第二個模組，其可以查詢影像分類伺服器，並將其結果回報給 IoT 中樞。 現在，您已準備好建立部署資訊清單，以便指示 IoT Edge 裝置要如何一起啟動和執行這兩個模組。 

Visual Studio Code 的 IoT Edge 擴充功能會在每個 IoT Edge 解決方案中提供範本，以協助您建立部署資訊清單。 

1. 開啟解決方案資料夾中的 **deployment.template.json** 檔案。 

2. 尋找 **modules** 區段，此區段應該會包含三個模組：您所建立的兩個模組 (classifier 和 cameraCapture)，以及預設會包含的第三個模組 SimulatedTemperatureSensor。 

3. 刪除 **SimulatedTemperatureSensor** 模組及其所有參數。 包含此模組是為了提供資料範例供測試案例使用，但此部署則不會用到。 

4. 如果您未將影像分類模組命名為 **classifier**，則請立即檢查名稱，並確認其名稱中全為小寫字母。 cameraCapture 模組會呼叫 classifier 模組，後者所使用的要求程式庫會將所有要求的格式設為小寫，而且 IoT Edge 會區分大小寫。 

5. 使用下列 JSON 來更新 cameraCapture 模組的 **createOptions** 參數。 這項資訊會在 main.py 程序所擷取的模組容器中建立環境變數。 藉由將這項資訊納入部署資訊清單中，您即可變更影像或端點，而不必重建模組影像。 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    如果您未將自訂視覺模組命名為 *classifier*，請更新影像處理端點值以便相符。 

6. 在檔案底部，更新 $edgeHub 模組的 **routes** 參數。 請將預測結果從 cameraCapture 路由傳送到 IoT 中樞。

    ```json
        "routes": {
          "cameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    如果您未將第二個模組命名為 *cameraCapture*，請更新路由值以便相符。 

7. 儲存 **deployment.template.json** 檔案。

## <a name="build-and-push-your-iot-edge-solution"></a>建置和推送 IoT Edge 解決方案

兩個模組都建立好且部署資訊清單範本已完成設定後，您便可開始建置容器映像，並將其推送至容器登錄。

映像進入登錄後，便可將解決方案部署到 IoT Edge 裝置。 您可以透過 IoT 中樞在裝置上設定模組，但您也可以透過 Visual Studio Code 存取 IoT 中樞和裝置。 在本節中，您會設定對 IoT 中樞的存取，然後使用 VS Code 將解決方案部署至您的 IoT Edge 裝置。

首先，建置解決方案並推送至容器登錄。

1. 選取 [檢視]   > [終端機]  ，以開啟 VS Code 整合式終端機。

2. 在終端機中輸入下列命令來登入 Docker。 使用您 Azure 容器登錄中的使用者名稱、密碼和登入伺服器登入。 您可以在 Azure 入口網站中，從登錄的 [存取金鑰]  區段擷取這些值。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   您可能會收到安全性警告，建議您使用 `--password-stdin`。 雖然建議生產案例使用該最佳做法，但是不在本教學課程的討論範圍內。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 參考。

3. 在 VS Code 總管中，以滑鼠右鍵按一下 **deployment.template.json** 檔案，然後選取 [建置並推送 IoT Edge 解決方案]  。

   建置和推送命令會啟動三項作業。 首先，它會在解決方案中建立名為 **config** 的新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。

## <a name="deploy-modules-to-device"></a>將模組部署到裝置

使用 Visual Studio Code 總管和 Azure IoT Tools 擴充功能，將模組專案部署到您的 IoT Edge 裝置。 您已備妥您的案例所需的部署資訊清單，即 config 資料夾中的 **deployment.amd64.json** 檔案。 現在您只需選取要接收部署的裝置即可。

請確定您的 IoT Edge 裝置已啟動並執行。

1. 在 Visual Studio Code 總管中，展開 [Azure IoT 中樞] 區段下的 [裝置] 來查看您的 IoT 裝置清單。

2. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [建立單一裝置的部署]。

3. 選取 **config** 資料夾中的 **deployment.amd64.json** 檔案，然後按一下 [選取 Edge 部署資訊清單]。 請勿使用 deployment.template.json 檔案。

4. 請展開裝置下的**模組**，以查看已部署且執行中的模組清單。 按一下 [重新整理] 按鈕。 您應該會看到新的**分類器**和 **cameraCapture** 模組正在與 **$edgeAgent** 和 **$edgeHub** 一起執行。  

您也可以查看並確認所有模組均已在裝置本身上啟動並執行。 在 IoT Edge 裝置上執行下列命令，以查看模組的狀態。

   ```bash
   iotedge list
   ```

模組可能需要幾分鐘才會啟動。 IoT Edge 執行階段需要接收其新的部署資訊清單、從容器執行階段提取模組映像，然後啟動每個新的模組。

## <a name="view-classification-results"></a>檢視分類結果

有兩種方式可供您檢視模組的結果，一種是在裝置本身上於訊息產生並傳送時檢視，另一種是從 Visual Studio Code 在訊息抵達 IoT 中樞時檢視。 

從裝置上，檢視 cameraCapture 模組的記錄，即可查看所傳送的訊息以及 IoT 中樞已收到訊息的確認。 

   ```bash
   iotedge logs cameraCapture
   ```

從 Visual Studio Code 中，以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [開始監視內建事件端點]。 

> [!NOTE]
> 一開始，您可能會在 cameraCapture 模組的輸出中看到連線錯誤。 這是因為部署和啟動模組之間的延遲所致。
>
> cameraCapture 模組會自動重新嘗試連線，直到成功為止。 成功連線之後，您會看到預期的影像分類訊息，如下所述。

來自自訂視覺模組的結果 (從 cameraCapture 模組以訊息形式傳來)，會包含影像屬於鐵杉還是櫻花的機率。 由於影像是鐵杉，您應該看到機率為 1.0。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。 

否則，可以刪除您在本文中使用的本機設定和 Azure 資源，以避免產生費用。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已訓練自訂視覺模型，並將它以模組形式部署到 IoT Edge 裝置。 然後，您建置了模組，以便查詢影像分類伺服器，並將其結果回報給 IoT 中樞。 

如果您想要嘗試此案例含有即時觀景窗饋送的更深入版本，請參閱 [Raspberry Pi 3 上的自訂視覺和 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) GitHub 專案。 

請繼續進行後續教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [使用 SQL Server 資料庫在Edge 上儲存資料](tutorial-store-data-sql-server.md)