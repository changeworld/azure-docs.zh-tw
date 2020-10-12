---
title: ACR 工作中的受控識別
description: 在 Azure Container Registry 工作中啟用適用于 Azure 資源的受控識別，以允許工作存取其他 Azure 資源，包括其他私用容器登錄。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77111776"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>在 ACR 工作中使用 Azure 受控識別 

在[ACR](container-registry-tasks-overview.md)工作中啟用[適用于 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)，讓工作可以存取其他 Azure 資源，而不需要提供或管理認證。 例如，使用受控識別來啟用工作步驟，以將容器映射提取或推送至另一個登錄。

在本文中，您將瞭解如何使用 Azure CLI，在 ACR 工作上啟用使用者指派或系統指派的受控識別。 您可以使用 Azure Cloud Shell 或本機安裝的 Azure CLI。 如果您想要在本機使用，則需要版本 >2.0.68 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

為了方便說明，本文中的範例命令會使用 [az acr task create][az-acr-task-create] 來建立基本的「映射組建」工作，以啟用受控識別。 如需使用受控識別從 ACR 工作存取受保護資源的範例案例，請參閱：

* [跨登錄驗證](container-registry-tasks-cross-registry-authentication.md)
* [使用儲存在 Azure Key Vault 中的秘密存取外部資源](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>為什麼要使用受控識別？

適用于 Azure 資源的受控識別會在 Azure Active Directory 中提供選取的 Azure 服務與自動管理的身分識別。 您可以使用受控識別來設定 ACR 工作，讓工作可以存取其他受保護的 Azure 資源，而不需在工作步驟中傳遞認證。

受控身分識別有兩種：

* *使用者指派*的身分識別，您可以將其指派給多個資源，而且只要您需要，就能保存。 使用者指派的身分識別目前處於預覽狀態。

* *系統指派*的身分識別，這對特定資源（例如 ACR 工作）是唯一的，而且會在該資源的存留期間持續。

您可以在 ACR 工作中啟用其中一種或兩種類型的身分識別。 將身分識別存取權授與另一個資源，就像任何安全性主體一樣。 當工作執行時，它會使用身分識別來存取任何需要存取之工作步驟中的資源。

## <a name="steps-to-use-a-managed-identity"></a>使用受控識別的步驟

遵循下列高階步驟，使用受控識別搭配 ACR 工作。

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (選擇性) 建立使用者指派的身分識別

如果您打算使用使用者指派的身分識別，請使用現有的身分識別，或使用 Azure CLI 或其他 Azure 工具建立身分識別。 例如，使用 [az identity create][az-identity-create] 命令。 

如果您打算只使用系統指派的身分識別，請略過此步驟。 建立 ACR 工作時，您會建立系統指派的身分識別。

### <a name="2-enable-identity-on-an-acr-task"></a>2. 在 ACR 工作上啟用身分識別

當您建立 ACR 工作時，可以選擇性地啟用使用者指派的身分識別、系統指派的身分識別，或兩者。 例如， `--assign-identity` 當您在 Azure CLI 中執行 [az acr task create][az-acr-task-create] 命令時，請傳遞參數。

若要啟用系統指派的身分識別，請傳遞 `--assign-identity` 無值或 `assign-identity [system]` 。 下列範例命令會從公用 GitHub 存放庫建立 Linux 工作，以建立 `hello-world` 映射並啟用系統指派的受控識別：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

若要啟用使用者指派的身分識別，請 `--assign-identity` 使用身分識別的 *資源識別碼* 值來傳遞。 下列範例命令會從公用 GitHub 存放庫建立 Linux 工作，以建立 `hello-world` 映射並啟用使用者指派的受控識別：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

您可以執行 [az identity show][az-identity-show] 命令以取得身分識別的資源識別碼。 資源群組*myResourceGroup*中識別碼*MYUSERASSIGNEDIDENTITY*的資源識別碼格式如下： 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. 授與身分識別許可權以存取其他 Azure 資源

視工作的需求而定，授與身分識別許可權以存取其他 Azure 資源。 範例包括：

* 使用提取、推送和提取，或 Azure 中目標容器登錄的其他許可權，將角色指派給受控識別。 如需登錄角色的完整清單，請參閱 [Azure Container Registry 角色和許可權](container-registry-roles.md)。 
* 將角色指派給受控識別，以讀取 Azure 金鑰保存庫中的秘密。

使用 [Azure CLI](../role-based-access-control/role-assignments-cli.md) 或其他 Azure 工具來管理以角色為基礎的資源存取權。 例如，執行 [az role assign create][az-role-assignment-create] 命令以將角色的身分識別指派給資源。 

下列範例會將從容器登錄中提取的許可權指派給受控識別。 此命令會指定工作身分識別的 *主體識別碼* 和目標登錄的 *資源識別碼* 。


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (選擇性) 將認證新增至工作

如果您的工作需要認證才能將映射提取或推送至另一個自訂登錄，或要存取其他資源，請將認證新增至工作。 執行 [az acr task credential add][az-acr-task-credential-add] 命令以新增認證，然後傳遞 `--use-identity` 參數以表示身分識別可以存取認證。 

例如，若要為系統指派的身分識別新增認證，以使用 Azure container registry *targetregistry*進行驗證，請傳遞 `use-identity [system]` ：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

若要為使用者指派的身分識別新增認證，以便向登錄 *targetregistry*進行驗證，請傳遞身分 `use-identity` 識別的 *用戶端識別碼* 值。 例如：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

您可以執行 [az identity show][az-identity-show] 命令以取得身分識別的用戶端識別碼。 用戶端識別碼是表單的 GUID `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 。

### <a name="5-run-the-task"></a>5. 執行工作

使用受控識別設定工作之後，請執行工作。 例如，若要測試本文中所建立的其中一項工作，請使用 [az acr task run][az-acr-task-run] 命令手動觸發。 如果您已設定額外的自動化工作觸發程式，則工作會在自動觸發時執行。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 ACR 工作上啟用及使用使用者指派或系統指派的受控識別。 如需使用受控識別從 ACR 工作存取受保護資源的案例，請參閱：

* [跨登錄驗證](container-registry-tasks-cross-registry-authentication.md)
* [使用儲存在 Azure Key Vault 中的秘密存取外部資源](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
