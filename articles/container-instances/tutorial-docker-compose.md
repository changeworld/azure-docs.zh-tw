---
title: 教學課程 - 使用 Docker Compose 部署多容器群組
description: 使用 Docker Compose 來建立和執行多容器應用程式，然後將應用程式帶入 Azure 容器執行個體
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: ''
ms.openlocfilehash: 1e8a5cd856358a0dc3e9c356cb3a55f75db29c86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708253"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>教學課程：使用 Docker Compose 部署多容器群組 

在本教學課程中，您會使用 [Docker Compose](https://docs.docker.com/compose/) 在本機定義和執行多容器應用程式，然後將其部署為 Azure 容器執行個體中的[容器群組](container-instances-container-groups.md)。 

當您使用 Docker 開發雲端原生應用程式，而且想要順暢地從本機開發切換到雲端部署時，請視需要執行 Azure 容器執行個體中的容器。 這項功能是透過 [Docker 與 Azure 之間的整合](https://docs.docker.com/engine/context/aci-integration/)所啟用。 您可以使用原生 Docker 命令在 Azure 中執行[單一容器執行個體](quickstart-docker-cli.md)或多容器群組。

> [!IMPORTANT]
> 並非所有 Azure 容器執行個體的功能都受到支援。 透過在 [Docker ACI 整合](https://github.com/docker/aci-integration-beta) GitHub 存放庫中建立問題，提供有關 Docker-Azure 整合的意見反應。

> [!TIP]
> 您可以使用適用於 Visual Studio Code 的 [Docker 延伸模組](https://aka.ms/VSCodeDocker)，以取得開發、執行和管理容器、映像和內容的整合式體驗。

在本文章中，您將：

> [!div class="checklist"]
> * 建立 Azure Container Registry
> * 從 GitHub 複製應用程式原始程式碼
> * 使用 Docker Compose 來建立映像，並在本機執行多容器應用程式
> * 將應用程式映像推送至容器登錄
> * 建立適用於 Docker 的 Azure 環境
> * 在 Azure 容器執行個體中啟動應用程式

## <a name="prerequisites"></a>Prerequisites

* **Azure CLI** - 您必須在本機電腦上安裝 Azure CLI。 建議使用 2.10.1 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

* **Docker Desktop** -您必須使用適用於 [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) 或 [macOS](https://desktop.docker.com/mac/edge/Docker.dmg) 的 Docker Desktop 2.3.0.5 版或更新版本。 或安裝[適用於 Linux 的 Docker ACI 整合 CLI](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)。

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>取得應用程式程式碼

本教學課程中使用的應用程式的範例是基本投票應用程式。 應用程式是由前端 Web 元件和後端 Redis 執行個體所組成。 Web 元件會封裝至自訂容器映像。 Redis 執行個體會從 Docker Hub 使用未修改的映像。

使用 [git](https://git-scm.com/downloads) 將應用程式的範例複製到您的開發環境：

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

變更為複製的目錄。

```console
cd azure-voting-app-redis
```

目錄內有應用程式原始程式碼及預先建立的 Docker Compose 檔案 docker-compose.yaml。

## <a name="modify-docker-compose-file"></a>修改 Docker Compose 檔案

在文字編輯器中開啟 docker-compose.yaml。 檔案會設定 `azure-vote-back` 和 `azure-vote-front` 服務。

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

在 `azure-vote-front` 設定中，進行下列兩項變更：

1. 更新 `azure-vote-front` 服務中的 `image` 屬性。 在映像名稱前面加上 Azure 容器登錄的登入伺服器名稱 \<acrName\>.azurecr.io。 例如，如果您的登錄名為 myregistry，則登入伺服器名稱會是 myregistry.azurecr.io (全部小寫)，然後 image 屬性會是 `myregistry.azurecr.io/azure-vote-front`。
1. 將 `ports` 對應變更為 `80:80`。 儲存檔案。

更新的檔案應如下所示：

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

進行這些替代後，您在下一個步驟中建立的 `azure-vote-front` 映像會針對您的 Azure 容器登錄進行標記，並可將映像提取到 Azure 容器執行個體中執行。

> [!TIP]
> 在此案例中，您不需要使用 Azure 容器登錄。 例如，您可以選擇 Docker Hub 中的私人存放庫來裝載您的應用程式映像。 如果您選擇不同的登錄，請適當地更新 image 屬性。

## <a name="run-multi-container-application-locally"></a>在本機執行多容器應用程式

執行 [docker-compose up](https://docs.docker.com/compose/reference/up/)，其會使用 `docker-compose.yaml` 檔案範例來建立容器映像、下載 Redis 映像，以及啟動應用程式：

```console
docker-compose up --build -d
```

完成時，使 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令來查看所建立的映像。 已下載或建立三個映像。 `azure-vote-front` 映像包含前端應用程式，其使用 `uwsgi-nginx-flask` 作為基礎映像。 `redis` 映像可用來啟動 Redis 執行個體。

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
redis                                     latest     a1b99da73d05        7 days ago          104MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

執行 [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) 命令可查看執行中的容器：

```
$ docker ps

CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front  "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b68fed4b66b6        redis                                   "docker-entrypoint.s…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

若要查看執行中的應用程式，請在本機網頁瀏覽器中輸入 `http://localhost:80`。 系統會載入範例應用程式，如下列範例所示：

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="投票應用程式的影像":::

嘗試使用本機應用程式之後，請執行 [docker-compose down](https://docs.docker.com/compose/reference/down/) 來停止應用程式並移除容器。

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>將映像推送至容器登錄

若要將應用程式部署至 Azure 容器執行個體，您必須將 `azure-vote-front` 映像推送至容器登錄。 執行 [docker-compose push](https://docs.docker.com/compose/reference/push) 來推送映像：

```console
docker-compose push
```

推送至登錄可能需要幾分鐘的時間。

若要確認映像是否儲存在您的登錄中，請執行 [az acr repository show](/cli/azure/acr/repository#az-acr-repository-show) 命令：

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>將應用程式部署至 Azure 容器執行個體

接下來，變更為 ACI 內容。 後續的 Docker 命令都會在此內容中執行。

```console
docker context use myacicontext
```

執行 `docker compose up` 以在 Azure 容器執行個體中啟動應用程式。 系統會從您的容器登錄中提取 `azure-vote-front` 映像，並在 Azure 容器執行個體中建立容器群組。

```console
docker compose up
```

> [!NOTE]
> ACI 內容中目前可用的 Docker Compose 命令是 `docker compose up` 和 `docker compose down`。 在這些命令中，`docker` 和 `compose` 之間沒有連字號。

容器群組會在短時間內部署完成。 範例輸出：

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

執行 `docker ps`，以查看指派給容器群組的執行中容器和 IP 位址。

```console
docker ps
```

範例輸出：

```
CONTAINER ID                           IMAGE                                    COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    redis                                                        Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                       Running             52.179.23.131:80->80/tcp
```

若要查看雲端中正在執行的應用程式，請在本機網頁瀏覽器中輸入顯示的 IP 位址。 在此範例中，請輸入 `52.179.23.131`。 系統會載入範例應用程式，如下列範例所示：

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="投票應用程式的影像":::

若要查看前端容器的記錄，請執行 [docker logs](https://docs.docker.com/engine/reference/commandline/logs) 命令。 例如：

```console
docker logs azurevotingappredis_azure-vote-front
```

您也可以使用 Azure 入口網站或其他 Azure 工具，查看您所部署容器群組的屬性和狀態。

當您完成應用程式的試用時，請使用 `docker compose down` 來停止應用程式和容器：

```console
docker compose down
```

此命令會刪除 Azure 容器執行個體中的容器群組。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Docker Compose 將本機執行的多容器應用程式切換為在 Azure 容器執行個體中執行。 您已了解如何︰

> [!div class="checklist"]
> * 建立 Azure Container Registry
> * 從 GitHub 複製應用程式原始程式碼
> * 使用 Docker Compose 來建立映像，並在本機執行多容器應用程式
> * 將應用程式映像推送至容器登錄
> * 建立適用於 Docker 的 Azure 環境
> * 在 Azure 容器執行個體中啟動應用程式

您也可以使用適用於 Visual Studio Code 的 [Docker 延伸模組](https://aka.ms/VSCodeDocker)，以取得開發、執行和管理容器、映像和內容的整合式體驗。

如果您想要利用 Azure 容器執行個體中的更多功能，請使用 Azure 工具來指定多容器群組。 例如，請參閱教學課程，使用 Azure CLI 搭配 [YAML 檔案](container-instances-multi-container-yaml.md)來部署容器群組，或使用 [Azure Resource Manager 範本](container-instances-multi-container-group.md)進行部署。 