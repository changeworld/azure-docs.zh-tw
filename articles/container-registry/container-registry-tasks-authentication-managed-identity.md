---
title: ACR 任務中的託管標識
description: 在 Azure 容器註冊表任務中為 Azure 資源啟用託管標識，以允許該任務訪問其他 Azure 資源，包括其他專用容器註冊表。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111776"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>在 ACR 任務中使用 Azure 託管標識 

在[ACR 任務中](container-registry-tasks-overview.md)[為 Azure 資源啟用託管標識](../active-directory/managed-identities-azure-resources/overview.md)，以便該任務可以訪問其他 Azure 資源，而無需提供或管理認證。 例如，使用託管標識使任務步驟能夠將容器映射拉到或推送到另一個註冊表。

在本文中，您將瞭解如何使用 Azure CLI 在 ACR 任務上啟用使用者分配或系統分配的託管標識。 可以使用 Azure 雲外殼或 Azure CLI 的本地安裝。 如果您想在本地使用它，則需要版本 2.0.68 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

為了便於說明，本文中的示例命令使用[az acr 任務創建][az-acr-task-create]來創建支援託管標識的基本映射生成任務。 有關使用託管標識從 ACR 任務訪問安全資源的示例方案，請參閱：

* [跨登錄驗證](container-registry-tasks-cross-registry-authentication.md)
* [使用存儲在 Azure 金鑰保存庫中的秘密訪問外部資源](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>為什麼要使用受控識別？

Azure 資源的託管標識為選定的 Azure 服務提供 Azure 活動目錄中自動託管標識。 可以使用託管標識配置 ACR 任務，以便該任務可以訪問其他安全的 Azure 資源，而無需在任務步驟中傳遞憑據。

受控身分識別有兩種：

* *使用者分配的身份 ，* 可以分配給多個資源，並根據需要保留。 使用者指派的身分識別目前處於預覽狀態。

* *系統分配的標識*，它對於特定資源（如 ACR 任務）是唯一的，並且持續該資源的存留期。

您可以在 ACR 任務中啟用任一類型或兩種類型的標識。 授予對另一個資源的標識存取權限，就像任何安全主體一樣。 任務運行時，它使用標識訪問需要訪問的任何任務步驟中的資源。

## <a name="steps-to-use-a-managed-identity"></a>使用託管標識的步驟

按照這些高級步驟將託管標識與 ACR 任務一起使用。

### <a name="1-optional-create-a-user-assigned-identity"></a>1. （可選） 創建使用者分配的標識

如果計畫使用使用者分配的標識，請使用現有標識，或使用 Azure CLI 或其他 Azure 工具創建標識。 例如，使用[az 標識創建][az-identity-create]命令。 

如果計畫僅使用系統分配的標識，請跳過此步驟。 創建 ACR 任務時創建系統分配的標識。

### <a name="2-enable-identity-on-an-acr-task"></a>2. 在 ACR 任務上啟用標識

創建 ACR 任務時，可以選擇啟用使用者分配的標識、系統分配的標識或兩者。 例如，在`--assign-identity`Azure CLI 中運行[az acr 任務創建][az-acr-task-create]命令時傳遞參數。

要啟用系統分配的標識，則傳遞`--assign-identity`時沒有值或`assign-identity [system]`。 以下示例命令從公共 GitHub 存儲庫創建 Linux 任務，該`hello-world`存儲庫生成映射並啟用系統分配的託管標識：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

要啟用使用者分配的標識，使用標識的資源`--assign-identity` *ID*的值傳遞。 以下示例命令從公共 GitHub 存儲庫創建 Linux 任務，該`hello-world`存儲庫生成映射並啟用使用者分配的託管標識：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

您可以通過運行[az 標識顯示][az-identity-show]命令獲取標識的資源識別碼。 資源組中的 ID *myUser 分配標識*的資源識別碼*為"資源組"，* 其形式為： 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. 授予訪問其他 Azure 資源的標識許可權

根據任務的要求，授予訪問其他 Azure 資源的標識許可權。 範例包括：

* 將託管標識分配具有拉取、推送和拉取或其他許可權的角色到 Azure 中的目標容器註冊表。 有關註冊表角色的完整清單，請參閱[Azure 容器註冊表角色和許可權](container-registry-roles.md)。 
* 為託管標識分配一個角色，以讀取 Azure 金鑰保存庫中的秘密。

使用[Azure CLI](../role-based-access-control/role-assignments-cli.md)或其他 Azure 工具管理基於角色的資源訪問。 例如，運行[az 角色指派創建][az-role-assignment-create]命令，將標識角色指派給資源。 

下面的示例為託管標識分配從容器註冊表中提取的許可權。 該命令指定任務標識*的主體 ID*和目標注冊表的資源*ID。*


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. （可選） 向任務添加憑據

如果任務需要憑據才能將映射拉到其他自訂註冊表或訪問其他資源，請向任務添加憑據。 運行[az acr 任務憑據添加][az-acr-task-credential-add]命令以添加憑據，並`--use-identity`傳遞參數以指示標識可以訪問憑據。 

例如，要添加系統分配的標識的憑據以使用 Azure 容器註冊表*目標注冊表*進行身份驗證，則通過`use-identity [system]`：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

要添加使用者分配的標識的憑據以使用註冊表*目標注冊表*進行身份驗證，使用標識的`use-identity`*用戶端 ID*的值傳遞。 例如：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

您可以通過運行[az 標識顯示][az-identity-show]命令獲取標識的用戶端 ID。 用戶端 ID 是表單`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`的 GUID。

### <a name="5-run-the-task"></a>5. 運行任務

使用託管標識配置任務後，運行該任務。 例如，要測試本文中創建的任務之一，請使用[az acr 任務運行][az-acr-task-run]命令手動觸發它。 如果配置了其他自動任務觸發器，則任務在自動觸發時運行。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何在 ACR 任務上啟用和使用使用者分配或系統分配的託管標識。 有關使用託管標識從 ACR 任務訪問安全資源的方案，請參閱：

* [跨登錄驗證](container-registry-tasks-cross-registry-authentication.md)
* [使用存儲在 Azure 金鑰保存庫中的秘密訪問外部資源](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
