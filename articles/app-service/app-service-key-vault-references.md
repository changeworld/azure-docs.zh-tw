---
title: 使用 Key Vault 參考
description: 瞭解如何設定 Azure App Service 和 Azure Functions 以使用 Azure Key Vault 參考。 將 Key Vault 秘密提供給您的應用程式程式碼。
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 5b76c940066539995dbefa76d503b5412ce0c359
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85557918"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>使用 App Service 和 Azure Functions 的 Key Vault 參考

本主題示範如何在 App Service 或 Azure Functions 應用程式中使用來自 Azure Key Vault 的祕密，而不需要變更任何程式碼。 [Azure Key Vault](../key-vault/general/overview.md) 是提供集中式祕密管理的服務，可完整控制存取原則和稽核歷程記錄。

## <a name="granting-your-app-access-to-key-vault"></a>將您的應用程式存取權授與 Key Vault

若要從 Key Vault 讀取祕密，您需要建立保存庫，並提供您的應用程式權限來存取它。

1. 依照 [Key Vault 快速入門](../key-vault/secrets/quick-create-cli.md)來建立金鑰保存庫。

1. 為您的應用程式建立[系統指派的受控識別](overview-managed-identity.md)。

   > [!NOTE] 
   > Key Vault 參考目前只支援系統指派的受控識別。 您無法使用使用者指派的識別。

1. 針對您稍早建立的應用程式識別碼，建立 [Key Vault 中的存取原則](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies)。 在此原則上啟用 "Get" 祕密權限。 請勿設定「授權的應用程式」或 `applicationId` 設定，因為這與受控識別不相容。

    > [!NOTE]
    > Key Vault 參考目前無法使用[網路限制](../key-vault/general/overview-vnet-service-endpoints.md)來解析儲存在金鑰保存庫中的秘密。

## <a name="reference-syntax"></a>參考語法

Key Vault 參考格式為 `@Microsoft.KeyVault({referenceString})`，其中 `{referenceString}` 會由下列其中一個選項來取代：

> [!div class="mx-tdBreakAll"]
> | 參考字串                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** 應該是 Key Vault 中祕密的完整資料平面 URI (包括版本在內)，例如 https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** 應該是您 Key Vault 資源的名稱。 **SecretName** 應該是目標祕密的名稱。 **SecretVersion** 應該是要使用的祕密版本。 |

> [!NOTE] 
> 目前需要版本。 輪替祕密時，您必須在應用程式設定中更新版本。

例如，完整的參考可能看起來如下：

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

或者：

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>來自 Key Vault 的來源應用程式設定

Key Vault 參照可以做為[應用程式設定](configure-common.md#configure-app-settings)的值使用，讓您可以將秘密保存在 Key Vault 中，而不是網站 config。應用程式設定會安全地在待用時加密，但如果您需要密碼管理功能，他們應該會進入 Key Vault。

若要使用應用程式設定的 Key Vault 參考，請將參考設為設定的值。 您的應用程式可以正常方式透過它的金鑰來參考祕密。 不需要變更程式碼。

> [!TIP]
> 使用 Key Vault 參考的大部分應用程式設定都應該標示為位置設定，因為每個環境都應該有不同的保存庫。

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 部署

透過 Azure Resource Manager 範本將資源部署自動化時，您可能需要以特定順序來排序相依性，才能使此功能運作。 請注意，您必須將應用程式設定定義為它們自己的資源，而不是使用網站定義中的 `siteConfig` 屬性。 這是因為必須先定義網站，才能使用它來建立系統指派的識別，並且可在存取原則中使用。

函數應用程式的範例虛擬範本可能如下所示：

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

## <a name="troubleshooting-key-vault-references"></a>Key Vault 參考的疑難排解

如果未正確解析參考，則會改用參考值。 這表示針對應用程式設定，會建立其值具有語法的環境變數 `@Microsoft.KeyVault(...)` 。 這可能會導致應用程式擲回錯誤，因為它預期會有特定結構的秘密。

最常見的原因是[Key Vault 存取原則](#granting-your-app-access-to-key-vault)的設定不正確。 不過，它也可能是因為秘密已不存在或參考本身的語法錯誤所導致。

如果語法正確，您可以藉由在入口網站中檢查目前的解決狀態來查看其他錯誤原因。 流覽至 [應用程式設定]，然後選取 [編輯] 以取得問題中的參考。 在設定設定底下，您應該會看到狀態資訊，包括任何錯誤。 缺少這些表示參考語法是不正確。

您也可以使用其中一個內建偵測器來取得其他資訊。

### <a name="using-the-detector-for-app-service"></a>使用 App Service 的偵測器

1. 在入口網站中，流覽至您的應用程式。
2. 選取 [診斷並解決問題]****。
3. 選擇 [**可用性和效能**]，然後選取 [ **Web 應用程式關閉]。**
4. 尋找**Key Vault 應用程式設定] 診斷**，然後按一下 [**詳細資訊**]。


### <a name="using-the-detector-for-azure-functions"></a>使用 Azure Functions 的偵測器

1. 在入口網站中，流覽至您的應用程式。
2. 流覽至 [**平臺功能]。**
3. 選取 [診斷並解決問題]****。
4. 選擇 [**可用性和效能**]，然後選取 [**函數應用程式關閉] 或**[回報錯誤]。
5. 按一下 [ **Key Vault 應用程式設定**] [診斷]。
