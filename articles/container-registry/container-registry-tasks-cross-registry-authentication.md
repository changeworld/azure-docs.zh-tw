---
title: 來自 ACR 任務的跨註冊表身份驗證
description: 配置 Azure 容器註冊表任務 （ACR 任務）以使用 Azure 資源的託管標識訪問另一個專用 Azure 容器註冊表
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842483"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>使用 Azure 託管標識的 ACR 任務中的跨註冊表身份驗證 

在[ACR 任務中](container-registry-tasks-overview.md)，可以為[Azure 資源啟用託管標識](container-registry-tasks-authentication-managed-identity.md)。 任務可以使用標識訪問其他 Azure 資源，而無需提供或管理認證。 

在本文中，您將瞭解如何在任務中啟用託管標識，以便從不同于用於運行該任務的註冊表中提取映射。

要創建 Azure 資源，本文要求運行 Azure CLI 版本 2.0.68 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>案例概觀

示例任務從另一個 Azure 容器註冊表中拉出基本映射以生成和推送應用程式映射。 要提取基本映射，請使用託管標識配置任務，並為其分配適當的許可權。 

此示例顯示使用使用者分配或系統分配的託管標識的步驟。 您選擇的身份取決於組織的需求。

在實際方案中，組織可能會維護所有開發團隊用於構建其應用程式的基本映射集。 這些基本映射存儲在公司註冊表中，每個開發團隊僅具有拉取許可權。 

## <a name="prerequisites"></a>Prerequisites

對於本文，您需要兩個 Azure 容器註冊表：

* 您可以使用第一個註冊表創建和執行 ACR 任務。 在本文中，此註冊表名為*myregistry*。 
* 第二個註冊表承載用於生成映射的任務的基本映射。 在本文中，第二個註冊表命名為*mybase 註冊表*。 

在後面的步驟中，用您自己的登錄機碼替換。

如果還沒有所需的 Azure 容器註冊表，請參閱[快速入門：使用 Azure CLI 創建專用容器註冊表](container-registry-get-started-azure-cli.md)。 您還不需要將映射推送到註冊表。

## <a name="prepare-base-registry"></a>準備基本註冊表

首先，創建一個工作目錄，然後創建一個名為 Dockerfile 的檔，包含以下內容。 此簡單示例從 Docker Hub 中的公共映射生成 Node.js 基本映射。
    
```bash
echo FROM node:9-alpine > Dockerfile
```
在目前的目錄中，運行 az [acr 生成][az-acr-build]命令以生成基映射並將基礎映射推送到基本註冊表。 實際上，組織中的另一個團隊或進程可能會維護基本註冊表。
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>在 YAML 檔中定義任務步驟

此示例[多步驟任務](container-registry-tasks-multi-step.md)的步驟在[YAML 檔中](container-registry-tasks-reference-yaml.md)定義。 在本地工作目錄中`helloworldtask.yaml`創建名為的檔並粘貼以下內容。 使用基本註冊表的`REGISTRY_NAME`伺服器名稱更新生成步驟中的值。

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

生成步驟使用`Dockerfile-app`[Azure-示例/acr 生成-helloworld 節點](https://github.com/Azure-Samples/acr-build-helloworld-node.git)回購中的檔來生成映射。 引用`--build-arg`基本註冊表來拉取基本映射。 成功生成後，映射將推送到用於運行任務的註冊表。

## <a name="option-1-create-task-with-user-assigned-identity"></a>選項 1：使用使用者分配的標識創建任務

本節中的步驟創建任務並啟用使用者分配的標識。 如果要改為啟用系統分配的標識，請參閱[選項 2：使用系統分配的標識創建任務](#option-2-create-task-with-system-assigned-identity)。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>建立工作

通過執行以下[az acr 任務創建命令創建][az-acr-task-create]任務*helloworld 任務*。 任務在沒有原始程式碼上下文的情況下運行，命令引用工作目錄中的檔`helloworldtask.yaml`。 參數`--assign-identity`傳遞使用者分配的標識的資源識別碼。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>選項 2：使用系統分配的標識創建任務

本節中的步驟創建任務並啟用系統分配的標識。 如果要改為啟用使用者分配的標識，請參閱[選項 1：使用使用者分配的身份創建任務](#option-1-create-task-with-user-assigned-identity)。 

### <a name="create-task"></a>建立工作

通過執行以下[az acr 任務創建命令創建][az-acr-task-create]任務*helloworld 任務*。 任務在沒有原始程式碼上下文的情況下運行，命令引用工作目錄中的檔`helloworldtask.yaml`。 沒有`--assign-identity`值的參數支援任務上的系統分配的標識。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>向基本註冊表授予標識拉取許可權

在本節中，授予從基本註冊表 *"mybase註冊表*"中提取的託管標識許可權。

使用[az acr show][az-acr-show]命令獲取基本註冊表的資源識別碼 並將其存儲在變數中：

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

使用[az 角色指派創建][az-role-assignment-create]命令將`acrpull`角色標識分配給基本註冊表。 此角色僅具有從註冊表中提取圖像的許可權。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>將目標注冊表憑據添加到任務

現在使用[az acr 任務憑據添加][az-acr-task-credential-add]命令使任務能夠使用標識的憑據對基本註冊表進行身份驗證。 運行與任務中啟用的託管標識類型對應的命令。 如果啟用了使用者分配的標識，則使用標識`--use-identity`的用戶端 ID 傳遞。 如果啟用了系統分配的標識，則傳遞`--use-identity [system]`。

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>手動運行任務

要驗證啟用託管標識的任務是否成功運行，請使用[az acr 任務運行][az-acr-task-run]命令手動觸發該任務。 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

如果任務成功運行，則輸出類似于：

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

運行[az acr 存儲庫顯示標記][az-acr-repository-show-tags]命令，以驗證映射是否生成並已成功推送到*我的註冊表*：

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

範例輸出︰

```console
cf10
```

## <a name="next-steps"></a>後續步驟

* 詳細瞭解在[ACR 任務中啟用託管標識](container-registry-tasks-authentication-managed-identity.md)。
* 請參閱[ACR 任務 YAML 參考](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
