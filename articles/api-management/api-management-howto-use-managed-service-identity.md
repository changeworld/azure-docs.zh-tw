---
title: 在 Azure API 管理中使用受控識別 |Microsoft Docs
description: 瞭解如何使用 Azure 入口網站、PowerShell 和 Resource Manager 範本，在 API 管理中建立系統指派和使用者指派的身分識別。
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 11/14/2020
ms.author: apimpm
ms.openlocfilehash: db1a8238cf9ddae57d73438d43daa54294ce6860
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686220"
---
# <a name="use-managed-identities-in-azure-api-management"></a>在 Azure API 管理中使用受控識別

本文說明如何建立 Azure API 管理實例的受控識別，以及如何存取其他資源。 由 Azure Active Directory (Azure AD) 產生的受控識別可讓您的 API 管理實例輕鬆且安全地存取其他受 Azure AD 保護的資源，例如 Azure Key Vault。 Azure 會管理此身分識別，因此您不需要布建或輪替任何秘密。 如需受控識別的詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)。

您可以將兩種類型的身分識別授與 API 管理實例：

- *系統指派* 的身分識別會系結至您的服務，如果您的服務已刪除，則會將其刪除。 服務只能有一個系統指派的身分識別。
- *使用者指派* 的身分識別是可指派給您服務的獨立 Azure 資源。 服務可以有多個使用者指派的身分識別。

## <a name="create-a-system-assigned-managed-identity"></a>建立系統指派的受控識別

### <a name="azure-portal"></a>Azure 入口網站

若要在 Azure 入口網站中設定受控識別，您必須先建立 API 管理實例，然後再啟用此功能。

1. 像平常一樣在入口網站中建立 API 管理執行個體。 在入口網站中流覽至該網站。
2. 選取 [ **受控** 識別]。
3. 在 [ **系統指派** ] 索引標籤上，將 [ **狀態** ] 切換為 **開啟**。 選取 [儲存]。

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="啟用系統指派的受控識別的選取專案" border="true":::


### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列步驟將逐步引導您建立 API 管理實例，並使用 Azure PowerShell 為其指派身分識別。 

1. 如有需要，請使用 [Azure PowerShell 指南](/powershell/azure/install-az-ps)中的指示來安裝 Azure PowerShell。 然後執行 `Connect-AzAccount` 以建立與 Azure 的連線。

2. 使用下列程式碼來建立實例。 如需如何搭配 API 管理實例使用 Azure PowerShell 的更多範例，請參閱 [Api 管理 PowerShell 範例](powershell-samples.md)。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. 更新現有的實例以建立身分識別：

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

您可以在資源定義中包含以下屬性，來建立具有身分識別的「API 管理」執行個體：

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

此屬性會告訴 Azure 為您的「API 管理」執行個體建立及管理身分識別。

例如，完整的 Azure Resource Manager 範本可能看起來如下：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

