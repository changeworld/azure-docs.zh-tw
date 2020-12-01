---
title: 管理公用登錄內容的工作流程
description: 建立自動化的 Azure Container Registry 工作工作流程，以在私人 Azure Container Registry 中追蹤、管理及取用公用映射內容。
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349277"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>如何使用 Azure Container Registry 工作來取用和維護公開內容

本文提供 Azure Container Registry 中的範例工作流程，協助您管理取用和維護公開內容：

1. 匯入相依公用映射的本機複本。
1. 透過安全性掃描和功能測試來驗證公用映射。
1. 將映射升階為私用登錄，以供內部使用。
1. 針對相依于公開內容的應用程式觸發基礎映射更新。
1. 使用 [Azure Container Registry 工作](container-registry-tasks-overview.md) 將此工作流程自動化。

下圖摘要說明工作流程：

![使用公用內容工作流程](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

閘道匯入工作流程可協助您管理組織對外部受管理成品的相依性，例如來自公用登錄的映射，包括 [Docker Hub][docker-hub]、 [GCR][gcr]、 [Quay][quay]、 [GitHub Container registry][ghcr]、 [Microsoft Container registry][mcr]，甚至是其他 [Azure Container][acr]registry。 

如需有關公開內容相依性所引進的風險，以及如何使用 Azure Container Registry 來減輕這些風險的背景資訊，請參閱 [OCI 使用公用內容 Blog 文章][oci-consuming-public-content] 和使用 [Azure Container Registry 管理公開內容](buffer-gate-public-content.md)。

您可以使用 Azure CLI 的 Azure Cloud Shell 或本機安裝來完成此逐步解說。 建議使用 Azure CLI 2.10 版或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-cli]。

## <a name="scenario-overview"></a>案例概觀

![匯入工作流程元件](./media/tasks-consume-public-content/consuming-public-content-objects.png)

本逐步解說會設定：

1. 三個 **容器** 登錄，代表：
   * 模擬的 [Docker Hub][docker-hub] (`publicregistry`) 以支援變更基礎映射
   * Team registry (`contoso`) 共用私人映射
   * 匯 `baseartifacts` 入的公開內容 () 公司/團隊共用登錄
1. 每個登錄中的 **ACR** 工作。 工作：  
   1. 建立模擬的公用 `node` 映射
   1. 將映射匯入並驗證 `node` 至公司/小組共用登錄
   1. 建立及部署 `hello-world` 映射
1. **ACR 工作定義**，包括下列各項的設定：
1. **登錄認證** 的集合，也就是金鑰保存庫的指標
1. **秘密** 的集合，可在中使用 `acr-task.yaml` ，這是金鑰保存庫的指標
1. 在中使用的 **已設定值** 集合 `acr-task.yaml`
1. 用來保護所有秘密的 **Azure key vault**
1. 裝載組建應用程式的 **Azure 容器實例** `hello-world`

## <a name="prerequisites"></a>必要條件

下列步驟會設定逐步解說中所建立和使用的資源值。

### <a name="set-environment-variables"></a>設定環境變數

為您的環境設定唯一的變數。 我們會遵循最佳作法，將具有永久性內容的資源放在其自己的資源群組中，以將意外刪除降至最低。 不過，您可以視需要將這些資源放在單一資源群組中。

本文中的範例會針對 bash shell 進行格式化。

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git 存放庫和權杖

若要模擬您的環境，請將下列每個 Git 存放庫分叉至您可以管理的存放庫。 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

然後，為您的分支存放庫更新下列變數。

`:main`附加至 Git url 結尾的會代表預設的儲存機制分支。

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

您需要 [ (PAT) 的 GitHub 存取權杖 ][git-token] ，才能讓 ACR 工作複製和建立 Git webhook。 如需使用私人存放庫的必要許可權建立權杖的步驟，請參閱 [建立 GitHub 存取權杖](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)。 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker Hub 認證  
若要避免從 Docker Hub 提取映射時的節流和身分識別要求，請建立 [Docker Hub token][docker-hub-tokens]。 然後，設定下列環境變數：

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>建立登錄

使用 Azure CLI 命令建立三個高階層容器登錄，每個都在自己的資源群組中：

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>建立金鑰保存庫並設定秘密

建立金鑰保存庫：

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

