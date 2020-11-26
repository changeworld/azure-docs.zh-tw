---
title: 針對 Windows 開發 C 模組的教學課程 - Azure IoT Edge | Microsoft Docs
description: 本教學課程說明如何使用 C 程式碼建立 IoT Edge 模組，並將其部署到執行 IoT Edge 的 Windows 裝置
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d9cffcadcb95b6c8c61205d458610f402fa7286d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964587"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>教學課程：開發適用於 Windows 裝置的 C IoT Edge 模組

使用 Visual Studio 開發 C 程式碼，並將其部署到執行 Azure IoT Edge 的 Windows 裝置。

您可以使用 Azure IoT Edge 模組來部署程式碼，直接在 IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用 Visual Studio 建立以 C SDK 為基礎的 IoT Edge 模組。
> * 使用 Visual Studio 和 Docker 建立 Docker 映像，並將其發佈至您的登錄中。
> * 將模組部署到您的 IoT Edge 裝置。
> * 檢視產生的資料。

您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

本教學課程示範如何使用 **Visual Studio 2019** 以 **C** 開發模組，並將其部署至 **Windows 裝置**。 如果您要開發適用於 Linux 裝置的模組，請改為移至[開發適用於 Linux 裝置的 C IoT Edge 模組](tutorial-c-module.md)。

使用下表來了解開發 C 模組並將其部署到 Windows 裝置的選項：

| C | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![在 Visual Studio 中開發適用於 WinAMD64 的 C 模組](./media/tutorial-c-module/green-check.png) |

在開始本教學課程之前，您應該已經完成先前的教學課程，以針對 Windows 容器開發設定您的開發環境：[開發適用於 Windows 裝置的 IoT Edge 模組](tutorial-develop-for-windows.md)。 完成該教學課程之後，您必須具備下列必要條件：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 Windows 裝置](quickstart.md)。
* 容器登錄，像是 [Azure Container Registry](../container-registry/index.yml)。
* 已設定 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 擴充功能的 [Visual Studio 2019](/visualstudio/install/install-visual-studio)。
* 已設定要執行 Windows 容器的 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。
* 透過 vcpkg 安裝適用於 Windows x64 的 Azure IoT C SDK：

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> 如果您使用 Visual Studio 2017 (15.7 或更高版本)，請從 Visual Studio 市集下載並安裝適用於 VS 2017 的 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

## <a name="create-a-module-project"></a>建立模組專案

下列步驟會使用 Visual Studio 和 Azure IoT Edge Tools 擴充功能，建立以 C SDK 為基礎的 IoT Edge 模組專案。 建立專案範本後，新增程式碼，以便模組根據其報告的屬性篩選出訊息。

### <a name="create-a-new-project"></a>建立新專案

建立您可以使用自己的程式碼自訂的 C 解決方案範本。

1. 啟動 Visual Studio 2019，然後選取 [建立新專案]  。

2. 搜尋 [IoT Edge]  ，然後選擇 [Azure IoT Edge (Windows amd64)]  專案。 按 [下一步]  。

   ![建立新的 Azure IoT Edge 專案](./media/tutorial-c-module-windows/new-project.png)

3. 將專案和解決方案重新命名為 **CTutorialApp** 之類的描述性項目。 按一下 [建立]  以建立專案。

   ![設定新的 Azure IoT Edge 專案](./media/tutorial-c-module-windows/configure-project.png)

4. 使用下列值設定專案：

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取範本 | 選取 [C 模組]  。 |
   | 模組專案名稱 | 將模組命名為 **CModule**。 |
   | Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會從模組專案名稱值預先填入容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的 **登入伺服器** 值。 您可以在 Azure 入口網站中，從容器登錄的概觀頁面擷取登入伺服器。 <br><br> 最終的映像存放庫看起來類似於：\<registry name\>.azurecr.io/cmodule。 |

   ![針對目標裝置、模組類型和容器登錄設定您的專案](./media/tutorial-c-module-windows/add-application-and-module.png)

