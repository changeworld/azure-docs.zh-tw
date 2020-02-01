---
title: 在容器群組中啟用受控識別
description: 瞭解如何在 Azure 容器實例中啟用可向其他 Azure 服務進行驗證的受控識別
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901941"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>如何搭配 Azure 容器執行個體使用受控識別

使用[適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)在「Azure 容器執行個體」中執行與其他 Azure 服務互動的程式碼，而無須在程式碼中保有任何秘密或認證。 此功能會為「Azure 容器執行個體」部署提供一個在 Azure Active Directory 中自動管理的身分識別。

在本文中，您會了解「Azure 容器執行個體」中的受控識別，以及如何：

> [!div class="checklist"]
> * 在容器群組中啟用使用者指派或系統指派的身分識別
> * 將身分識別存取權授與 Azure 金鑰保存庫
> * 使用受控識別從執行中的容器存取金鑰保存庫

請改寫範例，以啟用及使用「Azure 容器執行個體」中的身分識別來存取其他 Azure 服務。 這些範例為互動式範例。 不過，實際上您的容器映像會執行程式碼來存取 Azure 服務。

> [!NOTE]
> 目前您無法在已部署至虛擬網路的容器群組中使用受控識別。

## <a name="why-use-a-managed-identity"></a>為什麼要使用受控識別？

您可以在執行中的容器使用受控身分識別，向任何[支援 Azure AD 驗證的服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)進行驗證，而無須在您的容器程式碼中管理認證。 對於不支援 AD 驗證的服務，您可以將秘密儲存在 Azure 金鑰保存庫中，並使用受控識別來存取金鑰保存庫，以取得認證。 如需有關使用受控識別的詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。 目前，Azure 容器實例上的受控識別僅支援 Linux 容器，但尚未與 Windows 容器搭配使用。
>  

### <a name="enable-a-managed-identity"></a>啟用受控識別

 在「Azure 容器執行個體」中，從 REST API 2018-10-01 版及對應的 SDK 和工具開始，便支援適用於 Azure 資源的受控識別。 當您建立容器群組時，可藉由設定 [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) 屬性來啟用一或多個受控識別。 您也可以在容器群組執行後啟用或更新受控識別-任一動作都會導致容器群組重新開機。 若要在新的或現有的容器群組上設定身分識別，請使用 Azure CLI、Resource Manager 範本或 YAML 檔案。 

「Azure 容器執行個體」同時支援兩種類型的受控 Azure 身分識別：使用者指派和系統指派。 在容器群組上，您可以啟用一個系統指派的身分識別，一或多個使用者指派的身分識別，或是同時啟用這兩種身分識別。 

* **使用者指派的**受控識別會以獨立 Auzre 資源的形式，建立在使用中訂用帳戶所信任的 Azure AD 租用戶中。 建立身分識別之後，即可將該身分識別指派給一或多個 Azure 資源 (在「Azure 容器執行個體」或其他 Azure 服務中)。 使用者所指派身分識別的生命週期，與獲指派此身分識別之容器群組或其他服務資源的生命週期，兩者是分開管理的。 此行為在「Azure 容器執行個體」中特別有用。 由於身分識別的生命週期會比容器群組的生命週期長，因此您可以搭配其他標準設定來重複使用它，以提高您容器群組部署的重複使用性。

* **系統指派的**受控識別是在「Azure 容器執行個體」中的容器群組上直接啟用的。 啟用此身分識別時，Azure 會在執行個體訂用帳戶所信任的 Azure AD 租用戶中，為該群組建立身分識別。 建立身分識別之後，就會在該容器群組的每個容器中佈建認證。 系統所指派身分識別的生命週期會直接繫結至啟用該身分識別的容器群組。 如果將群組刪除，Azure 就會自動清除 Azure AD 中的認證和身分識別。

### <a name="use-a-managed-identity"></a>建立受控識別

