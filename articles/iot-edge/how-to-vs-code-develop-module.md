---
title: 開發適用於 Azure IoT Edge 的模組並對其進行偵錯 | Microsoft Docs
description: 使用 C#、Python、Node.js、Java 或 C 來開發和建置適用於 Azure IoT Edge 的模組，並對其進行偵錯
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78944304"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>使用 Visual Studio Code 來開發適用於 Azure IoT Edge 的模組，並對其進行偵錯

您可以將商務邏輯轉換成 Azure IoT Edge 的模組。 本文說明如何使用 Visual Studio Code 作為開發和偵錯模組的主要工具。

有兩種方式可在 Visual Studio Code 中，使用 c #、Node.js 或 JAVA 來進行偵錯工具的程式碼撰寫：您可以在模組容器中附加進程，或在 [偵錯工具] 模式中啟動模組程式碼。 若要對以 Python 或 C 撰寫的模組進行偵錯工具，您只能附加至 Linux amd64 容器中的進程。

如果您不熟悉 Visual Studio Code 的偵錯功能，請參閱[偵錯](https://code.visualstudio.com/Docs/editor/debugging)。

本文提供以多種語言針對多個架構來開發和偵測模組的指示。 目前，Visual Studio Code 提供以 c #、C、Python、Node.js 和 JAVA 撰寫之模組的支援。 支援的裝置架構為 X64 和 ARM32。 如需有關支援的作業系統、語言和架構的詳細資訊，請參閱[語言和架構支援](module-development.md#language-and-architecture-support)。

>[!NOTE]
>Linux ARM64 裝置的開發和偵錯工具支援現供[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 如需詳細資訊，請參閱[在 Visual Studio Code (預覽)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) 中開發和偵錯 ARM64 IoT Edge 模組。

## <a name="prerequisites"></a>必要條件

您可以使用執行 Windows、macOS 或 Linux 的電腦或虛擬機器作為開發電腦。 在 Windows 電腦上，您可以開發 Windows 或 Linux 模組。 若要開發 Windows 模組，請使用執行 1809/組建17763或更新版本的 Windows 電腦。 若要開發 Linux 模組，請使用符合[Docker Desktop 需求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的 Windows 電腦。

請先安裝 [Visual Studio Code](https://code.visualstudio.com/)，然後新增下列延伸模組：

- [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker 擴充功能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- 開發所用語言的專屬 Visual Studio 延伸模組：
  - C #，包括 Azure Functions： [c # 擴充](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)功能
  - Python： [python 擴充](https://marketplace.visualstudio.com/items?itemName=ms-python.python)功能
  - JAVA： [Visual Studio Code 的 JAVA 擴充功能套件](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C： [c/c + + 擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)功能

您也需要安裝一些額外的語言特定工具來開發您的模組：

- C# (包括 Azure Functions)：[.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python： [python](https://www.python.org/downloads/)和[Pip](https://pip.pypa.io/en/stable/installing/#installation) ，用於安裝 python 套件（通常包含在您的 python 安裝中）。

- Node.js： [Node.js](https://nodejs.org)。 您也要安裝 [Yeoman](https://www.npmjs.com/package/yo) 和 [Azure IoT Edge Node.js Module Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module)。

- JAVA： [JAVA SE 開發套件 10](https://aka.ms/azure-jdks)和[Maven](https://maven.apache.org/)。 您必須[設定 `JAVA_HOME` 環境變數](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)指向 JDK 安裝。

若要建立及部署您的模組映射，您需要 Docker 來建立模組映射和容器登錄來保存模組映射：

- 開發電腦上的[Docker 社區版本](https://docs.docker.com/install/)。

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)或[Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > 您可以使用本機 Docker 登錄作為原型並用於測試，而非使用雲端登錄。

除非使用 C 開發模組，否則您也需要以 Python 為基礎的 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)，才能設定本機開發環境以偵錯、執行和測試您的 IoT Edge 解決方案。 如果您尚未這麼做，請先安裝[Python （2.7/3.6/3.7）和 Pip](https://www.python.org/) ，然後在您的終端機中執行此命令來安裝**iotedgehubdev** 。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> 目前，iotedgehubdev 使用與 Python 3.8 不相容的 docker .py 程式庫。
>
> 如果您有多個 Python，包括預先安裝的 python 2.7 （例如，在 Ubuntu 或 macOS 上），請確定您使用正確的 `pip` 或 `pip3` 安裝**iotedgehubdev**

若要在裝置上測試模組，您需要一個有效的 IoT 中樞，而且該中樞中至少必須有一個 IoT Edge 裝置。 若要使用您的電腦作為 IoT Edge 裝置，請遵循 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入門中的步驟。 如果您在開發電腦上執行 IoT Edge 精靈，您可能必須先停止 EdgeHub 與 EdgeAgent 後，再移至下一個步驟。

## <a name="create-a-new-solution-template"></a>建立新的解決方案範本

下列步驟示範如何使用 Visual Studio Code 和 Azure IoT 工具組，以您慣用的開發語言 (包括以 C# 撰寫的 Azure Functions) 來建立 IoT Edge 模組。 您開始建立解決方案，然後在該解決方案中產生第一個模組。 每個解決方案都可以包含多個模組。

1. 選取 [檢視]   > [命令調色盤]  。

1. 在 [命令選擇區] 中，輸入並執行命令**Azure IoT Edge： [新增 IoT Edge 方案**]。

   ![執行新的 IoT Edge 解決方案](./media/how-to-develop-csharp-module/new-solution.png)

1. 瀏覽至要用來建立新解決方案的資料夾，然後選取 [選取資料夾]****。

1. 輸入解決方案的名稱。

1. 選取適用於您慣用開發語言的模組範本，作為解決方案中的第一個模組。

1. 輸入模組的名稱。 選擇容器登錄內唯一的名稱。

1. 提供模組映像存放庫的名稱。 Visual Studio Code 會自動以 **localhost:5000/<您的模組名稱\>** 填入模組名稱。 請使用您自己的登錄資訊加以取代。 如果您使用本機 Docker 登錄進行測試，則**localhost**是正常的。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 登入伺服器看起來像** _\<registry name\>_ . azurecr.io**。 僅取代字串的**localhost： 5000**部分，讓最終結果看起來像** \<*registry name*\> azurecr.io/ _\<your module name\>_ **。

   ![提供 Docker 映像存放庫](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code 會採用您提供的資訊、建立 IoT Edge 解決方案，然後將其載入至新的視窗。

解決方案中有四個項目：

- 一個包含偵錯組態的 **.vscode** 資料夾。

- 一個 **modules** 資料夾，其中包含每個模組的子資料夾。  在每個模組的資料夾中，都有一個檔案 [ **module.js開啟**]，可控制模組的建立和部署方式。  您必須修改此檔案，才能將模組部署容器登錄從 localhost 變更為遠端登入。 此時，您只有一個模組。  但是您可以在命令選擇區中使用 **Azure IoT Edge: Add IoT Edge Module** 命令來新增更多項目。

- 一個 **.env** 檔案，會列出您的環境變數。 如果您的登錄是 Azure Container Registry，您會有 Azure Container Registry 使用者名稱和密碼。

  > [!NOTE]
  > 環境檔案只會在您為模組提供了映像存放庫時才會建立。 如果您接受 localhost 預設值，並且在本機進行測試和偵錯，則不需要宣告環境變數。

- 檔案**上的deployment.template.js**會列出新的模組，以及模擬可用於測試之資料的範例**SimulatedTemperatureSensor**模組。 如需部署資訊清單運作方式的詳細資訊，請參閱[了解如何使用部署資訊清單以部署模組和建立路由](module-composition.md)。

若要查看模擬的溫度模組如何運作，請參閱[SimulatedTemperatureSensor 的原始碼](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)。

## <a name="add-additional-modules"></a>新增其他模組

若要將其他模組新增至您的解決方案，請從命令選擇區執行命令**Azure IoT Edge：新增 IoT Edge 模組**。 您也可以在 Visual Studio Code 總管檢視中，以滑鼠右鍵按一下 **modules** 資料夾或 `deployment.template.json` 檔案，然後選取 [新增 IoT Edge 模組]****。

## <a name="develop-your-module"></a>開發您的模組

解決方案隨附的預設模組程式碼位於下列位置：

- Azure Function （c #）：模組 **> 您的模組* &lt; &gt; 名稱*  >  * &lt; 您 &gt; 的模組名稱*.cs**
- C#：**modules > *&lt;您的模組名稱&gt;* > Program.cs**
- Python：**modules > *&lt;您的模組名稱&gt;* > main.py**
- Node.js：**modules > *&lt;您的模組名稱&gt;* > app.js**
- Java：**modules > *&lt;您的模組名稱&gt;* > src > main > java > com > edgemodulemodules > App.java**
- C：**modules > *&lt;您的模組名稱&gt;* > main.c**

系統會設定模組和 deployment.template.json 檔案，讓您可以建置解決方案、將其推送至容器登錄，然後部署到裝置以開始測試，而不需要觸碰到任何程式碼。 此模組的建立是為了只接受來源的輸入（在此案例中為模擬資料的 SimulatedTemperatureSensor 模組），並使用管線將它傳送至 IoT 中樞。

當您準備要使用自己的程式碼自訂範本時，請使用 [Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md) 以建置模組，該模組會滿足 IoT 解決方案的主要需求，例如安全性、裝置管理和可靠性。

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>偵錯模組而不使用容器 (C#、Node.js、Java)

如果使用 C#、Node.js 或 Java 進行開發，您的模組需要使用預設模組程式碼中的 **ModuleClient** 物件，才能啟動、執行及路由傳送訊息。 您也會使用預設輸入通道 **input1**，以便在模組收到訊息時採取動作。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>設定 IoT Edge 解決方案的 IoT Edge 模擬器

在您的開發電腦上，您可以藉由啟動 IoT Edge 模擬器來執行 IoT Edge 解決方案，而不安裝 IoT Edge 安全性精靈。

1. 在左側的裝置總管中，以滑鼠右鍵按一下您的 IoT Edge 裝置識別碼，然後選取 [設定 IoT Edge 模擬器]**** 以使用裝置連接字串啟動模擬器。
1. 您可以在整合式終端機中檢閱進度詳細資料，了解 IoT Edge 模擬器是否已成功設定。

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>設定單一模組應用程式的 IoT Edge 模擬器

若要設定並啟動模擬器，請從 Visual Studio Code 命令選擇區執行命令**Azure IoT Edge：啟動 IoT Edge Hub 模擬器 For 單一模組**。 出現提示時，使用預設模組程式碼中的值 **input1** (或您程式碼中的對等值)，作為您應用程式的輸入名稱。 此命令會觸發 **iotedgehubdev** CLI，然後啟動 IoT Edge 模擬器並測試公用程式模組容器。 如果模擬器已在單一模組模式中順利啟動，您就會在整合式終端機中看到下列輸出。 您也可以查看 `curl` 命令，以利傳送訊息。 稍後您將會用到此資訊。

   ![設定單一模組應用程式的 IoT Edge 模擬器](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   您可以使用 Visual Studio Code 中的 Docker 總管檢視，來查看模組的執行狀態。

   ![模擬器模組狀態](media/how-to-develop-csharp-module/simulator-status.png)

   **edgeHubDev** 容器是本機 IoT Edge 模擬器的核心。 它無需 IoT Edge 安全性精靈即可在您的開發電腦上執行，並可為您的原生模組應用程式或模組容器提供環境設定。 **輸入**容器公開的 REST API 可協助橋接器訊息在您的模組上設定輸入通道的目標。

### <a name="debug-module-in-launch-mode"></a>在啟動模式中對模組進行偵錯

1. 準備環境以便根據您的開發語言需求進行偵錯、在您的模組中設定中斷點，然後選取要使用的偵錯設定：
   - **C#**
     - 在 Visual Studio Code 整合式終端機中，將目錄變更為*** &lt; 您的 &gt; 模組名稱***資料夾，然後執行下列命令以建立 .net Core 應用程式。

       ```cmd
       dotnet build
       ```

     - 開啟檔案 `Program.cs` 並新增中斷點。

     - 選取 [檢視] > [偵錯]**** 以巡覽至 Visual Studio Code 偵錯檢視。 從下拉式清單中選取 [ ** * &lt; 您 &gt; 的模組名稱*本機 debug （.net Core）** ] 的 [偵測設定]。

        > [!NOTE]
        > 如果您的 .NET Core `TargetFramework` 與中的程式路徑不一致 `launch.json` ，您必須手動更新中的程式路徑， `launch.json` 以符合 .csproj 檔案 `TargetFramework` 中的，讓 Visual Studio Code 可以順利啟動此程式。

   - **Node.js**
     - 在 Visual Studio Code 整合式終端機中，將目錄變更為*** &lt; 您的 &gt; 模組名稱***資料夾，然後執行下列命令以安裝節點套件

       ```cmd
       npm install
       ```

     - 開啟檔案 `app.js` 並新增中斷點。

     - 選取 [檢視] > [偵錯]**** 以巡覽至 Visual Studio Code 偵錯檢視。 從下拉式清單中選取 [設定** * &lt; 您的模組名稱 &gt; *本機 debug （Node.js）** ] 的 [debug] 設定。
   - **Java**
     - 開啟檔案 `App.java` 並新增中斷點。

     - 選取 [檢視] > [偵錯]**** 以巡覽至 Visual Studio Code 偵錯檢視。 從下拉式清單中選取 [ ** * &lt; 您 &gt; 的模組名稱*本機 debug （JAVA）** ] 的 [偵測設定]。

1. 按一下 [開始偵錯]****，或按 **F5** 啟動偵錯工作階段。

1. 在 Visual Studio Code 整合式終端機中執行下列命令，將 **Hello World** 訊息傳送至您的模組。 這是先前的步驟中設定 IoT Edge 模擬器時所顯示的命令。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > 如果您使用 Windows，請確定 Visual Studio Code 整合式終端機的殼層是 **Git Bash** 或 **WSL Bash**。 您無法從 PowerShell 或命令提示字元執行 `curl` 命令。
   > [!TIP]
   > 您也可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具來傳送訊息，而不使用 `curl`。

1. 在 Visual Studio Code 偵錯檢視中，您可以在左面板中看到變數。

1. 若要停止您的偵錯工具，請選取 [停止] 按鈕，或按**Shift + F5**，然後在命令選擇區中執行**Azure IoT Edge： stop IoT Edge**模擬器，以停止模擬器並進行清除。

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>使用 IoT Edge 模擬器在附加模式中進行偵錯 (C#、Node.js、Java、Azure Functions)

您的預設解決方案包含兩個模組，一個是模擬溫度感應器模組，另一個則是管道模組。 模擬溫度感應器會將訊息傳送至管道模組，然後訊息會輸送至 IoT 中樞。 在您已建立的模組資料夾中，會有不同容器類型的多個 Docker 檔案。 請使用任何副檔名為 **.debug** 的檔案來建置測試用模組。

目前，對附加模式偵錯的支援僅限於：

- C# 模組 (包括 Azure Functions 模組) 支援在 Linux amd64 容器中進行偵錯
- Node.js 模組支援在 Linux amd64 和 arm32v7 容器以及 Windows amd64 容器中進行偵錯
- Java 模組支援在 Linux amd64 和 arm32v7 容器中進行偵錯

> [!TIP]
> 您可以為 IoT Edge 解決方案切換預設平台選項，方法是按一下 Visual Studio Code 狀態列中的項目。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>設定 IoT Edge 解決方案的 IoT Edge 模擬器

在您的開發電腦中，您可以藉由啟動 IoT Edge 模擬器來執行 IoT Edge 解決方案，而不安裝 IoT Edge 安全性精靈。

1. 在左側的裝置總管中，以滑鼠右鍵按一下您的 IoT Edge 裝置識別碼，然後選取 [設定 IoT Edge 模擬器]**** 以使用裝置連接字串啟動模擬器。

1. 您可以在整合式終端機中檢閱進度詳細資料，了解 IoT Edge 模擬器是否已成功設定。

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>建置並執行要偵錯的容器，並在附加模式中偵錯

1. 開啟您的模組檔 (`Program.cs`、`app.js`、`App.java` 或 `<your module name>.cs`) 並新增中斷點。

1. 在 Visual Studio Code 總管檢視中，以滑鼠右鍵按一下解決方案的 `deployment.debug.template.json` 檔案，然後選取 [在模擬器中建置並執行 IoT Edge 解決方案]****。 您可以在相同的視窗中查看所有模組容器記錄。 您也可以巡覽至 Docker 檢視以監看容器狀態。

   ![觀察變數](media/how-to-vs-code-develop-module/view-log.png)

1. 巡覽至 Visual Studio Code 偵錯檢視並選取模組的偵錯設定檔。 Debug 選項名稱應類似于** * &lt; 您的模組名稱 &gt; *遠端 debug**

1. 選取 [開始偵錯]****，或按 **F5**。 選取所要連結的流程。

1. 在 Visual Studio Code 偵錯檢視中，您可以在左面板中看到變數。

1. 若要停止「調試」會話，請先選取 [停止] 按鈕，或按**Shift + F5**，然後從命令選擇區選取 [ **Azure IoT Edge：停止 IoT Edge**模擬器]。

> [!NOTE]
> 上述範例說明如何針對容器上的 IoT Edge 模組進行偵錯。 它會將公開連接埠新增至您模組的容器 `createOptions` 設定。 完成模組偵錯之後，建議您針對已準備好投入生產環境的 IoT Edge 模組，移除這些公開連接埠。
>
> 對於以 C# (包括 Azure Functions) 撰寫的模組，此範例是以 `Dockerfile.amd64.debug` 的偵錯版本為基礎，其中包含建置它時容器映像中的 .NET Core 命令列偵錯工具 (VSDBG)。 針對 C# 模組進行偵錯之後，建議您針對已可供生產環境使用的 IoT Edge 模組，直接使用 Dockerfile (不含 VSDBG)。

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>使用 IoT Edge 執行階段偵錯模組

在每個模組資料夾中，會有不同容器類型的多個 Docker 檔案。 請使用任何副檔名為 **.debug** 的檔案來建置測試用模組。

使用此方法進行模組偵錯時，您的模組會在 IoT Edge 執行階段以外的執行階段執行。 IoT Edge 裝置和 Visual Studio Code 可以位於相同電腦上，或通常 Visual Studio Code 位於開發電腦上，而 IoT Edge 執行階段和模組則是在另一部實體電腦上執行。 若要從 Visual Studio Code 進行偵錯，您必須：

- 設定您的 IoT Edge 裝置，使用 **.debug** Dockerfile 建置 IoT Edge 模組，然後部署至 IoT Edge 裝置。
- 公開模組的 IP 和連接埠，以便連結偵錯工具。
- 更新 `launch.json`，以便 Visual Studio Code 可連結到遠端電腦上容器中的程序。 這個檔案位於您工作區的 `.vscode` 資料夾中，每當您新增支援偵錯的模組時，此檔案都會更新。

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>建置您的模組並且部署到 IoT Edge 裝置

1. 在 Visual Studio Code 中開啟 `deployment.debug.template.json` 檔案，其中包含您模組映像的偵錯版本並設定適當的 `createOptions` 值。

1. 如果您想要使用 Python 開發模組，請遵循下列步驟再繼續進行：
   - 開啟檔案 `main.py` 並在 import 區段後面新增此程式碼：

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - 將下列一行程式碼新增至您要進行偵錯的回呼：

      ```python
      ptvsd.break_into_debugger()
      ```

     例如，如果您想要對函式進行 `receive_message_listener` 程式碼處理，您可以插入該行，如下所示：

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. 在 Visual Studio Code 命令選擇區中：
   1. 執行命令**Azure IoT Edge： Build 和 Push IoT Edge 解決方案**。

   1. 為您的解決方案選取 `deployment.debug.template.json` 檔案。

1. 在 Visual Studio Code 總管檢視的 [Azure IoT 中樞裝置]**** 區段中：
   1. 在 IoT Edge 裝置識別碼上按一下滑鼠右鍵，然後選取 [**建立單一裝置的部署**]。

      > [!TIP]
      > 若要確認您所選擇的裝置是 IoT Edge 裝置，請選取該裝置以展開模組清單，並確認 **$edgeHub** 和 **$edgeAgent** 是否存在。 每個 IoT Edge 都裝置包含這兩個模組。

   1. 巡覽至您解決方案的 **config** 資料夾，選取 `deployment.debug.amd64.json` 檔案，然後選取 [選取 Edge 部署資訊清單]****。

您會看到部署已順利建立，而且整合式終端機中會有部署識別碼。

您可以透過在終端機中執行 `docker ps` 命令來檢查您的容器狀態。 如果 Visual Studio Code 和 IoT Edge 執行階段是在相同電腦上執行，您也可以在 Visual Studio Code Docker 檢視中檢查狀態。

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>公開偵錯工具的模組 IP 和連接埠

如果您的模組在與 Visual Studio Code 相同的電腦上執行，則可略過這一節，因為您使用 localhost 連結至容器且 **.debug** Dockerfile、模組的容器 `createOptions` 設定及 `launch.json` 檔案 中已經有正確的連接埠設定。 如果您的模組和 Visual Studio Code 在不同的機器上執行，請針對您的開發語言遵循下列步驟。

- **C#，包括 Azure Functions**

  [在您的開發電腦和 IoT Edge 裝置上設定 SSH 通道](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes)，然後編輯要連結的 `launch.json` 檔案。

- **Node.js**

  - 確定要偵錯的電腦上的模組正在執行並可供偵錯工具連結，而且可在外部存取連接埠 9229。 您可以在偵錯工具機器上開啟 `http://<target-machine-IP>:9229/json`，以驗證此操作。 此 URL 應顯示所要偵錯 Node.js 模組的相關資訊。
  
  - 在您的開發電腦上，開啟 Visual Studio Code 然後編輯， `launch.json` 讓** * &lt; 您的模組名稱 &gt; *遠端 debug （Node.js）** 設定檔（或您的** * &lt; 模組名稱 &gt; *遠端 debug （Windows 容器中的 Node.js）** 設定檔（如果模組是以 windows 容器形式執行）是要進行調試之機器的 IP 位址。

- **Java**

  - 執行 `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`，對要偵錯的電腦建置 SSH 通道。
  
  - 在您的開發電腦上，開啟 Visual Studio Code，並在中編輯** * &lt; 您的模組名稱 &gt; *遠端 Debug （JAVA）** 設定檔， `launch.json` 讓您可以附加至目的電腦。 若要深入了解如何使用 Visual Studio Code 來編輯 `launch.json` 及偵錯 Java，請參閱[設定偵錯工具](https://code.visualstudio.com/docs/java/java-debugging#_configuration)。

- **Python**

  - 請確定要偵錯的電腦上的連接埠 5678 已開放且可供存取。

  - 在您先前插入 `main.py` 的程式碼 `ptvsd.enable_attach(('0.0.0.0', 5678))` 中，將 **0.0.0.0** 變更為要偵錯的電腦 IP 位址。 再次建置、推送及部署您的 IoT Edge 模組。

  - 在您的開發電腦上，開啟 Visual Studio Code 然後編輯， `launch.json` 讓 `host` ** * &lt; 您的模組名稱 &gt; *遠端 Debug （Python）** 設定檔的值使用目的電腦的 IP 位址，而不是 `localhost` 。

### <a name="debug-your-module"></a>對模組進行偵錯

1. 在 Visual Studio Code 偵錯檢視中，選取模組的偵錯設定檔。 Debug 選項名稱應類似于** * &lt; 您的模組名稱 &gt; *遠端 debug**

1. 開啟適用於您開發語言的模組檔並新增中斷點：

   - **Azure Function （c #）**：將您的中斷點新增至檔案 `<your module name>.cs` 。
   - **C #**：將您的中斷點新增至檔案 `Program.cs` 。
   - **Node.js**：將您的中斷點新增至檔案 `app.js` 。
   - **JAVA**：將您的中斷點新增至檔案 `App.java` 。
   - **Python**： `main.py` 在您新增行的回呼方法中，將您的中斷點新增至檔案 `ptvsd.break_into_debugger()` 。
   - **C**：將您的中斷點新增至檔案 `main.c` 。

1. 選取 [開始偵錯]**** 或選取 **F5**。 選取所要連結的流程。

1. 在 Visual Studio Code 偵錯檢視中，您可以在左面板中看到變數。

> [!NOTE]
> 上述範例說明如何針對容器上的 IoT Edge 模組進行偵錯。 它會將公開連接埠新增至您模組的容器 `createOptions` 設定。 完成模組偵錯之後，建議您針對已準備好投入生產環境的 IoT Edge 模組，移除這些公開連接埠。

## <a name="build-and-debug-a-module-remotely"></a>從遠端對模組進行建置及偵錯

由於 Docker 和 Moby 引擎於近日皆已導入支援 SSH 連線的變更，且 Azure IoT 工具中也有新的設定，可供將環境設定插入 Visual Studio Code 命令選擇區及 Azure IoT Edge 終端機中，因此您現已可以在遠端裝置上對模組進行建置及偵錯。

請參閱此 [IoT 開發人員部落格文章](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) \(英文\) 以取得詳細資訊及逐步指示。

## <a name="next-steps"></a>後續步驟

建立模組之後，請瞭解如何[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)。

若要為您的 IoT Edge 裝置開發模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
