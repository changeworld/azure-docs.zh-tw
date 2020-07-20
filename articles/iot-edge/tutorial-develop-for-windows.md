---
title: 教學課程 - 使用 Azure IoT Edge 開發 Windows 裝置的模組
description: 本教學課程會逐步引導您設定開發機器和雲端資源，以使用 Windows 容器開發適用於 Windows 裝置的 IoT Edge 模組
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772256"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>教學課程：開發適用於 Windows 裝置的 IoT Edge 模組

使用 Visual Studio 開發程式碼並將其部署至執行 IoT Edge 的 Windows 裝置。

在快速入門中，您已使用 Windows 虛擬機器建立 IoT Edge 裝置，並部署了來自 Azure Marketplace 的預先建置模組。 本教學課程會逐步引導您了解要如何做才能開發您自己的程式碼並將其部署至 IoT Edge 裝置。 本教學課程是其他教學課程的實用先決條件，其內容會深入探討特定的程式設計語言或 Azure 服務。

本教學課程使用將 **C# 模組部署至 Windows 裝置**的範例。 選擇此範例是因為這是最常見的開發案例。 如果您想要以不同的語言進行開發，或打算將 Azure 服務部署為模組，本教學課程對於了解開發工具還是很有用。 了解開發概念後，您就可以選擇您偏好的語言或 Azure 服務來對細節進行深入了解。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 設定開發機器。
> * 使用 IoT Edge Tools for Visual Studio 來建立新專案。
> * 將專案建置為容器，並儲存在 Azure Container Registry。
> * 將程式碼部署至 IoT Edge 裝置。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>重要概念

本教學課程會逐步引導您開發 IoT Edge 模組。 「IoT Edge 模組」  (有時簡稱「模組」)  是含有可執行程式碼的容器。 您可以在 IoT Edge 裝置中部署一或多個模組。 模組會執行特定工作，例如從感應器擷取資料、執行資料分析或資料清除作業，或對 IoT 中樞傳送訊息。 如需詳細資訊，請參閱[了解 Azure IoT Edge 模組](iot-edge-modules.md)。

在開發 IoT Edge 模組時，請務必了解開發機器和作為模組最終部署位置的目標 IoT Edge 裝置之間有何差異。 為了保存模組程式碼所建置的容器必須符合「目標裝置」  的作業系統 (OS)。 在開發 Windows 容器時，這個概念會比較簡單，因為 Windows 容器只會在 Windows 作業系統上執行。 但是，您也可以 (舉例來說) 使用 Windows 開發機器來建置適用於 Linux IoT Edge 裝置的模組。 在該情況下，您必須確定開發機器執行的是 Linux 容器。 當您進行本教學課程時，請牢記「開發機器 OS」  和「容器 OS」  之間的差異。

本教學課程是以執行 IoT Edge 的 Windows 裝置作為適用對象。 Windows IoT Edge 裝置會使用 Windows 容器。 建議您使用 Visual Studio 來針對 Windows 裝置進行開發，本教學課程也是這麼做的。 您也可以使用 Visual Studio Code，但這兩種工具的支援有所差異。

下表列出在 Visual Studio Code 和 Visual Studio 中 **Windows 容器**所支援的開發案例。

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure 服務** | Azure Functions <br> Azure 串流分析 |   |
| **語言** | C# (不支援偵錯) | C <br> C# |
| **詳細資訊** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Prerequisites

開發機器：

* Windows 10 (含 1809 更新或更新版本)。
* 您可以使用自己的電腦或虛擬機器 (視您偏好的開發方式而定)。
  * 請確定您的開發機器支援「巢狀虛擬化」。 下一節中安裝的容器引擎會需要此功能來執行。
