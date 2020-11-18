---
title: 設定您自己的金鑰來加密待用 Azure 服務匯流排資料
description: 本文提供有關如何設定您自己的金鑰來加密 Azure 服務匯流排資料 rest 的資訊。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 32fcdad28b06df1763ab1efb1740d87d0b247b0a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843395"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>使用 Azure 入口網站，設定客戶管理的金鑰來加密 Azure 服務匯流排待用資料
Azure 服務匯流排 Premium 提供加密待用資料，以及 (Azure SSE) Azure 儲存體服務加密。 服務匯流排 Premium 依賴 Azure 儲存體來儲存資料，而以 Azure 儲存體儲存的所有資料都會使用 Microsoft 管理的金鑰進行加密。 

## <a name="overview"></a>概觀
Azure 服務匯流排現在支援使用 Microsoft 管理的金鑰或由客戶管理的金鑰來加密待用資料的選項， (攜帶您自己的金鑰 BYOK) 。 這項功能可讓您建立、輪替、停用及撤銷用來加密待用 Azure 服務匯流排之客戶管理金鑰的存取權。

啟用 BYOK 功能是您的命名空間上的一次性設定程式。

> [!NOTE]
> 針對服務端加密，客戶管理的金鑰有一些注意事項。 
>   * [Azure 服務匯流排](service-bus-premium-messaging.md)進階層可支援此功能。 標準層服務匯流排命名空間無法啟用此功能。
>   * 加密只能針對新的或空的命名空間啟用。 如果命名空間包含任何佇列或主題，則加密作業將會失敗。

您可以使用 Azure Key Vault 來管理您的金鑰，並審核您的金鑰使用方式。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)

本文說明如何使用 Azure 入口網站，透過客戶管理的金鑰來設定金鑰保存庫。 若要瞭解如何使用 Azure 入口網站來建立金鑰保存庫，請參閱 [快速入門：使用 Azure 入口網站建立 Azure Key Vault](../key-vault/general/quick-create-portal.md)。

