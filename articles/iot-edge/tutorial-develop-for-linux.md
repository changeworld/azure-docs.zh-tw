---
title: 教學課程 - 使用 Azure IoT Edge 開發 Linux 裝置的模組
description: 本教學課程會逐步引導您設定開發機器和雲端資源，以使用 Linux 容器開發適用於 Linux 裝置的 IoT Edge 模組
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 38ffd4931f318b15f1ac06fcfd742bde8e5c6838
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772958"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>教學課程：開發適用於 Linux 裝置的 IoT Edge 模組

使用 Visual Studio Code 開發程式碼並將其部署至執行 IoT Edge 的 Linux 裝置。

在本快速入門中，您已使用 Linux 虛擬機器建立 IoT Edge 裝置，並部署了來自 Azure Marketplace 的模組。 本教學課程會逐步說明如何開發您自己的程式碼並將其部署至 IoT Edge 裝置。 本文是其他教學課程的實用先決條件，其內容會深入探討特定的程式設計語言或 Azure 服務。

本教學課程使用將 **C# 模組部署至 Linux 裝置**的範例。 選擇此範例是因為這是 IoT Edge 解決方案最常見的開發人員案例。 即使您打算使用不同的語言或部署 Azure 服務，本教學課程對於了解開發工具和概念還是很有用。 完成此開發程序的簡介後，請選擇您偏好的語言或 Azure 服務以深入了解細節。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 設定開發機器。
> * 使用 IoT Edge Tools for Visual Studio Code 來建立新專案。
> * 將專案建置為容器，並儲存在 Azure Container Registry。
> * 將程式碼部署至 IoT Edge 裝置。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>重要概念

本教學課程會逐步引導您開發 IoT Edge 模組。 「IoT Edge 模組」  (有時簡稱為「模組」)  是含有可執行程式碼的容器。 您可以在 IoT Edge 裝置中部署一或多個模組。 模組會執行特定工作，例如從感應器擷取資料、清除及分析資料，或對 IoT 中樞傳送訊息。 如需詳細資訊，請參閱[了解 Azure IoT Edge 模組](iot-edge-modules.md)。

在開發 IoT Edge 模組時，請務必了解開發機器和作為模組最終部署位置的目標 IoT Edge 裝置之間有何差異。 為了保存模組程式碼所建置的容器必須符合「目標裝置」  的作業系統 (OS)。 例如，最常見的情況是有人在 Windows 電腦上開發模組，卻又打算以執行 IoT Edge 的 Linux 裝置作為適用對象。 在此情況下，容器的作業系統會是 Linux。 當您進行本教學課程時，請牢記「開發機器 OS」  和「容器 OS」  之間的差異。

本教學課程是以執行 IoT Edge 的 Linux 裝置作為適用對象。 您可以使用慣用的作業系統，只要您的開發機器執行 Linux 容器即可。 建議您使用 Visual Studio Code 來針對 Linux 裝置進行開發，本教學課程也是這麼做的。 您也可以使用 Visual Studio，但這兩種工具的支援有所差異。

下表列出在 Visual Studio Code 和 Visual Studio 中 **Linux 容器**所支援的開發案例。

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux 裝置架構** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure 服務** | Azure Functions <br> Azure 串流分析 <br> Azure Machine Learning |   |
| **語言** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **詳細資訊** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)提供 Linux ARM64 裝置的支援。 如需詳細資訊，請參閱[在 Visual Studio Code (預覽)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) 中開發和偵錯 ARM64 IoT Edge 模組。

本教學課程會教授 Visual Studio Code 的開發步驟。 如果您比較想要使用 Visual Studio，請參閱[使用 Visual Studio 2019 來開發適用於 Azure IoT Edge 的模組並針對其進行偵錯](how-to-visual-studio-develop-module.md)中的指示。

## <a name="prerequisites"></a>Prerequisites

開發機器：

