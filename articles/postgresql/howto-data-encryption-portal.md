---
title: 資料加密 - Azure 門戶 - 用於後格雷SQL的 Azure 資料庫 - 單個伺服器
description: 瞭解如何使用 Azure 門戶為 PostgreSQL 單一伺服器設置和管理 Azure 資料庫的資料加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fe4c69787b606c601d2dc8b31cadc6dcf57458da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297062"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>通過使用 Azure 門戶，為後格雷SQL 單個伺服器的 Azure 資料庫的資料加密

瞭解如何使用 Azure 門戶為 PostgreSQL 單一伺服器的 Azure 資料庫設置和管理資料加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先決條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 在 Azure 金鑰保存庫中，創建用於客戶管理的金鑰的金鑰保存庫和金鑰。
* 金鑰保存庫必須具有以下屬性才能用作客戶管理的金鑰：
  * [虛刪除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保護](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 金鑰必須具有以下屬性才能用作客戶管理的金鑰：
  * 沒有到期日
  * 未停用
  * 能夠執行獲取、包裝鍵和解包鍵操作

## <a name="set-the-right-permissions-for-key-operations"></a>為金鑰操作設置正確的許可權

1. 在金鑰保存庫中，選擇**訪問策略** > **添加訪問策略**。

   ![突出顯示了金鑰保存庫的螢幕截圖，並突出顯示了訪問策略和添加訪問策略](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 選擇 **"金鑰許可權**"，然後選擇 **"獲取**、**換行**、**解包**"和 **"主體**"，這是 PostgreSQL 伺服器的名稱。 如果在現有主體清單中找不到伺服器主體，則需要註冊它。 首次嘗試設置資料加密時，系統會提示您註冊伺服器主體，但該加密失敗。  

   ![訪問策略概述](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 選取 [儲存]****。

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>為後格雷SQL單一伺服器設置 Azure 資料庫的資料加密

1. 在 PostgreSQL 的 Azure 資料庫中，選擇**資料加密**以設置客戶管理的金鑰。

   ![Azure 資料庫的螢幕截圖，用於 PostgreSQL，並突出顯示資料加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以選擇金鑰保存庫和金鑰組，也可以輸入金鑰識別碼。

   ![用於 PostgreSQL 的 Azure 資料庫螢幕截圖，突出顯示了資料加密選項](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 選取 [儲存]****。

4. 為確保所有檔（包括暫存檔案）完全加密，請重新開機伺服器。

## <a name="restore-or-create-a-replica-of-the-server"></a>還原或創建伺服器複本

在使用存儲在金鑰保存庫中的客戶託管金鑰組 PostgreSQL 單個伺服器的 Azure 資料庫進行加密後，對新創建的伺服器複本也進行加密。 您可以通過本地或異地還原操作或通過副本（本地/跨區域）操作製作此新副本。 因此，對於加密的 PostgreSQL 伺服器，可以使用以下步驟創建加密還原的伺服器。

1. 在伺服器上，選擇 **"概述** > **還原**"。

   ![Azure 資料庫的螢幕截圖，用於 PostgreSQL，突出顯示了概述和還原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   或者，對於啟用複製的伺服器，在 **"設置"** 標題下，選擇 **"複製**"。

   ![Azure 資料庫的螢幕截圖，用於 PostgreSQL，並突出顯示覆制](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 還原操作完成後，創建的新伺服器將使用主伺服器的金鑰進行加密。 但是，伺服器上的功能和選項被禁用，並且無法訪問伺服器。 這可以防止任何資料操作，因為新伺服器的標識尚未被授予訪問金鑰保存庫的許可權。

   ![用於 PostgreSQL 的 Azure 資料庫螢幕截圖，突出顯示不可訪問狀態](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 要使伺服器可訪問，請重新驗證還原伺服器上的金鑰。 選擇**資料加密** > **重新驗證金鑰**。

   > [!NOTE]
   > 第一次重新驗證的嘗試將失敗，因為需要授予新伺服器的服務主體對金鑰保存庫的存取權限。 要生成服務主體，請選擇 **"重新驗證金鑰**"，該鍵將顯示錯誤，但會生成服務主體。 之後，請參閱[本文前面的步驟](#set-the-right-permissions-for-key-operations)。

   ![後格雷SQL Azure 資料庫的螢幕截圖，重新驗證步驟突出顯示](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   您必須授予金鑰保存庫對新伺服器的存取權限。

4. 註冊服務主體後，再次重新驗證金鑰，伺服器將恢復其正常功能。

   ![用於 PostgreSQL 的 Azure 資料庫螢幕截圖，顯示還原的功能](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>使用 Azure 資源管理器範本啟用資料加密

除了 Azure 門戶之外，您還可以使用新伺服器和現有伺服器的 Azure 資源管理器範本為 PostgreSQL 單台伺服器在 Azure 資料庫上啟用資料加密。

### <a name="for-a-new-server"></a>對於新伺服器

使用預先創建的 Azure 資源管理器範本之一為伺服器預配啟用了資料加密：[資料加密示例](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

此 Azure 資源管理器範本為 PostgreSQL 單一伺服器創建 Azure 資料庫，並使用作為參數傳遞的**KeyVault**和**金鑰**在伺服器上啟用資料加密。

### <a name="for-an-existing-server"></a>對於現有伺服器
此外，可以使用 Azure 資源管理器範本在現有的 Azure 資料庫上為 PostgreSQL 單個伺服器啟用資料加密。

* 傳遞前面在屬性物件`keyVaultKeyUri`中的屬性下複製的 Azure 金鑰保存庫金鑰的 URI。

* 使用*2020-01-01 預覽*作為 API 版本。

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
      "type": "Microsoft.DBforPostgreSQL/servers",
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
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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

 要瞭解有關資料加密的詳細資訊，請參閱[Azure 資料庫，用於 PostgreSQL 單一伺服器資料加密，並使用客戶管理的金鑰](concepts-data-encryption-postgresql.md)。
