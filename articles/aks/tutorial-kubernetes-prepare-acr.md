---
title: Azure 上的 Kubernertes 教學課程 - 建立容器登錄
description: 在本 Azure Kubernetes Service (AKS) 教學課程中，您會建立 Azure Container Registry 執行個體，並上傳範例應用程式容器映像。
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d1dce1c59c4bf40eaead89e4a8a088e9a8ea4f76
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250616"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>教學課程：部署和使用 Azure Container Registry

Azure Container Registry (ACR) 是適用於容器映像的私人登錄。 私人容器登錄可讓您安全地建置及部署應用程式和自訂程式碼。 在本教學課程 (2/7 部分) 中，您將部署 ACR 執行個體，並將容器映像推送至該處。 您會了解如何：

> [!div class="checklist"]
> * 建立 Azure Container Registry (ACR) 執行個體
> * 標記 ACR 的容器映像
> * 將映像上傳至 ACR
> * 檢視登錄中的映像

在稍後的教學課程中，此 ACR 實例會與 AKS 中的 Kubernetes 叢集整合，並從映射部署應用程式。

## <a name="before-you-begin"></a>開始之前

在[上一個教學課程][aks-tutorial-prepare-app]中，已針對簡單的 Azure Voting 應用程式建立容器映像。 如果您尚未建立 Azure Voting 應用程式映像，請回到[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]。

在本教學課程中，您必須執行 Azure CLI 2.0.53 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

若要建立 Azure Container Registry，您必須已有資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create][az-group-create] 命令來建立資源群組。 在下列範例中，會在 eastus 區域中建立名為 myResourceGroup 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az acr create][az-acr-create] 命令建立 Azure Container Registry 執行個體，並提供您自己的登錄名稱。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 在本教程的后续部分，會使用 `<acrName>` 作為容器登錄名稱的預留位置。 提供您自己唯一的登錄名稱。 *基本* SKU 對開發用途而言是最符合成本效益的進入點，可在儲存體和輸送量之間取得平衡。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>登入容器登錄

若要使用 ACR 執行個體，您必須先登入。 請使用 [az acr login][az-acr-login] 命令，並提供在先前的步驟中指定給容器登錄的唯一名稱。

```azurecli
az acr login --name <acrName>
```

此命令在完成之後會傳回 *登入成功* 訊息。

## <a name="tag-a-container-image"></a>標記容器映像

若要查看目前的本機映像清單，請使用 [docker images][docker-images] 命令：

```console
$ docker images
```
上述命令的輸出會顯示您目前的本機映射清單：

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

若要將 *azure-vote-front* 容器映像用於 ACR，該映像必須標記登錄的登入伺服器位址。 將容器映像推送到映像登錄時，此標籤可用於路由傳送。

若要取得登入伺服器位址，請使用 [az acr list][az-acr-list] 命令並查詢 *loginServer*，如下所示：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

現在，請以容器登錄的 *acrLoginServer* 位址標記您的本機 *azure-vote-front* 映像。 若要指出映像版本，請在映像名稱結尾處加上 *:v1*：

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

若要確認標記是否已套用，請再次執行 [docker images][docker-images]。

```azurecli
$ docker images
```

映像會加上 ACR 執行個體位址和版本號碼的標記。

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>將映像推送到登錄

利用您已建置並標記的映像，將 *azure-vote-front* 映像推送至您的 ACR 執行個體。 請使用 [docker push][docker-push]，並提供您自己的 *acrLoginServer* 位址作為映像名稱，如下所示：

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

將映像推送至 ACR 的作業可能需要幾分鐘才會完成。

## <a name="list-images-in-registry"></a>列出登錄中的映像

若要傳回已推送至 ACR 執行個體的映像清單，請使用 [az acr repository list][az-acr-repository-list] 命令。 請提供您自己的 `<acrName>`，如下所示：

```azurecli
az acr repository list --name <acrName> --output table
```

下列範例輸出會將 *azure-vote-front* 映像列為登錄中的可用映像：

```output
Result
----------------
azure-vote-front
```

若要查看特定映像的標記，請使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令，如下所示：

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

下列範例輸出會顯示在先前的步驟中標記的 *v1* 映像：

```output
Result
--------
v1
```

現在，您已有儲存在私人 Azure Container Registry 執行個體中的容器映像。 此映像會在下一個教學課程中，從 ACR 部署至 Kubernetes 叢集。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 Azure Container Registry，並推送了可在 AKS 叢集中使用的映像。 您已了解如何︰

> [!div class="checklist"]
> * 建立 Azure Container Registry (ACR) 執行個體
> * 標記 ACR 的容器映像
> * 將映像上傳至 ACR
> * 檢視登錄中的映像

請繼續進行下一個教學課程，了解如何在 Azure 中部署 Kubernetes 叢集。

> [!div class="nextstepaction"]
> [部署 Kubernetes 叢集][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