在金鑰保存庫中設定 Docker Hub 使用者名稱和權杖：

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

在金鑰保存庫中設定並驗證 Git PAT：

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>建立 Azure 容器實例的資源群組

部署映射時，會在稍後的工作中使用此資源群組 `hello-world` 。

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>建立公用 `node` 基礎映射

若要 `node` 在 Docker Hub 上模擬映射，請建立 [ACR][acr-task] 工作來建立和維護公用映射。 這項設定可讓 `node` 映射維護人員模擬變更。

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

若要避免 Docker 節流，請將 [Docker Hub 認證][docker-hub-tokens] 新增至工作。 您可以使用 [ [acr 工作認證][acr-task-credentials] ] 命令將 Docker 認證傳遞給任何登錄，包括 Docker Hub。

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

授與工作存取權，以讀取金鑰保存庫中的值：

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Git 認可、基底映射更新、計時器或手動執行[都可以觸發][acr-task-triggers]工作。 

手動執行工作以產生 `node` 映射：

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

列出模擬公用登錄中的映射：

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>建立 `hello-world` 映射

根據模擬的公用 `node` 映射建立 `hello-world` 映射。

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>建立權杖以供提取存取模擬的公用登錄

建立模擬公用登錄的 [存取權杖][acr-tokens] ，範圍為 `pull` 。 然後，在金鑰保存庫中設定它：

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>針對 Azure 容器實例的提取存取建立權杖

針對裝載映射的登錄建立 [存取權杖][acr-tokens] `hello-world` ，範圍為提取。 然後，在金鑰保存庫中設定它：

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>建立工作以建立和維護 `hello-world` 映射

下列命令會從存放庫中的定義建立工作 `acr-tasks.yaml` `hello-world` 。 工作步驟 `hello-world` 會建立映射，然後將其部署至 Azure 容器實例。 Azure 容器實例的資源群組是在上一節中建立的。 藉由在工作 `az container create` 中呼叫，而只有中的差異 `image:tag` ，工作會在此逐步解說中部署至相同的實例。

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

將認證新增至模擬公用登錄的工作：

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

授與工作存取權，以讀取金鑰保存庫中的值：

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

藉由授與資源群組的存取權，授與工作存取權來建立及管理 Azure 容器實例：

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

建立並設定工作之後，執行工作來建立和部署 `hello-world` 映射：

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

建立之後，取得裝載映射的容器 IP 位址 `hello-world` 。

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

在您的瀏覽器中，移至 IP 位址以查看正在執行的應用程式。

## <a name="update-the-base-image-with-a-questionable-change"></a>以「可疑」的變更更新基礎映射

本節將模擬可能在環境中造成問題的基底映射變更。

1. 在分支存放庫 `Dockerfile` 中開啟 `base-image-node` 。
1. 將變更 `BACKGROUND_COLOR` 為， `Orange` 以模擬變更。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

認可變更並監看 ACR 工作，以自動開始建立。

請留意工作以開始執行：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

最後，您應該會看到 `Succeeded` 以觸發程式為基礎的狀態 `Commit` ：

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

輸入 **Ctrl + C** 以結束 watch 命令，然後查看最近執行的記錄：

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

`node`映射完成後， `watch` 為了讓 ACR 工作自動開始建立 `hello-world` 映射：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

最後，您應該會看到 `Succeeded` 以觸發程式為基礎的狀態 `Image Update` ：

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

輸入 **Ctrl + C** 以結束 watch 命令，然後查看最近執行的記錄：

```azurecli-interactive
az acr task logs -r $REGISTRY
```

完成之後，取得裝載已更新映射之網站的 IP 位址 `hello-world` ：

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

在您的瀏覽器中，移至該網站，這應該會有橙色的 () 背景。

### <a name="checking-in"></a>簽入

至此，您已建立會 `hello-world` 自動建立在 Git 認可上的映射，以及對基底映射所做的變更 `node` 。 在此範例中，工作是根據 Azure Container Registry 中的基底映射建立，但可使用任何支援的登錄。

當映射更新時，基底映射更新會自動 retriggers 工作執行 `node` 。 如這裡所示，並非所有更新都需要。

## <a name="gated-imports-of-public-content"></a>公開內容的閘道匯入

