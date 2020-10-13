---
title: 使用受控識別進行驗證
description: 在您的私人˙容器登錄中，使用使用者指派或系統指派的受控 Azure 身分識別提供映像的存取權。
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9a144f0e865cfc9bf857752eed65dbe5cda88bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253457"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>使用 Azure 受控識別向 Azure 容器登錄進行驗證 

使用 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md) 從另一個 Azure 資源向 Azure 容器登錄進行驗證，不需提供或管理登錄認證。 例如在 Linux VM 上設定使用者指派或系統指派的受控識別，以從您的容器登錄存取容器映像，就像使用公用登錄一樣容易。

您可以從本文了解受控識別，以及如何：

> [!div class="checklist"]
> * 在 Azure VM 中啟用使用者指派或系統指派的身分識別
> * 授與 Azure 容器登錄的身分識別存取權
> * 使用受控識別存取登錄及提取容器映像 

若要建立 Azure，本文需要您執行 Azure CLI 2.0.55 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

若要設定容器登錄並將容器映像推送至登錄，您也必須在本機安裝 Docker。 Docker 提供可輕鬆在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。

## <a name="why-use-a-managed-identity"></a>為什麼要使用受控識別？

適用於 Azure 資源的受控識別會在 Azure Active Directory (Azure AD) 中為 Azure 服務提供自動的受控識別。 您可以設定[某些 Azure 資源](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)，包括虛擬機器與受控識別。 然後使用身分識別存取其他 Azure 資源，不需要使用程式碼或指令碼傳遞認證。

受控身分識別有兩種：

* *使用者指派的身分識別*，您可以指派給多個資源並視需要保存。 使用者指派的身分識別目前處於預覽狀態。

* *系統受控識別*，這對特定資源 (例如單一虛擬機器) 而言是唯一的，且存留時間與該資源一致。

以受控識別設定 Azure 資源後，請為此受控識別提供對另一項資源的存取權，就像任何安全性主體一樣。 例如，指派一個角色給受控識別，該角色可具備 Azure 中私人登錄的提取、推送和提取，或其他權限。  (如需登錄角色的完整清單，請參閱 [Azure Container Registry 角色和許可權](container-registry-roles.md)。 ) 您可以提供一或多個資源的身分識別存取權。

然後使用身分識別向任何[支援 Azure AD 驗證的服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)進行驗證，不需要任何您程式碼中的認證。 若要使用身分識別從虛擬機器存取 Azure 容器登錄，可以使用 Azure Resource Manager 驗證。 請依據您的案例選擇如何使用受控識別進行驗證：

