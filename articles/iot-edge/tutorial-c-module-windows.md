---
title: 教學課程 - 使用 Azure IoT Edge 開發 Windows 的 C 模組
description: 本教學課程說明如何使用 C 程式碼建立 IoT Edge 模組，並將其部署至執行 IoT Edge 的 Windows 裝置。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1f346e1b737075fa79dc1146152125a6c5a3ec1a
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704671"
---
# <a name="tutorial-develop-c-iot-edge-modules-for-windows-devices"></a>教學課程：開發適用於 Windows 裝置的 C IoT Edge 模組

本文說明如何使用 Visual Studio 開發 C 程式碼，並將其部署至執行 Azure IoT Edge 的 Windows 裝置。

您可以使用 Azure IoT Edge 模組部署程式碼，以直接在 IoT Edge 裝置中實作您的商務邏輯。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用 Visual Studio 建立以 C SDK 為基礎的 IoT Edge 模組。
> * 使用 Visual Studio 和 Docker 建立 Docker 映像，並將其發佈至您的登錄中。
> * 將模組部署到您的 IoT Edge 裝置。
> * 檢視產生的資料。

您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您裝置所產生的溫度資料。 只有在溫度超過指定的閾值時，此模組才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

本教學課程示範如何使用 Visual Studio 2019 以 C 開發模組，並將其部署至 Windows 裝置。 如果您要開發適用於 Linux 裝置的模組，則應移至[開發適用於 Linux 裝置的 C IoT Edge 模組](tutorial-csharp-module.md)。

若要了解開發 C 模組並將其部署至 Windows 裝置的選項，請參閱下表：

| C | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;和&nbsp;2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![在 Visual Studio 中開發適用於 WinAMD64 的 C 模組](./media/tutorial-c-module/green-check.png) |

開始進行本教學課程之前，請依照[開發適用於 Windows 裝置的 IoT Edge 模組](tutorial-develop-for-windows.md)教學課程中的指示設定您的開發環境。 完成後，您的環境將包含下列必要條件：

* 在 Azure 中擁有免費或標準層的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 Windows 裝置](quickstart.md)。
* 容器登錄，例如 [Azure Container Registry](../container-registry/index.yml)。
* 已設定 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 擴充功能的 [Visual Studio 2019](/visualstudio/install/install-visual-studio)。
* 已設定要執行 Windows 容器的 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。

執行下列命令，以透過 vcpkg 安裝適用於 Windows x64 的 Azure IoT C SDK：

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> 如果您使用 Visual Studio 2017 (15.7 版或更新版本)，請從 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) 下載並安裝適用於 Visual Studio 2017 的 Azure IoT Edge Tools。

## <a name="create-a-module-project"></a>建立模組專案

在本節中，您會使用 Visual Studio 和 Azure IoT Edge Tools 擴充功能，建立以 C SDK 為基礎的 IoT Edge 模組專案。 建立專案範本後，您將新增程式碼，讓模組根據其報告的屬性篩除訊息。

### <a name="create-a-new-project"></a>建立新專案

建立您可以使用自己的程式碼自訂的 C 解決方案範本。

1. 開啟 Visual Studio 2019，然後選取 [建立新專案]。

1. 在 [建立新專案] 窗格中搜尋 **IoT Edge**，然後在結果清單中選取 [Azure IoT Edge (Windows amd64)] 專案。

   ![IoT Edge [建立新專案] 窗格的螢幕擷取畫面。](./media/tutorial-c-module-windows/new-project.png)

1. 選取 [下一步]  。

    [設定您的新專案] 窗格隨即開啟。

   ![[設定您的新專案] 窗格的螢幕擷取畫面。](./media/tutorial-c-module-windows/configure-project.png)

1. 在 [設定您的新專案] 窗格中，將專案和解決方案重新命名為更具描述性的名稱，例如 **CTutorialApp**。 

1. 選取 [Create] \(建立\)  以建立專案。

   [新增模組] 窗格隨即開啟。

   ![用以設定專案的 [新增模組] 窗格的螢幕擷取畫面。](./media/tutorial-c-module-windows/add-application-and-module.png)

