---
title: 部署受管理的應用程式時使用 Key Vault
description: 說明在部署受控應用程式時如何使用 Azure Key Vault 中的存取祕密
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: f434ad6e19c89f248fec948c0a049fabb0f7c476
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086733"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>在部署 Azure 受控應用程式時存取 Key Vault 秘密

當您需要在部署期間，傳送安全值 (例如密碼) 做為參數時，您可以從 [Azure Key Vault](../../key-vault/key-vault-overview.md) 擷取值。 若要在部署受控應用程式時存取 Key Vault，您必須授與**設備資源提供者**服務主體的存取權。 受控應用程式服務會使用這個身分識別來執行作業。 若要在部署期間成功地從 Key Vault 擷取值，服務主體必須要能夠存取 Key Vault。

本文說明如何設定與受控應用程式搭配使用的 Key Vault。

## <a name="enable-template-deployment"></a>啟用範本部署

1. 在入口網站中，選取您的 Key Vault。

1. 選取 [存取原則]。   

   ![選取存取原則](./media/key-vault-access/select-access-policies.png)

1. 選取 [按一下以顯示進階存取原則]。

   ![顯示進階存取原則](./media/key-vault-access/advanced.png)

1. 選取 [為範本部署啟用對 Azure Resource Manager 的存取]。 然後選取 [儲存]。

   ![啟用範本部署](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>將服務新增為參與者

1. 選取 [存取控制 (IAM)]。

   ![選取存取控制](./media/key-vault-access/access-control.png)

1. 選取 [新增角色指派]。

   ![選取 [新增]](./media/key-vault-access/add-access-control.png)

1. 選取 [參與者] 作為角色。 搜尋**設備資源提供者**，並從可用的選項中加以選取。

   ![搜尋提供者](./media/key-vault-access/search-provider.png)

1. 選取 [儲存]。

## <a name="reference-key-vault-secret"></a>參考 Key Vault 祕密

若要從 Key Vault 將秘密傳遞至 Managed 應用程式中的範本，您必須使用[連結或嵌套的範本](../templates/linked-templates.md)，並在連結或嵌套範本的參數中參考 Key Vault。 請提供 Key Vault 的資源識別碼和秘密的名稱。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

您已將 Key Vault 設定為在受控應用程式部署期間可供存取。

* 如需從 Key Vault 傳入值作為範本參數的相關資訊，請參閱[在部署期間使用 Azure Key Vault 傳遞安全的參數值](../templates/key-vault-parameter.md)。
* 如需受控應用程式範例，請參閱[適用於 Azure 受控應用程式之範例專案](sample-projects.md)。
* 若要了解如何建立受控應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
