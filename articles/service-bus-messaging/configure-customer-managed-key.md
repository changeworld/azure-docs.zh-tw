---
title: 設定您自己的金鑰來加密待用 Azure 服務匯流排資料
description: 本文提供有關如何設定您自己的金鑰來加密 Azure 服務匯流排資料其餘部分的資訊。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 97de8df336367a74f66628675569c06d7726f2a4
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067234"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>使用 Azure 入口網站，設定客戶管理的金鑰來加密待用 Azure 服務匯流排資料
Azure 服務匯流排 Premium 會使用 Azure SSE)  (Azure 儲存體服務加密，提供待用資料的加密。 服務匯流排 Premium 依賴 Azure 儲存體來儲存資料，而且根據預設，所有以 Azure 儲存體儲存的資料都會使用 Microsoft 管理的金鑰進行加密。 

## <a name="overview"></a>概觀
Azure 服務匯流排現在支援使用 Microsoft 管理的金鑰或客戶管理的金鑰來加密待用資料的選項 (攜帶您自己的金鑰 BYOK) 。 這項功能可讓您建立、輪替、停用及撤銷用來加密待用 Azure 服務匯流排之客戶管理金鑰的存取權。

啟用 BYOK 功能是在命名空間上進行的一次性設定程式。

> [!NOTE]
> 針對服務端加密，客戶管理的金鑰會有一些注意事項。 
>   * [Azure 服務匯流排](service-bus-premium-messaging.md)進階層支援這項功能。 無法為標準層服務匯流排命名空間啟用此功能。
>   * 只能為新的或空的命名空間啟用加密。 如果命名空間包含任何佇列或主題，加密作業將會失敗。

您可以使用 Azure Key Vault 來管理金鑰，並審核金鑰使用方式。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)

本文說明如何使用 Azure 入口網站，以客戶管理的金鑰來設定金鑰保存庫。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../key-vault/secrets/quick-create-portal.md)。