* 您可以使用自己的電腦或虛擬機器 (視您偏好的開發方式而定)。
  * 請確定您的開發機器支援「巢狀虛擬化」。 下一節中安裝的容器引擎會需要此功能來執行。
* 大部分可以執行容器引擎的作業系統，均可用來開發適用於 Linux 裝置的 IoT Edge 模組。 本教學課程會使用 Windows 電腦，但會指出 MacOS 或 Linux 上的已知差異。
* 安裝 [Git](https://git-scm.com/)，以在本教學課程稍後提取模組的範本套件。  
* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

位於 Linux 上的 Azure IoT Edge 裝置：

* 我們不建議您在開發機器上執行 IoT Edge，請改為使用另外的裝置。 讓開發機器和 IoT Edge 裝置有所區分可以更加精確地反映真正的部署情況，而有助於讓不同的概念變得簡單明瞭。
* 如果您沒有第二個裝置可供使用，請使用快速入門文章在具有 [Linux 虛擬機器](quickstart-linux.md)的 Azure 中建立 IoT Edge 裝置。

雲端資源：

* 在 Azure 中擁有免費或標準層的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="install-container-engine"></a>安裝容器引擎

IoT Edge 模組會封裝為容器，因此開發機器上必須有容器引擎才能建置和管理容器。 建議您使用 Docker Desktop 進行開發，因其支援多項功能，並且廣受使用。 Windows 上的 Docker Desktop 可讓您在 Linux 容器和 Windows 容器之間進行切換，從而能夠輕鬆地為不同類型的 IoT Edge 裝置開發模組。

使用 Docker 文件以便安裝在開發機器上：

* [安裝適用於 Windows 的 Docker Desktop](https://docs.docker.com/docker-for-windows/install/)

  * 當您安裝適用於 Windows 的 Docker Desktop 時，系統會詢問您是要使用 Linux 還是 Windows 容器。 您可以隨時輕鬆地切換而改變這項決策。 本教學課程會使用 Linux 容器，因為我們的模組會以 Linux 裝置作為目標。 如需詳細資訊，請參閱[在 Windows 與 Linux 容器之間切換](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。

* [安裝適用於 Mac 的 Docker Desktop](https://docs.docker.com/docker-for-mac/install/)

* 閱讀[關於 Docker CE](https://docs.docker.com/install/) 來獲得數個 Linux 平台的安裝資訊。

## <a name="set-up-vs-code-and-tools"></a>設定 VS Code 和工具

使用適用於 Visual Studio Code 的 IoT 擴充功能來開發 IoT Edge 模組。 這些擴充功能會提供專案範本、自動執行部署資訊清單的建立程序，並可讓您監視和管理 IoT Edge 裝置。 在本節中，您會安裝 Visual Studio Code 和 IoT 擴充功能，然後設定 Azure 帳戶以便從 Visual Studio Code 內部管理 IoT 中樞資源。

1. 在開發機器上安裝 [Visual Studio Code](https://code.visualstudio.com/)。

2. 安裝完成後，選取 [檢視]   > [擴充功能]  。

3. 搜尋 **Azure IoT Tools**，這實際上是擴充功能的集合，可協助您與 IoT 中樞和 IoT 裝置互動，以及開發 IoT Edge 模組。

4. 選取 [安裝]  。 內含的每個擴充功能會分別進行安裝。

5. 擴充功能完成安裝時，選取 [檢視]   > [命令選擇區]  來開啟命令選擇區。

6. 在命令選擇區中，搜尋並選取 [Azure:  登入]。 依照提示來登入您的 Azure 帳戶。

7. 再次於命令選擇區中，搜尋並選取 [Azure IoT 中樞:  選取 IoT 中樞]。 遵循提示來選取 Azure 訂用帳戶和 IoT 中樞。

8. 在左側的活動列中選取圖示，或選取 [檢視]   > [總管]  ，來開啟 Visual Studio Code 的 [總管] 區段。

9. 在 [總管] 區段底部，將收合的 [Azure IoT 中樞裝置]  功能表展開。 您應該會看到與您透過命令選擇區所選取 IoT 中樞相關聯的裝置和 IoT Edge 裝置。

   ![檢視 IoT 中樞內的裝置](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>建立新的模組專案

Azure IoT Tools 擴充功能會針對 Visual Studio Code 中所有支援的 IoT Edge 模組語言，提供專案範本。 這些範本具有您部署運作中模組以測試 IoT Edge 時需要的所有檔案和程式碼，或可成為您利用自己的商務邏輯自訂此範本的起點。

本教學課程會使用 C# 模組範本，因為這是最常使用的範本。

### <a name="create-a-project-template"></a>建立專案範本

在 Visual Studio Code 命令選擇區中，搜尋並選取 [Azure IoT Edge:  檢視 IoT Edge 方案]。 遵循提示，然後使用下列值來建立解決方案：

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution**。 |
   | 選取模組範本 | 選擇 [C# 模組]  。 |
   | 提供模組名稱 | 接受預設的 **SampleModule**。 |
   | 提供模組的 Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 您的容器映像會從您在上一個步驟中提供的名稱預先填入。 將 **localhost:5000** 取代為 Azure Container Registry 的登入伺服器值。 您可以在 Azure 入口網站中，從容器登錄的 [概觀] 頁面擷取登入伺服器。 <br><br> 最終的映像存放庫看起來類似於：\<登錄名稱\>.azurecr.io/samplemodule。 |

   ![提供 Docker 映像存放庫](./media/tutorial-develop-for-linux/image-repository.png)

Visual Studio Code 視窗中載入您的新解決方案後，請花點時間熟悉其所建立的檔案：

* **.vscode** 資料夾包含名為 **launch.json** 的檔案，其可用來對模組進行偵錯。
* **modules** 資料夾包含解決方案中每個模組的資料夾。 現在，裡面應該只有 **SampleModule**，或您提供給模組的任何名稱。 SampleModule 資料夾包含主要程式碼、模組中繼資料和數個 Docker 檔案。
* **.env** 檔案會保有容器登錄的認證。 這些認證會與 IoT Edge 裝置共用，使其具有存取權而得以提取容器映像。
* **deployment.debug.template.json** 檔案和 **deployment.template.json** 檔案是範本，可協助您建立部署資訊清單。 「部署資訊清單」  檔案會確切定義您想要在裝置上部署的模組、模組的設定方式，以及模組要如何彼此通訊以及與雲端通訊。 範本檔案會對某些值使用指標。 當您將範本轉換為真正的部署資訊清單時，指標便會取代為從其他解決方案檔案取得的值。 請在部署範本中找出兩個常見的預留位置：

  * 在 [登錄認證] 區段中，位址會自動填入您在建立解決方案時所提供的資訊。 不過，使用者名稱和密碼會參照 .env 檔案中所儲存的變數。 此設定是為了確保安全，因為 .env 檔案會遭到 git 忽略，但部署範本則不會。
  * [SampleModule] 區段中不會填入容器映像，即使您在建立解決方案時提供了映像存放庫也是一樣。 此預留位置會指向 SampleModule 資料夾內的 **module.json** 檔案。 如果您移至該檔案，就會看到 [映像] 欄位的確有包含存放庫，但也會包含由容器的版本和平台組成的標記值。 您可以在開發週期中手動重複版本，並且會使用本節稍候會介紹的切換器來選取容器平台。

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>對 IoT Edge 代理程式提供登錄認證

環境檔案會儲存容器登錄的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將容器映像提取到 IoT Edge 裝置。

IoT Edge 擴充功能會嘗試從 Azure 提取您的容器登錄認證，並將這些認證填入到環境檔案中。 請查看您的認證是否已包含其中。 如果沒有，請立即新增：

1. 開啟模組解決方案中的 **.env** 檔案。
2. 新增您從 Azure Container Registry 複製過來的 [使用者名稱]  和 [密碼]  值。
3. 將變更儲存至 .env 檔案。

### <a name="select-your-target-architecture"></a>選取您的目標架構

目前，Visual Studio Code 可以開發適用於 Linux AMD64 和ARM32v7 裝置的 C# 模組。 您必須為每個解決方案選取要作為目標的架構，因為架構會影響容器的建置和執行方式。 預設值為 Linux AMD64。

1. 開啟命令選擇區並搜尋 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或選取視窗底部側邊欄的捷徑圖示。

   ![在提要欄位選取架構圖示](./media/tutorial-develop-for-linux/select-architecture.png)

2. 在命令選擇區中，從選項清單中選取目標架構。 針對此教學課程，我們是使用 Ubuntu 虛擬機器作為 IoT Edge 裝置，因此會保留預設 **amd64**。

### <a name="review-the-sample-code"></a>檢閱範例程式碼

您所建立的解決方案範本包含 IoT Edge 模組的程式碼範例。 此模組範例只會接收訊息，再加以傳遞。 管線功能會示範 IoT Edge 中的重要概念，也就是模組彼此之間要如何通訊。

每個模組都可以在其程式碼中宣告多個「輸入」  和「輸出」  佇列。 在裝置上執行的 IoT Edge 中樞會將訊息從某個模組的輸出路由傳送至一或多個模組的輸入。 用於宣告輸入和輸出的特定語言會隨語言而異，但概念在所有模組則都相同。 如需模組之間路由方式的詳細資訊，請參閱[宣告路由](module-composition.md#declare-routes)。

專案範本所隨附的範例 C# 程式碼會使用 IoT Hub SDK for .NET 所提供的 [ModuleClient 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)。

1. 開啟 **Program.cs** 檔案，其位於 **modules/SampleModule/** 資料夾內。

2. 在 program.cs 中，尋找 **SetInputMessageHandlerAsync** 方法。

3. [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) 方法可設定用於接收內送訊息的輸入佇列。 檢閱此方法，並查看它如何初始化名為 **input1** 的輸入佇列。

   ![在 SetInputMessageCallback 建構函式中尋找輸入名稱](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. 接著，尋找 **SendEventAsync** 方法。

5. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) 方法會處理接收的訊息並設定輸出佇列來傳遞它們。 檢閱此方法，並查看它初始化名為 **output1** 的輸出佇列。

   ![在 SendEventToOutputAsync 中尋找輸出名稱](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. 開啟 **deployment.template.json** 檔案。

7. 尋找 $edgeAgent 所需屬性的 **modules** 屬性。

   這裡應該會列出兩個模組。 第一個是 **SimulatedTemperatureSensor**，所有範本預設都會隨附此模組，以提供可讓您用來測試模組的模擬溫度資料。 第二個是您在此解決方案內所建立的 **SampleModule** 模組。

8. 在檔案底部，尋找 **$edgeHub** 模組所需的屬性。

   IoT Edge 中樞模組的其中一個函式會在部署中的所有模組之間路由傳送訊息。 檢閱 **routes** 屬性中的值。 第一個路由 **SampleModuleToIoTHub** 會使用萬用字元 ( **\*** ) 來表示任何來自 SampleModule 模組中任何輸出佇列的訊息。 這些訊息會進入 $upstream  ，這是會指出 IoT 中樞的保留名稱。 第二個路由 sensorToSampleModule 會取得來自 SimulatedTemperatureSensor 模組的訊息，並將其路由至您看到再 SampleModule 程式碼中初始化的 input1  輸入佇列。

   ![檢閱 deployment.template.json 中的路由](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>建置並推送解決方案

您已檢閱過模組程式碼和部署範本來了解一些重要的部署概念。 現在，您已準備好建置 SampleModule 容器映像，並將其推送至容器登錄。 在使用適用於 Visual Studio Code 的 IoT Tools 擴充功能時，此步驟還會根據範本檔案中的資訊以及來自解決方案檔案的模組資訊，產生部署資訊清單。

### <a name="sign-in-to-docker"></a>登入 Docker

對 Docker 提供容器登錄認證，使其可以推送要儲存在登錄中的容器映像。

1. 選取 [檢視]   > [終端機]  ，以開啟 Visual Studio Code 整合式終端機。

2. 使用您在建立登錄之後所儲存的 Azure Container Registry 認證來登入 Docker。

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   您可能會收到安全性警告，建議您使用 `--password-stdin`。 雖然建議生產案例使用該最佳做法，但是不在本教學課程的討論範圍內。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 參考。

### <a name="build-and-push"></a>建置與推送

Visual Studio Code 現在可以存取容器登錄，因此您現在可以將解決方案的程式碼轉換成容器映像。

1. 在 Visual Studio Code 總管中，以滑鼠右鍵按一下 **deployment.template.json** 檔案，然後選取 [建置並推送 IoT Edge 解決方案]  。

   ![建置和推送 IoT Edge 模組](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   建置和推送命令會啟動三項作業。 首先，它會在解決方案中建立名為 **config** 的新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。

2. 在新建立的 config 資料夾中開啟 **deployment.amd64.json** 檔案。 檔案名稱會反映目標架構，因此如果您選擇不同的架構，就會有不同的檔案名稱。

3. 請注意，有預留位置的兩個參數現在會填入適當的值。 **registryCredentials** 區段會從 .env 檔案提取登錄的使用者名稱和密碼。 **SampleModule** 具有完整的映像存放庫，內含來自 module.json 檔案的名稱、版本和架構標記。

4. 開啟 SampleModule 資料夾中的 **module.json** 檔案。

5. 變更模組映像的版本號碼。 (版本，而非 $schema-version)。例如，將修補程式版本號碼遞增 **0.0.2**，就好像我們在模組程式碼中進行了些許修正。

   >[!TIP]
   >模組版本可實現版本控制功能，並讓您先對一小組裝置測試變更，再將更新部署到生產環境。 如果您沒有先遞增模組版本再進行建置和推送，則會覆寫容器登錄中的存放庫。

6. 將變更儲存至 module.json 檔案。

7. 再次以滑鼠右鍵按一下 **deployment.template.json** 檔案，然後再次選取 [建置並推送 IoT Edge 解決方案]  。

8. 再次開啟 **deployment.amd64.json** 檔案。 請注意，當您再次執行建置和推送命令時，系統並未建立新的檔案。 反過來，系統會更新同一個檔案以反映變更。 SampleModule 映像現在會指向容器的 0.0.2 版本。

9. 若要進一步確認建置和推送命令會做些什麼，請移至 [Azure 入口網站](https://portal.azure.com)並瀏覽至容器登錄。

10. 在容器登錄中，依序選取 [存放庫]  和 [samplemodule]  。 確認映像的這兩個版本皆已推送至登錄。

    ![在容器登錄中檢視這兩個映像版本](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>疑難排解

如果您在建置和推送模組映像時遇到錯誤，原因往往與開發機器上的 Docker 設定有關。 請使用下列檢查來檢閱您的設定：

* 您是否使用從容器登錄複製的認證來執行 `docker login` 命令？ 這些認證與您用來登入 Azure 的認證不同。
* 您的容器存放庫是否正確？ 其是否有正確的容器登錄名稱和正確的模組名稱？ 開啟 SampleModule 資料夾中的 **module.json** 檔案來進行檢查。 存放庫值看起來類似於： **\<登錄名稱\>.azurecr.io/samplemodule**。
* 如果您為模組使用不同於 **SampleModule** 的名稱，該名稱在整個解決方案中是否保持一致？
* 機器所執行的容器類型是否與所建置的容器類型相同？ 本教學課程適用於 Linux 的 IoT Edge 裝置，因此 Visual Studio Code 應該要在提要欄位中顯示 **amd64** 或 **arm32v7**，且 Docker Desktop 應該執行 Linux 容器。  

## <a name="deploy-modules-to-device"></a>將模組部署到裝置

您已確認所建置的容器映像儲存在容器登錄中，因此可以開始將這些映像部署到裝置。 請確定您的 IoT Edge 裝置已啟動並執行。

1. 在 Visual Studio Code 總管中，展開 [Azure IoT 中樞裝置] 區段。

2. 以滑鼠右鍵按一下要作為部署目的地的 IoT Edge 裝置，然後選取 [建立單一裝置的部署]  。

   ![建立單一裝置的部署](./media/tutorial-develop-for-linux/create-deployment.png)

3. 在檔案總管中，瀏覽至 **config** 資料夾，然後選取 **deployment.amd64.json** 檔案。

   請勿使用 deployment.template.json 檔案，其並未內含容器登錄認證或模組映像值。 如果您以 Linux ARM32 裝置作為目標，則部署資訊清單會命名為 deployment.arm32v7.json。

4. 依序展開 IoT Edge 裝置的詳細資料和裝置的 [模組]  清單。

5. 使用 [重新整理] 按鈕來更新裝置檢視，直到您看到裝置上開始執行 SimulatedTemperatureSensor 和 SampleModule 模組。

   這兩個模組可能需要幾分鐘才會啟動。 IoT Edge 執行階段需要接收其新的部署資訊清單、從容器執行階段提取模組映像，然後啟動每個新的模組。

   ![檢視在 IoT Edge 裝置上執行的模組](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>檢視裝置的訊息

SampleModule 程式碼會透過其輸入佇列接收訊息，並透過其輸出佇列傳遞。 部署資訊清單會宣告將訊息從 SimulatedTemperatureSensor 傳至 SampleModule，再將訊息從 SampleModule 轉送到 IoT 中樞的路由。 適用於 Visual Studio Code 的 Azure IoT Tools 可讓您查看從個別裝置抵達 IoT 中樞的訊息。

1. 在 Visual Studio Code 總管中，以滑鼠右鍵按一下您要監視的 IoT Edge 裝置，然後選取 [開始監視內建事件端點]  。

2. 觀看 Visual Studio Code 中的 [輸出] 視窗，以查看抵達 IoT 中樞的訊息。

   ![檢視傳入的裝置到雲端訊息](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>檢視裝置上的變更

如果您要查看裝置本身發生了什麼事情，請使用本節中的命令來檢查正在裝置上執行的 IoT Edge 執行階段和模組。

本節中的命令適用於 IoT Edge 裝置而非開發機器。 如果您對 IoT Edge 裝置使用虛擬機器，請立即與其連線。 在 Azure 中，移至虛擬機器的 [概觀] 頁面，然後選取 [連線]  以存取安全殼層連線。

* 檢視所有部署到裝置的模組，並檢查其狀態：

   ```bash
   iotedge list
   ```

   您應該會看到四個模組：兩個 IoT Edge 執行階段模組、SimulatedTemperatureSensor 和 SampleModule。 這四個模組應該都會列示為執行中狀態。

* 檢查特定模組的記錄：

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge 模組會區分大小寫。

   SimulatedTemperatureSensor 和 SampleModule 記錄應該會顯示它們正在處理的訊息。 edgeAgent 模組負責啟動其他模組，因此其記錄會有關於實做部署資訊清單的資訊。 如果有任何模組並未列出或未執行，edgeAgent 記錄可能會有錯誤。 edgeHub 模組負責模組與 IoT 中樞之間的通訊。 如果模組已啟動並執行，但訊息並未抵達 IoT 中樞，則 edgeHub 記錄可能會有錯誤。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在開發機器上設定 Visual Studio Code，並從中部署第一個 IoT Edge 模組。 您已知道基本概念，接下來請嘗試對模組新增功能，使其可以分析其中通過的資料。 請選擇您慣用的語言：

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
