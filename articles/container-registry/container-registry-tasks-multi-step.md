---
title: 要構建、測試&修補程式映射的多步驟任務
description: 多步驟任務的簡介，Azure 容器註冊表中的 ACR 任務功能，提供基於任務的工作流，用於在雲中構建、測試和修補容器映射。
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399691"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>執行 ACR 工作中的多步驟建置、測試及修補工作

多步驟工作可藉由多步驟、多容器型工作流程，延伸「ACR 工作」的單一映像建置和推送功能。 您可以使用多步驟工作，以循序或平行方式建置和推送數個映像。 然後在單一工作執行內以命令方式執行這些映像。 每個步驟都會定義容器映像建置或推送作業，並且也可以定義容器的執行。 多步驟工作中的每個步驟都會使用某個容器作為其執行環境。

> [!IMPORTANT]
> 如果您先前已在預覽期間使用 `az acr build-task` 命令建立工作，則必須使用 [az acr task][az-acr-task] 命令重新建立這些工作。

例如，您可以執行含有使下列邏輯自動化之步驟的工作：

1. 建置 Web 應用程式映像
1. 執行 Web 應用程式容器
1. 建置 Web 應用程式測試映像
1. 執行會針對執行中應用程式容器執行測試的 Web 應用程式測試容器
1. 如果測試通過，便建置 Helm 圖表封存套件
1. 使用新的 Helm 圖表封存套件來執行 `helm upgrade`

所有步驟都會在 Azure 內執行，這會將工作卸交給 Azure 的計算資源，讓您無須管理基礎結構。 除了您的 Azure 容器登錄之外，您只需支付您所用資源的費用。 如需有關定價的資訊，請參閱 [Azure Container Registry 價格][pricing]中的＜容器組建＞**** 一節。


## <a name="common-task-scenarios"></a>常見的工作案例

多步驟工作可讓您進行諸如下列邏輯的案例：

* 以循序或平行方式建置、標記及推送一或多個容器映像。
* 執行和擷取單元測試及程式碼涵蓋範圍結果。
* 執行和擷取功能測試。 「ACR 工作」支援執行多個容器，並且在其間執行一系列要求。
* 執行工作型執行，包括容器映像的建置前/建置後步驟。
* 使用您喜愛的部署引擎將一或多個容器部署到目標環境。

## <a name="multi-step-task-definition"></a>多步驟工作定義

「ACR 工作」中的多步驟工作會在 YAML 檔案內定義為一系列的步驟。 每個步驟皆可指定是否要依據一或多個先前步驟成功完成才執行。 以下是可用的工作步驟類型：

* [`build`](container-registry-tasks-reference-yaml.md#build)：使用熟悉的`docker build`語法、串聯或並行構建一個或多個容器映射。
* [`push`](container-registry-tasks-reference-yaml.md#push)：將生成的映射推送到容器註冊表。 支援 Azure Container Registry 之類的私人登錄，也支援公用 Docker Hub。
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd)：運行容器，以便它可以在正在運行的任務的上下文中作為函數運行。 您可以將參數傳遞給容器的 `[ENTRYPOINT]`，並指定 env、detach 等屬性及其他熟悉的 `docker run` 參數。 `cmd` 步驟類型可讓您搭配並行容器執行來進行單元和功能測試。

下列程式碼片段說明如何結合這些工作步驟類型。 多步驟的工作有可能很簡單，像是使用如下的 YAML 檔案，從 Dockerfile 建置單一映像並推送至登錄：

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

但也可能較為複雜，例如這個虛構的多步驟定義，其中包含建置、測試、Helm 封裝和 Helm 部署的步驟 (容器登錄和 Helm 存放庫組態未顯示)：

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

有關多個方案，請參閱多步驟任務 YAML 檔和 Dockerfile[的任務示例](container-registry-tasks-samples.md)。

## <a name="run-a-sample-task"></a>執行範例工作

工作支援手動執行 (稱為「快速執行」)，也支援在進行 Git 認可或基底影像更新時自動執行。

若要執行工作，您需先在 YAML 檔案中定義工作的步驟，然後執行 Azure CLI 命令 [az acr run][az-acr-run]。

以下是一個使用範例工作 YAML 檔案來執行工作的範例 Azure CLI 命令。 其步驟會建置映像，然後推送該映像。 請先使用您自己的 Azure 容器登錄名稱更新 `\<acrName\>`，再執行此命令。

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

當您執行此工作時，輸出應該會顯示 YAML 檔案中所定義每個步驟的進度。 在下列輸出中，步驟會顯示為 `acb_step_0` 和 `acb_step_1`。

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

如需有關在進行 Git 認可或基底映像更新時自動執行建置的詳細資訊，請參閱[映像建置](container-registry-tutorial-build-task.md)和[基底映像更新建置](container-registry-tutorial-base-image-update.md)教學課程文章。

## <a name="next-steps"></a>後續步驟

您可以在下列位置找到多步驟工作參考和範例：

* [工作參考](container-registry-tasks-reference-yaml.md) - 工作步驟類型、其屬性及使用方式。
* [任務示例](container-registry-tasks-samples.md)-`task.yaml`多個方案的示例和 Docker 檔，簡單到複雜。
* [Cmd 存放庫](https://github.com/AzureCR/cmd) - 作為 ACR 工作命令的容器集合。

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task