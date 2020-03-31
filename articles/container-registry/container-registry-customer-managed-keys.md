---
title: 使用客戶管理的金鑰進行靜態加密
description: 瞭解 Azure 容器註冊表的靜態加密，以及如何使用存儲在 Azure 金鑰保存庫中的客戶託管金鑰加密註冊表
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498958"
---
# <a name="encryption-using-customer-managed-keys"></a>使用客戶管理的金鑰進行加密

在 Azure 容器註冊表中存儲圖像和其他專案時，Azure 會自動使用[服務管理的金鑰](../security/fundamentals/encryption-atrest.md#data-encryption-models)對靜態註冊表內容進行加密。 您可以使用在 Azure 金鑰保存庫中創建和管理的金鑰，使用其他加密層補充預設加密。 本文將引導您使用 Azure CLI 和 Azure 門戶完成這些步驟。

通過與[Azure 金鑰保存庫](../key-vault/key-vault-overview.md)集成，支援使用客戶管理金鑰進行伺服器端加密。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 使用 Azure 金鑰保存庫，還可以審核金鑰使用方式。

此功能在**高級**容器註冊表服務層中可用。 有關註冊表服務層和限制的資訊，請參閱[Azure 容器註冊表 SKU](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能當前處於預覽狀態，並應用一些[限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>
   
## <a name="preview-limitations"></a>預覽限制 

* 當前，您只能在創建註冊表時啟用此功能。
* 在註冊表上啟用客戶管理的金鑰後，無法禁用它。
* 使用客戶管理的金鑰加密的註冊表中當前不支援[內容信任](container-registry-content-trust.md)。
* 在使用客戶管理的金鑰加密的註冊表中[，ACR 任務](container-registry-tasks-overview.md)的運行日誌當前僅保留 24 小時。 如果需要將日誌保留更長時間，請參閱[匯出和存儲任務運行日誌](container-registry-tasks-logs.md#alternative-log-storage)的指導。

## <a name="prerequisites"></a>Prerequisites

要使用本文中的 Azure CLI 步驟，需要 Azure CLI 版本 2.2.0 或更高版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="enable-customer-managed-key---cli"></a>啟用客戶管理金鑰 - CLI

### <a name="create-a-resource-group"></a>建立資源群組

如果需要，運行[az 組創建][az-group-create]命令以創建用於創建金鑰保存庫、容器註冊表和其他必需資源的資源組。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

使用[az 標識創建][az-identity-create]命令為 Azure 資源創建使用者分配的[託管標識](../active-directory/managed-identities-azure-resources/overview.md)。 註冊表將使用此標識訪問金鑰保存庫服務。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

在命令輸出中，請注意以下值：`id`和`principalId`。 在後續步驟中，您需要這些值來配置對金鑰保存庫的註冊表訪問。

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

為方便起見，在環境變數中存儲這些值：

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>建立金鑰保存庫

使用[az 金鑰保存庫創建][az-keyvault-create]金鑰保存庫以存儲客戶管理的金鑰以進行註冊表加密。 

為了防止意外刪除金鑰或金鑰保存庫而導致的資料丟失，必須啟用以下設置：**虛刪除**和**清除保護**。 以下示例包括這些設置的參數： 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>添加金鑰保存庫訪問策略

為金鑰保存庫配置策略，以便標識可以訪問它。 在以下[az keyvault set 策略][az-keyvault-set-policy]命令中，您將傳遞以前存儲在環境變數中的託管標識的主體 ID。 設置金鑰許可權**以獲取**、**解包金鑰**和**包裝金鑰**。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>創建金鑰並獲取金鑰識別碼

運行[az 金鑰庫金鑰創建][az-keyvault-key-create]命令以在金鑰保存庫中創建金鑰。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

在命令輸出中，記下金鑰的 ID `kid`。 在下一步中使用此 ID：

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
為方便起見，將此值存儲在環境變數中：

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>使用客戶管理的金鑰創建註冊表

運行[az acr 創建][az-acr-create]命令以創建註冊表並啟用客戶管理的金鑰。 傳遞以前存儲在環境變數中的託管標識主體 ID 和金鑰識別碼：

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>顯示加密狀態

要顯示是否啟用了客戶託管金鑰的註冊表加密，運行 az [acr 加密顯示][az-acr-encryption-show]命令：

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>啟用客戶管理金鑰 - 門戶

### <a name="create-a-managed-identity"></a>創建託管標識

在 Azure 門戶中為 Azure 資源創建使用者分配的[託管標識](../active-directory/managed-identities-azure-resources/overview.md)。 有關步驟，請參閱[創建使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

請注意託管標識**的資源名稱**。 在後面的步驟中需要此名稱。

![在 Azure 門戶中創建使用者分配的託管標識](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>建立金鑰保存庫

有關創建金鑰保存庫的步驟，請參閱[快速入門：使用 Azure 門戶從 Azure 金鑰保存庫設置和檢索機密](../key-vault/quick-create-portal.md)。

在 **"基本"** 選項卡中為客戶管理的金鑰創建金鑰保存庫時，必須啟用以下保護設置：**虛刪除**和**清除保護**。 這些設置有助於防止意外刪除金鑰或金鑰保存庫而導致的資料丟失。

![在 Azure 門戶中創建金鑰保存庫](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>添加金鑰保存庫訪問策略

為金鑰保存庫配置策略，以便標識可以訪問它。

1. 導航到金鑰保存庫。
1. 選擇 **"設置** > **訪問策略> +添加訪問策略**。
1. 選擇 **"鍵"許可權**，然後選擇 **"獲取**、**取消包裝鍵**"和 **"包裝鍵**"。
1. **選擇"選擇主體**"並選擇使用者分配的託管標識的資源名稱。  
1. 選擇 **"添加**"，然後選擇 **"保存**"。

![創建金鑰保存庫訪問策略](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>建立金鑰

1. 導航到金鑰保存庫。
1. 選擇**設置** > **鍵**。
1. 選擇 **"生成/導入**"並輸入鍵的唯一名稱。
1. 接受剩餘的預設值，然後選擇 **"創建**"。
1. 創建後，選擇金鑰並記下當前金鑰版本。

### <a name="create-azure-container-registry"></a>建立 Azure Container Registry

1. 選擇 **"創建資源** > **容器** > **容器註冊表**"。
1. 在 **"基本"** 選項卡中，選擇或創建資源組，然後輸入註冊表名稱。 在**SKU**中，選擇 **"高級**"。
1. 在 **"加密"** 選項卡中，在**客戶管理的金鑰**中，選擇**啟用**。
1. 在 **"標識"** 中，選擇您創建的託管標識。
1. 在**加密金鑰**中，**從金鑰保存庫中選擇**。
1. 在"**從 Azure 金鑰庫中選擇金鑰"** 視窗中，選擇在上一節中創建的金鑰保存庫、金鑰和版本。
1. 在 **"加密**"選項卡中，選擇 **"審閱 + 創建**"。
1. 選擇 **"創建**"以部署註冊表實例。

![在 Azure 入口網站中建立容器登錄](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

要查看門戶中的註冊表的加密狀態，請導航到註冊表。 在 **"設置"** 下，選擇 **"加密（預覽）"。**

## <a name="enable-customer-managed-key---template"></a>啟用客戶管理金鑰 - 範本

您還可以使用資源管理器範本創建註冊表並使用客戶管理的金鑰啟用加密。 

以下範本將創建新的容器註冊表和使用者分配的託管標識。 將以下內容複寫到新檔，並使用檔案名（如`CMKtemplate.json`）保存它。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

按照前幾節中的步驟創建以下資源：

* 按名稱標識的金鑰保存庫
* 金鑰保存庫金鑰，由金鑰識別碼 標識

運行以下[az 組部署創建][az-group-deployment-create]命令，使用前面的範本檔創建註冊表。 如果指示，請提供新的登錄機碼名稱和託管標識名稱，以及您創建的金鑰保存庫名稱和金鑰識別碼。 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>顯示加密狀態

要顯示註冊表加密的狀態，運行 [az acr 加密顯示狀態][az-acr 加密-顯示狀態]命令：

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>使用註冊表

啟用註冊表使用客戶託管金鑰加密資料後，可以執行與未使用客戶託管金鑰加密的註冊表執行的相同註冊表操作。 例如，您可以使用註冊表進行身份驗證並推送 Docker 映射。 請參閱["推送"中的示例命令並拉取圖像](container-registry-get-started-docker-cli.md)。

## <a name="rotate-key"></a>旋轉鍵

您可以根據合規性策略在 Azure 金鑰保存庫中輪換客戶管理的金鑰。 創建新金鑰，然後更新註冊表以使用新金鑰加密資料。 可以使用 Azure CLI 或在門戶中執行這些步驟。

例如，運行[az 金鑰庫鍵創建][az-keyvault-key-create]命令以創建新金鑰：

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

然後運行[az acr 加密旋轉鍵][az-acr-encryption-rotate-key]命令，傳遞您以前配置的託管標識的新金鑰識別碼 和主體 ID：

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>撤銷金鑰

通過更改金鑰保存庫上的訪問策略或刪除金鑰來撤銷客戶管理的加密金鑰。 例如，使用[az keyvault 刪除策略][az-keyvault-delete-policy]命令更改註冊表使用的託管標識的訪問策略。 例如：

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

由於註冊表無法訪問加密金鑰，因此撤銷金鑰可有效地阻止對所有註冊表資料的訪問。 如果啟用了對金鑰的訪問或還原了已刪除的金鑰，則註冊表將選取該金鑰，以便您可以再次訪問加密的註冊表資料。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 中靜態加密](../security/fundamentals/encryption-atrest.md)的更多詳細資訊。
* 詳細瞭解訪問策略以及如何[保護對金鑰保存庫的訪問](../key-vault/key-vault-secure-your-key-vault.md)。
* 要提供有關 Azure 容器註冊表的客戶管理金鑰的回饋，請訪問[ACR GitHub 網站](https://aka.ms/acr/issues)。


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