5. 選取 [新增]  以建立專案。

### <a name="add-your-registry-credentials"></a>新增登錄認證

部署資訊清單會與 IoT Edge 執行階段共用您的容器登錄認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。 使用您 Azure 容器登錄的 [存取金鑰]  區段中的認證。

1. 在 Visual Studio 方案總管中，開啟 **deployment.template.json** 檔案。

2. 在 $edgeAgent 所需的屬性中尋找 **registryCredentials** 屬性。 其中應該會從您在建立專案時提供的資訊，自動填入您的登錄位址。 使用者名稱和密碼欄位應該會包含變數名稱。 例如：

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

1. 在此案例中，來自感應器的資料會以 JSON 格式傳入。 若要篩選 JSON 格式的訊息，請匯入適用於 C 的 JSON 程式庫。本教學課程使用 Parson。

   1. 下載 [Parson GitHub 存放庫](https://github.com/kgabis/parson)。 將 **parson.c** 和 **parson.h** 檔案複製到 **CModule** 專案中。

   2. 在 Visual Studio 中，開啟 CModule 專案資料夾中的 **CMakeLists.txt** 檔案。 在檔案頂端匯入 Parson 檔案，作為名為 **my_parson** 的程式庫。

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. 在 CMakeLists.txt 檔案的 **target_link_libraries** 區段中，將 `my_parson` 新增至程式庫清單。

   4. 儲存 **CMakeLists.txt** 檔案。

   5. 開啟 **CModule** > **main.c**。 在 Include 陳述式清單底部新增包含 `parson.h` 的新陳述式，以支援 JSON：

      ```c
      #include "parson.h"
      ```

2. 在 **main.c** 檔案中，於 messagesReceivedByInput1Queue 變數旁邊新增名為 `temperatureThreshold` 的全域變數。 此變數會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。

    ```c
    static double temperatureThreshold = 25;
    ```

3. 在 main.c 中尋找 `CreateMessageInstance` 函式。 使用下列程式碼取代內部 if-else 陳述式，以新增幾行功能：

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   else 陳述式中的新程式碼行會將新屬性新增至訊息，以將此訊息標示為警示。 此程式碼會將所有訊息標示為警示，因為我們將新增只會在回報高溫時將訊息傳送至 IoT 中樞的功能。

4. 尋找 `InputQueue1Callback` 函式，並以下列程式碼取代整個函式。 此函式會實作實際的傳訊篩選條件。 收到訊息時，它會檢查報告的溫度是否超過閾值。 如果為是，則會透過其輸出佇列轉送訊息。 如果為否，則會忽略此訊息。

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. 新增 `moduleTwinCallback` 函式。 此方法會從模組對應項接收所需的屬性，並會更新 **temperatureThreshold** 變數以符合該屬性。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

6. 尋找 `SetupCallbacksForModule` 函式。 使用下列程式碼取代此函式，以新增 **else if** 陳述式來檢查是否已更新模組對應項。

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. 儲存 main.c 檔案。

8. 開啟 **deployment.template.json** 檔案。

9. 在部署資訊清單中新增 CModule 模組對應項。 在 `moduleContent` 區段底部，於 `$edgeHub` 模組對應項後面插入下列 JSON 內容：

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![將 CModule 對應項新增至部署範本](./media/tutorial-c-module-windows/module-twin.png)

10. 儲存 **deployment.template.json** 檔案。

## <a name="build-and-push-your-module"></a>建置和推送模組

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **CModule**，以篩選掉報告的機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。

### <a name="sign-in-to-docker"></a>登入 Docker

對您部署機器上的 Docker 提供容器登錄認證，使其可以推送要儲存在登錄中的容器映像。

1. 開啟 PowerShell 或命令提示字元。

2. 使用您在建立登錄之後所儲存的 Azure Container Registry 認證來登入 Docker。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   您可能會收到安全性警告，建議您使用 `--password-stdin`。 雖然建議生產案例使用該最佳做法，但是不在本教學課程的討論範圍內。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 參考。

### <a name="build-and-push"></a>建置與推送

開發機器現在已可存取容器登錄，且 IoT Edge 裝置也會存取。 您可以開始將專案程式碼轉換成容器映像。

1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下您想要建置的專案名稱。 預設名稱是 **AzureIotEdgeApp1**。 在本教學課程中，已選擇 **CTutorialApp** 名稱。 由於您要建置 Windows 模組，因此擴充功能應該為 **Windows.Amd64**。

2. 選取 [建置和推送 IoT Edge 模組]。

   建置和推送命令會啟動三項作業。 首先，它會在名為 **config** 的解決方案中建立新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。

## <a name="deploy-modules-to-device"></a>將模組部署到裝置

使用 Visual Studio Cloud Explorer 和 Azure IoT Edge Tools 擴充功能，將模組專案部署到您的 IoT Edge 裝置。 您已備妥您的案例所需的部署資訊清單，即 config 資料夾中的 **deployment.windows-amd64.json** 檔案。 現在您只需選取要接收部署的裝置即可。


請確定您的 IoT Edge 裝置已啟動並執行。

1. 在 Visual Studio Cloud Explorer 中，展開資源以查看您的 IoT 裝置清單。

2. 以滑鼠右鍵按一下您要接收部署的 IoT Edge 裝置名稱。

3. 選取 [建立部署]。

4. 在檔案總管中，選取您解決方案的 config 資料夾中的 **deployment.windows-amd64** 檔案。

5. 重新整理 Cloud Explorer，以查看列在您裝置之下的已部署模組。

## <a name="view-generated-data"></a>檢視產生的資料

在您將部署資訊清單套用至 IoT Edge 裝置後，裝置的 IoT Edge 執行階段即會收集新的部署資訊，並開始在裝置上執行。 裝置上任何執行中、但未包含在部署資訊清單中的模組都會停止。 裝置中遺漏的任何模組都會啟動。

您可以在訊息送達 IoT 中樞時，使用 IoT Edge Tools 擴充功能檢視訊息。

1. 在 Visual Studio Cloud Explorer 中，選取您的 IoT Edge 裝置名稱。

2. 在 [動作] 清單中，選取 [開始監視內建事件端點]。

3. 檢視送達 IoT 中樞的訊息。 訊息可能需要一段時間才能送達，因為 IoT Edge 裝置必須接收其新的部署和啟動所有模組。 然後，我們對 CModule 程式碼所做的變更會等到機器溫度達到 25 度時才會傳送訊息。 它也會將 [警示] 訊息類型新增至任何觸達該溫度閾值的訊息。

   ![檢視送達 IoT 中樞的訊息](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>編輯模組對應項

我們已使用 CModule 模組對應項來設定 25 度的溫度閾值。 您可以使用模組對應項來變更此功能，而不必更新模組程式碼。

1. 在 Visual Studio 中，開啟 **deployment.windows-amd64.json** 檔案。 (並非 deployment.template 檔案。 如果您在方案總管中沒看到 config 檔案中的部署資訊清單，請選取總管工具列中的 [顯示所有檔案] 圖示。)

2. 尋找 CModule 對應項，然後將 **temperatureThreshold** 參數的值變更為高於最近報告溫度 5 到 10 度的新溫度。

3. 儲存 **deployment.windows-amd64.json** 檔案。

4. 請再次依照部署步驟，將更新後的部署資訊清單套用到您的裝置。

5. 監視傳入的裝置到雲端訊息。 在新的溫度閾值送達前，您應該會看到訊息停止。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，可以刪除您在本文中使用的本機設定和 Azure 資源，以避免產生費用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。

您可以繼續進行後續教學課程，以了解 Azure IoT Edge 如何協助您部署 Azure 雲端服務，以在邊緣處理和分析資料。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [串流分析](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [自訂視覺服務](tutorial-deploy-custom-vision.md)