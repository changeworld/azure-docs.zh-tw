---
title: 教學課程 - 準備容器登錄以部署映像
description: Azure 容器執行個體教學課程第 2 部分 (共 3 部分) - 準備 Azure Container Registry 並推送映像
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 44a7f21c067897b046413851ef5a2c73bfccc24f
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708031"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>教學課程：建立 Azure Container Registry 並推送容器映像

這是三段式教學課程的第二段。 本教學課程的[第一部分](container-instances-tutorial-prepare-app.md)已建立 Node.js Web 應用程式的 Docker 容器映像。 在本教學課程中，您會將此映像推送至 Azure Container Registry。 如果您尚未建立容器映像，請回到[教學課程 1 – 建立容器映像](container-instances-tutorial-prepare-app.md)。

Azure Container Registry 是 Azure 中的私人 Docker 登錄。 在本教學課程 (系列的第二部分) 中，您會：

> [!div class="checklist"]
> * 使用 Azure CLI 建立 Azure Container Registry 執行個體
> * 標記 Azure Container Registry 的容器映像
> * 將映像上傳至您的登錄

在下一篇文章 (本系列的最後一部分) 中，您會從私人登錄將容器部署至 Azure Container Instances。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>標記容器映像

若要將容器映像推送到私人登錄 (像是 Azure Container Registry)，您必須為此映像標記登錄的登入伺服器完整名稱。

首先，取得 Azure Container Registry 的完整登入伺服器名稱。 執行下列 [az acr show][az-acr-show] 命令，並以您剛建立的登錄名稱取代 `<acrName>`：

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

例如，如果您的登錄名稱為 mycontainerregistry082**：

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

現在，使用 [docker images][docker-images] 命令顯示您的本機映像清單：

```bash
docker images
```

除了電腦上的任何其他映像，您應會看到在[上一個教學課程](container-instances-tutorial-prepare-app.md)中建立的 aci-tutorial-app** 映像：

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

以您容器登錄的登入伺服器標記 *aci-tutorial-app*映像。 此外，將 `:v1` 標記新增至映像名稱尾端以表示映像版本號碼。 以您稍早執行 [az acr show][az-acr-show] 命令的結果取代 `<acrLoginServer>`。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

再次執行 `docker images` 來驗證標記作業：

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>將映像推送至 Azure Container Registry

您已使用私人登錄的完整登入伺服器名稱標記 aci-tutorial-app** 映像，接下來您可以使用 [docker push][docker-push] 命令將映像推送至登錄。 以您在稍早步驟中取得的完整登入伺服器名稱取代 `<acrLoginServer>`。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

視您的網際網路連線而定，`push` 作業應該會花費幾秒鐘到幾分鐘的時間，且輸出會與以下類似：

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>在 Azure Container Registry 中列出映像

若要確認剛推送的映像確實在您的 Azure Container Registry 中，請使用 [az acr repository list][az-acr-repository-list] 命令列出您登錄中的映像。 以您的容器登錄名稱取代 `<acrName>`。

```azurecli
az acr repository list --name <acrName> --output table
```

例如：

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

若要查看特定映像的「標籤」**，請使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

您應該會看到如下輸出：

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>下一步

在本教學課程中，您準備了 Azure Container Registry 與 Azure Container Instances 搭配使用，並已將容器映像推送至登錄。 已完成下列步驟：

> [!div class="checklist"]
> * 使用 Azure CLI 建立 Azure Container Registry 執行個體
> * 標記 Azure Container Registry 的容器映像
> * 將映像上傳至 Azure Container Registry

進入下一個教學課程，了解如何使用 Azure Container Instances 將容器部署至 Azure：

> [!div class="nextstepaction"]
> [將容器部署至 Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