* 使用 HTTP 或 REST 呼叫以程式設計方式[取得 Azure AD 存取權杖](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

* 使用 [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* 使用身分識別[登入 Azure CLI 或 PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)。 

## <a name="create-a-container-registry"></a>建立容器登錄庫

如果您還沒有 Azure 容器登錄，請建立登錄並將範例容器映像推送至該登錄。 如需相關步驟，請參閱 [快速入門：使用 Azure CLI 建立私用容器](container-registry-get-started-azure-cli.md)登錄。

本文假設您的登錄中已儲存 `aci-helloworld:v1` 容器映像。 範例中使用名為 *myContainerRegistry* 的登錄。 請在稍後的步驟中以您自己的登錄和映像名稱取代。

## <a name="create-a-docker-enabled-vm"></a>建立啟用 Docker 的 VM

建立啟用 Docker 的 Ubuntu 虛擬機器。 您也需要在虛擬機器上安裝 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 如果您已經有 Azure 虛擬機器，請略過此步驟以建立虛擬機器。

使用 [az vm create][az-vm-create] 部署一個預設 Ubuntu Azure 虛擬機器。 下列範例會在名為 myResourceGroup** 的現有資源群組中建立名為 myDockerVM** 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

系統需要花幾分鐘的時間來建立 VM。 命令完成之後，請記下 Azure CLI 所顯示的 `publicIpAddress`。 請使用此位址與 VM 建立 SSH 連線。

### <a name="install-docker-on-the-vm"></a>在 VM 上安裝 Docker

在 VM 執行之後，與 VM 建立 SSH 連線。 以您 VM 的公用 IP 位址取代 *publicIpAddress*。

```bash
ssh azureuser@publicIpAddress
```

執行下列命令以在該 VM 上安裝 Docker：

```bash
sudo apt update
sudo apt install docker.io -y
```

安裝之後，執行下列命令確認 Docker 在 VM 上正常執行：

```bash
sudo docker run -it hello-world
```

輸出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>安裝 Azure CLI

請遵循[使用 apt 安裝 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步驟在您的 Ubuntu 虛擬機器上安裝 Azure CLI。 對於本文，請確定您安裝的是 2.0.55 版或更新版本。

結束 SSH 工作階段。

## <a name="example-1-access-with-a-user-assigned-identity"></a>範例1：使用使用者指派的身分識別進行存取

### <a name="create-an-identity"></a>建立身分識別

使用 [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 命令，在您的訂用帳戶中建立身分識別。 您可以使用先前用來建立容器登錄或虛擬機器的同一個資源群組，或使用其他資源群組。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

若要在接下來的步驟中設定身分識別，請使用 [az identity show][az-identity-show] 命令，以將身分識別的資源識別碼與服務主體識別碼儲存在變數中。

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

當您在稍後的步驟中，當您從虛擬機器登入 CLI 時需要身分識別的識別碼時，請顯示下列值：

```bash
echo $userID
```

識別碼的格式為：

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>使用身分識別設定 VM

以下 [az vm identity assign][az-vm-identity-assign] 命令會使用使用者指派的身分識別設定您的 Docker VM：

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>授與容器登錄的身分識別存取權

現在請設定身分識別以存取容器登錄。 第一次使用 [az acr show][az-acr-show]命令取得登錄的資源識別碼：

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

請使用 [az role assignment create][az-role-assignment-create] 命令指派 AcrPull 角色給登錄。 此角色提供對登錄的 [提取權限](container-registry-roles.md)。 若要提供提取和推送權限，請指派 ACRPush 角色。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>使用身分識別存取登錄

透過 SSH 連線到使用身分識別設定的 Docker 虛擬機器。 使用 VM 上安裝的 Azure CLI 執行下列 Azure CLI 命令。

首先，使用您在 VM 上設定的身分識別，以 [az login][az-login]驗證 Azure CLI。 對於 `<userID>`，請取代為您在上一個步驟中擷取的身分識別的識別碼。 

```azurecli
az login --identity --username <userID>
```

然後，使用 [az acr login 向登錄][az-acr-login]進行驗證。 使用此命令時，CLI 會使用您執行 `az login` 時建立的 Active Directory 權杖順暢地向容器登錄驗證您的工作階段。 (根據 VM 設定，您可能需要使用 `sudo` 執行此命令與 docker 命令。)

```azurecli
az acr login --name myContainerRegistry
```

您應該會看見 `Login succeeded` 訊息。 接著可以執行 `docker` 命令，不需要提供認證。 例如執行 [docker 提取][docker-pull] 以提取 `aci-helloworld:v1` 映像，指定您登錄的登入伺服器名稱。 登入伺服器名稱包含您的容器登錄名稱 (全部小寫)，後面接著 `.azurecr.io` - 例如 `mycontainerregistry.azurecr.io`。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>範例2：使用系統指派的身分識別進行存取

### <a name="configure-the-vm-with-a-system-managed-identity"></a>使用系統受控身分識別設定 VM

以下 [az vm 身分識別指派][az-vm-identity-assign] 命令會使用系統指派的身分識別設定您的 Docker VM：

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

請使用 [az vm show][az-vm-show] 命令將變數設定為 VM 身分識別 `principalId` (服務主體識別碼) 的值，以在稍後的步驟中使用。

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>授與容器登錄的身分識別存取權

現在請設定身分識別以存取容器登錄。 第一次使用 [az acr show][az-acr-show]命令取得登錄的資源識別碼：

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

請使用 [az 角色指派建立][az-role-assignment-create] 命令指派 AcrPull 角色給身分識別。 此角色提供對登錄的 [提取權限](container-registry-roles.md)。 若要提供提取和推送權限，請指派 ACRPush 角色。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>使用身分識別存取登錄

透過 SSH 連線到使用身分識別設定的 Docker 虛擬機器。 使用 VM 上安裝的 Azure CLI 執行下列 Azure CLI 命令。

首先，使用在 VM 上系統指派的身分識別，以 [az login][az-login]驗證 Azure CLI。

```azurecli
az login --identity
```

然後，使用 [az acr login 向登錄][az-acr-login]進行驗證。 使用此命令時，CLI 會使用您執行 `az login` 時建立的 Active Directory 權杖順暢地向容器登錄驗證您的工作階段。 (根據 VM 設定，您可能需要使用 `sudo` 執行此命令與 docker 命令。)

```azurecli
az acr login --name myContainerRegistry
```

您應該會看見 `Login succeeded` 訊息。 接著可以執行 `docker` 命令，不需要提供認證。 例如執行 [docker 提取][docker-pull] 以提取 `aci-helloworld:v1` 映像，指定您登錄的登入伺服器名稱。 登入伺服器名稱包含您的容器登錄名稱 (全部小寫)，後面接著 `.azurecr.io` - 例如 `mycontainerregistry.azurecr.io`。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```
> [!NOTE]
> 系統指派的受控服務識別可以用來與 Acr 互動，App Service 可以使用系統指派的受控服務識別。 不過，您無法結合這些，因為 App Service 無法使用 MSI 來與 ACR 交談。 唯一的方法是在 ACR 上啟用系統管理員，並使用系統管理員使用者名稱/密碼。

## <a name="next-steps"></a>後續步驟

您已在本文中了解搭配 Azure 容器登錄使用受控識別的方式以及如何：

> [!div class="checklist"]
> * 在 Azure VM 中啟用使用者指派或系統指派的身分識別
> * 授與 Azure 容器登錄的身分識別存取權
> * 使用受控識別存取登錄及提取容器映像

* 深入瞭解 [Azure 資源的受控](../active-directory/managed-identities-azure-resources/index.yml)識別。


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
