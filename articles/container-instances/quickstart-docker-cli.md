---
title: 快速入門 - 將 Docker 容器部署至容器執行個體 - Docker CLI
description: 在本快速入門中，您可以使用 Docker CLI 快速地部署在隔離式 Azure 容器執行個體中執行的容器化的 Web 應用程式
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262309"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>快速入門：使用 Docker CLI 部署容器執行個體

使用 Azure 容器執行個體在 Azure 中簡潔且快速地執行無伺服器 Docker 容器。 當您開發雲端原生應用程式，而且想要順暢地從本機開發切換到雲端部署時，請視需要部署至容器執行個體。

在本快速入門中，您會使用原生 Docker CLI 命令來部署 Docker 容器，並讓其應用程式可在 Azure 容器執行個體中使用。 這項功能是透過 [Docker 與 Azure 之間的整合](https://docs.docker.com/engine/context/aci-integration/)所啟用。 執行 `docker run` 命令的幾秒之後，您可以瀏覽至容器中執行的應用程式：

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式":::

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][azure-account]。

針對本快速入門，您需要適用於 [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) 或 [macOS](https://desktop.docker.com/mac/edge/Docker.dmg) 的 Docker Desktop 2.3.0.5 版或更新版本。 或安裝[適用於 Linux 的 Docker ACI 整合 CLI](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)。 

> [!IMPORTANT]
> 並非所有 Azure 容器執行個體的功能都受到支援。 透過在 [aci-integration-beta](https://github.com/docker/aci-integration-beta) GitHub 存放庫中建立問題，提供有關 Docker-Azure 整合的意見反應。

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>建立容器

建立 Docker 內容之後，您可以在 Azure 中建立容器。 在此快速入門中，您可以使用公用 `mcr.microsoft.com/azuredocs/aci-helloworld` 映像。 此映像會封裝以 Node.js 撰寫並提供靜態 HTML 網頁的小型 Web 應用程式。

首先，變更為 ACI 內容。 所有後續的 Docker 命令都會在此內容中執行。

```
docker context use myacicontext
```

執行下列 `docker run` 命令來建立 Azure 容器執行個體，並將連接埠 80 公開至網際網路：

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

成功部署的範例輸出：

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

執行 `docker ps` 以取得執行中容器的詳細資料，包括公用 IP 位址：

```
docker ps
```


範例輸出會顯示公用 IP 位址，在此案例中是 52.230.225.232：

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 現在請於瀏覽器中移至該 IP 位址。 如果您看到如下的網頁，恭喜您！ 您已將 Docker 容器中執行的應用程式成功部署至 Azure。

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式":::

## <a name="pull-the-container-logs"></a>提取容器記錄

如果您要對容器或其執行的應用程式進行疑難排解 (或只是要檢視其輸出)，請先檢視容器執行個體的記錄。

例如，執行 `docker logs` 命令以查看 ACI 內容中 hungry-kirch 容器的記錄：

```azurecli-interactive
docker logs hungry-kirch
```

輸出會顯示容器的記錄，且應該會顯示您在瀏覽器中檢視應用程式時產生的 HTTP GET 要求。

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>清除資源

當您完成容器的作業時，請執行 `docker rm` 以將其移除。 此命令會停止並刪除 Azure 容器執行個體。

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>後續步驟

在本快速入門中，您已透過使用 Docker 與 Azure 之間的整合，從公用映像建立 Azure 容器執行個體。 若要深入了解整合案例，請參閱 [Docker 文件](https://docs.docker.com/engine/context/aci-integration/)。 

您也可以使用適用於 Visual Studio Code 的 [Docker 延伸模組](https://aka.ms/VSCodeDocker)，以取得開發、執行和管理容器、映像和內容的整合式體驗。

若要使用 Azure 工具來建立和管理容器執行個體，請參閱使用 [Azure CLI](container-instances-quickstart.md)、[Azure PowerShell](container-instances-quickstart-powershell.md)、[Azure 入口網站](container-instances-quickstart-portal.md)和 [Azure Resource Manager 範本](container-instances-quickstart-template.md)的其他快速入門。

如果您想要使用 Docker Compose 在本機定義和執行多容器應用程式，然後切換至 Azure 容器執行個體，請繼續完成教學課程。

> [!div class="nextstepaction"]
> [Docker Compose 教學課程](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

