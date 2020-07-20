---
title: 從 ACR 工作進行外部驗證
description: 使用 Azure 資源的受控識別，設定 Azure Container Registry 工作（ACR 工作）來讀取儲存在 Azure 金鑰保存庫中的 Docker Hub 認證。
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058124"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>使用 Azure 管理的身分識別在 ACR 工作中進行外部驗證 

在 [ACR 工作](container-registry-tasks-overview.md)中，您可以[啟用 Azure 資源的受控識別](container-registry-tasks-authentication-managed-identity.md)。 工作可以使用身分識別存取其他 Azure 資源，不需要提供或管理認證。 

在本文中，您將瞭解如何在存取儲存在 Azure key vault 中的秘密的工作中啟用受控識別。 

若要建立 Azure，本文需要您執行 Azure CLI 2.0.68 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>案例概觀

範例工作會讀取儲存在 Azure 金鑰保存庫中的 Docker Hub 認證。 認證適用于對私人 Docker Hub 存放庫具有寫入（push）許可權的 Docker Hub 帳戶。 若要讀取認證，您可以使用受控識別來設定工作，並為其指派適當的許可權。 與身分識別相關聯的工作會建立映射，並登入 Docker Hub 以將映射推送至私人存放庫。 

這個範例會示範使用使用者指派或系統指派受控識別的步驟。 您所選擇的身分識別取決於貴組織的需求。

在真實世界的案例中，公司可能會將映射發佈到 Docker Hub 中的私人存放庫，做為組建程式的一部分。 

## <a name="prerequisites"></a>必要條件

您需要在其中執行工作的 Azure container registry。 在本文中，此登錄名為 myregistry。 在稍後的步驟中，將取代為您自己的登錄名稱。

如果您還沒有 Azure container registry，請參閱[快速入門：使用 Azure CLI 建立私人容器](container-registry-get-started-azure-cli.md)登錄。 您還不需要將映像推送至登錄。

您也需要 Docker Hub 中的私人存放庫，以及具有寫入儲存機制之許可權的 Docker Hub 帳戶。 在此範例中，此存放庫名為*hubuser/hubrepo*。 

## <a name="create-a-key-vault-and-store-secrets"></a>建立金鑰保存庫並儲存秘密

首先，如果您需要，請使用下列[az group create][az-group-create]命令，在*eastus*位置中建立名為*myResourceGroup*的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az keyvault create][az-keyvault-create]命令來建立金鑰保存庫。 請務必指定唯一的金鑰保存庫名稱。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

使用[az keyvault secret set][az-keyvault-secret-set]命令，將所需的 Docker Hub 認證儲存在金鑰保存庫中。 在這些命令中，值會在環境變數中傳遞：

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

在真實世界的案例中，可能會在個別的進程中設定和維護秘密。

## <a name="define-task-steps-in-yaml-file"></a>定義 YAML 檔案中的工作步驟

此範例工作的步驟定義于[YAML](container-registry-tasks-reference-yaml.md)檔案中。 `dockerhubtask.yaml`在本機工作目錄中建立名為的檔案，並貼上下列內容。 請務必以您的金鑰保存庫名稱取代檔案中的金鑰保存庫名稱。

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

工作步驟會執行下列動作：

* 管理秘密認證以向 Docker Hub 進行驗證。
* 將秘密傳遞至命令，以向 Docker Hub 進行驗證 `docker login` 。
* 使用[Azure 範例/acr-](https://github.com/Azure-Samples/acr-tasks.git)工作存放庫中的範例 Dockerfile 來建立映射。
* 將映射推送至私人 Docker Hub 存放庫。


## <a name="option-1-create-task-with-user-assigned-identity"></a>選項 1：使用使用者指派的身分識別來建立工作

本節中的步驟可建立工作，並啟用使用者指派的身分識別。 如果您想要改為啟用系統指派的身分識別，請參閱[選項 2：使用系統指派的身分識別來建立工作](#option-2-create-task-with-system-assigned-identity)。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>建立工作

藉由執行下列[az acr task create][az-acr-task-create]命令來建立工作*dockerhubtask* 。 此工作會在沒有原始程式碼內容的情況下執行，而命令會參考工作目錄中的 `dockerhubtask.yaml` 檔案。 `--assign-identity` 參數會傳遞使用者指派身分識別的資源識別碼。 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>授與金鑰保存庫的身分識別存取權

執行下列[az keyvault set-policy][az-keyvault-set-policy]命令，在金鑰保存庫上設定存取原則。 下列範例可讓識別從金鑰保存庫讀取秘密。 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

繼續[手動執行工作](#manually-run-the-task)。

## <a name="option-2-create-task-with-system-assigned-identity"></a>選項 2：使用系統指派的身分識別來建立工作

本節中的步驟可建立工作，並啟用系統指派的身分識別。 如果您想要改為啟用使用者指派的身分識別，請參閱[選項 1：使用使用者指派的身分識別來建立工作](#option-1-create-task-with-user-assigned-identity)。 

### <a name="create-task"></a>建立工作

藉由執行下列[az acr task create][az-acr-task-create]命令來建立工作*dockerhubtask* 。 此工作會在沒有原始程式碼內容的情況下執行，而命令會參考工作目錄中的 `dockerhubtask.yaml` 檔案。 不含值的 `--assign-identity` 參數可在工作上啟用系統指派的身分識別。  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>授與金鑰保存庫的身分識別存取權

執行下列[az keyvault set-policy][az-keyvault-set-policy]命令，在金鑰保存庫上設定存取原則。 下列範例可讓識別從金鑰保存庫讀取秘密。 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>手動執行工作

若要確認您已啟用受控識別的工作成功執行，請使用 [az acr task run][az-acr-task-run] 命令來手動觸發工作。 `--set`參數是用來將私人存放庫名稱傳遞給工作。 在此範例中，預留位置存放庫名稱是*hubuser/hubrepo*。

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

當工作執行成功時，輸出會向 Docker Hub 顯示成功的驗證，並成功建立映射並推送至私人存放庫：

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

若要確認已推送映射，請檢查私人 Docker Hub 存放庫中的標記（ `cf24` 在此範例中為）。

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
