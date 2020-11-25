---
title: 在本機部署遠端監視解決方案-VS IDE-Azure |Microsoft Docs
description: 此操作指南會示範如何使用 Visual Studio，將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024141"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>本機部署遠端監視解決方案加速器 - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文會示範如何將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。 您可了解如何在 Visual Studio 中執行微服務。 本機微服務部署使用下列雲端服務：雲端中的 IoT 中樞、Cosmos DB、Azure 串流分析及 Azure 時間序列深入解析服務。

如果您想要在本機電腦上執行 Docker 中的遠端監視解決方案加速器，請參閱[本機部署遠端監視解決方案加速器 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)。

## <a name="prerequisites"></a>Prerequisites

若要部署遠端監視解決方案加速器所使用的 Azure 服務，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>電腦設定

若要完成本機部署，您必須在本機開發機器上安裝下列工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - 此軟體是指令碼用來建立 Azure 資源之 PCS CLI 的先決條件。 不要使用 Node.js v10。

> [!NOTE]
> Visual Studio 適用於 Windows 和 Mac。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>執行微服務

在本節中，您會執行遠端監視微服務。 原生執行 Web UI、Docker 中的裝置模擬服務，以及 Visual Studio 中的微服務。

### <a name="run-the-device-simulation-service"></a>執行裝置模擬服務

開啟新的命令提示字元視窗，以確保能夠存取上一節中 **start.cmd** 指令碼設定的環境變數。

執行下列命令以啟動裝置模擬服務的 Docker 容器。 該服務會模擬遠端監視解決方案的裝置。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>在本機電腦上部署所有其他微服務

下列步驟說明如何在 Visual Studio 中執行遠端監視微服務：

1. 啟動 Visual Studio。
1. 開啟 **remote-monitoring.sln** 解決方案 (位於存放庫本機複本的 **services** 資料夾中)。
1. 以滑鼠右鍵按一下 [方案總管] 中的方案，然後按一下 [屬性]。
1. 選取 [通用屬性] > [啟始專案]。
1. 選取 [多個啟始專案]，然後將下列專案的 [動作] 設定為 [啟動]：
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. 按一下 [確定]  儲存您的選擇。
1. 按一下 [偵錯] > [開始偵錯]，以在本機電腦上建置並執行 Web 服務。

每個 Web 服務都會開啟一個命令提示字元和 Web 瀏覽器視窗。 您可以在命令提示字元中看到執行中服務的輸出，還有瀏覽器視窗可讓您監視狀態。 請不要關閉命令提示字元或網頁，這個動作會停止 Web 服務。

### <a name="start-the-stream-analytics-job"></a>啟動串流分析工作

請遵循下列步驟以啟動串流分析作業：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至為解決方案建立的 **資源群組**。 當您執行 **start.cmd** 指令碼時為解決方案選擇的名稱，就是資源群組的名稱。
1. 在資源清單中，按一下 [串流分析作業]。
1. 在串流分析作業 [概觀] 頁面上，按一下 [啟動] 按鈕。 接著，按一下 [啟動] 以立即啟動作業。

### <a name="run-the-web-ui"></a>執行 Web UI

在此步驟中，您會啟動 Web UI。 開啟新的命令提示字元視窗，以確保能夠存取 **start.cmd** 指令碼設定的環境變數。 瀏覽到存放庫本機複本中的 **webui** 資料夾，然後執行下列命令：

```cmd
npm install
npm start
```

當啟動完成時，您的瀏覽器會顯示頁面 **HTTP： \/ /localhost： 3000/儀表板**。 此頁面上的錯誤一如預期。 若要檢視沒有錯誤的應用程式，請完成下列步驟。

### <a name="configure-and-run-nginx"></a>設定和執行 NGINX

設定反向 Proxy 伺服器，以連結 Web 應用程式與在本機電腦上執行的微服務：

* 將 **nginx.conf** 檔案從存放庫本機複本中的 **webui\scripts\localhost** 資料夾複製到 **nginx\conf** 安裝目錄。
* 執行 **nginx**。

如需執行 **nginx** 的詳細資訊，請參閱 [適用於 Windows 的 nginx](https://nginx.org/en/docs/windows.html) \(英文\)。

### <a name="connect-to-the-dashboard"></a>連線至儀表板

若要存取遠端監視解決方案儀表板，請 \/ 在瀏覽器中流覽至 HTTP：/localhost：9000。

## <a name="clean-up"></a>清除

為避免不必要的費用，當您完成測試後，請從您的 Azure 訂用帳戶中移除雲端服務。 若要移除服務，請瀏覽到 [Azure 入口網站](https://ms.portal.azure.com)，然後刪除 **start.cmd** 指令碼建立的資源群組。

您也可以將您從 GitHub 複製原始程式碼時，所建立的遠端監視存放庫本機副本刪除。

## <a name="next-steps"></a>後續步驟

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](quickstart-remote-monitoring-deploy.md)。
