---
title: 使用範本 Key Vault 秘密
description: 示範如何在部署期間從金鑰保存庫中傳遞密碼做為參數。
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: eb57e680090a38a5be725daa7b3a118039aa35f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677860"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署期間使用 Azure Key Vault 以傳遞安全的參數值

除了將安全的值（例如密碼）直接放在您的範本或參數檔案中，您也可以在部署期間從[Azure Key Vault](../../key-vault/general/overview.md)取得值。 您可以藉由參考金鑰保存庫和參數檔案中的密碼來擷取值。 您只參考其金鑰保存庫識別碼，因此該值絕不會公開。 金鑰保存庫可存在於與您要部署的資源群組不同的訂用帳戶中。

本文著重于將中的機密值當做範本參數傳遞的案例。 它不會涵蓋將虛擬機器屬性設定為 Key Vault 中憑證 URL 的案例。 如需該案例的快速入門範本，請參閱[從虛擬機器上的 Azure Key Vault 安裝憑證](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)。

## <a name="deploy-key-vaults-and-secrets"></a>部署金鑰保存庫和祕密

若要在範本部署期間存取金鑰保存庫，請將 `enabledForTemplateDeployment` 金鑰保存庫上的設定為 `true` 。

如果您已經有 Key Vault，請確定它允許範本部署。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

若要建立新的 Key Vault 並新增秘密，請使用：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

身為金鑰保存庫的擁有者，您可以自動擁有建立秘密的存取權。 如果使用秘密的使用者不是金鑰保存庫的擁有者，請使用下列方式授與存取權：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

如需建立金鑰保存庫和新增秘密的詳細資訊，請參閱：

- [使用 CLI 設定和擷取祕密](../../key-vault/secrets/quick-create-cli.md)
- [使用 PowerShell 設定和取出秘密](../../key-vault/secrets/quick-create-powershell.md)
- [使用入口網站設定和擷取祕密](../../key-vault/secrets/quick-create-portal.md)
- [使用 .NET 設定和擷取祕密](../../key-vault/secrets/quick-create-net.md)
- [使用 Node.js 設定和擷取祕密](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>授與祕密的存取權

部署範本的使用者必須具有 `Microsoft.KeyVault/vaults/deploy/action` 資源群組和金鑰保存庫範圍的許可權。 [擁有者](../../role-based-access-control/built-in-roles.md#owner)和[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色皆可授與此權限。 如果您已建立金鑰保存庫，則您是擁有者，因此您擁有該許可權。

下列程序說明如何建立具有最低權限的角色，以及如何指派使用者

1. 建立自訂角色定義 JSON 檔案：

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    以訂用帳戶識別碼取代 "00000000-0000-0000-0000-000000000000"。

2. 使用 JSON 檔案建立新的角色：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    這些範例會將自訂角色指派給資源群組層級的使用者。

將金鑰保存庫與[受控應用程式](../managed-applications/overview.md)的範本搭配使用時，您必須授與**設備資源提供者**服務主體的存取權。 如需詳細資訊，請參閱[在部署 Azure 受控應用程式時存取金鑰保存庫密碼](../managed-applications/key-vault-access.md) (英文)。

## <a name="reference-secrets-with-static-id"></a>使用靜態識別碼參考祕密

透過這個方法，您可參考參數檔案中的金鑰保存庫，而非範本。 下圖顯示參數檔案如何參考祕密，並將該值傳遞至範本。

![Resource Manager 金鑰保存庫整合靜態識別碼圖表](./media/key-vault-parameter/statickeyvault.png)

[教學課程：將 Azure Key Vault 整合 Resource Manager 範本部署](./template-tutorial-use-key-vault.md)使用此方法。

下列範本會部署包含系統管理員密碼的 SQL server。 密碼參數會設定為安全字串。 但範本並不會指定該值來自何處。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

現在，請為前述範本建立參數檔案。 在參數檔案中，指定符合範本中參數名稱的參數。 針對參數值，參考來自金鑰保存庫的密碼。 您可以藉由傳遞金鑰保存庫的資源識別碼和密碼的名稱來參考密碼：

在下列參數檔案中，金鑰保存庫祕密必須已經存在，而且您要針對其資源識別碼提供靜態值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "adminLogin": {
        "value": "exampleadmin"
      },
      "adminPassword": {
        "reference": {
          "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
        }
      },
      "sqlServerName": {
        "value": "<your-server-name>"
      }
  }
}
```

如果需要使用目前版本以外的祕密版本，請使用 `secretVersion` 屬性。

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

部署範本並在參數檔案中傳遞：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>使用動態識別碼參考祕密

上一節已說明如何從參數傳遞金鑰保存庫秘密的靜態資源識別碼。 不過，在某些情況下，您需要參考會隨目前的部署而變化的金鑰保存庫密碼。 或者，您可能想要將參數值傳至範本，而不要在參數檔案中建立參考參數。 在任一情況下，您都可以使用連結的範本，以動態方式產生金鑰保存庫秘密的資源識別碼。

由於參數檔中不允許使用範本運算式，因此您無法在參數檔中以動態方式產生資源識別碼。

在您的父範本中，您可以新增嵌套的範本，並傳入包含動態產生之資源識別碼的參數。 下圖顯示連結的範本中的參數如何參考祕密。

![動態識別碼](./media/key-vault-parameter/dynamickeyvault.png)

下列範本會動態建立金鑰保存庫識別碼，並將它當作參數傳遞。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "The location where the resources will be deployed."
        }
      },
      "vaultName": {
        "type": "string",
        "metadata": {
          "description": "The name of the keyvault that contains the secret."
        }
      },
      "secretName": {
        "type": "string",
        "metadata": {
          "description": "The name of the secret."
        }
      },
      "vaultResourceGroupName": {
        "type": "string",
        "metadata": {
          "description": "The name of the resource group that contains the keyvault."
        }
      },
      "vaultSubscription": {
        "type": "string",
        "defaultValue": "[subscription().subscriptionId]",
        "metadata": {
          "description": "The name of the subscription that contains the keyvault."
        }
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>後續步驟

- 如需有關金鑰保存庫的一般資訊，請參閱[什麼是 Azure Key Vault？](../../key-vault/general/overview.md)。
- 如需參考金鑰密碼的完整範例，請參閱 [金鑰保存庫範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
