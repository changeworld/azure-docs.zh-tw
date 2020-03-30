---
title: 配置自己的金鑰以加密靜態 Azure 服務匯流排資料
description: 本文提供有關如何配置自己的金鑰以加密 Azure 服務匯流排資料靜態的資訊。
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624078"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>配置客戶管理金鑰，以便使用 Azure 門戶加密靜態 Azure 服務匯流排資料
Azure 服務匯流排高級版通過 Azure 存儲服務加密 （Azure SSE） 提供靜態資料加密。 服務匯流排高級版依賴于 Azure 存儲來存儲資料，預設情況下，與 Azure 存儲一起存儲的所有資料都使用 Microsoft 管理的金鑰進行加密。 

## <a name="overview"></a>總覽
Azure 服務匯流排現在支援使用 Microsoft 管理的金鑰或客戶管理的金鑰（自帶金鑰 - BYOK）加密靜態資料的選項。 此功能使您能夠創建、旋轉、禁用和撤銷對用於在靜態時加密 Azure 服務匯流排的客戶管理金鑰的存取權限。

啟用 BYOK 功能是命名空間上的一次性設置過程。

> [!NOTE]
> 對於服務端加密，客戶託管金鑰有一些注意事項。 
>   * [Azure 服務匯流排高級](service-bus-premium-messaging.md)層支援此功能。 無法為標準層服務匯流排命名空間啟用它。
>   * 只能為新或空命名空間啟用加密。 如果命名空間包含資料，則加密操作將失敗。

您可以使用 Azure 金鑰保存庫來管理金鑰並審核金鑰使用方式。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-overview.md)

本文演示如何使用 Azure 門戶使用客戶管理的金鑰配置金鑰保存庫。 若要瞭解如何使用 Azure 門戶創建金鑰保存庫，請參閱[快速入門：使用 Azure 門戶從 Azure 金鑰保存庫設置和檢索機密](../key-vault/quick-create-portal.md)。

> [!IMPORTANT]
> 將客戶管理的金鑰與 Azure 服務匯流排一起使用需要金鑰保存庫配置兩個必需屬性。 它們是：**虛刪除**和**不清除**。 預設情況下，在 Azure 門戶中創建新金鑰保存庫時，將啟用這些屬性。 但是，如果需要在現有金鑰保存庫上啟用這些屬性，則必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰
要在 Azure 門戶中啟用客戶管理的金鑰，請按照以下步驟操作：