* 安裝 [Git](https://git-scm.com/)。

位於 Windows 上的 Azure IoT Edge 裝置：

* 我們不建議您在開發機器上執行 IoT Edge，請改為使用另外的裝置。 讓開發機器和 IoT Edge 裝置有所區分可以更加精確地反映真正的部署情況，而有助於讓不同的概念變得簡單明瞭。
* 如果您沒有第二個裝置可供使用，請使用快速入門文章在具有 [Windows 虛擬機器](quickstart.md)的 Azure 中建立 IoT Edge 裝置。

雲端資源：

* 在 Azure 中擁有免費或標準層的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="install-container-engine"></a>安裝容器引擎

IoT Edge 模組會封裝為容器，因此開發機器上必須有容器引擎才能建置和管理容器。 我們建議使用 Docker Desktop 來進行開發，因為其具有許多功能並且是熱門的容器引擎。 若您在 Windows 電腦上使用 Docker Desktop，便可以在 Linux 容器和 Windows 容器之間進行切換，從而能夠輕鬆地針對不同類型的 IoT Edge 裝置來開發模組。

使用 Docker 文件以便安裝在開發機器上：

* [安裝適用於 Windows 的 Docker Desktop](https://docs.docker.com/docker-for-windows/install/)

  * 當您安裝適用於 Windows 的 Docker Desktop 時，系統會詢問您是要使用 Linux 還是 Windows 容器。 本教學課程使用 **Windows 容器**。 如需詳細資訊，請參閱[在 Windows 與 Linux 容器之間切換](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。

## <a name="set-up-visual-studio-and-tools"></a>設定 Visual Studio 和工具

適用於 Visual Studio 的 IoT 擴充功能可協助您開發 IoT Edge 模組。 這些擴充功能會提供專案範本、自動執行部署資訊清單的建立程序，並可讓您監視和管理 IoT Edge 裝置。 在本節中，您會安裝 Visual Studio 和 IoT Edge 擴充功能，然後設定 Azure 帳戶以便從 Visual Studio 內部管理 IoT 中樞資源。

本教學課程會教授 Visual Studio 2019 的開發步驟。 如果您使用 Visual Studio 2017 (15.7 版或更新版本)，其步驟也大致相同。 如果您比較想要使用 Visual Studio Code，請參閱[使用 Visual Studio Code 來開發適用於 Azure IoT Edge 的模組並針對其進行偵錯](how-to-vs-code-develop-module.md)中的指示。

1. 在您的開發電腦上準備 Visual Studio 2019。

   * 如果開發機器上還沒有 Visual Studio，請使用下列工作負載來[安裝 Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio)：

      * Azure 開發
      * 使用 C++ 的桌面開發
      * .NET Core 跨平台開發

   * 如果您的開發電腦上已經有 Visual Studio 2019，請依照[修改 Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) 中的步驟來新增必要的工作負載。

2. 下載並安裝適用於 Visual Studio 2019 的 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 擴充功能。

   如果您使用 Visual Studio 2017 (15.7 或更高版本)，請下載並安裝[適用於 Visual Studio 2017 的 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)。

3. 安裝完成時，請開啟 Visual Studio 2019，並選取 [繼續但不使用程式碼]  。

4. 選取 [檢視]   > [Cloud Explorer]  。

5. 在 Cloud Explorer 中選取 [設定檔] 圖示，並登入 Azure 帳戶 (如果您尚未登入)。

6. 登入後，系統便會列出您的 Azure 訂用帳戶。 展開具有 IoT 中樞的訂用帳戶。

7. 在您的訂用帳戶之下，依序展開 [IoT 中樞]  和您的 IoT 中樞。 您應該會看到 IoT 裝置清單，並可使用此瀏覽器來管理這些裝置。

   ![在 Cloud Explorer 中存取 IoT 中樞資源](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>建立新的模組專案

Azure IoT Edge Tools 擴充功能會針對 Visual Studio 中所有支援的 IoT Edge 模組語言，提供專案範本。 這些範本具有您部署運作中模組以測試 IoT Edge 時需要的所有檔案和程式碼，或可成為您利用自己的商務邏輯自訂此範本的起點。

1. 選取 [檔案]   > [新增]   > [專案...] 

2. 在新的專案視窗中，搜尋 [IoT Edge]  ，然後選擇 [Azure IoT Edge (Windows amd64)]  專案。 按 [下一步]  。

   ![建立新的 Azure IoT Edge 專案](./media/tutorial-develop-for-windows/new-project.png)

3. 在設定新專案的視窗中，將專案和解決方案重新命名為 **CSharpTutorialApp** 之類的描述性項目。 按一下 [建立]  以建立專案。

   ![設定新的 Azure IoT Edge 專案](./media/tutorial-develop-for-windows/configure-project.png)

4. 在 [新增模組] 視窗中，使用下列值設定您的專案：

   | 欄位 | 值 |
   | ----- | ----- |
   | Visual Studio 範本 | 選取 [C# 模組]  。 |
   | 模組名稱 | 接受預設值 **IotEdgeModule1**。 |
   | 存放庫 URL | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會從模組專案名稱值預先填入容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的登入伺服器值。 您可以在 Azure 入口網站中，從容器登錄的 [概觀]  頁面擷取**登入伺服器**值。 <br><br> 最終的映像存放庫看起來類似於：\<登錄名稱\>.azurecr.io/iotedgemodule1。 |

      ![針對目標裝置、模組類型和容器登錄設定您的專案](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. 選取 [新增]  以建立模組。

Visual Studio 視窗中載入您的新專案後，請花點時間熟悉其所建立的檔案：

* 名為 **CSharpTutorialApp** 的 IoT Edge 專案。
  * **Modules** 資料夾包含專案中所納入模組的指標。 在此案例中，應該就是 IotEdgeModule1。
  * 隱藏的 **.env** 檔案會保有容器登錄的認證。 這些認證會與 IoT Edge 裝置共用，使其具有存取權而得以提取容器映像。
  * **deployment.template.json** 檔案是可協助您建立部署資訊清單的範本。 「部署資訊清單」  檔案會確切定義您想要在裝置上部署的模組、模組的設定方式，以及模組要如何彼此通訊以及與雲端通訊。
    > [!TIP]
    > 在 [登錄認證] 區段中，位址會自動填入您在建立解決方案時所提供的資訊。 不過，使用者名稱和密碼會參照 .env 檔案中所儲存的變數。 這麼做是為了確保安全，因為 .env 檔案會遭到 git 忽略，但部署範本則不會。
* 名為 **IotEdgeModule1** 的 IoT Edge 模組專案。
  * **program.cs** 檔案包含預設的 C# 模組程式碼，此程式碼隨附於專案範本中。 預設模組會從來源取得輸入，再將其傳遞至 IoT 中樞。
  * **module.json** 檔案會保存模組的相關詳細資料，包括完整的映像存放庫、映像版本，以及要對每個支援的平台使用哪一個 Dockerfile。

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>對 IoT Edge 代理程式提供登錄認證

IoT Edge 執行階段需要登錄認證才能將容器映像提取到 IoT Edge 裝置。 IoT Edge 擴充功能會嘗試從 Azure 提取您的容器登錄資訊，並將其填入部署範本中。

1. 開啟模組解決方案中的 **deployment.template.json** 檔案。

1. 在 $edgeAgent 所需的屬性中尋找 **registryCredentials** 屬性。 其中應該會從您在建立專案時提供的資訊自動填入您的登錄位址，而使用者名稱和密碼欄位應該會包含變數名稱。 例如：

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. 開啟模組解決方案中的 **.env** 檔案。 (該檔案依預設會隱藏於方案總管中，因此您可能需要選取 [顯示所有檔案]  按鈕加以顯示。)

1. 新增您從 Azure Container Registry 複製過來的 [使用者名稱]  和 [密碼]  值。

1. 將變更儲存至 .env 檔案。

### <a name="review-the-sample-code"></a>檢閱範例程式碼

您所建立的解決方案範本包含 IoT Edge 模組的程式碼範例。 此模組範例只會接收訊息，再加以傳遞。 管線功能會示範 IoT Edge 中的重要概念，也就是模組彼此之間要如何通訊。

每個模組都可以在其程式碼中宣告多個「輸入」  和「輸出」  佇列。 在裝置上執行的 IoT Edge 中樞會將訊息從某個模組的輸出路由傳送至一或多個模組的輸入。 用於宣告輸入和輸出的特定語言會隨語言而異，但概念在所有模組則都相同。 如需模組之間路由方式的詳細資訊，請參閱[宣告路由](module-composition.md#declare-routes)。

專案範本所隨附的範例 C# 程式碼會使用 IoT Hub SDK for .NET 所提供的 [ModuleClient 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)。

1. 在 **program.cs** 檔案中，尋找 **SetInputMessageHandlerAsync** 方法。

2. [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) 方法可設定用於接收內送訊息的輸入佇列。 檢閱此方法，並查看它如何初始化名為 **input1** 的輸入佇列。

   ![在 SetInputMessageHandlserAsync 建構函式中尋找輸入名稱](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. 接著，尋找 **SendEventAsync** 方法。

4. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) 方法會處理接收的訊息並設定輸出佇列來傳遞它們。 檢閱此方法，並查看它初始化名為 **output1** 的輸出佇列。

   ![在 SendEventAsync 建構函式中尋找輸出名稱](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. 開啟 **deployment.template.json** 檔案。

6. 尋找 $edgeAgent 所需屬性的 **modules** 屬性。

   這裡應該會列出兩個模組。 第一個是 **SimulatedTemperatureSensor**，所有範本預設都會隨附此模組，以提供可讓您用來測試模組的模擬溫度資料。 第二個是您在此專案內建立的 **IotEdgeModule1** 模組。

   此 modules 屬性會宣告哪些模組應該包含在您的裝置部署中。

7. 尋找 $edgeHub 所需屬性的 **routes** 屬性。

   IoT Edge 中樞模組的其中一個函式會在部署中的所有模組之間路由傳送訊息。 檢閱 routes 屬性中的值。 第一個路由 **IotEdgeModule1ToIoTHub** 會使用萬用字元 ( **\*** ) 來包含任何來自 IotEdgeModule1 模組中任何輸出佇列的訊息。 這些訊息會進入 $upstream  ，這是會指出 IoT 中樞的保留名稱。 第二個路由 **sensorToIotEdgeModule1** 會取得來自 SimulatedTemperatureSensor 模組的訊息，並將它們路由傳送至 IotEdgeModule1 模組的 *input1* 輸入佇列。

   ![檢閱 deployment.template.json 中的路由](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>建置並推送解決方案

您已檢閱過模組程式碼和部署範本來了解一些重要的部署概念。 現在，您已準備好建置 IotEdgeModule1 容器映像，並將其推送至容器登錄。 在使用適用於 Visual Studio 的 IoT Tools 擴充功能時，此步驟還會根據範本檔案中的資訊以及來自解決方案檔案的模組資訊，產生部署資訊清單。

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

1. 以滑鼠右鍵按一下 **CSharpTutorialApp** 專案資料夾，然後選取 [建置和推送 IoT Edge 模組]  。

   ![建置和推送 IoT Edge 模組](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   建置和推送命令會啟動三項作業。 首先，它會在解決方案中建立名為 **config** 的新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。

2. 在新建立的 config 資料夾中開啟 **deployment.windows-amd64.json** 檔案。 (config 資料夾可能不會出現在 Visual Studio 中的方案總管內。 如果遇到這種情況，請選取方案總管工作列中的 [顯示所有檔案]  圖示)。

3. 尋找 IotEdgeModule1 區段的 **image** 參數。 請注意，此映像包含完整的映像存放庫，內含來自 module.json 檔案的名稱、版本和架構標記。

4. 開啟 IotEdgeModule1 資料夾中的 **module.json** 檔案。

5. 變更模組映像的版本號碼。 (版本，而非 $schema-version)。例如，將修補程式版本號碼遞增 **0.0.2**，就好像我們在模組程式碼中進行了些許修正。

   >[!TIP]
   >模組版本可實現版本控制功能，並讓您先對一小組裝置測試變更，再將更新部署到生產環境。 如果您沒有先遞增模組版本再進行建置和推送，則會覆寫容器登錄中的存放庫。

6. 將變更儲存至 module.json 檔案。

7. 再次以滑鼠右鍵按一下 **CSharpTutorialApp** 專案資料夾，然後再次選取 [建置和推送 IoT Edge 模組]  。

8. 再次開啟 **deployment.windows-amd64.json** 檔案。 請注意，當您再次執行建置和推送命令時，系統並未建立新的檔案。 反過來，系統會更新同一個檔案以反映變更。 IotEdgeModule1 映像現在會指向容器的 0.0.2 版本。 在部署資訊清單中進行這項變更可讓 IoT Edge 裝置知道有新的模組版本需要提取。

9. 若要進一步確認建置和推送命令會做些什麼，請移至 [Azure 入口網站](https://portal.azure.com)並瀏覽至容器登錄。

10. 在容器登錄中，依序選取 [存放庫]  和 [iotedgemodule1]  。 確認映像的這兩個版本皆已推送至登錄。

    ![在容器登錄中檢視這兩個映像版本](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>疑難排解

如果您在建置和推送模組映像時遇到錯誤，原因往往與開發機器上的 Docker 設定有關。 請使用下列檢查來檢閱您的設定：

* 您是否使用從容器登錄複製的認證來執行 `docker login` 命令？ 這些認證與您用來登入 Azure 的認證不同。
* 您的容器存放庫是否正確？ 其是否有正確的容器登錄名稱和正確的模組名稱？ 開啟 IotEdgeModule1 資料夾中的 **module.json** 檔案來進行檢查。 存放庫值看起來類似於： **\<登錄名稱\>.azurecr.io/iotedgemodule1**。
* 如果您為模組使用不同於 **IotEdgeModule1** 的名稱，該名稱在整個解決方案中是否保持一致？
* 機器所執行的容器類型是否與所建置的容器類型相同？ 本教學課程適用於 Windows 的 IoT Edge 裝置，因此 Visual Studio 檔案應該要有 **windows-amd64** 擴充功能，且 Docker Desktop 應該執行 Windows 容器。

## <a name="deploy-modules-to-device"></a>將模組部署到裝置

您已確認所建置的容器映像儲存在容器登錄中，因此可以開始將這些映像部署到裝置。 請確定您的 IoT Edge 裝置已啟動並執行。

1. 在 Visual Studio 中開啟 [Cloud Explorer]，並展開 IoT 中樞的詳細資料。

2. 選取要作為部署目的地裝置的名稱。 在 [動作]  清單中，選取 [建立部署]  。

   ![建立單一裝置的部署](./media/tutorial-develop-for-windows/create-deployment.png)

3. 在檔案總管中瀏覽至專案的 config 資料夾，然後選取 **deployment.windows-amd64.json** 檔案。 此檔案通常位於 `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   請勿使用 deployment.template.json 檔案，其並未內含完整的模組映像值。

4. 在 Cloud Explorer 中展開 IoT Edge 裝置的詳細資料，以查看裝置上的模組。

5. 使用 [重新整理]  按鈕來更新裝置狀態，以查看 SimulatedTemperatureSensor 和 IotEdgeModule1 模組是否已部署至裝置。

   ![檢視在 IoT Edge 裝置上執行的模組](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>檢視裝置的訊息

IotEdgeModule1 程式碼會透過其輸入佇列接收訊息，並透過其輸出佇列傳遞。 部署資訊清單會宣告將訊息從 SimulatedTemperatureSensor 傳遞至 IotEdgeModule1，再將訊息從 IotEdgeModule1 轉送到 IoT 中樞的路由。 Azure IoT Edge Tools for Visual Studio 可讓您查看從個別裝置抵達 IoT 中樞的訊息。

1. 在 Visual Studio Cloud Explorer 中，選取已作為您部署目的地 IoT Edge 裝置的名稱。

2. 在 [動作]  功能表中，選取 [開始監視內建事件端點]  。

3. 觀看 Visual Studio 中的 [輸出]  區段，以查看抵達 IoT 中樞的訊息。

   這兩個模組可能需要幾分鐘才會啟動。 IoT Edge 執行階段需要接收其新的部署資訊清單、從容器執行階段提取模組映像，然後啟動每個新的模組。

   ![檢視傳入的裝置到雲端訊息](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>檢視裝置上的變更

如果您要查看裝置本身發生了什麼事情，請使用本節中的命令來檢查正在裝置上執行的 IoT Edge 執行階段和模組。

本節中的命令適用於 IoT Edge 裝置而非開發機器。 如果您對 IoT Edge 裝置使用虛擬機器，請立即與其連線。 在 Azure 中，移至虛擬機器的 [概觀] 頁面，然後選取 [連線]  以存取遠端桌面連線。 在裝置上，開啟命令或 PowerShell 視窗以執行 `iotedge` 命令。

* 檢視所有部署到裝置的模組，並檢查其狀態：

   ```cmd
   iotedge list
   ```

   您應該會看到四個模組：兩個 IoT Edge 執行階段模組、SimulatedTemperatureSensor 和 IotEdgeModule1。 這四個模組應該都會列示為執行中狀態。

* 檢查特定模組的記錄：

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge 模組會區分大小寫。

   SimulatedTemperatureSensor 和 IotEdgeModule1 記錄應該會顯示它們正在處理的訊息。 edgeAgent 模組負責啟動其他模組，因此其記錄會有關於實做部署資訊清單的資訊。 如果有任何模組並未列出或未執行，edgeAgent 記錄可能會有錯誤。 edgeHub 模組負責模組與 IoT 中樞之間的通訊。 如果模組已啟動並執行，但訊息並未抵達 IoT 中樞，則 edgeHub 記錄可能會有錯誤。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在開發機器上設定 Visual Studio 2019，並從中部署第一個 IoT Edge 模組。 您已知道基本概念，接下來請嘗試對模組新增功能，使其可以分析其中通過的資料。 請選擇您慣用的語言：

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