> [!IMPORTANT]
> 將客戶管理的金鑰與 Azure 服務匯流排搭配使用時，金鑰保存庫必須設定兩個必要的屬性。 它們是：虛**刪除**且**不會清除**。 當您在 Azure 入口網站中建立新的金鑰保存庫時，預設會啟用這些屬性。 不過，如果您需要在現有的金鑰保存庫上啟用這些屬性，則必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰
若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的服務匯流排 Premium 命名空間。
2. 在服務匯流排命名空間的 [**設定**] 頁面上，選取 [**加密**]。
3. 選取 [待用**客戶管理的金鑰加密**]，如下圖所示。

    ![啟用客戶管理的金鑰](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>使用金鑰設定金鑰保存庫

啟用客戶管理的金鑰之後，您必須將客戶管理的金鑰與您的 Azure 服務匯流排命名空間產生關聯。 服務匯流排只支援 Azure Key Vault。 如果您在上一節中啟用 [**使用客戶管理的金鑰進行加密**] 選項，則必須將金鑰匯入 Azure Key Vault。 此外，索引鍵必須具有虛**刪除**，而且**不會**針對金鑰進行清除設定。 這些設定可以使用[PowerShell](../key-vault/general/soft-delete-powershell.md)或[CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)來設定。

1. 若要建立新的金鑰保存庫，請遵循 Azure Key Vault[快速入門](../key-vault/general/overview.md)。 如需匯入現有金鑰的詳細資訊，請參閱[關於金鑰、秘密和憑證](../key-vault/general/about-keys-secrets-certificates.md)。
1. 若要在建立保存庫時開啟虛刪除和清除保護，請使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要將清除保護新增到已啟用虛刪除) 的現有保存庫 (，請使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 依照下列步驟建立金鑰：
    1. 若要建立新的金鑰，從 [設定]**** 下方的 [金鑰]**** 功能表中選取 [產生/匯入]****。
        
        ![選取 [產生/匯入] 按鈕](./media/configure-customer-managed-key/select-generate-import.png)

    1. 將 [選項]**** 設定為 [產生]****，並為金鑰提供名稱。

        ![建立金鑰](./media/configure-customer-managed-key/create-key.png) 

    1. 您現在可以從下拉式清單中選取此金鑰，使其與要加密的服務匯流排命名空間產生關聯。 

        ![從 key vault 選取金鑰](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 針對 [冗余]，您可以新增最多3個金鑰。 如果其中一個金鑰已過期，或無法存取，則會使用其他金鑰進行加密。
        
    1. 填入金鑰的詳細資料，然後按一下 [**選取**]。 這可讓您使用客戶管理的金鑰，對命名空間中的待用資料進行加密。 


    > [!IMPORTANT]
    > 如果您想要使用客戶管理的金鑰以及異地嚴重損壞修復，請參閱下列- 
    >
    > 若要使用客戶管理的金鑰啟用待用加密，請在指定的 Azure KeyVault 上設定服務匯流排「受控識別」的[存取原則](../key-vault/general/secure-your-key-vault.md)。 這可確保從 Azure 服務匯流排命名空間控制對 Azure KeyVault 的存取。
    >
    > 原因如下：
    > 
    >   * 如果已啟用服務匯流排命名空間的[地理災難](service-bus-geo-dr.md)復原，而且您想要啟用客戶管理的金鑰，則 
    >     * 中斷配對
    >     * 將主要和次要命名空間的受控識別[存取原則設定](../key-vault/general/managed-identity.md)為金鑰保存庫。
    >     * 在主要命名空間上設定加密。
    >     * 重新配對主要和次要命名空間。
    > 
    >   * 如果您想要在已設定客戶管理金鑰的服務匯流排命名空間上啟用異地 DR，則-
    >     * 將次要命名空間之受控識別的[存取原則設定](../key-vault/general/managed-identity.md)為金鑰保存庫。
    >     * 配對主要和次要命名空間。


## <a name="rotate-your-encryption-keys"></a>輪替加密金鑰

您可以使用 Azure 金鑰保存庫迴圈機制來輪替金鑰保存庫中的金鑰。 啟用和到期日期也可以設定為自動執行金鑰輪替。 服務匯流排服務會偵測新的金鑰版本，並開始自動使用它們。

## <a name="revoke-access-to-keys"></a>撤銷金鑰的存取權

撤銷加密金鑰的存取權並不會從服務匯流排清除資料。 不過，資料無法從服務匯流排命名空間存取。 您可以透過存取原則或藉由刪除金鑰來撤銷加密金鑰。 深入瞭解存取原則和保護您的金鑰保存庫，[以安全地存取金鑰保存庫](../key-vault/general/secure-your-key-vault.md)。

撤銷加密金鑰之後，加密命名空間上的服務匯流排服務就會變成無法運作。 如果已啟用金鑰的存取權，或還原已刪除的金鑰，服務匯流排服務會挑選金鑰，讓您可以從加密的服務匯流排命名空間存取資料。

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用 Resource Manager 範本來啟用加密
本節說明如何使用**Azure Resource Manager 範本**執行下列工作。 

1. 建立具有**受控服務識別**的**premium**服務匯流排命名空間。
2. 建立**金鑰保存庫**，並將金鑰保存庫的存取權授與服務識別。 
3. 使用金鑰保存庫資訊更新服務匯流排命名空間， (索引鍵/值) 。 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>建立具有受控服務識別的 premium 服務匯流排命名空間
本節說明如何使用 Azure Resource Manager 範本和 PowerShell，建立具有受控服務識別的 Azure 服務匯流排命名空間。 

1. 建立 Azure Resource Manager 範本，以使用受控服務識別來建立服務匯流排進階層命名空間。 將檔案命名為： **CreateServiceBusPremiumNamespace.js開啟**： 

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
2. 建立名為的範本參數檔案： **CreateServiceBusPremiumNamespaceParams.json**。 

    > [!NOTE]
    > 取代下列值： 
    > - `<ServiceBusNamespaceName>`-您服務匯流排命名空間的名稱
    > - `<Location>`-服務匯流排命名空間的位置

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
3. 執行下列 PowerShell 命令來部署範本，以建立 premium 服務匯流排命名空間。 然後，取出服務匯流排命名空間的識別碼，以供稍後使用。 執行命令之前，請將取代 `{MyRG}` 為資源群組的名稱。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>授與金鑰保存庫的服務匯流排命名空間身分識別存取權

1. 執行下列命令，以建立已啟用**清除保護**和虛**刪除**的金鑰保存庫。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (或)
    
    執行下列命令來更新現有的**金鑰保存庫**。 執行命令之前，請先指定資源群組和金鑰保存庫名稱的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 設定 key vault 存取原則，讓服務匯流排命名空間的受控識別可以存取金鑰保存庫中的金鑰值。 使用上一節中服務匯流排命名空間的識別碼。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>使用 key vault 中客戶管理的金鑰來加密服務匯流排命名空間中的資料
您目前已完成下列步驟： 

1. 已建立具有受控識別的 premium 命名空間。
2. 建立金鑰保存庫，並將金鑰保存庫的存取權授與受控識別。 

在此步驟中，您將使用金鑰保存庫資訊來更新服務匯流排命名空間。 

1. 使用下列內容，**在上**建立名為UpdateServiceBusNamespaceWithEncryption.js的 JSON 檔案： 

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
    > - `<ServiceBusNamespaceName>`-您服務匯流排命名空間的名稱
    > - `<Location>`-服務匯流排命名空間的位置
    > - `<KeyVaultName>`-金鑰保存庫的名稱
    > - `<KeyName>`-金鑰保存庫中的金鑰名稱  

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
3. 執行下列 PowerShell 命令來部署 Resource Manager 範本。 執行 `{MyRG}` 命令之前，請將取代為您的資源組名。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>後續步驟
查看下列文章：
- [服務匯流排總覽](service-bus-messaging-overview.md)
- [Key Vault 總覽](../key-vault/general/overview.md)