1. 導航到服務匯流排高級命名空間。
2. 在服務匯流排命名空間的 **"設置"** 頁上，選擇 **"加密**"。
3. 選擇**客戶管理金鑰靜態加密，** 如下圖所示。

    ![啟用客戶託管金鑰](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>設置帶金鑰的金鑰保存庫

啟用客戶管理的金鑰後，需要將客戶託管金鑰與 Azure 服務匯流排命名空間相關聯。 服務匯流排僅支援 Azure 金鑰保存庫。 如果在上一節中啟用**了使用客戶管理的金鑰選項的加密**，則需要將金鑰導入 Azure 金鑰保存庫。 此外，金鑰必須為金鑰配置 **"虛刪除****"和"不清除**"。 這些設置可以使用[PowerShell](../key-vault/key-vault-soft-delete-powershell.md)或[CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)進行配置。

1. 要創建新金鑰保存庫，請按照 Azure 金鑰保存庫[快速入門](../key-vault/key-vault-overview.md)。 有關導入現有金鑰的詳細資訊，請參閱[有關金鑰、機密和證書](../key-vault/about-keys-secrets-and-certificates.md)。
1. 要在創建保存庫時同時打開虛刪除和清除保護，請使用[az 金鑰保存庫創建](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 要向現有保存庫（已啟用虛刪除）添加清除保護，請使用[az keyvault 更新](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 通過以下步驟創建鍵：
    1. 若要建立新的金鑰，從 [設定]**** 下方的 [金鑰]**** 功能表中選取 [產生/匯入]****。
        
        ![選擇"生成/導入"按鈕](./media/configure-customer-managed-key/select-generate-import.png)

    1. 將 [選項]**** 設定為 [產生]****，並為金鑰提供名稱。

        ![建立金鑰](./media/configure-customer-managed-key/create-key.png) 

    1. 現在，您可以選擇此金鑰以與服務匯流排命名空間關聯，以便從下拉清單中進行加密。 

        ![從金鑰保存庫中選擇金鑰](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 對於冗余，您最多可以添加 3 個金鑰。 如果其中一個金鑰已過期或無法訪問，則其他金鑰將用於加密。
        
    1. 填寫金鑰的詳細資訊，然後按一下 **"選擇**"。 這將啟用使用客戶託管金鑰在命名空間上靜態資料加密。 


    > [!IMPORTANT]
    > 如果您希望使用客戶託管金鑰以及地理災害復原，請查看以下 ： 
    >
    > 要使用客戶託管金鑰啟用靜態加密，將在指定的 Azure KeyVault 上為服務匯流排的託管標識設置[訪問策略](../key-vault/key-vault-secure-your-key-vault.md)。 這可確保從 Azure 服務匯流排命名空間對 Azure 金鑰庫進行受控訪問。
    >
    > 因此：
    > 
    >   * 如果服務匯流排命名空間已啟用[了地理災害復原](service-bus-geo-dr.md)，並且您希望啟用客戶託管金鑰，則 
    >     * 中斷配對
    >     * 為金鑰保存庫的主命名空間和輔助命名空間的託管標識[設置訪問策略](../key-vault/managed-identity.md)。
    >     * 在主命名空間上設置加密。
    >     * 重新配對主命名空間和輔助命名空間。
    > 
    >   * 如果要在已設置客戶託管金鑰的服務匯流排命名空間啟用 Geo-DR，則 -
    >     * 為金鑰保存庫的輔助命名空間的託管標識[設置訪問策略](../key-vault/managed-identity.md)。
    >     * 配對主命名空間和輔助命名空間。


## <a name="rotate-your-encryption-keys"></a>旋轉加密金鑰

可以使用 Azure 金鑰保存庫輪換機制在金鑰保存庫中旋轉金鑰。 有關詳細資訊，請參閱[設置金鑰輪換和審核](../key-vault/key-vault-key-rotation-log-monitoring.md)。 啟動和到期日期也可以設置為自動輪換金鑰。 服務匯流排服務將檢測新的金鑰版本，並自動開始使用它們。

## <a name="revoke-access-to-keys"></a>撤銷對金鑰的訪問

撤銷對加密金鑰的訪問不會從服務匯流排清除資料。 但是，無法從服務匯流排命名空間訪問資料。 您可以通過訪問策略或刪除金鑰來撤銷加密金鑰。 詳細瞭解訪問策略和保護金鑰保存庫從[安全訪問金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。

一旦加密金鑰被吊銷，加密命名空間上的服務匯流排服務將變得無法運行。 如果啟用了對金鑰的訪問或已刪除的金鑰已恢復，則服務匯流排服務將選擇該金鑰，以便您可以從加密的服務匯流排命名空間訪問資料。

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用資源管理器範本啟用加密
本節演示如何使用**Azure 資源管理器範本**執行以下任務。 

1. 使用**託管服務標識**創建**高級**服務匯流排命名空間。
2. 創建**金鑰保存庫**並授予對金鑰保存庫的服務標識存取權限。 
3. 使用金鑰保存庫資訊（金鑰/值）更新服務匯流排命名空間。 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>使用託管服務標識創建高級服務匯流排命名空間
本節演示如何使用 Azure 資源管理器範本和 PowerShell 使用託管服務標識創建 Azure 服務匯流排命名空間。 

1. 創建 Azure 資源管理器範本以創建具有託管服務標識的服務匯流排高級層命名空間。 命名檔：**創建服務匯流排高級命名空間.json**： 

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
2. 創建名為：**創建服務匯流排高級命名空間Params.json 的**範本參數檔。 

    > [!NOTE]
    > 取代下列值： 
    > - `<ServiceBusNamespaceName>`- 服務匯流排命名空間的名稱
    > - `<Location>`- 服務匯流排命名空間的位置

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
3. 運行以下 PowerShell 命令以部署範本以創建高級服務匯流排命名空間。 然後，檢索服務匯流排命名空間的 ID 以以後使用它。 在`{MyRG}`運行命令之前，請替換為資源組的名稱。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>授予服務匯流排命名空間對金鑰保存庫的標識訪問

1. 運行以下命令以創建具有**清除保護和****虛刪除**功能的金鑰保存庫。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (或)
    
    運行以下命令以更新**現有金鑰保存庫**。 在運行命令之前，指定資源組和金鑰保存庫名稱的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 設置金鑰保存庫訪問策略，以便服務匯流排命名空間的託管標識可以訪問金鑰保存庫中的金鑰值。 使用上一節中的服務匯流排命名空間的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>使用金鑰保存庫的客戶託管金鑰加密服務匯流排命名空間中的資料
到目前為止，您已完成以下步驟： 

1. 使用託管標識創建高級命名空間。
2. 創建金鑰保存庫並授予對金鑰保存庫的託管標識存取權限。 

在此步驟中，您將使用金鑰保存庫資訊更新服務匯流排命名空間。 

1. 創建一個名為**更新服務Bus命名空間與加密.json**的JSON檔與以下內容： 

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

2. 創建範本參數檔：**更新服務Bus命名空間與加密帕拉姆斯.json**。

    > [!NOTE]
    > 取代下列值： 
    > - `<ServiceBusNamespaceName>`- 服務匯流排命名空間的名稱
    > - `<Location>`- 服務匯流排命名空間的位置
    > - `<KeyVaultName>`- 金鑰保存庫的名稱
    > - `<KeyName>`- 金鑰保存庫中金鑰的名稱  

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
3. 運行以下 PowerShell 命令以部署資源管理器範本。 在`{MyRG}`運行命令之前，請替換為資源組的名稱。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>後續步驟
查看下列文章：
- [服務匯流排概述](service-bus-messaging-overview.md)
- [金鑰保存庫概述](../key-vault/key-vault-overview.md)


