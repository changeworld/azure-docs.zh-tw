---
title: 適用於 Azure Data Box Edge 的 C# IoT Edge 模組 | Microsoft Docs
description: 了解如何開發可在 Data Box Edge 上部署的 C# IoT Edge 模組。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942544"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>開發C# IoT Edge 模組以在 Data Box Edge 上移動檔案

本文將逐步引導您如何建立 IoT Edge 模組，以使用您的 Data Box Edge 裝置進行部署。 Azure Data Box Edge 是允許您處理資料並將它透過網路傳送到 Azure 的儲存體解決方案。

您可以搭配 Data Box Edge 使用 Azure IoT Edge 模組，在已將資料移至 Azure 時進行轉換。 本文所使用的模組會實作邏輯，在您的 Data Box Edge 裝置上，將檔案從本機共用複製到雲端共用。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立容器登錄來儲存和管理模組 (Docker 映像)。
> * 建立要在 Data Box Edge 裝置上部署的 IoT Edge 模組。


## <a name="about-the-iot-edge-module"></a>關於 IoT Edge 模組

您的 Data Box Edge 裝置可以部署和執行 IoT Edge 模組。 Edge 模組本質上是可執行特定工作的 Docker 容器，例如，從裝置內嵌訊息、轉換訊息或將訊息傳送到 IoT 中樞。 在本文中，您將在 Data Box Edge 裝置上建立模組，將檔案從本機共用複製到雲端共用。

1. 檔案會寫入到 Data Box Edge 裝置上的本機共用。
2. 檔案事件產生器會針對寫入到本機共用的每個檔案建立檔案事件。 檔案事件也會在檔案已修改時產生。 接著將檔案事件傳送到 IoT Edge 中樞 (位於 IoT Edge 執行階段)。
3. IoT Edge 自訂模組會處理檔案事件以建立檔案事件物件，此物件也包含該檔案的相對路徑。 此模組會使用相對的檔案路徑來產生絕對路徑，並將檔案從本機共用複製到雲端共用。 模組接著會從本機共用中刪除該檔案。

