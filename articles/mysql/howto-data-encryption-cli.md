---
title: 資料加密-Azure CLI-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用 Azure CLI 設定和管理適用於 MySQL 的 Azure 資料庫的資料加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.openlocfilehash: f73910c528ff65a8c537e779a028860d64561d05
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386719"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>使用 Azure CLI 適用於 MySQL 的 Azure 資料庫的資料加密

瞭解如何使用 Azure CLI 來設定和管理適用於 MySQL 的 Azure 資料庫的資料加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 建立金鑰保存庫和金鑰，以用於客戶管理的金鑰。 此外，也請在金鑰保存庫上啟用清除保護和虛刪除。

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true -enable-purge-protection true
  ```

* 在建立的 Azure Key Vault 中，建立將用於適用於 MySQL 的 Azure 資料庫資料加密的金鑰。

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* 若要使用現有的金鑰保存庫，它必須具有下列屬性，才能用來作為客戶管理的金鑰：

  * [虛刪除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保護](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 金鑰必須具有下列屬性，才能當做客戶管理的金鑰使用：
  * 沒有到期日
  * 未停用
  * 執行**取得**、**包裝**、**解除**包裝作業

## <a name="set-the-right-permissions-for-key-operations"></a>設定金鑰作業的正確許可權

1. 有兩種方式可取得適用於 MySQL 的 Azure 資料庫的受控識別。

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>建立具有受控識別的新適用於 MySQL 的 Azure 資料庫伺服器。

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>更新現有的適用於 MySQL 的 Azure 資料庫伺服器，以取得受控識別。

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. 設定**主體**的**金鑰許可權**（**取得**、包裝、解除**包裝** **），** 這是 MySQL 伺服器的名稱。

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的資料加密

1. 使用在 Azure Key Vault 中建立的金鑰，為適用於 MySQL 的 Azure 資料庫啟用資料加密。

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    金鑰 url：`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>針對還原或複本伺服器使用資料加密

在「適用於 MySQL 的 Azure 資料庫」使用儲存於 Key Vault 的客戶管理金鑰加密之後，任何新建立的伺服器複本也會一併加密。 您可以透過本機或異地還原作業，或透過複本（本機/跨區域）作業來建立此新複本。 因此，對於已加密的 MySQL 伺服器，您可以使用下列步驟來建立加密的已還原伺服器。

### <a name="creating-a-restoredreplica-server"></a>建立還原/複本伺服器

* [建立還原伺服器](howto-restore-server-cli.md) 
* [建立讀取複本伺服器](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>伺服器一旦還原，就會將還原的伺服器重新驗證資料加密

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>用於適用於 MySQL 的 Azure 資料庫之金鑰的額外功能

### <a name="get-the-key-used"></a>取得使用的金鑰

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

金鑰 url：`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>列出使用的金鑰

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>捨棄正在使用的金鑰

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>使用 Azure Resource Manager 範本來啟用資料加密

除了 Azure 入口網站以外，您也可以針對新的和現有的伺服器使用 Azure Resource Manager 範本，在您的適用於 MySQL 的 Azure 資料庫伺服器上啟用資料加密。

### <a name="for-a-new-server"></a>針對新的伺服器

使用其中一個預先建立的 Azure Resource Manager 範本來布建已啟用資料加密的伺服器：[使用資料加密的範例](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

此 Azure Resource Manager 範本會建立適用於 MySQL 的 Azure 資料庫伺服器，並使用傳遞做為參數的**KeyVault**和**金鑰**來啟用伺服器上的資料加密。

### <a name="for-an-existing-server"></a>針對現有的伺服器

此外，您可以使用 Azure Resource Manager 範本來啟用現有適用於 MySQL 的 Azure 資料庫伺服器上的資料加密。

* 傳遞您稍早在 properties 物件的屬性下複製之 Azure Key Vault 金鑰的資源識別碼 `Uri` 。

* 使用*2020-01-01-preview*作為 API 版本。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>後續步驟

 若要深入瞭解資料加密，請參閱[使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密](concepts-data-encryption-mysql.md)。
