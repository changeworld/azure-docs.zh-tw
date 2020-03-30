---
title: 在視覺化工作室 - Azure IoT 邊緣開發和調試模組
description: 將視覺化工作室與 Azure IoT 工具一起開發 C 或 C# IoT 邊緣模組，並將其從 IoT 中心推送到 IoT 設備（由部署清單配置）。
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384852"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>使用 Visual Studio 2019 開發和調試 Azure IoT 邊緣的模組

您可以將商務邏輯轉換成 Azure IoT Edge 的模組。 本文介紹如何使用 Visual Studio 2019 作為開發和調試模組的主要工具。

Azure IoT Edge Tools for Visual Studio 提供下列優點：

- 在本機開發電腦上建立、編輯、建置、執行 Azure IoT Edge 解決方案和模組並針對其進行偵錯。
- 透過 Azure IoT 中樞將您的 Azure IoT Edge 解決方案部署到 Azure IoT Edge 裝置。
- 在 C 或 C# 中對 Azure IoT 模組進行編碼，同時具有視覺化工作室開發的所有優勢。
- 使用 UI 管理 Azure IoT Edge 裝置與模組。

本文介紹如何使用 Visual Studio 2019 的 Azure IoT 邊緣工具來開發 IoT 邊緣模組。 您也將學習如何將專案部署到 Azure IoT Edge 裝置。 目前，Visual Studio 2019 為以 C 和 C++ 編寫的模組提供支援。 支援的設備體系結構是 Windows X64 和 Linux X64 或 ARM32。 有關支援的作業系統、語言和體系結構的詳細資訊，請參閱[語言和體系結構支援](module-development.md#language-and-architecture-support)。
  
## <a name="prerequisites"></a>Prerequisites

此文章假設您使用執行 Windows 的電腦或虛擬機器作為開發電腦。 在 Windows 電腦上，可以開發 Windows 或 Linux 模組。 要開發 Windows 模組，請使用運行版本 1809/生成 17763 或更新版本的 Windows 電腦。 要開發 Linux 模組，請使用滿足 Docker[桌面要求的](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)Windows 電腦。

由於本文使用 Visual Studio 2019 作為主要開發工具，因此請安裝 Visual Studio。 請確保在 Visual Studio 2019 安裝中包含具有C++工作負載的**Azure 開發和****桌面開發**。 您可以[修改 Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019)以添加所需的工作負載。

視覺工作室 2019 準備就緒後，您還需要以下工具和元件：

- 從視覺化工作室市場下載並安裝[Azure IoT 邊緣工具](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)，以在 Visual Studio 2019 中創建 IoT 邊緣專案。

> [!TIP]
> 如果您使用的是 Visual Studio 2017，請從視覺化工作室市場下載並安裝用於 VS 2017 的[Azure IoT 邊緣工具](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

- 在開發電腦下載並安裝 [Docker Community Edition](https://docs.docker.com/install/)，以建置並執行您的模組映像。 您必須設定 Docker CE 在 Linux 容器模式或 Windows 容器模式中執行。

- 安裝 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)，以便設定本機開發環境以偵錯、執行和測試您的 IoT Edge 解決方案。 安裝[Python （2.7/3.6+） 和 Pip，](https://www.python.org/)然後在終端中運行以下命令來安裝**iotedgehubdev**包。 確定您的 Azure IoT EdgeHub Dev Tool 版本大於 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- 克隆存儲庫並安裝 Vcpkg 庫管理器，然後為 Windows 安裝**Azure-iot-sdk-c 包**。

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

1. 從 **"擴展"** 功能表中，選擇 **"管理擴展**"。 展開**已安裝>工具**，您可以找到**用於視覺化工作室的 Azure IoT 邊緣工具**，**並為視覺化工作室找到雲資源管理器**。

1. 記下已安裝的版本。 您可以將此版本與 Visual Studio Marketplace 上的最新版本進行比較 ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. 如果您的版本比 Visual Studio Marketplace 提供的版本舊，請依照下一節中的說明在 Visual Studio 中更新您的工具。

### <a name="update-your-tools"></a>更新您的工具

1. 在 **"管理擴展"** 視窗中，展開 **"visual Studio 應用商店>更新**，為視覺化工作室選擇**Azure IoT 邊緣工具**或**雲資源管理器**，然後選擇 **"更新**"。

1. 下載工具更新之後，請關閉 Visual Studio，以使用 VSIX 安裝程式觸發工具更新。

1. 在安裝程式中選取 [確定]**** 以啟動，然後選取 [修改]**** 以更新工具。

1. 更新完成後，請選取 [關閉]**** 並重新啟動 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>建立 Azure IoT Edge 專案

Visual Studio 中的 Azure IoT Edge 專案範本可建立可以在 Azure IoT 中樞中部署到 Azure IoT Edge 裝置的專案。 首先創建 Azure IoT 邊緣解決方案，然後生成該解決方案中的第一個模組。 每個 IoT Edge 解決方案都可以包含多個模組。

> [!TIP]
> Visual Studio 所建立的 IoT Edge 專案結構與 Visual Studio Code 中的 IoT Edge 專案結構不同。

1. 在視覺化工作室新專案對話方塊中，搜索並選擇**Azure IoT 邊緣**專案，然後按一下"**下一步**"。 在專案配置視窗中，輸入專案的名稱並指定位置，然後選擇 **"創建**"。 預設專案名稱是 **AzureIoTEdgeApp1**。

   ![建立新專案](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. 在 **"添加 IoT 邊緣應用程式和模組"** 視窗中，選擇**C# 模組**或**C 模組**，然後指定模組名稱和模組映射存儲庫。 Visual Studio 會自動以 **localhost:5000/<您的模組名稱\>** 填入模組名稱。 請使用您自己的登錄資訊加以取代。 如果使用本地 Docker 註冊表進行測試，則**本地主機**正常。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 登錄伺服器看起來像**_\<註冊表名稱\>_.azurecr.io**。 僅替換字串的**localhost：5000**部分，以便最終結果看起來像**\<*註冊表名稱*\>.azurecr.io/_\<您的模組名稱\>_**。 預設模組名稱為**IotEdgeModule1**

   ![添加應用程式和模組](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. 選擇 **"確定"** 以創建使用 C# 或 C 模組的 Azure IoT 邊緣解決方案。

現在，您有一個**AzureIoTEdgeApp1.Linux.Amd64**專案或**AzureIoTEdgeApp1.Windows.Amd64**專案，以及解決方案中的**IotEdgeModule1**專案。 每個**AzureIoTEdgeApp1**專案`deployment.template.json`都有一個檔，該檔定義要為 IoT Edge 解決方案構建和部署的模組，並定義模組之間的路由。 預設解決方案具有**類比溫度感應器**模組和**IotEdgeModule1 模組**。 **類比溫度感應器**模組向**IotEdgeModule1 模組**生成類比資料，而**IotEdgeModule1 模組**中的預設代碼直接將接收到的消息管道發送到 Azure IoT 中心。

要查看類比溫度感應器的工作原理，請查看[類比溫度感應器.csproj 原始程式碼](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)。

**IotEdgeModule1**專案是 .NET Core 2.1 主控台應用程式（如果是 C# 模組）。 它包含執行 Windows 容器或 Linux 容器之 IoT Edge 裝置所需的 Dockerfiles。 該檔`module.json`描述模組的中繼資料。 實際模組代碼以 Azure IoT 設備 SDK 作為依賴項，在`Program.cs`或`main.c`檔中找到。

## <a name="develop-your-module"></a>開發您的模組

解決方案附帶的預設模組代碼位於**IotEdgeModule1** > **Program.cs（** 對於 C#）或**main.c** （C）。 模組和`deployment.template.json`檔被設置，以便您可以生成解決方案，將其推送到容器註冊表，並將其部署到設備以開始測試，而無需接觸任何代碼。 該模組旨在從源（在本例中為類比資料的**類比溫度感應器**模組）獲取輸入，並將其傳送到 Azure IoT 中心。

準備好使用自己的代碼自訂模組範本後，請使用[Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)構建滿足 IoT 解決方案關鍵需求（如安全性、裝置管理和可靠性）的模組。

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 裝置連接字串來初始化 iotedgehubdev

1. 從 Visual Studio Cloud Explorer 中的**主要連接字串**，複製任何 IoT Edge 裝置的連接字串。 慎勿複製非 Edge 裝置的連接字串，IoT Edge 裝置的圖示與非 Edge 裝置的圖示不同。

   ![複製 Edge 裝置連接字串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. 在 **"工具"** 功能表中，選擇**Azure IoT 邊緣工具** > **設置 IoT 邊緣模擬器**，粘貼連接字串並按一下 **"確定**"。

   ![開啟 [設定 Edge 連接字串] 視窗](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 輸入第一個步驟中的連接字串，然後選取 [確定]****。

> [!NOTE]
> 您只需要在開發電腦上進行這些步驟一次，因為結果會自動套用至全部後續的 Azure IoT Edge 解決方案。 如果您要變更為不同的連接字串，可以再次進行此程序。

## <a name="build-and-debug-single-module"></a>構建和調試單個模組

一般而言，您為想要先針對每個模組進行測試和偵錯，然後在使用多個模組的整個解決方案中執行。

1. 在**解決方案資源管理器**中，按右鍵**IotEdgeModule1，** 然後從內容功能表**中選擇"設置為啟動專案**"。

   ![設定啟始專案](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按下 **F5** 或按一下下面的按鈕執行模組，第一次可能需要 10&ndash;20 秒。

   ![執行模組](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模組已成功初始化，您應該會看到 .NET Core 主控台應用程式已啟動。

   ![執行中的模組](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. 如果在 C# 中開發，則在 函數`PipeMessage()`中設置**中斷點Program.cs**。如果使用 C，則在`InputQueue1Callback()`**main.c**中的函數中設置中斷點。 然後，您可以通過在**Git Bash**或**WSL Bash** shell 中運行以下命令來發送消息來測試它。 (您無法從 PowerShell 或命令提示字元執行 `curl` 命令。)

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

1. 在**解決方案資源管理器中**，通過按右鍵**AzureIoTEdgeApp1**並選擇 **"添加新** > **IoT 邊緣模組**"，將第二個模組添加到解決方案中。 第二個模組的預設名稱是**IotEdgeModule2，** 它將充當另一個管道模組。

1. 打開檔`deployment.template.json`，您將看到在**模組**部分中添加了**IotEdgeModule2。** 以下列程式碼取代 **routes** 區段。 如果您已自訂模組名稱，請確定更新名稱以使其相符。

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. 以滑鼠右鍵按一下 **AzureIoTEdgeApp1**，然後選取快顯功能表中的 [設定為啟始專案]****。

1. 建立您的中斷點，然後按下 **F5** 同時執行多個模組並針對其進行偵錯。 您應該看到多個 .NET Core 主控台應用視窗，每個視窗代表不同的模組。

   ![針對多個模組進行偵錯](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按下 **Ctrl + F5** 或選取 [停止] 按鈕以停止偵錯。

## <a name="build-and-push-images"></a>建置及推送映像

1. 確定 **AzureIoTEdgeApp1** 是啟始專案。 選取 [偵錯]**** 或 [發行]**** 做為要對於模組映像建置的設定。

    > [!NOTE]
    > 選擇 [偵錯]**** 時，Visual Studio 將使用 `Dockerfile.(amd64|windows-amd64).debug` 來建置 Docker 映像。 這包含建置它時容器映像中的 .NET Core 命令列偵錯工具 VSDBG。 對於生產就緒 IoT Edge 模組，建議您使用 [發行]**** 設定，此設定會使用無 VSDBG 的 `Dockerfile.(amd64|windows-amd64)`。

1. 如果使用 Azure 容器註冊表 （ACR） 這樣的專用註冊表，請使用以下 Docker 命令登錄到它。  可以從 Azure 門戶中的註冊表**的訪問金鑰**頁面獲取使用者名和密碼。 如果您使用的是本機登錄，您可以[執行本機登錄](https://docs.docker.com/registry/deploying/#run-a-local-registry)。

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

1. 在**解決方案資源管理器**中，按右鍵**AzureIoTEdgeApp1，** 然後選擇 **"生成和推送 IoT 邊緣模組**"以為每個模組生成和推送 Docker 映射。

## <a name="deploy-the-solution"></a>部署解決方案

在您用來設定 IoT Edge 裝置的快速入門文章中，您使用 Azure 入口網站部署了模組。 您也可以使用適用於 Visual Studio 的 Cloud Explorer 來部署模組。 您已備妥對於您的案例準備的部署資訊清單 (`deployment.json` 檔案)，您只需要選取要接收部署的裝置即可。

1. 依序按一下 [檢視]**** > [Cloud Explorer]**** 以開啟 **Cloud Explorer**。 請確保您已登錄到 Visual Studio 2019。

1. 在 **Cloud Explorer** 中，展開您的訂用帳戶，尋找您的 Azure IoT 中樞與您要部署的 Azure IoT Edge 裝置。

1. 按右鍵 IoT 邊緣設備可為其創建部署。 導航到為位於 Visual Studio 解決方案（如**config**`deployment.arm32v7.json`）配置資料夾中的平台佈建的平臺的部署清單。

1. 按一下刷新按鈕以查看與**類比溫度感應器**模組一起運行的新模組 **，並$edgeAgent**和 **$edgeHub**。

## <a name="view-generated-data"></a>檢視產生的資料

1. 要監視特定 IoT 邊緣設備的 D2C 消息，請在**雲資源管理器**中的 IoT 中心中選擇它，然後在 **"操作"** 視窗中按一下 **"開始監視內置事件終結點**"。

1. 要停止監視資料，請在 **"操作"** 視窗中選擇 **"停止監視內置事件終結點**"。

## <a name="next-steps"></a>後續步驟

若要為您的 IoT Edge 裝置開發自訂模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
