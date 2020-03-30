---
title: 來自 ACR 任務的外部身份驗證
description: 配置 Azure 容器註冊表任務 （ACR 任務）以使用 Azure 資源的託管標識讀取存儲在 Azure 金鑰保存庫中的 Docker Hub 憑據。
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842515"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>使用 Azure 管理的標識的 ACR 任務中的外部身份驗證 

在[ACR 任務中](container-registry-tasks-overview.md)，可以為[Azure 資源啟用託管標識](container-registry-tasks-authentication-managed-identity.md)。 任務可以使用標識訪問其他 Azure 資源，而無需提供或管理認證。 

在本文中，您將瞭解如何在訪問存儲在 Azure 金鑰保存庫中的秘密的任務中啟用託管標識。 

要創建 Azure 資源，本文要求運行 Azure CLI 版本 2.0.68 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>案例概觀

示例任務讀取存儲在 Azure 金鑰保存庫中的 Docker 中心憑據。 憑據適用于具有專用 Docker Hub 存儲庫的寫入（推送）許可權的 Docker Hub 帳戶。 要讀取憑據，請使用託管標識配置任務，並為其分配適當的許可權。 與標識關聯的任務生成映射，並登錄到 Docker 中心以將映射推送到專用回購。 

此示例顯示使用使用者分配或系統分配的託管標識的步驟。 您選擇的身份取決於組織的需求。

在實際場景中，公司可能會將映射發佈到 Docker Hub 中的私有回購，作為生成過程的一部分。 

## <a name="prerequisites"></a>Prerequisites

您需要在其中運行任務的 Azure 容器註冊表。 在本文中，此註冊表名為*myregistry*。 在後面的步驟中，用您自己的註冊表名稱替換。

如果還沒有 Azure 容器註冊表，請參閱[快速入門：使用 Azure CLI 創建專用容器註冊表](container-registry-get-started-azure-cli.md)。 您還不需要將映射推送到註冊表。

您還需要 Docker Hub 中的私有存儲庫和具有寫入存儲庫許可權的 Docker Hub 帳戶。 在此示例中，此回購名為*hubuser/hubrepo*。 

## <a name="create-a-key-vault-and-store-secrets"></a>創建金鑰保存庫並存儲機密

首先，如果需要，請使用以下[az 組創建][az-group-create]命令在*東部*位置創建名為*myResourceGroup*的資源組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az 金鑰保存庫創建][az-keyvault-create]命令創建金鑰保存庫。 請確保指定唯一的金鑰保存庫名稱。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

使用[az 金鑰庫金鑰集][az-keyvault-secret-set]命令將所需的 Docker Hub 憑據存儲在金鑰保存庫中。 在這些命令中，值在環境變數中傳遞：

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

在實際場景中，機密可能會在單獨的進程中設置和維護。

## <a name="define-task-steps-in-yaml-file"></a>在 YAML 檔中定義任務步驟

此示例任務的步驟在[YAML 檔中](container-registry-tasks-reference-yaml.md)定義。 在本地工作目錄中`dockerhubtask.yaml`創建名為的檔並粘貼以下內容。 請確保將檔中的金鑰保存庫名稱替換為金鑰保存庫的名稱。

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

任務步驟執行以下操作：

* 管理金鑰憑據以使用 Docker 中心進行身份驗證。
* 通過將機密傳遞給`docker login`命令，使用 Docker Hub 進行身份驗證。
* 使用[Azure 採樣/acr 任務](https://github.com/Azure-Samples/acr-tasks.git)回購中的示例 Dockerfile 生成映射。
* 將映射推送到專用 Docker 中心存儲庫。


## <a name="option-1-create-task-with-user-assigned-identity"></a>選項 1：使用使用者分配的標識創建任務

本節中的步驟創建任務並啟用使用者分配的標識。 如果要改為啟用系統分配的標識，請參閱[選項 2：使用系統分配的標識創建任務](#option-2-create-task-with-system-assigned-identity)。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>建立工作

通過執行以下[az acr 任務創建命令創建][az-acr-task-create]任務*dockerhubtask。* 任務在沒有原始程式碼上下文的情況下運行，命令引用工作目錄中的檔`dockerhubtask.yaml`。 參數`--assign-identity`傳遞使用者分配的標識的資源識別碼。 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>選項 2：使用系統分配的標識創建任務

本節中的步驟創建任務並啟用系統分配的標識。 如果要改為啟用使用者分配的標識，請參閱[選項 1：使用使用者分配的身份創建任務](#option-1-create-task-with-user-assigned-identity)。 

### <a name="create-task"></a>建立工作

通過執行以下[az acr 任務創建命令創建][az-acr-task-create]任務*dockerhubtask。* 任務在沒有原始程式碼上下文的情況下運行，命令引用工作目錄中的檔`dockerhubtask.yaml`。 沒有`--assign-identity`值的參數支援任務上的系統分配的標識。  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>授予對金鑰保存庫的標識存取權限

運行以下[az 金鑰庫集策略][az-keyvault-set-policy]命令，以在金鑰保存庫上設置訪問策略。 下面的示例允許標識從金鑰保存庫讀取機密。 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>手動運行任務

要驗證啟用託管標識的任務是否成功運行，請使用[az acr 任務運行][az-acr-task-run]命令手動觸發該任務。 參數`--set`用於將私有回購名稱傳遞給任務。 在此示例中，預留位置回購名稱為*輪轂使用者/hubrepo*。

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

當任務成功運行時，輸出將顯示對 Docker Hub 的成功身份驗證，並且映射已成功生成並推送到專用回購：

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

要確認推送圖像，請在專用 Docker Hub`cf24`回購中檢查標記（在此示例中）。

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
