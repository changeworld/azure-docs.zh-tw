---
title: Azure 上的 Kubernetes 教學課程 - 準備應用程式
description: 在本 Azure Kubernetes Service (AKS) 教學課程中，您將了解如何透過 Docker Compose 來準備和建置後續可部署至 AKS 的多容器應用程式。
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: 349bf90ea0b344d5232c885358814f39fba4c19f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251944"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>教學課程：準備 Azure Kubernetes Service (AKS) 的應用程式

在本教學課程 (七個章節的第一部分) 中，多容器應用程式已準備好用於 Kubernetes。 使用現有的開發工具 (例如 Docker Compose) 在本機建置和測試應用程式。 您會了解如何：

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源的範例
> * 從範例應用程式來源建立容器映像
> * 在本機 Docker 環境中測試多容器應用程式

完成後，就可在本機開發環境中執行下列應用程式：

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="螢幕擷取畫面，顯示在本機網頁瀏覽器中以本機方式開啟的容器映射 Azure 投票應用程式" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

在稍後的教學課程中，會將容器映射上傳至 Azure Container Registry，然後部署到 AKS 叢集中。

## <a name="before-you-begin"></a>開始之前

本教學課程假設使用者對核心 Docker 概念有基本認識，例如容器、容器映像和 `docker` 命令。 如需容器基本概念的入門參考資料，請參閱[開始使用 Docker][docker-get-started]。

若要完成此教學課程，您需要一個執行 Linux 容器的本機 Docker 開發環境。 Docker 提供可在 [Mac][docker-for-mac]、[Windows][docker-for-windows] 或 [Linux][docker-for-linux] 系統上設定 Docker 的套件。

> [!NOTE]
> Azure Cloud Shell 不含完成這些教學課程中各個步驟所需的 Docker 元件。 因此，我們建議使用完整的 Docker 開發環境。

## <a name="get-application-code"></a>取得應用程式程式碼

本教學課程中使用的 [範例應用程式][sample-application] 是由前端 web 元件和後端 Redis 實例所組成的基本投票應用程式。 Web 元件會封裝至自訂容器映像。 Redis 執行個體會從 Docker Hub 使用未修改的映像。

使用 [git][] 將應用程式的範例複製到您的開發環境：

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

變更為複製的目錄。

```console
cd azure-voting-app-redis
```

目錄內有應用程式原始程式碼、預先建立的 Docker Compose 檔案和 Kubernetes 資訊清單檔。 整套教學課程都會使用到這些檔案。 目錄的內容和結構如下所示：

```output
azure-voting-app-redis
│   azure-vote-all-in-one-redis.yaml
│   docker-compose.yaml
│   LICENSE
│   README.md
│
├───azure-vote
│   │   app_init.supervisord.conf
│   │   Dockerfile
│   │   Dockerfile-for-app-service
│   │   sshd_config
│   │
│   └───azure-vote
│       │   config_file.cfg
│       │   main.py
│       │
│       ├───static
│       │       default.css
│       │
│       └───templates
│               index.html
│
└───jenkins-tutorial
        config-jenkins.sh
        deploy-jenkins-vm.sh
```

## <a name="create-container-images"></a>建立容器映像

[Docker Compose][docker-compose] 可用來自動建置容器映像和部署多容器應用程式。

使用範例 `docker-compose.yaml` 檔案可建立容器映像、下載 Redis 映像，以及啟動應用程式：

```console
docker-compose up -d
```

完成時，使 [docker images][docker-images] 命令來查看所建立的映像。 已下載或建立三個映像。 *azure-vote-front* 映像包含前端應用程式，並使用 *nginx-flask* 映像作為基礎映像。 *redis* 映像可用來啟動 Redis 執行個體。

```
$ docker images

REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        9 seconds ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

執行 [docker ps][docker-ps] 命令可查看執行中的容器：

```
$ docker ps

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS                           NAMES
d10e5244f237        mcr.microsoft.com/azuredocs/azure-vote-front:v1   "/entrypoint.sh /sta…"   3 minutes ago       Up 3 minutes        443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
21574cb38c1f        mcr.microsoft.com/oss/bitnami/redis:6.0.8         "/opt/bitnami/script…"   3 minutes ago       Up 3 minutes        0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>在本機測試應用程式

若要查看執行中的應用程式，請在本機網頁瀏覽器中輸入 `http://localhost:8080`。 系統會載入範例應用程式，如下列範例所示：

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="螢幕擷取畫面，顯示在本機網頁瀏覽器中以本機方式開啟的容器映射 Azure 投票應用程式" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

## <a name="clean-up-resources"></a>清除資源

應用程式的功能已完成驗證，因此可以停止並移除執行中的容器。 ***請勿刪除容器映射** _-在下一個教學課程中，_azure 投票-front * 影像會上傳至 Azure Container Registry 實例。

使用 [docker-compose down][docker-compose-down] 命令停止並移除容器執行個體和資源：

```console
docker-compose down
```

本機應用程式已移除後，您會有包含 Azure 投票應用程式的 Docker 映像 azure-vote-front  ，可供下一個教學課程使用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，應用程式已經過測試並已建立應用程式的容器映像。 您已了解如何︰

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源的範例
> * 從範例應用程式來源建立容器映像
> * 在本機 Docker 環境中測試多容器應用程式

繼續進行下一個教學課程，以了解如何在 Azure Container Registry 中儲存容器映像。

> [!div class="nextstepaction"]
> [將映像推送至 Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads
[sample-application]: https://github.com/Azure-Samples/azure-voting-app-redis

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
