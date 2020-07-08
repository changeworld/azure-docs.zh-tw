---
title: 使用範本執行快速工作
description: 將 ACR 工作執行排入佇列，以使用 Azure Resource Manager 範本建立映射
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 7ad40d2e925d5e1443af9bce4115d45b0e8c06e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82927763"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>使用 Resource Manager 範本執行 ACR 工作

[ACR 工作](container-registry-tasks-overview.md)是 Azure Container Registry 內的一組功能，可協助您跨容器生命週期管理及修改容器映像。 

本文說明將快速工作執行排入佇列的 Azure Resource Manager 範本範例，類似于您可以使用[az acr build][az-acr-build]命令來手動建立。

將工作執行排入佇列的 Resource Manager 範本在自動化案例中非常有用，而且會擴充的功能 `az acr build` 。 例如：

* 使用範本來建立容器登錄，並立即將工作執行排入佇列，以建立及推送容器映射
* 建立或啟用可在快速工作執行中使用的其他資源，例如適用于 Azure 資源的受控識別

## <a name="limitations"></a>限制

* 您必須指定遠端內容（例如 GitHub 存放庫）做為工作執行的[來源位置](container-registry-tasks-overview.md#context-locations)。 您不能使用本機來源內容。
* 針對使用受控識別執行的工作，只允許*使用者指派*的受控識別。

## <a name="prerequisites"></a>必要條件

* **GitHub 帳戶**-如果您還沒有帳戶，請在上建立帳戶 https://github.com 。 
* **派生範例存放庫**-針對此處顯示的工作範例，請使用 github UI 將下列範例存放庫分支至您的 GitHub 帳戶： https://github.com/Azure-Samples/acr-build-helloworld-node 。 此存放庫包含用來建立小型容器映射的範例 Dockerfile 和原始程式碼。

## <a name="example-create-registry-and-queue-task-run"></a>範例：建立登錄和佇列工作執行

這個範例會使用[範例範本](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild)來建立容器登錄，並將建立和推送映射的工作執行排入佇列。 

### <a name="template-parameters"></a>範本參數

針對此範例，請提供下列範本參數的值：

|參數  |值  |
|---------|---------|
|于： registryname     |所建立登錄的唯一名稱         |
|repository     |組建工作的目標存放庫        |
|taskRunName     |指定影像標記的工作執行名稱 |
|sourceLocation     |組建工作的遠端內容，例如 https://github.com/Azure-Samples/acr-build-helloworld-node 。 存放庫根目錄中的 Dockerfile 會建立小型 Node.js web 應用程式的容器映射。 如有需要，請使用您的存放庫分支做為組建內容。         |

### <a name="deploy-the-template"></a>部署範本

使用[az deployment group create][az-deployment-group-create]命令來部署範本。 這個範例會建立*helloworld 節點： testrun*映射，並將其推送至名為*mycontainerregistry*的登錄。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git
 ```

上一個命令會在命令列上傳遞參數。 如有需要，請在[參數](../azure-resource-manager/templates/parameter-files.md)檔案中傳遞它們。

### <a name="verify-deployment"></a>驗證部署

成功完成部署之後，請執行[az acr repository show-tags][az-acr-repository-show-tags]來確認映射已建立：

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

輸出：

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>查看執行記錄

若要查看工作執行的詳細資料，請參閱執行記錄。

首先，使用[az acr task list-][az-acr-task-list-runs]回合取得執行識別碼
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

輸出會類似：

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

執行[az acr task logs][az-acr-task-logs]來查看執行識別碼的工作執行記錄，在此案例中為*ca1*：

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

輸出會顯示工作執行記錄。

您也可以在 Azure 入口網站中查看工作執行記錄。 

1. 流覽至您的 container registry
2. 在 [**服務**] 下 **，選取 [** 工作] [  >  **執行**]。
3. 選取 [執行識別碼]，在此案例中為*ca1*。 

入口網站會顯示 [工作] [執行記錄]。

## <a name="example-task-run-with-managed-identity"></a>範例：使用受控識別執行的工作

使用[範例範本](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity)將工作執行排入佇列，以啟用使用者指派的受控識別。 在工作執行期間，身分識別會進行驗證，以從另一個 Azure container registry 提取映射。 

此案例類似于[使用 Azure 管理的身分識別，在 ACR 工作中進行跨登錄驗證](container-registry-tasks-cross-registry-authentication.md)。 例如，組織可能會使用多個開發小組所存取的基底映射來維護集中式登錄。

### <a name="prepare-base-registry"></a>準備基礎登錄

基於示範目的，請建立另一個容器登錄作為基底登錄，然後推送從 Docker Hub 提取的 Node.js 基底映射。

1. 建立第二個容器登錄，例如*mybaseregistry*，以儲存基底映射。
1. `node:9-alpine`從 Docker Hub 提取映射，將它標記為基底登錄，然後將它推送至基底登錄：

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>建立新的 Dockerfile

建立從基底登錄提取基底映射的 Dockerfile。 在 GitHub 存放庫的本機分支中執行下列步驟，例如 `https://github.com/myGitHubID/acr-build-helloworld-node.git` 。

1. 在 GitHub UI 中，選取 [**建立新**檔案]。
1. 將您的檔案命名*為 Dockerfile-test* ，並貼上下列內容。 以您的登錄名稱取代*mybaseregistry*。
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. 選取 [**認可新**檔案]。

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>將身分識別提取權限授與基礎登錄

授與受控識別許可權以從基底登錄（ *mybaseregistry*）提取。

使用 [az acr show][az-acr-show] 命令來取得基礎登錄的資源識別碼，並將其儲存在變數中：

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

使用[az role 指派 create][az-role-assignment-create]命令，將 Acrpull 角色的身分識別指派給基底登錄。 此角色只有從登錄提取映像的權限。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>範本參數

針對此範例，請提供下列範本參數的值：

|參數  |值  |
|---------|---------|
|于： registryname     |建立映射的登錄名稱  |
|repository     |組建工作的目標存放庫        |
|taskRunName     |指定影像標記的工作執行名稱 |
|userAssignedIdentity |在工作中啟用之使用者指派身分識別的資源識別碼|
|customRegistryIdentity | 在工作中啟用之使用者指派身分識別的用戶端識別碼，用來向自訂登錄進行驗證 |
|customRegistry |在工作中存取之自訂登錄的登入伺服器名稱，例如*mybaseregistry.azurecr.io*|
|sourceLocation     |組建工作的遠端內容，例如* https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* |
|dockerFilePath | Dockerfile 在遠端內容的路徑，用來建立映射。 |

### <a name="deploy-the-template"></a>部署範本

使用[az deployment group create][az-deployment-group-create]命令來部署範本。 這個範例會建立*helloworld 節點： testrun*映射，並將其推送至名為*mycontainerregistry*的登錄。 基底映射會從*mybaseregistry.azurecr.io*提取。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

上一個命令會在命令列上傳遞參數。 如有需要，請在[參數](../azure-resource-manager/templates/parameter-files.md)檔案中傳遞它們。

### <a name="verify-deployment"></a>驗證部署

成功完成部署之後，請執行[az acr repository show-tags][az-acr-repository-show-tags]來確認映射已建立：

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

輸出：

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>查看執行記錄

若要查看執行記錄，請參閱[上一節](#view-run-log)中的步驟。

## <a name="next-steps"></a>後續步驟

 * 請參閱[ACR GitHub](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)存放庫中的更多範本範例。
 * 如需範本屬性的詳細資訊，請參閱工作[執行](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns)和[作業](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)的範本參考。


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