若要使用受控識別，身分識別一開始必須被授與訂用帳戶中一或多個 Azure 服務資源（例如 web 應用程式、金鑰保存庫或儲存體帳戶）的存取權。 若要從執行中的容器存取 Azure 資源，您的程式碼必須向 Azure AD 端點取得「存取權杖」。 接著，您的程式碼會藉由呼叫將存取權杖傳送給支援 Azure AD 驗證的服務。 

在執行中的容器中使用受控識別基本上與在 Azure VM 中使用身分識別相同。 如需了解如何使用[權杖](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)、[Azure PowerShell 或 Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) 或 [Azure SDKs](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)，請參閱 VM 指引。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝並使用 CLI，本文會要求您執行 Azure CLI 2.0.49 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-key-vault"></a>建立 Azure 金鑰保存庫

本文中的範例會使用 Azure 容器實例中的受控識別來存取 Azure 金鑰保存庫密碼。 

首先，使用下列 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 命令，在 *eastus* 位置中建立一個名為 *myResourceGroup* 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令來建立金鑰保存庫。 請務必指定唯一的金鑰保存庫名稱。 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

使用[az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)命令，將範例密碼儲存在金鑰保存庫中：

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

繼續進行下列範例，以在 Azure 容器實例中使用使用者指派或系統指派的受控識別來存取金鑰保存庫。

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>範例1：使用使用者指派的身分識別來存取 Azure 金鑰保存庫

### <a name="create-an-identity"></a>建立身分識別