![Azure IoT Edge 模組在 Data Box Edge 上的運作方式](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

一旦檔案位於雲端共用之後，它就會自動上傳至您的 Azure 儲存體帳戶。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您擁有：

- 執行中的 Data Box Edge 裝置。

    - 裝置也會有相關聯的 IoT 中樞資源。
    - 裝置已設定 Edge 計算角色。
    如需詳細資訊，請移至為您的 Data Box Edge[設定計算](data-box-edge-deploy-configure-compute.md#configure-compute)。

- 下列開發資源：

    - [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
    - [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。
    - [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows)。 您可能必須建立帳戶，才能下載並安裝軟體。

## <a name="create-a-container-registry"></a>建立容器登錄庫

Azure Container Registry 是 Azure 中的私人 Docker 登錄，您可以在其中儲存並管理私人 Docker 容器映像。 雲端中所提供的兩個熱門 Docker 登錄服務為 Azure Container Registry 和 Docker Hub。 本文使用 Container Registry。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 選取 [建立資源] > [容器] > [Container Registry]。 按一下 [建立]。
3. 提供：

   1. Azure 內唯一的**登錄名稱**，其中包含 5 到 50 個英數字元。
   2. 選擇 [訂用帳戶]。
   3. 建立新的，或選擇現有的**資源群組**。
   4. 選取 [位置]。 我們建議此位置應該和與 Data Box Edge 資源相關聯的位置一樣。
   5. 將 [管理使用者] 切換為 [啟用]。
   6. 將 SKU 設定為 [基本]。

      ![建立容器登錄](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. 選取 [建立]。
5. 建立容器登錄之後，請加以瀏覽，並選取 [存取金鑰]。

    ![取得存取金鑰](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 您稍後會使用這些值來將 Docker 映像發佈到登錄，以及將登錄認證新增至 Azure IoT Edge 執行階段。


## <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案

下列步驟會根據 .NET Core 2.1 SDK 建立 IoT Edge 模組專案。 此專案會使用 Visual Studio Code 和 Azure IoT Edge 擴充功能。

### <a name="create-a-new-solution"></a>建立新解決方案

建立您可以使用自己的程式碼自訂的 C# 解決方案範本。

1. 在 Visual Studio Code 中，選取 [檢視] > [命令選擇區] 以開啟 VS Code 命令選擇區。
2. 請在命令選擇區中，輸入並執行命令 **Azure: Sign in**，然後依照指示登入您的 Azure 帳戶。 如果您已登入，則可以略過此步驟。
3. 在 [命令選擇區] 中，輸入並執行命令 **Azure IoT Edge: New IoT Edge solution**。 在命令選擇區中提供下列資訊，以建立解決方案：

    1. 選取要用來建立解決方案的資料夾。
    2. 為解決方案提供名稱，或是接受預設值 **EdgeSolution**。
    
        ![建立新的解決方案 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. 選擇 [C# 模組] 作為模組範本。
    4. 將預設模組名稱取代為您想要指派的名稱，在此案例中為 **FileCopyModule**。
    
        ![建立新的解決方案 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. 將您在上一節所建立的容器登錄指定為第一個模組的映像存放庫。 將 **localhost:5000** 取代為您所複製的登入伺服器值。

        最終字串看起來像 `<Login server name>/<Module name>`。 在此範例中，該字串為 `mycontreg2.azurecr.io/filecopymodule`。

        ![建立新的解決方案 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. 移至 [檔案] > [開啟資料夾]。

    ![建立新的解決方案 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. 瀏覽並指向您稍早建立的 **EdgeSolution** 資料夾。 VS Code 視窗會載入您的 IoT Edge 解決方案工作區，以及它的五個最上層元件。 您將不會在本文中編輯 **.vscode** 資料夾、 **.gitignore** 檔案、 **.env** 檔案及 **deployment.template.json**。
    
    您修改的唯一元件是 modules 資料夾。 此資料夾含有適用於您模組的 C# 和 Docker 檔案，可用來建置您的模組作為容器映像。

    ![建立新的解決方案 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

1. 在 [VS Code explorer] 中，開啟 [**模組 > FileCopyModule > Program.cs**]。
2. 在 **FileCopyModule 命名空間**頂端，為稍後用到的類型新增下列 using 陳述式。 **Microsoft.Azure.Devices.Client.Transport.Mqtt** 是將訊息傳送至 IoT Edge 中樞的通訊協定。

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. 將 **InputFolderPath** 和 **OutputFolderPath** 變數新增至 Program 類別。

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. 在上一個步驟之後，請新增**FileEvent**類別來定義訊息本文。

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. 在**Init 方法**中，程式碼會建立並設定**ModuleClient**物件。 此物件允許模組使用 MQTT 通訊協定連線至本機的 Azure IoT Edge 執行階段，來傳送和接收訊息。 Init 方法中使用的連接字串，會由 IoT Edge 執行階段提供給模組。 程式碼會註冊 FileCopy 回呼，透過 **input1** 端點接收來自 IoT Edge 中樞的訊息。 以下列程式碼取代**Init 方法**。

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. 移除**PipeMessage 方法**的程式碼，並在其位置插入**FileCopy**的程式碼。

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. 儲存這個檔案。
8. 您也可以下載此專案的[現有程式碼範例](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable)。 接著，您可以針對此範例中的**program.cs**檔案，驗證您所儲存的檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 FileCopyModule，以將檔案從本機共用複製到雲端共用。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。

1. 在 VSCode 中，移至 終端機 > 新的終端機 以開啟新的 Visual Studio Code 整合式終端機。
2. 在整合式終端機中輸入下列命令，以登入 Docker。

    `docker login <ACR login server> -u <ACR username>`

    使用您從容器登錄中複製而來的登入伺服器和使用者名稱。

    ![建置並推送 IoT Edge 解決方案](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. 當系統提示您輸入密碼時，請提供密碼。 您也可以在 Azure 入口網站的容器登錄中，從 [存取金鑰] 擷取適用於登入伺服器、使用者名稱和密碼的值。
 
3. 提供認證之後，您就能將模組映像推送到您的 Azure 容器登錄。 在 VS Code 總管中，以滑鼠右鍵按一下 **module.json** 檔案，然後選取 [建置並推送 IoT Edge 解決方案]。

    ![建置並推送 IoT Edge 解決方案](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    當您告知 Visual Studio Code 建置您的解決方案時，它會在整合式終端機中執行兩個命令：docker build 和 docker push。 這兩個命令會建置程式碼、將 CSharpModule.dll 容器化，再將程式碼推送至您在初始化解決方案時所指定的容器登錄中。

    系統會提示您選擇模組平台。 選取對應至 Linux 的 [amd64]。

    ![選取平台](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > 僅支援 Linux 模組。

    您可能會看到下列可略過的警告：

    *Program .cs （77，44）： warning CS1998：這個非同步方法缺少 ' await ' 運算子，並會同步執行。請考慮使用 ' await ' 運算子來等候非封鎖式 API 呼叫，或 ' await Task. Run （...） ' 以在背景執行緒上執行 CPU 系結工作。*

4. 您可以在 VS Code 整合式終端機中檢視完整容器映像位址。 系統會根據 module.json 檔案中的資訊，使用 `<repository>:<version>-<platform>` 格式來建置映像位址。 在本文中，它應該看起來像 `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`。

## <a name="next-steps"></a>後續步驟

若要在 Data Box Edge 上部署並執行此模組，請參閱[新增模組](data-box-edge-deploy-configure-compute.md#add-a-module)中的步驟。
