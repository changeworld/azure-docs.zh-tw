---
title: 在本地部署遠端監視解決方案 - IntelliJ IDE - Azure |微軟文檔
description: 本說明指南演示如何使用 IntelliJ 進行測試和開發將遠端監視解決方案加速器部署到本地電腦。
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888817"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>在本機部署遠端監視解決方案加速器 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文會示範如何將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。 您將學習如何在 IntelliJ 中運行微服務。 本地微服務部署將使用以下雲服務：IoT 中心、Azure Cosmos DB、Azure 流分析和 Azure 時間序列見解。

如果您想要在本機電腦上執行 Docker 中的遠端監視解決方案加速器，請參閱[本機部署遠端監視解決方案加速器 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)。

## <a name="prerequisites"></a>Prerequisites

若要部署遠端監視解決方案加速器所使用的 Azure 服務，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 有關詳細資訊，請參閱[Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>電腦設定

若要完成本機部署，您必須在本機開發機器上安裝下列工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ 社群版](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala 外掛程式](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT 外掛程式](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT 執行外掛程式](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 是腳本用於創建 Azure 資源的 PCS CLI 的先決條件。 不要使用 Node.js v10。

> [!NOTE]
> IntelliJ IDE 適用於 Windows 和 Mac。

## <a name="download-the-source-code"></a>下載原始程式碼

遠端監視原始程式碼存放庫包含您執行微服務 Docker 映像所需的原始程式碼和 Docker 設定檔。

要克隆和創建存儲庫的本地版本，請使用命令列環境轉到本地電腦上的適當資料夾。 然後運行以下命令集之一以克隆 JAVA 存儲庫：

* 要下載最新版本的 JAVA 微服務實現，請運行以下命令：

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* 要檢索最新的子模組，運行以下命令：

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> 除了您用來在本機執行微服務的指令碼之外，這些命令還會下載所有微服務的原始程式碼。 不需要原始程式碼來在 Docker 中運行微服務。 但是，如果您以後計畫修改解決方案加速器並在本地測試更改，則原始程式碼非常有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服務

雖然此文章會示範如何在本機執行微服務，但它們會仰賴雲端中執行的 Azure 服務。 使用下列指令碼部署 Azure 服務。 腳本示例假定您在 Windows 電腦上使用 JAVA 存儲庫。 若您是在另一個環境作業，請適當地調整路徑、檔案副檔名與路徑分隔符號。

### <a name="create-new-azure-resources"></a>建立新的 Azure 資源

若您尚未建立必要的 Azure 資源，請依照下列步驟執行：

1. 在命令列環境中，轉到存儲庫克隆副本中的 **[服務]腳本\local_啟動**資料夾。

1. 執行下列命令來安裝 **pcs** CLI 工具，並登入至您的 Azure 帳戶：

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. 執行 **start.cmd** 指令碼。 指令碼會提示您輸入下列資訊：

   * 解決方案名稱。
   * 要使用的 Azure 訂用帳戶。
   * 要使用之 Azure 資料中心的位置。

   該腳本在 Azure 中創建具有解決方案名稱的資源組。 此資源組包含解決方案加速器使用的 Azure 資源。 在不再需要相應的資源後，可以刪除此資源組。

   該腳本還會向本地電腦添加一組環境變數。 每個變數名稱都有首碼**PCS**。 這些環境變數提供了允許遠端監視從 Azure 金鑰保存庫資源讀取其配置值的詳細資訊。

   > [!TIP]
   > 當腳本完成時，它將環境變數保存到一個稱為**\<主資料夾\>\\\\\<.pcs 解決方案名稱\>.env**的檔。 您可以將它們用於將來的解決方案加速器部署。 請注意，在本地電腦上設置的任何環境變數在運行**docker-compose**時將覆蓋**\\服務腳本\\local\\.env**檔中的值。

1. 關閉命令列環境。

### <a name="use-existing-azure-resources"></a>使用現有的 Azure 資源

如果已創建所需的 Azure 資源，請在本地電腦上設置相應的環境變數：
* **PCS_KEYVAULT_NAME**： 金鑰保存庫資源的名稱。
* **PCS_AAD_APPID**： Azure 活動目錄 （Azure AD） 應用程式 ID。
* **PCS_AAD_APPSECRET**：Azure AD 應用程式機密。

配置值將從此金鑰保存庫資源中讀取。 這些環境變數可以保存在**\<主資料夾\>\\\\\<.pcs 解決方案名稱\>.env**檔中。 請注意，當您執行 **docker-compose** 時，您本機電腦上設定的環境變數會覆寫 **services\\scripts\\local\\.env** 檔案中的值。

微服務所需的某些配置存儲在在初始部署時創建的金鑰保存庫的實例中。 應根據需要修改金鑰保存庫中的相應變數。

## <a name="run-the-microservices"></a>執行微服務

在本節中，您會執行遠端監視微服務。 您執行：

* 本機的 Web UI。
* Docker 中的 Azure IoT 設備類比、Auth 和 Azure 流分析管理器服務。
* IntelliJ 的微服務。

### <a name="run-the-device-simulation-service"></a>運行設備類比服務

打開新的命令提示視窗。 檢查您是否有權訪問上一節中**start.cmd**腳本設置的環境變數。

運行以下命令以打開設備類比服務的 Docker 容器。 該服務類比遠端監視解決方案的設備。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>執行驗證服務

打開新的命令提示視窗，然後運行以下命令以打開 Auth 服務的 Docker 容器。 通過使用此服務，可以管理有權訪問 Azure IoT 解決方案的使用者。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>運行流分析管理器服務

打開新的命令提示視窗，然後運行以下命令以打開流分析管理器服務的 Docker 容器。 使用此服務，您可以管理流分析作業。 此類管理包括設置作業配置和啟動、停止和監視作業狀態。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>在本地電腦上部署所有其他微服務

以下步驟演示如何在 IntelliJ 中運行遠端監視微服務。

#### <a name="import-a-project"></a>導入專案

1. 打開 IntelliJ IDE。
1. 選擇**導入專案**。
1. 選擇**azure-iot-pcs-遠端監視-java_服務\build.sbt**。

#### <a name="create-run-configurations"></a>創建回合組態

1. 選擇 **"運行** > **編輯配置**"。
1. 選擇 **"添加新配置** > **sbt 任務**"。
1. 輸入**名稱**，然後輸入 **"任務****"作為運行**。
1. 根據要運行的服務選擇**工作目錄**。
1. 選擇 **"應用** > **確定"** 以保存您的選擇。
1. 為以下 Web 服務創建回合組態：
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

例如，下圖演示如何添加服務的配置：

[![IntelliJ IDE 運行/調試配置視窗的螢幕截圖，在左側窗格中的 sbt 工作清單中突出顯示的存儲配接器選項，並在右側窗格中的"名稱、任務、工作目錄和 VM 參數"框中顯示條目。](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>創建複合配置

1. 要一起運行所有服務，請選擇"**添加新配置** > **複合**"。
1. 輸入**名稱**，然後選擇**添加 sbt 任務**。
1. 選擇 **"應用** > **確定"** 以保存您的選擇。

例如，下圖演示如何將所有 sbt 任務添加到單個配置：

[![IntelliJ IDE 運行/調試配置視窗的螢幕截圖，顯示左側窗格中的"複合"清單中突出顯示的所有服務選項，並在右側窗格中突出顯示的 sbt 任務"設備遙測"選項。](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

選擇 **"運行"** 以在本地電腦上生成和運行 Web 服務。

每個 Web 服務將打開一個命令提示視窗和 Web 瀏覽器視窗。 在命令提示符下，您將看到來自正在運行的服務的輸出。 瀏覽器視窗允許您監視狀態。 不要關閉命令提示視窗或網頁，因為這些操作將停止 Web 服務。

要訪問服務的狀態，請訪問以下 URL：

* IoT 中心管理器：[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 設備遙測：[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* 配置：[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 存儲配接器：[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>啟動串流分析工作

請遵循下列步驟以啟動串流分析作業：

1. 轉到[Azure 門戶](https://portal.azure.com)。
1. 轉到為解決方案創建**的資源組**。 當您執行 **start.cmd** 指令碼時為解決方案選擇的名稱，就是資源群組的名稱。
1. 在資源清單中選擇**流分析作業**。
1. 在"流分析"作業**概述**頁上，選擇"**開始"** 按鈕，然後選擇 **"開始"** 以啟動作業。

### <a name="run-the-web-ui"></a>執行 Web UI

在此步驟中，您會啟動 Web UI。 打開新的命令提示視窗。 檢查您是否有權訪問**start.cmd**腳本設置的環境變數。 轉到存儲庫本機複本中的**webui**資料夾，然後運行以下命令：

```cmd
npm install
npm start
```

**啟動**命令完成後，瀏覽器將在 位址[http://localhost:3000/dashboard](http://localhost:3000/dashboard)顯示頁面。 此頁面上的錯誤一如預期。 要查看應用程式時沒有錯誤，請完成以下步驟。

### <a name="configure-and-run-nginx"></a>配置和運行 Nginx

設置反向代理伺服器，將 Web 應用程式連結到本地電腦上運行的微服務：

1. 將存儲庫本機複本中的**webui_scripts_localhost**資料夾中的**nginx.conf**檔案複製到**nginx_conf**安裝目錄。
1. 運行恩金克斯。

有關運行 Nginx 的詳細資訊，請參閱[適用于 Windows 的 nginx。](https://nginx.org/en/docs/windows.html)

### <a name="connect-to-the-dashboard"></a>連線至儀表板

要訪問遠端監視解決方案儀表板，請轉到http://localhost:9000瀏覽器中。

## <a name="clean-up"></a>清除

為避免不必要的費用，請在完成測試後從 Azure 訂閱中刪除雲服務。 要刪除服務，請轉到[Azure 門戶](https://ms.portal.azure.com)，然後刪除**start.cmd**腳本創建的資源組。

您還可以刪除從 GitHub 克隆原始程式碼時創建的遠端監視存儲庫的本機複本。

## <a name="next-steps"></a>後續步驟

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](quickstart-remote-monitoring-deploy.md)。