首先，使用 [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 命令，在您的訂用帳戶中建立身分識別。 您可以使用與用來建立金鑰保存庫相同的資源群組，或使用另一個。

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

若要在接下來的步驟中使用身分識別，請使用 [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) 命令，以將身分識別的服務主體識別碼和資源識別碼儲存在變數中。

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>在容器群祖上啟用使用者指派的身分識別

執行下列[az container create](/cli/azure/container?view=azure-cli-latest#az-container-create)命令，以根據 Microsoft 的 `azure-cli` 映射來建立容器實例。 這個範例會提供單一容器群組，您可以用互動方式執行 Azure CLI 來存取其他 Azure 服務。 在本節中，只會使用基底 Ubuntu 作業系統。 

`--assign-identity` 參數會將使用者指派的受控識別傳遞給群組。 長時間執行的命令會讓容器保持執行。 這個範例會使用用來建立金鑰保存庫的相同資源群組，但您可以指定不同的資源群組。

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

您應該會在幾秒內獲得 Azure CLI 的回應，指出部署已。 請使用 [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) 命令來檢查其狀態。

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

輸出中的 `identity` 區段看起來會與以下類似，顯示容器群組中已設定身分識別。 `userAssignedIdentities` 底下的 `principalID` 是您在 Azure Active Directory 中所建立身分識別的服務主體：

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>將金鑰保存庫的存取權授與使用者指派的身分識別

執行下列[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest)命令，在金鑰保存庫上設定存取原則。 下列範例可讓使用者指派的身分識別從金鑰保存庫取得秘密：

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>使用使用者指派的身分識別從金鑰保存庫取得秘密

現在您可以使用執行中容器實例內的受控識別來存取金鑰保存庫。 首先在容器中啟動 bash shell：

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

在容器的 Bash 殼層中執行下列命令。 若要使用 Azure Active Directory 來取得存取權杖，以向 key vault 進行驗證，請執行下列命令：

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

輸出：

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

若要將存取權杖儲存在變數中以在後續命令中用來進行驗證，請執行下列命令：

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

現在，使用存取權杖向 key vault 進行驗證並讀取秘密。 請務必替換 URL ( *https://mykeyvault.vault.azure.net/...* ) 中您的金鑰保存庫名稱：

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

回應看起來會與以下類似，其中會顯示祕密。 在您的程式碼中，您會剖析此輸出來取得祕密。 接著，請在後續作業中使用此祕密來存取另一個 Azure 資源。

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>範例2：使用系統指派的身分識別來存取 Azure 金鑰保存庫

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>在容器群祖上啟用系統指派的身分識別

執行下列[az container create](/cli/azure/container?view=azure-cli-latest#az-container-create)命令，以根據 Microsoft 的 `azure-cli` 映射來建立容器實例。 這個範例會提供單一容器群組，您可以用互動方式執行 Azure CLI 來存取其他 Azure 服務。 

不含任何其他值的 `--assign-identity` 參數可在群組上啟用系統指派的受控識別。 身分識別的範圍是容器群組的資源群組。 長時間執行的命令會讓容器保持執行。 這個範例會使用用來建立金鑰保存庫的相同資源群組，但您可以指定不同的資源群組。

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

您應該會在幾秒內獲得 Azure CLI 的回應，指出部署已。 請使用 [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) 命令來檢查其狀態。

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

輸出中的 `identity` 區段看起來會與以下類似，顯示已在 Azure Active Directory 中建立系統指派的身分識別：

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

請將變數設定為身分識別之 `principalId` (服務主體識別碼) 的值，以在稍後的步驟中使用。

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>將金鑰保存庫的存取權授與容器群組

執行下列[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest)命令，在金鑰保存庫上設定存取原則。 下列範例可讓系統管理的身分識別從金鑰保存庫取得秘密：

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>使用容器群組識別從金鑰保存庫取得秘密

現在您可以使用受控識別來存取執行中容器實例內的金鑰保存庫。 首先在容器中啟動 bash shell：

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

在容器的 Bash 殼層中執行下列命令。 首先，使用受控識別登入 Azure CLI：

```bash
az login --identity
```

從執行中的容器，取出金鑰保存庫中的秘密：

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

會抓取秘密的值：

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>使用 Resource Manager 範本來啟用受控識別

若要使用 [Resource Manager 範本](container-instances-multi-container-group.md)在容器群組中啟用受控識別，請使用 `ContainerGroupIdentity` 物件來設定 `Microsoft.ContainerInstance/containerGroups` 物件的 `identity` 屬性。 下列程式碼片段顯示針對不同案例設定的 `identity` 屬性。 請參閱 [Resource Manager 範本參考](/azure/templates/microsoft.containerinstance/containergroups)。 指定 `2018-10-01`的最小 `apiVersion`。

### <a name="user-assigned-identity"></a>使用者指派的身分識別

使用者指派的身分識別是一個下列形式的資源識別碼：

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

您可以啟用一或多個使用者指派的身分識別。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>系統指派的身分識別

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>系統指派與使用者指派的身分識別

在容器群組上，您可以啟用一個系統指派的身分識別及一或多個使用者指派的身分識別。

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>使用 YAML 檔案來啟用受控識別

若要在使用 [YAML 檔案](container-instances-multi-container-yaml.md)來部署的容器群組中啟用受控識別，請包含下列 YAML。
指定 `2018-10-01`的最小 `apiVersion`。

### <a name="user-assigned-identity"></a>使用者指派的身分識別

使用者指派的身分識別是一個下列形式的資源識別碼 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

您可以啟用一或多個使用者指派的身分識別。

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>系統指派的身分識別

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>系統指派與使用者指派的身分識別

在容器群組上，您可以啟用一個系統指派的身分識別及一或多個使用者指派的身分識別。

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解「Azure 容器執行個體」中的受控識別，以及如何：

> [!div class="checklist"]
> * 在容器群組中啟用使用者指派或系統指派的身分識別
> * 將身分識別存取權授與 Azure 金鑰保存庫
> * 使用受控識別從執行中的容器存取金鑰保存庫

* 深入了解 [Azure 資源的受控識別](/azure/active-directory/managed-identities-azure-resources/)。

* 請參閱使用受控識別從 Azure 容器實例存取金鑰保存庫的[Azure GO SDK 範例](https://medium.com/@samkreter/c98911206328)。