1. 在 [設定您的新專案] 頁面上，執行下列動作：

   a. 在左窗格中，選取 [C 模組] 範本。  
   b. 在 [模組名稱] 方塊中，輸入 **CModule**。  
   c. 在 [存放庫 URL] 方塊中，將 **localhost:5000** 取代為 Azure container registry 中的 [登入伺服器] 值，格式如下：`<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > 映像存放庫包含容器登錄名稱和容器映像名稱。 您的容器映像會預先填入模組專案名稱值。  您可以在 Azure 入口網站中，從容器登錄的概觀頁面擷取登入伺服器。

1. 選取 [新增]  以建立專案。

### <a name="add-your-registry-credentials"></a>新增登錄認證

部署資訊清單會與 IoT Edge 執行階段共用您的容器登錄認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。 使用您 Azure 容器登錄的 [存取金鑰]  區段中的認證。

1. 在 Visual Studio 方案總管中，開啟 *deployment.template.json* 檔案。

1. 在 $edgeAgent 所需的屬性中尋找 **registryCredentials** 屬性。 屬性的登錄位址應自動填入您在建立專案時所提供的資訊。 使用者名稱和密碼欄位應該會包含變數名稱。 例如：

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. 開啟模組解決方案中的環境 (ENV) 檔案。 該檔案依預設會隱藏於方案總管中，因此您可能需要選取 [顯示所有檔案] 按鈕，才能加以顯示。 ENV 檔案應會包含您在 *deployment.template.json* 檔案中看到的相同使用者名稱和密碼變數。

1. 從您的 Azure Container Registry 新增 [使用者名稱]  和 [密碼]  值。

1. 將變更儲存至 ENV 檔案。

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

預設模組程式碼會透過輸入佇列接收訊息，並透過輸出佇列傳遞這些訊息。 我們將再新增一些程式碼，讓模組在邊緣處理訊息，然後再將其轉送至 IoT 中樞。 更新模組，以分析每則訊息中的溫度資料，且只有在溫度超過特定閾值時，才會將訊息傳送至 IoT 中樞。

1. 在此案例中，來自感應器的資料會以 JSON 格式傳入。 若要篩選 JSON 格式的訊息，請匯入適用於 C 的 JSON 程式庫。本教學課程使用 Parson。

   a. 下載 [Parson GitHub 存放庫](https://github.com/kgabis/parson)。  
   b. 將 *parson.c* 和 *parson.h* 檔案複製到 CModule 專案中。  
   c. 在 Visual Studio 中，開啟 CModule 專案資料夾中的 *CMakeLists.txt* 檔案。  
   d. 在檔案頂端匯入 Parson 檔案，作為名為 **my_parson** 的程式庫。

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. 在 *CMakeLists.txt* 檔案的 "target_link_libraries" 區段中，將 `my_parson` 新增至程式庫清單。  
   f. 儲存 *CMakeLists.txt* 檔案。  
   g. 選取 [CModule] > [main.c]。 在 Include 陳述式清單底部新增包含 `parson.h` 的新陳述式，以支援 JSON：

      ```c
      #include "parson.h"
      ```

1. 在 *main.c* 檔案中的 `messagesReceivedByInput1Queue` 變數旁，新增名為 `temperatureThreshold` 的全域變數。 此變數會設定必須在測量的溫度超過特定值時，才會將資料傳送至 IoT 中樞。

    ```c
    static double temperatureThreshold = 25;
    ```

1. 在 *main.c* 中尋找 `CreateMessageInstance` 函式。 將內部 *if-else* 陳述式取代為下列程式碼，以新增幾行功能：

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

   *else* 陳述式中的新程式碼行會將新屬性新增至訊息，以將此訊息標示為警示。 此程式碼會將所有訊息標示為警示，因為我們將新增只會在回報高溫時將訊息傳送至 IoT 中樞的功能。

1. 尋找 `InputQueue1Callback` 函式，並以下列程式碼取代整個函式。 此函式會實作實際的傳訊篩選條件。 收到訊息時，它會檢查報告的溫度是否超過閾值。 如果溫度超過閾值，函式就會透過其輸出佇列轉送訊息。 若未超過閾值，則函式會忽略訊息。

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

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. 新增 `moduleTwinCallback` 函式。 此方法會從模組對應項接收所需的屬性，並會更新 **temperatureThreshold** 變數以符合該屬性。 所有模組都具有本身的模組對應項，這可讓您直接從雲端設定在模組內執行的程式碼。

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

1. 尋找 `SetupCallbacksForModule` 函式。 將此函式取代為下列程式碼，以新增 *else-if* 陳述式來檢查模組對應項是否已更新。

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

1. 儲存 *main.c* 檔案。

1. 開啟 *deployment.template.json* 檔案。

1. 在部署資訊清單中新增 **CModule** 模組對應項。 在 `moduleContent` 區段底部，於 `$edgeHub` 模組對應項後面插入下列 JSON 內容：

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![顯示將模組對應項新增至部署範本的螢幕擷取畫面。](./media/tutorial-c-module-windows/module-twin.png)

1. 儲存 *deployment.template.json* 檔案。

## <a name="build-and-push-your-module"></a>建置和推送模組

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **CModule**，以篩選掉報告的機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。

### <a name="sign-in-to-docker"></a>登入 Docker

對您部署機器上的 Docker 提供容器登錄認證，使其可以推送要儲存在登錄中的容器映像。

1. 開啟 PowerShell 或命令提示字元視窗。

1. 使用您在建立登錄之後儲存的 Azure Container Registry 認證來登入 Docker。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   您可能會收到建議使用 `--password-stdin` 的安全性警告。 雖然我們建議您將此作為生產案例的最佳做法，但這不在本教學課程的討論範圍內。 如需詳細資訊，請參閱 [docker login 參考](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)。

### <a name="build-and-push"></a>建置與推送

開發機器現在已可存取容器登錄，且 IoT Edge 裝置也會存取。 您可以開始將專案程式碼轉換成容器映像。

1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下您想要建置的專案名稱。 預設名稱是 **AzureIotEdgeApp1**。 在本教學課程中，我們選擇 **CTutorialApp** 這個名稱，因為您要建置 Windows 模組，擴充功能應為 **Windows.Amd64**。

1. 選取 [建置和推送 IoT Edge 模組]。

   建置和推送命令會啟動三項作業：
   * 首先，會在方案中建立名為 *config* 的新資料夾，其中包含完整的部署資訊清單。 這是根據部署範本和其他解決方案檔案中的資訊建置的。 
   * 接著會執行 `docker build`，以根據目標架構的適當 Dockerfile 建置容器映像。 
   * 最後會執行 `docker push`，以將映像存放庫推送至您的容器登錄。

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。

## <a name="deploy-modules-to-the-device"></a>將模組部署至裝置

使用 Visual Studio Cloud Explorer 和 Azure IoT Edge Tools 擴充功能，將模組專案部署到您的 IoT Edge 裝置。 您已備妥您的案例所需的部署資訊清單，即 *config* 資料夾中的 *deployment.windows-amd64.json* 檔案。 現在您只需選取要接收部署的裝置即可。

請確定您的 IoT Edge 裝置已啟動並執行。

1. 在 Visual Studio Cloud Explorer 中，展開資源以檢視您的 IoT 裝置清單。

1. 以滑鼠右鍵按一下您要接收部署的 IoT Edge 裝置名稱。

1. 選取 [建立部署]。

1. 在 Visual Studio 檔案總管中，選取解決方案的 *config* 資料夾中的 *deployment.windows-amd64.json* 檔案。

1. 重新整理 Cloud Explorer，以檢視列在您裝置之下的已部署模組。

## <a name="view-generated-data"></a>檢視產生的資料

在您將部署資訊清單套用至 IoT Edge 裝置後，裝置的 IoT Edge 執行階段即會收集新的部署資訊，並開始在裝置上執行。 裝置上任何執行中、但未包含在部署資訊清單中的模組都會停止。 裝置中遺漏的任何模組都會啟動。

您可以在訊息送達 IoT 中樞時，使用 IoT Edge Tools 擴充功能檢視訊息。

1. 在 Visual Studio Cloud Explorer 中，選取您的 IoT Edge 裝置名稱。

1. 在 [動作] 清單中，選取 [開始監視內建事件端點]。

1. 檢視送達 IoT 中樞的訊息。 訊息可能需要一段時間才能送達，因為 IoT Edge 裝置必須接收其新的部署和啟動所有模組。 CModule 程式碼的變更必須等到機器溫度達到 25 度時，才可傳送訊息。 程式碼也會將 [警示] 訊息類型新增至任何觸達該溫度閾值的訊息。

   ![[輸出] 視窗的螢幕擷取畫面，顯示送達 IoT 中樞的訊息。](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>編輯模組對應項

您已使用 CModule 模組對應項設定了 25 度的溫度閾值。 您可以使用模組對應項來變更此功能，而不必更新模組程式碼。

1. 在 Visual Studio 中，開啟 *deployment.windows-amd64.json* 檔案。 

   *請勿* 開啟 *deployment.template* 檔案。 如果您在方案總管中未看到 *config* 檔案中的部署資訊清單，請選取總管工具列中的 [顯示所有檔案] 圖示。

1. 尋找 CModule 對應項，然後將 **temperatureThreshold** 參數的值變更為比最近報告的溫度高 5 到 10 度的新溫度。

1. 儲存 *deployment.windows-amd64.json* 檔案。

1. 請再次依照部署指示，將更新後的部署資訊清單套用到您的裝置。

1. 監視傳入的裝置到雲端訊息。 在觸達新的溫度閾值前，訊息應該會停止。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，可以保留並重複使用您在本教學課程中建立的資源和設定。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，為避免產生費用，您可以刪除在本文中使用的本機設定和 Azure 資源。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。

若要了解 Azure IoT Edge 如何協助您部署 Azure 雲端服務，以在邊緣處理和分析資料，請繼續進行後續教學課程。

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure 串流分析](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [自訂視覺服務](tutorial-deploy-custom-vision.md)
