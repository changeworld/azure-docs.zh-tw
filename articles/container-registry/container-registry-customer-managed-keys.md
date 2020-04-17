---
title: 使用客戶管理的金鑰進行靜態加密
description: 瞭解 Azure 容器註冊表的靜態加密,以及如何使用儲存在 Azure 金鑰保管庫中的客戶託管金鑰加密註冊表
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461747"
---
# <a name="encryption-using-customer-managed-keys"></a>使用客戶管理的金鑰進行加密

在 Azure 容器註冊表中儲存圖像和其他專案時,Azure 會自動使用[服務管理的密鑰](../security/fundamentals/encryption-atrest.md#data-encryption-models)對靜態註冊表內容進行加密。 您可以使用在 Azure 金鑰保管庫中創建和管理的密鑰,使用其他加密層補充默認加密。 本文將引導您使用 Azure CLI 和 Azure 門戶完成這些步驟。

通過與[Azure 金鑰保管庫](../key-vault/general/overview.md)整合,支援使用客戶管理金鑰進行伺服器端加密。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 使用 Azure 金鑰保管庫,還可以審核密鑰使用方式。

此功能在**先進**容器註冊表服務層中可用。 有關註冊表服務層和限制的資訊,請參閱[Azure 容器註冊表 SKU](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前處於預覽狀態,並套用限制[。](#preview-limitations) 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>
   
## <a name="preview-limitations"></a>預覽限制 

* 當前,您只能在創建註冊表時啟用此功能。
* 在註冊表上啟用客戶管理的密鑰後,無法禁用它。
* 使用客戶管理的金鑰加密的註冊表中目前不支援[內容信任](container-registry-content-trust.md)。
* 在使用客戶管理的金鑰加密的註冊表中[,ACR 任務](container-registry-tasks-overview.md)的運行日誌當前僅保留 24 小時。 如果需要將日誌保留更長時間,請參閱[匯出和存儲任務運行日誌](container-registry-tasks-logs.md#alternative-log-storage)的指導。

## <a name="prerequisites"></a>Prerequisites

要使用本文中的 Azure CLI 步驟,需要 Azure CLI 版本 2.2.0 或更高版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="enable-customer-managed-key---cli"></a>開啟客戶管理金鑰 - CLI

### <a name="create-a-resource-group"></a>建立資源群組

如果需要,執行[az 組創建][az-group-create]命令以創建用於創建金鑰保管庫、容器註冊表和其他必需資源的資源組。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

使用[az 識別建立][az-identity-create]指令為 Azure 資源建立使用者分配的[託管識別](../active-directory/managed-identities-azure-resources/overview.md)。 註冊表將使用此標識訪問密鑰保管庫服務。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

在指令輸出中,請注意以下值:`id``principalId`與 。 在後續步驟中,您需要這些值來配置對密鑰保管庫的註冊表訪問。

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

為方便起見,在環境變數中存儲這些值:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>建立金鑰保存庫

使用[az 金鑰保管庫創建][az-keyvault-create]密鑰保管庫以儲存客戶管理的密鑰以進行註冊表加密。 

為了防止意外刪除金鑰或金鑰保管庫而導致的資料遺失,必須啟用以下設定:**軟刪除**與**清除保護**。 以下範例包括這些設定的參數: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>新增金鑰保存原則

為金鑰保管庫配置策略,以便標識可以訪問它。 在以下[az keyvault set 策略][az-keyvault-set-policy]命令中,您將傳遞以前儲存在環境變數中的託管識別的主體 ID。 設定金鑰權**限 以取得**、**解包金鑰**與**包裝金鑰**。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>建立金鑰並取得金鑰 ID

執行[az 金鑰庫金鑰創建][az-keyvault-key-create]命令以在金鑰保管庫中創建金鑰。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

在指令輸出中,記下金鑰的識別`kid`碼 。 在下一步中使用此 ID:

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
為方便起見,將此值存儲在環境變數中:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>使用客戶管理的金鑰建立註冊表

執行[az acr 建立][az-acr-create]指令以建立註冊表並啟用客戶管理的密鑰。 傳遞以前儲存在環境變數中的託管識別主體 ID 和金鑰 ID:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>顯示加密狀態

要顯示是否啟用了客戶託管金鑰的註冊表加密,執行 az [acr 加密顯示][az-acr-encryption-show]指令:

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>開啟客戶管理金鑰 - 門戶

### <a name="create-a-managed-identity"></a>建立託管識別

在 Azure 門戶中為 Azure 資源建立使用者分配的[託管識別](../active-directory/managed-identities-azure-resources/overview.md)。 有關步驟,請參閱[創建使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

請注意託管識別**的資源名稱**。 在後面的步驟中需要此名稱。

![在 Azure 門戶建立使用者分配的託管識別](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>建立金鑰保存庫

有關建立金鑰保管庫的步驟,請參閱[快速入門:使用 Azure 門戶從 Azure 金鑰保管庫設置和檢索機密](../key-vault/secrets/quick-create-portal.md)。

在 **「基本」** 選項卡中為客戶管理的金鑰建立金鑰保管庫時,必須啟用以下保護設定:**軟刪除**與**清除保護**。 這些設置有助於防止意外刪除密鑰或密鑰保管庫而導致的數據丟失。

![在 Azure 門戶建立金鑰保管庫](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>新增金鑰保存原則

為金鑰保管庫配置策略,以便標識可以訪問它。

1. 導航到密鑰保管庫。
1. 選擇 **「設定** > **存取政策> +新增存取政策**。
1. 選擇 **「鍵」權限**,然後選擇 **「獲取**、**取消包裝鍵**」和 **「包裝鍵**」。
1. **選擇「選擇主體**」並選擇使用者分配的託管標識的資源名稱。  
1. 選擇 **'添加**",然後選擇 **"儲存**"。

![建立金鑰保存者存取原則](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>建立金鑰

1. 導航到密鑰保管庫。
1. 選擇**設定** > **鍵**。
1. 選擇 **「生成/匯入**」並輸入鍵的唯一名稱。
1. 接受剩餘的預設值,然後選擇 **"創建**"。
1. 建立後,選擇金鑰並記下當前密鑰版本。

### <a name="create-azure-container-registry"></a>建立 Azure Container Registry

1. 選擇 **「創建資源** > **容器** > **容器註冊表**」。
1. 在 **"基本'** 選項卡中,選擇或創建資源組,然後輸入註冊表名稱。 在**SKU**中,選擇 **「高級**」。。
1. 在 **「加密」** 選項卡中,在**客戶管理的金鑰**中,選擇**開啟**。
1. 在 **「識別」** 中,選擇您創建的託管標識。
1. 加密**金鑰**中,**從金鑰保存庫中選擇**。
1. 在「**從 Azure 金鑰庫中選擇金鑰」** 視窗中,選擇在上一節中建立的金鑰保管庫、金鑰和版本。
1. 在 **「加密**」選項卡中,選擇 **「審閱 + 創建**」 。
1. 選擇 **「創建**」以部署註冊表實例。

![在 Azure 入口網站中建立容器登錄](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

要查看門戶中的註冊表的加密狀態,請導航到註冊表。 在 **「設定」** 下,選擇 **「加密(預覽)」。。**

## <a name="enable-customer-managed-key---template"></a>開啟客戶管理金鑰 - 樣本

您還可以使用資源管理器範本創建註冊表並使用客戶管理的密鑰啟用加密。 

以下範本將創建新的容器註冊表和使用者分配的託管標識。 將以下內容複製到新檔,並使用檔名(如`CMKtemplate.json`)儲存它。

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

依前幾節中的步驟建立以下資源:

* 依名稱識別金鑰保存庫
* 金鑰保管庫金鑰,由金鑰 ID 識別

執行以下[az 組部署建立][az-group-deployment-create]命令,使用前面的樣本檔創建註冊表。 如果指示,請提供新的註冊表項名稱和託管標識名稱,以及您創建的密鑰保管庫名稱和密鑰 ID。 

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

要顯示註冊表加密的狀態,運行 [az acr 加密顯示狀態][az-acr 加密-顯示狀態]命令:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>使用註冊表

啟用註冊表使用客戶託管金鑰加密數據後,可以執行與未使用客戶託管密鑰加密的註冊表執行的相同註冊表操作。 例如,您可以使用註冊表進行身份驗證並推送 Docker 映射。 請參考[「推送」 的範例命令並拉取影像](container-registry-get-started-docker-cli.md)。

## <a name="rotate-key"></a>旋轉鍵

您可以根據合規性策略在 Azure 密鑰保管庫中輪換客戶管理的密鑰。 建立新密鑰,然後更新註冊表以使用新密鑰加密數據。 可以使用 Azure CLI 或在門戶中執行這些步驟。

例如,執行[az 金鑰庫鍵建立][az-keyvault-key-create]指令以建立新金鑰:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

然後執行[az acr 加密旋轉鍵][az-acr-encryption-rotate-key]指令,傳遞您以前設定的託管識別的新金鑰 ID 與主體 ID:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>復原金鑰

通過更改金鑰保管庫上的訪問策略或刪除金鑰來撤消客戶管理的加密密鑰。 例如,使用[az keyvault 刪除策略][az-keyvault-delete-policy]指令更改註冊表使用的託管標識的存取策略。 例如：

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

由於註冊表無法訪問加密密鑰,因此撤銷金鑰可有效地阻止對所有註冊表數據的訪問。 如果啟用了對金鑰的訪問或還原了已刪除的密鑰,則註冊表將選取該密鑰,以便您可以再次訪問加密的註冊表數據。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 中靜態加密](../security/fundamentals/encryption-atrest.md)的更多詳細資訊。
* 詳細瞭解存取原則以及如何[保護對金鑰保存的權限 。](../key-vault/general/secure-your-key-vault.md)
* 要提供有關 Azure 容器註冊表的客戶管理金鑰的回饋,請造[訪 ACR GitHub 網站](https://aka.ms/acr/issues)。


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
