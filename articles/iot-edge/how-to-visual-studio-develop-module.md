---
title: 在 Visual Studio Azure IoT Edge 中開發和偵錯工具模組
description: '使用 Visual Studio 搭配 Azure IoT Tools 來開發 C 或 c # IoT Edge 模組，並將它從您的 IoT 中樞推送至 IoT 裝置，如部署資訊清單所設定。'
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80384852"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>使用 Visual Studio 2019 來開發和偵測模組，以進行 Azure IoT Edge

您可以將商務邏輯轉換成 Azure IoT Edge 的模組。 本文說明如何使用 Visual Studio 2019 做為開發和偵錯工具模組的主要工具。

Azure IoT Edge Tools for Visual Studio 提供下列優點：

- 在本機開發電腦上建立、編輯、建置、執行 Azure IoT Edge 解決方案和模組並針對其進行偵錯。
- 透過 Azure IoT 中樞將您的 Azure IoT Edge 解決方案部署到 Azure IoT Edge 裝置。
- 以 C 或 c # 撰寫 Azure IoT 模組的程式碼，同時享有 Visual Studio 開發的所有優點。
- 使用 UI 管理 Azure IoT Edge 裝置與模組。

本文說明如何使用適用于 Visual Studio 2019 的 Azure IoT Edge 工具來開發您的 IoT Edge 模組。 您也將學習如何將專案部署到 Azure IoT Edge 裝置。 目前，Visual Studio 2019 提供以 C 和 c # 撰寫之模組的支援。 支援的裝置架構為 Windows X64 和 Linux X64 或 ARM32。 如需有關支援的作業系統、語言和架構的詳細資訊，請參閱[語言和架構支援](module-development.md#language-and-architecture-support)。
  
## <a name="prerequisites"></a>必要條件

