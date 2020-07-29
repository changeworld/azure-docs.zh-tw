---
title: 使用雲端原生 Buildpack 建立映射
description: 使用 az acr pack build 命令，從應用程式建立容器映射並推送至 Azure Container Registry，而不需要使用 Dockerfile。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79087077"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>使用雲端原生 Buildpack 從應用程式建立及推送映射

Azure CLI 命令會 `az acr pack build` 使用 Buildpacks 中的 [`pack`](https://github.com/buildpack/pack) CLI 工具[Buildpacks](https://buildpacks.io/)來建立應用程式，並將其映射推送至 Azure container registry。 這項功能可讓您從應用程式原始程式碼快速地建立容器映射，Node.js、JAVA 和其他語言，而不需要定義 Dockerfile。

您可以使用 Azure Cloud Shell 或本機安裝的 Azure CLI 來執行本文中的範例。 如果您想要在本機使用，則需要2.0.70 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="use-the-build-command"></a>使用 build 命令

若要使用雲端原生 Buildpacks 來建立及推送容器映射，請執行[az acr pack build][az-acr-pack-build]命令。 [Az acr build][az-acr-build]命令會從 Dockerfile 來源和相關程式碼建立及推送映射，而 `az acr pack build` 您可以直接指定應用程式來源樹狀結構。

當您執行 `az acr pack build` 時，請至少指定下列內容：

* 您在其中執行命令的 Azure container registry
* 產生之影像的映射名稱和標記
* ACR 工作的其中一個[支援內容位置](container-registry-tasks-overview.md#context-locations)，例如本機目錄、GitHub 存放庫或遠端 tarball
* 適用于您應用程式的 Buildpack 產生器映射名稱。 Azure Container Registry 快取產生器映射，例如 `cloudfoundry/cnb:0.0.34-cflinuxfs3` ，以加快組建的速度。  

`az acr pack build` 支援 ACR 工作命令的其他功能，包括 [Run 變數](container-registry-tasks-reference-yaml.md#run-variables)和[工作執行記錄](container-registry-tasks-logs.md)，這些會進行資料串流處理，並儲存以供日後擷取。

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>範例：使用 Cloud Foundry builder 建立 Node.js 影像

下列範例會使用產生器，從[Azure 範例/nodejs 檔-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world)存放庫中的 Node.js 應用程式建立容器映射 `cloudfoundry/cnb:0.0.34-cflinuxfs3` 。 此產生器是由 Azure Container Registry 快取，因此 `--pull` 不需要參數：

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

此範例會建立 `node-app` 具有標記的映射 `1.0` ，並將其推送至*myregistry*容器登錄。 在此範例中，目標登錄名稱會明確地加到映射名稱前面。 如果未指定，登錄登入伺服器名稱會自動加在映射名稱前面。

命令輸出會顯示建立和推送映射的進度。 

成功建立映射之後，您可以使用 Docker 執行它（如果已安裝）。 第一次登入您的登錄：

```azurecli
az acr login --name myregistry
```

執行映射：

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

`localhost:1337`在您慣用的瀏覽器中流覽至，以查看範例 web 應用程式。 按 `[Ctrl]+[C]` 以停止容器。

## <a name="example-build-java-image-with-heroku-builder"></a>範例：使用 Heroku builder 建立 JAVA 映射

下列範例會使用產生器，從[buildpack/sample-java 應用](https://github.com/buildpack/sample-java-app)程式存放庫中的 java 應用程式建立容器映射 `heroku/buildpacks:18` 。 `--pull`參數會指定命令應提取最新的產生器映射。 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

此範例會建立以 `java-app` 命令的執行識別碼標記的映射，並將其推送至*myregistry*容器登錄。

命令輸出會顯示建立和推送映射的進度。 

成功建立映射之後，您可以使用 Docker 執行它（如果已安裝）。 第一次登入您的登錄：

```azurecli
az acr login --name myregistry
```

執行映射，以*runid*的映射標記取代：

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

`localhost:8080`在您慣用的瀏覽器中流覽至，以查看範例 web 應用程式。 按 `[Ctrl]+[C]` 以停止容器。


## <a name="next-steps"></a>後續步驟

當您使用建立並推送容器映射之後 `az acr pack build` ，就可以將它部署到您選擇的目標，如同任何映射。 Azure 部署選項包括在[App Service](../app-service/containers/tutorial-custom-docker-image.md)或[Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md)之間執行，以及其他專案。

如需 ACR 工作功能的詳細資訊，請參閱[使用 ACR 工作自動化容器映射組建和維護](container-registry-tasks-overview.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
