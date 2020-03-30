---
title: 使用雲本機構建包構建映射
description: 使用 az acr pack 生成命令從應用生成容器映射，然後推送到 Azure 容器註冊表，而無需使用 Dockerfile。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087077"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>使用雲本機構建包從應用生成和推送圖像

Azure CLI`az acr pack build`命令使用[`pack`](https://github.com/buildpack/pack)來自[Buildpack](https://buildpacks.io/)的 CLI 工具來構建應用並將其映射推送到 Azure 容器註冊表中。 此功能提供了一個選項，用於從 Node.js、JAVA 和其他語言中的應用程式原始程式碼快速構建容器映射，而無需定義 Dockerfile。

可以使用 Azure 雲外殼或 Azure CLI 的本地安裝來運行本文中的示例。 如果您想在本地使用它，則需要版本 2.0.70 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="use-the-build-command"></a>使用生成命令

要使用雲本機構建包構建和推送容器映射，請使用[az acr 包生成][az-acr-pack-build]命令。 az [acr 生成][az-acr-build]命令從 Dockerfile 源和相關代碼生成和推送圖像，`az acr pack build`而您直接指定應用程式源樹。

至少，在運行`az acr pack build`： 時指定以下內容：

* 運行命令的 Azure 容器註冊表
* 生成的圖像的圖像名稱和標記
* ACR 任務（如本地目錄、GitHub 存儲庫或遠端 tarball）[支援的上下文位置](container-registry-tasks-overview.md#context-locations)之一
* 適合您的應用程式的構建器產生器映射的名稱。 Azure 容器註冊表緩存產生器映射，例如`cloudfoundry/cnb:0.0.34-cflinuxfs3`為了加快生成速度。  

`az acr pack build`支援 ACR Task 命令的其他功能，包括流式處理並保存以供以後檢索[的運行變數](container-registry-tasks-reference-yaml.md#run-variables)和[任務運行日誌](container-registry-tasks-logs.md)。

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>示例：使用雲鑄造產生器構建 Node.js 映射

下面的示例使用`cloudfoundry/cnb:0.0.34-cflinuxfs3`產生器在[Azure-示例/nodejs-doc-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world)樣裡庫中從 Node.js 應用生成容器映射。 此產生器由 Azure 容器註冊表緩存，因此不需要`--pull`參數：

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

本示例使用`1.0`標記`node-app`生成映射，並將其推送到*myREGISTRY*容器註冊表。 在此示例中，目標注冊表名稱顯式預置到映射名稱。 如果未指定，註冊表登錄伺服器名稱將自動預告到映射名稱。

命令輸出顯示映射的生成和推送進度。 

成功生成映射後，如果安裝了 Docker，則可以使用 Docker 運行映射。 首次登錄註冊表：

```azurecli
az acr login --name myregistry
```

運行映射：

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

流覽到`localhost:1337`您最喜愛的瀏覽器以查看示例 Web 應用。 按`[Ctrl]+[C]`以停止容器。

## <a name="example-build-java-image-with-heroku-builder"></a>示例：使用 Heroku 產生器構建 JAVA 映射

下面的示例使用`heroku/buildpacks:18`產生器在[buildpack/示例 java 應用](https://github.com/buildpack/sample-java-app)回購中從 JAVA 應用生成容器映射。 參數`--pull`指定命令應拉最新的產生器映射。 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

本示例生成使用`java-app`命令的運行 ID 標記的映射，並將其推送到*myREGISTRY*容器註冊表。

命令輸出顯示映射的生成和推送進度。 

成功生成映射後，如果安裝了 Docker，則可以使用 Docker 運行映射。 首次登錄註冊表：

```azurecli
az acr login --name myregistry
```

運行圖像，將圖像標記替換為*runid*：

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

流覽到`localhost:8080`您最喜愛的瀏覽器以查看示例 Web 應用。 按`[Ctrl]+[C]`以停止容器。


## <a name="next-steps"></a>後續步驟

使用`az acr pack build`生成和推送容器映射後，可以像將任何映射一樣部署到您選擇的目標。 Azure 部署選項包括在[應用服務](../app-service/containers/tutorial-custom-docker-image.md)或[Azure 庫伯奈斯服務](../aks/tutorial-kubernetes-deploy-cluster.md)中運行它，等等。

有關 ACR 任務功能的詳細資訊，請參閱[使用 ACR 任務自動生成和維護容器映射](container-registry-tasks-overview.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