此文章假設您使用執行 Windows 的電腦或虛擬機器作為開發電腦。 在 Windows 電腦上，您可以開發 Windows 或 Linux 模組。 若要開發 Windows 模組，請使用執行 1809/組建17763或更新版本的 Windows 電腦。 若要開發 Linux 模組，請使用符合[Docker Desktop 需求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的 Windows 電腦。

因為本文使用 Visual Studio 2019 作為主要開發工具，所以請安裝 Visual Studio。 請確定您在 Visual Studio 2019 安裝中包含**Azure 開發**和**使用 c + + 的桌面開發**工作負載。 您可以[修改 Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019)來新增所需的工作負載。

當您的 Visual Studio 2019 就緒之後，您也需要下列工具和元件：

- 從 Visual Studio marketplace 下載並安裝[Azure IoT Edge 工具](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)，以在 Visual Studio 2019 中建立 IoT Edge 專案。

> [!TIP]
> 如果您使用 Visual Studio 2017，請從 Visual Studio marketplace 下載並安裝適用于 VS 2017 的[Azure IoT Edge 工具](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

- 在開發電腦下載並安裝 [Docker Community Edition](https://docs.docker.com/install/)，以建置並執行您的模組映像。 您必須設定 Docker CE 在 Linux 容器模式或 Windows 容器模式中執行。

- 安裝 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)，以便設定本機開發環境以偵錯、執行和測試您的 IoT Edge 解決方案。 安裝[Python （2.7/3.6 +）和 Pip](https://www.python.org/) ，然後在您的終端機中執行下列命令來安裝**iotedgehubdev**套件。 確定您的 Azure IoT EdgeHub Dev Tool 版本大於 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- 複製存放庫，並安裝 Vcpkg 程式庫管理員，然後安裝適用于 Windows 的**azure-iot-c 封裝**。

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或 [Docker 中樞](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。

  > [!TIP]
  > 您可以使用本機 Docker 登錄作為原型並用於測試，而非使用雲端登錄。

- 若要在裝置上測試模組，您需要一個有效的 IoT 中樞，而且該中樞中至少必須有一個 IoT Edge 裝置。 若要使用您的電腦作為 IoT Edge 裝置，請遵循 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入門中的步驟。 如果您在開發電腦上執行 IoT Edge 精靈，您可能必須先停止 EdgeHub 與 EdgeAgent，才能開始在 Visual Studio 中進行開發。

### <a name="check-your-tools-version"></a>檢查您的工具版本

1. 從 [**擴充**功能] 功能表中，選取 [**管理延伸**模組]。 展開 [**已安裝的 > 工具**]，您可以找到 Visual Studio Visual Studio 和**Cloud Explorer 的** **Azure IoT Edge 工具**。

1. 記下已安裝的版本。 您可以將此版本與 Visual Studio Marketplace 上的最新版本進行比較 ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. 如果您的版本比 Visual Studio Marketplace 提供的版本舊，請依照下一節中的說明在 Visual Studio 中更新您的工具。

### <a name="update-your-tools"></a>更新您的工具

1. 在 [**管理擴充**功能] 視窗中，展開 [**更新] > Visual Studio Marketplace**，選取 [ **Azure IoT Edge 工具**] 或 [ **Cloud Explorer] Visual Studio**然後選取 [**更新**]。

1. 下載工具更新之後，請關閉 Visual Studio，以使用 VSIX 安裝程式觸發工具更新。

1. 在安裝程式中選取 [確定]**** 以啟動，然後選取 [修改]**** 以更新工具。

1. 更新完成後，請選取 [關閉]**** 並重新啟動 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>建立 Azure IoT Edge 專案

Visual Studio 中的 Azure IoT Edge 專案範本可建立可以在 Azure IoT 中樞中部署到 Azure IoT Edge 裝置的專案。 首先您要建立 Azure IoT Edge 解決方案，然後在該解決方案中產生第一個模組。 每個 IoT Edge 解決方案都可以包含多個模組。

> [!TIP]
> Visual Studio 所建立的 IoT Edge 專案結構與 Visual Studio Code 中的 IoT Edge 專案結構不同。

1. 在 Visual Studio 新增專案] 對話方塊中，搜尋並選取 [ **Azure IoT Edge**專案，然後按 **[下一步]**。 在 [專案設定] 視窗中，輸入專案的名稱並指定位置，然後選取 [**建立**]。 預設專案名稱是 **AzureIoTEdgeApp1**。

   ![建立新專案](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. 在 [**新增 IoT Edge 應用程式和模組**] 視窗中，選取 [ **c # 模組**] 或 [ **c 模組**]，然後指定您的模組名稱和模組映射存放庫。 Visual Studio 會自動以 **localhost:5000/<您的模組名稱\>** 填入模組名稱。 請使用您自己的登錄資訊加以取代。 如果您使用本機 Docker 登錄進行測試，則**localhost**是正常的。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 登入伺服器看起來像** _\<registry name\>_ . azurecr.io**。 僅取代字串的**localhost： 5000**部分，讓最終結果看起來像** \<*registry name*\> azurecr.io/ _\<your module name\>_ **。 預設的模組名稱是**IotEdgeModule1**

   ![新增應用程式和模組](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. 選取 **[確定]** 以使用 c # 或 c 模組來建立 Azure IoT Edge 解決方案。

現在您已有**AzureIoTEdgeApp1**或**AzureIoTEdgeApp1**專案，以及您的方案中的**IotEdgeModule1**專案。 每個**AzureIoTEdgeApp1**專案都有一個檔案 `deployment.template.json` ，該檔案會定義您想要為 IoT Edge 方案建立及部署的模組，也會定義模組之間的路由。 預設解決方案具有**SimulatedTemperatureSensor**模組和**IotEdgeModule1**模組。 **SimulatedTemperatureSensor**模組會將模擬的資料產生到**IotEdgeModule1**模組，而**IotEdgeModule1**模組中的預設程式碼會直接將收到的訊息傳送至 Azure IoT 中樞。

若要查看模擬的溫度感應器如何運作，請參閱[SimulatedTemperatureSensor 的原始碼](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)。

**IotEdgeModule1**專案是 .net Core 2.1 主控台應用程式（如果它是 c # 模組）。 它包含執行 Windows 容器或 Linux 容器之 IoT Edge 裝置所需的 Dockerfiles。 檔案會 `module.json` 描述模組的中繼資料。 實際的模組程式碼（採用 Azure IoT 裝置 SDK 作為相依性）可在或檔案中找到 `Program.cs` `main.c` 。

## <a name="develop-your-module"></a>開發您的模組

解決方案隨附的預設模組程式碼位於**IotEdgeModule1**  >  **Program.cs** （適用于 c #）或**main. c** （c）。 系統會設定模組和檔案， `deployment.template.json` 讓您可以建立解決方案、將其推送至容器登錄，然後將它部署至裝置，以開始測試，而不需要觸及任何程式碼。 建立模組是為了接受來源的輸入（在此案例中為模擬資料的**SimulatedTemperatureSensor**模組），並使用管線將它傳送至 Azure IoT 中樞。

當您準備好使用自己的程式碼自訂模組範本時，請使用[Azure IoT 中樞 sdk](../iot-hub/iot-hub-devguide-sdks.md)來建立模組，以滿足 IoT 解決方案的主要需求，例如安全性、裝置管理和可靠性。

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 裝置連接字串來初始化 iotedgehubdev

1. 從 Visual Studio Cloud Explorer 中的**主要連接字串**，複製任何 IoT Edge 裝置的連接字串。 慎勿複製非 Edge 裝置的連接字串，IoT Edge 裝置的圖示與非 Edge 裝置的圖示不同。

   ![複製 Edge 裝置連接字串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. 從 [**工具**] 功能表中，選取 [ **Azure IoT Edge 工具**] [  >  **安裝 IoT Edge**模擬器]，貼上連接字串，然後按一下 **[確定]**。

   ![開啟 [設定 Edge 連接字串] 視窗](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 輸入第一個步驟中的連接字串，然後選取 [確定]****。

> [!NOTE]
> 您只需要在開發電腦上進行這些步驟一次，因為結果會自動套用至全部後續的 Azure IoT Edge 解決方案。 如果您要變更為不同的連接字串，可以再次進行此程序。

## <a name="build-and-debug-single-module"></a>組建和 debug 單一模組

一般而言，您為想要先針對每個模組進行測試和偵錯，然後在使用多個模組的整個解決方案中執行。

1. 在**方案總管**中，以滑鼠右鍵按一下**IotEdgeModule1** ，然後從內容功能表中選取 [**設定為啟始專案**]。

   ![設定啟始專案](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按下 **F5** 或按一下下面的按鈕執行模組，第一次可能需要 10&ndash;20 秒。

   ![執行模組](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模組已成功初始化，您應該會看到 .NET Core 主控台應用程式已啟動。

   ![執行中的模組](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. 如果在 c # 中進行開發，請在 Program.cs 的函式中設定中斷點 `PipeMessage()` ; 如果使用 C，請**Program.cs**在 `InputQueue1Callback()` **main. C**的函式中設定中斷點。 然後您可以藉由在**Git Bash**或**WSL Bash** shell 中執行下列命令來傳送訊息，藉以進行測試。 (您無法從 PowerShell 或命令提示字元執行 `curl` 命令。)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![針對單一模組進行偵錯](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    中斷點應該已觸發。 您可以在 Visual Studio [區域變數]**** 視窗中觀察變數。

   > [!TIP]
   > 您也可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具來傳送訊息，而不使用 `curl`。

1. 按 **Ctrl + F5** 或按一下 [停止] 按鈕以停止偵錯。

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>建置使用多個模組的 IoT Edge 解決方案以及針對其進行偵錯

您完成開發單一模組之後，您會想要執行使用多個模組的整個解決方案以及針對其進行偵錯。

1. 在**方案總管**中，以滑鼠右鍵按一下 [ **AzureIoTEdgeApp1** ]，**然後選取 [**  >  **新增 IoT Edge 模組**]，將第二個模組新增至解決方案。 第二個模組的預設名稱是**IotEdgeModule2** ，而且會作為另一個管道模組。

1. 開啟檔案 `deployment.template.json` ，您會看到**IotEdgeModule2**已新增至 [**模組**] 區段。 以下列程式碼取代 **routes** 區段。 如果您已自訂模組名稱，請確定更新名稱以使其相符。

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. 以滑鼠右鍵按一下 **AzureIoTEdgeApp1**，然後選取快顯功能表中的 [設定為啟始專案]****。

1. 建立您的中斷點，然後按下 **F5** 同時執行多個模組並針對其進行偵錯。 您應該會看到多個 .NET Core 主控台應用程式視窗，每個視窗都代表不同的模組。

   ![針對多個模組進行偵錯](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按下 **Ctrl + F5** 或選取 [停止] 按鈕以停止偵錯。

## <a name="build-and-push-images"></a>建置及推送映像

1. 確定 **AzureIoTEdgeApp1** 是啟始專案。 選取 [偵錯]**** 或 [發行]**** 做為要對於模組映像建置的設定。

    > [!NOTE]
    > 選擇 [偵錯]**** 時，Visual Studio 將使用 `Dockerfile.(amd64|windows-amd64).debug` 來建置 Docker 映像。 這包含建置它時容器映像中的 .NET Core 命令列偵錯工具 VSDBG。 對於生產就緒 IoT Edge 模組，建議您使用 [發行]**** 設定，此設定會使用無 VSDBG 的 `Dockerfile.(amd64|windows-amd64)`。

1. 如果您使用 Azure Container Registry （ACR）之類的私用登錄，請使用下列 Docker 命令登入。  您可以在 Azure 入口網站中，從登錄的 [**存取金鑰**] 頁面取得使用者名稱和密碼。 如果您使用的是本機登錄，您可以[執行本機登錄](https://docs.docker.com/registry/deploying/#run-a-local-registry)。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. 如果您使用私人登錄，例如 Azure Container Registry，您需要將檔案登入資訊新增到在檔案 `deployment.template.json` 中找到的執行階段設定。 將您的實際 ACR 系統管理員使用者名稱、密碼和登錄名稱填入預留位置。

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. 在**方案總管**中，以滑鼠右鍵按一下**AzureIoTEdgeApp1** ，然後選取 [**組建] 和 [推送 IoT Edge 模組**]，以建立並推送每個模組的 Docker 映射。

## <a name="deploy-the-solution"></a>部署解決方案

在您用來設定 IoT Edge 裝置的快速入門文章中，您使用 Azure 入口網站部署了模組。 您也可以使用適用於 Visual Studio 的 Cloud Explorer 來部署模組。 您已備妥對於您的案例準備的部署資訊清單 (`deployment.json` 檔案)，您只需要選取要接收部署的裝置即可。

1. 依序按一下 [檢視]**** > [Cloud Explorer]**** 以開啟 **Cloud Explorer**。 請確定您已登入 Visual Studio 2019。

1. 在 **Cloud Explorer** 中，展開您的訂用帳戶，尋找您的 Azure IoT 中樞與您要部署的 Azure IoT Edge 裝置。

1. 在 IoT Edge 裝置上按一下滑鼠右鍵，以建立其部署。 流覽至您的平臺所設定的部署資訊清單（位於 Visual Studio 方案的**config**資料夾中），例如 `deployment.arm32v7.json` 。

1. 按一下 [重新整理] 按鈕，以查看與**SimulatedTemperatureSensor**模組一起執行的新模組，並 **$edgeAgent**和 **$edgeHub**。

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視特定 IoT Edge 裝置的 D2C 訊息，請在**Cloud Explorer**中的 iot 中樞內加以選取，然後按一下 [**動作**] 視窗中的 [**開始監視內建事件端點**]。

1. 若要停止監視資料，請選取 [**動作**] 視窗中的 [**停止監視內建事件端點**]。

## <a name="next-steps"></a>後續步驟

若要為您的 IoT Edge 裝置開發自訂模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
