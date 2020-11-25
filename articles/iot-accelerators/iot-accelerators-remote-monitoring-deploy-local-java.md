---
title: 在本機部署遠端監視解決方案-IntelliJ IDE-Azure |Microsoft Docs
description: 本操作指南說明如何使用 IntelliJ 進行測試和開發，將遠端監視解決方案加速器部署到本機電腦。
author: dominicbetts
ms.custom: devx-track-java
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 78573cfe00d8e2e7ddcbf705dffdd5530f82c4e0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024192"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>在本機部署遠端監視解決方案加速器 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文會示範如何將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。 您將瞭解如何在 IntelliJ 中執行微服務。 本機微服務部署會使用下列雲端服務： IoT 中樞、Azure Cosmos DB、Azure 串流分析和 Azure 時間序列深入解析。

如果您想要在本機電腦上執行 Docker 中的遠端監視解決方案加速器，請參閱[本機部署遠端監視解決方案加速器 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)。

## <a name="prerequisites"></a>Prerequisites

若要部署遠端監視解決方案加速器所使用的 Azure 服務，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>電腦設定

若要完成本機部署，您必須在本機開發機器上安裝下列工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ 社群版](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala 外掛程式](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT 外掛程式](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT 執行程式外掛程式](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 是腳本用來建立 Azure 資源之電腦 CLI 的先決條件。 不要使用 Node.js v10。

> [!NOTE]
> IntelliJ IDE 適用於 Windows 和 Mac。

## <a name="download-the-source-code"></a>下載原始程式碼

遠端監視原始程式碼存放庫包含您執行微服務 Docker 映像所需的原始程式碼和 Docker 設定檔。

若要複製及建立本機版本的存放庫，請使用您的命令列環境移至本機電腦上的適當資料夾。 然後執行下列其中一組命令，以複製 JAVA 存放庫：

* 若要下載最新版本的 JAVA 微服務執行，請執行下列命令：

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* 若要取得最新的子模組，請執行下列命令：

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> 除了您用來在本機執行微服務的指令碼之外，這些命令還會下載所有微服務的原始程式碼。 您不需要原始程式碼，就能在 Docker 中執行微服務。 但是，如果您稍後打算修改解決方案加速器，並在本機測試您的變更，原始程式碼就會很有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服務

雖然此文章會示範如何在本機執行微服務，但它們會仰賴雲端中執行的 Azure 服務。 使用下列指令碼部署 Azure 服務。 腳本範例假設您使用的是 Windows 電腦上的 JAVA 存放庫。 若您是在另一個環境作業，請適當地調整路徑、檔案副檔名與路徑分隔符號。

### <a name="create-new-azure-resources"></a>建立新的 Azure 資源

若您尚未建立必要的 Azure 資源，請依照下列步驟執行：

1. 在您的命令列環境中，移至您複製的存放庫複本中的 **\services\scripts\local\launch** 資料夾。

1. 執行下列命令來安裝 **pcs** CLI 工具，並登入至您的 Azure 帳戶：

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. 執行 **start.cmd** 指令碼。 指令碼會提示您輸入下列資訊：

   * 解決方案名稱。
   * 要使用的 Azure 訂用帳戶。
   * 要使用之 Azure 資料中心的位置。

   此腳本會在 Azure 中建立具有您解決方案名稱的資源群組。 此資源群組包含解決方案加速器所使用的 Azure 資源。 當您不再需要對應的資源之後，就可以刪除此資源群組。

   此腳本也會將一組環境變數新增至您的本機電腦。 每個變數名稱都有首碼 **電腦**。 這些環境變數會提供詳細資料，讓遠端監視從 Azure Key Vault 資源讀取其設定值。

   > [!TIP]
   > 當腳本完成時，會將環境變數儲存到名為的 **\<your home folder\> \\ \\ \<solution name\> 檔案中。** 您可以將它們用於未來的解決方案加速器部署。 請注意，您在本機電腦上設定的任何環境變數都會覆寫 **服務 \\ 腳本 \\ 本機 \\ env** 檔案中的值（當您執行 **docker 撰寫時）**。

1. 關閉您的命令列環境。

### <a name="use-existing-azure-resources"></a>使用現有的 Azure 資源

如果您已建立必要的 Azure 資源，請在本機電腦上設定對應的環境變數：
* **PCS_KEYVAULT_NAME**： Key Vault 資源的名稱。
* **PCS_AAD_APPID**： Azure Active Directory (Azure AD) 應用程式識別碼。
* **PCS_AAD_APPSECRET**： Azure AD 應用程式秘密。

將會從此 Key Vault 資源讀取設定值。 您可以從部署將這些環境變數儲存在 **\<your home folder\> \\ 電腦 \\ \<solution name\> . env** 檔案中。 請注意，當您執行 **docker-compose** 時，您本機電腦上設定的環境變數會覆寫 **services\\scripts\\local\\.env** 檔案中的值。

微服務所需的部分設定會儲存在初始部署時建立的 Key Vault 實例中。 您應視需要修改金鑰保存庫中的對應變數。

## <a name="run-the-microservices"></a>執行微服務

在本節中，您會執行遠端監視微服務。 您執行：

* Web UI 本身。
* Docker 中的 Azure IoT 裝置模擬、驗證和 Azure 串流分析管理員服務。
* IntelliJ 中的微服務。

### <a name="run-the-device-simulation-service"></a>執行裝置模擬服務

開啟新的命令提示字元視窗。 檢查您是否可以存取上一節中的 **start. cmd** 腳本所設定的環境變數。

執行下列命令，以開啟適用于裝置模擬服務的 Docker 容器。 服務會模擬遠端監視解決方案的裝置。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>執行驗證服務

開啟新的命令提示字元視窗，然後執行下列命令以開啟驗證服務的 Docker 容器。 您可以使用此服務來管理已獲授權可存取 Azure IoT 解決方案的使用者。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>執行串流分析管理員服務

開啟新的命令提示字元視窗，然後執行下列命令，以開啟串流分析管理員服務的 Docker 容器。 您可以使用此服務來管理串流分析作業。 這類管理包括設定作業設定，以及啟動、停止和監視作業狀態。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>在本機電腦上部署所有其他微服務

下列步驟說明如何在 IntelliJ 中執行遠端監視微服務。

#### <a name="import-a-project"></a>匯入專案

1. 開啟 IntelliJ IDE。
1. 選取 [匯 **入專案**]。
1. 選擇 [ **azure-iot-pcs-remote-monitoring-java\services\build.sbt**]。

#### <a name="create-run-configurations"></a>建立回合設定

1. 選取 [**執行**  >  **編輯** 設定]。
1. 選取 [**新增** 設定  >  **sbt** 工作]。
1. 輸入 **名稱**，然後將工作 **輸入** 為 **執行**。
1. 根據您想要執行的服務來選取 **工作目錄** 。
1. 選取 **Apply**  >  **[套用確定]** 以儲存您的選擇。
1. 建立下列 web 服務的回合設定：
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

例如，下圖顯示如何新增服務的設定：

[![IntelliJ IDE [執行]/[偵錯工具設定] 視窗的螢幕擷取畫面，顯示在左窗格的 [sbt 工作] 清單中反白顯示的 >storageadapter 選項，以及右窗格中 [名稱]、[工作]、[工作目錄] 和 [VM 參數] 方塊中的專案。](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>建立複合設定

1. 若要同時執行所有服務，請選取 [**新增** 設定  >  **複合**]。
1. 輸入 **名稱**，然後選取 [ **加入 sbt** 工作]。
1. 選取 **Apply**  >  **[套用確定]** 以儲存您的選擇。

例如，下圖顯示如何將所有 sbt 工作新增至單一設定：

[![IntelliJ IDE [執行/偵錯工具設定] 視窗的螢幕擷取畫面，其中顯示在左窗格的複合清單中反白顯示的 AllServices 選項，以及在右窗格中反白顯示的 sbt 工作 ' deviceTelemetry ' 選項。](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

選取 [ **執行** ]，在本機電腦上建立並執行 web 服務。

每個 web 服務都會開啟命令提示字元視窗和網頁瀏覽器視窗。 在命令提示字元中，您會看到執行中服務的輸出。 瀏覽器視窗可讓您監視狀態。 不要關閉命令提示字元視窗或網頁，因為這些動作會停止 web 服務。

若要存取服務的狀態，請移至下列 Url：

* IoT-Hub 管理員： `http://localhost:9002/v1/status`
* 裝置遙測： `http://localhost:9004/v1/status`
* 配置： `http://localhost:9005/v1/status`
* 存放裝置-介面卡： `http://localhost:9022/v1/status`

### <a name="start-the-stream-analytics-job"></a>啟動串流分析工作

請遵循下列步驟以啟動串流分析作業：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
1. 移至為解決方案建立的 **資源群組** 。 當您執行 **start.cmd** 指令碼時為解決方案選擇的名稱，就是資源群組的名稱。
1. 選取資源清單中的 **串流分析作業** 。
1. 在 [串流 **分析作業]** 頁面上，選取 [ **開始** ] 按鈕，然後選取 [ **啟動** ] 以啟動作業。

### <a name="run-the-web-ui"></a>執行 Web UI

在此步驟中，您會啟動 Web UI。 開啟新的命令提示字元視窗。 檢查您是否可以存取 **start. cmd** 腳本所設定的環境變數。 移至存放庫本機複本中的 **webui** 資料夾，然後執行下列命令：

```cmd
npm install
npm start
```

當 **start** 命令完成時，您的瀏覽器會在位址顯示頁面 `http://localhost:3000/dashboard` 。 此頁面上的錯誤一如預期。 若要在沒有錯誤的情況下查看應用程式，請完成下列步驟。

### <a name="configure-and-run-nginx"></a>設定和執行 Nginx

設定反向 proxy 伺服器，以將 web 應用程式連結至在本機電腦上執行的微服務：

1. 從存放庫本機複本中的 **webui\scripts\localhost** 資料夾，將 **nginx** 檔複製到 **nginx\conf** 安裝目錄。
1. 執行 Nginx。

如需執行 Nginx 的詳細資訊，請參閱 [適用于 Windows 的 Nginx](https://nginx.org/en/docs/windows.html)。

### <a name="connect-to-the-dashboard"></a>連線至儀表板

若要存取遠端監視解決方案儀表板，請移至 `http://localhost:9000` 瀏覽器中的。

## <a name="clean-up"></a>清除

若要避免不必要的費用，請在完成測試之後，從您的 Azure 訂用帳戶中移除雲端服務。 若要移除服務，請移至 [Azure 入口網站](https://ms.portal.azure.com)，並刪除 **啟動 .cmd** 腳本所建立的資源群組。

您也可以刪除當您從 GitHub 複製原始程式碼時，所建立的遠端監視存放庫本機副本。

## <a name="next-steps"></a>後續步驟

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](quickstart-remote-monitoring-deploy.md)。
