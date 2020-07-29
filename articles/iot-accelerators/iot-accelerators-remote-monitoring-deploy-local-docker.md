---
title: 在本機部署遠端監視解決方案-Docker-Azure |Microsoft Docs
description: 此操作指南會示範如何使用 Docker，將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73888827"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>本機部署遠端監視解決方案加速器 - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文會示範如何將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。 您可了解如何將微服務部署盪本機的 Docker 容器。 本機微服務部署使用下列雲端服務：雲端中的 IoT 中樞、Cosmos DB、Azure 串流分析及 Azure 時間序列深入解析服務。

如果您想要在本機電腦上執行 IDE 中的遠端監視解決方案加速器，請參閱[本機部署遠端監視解決方案加速器 - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md)。

## <a name="prerequisites"></a>必要條件

若要部署遠端監視解決方案加速器所使用的 Azure 服務，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>電腦設定

若要完成本機部署，您必須在本機開發機器上安裝下列工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - 如果您打算對微服務進行變更。
* [Node.js v8](https://nodejs.org/) - 此軟體是指令碼用來建立 Azure 資源之 PCS CLI 的先決條件。 不要使用 Node.js v10。

> [!NOTE]
> 這些工具可在許多平台上取得，包括 Windows、Linux 及 iOS。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>在 Docker 中執行微服務

開啟新的命令提示字元，以確保能夠存取 **start.cmd** 指令碼設定的環境變數。 在 Windows 上，您可以執行下列命令來驗證設定的環境變數：

```cmd
set PCS
```

此命令會顯示 **start.cmd** 指令碼設定的所有環境變數。

請確定 Docker 正在本機電腦上執行。
> [!NOTE]
> Docker 如果是在 Windows 上執行，必須執行 [Linux 容器](https://docs.docker.com/docker-for-windows/)。

在本機 Docker 容器中執行的微服務必須能夠存取 Azure 雲端服務。 您可以使用下列命令來測試您 Docker 環境的網際網路連線，該命令會從容器內來 Ping 網際網路位址：

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

若要執行解決方案加速器，在命令列環境中瀏覽至 **services\\scripts\\local** 資料夾，然後執行下列命令：

```cmd/sh
docker-compose up
```

> [!NOTE] 
> 請務必先與 Docker [共用本機磁碟機](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115)，然後才執行 `docker-compose up`。

您第一次執行此命令時，Docker 會下載 Docker 中樞的微服務映像，以在本機建置容器。 在後續執行時，Docker 則會立即執行容器。

> [!TIP]
> Microsoft 經常會發佈包含新功能的新 Docker 映像。 在您提取最新的映像之前，可以使用下列這組命令來清除本機 Docker 容器和對應的映像：

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

您可以使用個別殼層來檢視容器中的記錄。 請先使用 `docker ps` 命令來尋找容器識別碼。 然後使用 `docker logs {container-id} --tail 1000` 來檢視指定容器的最後 1000 個項目。

### <a name="start-the-stream-analytics-job"></a>啟動串流分析工作

請遵循下列步驟以啟動串流分析作業：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至為解決方案建立的**資源群組**。 當您執行 **start.cmd** 指令碼時為解決方案選擇的名稱，就是資源群組的名稱。
1. 在資源清單中，按一下 [串流分析作業]****。
1. 在串流分析作業 [概觀]**** 頁面上，按一下 [啟動]**** 按鈕。 接著，按一下 [啟動]**** 以立即啟動作業。

### <a name="connect-to-the-dashboard"></a>連線至儀表板

若要存取遠端監視解決方案儀表板，請在瀏覽器中瀏覽至 `http://localhost:8080`。 您現在可以使用 Web UI 和本機微服務。

## <a name="clean-up"></a>清除

為避免不必要的費用，當您完成測試後，請從您的 Azure 訂用帳戶中移除雲端服務。 若要移除服務，請瀏覽到 [Azure 入口網站](https://ms.portal.azure.com)，然後刪除 **start.cmd** 指令碼建立的資源群組。

使用 `docker-compose down --rmi all` 命令來移除 Docker 映像，並釋放您本機電腦上的空間。 您也可以將您從 GitHub 複製原始程式碼時，所建立的遠端監視存放庫本機副本刪除。

## <a name="next-steps"></a>後續步驟

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](quickstart-remote-monitoring-deploy.md)。
