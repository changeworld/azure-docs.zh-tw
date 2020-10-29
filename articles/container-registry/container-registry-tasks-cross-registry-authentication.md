---
title: 從 ACR 工作進行跨登錄驗證
description: 使用 Azure 資源的受控識別來設定 Azure Container Registry 工作 (ACR 工作)，以存取另一個私人 Azure 容器登錄
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 9a460102eafa5c1eda2f37330887d985387d5df5
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026253"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>在 ACR 工作中使用 Azure 受控識別進行跨登錄驗證 

在 [ACR 工作](container-registry-tasks-overview.md)中，您可以[啟用 Azure 資源的受控識別](container-registry-tasks-authentication-managed-identity.md)。 工作可以使用身分識別存取其他 Azure 資源，不需要提供或管理認證。 

在本文中，您將了解如何在工作中啟用受控識別，從不同於用來執行工作的登錄中提取映像。

若要建立 Azure，本文需要您執行 Azure CLI 2.0.68 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>案例概觀

範例工作會從另一個 Azure 容器登錄提取基礎映像，以建置及推送應用程式映像。 若要提取基礎映像，您可以使用受控識別來設定工作，並為其指派適當的權限。 

這個範例會示範使用使用者指派或系統指派受控識別的步驟。 您所選擇的身分識別取決於貴組織的需求。

在真實世界案例中，組織可能會維護一組可供所有開發小組用來建置其應用程式的基礎映像。 這些基礎映像會儲存在公司登錄中，每個開發小組僅具有「提取」權限。 

## <a name="prerequisites"></a>必要條件

在本文中，您需要兩個 Azure 容器登錄：

* 您可以使用第一個登錄來建立及執行 ACR 工作。 在本文中，此登錄名為 myregistry。 
* 第二個登錄會裝載用來建置映像的基礎映像。 在本文中，第二個登錄名為 mybaseregistry。 

請在稍後的步驟中，以您自己的登錄名稱取代。

如果您還沒有所需的 Azure 容器登錄，請參閱[快速入門：使用 Azure CLI 建立私人容器登錄](container-registry-get-started-azure-cli.md)。 您還不需要將映像推送至登錄。

## <a name="prepare-base-registry"></a>準備基礎登錄

基於示範目的，請執行 [az acr import] [az-acr-import]，將公用 Node.js 映射從 Docker Hub 匯入至您的基底登錄。 在實務上，組織中的另一個小組或流程可能會在基底登錄中維護映射。

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:9-alpine \
  --image baseimages/node:9-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>定義 YAML 檔案中的工作步驟

[多步驟工作](container-registry-tasks-multi-step.md)這個範例的步驟是在 [YAML 檔案](container-registry-tasks-reference-yaml.md)中進行定義。 在您的本機工作目錄中建立名為 `helloworldtask.yaml` 的檔案，並貼上下列內容。 使用基礎登錄的伺服器名稱，更新組建步驟中的 `REGISTRY_NAME` 值。

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

組建步驟會使用 [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) 存放庫中的 `Dockerfile-app` 檔案來建置映像。 `--build-arg` 會參考基礎登錄來提取基礎映像。 成功建置後，會將映像推送至用來執行工作的登錄。

## <a name="option-1-create-task-with-user-assigned-identity"></a>選項 1：使用使用者指派的身分識別來建立工作

本節中的步驟可建立工作，並啟用使用者指派的身分識別。 如果您想要改為啟用系統指派的身分識別，請參閱[選項 2：使用系統指派的身分識別來建立工作](#option-2-create-task-with-system-assigned-identity)。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>建立工作

執行下列 [az acr task create][az-acr-task-create] 命令來建立 helloworldtask 工作。 此工作會在沒有原始程式碼內容的情況下執行，而命令會參考工作目錄中的 `helloworldtask.yaml` 檔案。 `--assign-identity` 參數會傳遞使用者指派身分識別的資源識別碼。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>將身分識別提取權限授與基礎登錄

在本節中，授與要從基礎登錄提取的受控識別權限，mybaseregistry。

使用 [az acr show][az-acr-show] 命令來取得基礎登錄的資源識別碼，並將其儲存在變數中：

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

請使用 [az role assignment create][az-role-assignment-create] 命令，將身分識別的 `acrpull` 角色指派給基礎登錄。 此角色只有從登錄提取映像的權限。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

繼續 [將目標登錄認證新增至](#add-target-registry-credentials-to-task)工作。

## <a name="option-2-create-task-with-system-assigned-identity"></a>選項 2：使用系統指派的身分識別來建立工作

本節中的步驟可建立工作，並啟用系統指派的身分識別。 如果您想要改為啟用使用者指派的身分識別，請參閱[選項 1：使用使用者指派的身分識別來建立工作](#option-1-create-task-with-user-assigned-identity)。 

### <a name="create-task"></a>建立工作

執行下列 [az acr task create][az-acr-task-create] 命令來建立 helloworldtask 工作。 此工作會在沒有原始程式碼內容的情況下執行，而命令會參考工作目錄中的 `helloworldtask.yaml` 檔案。 不含值的 `--assign-identity` 參數可在工作上啟用系統指派的身分識別。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>將身分識別提取權限授與基礎登錄

在本節中，授與要從基礎登錄提取的受控識別權限，mybaseregistry。

使用 [az acr show][az-acr-show] 命令來取得基礎登錄的資源識別碼，並將其儲存在變數中：

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

請使用 [az role assignment create][az-role-assignment-create] 命令，將身分識別的 `acrpull` 角色指派給基礎登錄。 此角色只有從登錄提取映像的權限。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>將目標登錄認證新增至工作

現在，使用 [az acr task credential add][az-acr-task-credential-add] 命令，讓工作能夠使用身分識別的認證向基礎登錄進行驗證。 執行對應於您在工作中所啟用受控識別類型的命令。 如果您已啟用使用者指派的身分識別，請以身分識別的用戶端識別碼來傳遞 `--use-identity`。 如果您已啟用系統指派的身分識別，請傳遞 `--use-identity [system]`。

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

## <a name="manually-run-the-task"></a>手動執行工作

若要確認您已啟用受控識別的工作成功執行，請使用 [az acr task run][az-acr-task-run] 命令來手動觸發工作。 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

如果工作成功執行，則輸出會類似於：

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

執行 [az acr repository show-tags][az-acr-repository-show-tags] 命令，以確認建置映像並成功加以推送至 myregistry：

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

範例輸出︰

```console
cf10
```

## <a name="next-steps"></a>後續步驟

* 深入了解[在 ACR 工作中啟用受控識別](container-registry-tasks-authentication-managed-identity.md)。
* 請參閱 [ACR 工作 YAML 參考](container-registry-tasks-reference-yaml.md)

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
