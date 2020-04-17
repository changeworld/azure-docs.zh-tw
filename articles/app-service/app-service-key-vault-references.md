---
title: 使用金鑰保存
description: 瞭解如何設定 Azure 應用服務和 Azure 函數以使用 Azure 密鑰保管庫引用。 使金鑰保管庫機密可供應用程式代碼使用。
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: dd0a03ea76d517486bb9bda6d9628fb529166dd8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453722"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>套用金鑰的金鑰

本主題示範如何在 App Service 或 Azure Functions 應用程式中使用來自 Azure Key Vault 的祕密，而不需要變更任何程式碼。 [Azure Key Vault](../key-vault/general/overview.md) 是提供集中式祕密管理的服務，可完整控制存取原則和稽核歷程記錄。

## <a name="granting-your-app-access-to-key-vault"></a>將您的應用程式存取權授與 Key Vault

若要從 Key Vault 讀取祕密，您需要建立保存庫，並提供您的應用程式權限來存取它。

1. 依照 [Key Vault 快速入門](../key-vault/secrets/quick-create-cli.md)來建立金鑰保存庫。

1. 為您的應用程式建立[系統指派的受控識別](overview-managed-identity.md)。

   > [!NOTE] 
   > Key Vault 參考目前只支援系統指派的受控識別。 您無法使用使用者指派的識別。

1. 針對您稍早建立的應用程式識別碼，建立 [Key Vault 中的存取原則](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies)。 在此原則上啟用 "Get" 祕密權限。 請勿設定「授權的應用程式」或 `applicationId` 設定，因為這與受控識別不相容。

    > [!NOTE]
    > 密鑰保管庫引用目前無法解決存儲在[具有網路限制](../key-vault/general/overview-vnet-service-endpoints.md)的密鑰保管庫中的機密。

## <a name="reference-syntax"></a>參考語法

Key Vault 參考格式為 `@Microsoft.KeyVault({referenceString})`，其中 `{referenceString}` 會由下列其中一個選項來取代：

> [!div class="mx-tdBreakAll"]
> | 參考字串                                                            | 描述                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** 應該是 Key Vault 中祕密的完整資料平面 URI (包括版本在內)，例如 https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** 應該是您 Key Vault 資源的名稱。 **SecretName** 應該是目標祕密的名稱。 **SecretVersion** 應該是要使用的祕密版本。 |

例如,使用 Version 的完整引用如下所示:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
或者：

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>來自 Key Vault 的來源應用程式設定

金鑰保管庫引用可用作[應用程式設定](configure-common.md#configure-app-settings)的值,允許您在密鑰保管庫中保留機密,而不是網站配置。應用程式設置在靜態時進行安全加密,但如果您需要機密管理功能,它們應進入密鑰保管庫。

若要使用應用程式設定的 Key Vault 參考，請將參考設為設定的值。 您的應用程式可以正常方式透過它的金鑰來參考祕密。 不需要變更程式碼。

> [!TIP]
> 使用 Key Vault 參考的大部分應用程式設定都應該標示為位置設定，因為每個環境都應該有不同的保存庫。

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 部署

透過 Azure Resource Manager 範本將資源部署自動化時，您可能需要以特定順序來排序相依性，才能使此功能運作。 請注意，您必須將應用程式設定定義為它們自己的資源，而不是使用網站定義中的 `siteConfig` 屬性。 這是因為必須先定義網站，才能使用它來建立系統指派的識別，並且可在存取原則中使用。

函式應用程式的範例虛擬範本看起來可能如下：

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> 在此範例中，原始檔控制部署取決於應用程式設定。 這通常是不安全的行為，因為應用程式設定更新會以非同步方式運作。 不過，因為我們已包含 `WEBSITE_ENABLE_SYNC_UPDATE_SITE` 應用程式設定，所以更新會同步。 這表示，原始檔控制部署將只會在應用程式設定已完全更新之後開始。

## <a name="troubleshooting-key-vault-references"></a>排除金鑰保存

如果未正確解析引用,則將改用引用值。 這意味著,對於應用程式設置,將創建一個環境變數,`@Microsoft.KeyVault(...)`其值具有語法。 這可能會導致應用程式引發錯誤,因為它期望某個結構的機密。

最常見的原因是[密鑰保管庫存取策略](#granting-your-app-access-to-key-vault)配置錯誤。 但是,這也可能是由於引用本身中不再存在的秘密或語法錯誤造成的。

如果語法正確,可以通過檢查門戶中的當前解析度狀態來查看其他錯誤原因。 導航到應用程式設置,然後選擇"編輯"以參考相關參考。 在設定配置下方,您應該會看到狀態資訊,包括任何錯誤。 缺少這些意味著引用語法無效。

您還可以使用內建探測器之一獲取其他資訊。

### <a name="using-the-detector-for-app-service"></a>使用偵測器進行應用服務

1. 在門戶中,導航到你的應用。
2. 選取 [診斷並解決問題]****。
3. 選擇**可用性和性能**並**選擇"Web 應用"**
4. 尋找**金鑰保管庫應用程式設定診斷**,然後按下 **「更多資訊**」。


### <a name="using-the-detector-for-azure-functions"></a>使用偵測器進行 Azure 函數

1. 在門戶中,導航到你的應用。
2. 導航到**平臺功能。**
3. 選取 [診斷並解決問題]****。
4. 選擇 **「可用性和效能」** 並選擇 **「降低功能」應用或報告錯誤。**
5. 按一下**密鑰保管庫應用程式設置診斷。**
