---
title: Azure 指南-使用 Azure Resource Manager 範本建立 Azure key vault 和保存庫存取原則 |Microsoft Docs
description: 說明如何使用 Azure Resource Manager 範本，建立 Azure 金鑰保存庫和保存庫存取原則。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804307"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>如何使用 Resource Manager 範本建立 Azure Key Vault 和保存庫存取原則

[Azure Key Vault](../general/overview.md) 是雲端服務，可安全儲存祕密，例如金鑰、密碼、憑證和其他祕密。 本指南著重于部署 Azure Resource Manager 範本 (ARM 範本) 建立金鑰保存庫的程式。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>必要條件

若要完成此文章：

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="create-key-vault-resource-manager-template"></a>建立 Key Vault Resource Manager 範本

下列範本顯示建立金鑰保存庫的基本方式。 某些值是在範本內指定。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

如需 Key Vault 範本設定的詳細資訊，請參閱 [KEY VAULT ARM 範本參考](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults)。

> [!IMPORTANT]
> 如果範本已重新部署，則會覆寫金鑰保存庫中的任何現有存取原則。 建議您以 `accessPolicies` 現有的存取原則填入屬性，以避免失去金鑰保存庫的存取權。 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>將存取原則新增至 Key Vault Resource Manager 範本

您可以將存取原則部署到現有的金鑰保存庫，而不需要重新部署整個金鑰保存庫範本。 下列範本顯示建立存取原則的基本方式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
如需 Key Vault 範本設定的詳細資訊，請參閱 [KEY VAULT ARM 範本參考](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies)。

## <a name="other-available-key-vault-resource-manager-templates"></a>其他可用的 Key Vault Resource Manager 範本

有其他 Resource Manager 範本可供 Key Vault 物件使用：

| 密碼 | 索引鍵 | 憑證 |
|--|--|--|
|[快速入門](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[參考](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N/A|N/A|

您可以在這裡找到更多 Key Vault 範本： [Key Vault Resource Manager 參考](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>部署範本

您可以使用 Azure 入口網站來部署上述範本，方法是使用 [在編輯器中建立您自己的範本] 選項，如下所示： [從自訂範本部署資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

您也可以將上述範本儲存至檔案，並使用下列命令：  [>new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。 當不再需要資源時，請刪除資源群組，這會刪除 key vault 和相關資源。 若要使用 Azure CLI 或 Azure PowerShell 來刪除資源群組，請使用下列步驟。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>資源

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 深入了解 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)

## <a name="next-steps"></a>後續步驟

- [針對金鑰保存庫的存取進行保護](secure-your-key-vault.md)
- [對金鑰保存庫進行驗證](authentication.md)
- [Azure Key Vault 開發人員指南](developers-guide.md)