> [!IMPORTANT]
> 使用客戶管理的金鑰搭配 Azure 服務匯流排需要已設定兩個必要屬性的金鑰保存庫。 它們是：虛  **刪除** ，且 **不會清除**。 當您在 Azure 入口網站中建立新的金鑰保存庫時，預設會啟用這些屬性。 但是，如果您需要在現有的金鑰保存庫上啟用這些屬性，您必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰
若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的服務匯流排 Premium 命名空間。
2. 在服務匯流排命名空間的 [ **設定** ] 頁面上，選取 [ **加密**]。
3. 選取 **客戶管理的金鑰加密** ，如下圖所示。

    ![啟用客戶管理的金鑰](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>使用金鑰設定金鑰保存庫

啟用客戶管理的金鑰之後，您必須將客戶管理的金鑰與您的 Azure 服務匯流排命名空間建立關聯。 服務匯流排僅支援 Azure Key Vault。 如果您在上一節中 **使用客戶管理的金鑰啟用加密** ，您必須將金鑰匯入 Azure Key Vault。 此外，索引鍵必須具有虛 **刪除** ，且不 **會清除** 為金鑰設定。 您可以使用 [PowerShell](../key-vault/general/soft-delete-powershell.md) 或 [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)來設定這些設定。

1. 若要建立新的金鑰保存庫，請遵循 Azure Key Vault [快速入門](../key-vault/general/overview.md)。 如需有關匯入現有金鑰的詳細資訊，請參閱 [關於金鑰、秘密和憑證](../key-vault/general/about-keys-secrets-certificates.md)。
1. 若要在建立保存庫時開啟虛刪除和清除保護，請使用 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要將清除保護新增至已啟用虛刪除的現有保存庫 () ，請使用 [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 遵循下列步驟來建立金鑰：
    1. 若要建立新的金鑰，從 [設定] 下方的 [金鑰] 功能表中選取 [產生/匯入]。
        
        ![選取 [產生/匯入] 按鈕](./media/configure-customer-managed-key/select-generate-import.png)

    1. 將 [選項] 設定為 [產生]，並為金鑰提供名稱。

        ![建立金鑰](./media/configure-customer-managed-key/create-key.png) 

    1. 您現在可以從下拉式清單選取此索引鍵，以與服務匯流排命名空間產生關聯以進行加密。 

        ![從 key vault 選取金鑰](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 針對冗余，您最多可以新增3個金鑰。 如果其中一個金鑰已過期，或無法存取，則會使用其他金鑰進行加密。
        
    1. 填入金鑰的詳細資料，然後按一下 [ **選取**]。 這可讓您使用客戶管理的金鑰來加密命名空間上的待用資料。 


    > [!IMPORTANT]
    > 如果您想要使用客戶管理的金鑰和異地嚴重損壞修復，請參閱下列- 
    >
    > 若要使用客戶管理的金鑰啟用待用加密，可在指定的 Azure KeyVault 上設定服務匯流排「受控識別」的 [存取原則](../key-vault/general/secure-your-key-vault.md) 。 這可確保從 Azure 服務匯流排命名空間進行 Azure KeyVault 的受控存取。
    >
    > 原因如下：
    > 
    >   * 如果已針對服務匯流排命名空間啟用 [地理災難](service-bus-geo-dr.md) 復原，而且您想要啟用客戶管理的金鑰，則 
    >     * 中斷配對
    >     * 將主要和次要命名空間的受控識別[存取原則設定](../key-vault/general/assign-access-policy-portal.md)為金鑰保存庫。
    >     * 在主要命名空間上設定加密。
    >     * 重新配對主要和次要命名空間。
    > 
    >   * 如果您想要在已設定客戶管理金鑰的服務匯流排命名空間上啟用異地 DR，則-
    >     * 為金鑰保存庫的次要命名空間設定受控識別的[存取原則](../key-vault/general/assign-access-policy-portal.md)。
    >     * 將主要和次要命名空間配對。


## <a name="rotate-your-encryption-keys"></a>輪替加密金鑰

您可以使用 Azure 金鑰保存庫輪替機制來輪替金鑰保存庫中的金鑰。 啟用和到期日也可以設定為自動進行金鑰輪替。 服務匯流排服務會偵測到新的金鑰版本，並自動開始使用它們。

## <a name="revoke-access-to-keys"></a>撤銷金鑰的存取權

撤銷加密金鑰的存取權，並不會清除服務匯流排中的資料。 但是，無法從服務匯流排命名空間存取資料。 您可以透過存取原則或藉由刪除金鑰來撤銷加密金鑰。 深入瞭解存取原則，並保護金鑰保存庫免于 [安全地存取金鑰保存庫](../key-vault/general/secure-your-key-vault.md)。

撤銷加密金鑰之後，加密命名空間上的服務匯流排服務將變成無法運作。 如果已啟用金鑰的存取權，或還原已刪除的金鑰，服務匯流排服務將會挑選金鑰，讓您可以從加密的服務匯流排命名空間存取資料。

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用 Resource Manager 範本啟用加密
本節說明如何使用 **Azure Resource Manager 範本** 來執行下列工作。 

1. 使用 **受控服務識別** 建立 **premium** 服務匯流排命名空間。
2. 建立 **金鑰保存庫** ，並將服務身分識別存取權授與金鑰保存庫。 
3. 使用金鑰保存庫資訊更新服務匯流排命名空間 (索引鍵/值) 。 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>使用受控服務識別建立 premium 服務匯流排命名空間
本節說明如何使用 Azure Resource Manager 範本和 PowerShell 來建立具有受控服務識別的 Azure 服務匯流排命名空間。 

1. 建立 Azure Resource Manager 範本，以使用受控服務識別來建立服務匯流排進階層命名空間。 將檔案命名為： **CreateServiceBusPremiumNamespace.js**： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 建立名為： **CreateServiceBusPremiumNamespaceParams.js的** 範本參數檔案。 

    > [!NOTE]
    > 取代下列值： 
    > - `<ServiceBusNamespaceName>` -服務匯流排命名空間的名稱
    > - `<Location>` -服務匯流排命名空間的位置

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. 執行下列 PowerShell 命令來部署範本，以建立 premium 服務匯流排命名空間。 然後，取得服務匯流排命名空間的識別碼，以供稍後使用。 執行 `{MyRG}` 命令之前，請以資源群組的名稱取代。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>將服務匯流排命名空間身分識別存取權授與 key vault

1. 執行下列命令，以建立啟用 **清除保護** 和虛 **刪除** 的金鑰保存庫。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (或)
    
    執行下列命令以更新現有的 **金鑰保存庫**。 請先指定資源群組和金鑰保存庫名稱的值，再執行命令。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 設定 key vault 存取原則，讓服務匯流排命名空間的受控識別可以存取金鑰保存庫中的金鑰值。 使用上一節中服務匯流排命名空間的識別碼。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>使用金鑰保存庫中客戶管理的金鑰，將服務匯流排命名空間中的資料加密
您目前已完成下列步驟： 

1. 建立具有受控識別的 premium 命名空間。
2. 建立金鑰保存庫，並將金鑰保存庫的存取權授與受控識別。 

在此步驟中，您將使用金鑰保存庫資訊來更新服務匯流排命名空間。 

1. 使用下列內容建立名為 **UpdateServiceBusNamespaceWithEncryption.js** 的 JSON 檔案： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 建立範本參數檔案： **UpdateServiceBusNamespaceWithEncryptionParams.js開啟**。

    > [!NOTE]
    > 取代下列值： 
    > - `<ServiceBusNamespaceName>` -服務匯流排命名空間的名稱
    > - `<Location>` -服務匯流排命名空間的位置
    > - `<KeyVaultName>` -金鑰保存庫的名稱
    > - `<KeyName>` -金鑰保存庫中的金鑰名稱  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 執行下列 PowerShell 命令，以部署 Resource Manager 範本。 `{MyRG}`在執行命令之前，以您的資源組名取代。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>後續步驟
查看下列文章：
- [服務匯流排總覽](service-bus-messaging-overview.md)
- [Key Vault 總覽](../key-vault/general/overview.md)