為了防止上游變更中斷重要的工作負載，可能會新增安全性掃描和功能測試。

在本節中，您會建立 ACR 工作，以：

* 建立測試映射
* 針對測試映射執行功能測試腳本 `./test.sh`
* 如果映射測試成功，請將公用映射匯入至 **baseimages** 登錄

### <a name="add-automation-testing"></a>新增自動化測試

若要為任何上游內容進行閘道，則會執行自動化測試。 在此範例中， `test.sh` 會提供，它會檢查 `$BACKGROUND_COLOR` 。 如果測試失敗， `EXIT_CODE` `1` 則會傳回的，這會導致 ACR 工作步驟失敗，並結束工作執行。 您可以用任何形式的工具（包括記錄結果）來展開測試。 閘道是由腳本中的通過/失敗回應所管理，並在此處重現：

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>工作 YAML 

請參閱存放庫 `acr-task.yaml` 中的 `import-baseimage-node` ，它會執行下列步驟：

1. 使用下列 Dockerfile 建立測試基礎映射：
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. 完成時，執行會執行的容器來驗證映射 `./test.sh`
1. 只有在成功完成時，才執行匯入步驟，這些步驟是以閘道 `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>建立工作以匯入和測試基礎映射

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

將認證新增至模擬公用登錄的工作：

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

授與工作存取權，以讀取金鑰保存庫中的值：

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

執行匯入工作：

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> 如果工作因為 `./test.sh: Permission denied` 而失敗，請確定腳本具有執行許可權，並認可回 Git 存放庫：
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>更新 `hello-world` 映射以從閘道 `node` 映射建立

建立 [存取權杖][acr-tokens] 以存取基礎構件登錄（範圍從存放庫） `read` `node` 。 然後，在金鑰保存庫中設定：

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

將認證新增至基底成品登錄的 **hello world** 工作：

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

更新工作以變更 `REGISTRY_FROM_URL` 以使用登錄 `BASE_ARTIFACTS`

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

執行 **hello world** 工作以變更其基底映射相依性：

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>使用「有效」變更來更新基礎映射

1. 在存放庫 `Dockerfile` 中開啟 `base-image-node` 。
1. 將變更 `BACKGROUND_COLOR` 為， `Green` 以模擬有效的變更。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

認可變更並監視更新順序：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

執行之後，輸入 **Ctrl + C** 並監視記錄：

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

完成之後，請監視 **基底-映射匯入** 工作：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

執行之後，輸入 **Ctrl + C** 並監視記錄：

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

完成之後，請監視 **hello world** 工作：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

執行之後，輸入 **Ctrl + C** 並監視記錄：

```azurecli-interactive
az acr task logs -r $REGISTRY
```

完成之後，取得裝載已更新映射之網站的 IP 位址 `hello-world` ：

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

在您的瀏覽器中，移至該網站，這應該會有綠色 (有效的) 背景。

### <a name="view-the-gated-workflow"></a>查看閘道工作流程

再次執行上一節中的步驟，其背景色彩為紅色。

1. 在存放庫 `Dockerfile` 中 `base-image-node` 開啟
1. 將變更 `BACKGROUND_COLOR` 為， `Red` 以模擬不正確變更。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

認可變更並監視更新順序：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

執行之後，輸入 **Ctrl + C** 並監視記錄：

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

完成之後，請監視 **基底-映射匯入** 工作：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

執行之後，輸入 **Ctrl + C** 並監視記錄：

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

此時，您應該會看到「 **基本-匯入節點** 」工作無法通過驗證，並停止該順序以發佈 `hello-world` 更新。 輸出會類似：

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>將更新發佈至 `hello-world`

映射的變更 `hello-world` 將繼續使用最後驗證的 `node` 映射。

通過閘道驗證的基底映射任何額外的變更 `node` ，都會觸發映射的基底映射更新 `hello-world` 。

## <a name="cleaning-up"></a>清除

當不再需要時，請刪除本文中使用的資源。

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>後續步驟

本文中。 您已使用 ACR 工作來建立自動化管制工作流程，以將更新的基底映射引進您的環境。 請參閱 Azure Container Registry 中管理映射的相關資訊。


* [標記和版本設定容器映射的建議](container-registry-image-tag-version.md)
* [鎖定 Azure container registry 中的容器映射](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io