建立實例時，它會有下列額外屬性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId`屬性會識別身分識別所屬 Azure AD 租使用者。 `principalId`屬性是實例新身分識別的唯一識別碼。 在 Azure AD 中，服務主體的名稱與您提供給 API 管理實例的名稱相同。


> [!NOTE]
> API 管理實例可以同時具有系統指派和使用者指派的身分識別。 在此情況下， `type` 屬性會是 `SystemAssigned,UserAssigned` 。

## <a name="supported-scenarios-using-system-assigned-identity"></a>使用系統指派身分識別的支援案例

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>從 Azure Key Vault 取得 API 管理實例的自訂 TLS/SSL 憑證
您可以使用 API 管理實例的系統指派身分識別，來取出儲存在 Azure Key Vault 中的自訂 TLS/SSL 憑證。 然後，您可以將這些憑證指派給 API 管理實例中的自訂網域。 請記住這些考量：

- 秘密的內容類型必須是 *application/x-pkcs12*。
- 使用包含秘密的 Key Vault 憑證秘密端點。

> [!Important]
> 如果您未提供憑證的物件版本，API 管理會在 Key Vault 中更新後的四個小時內自動取得憑證的較新版本。

下列範例顯示的 Azure Resource Manager 範本包含下列步驟：

1. 使用受控識別建立 API 管理實例。
2. 更新 Azure Key Vault 執行個體的存取原則，以允許「API 管理」執行個體從它取得祕密。
3. 透過來自 Key Vault 執行個體的憑證來設定自訂網域名稱，以更新「API 管理」執行個體。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>使用 API 管理身分識別來驗證後端

您可以使用系統指派的身分識別，透過 [驗證管理](api-management-authentication-policies.md#ManagedIdentity) 的身分識別原則來驗證後端。


## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

> [!NOTE]
> 您可以將 API 管理實例與最多10個使用者指派的受控識別建立關聯。

### <a name="azure-portal"></a>Azure 入口網站

若要在入口網站中設定受控識別，您必須先建立 API 管理實例，然後再啟用此功能。

1. 像平常一樣在入口網站中建立 API 管理執行個體。 在入口網站中流覽至該網站。
2. 選取 [ **受控** 識別]。
3. 在 [ **使用者指派** ] 索引標籤上，選取 [ **新增**]。
4. 搜尋您稍早建立的身分識別，然後選取它。 選取 [新增]  。

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="啟用使用者指派的受控識別的選取專案" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列步驟將逐步引導您建立 API 管理實例，並使用 Azure PowerShell 為其指派身分識別。 

1. 如有需要，請使用 [Azure PowerShell 指南](/powershell/azure/install-az-ps)中的指示來安裝 Azure PowerShell。 然後執行 `Connect-AzAccount` 以建立與 Azure 的連線。

2. 使用下列程式碼來建立實例。 如需如何搭配 API 管理實例使用 Azure PowerShell 的更多範例，請參閱 [Api 管理 PowerShell 範例](powershell-samples.md)。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. 更新現有的服務，以將身分識別指派給服務：

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

您可以在資源定義中包含以下屬性，來建立具有身分識別的「API 管理」執行個體：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

新增使用者指派的類型可告知 Azure 使用為您的實例指定的使用者指派身分識別。

例如，完整的 Azure Resource Manager 範本可能看起來如下：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

建立服務時，會有下列其他屬性：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId`屬性是用於 Azure AD 管理之身分識別的唯一識別碼。 `clientId`屬性是應用程式新身分識別的唯一識別碼，用來指定執行時間呼叫期間要使用的身分識別。

> [!NOTE]
> API 管理實例可以同時具有系統指派和使用者指派的身分識別。 在此情況下， `type` 屬性會是 `SystemAssigned,UserAssigned` 。

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>使用使用者指派的受控識別的支援案例

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>從 Azure Key Vault 取得 API 管理實例的自訂 TLS/SSL 憑證
您可以使用任何使用者指派的身分識別，在 API 管理實例和 KeyVault 之間建立信任。 然後，您可以使用此信任來取出儲存在 Azure Key Vault 中的自訂 TLS/SSL 憑證。 然後，您可以將這些憑證指派給 API 管理實例中的自訂網域。 

請記住這些考量：

- 秘密的內容類型必須是 *application/x-pkcs12*。
- 使用包含秘密的 Key Vault 憑證秘密端點。

> [!Important]
> 如果您未提供憑證的物件版本，API 管理會在 Key Vault 中更新後的四個小時內自動取得憑證的較新版本。

如需完整範本，請參閱 [使用以 KeyVault 為基礎的 SSL 的 API 管理（使用使用者指派的身分識別](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json)）。

在此範本中，您將部署：

* Azure API 管理
* Azure 受控使用者指派的身分識別
* 用於儲存 SSL/TLS 憑證的 Azure KeyVault

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>使用使用者指派的身分識別來驗證後端

您可以使用使用者指派的身分識別，透過 [驗證管理](api-management-authentication-policies.md#ManagedIdentity) 的身分識別原則來向後端進行驗證。


## <a name="remove-an-identity"></a><a name="remove"></a>移除身分識別

若要移除系統指派的身分識別，您可以透過入口網站或 Azure Resource Manager 範本停用該功能，方法與建立的方式相同。 使用者指派的身分識別可以個別移除。 若要移除所有身分識別，請將識別類型設定為 `"None"` 。

以這種方式移除系統指派的身分識別，也會從 AAD 將其刪除。 當 API 管理實例被刪除時，系統也會自動從 Azure AD 移除系統指派的身分識別。

若要使用 Azure Resource Manager 範本移除所有身分識別，請更新此區段：

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> 如果已使用來自 Key Vault 的自訂 SSL 憑證設定 API 管理實例，而您嘗試停用受控識別，則要求將會失敗。
>
> 您可以從 Azure Key Vault 憑證切換至內嵌編碼憑證，然後停用受控識別，以解除封鎖。 如需詳細資訊，請參閱[設定自訂網域名稱](configure-custom-domain.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 資源的受控識別：

* [什麼是 Azure 資源受控識別？](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure 資源管理員範本](https://github.com/Azure/azure-quickstart-templates)
* [使用原則中的受控識別進行驗證](./api-management-authentication-policies.md#ManagedIdentity)
