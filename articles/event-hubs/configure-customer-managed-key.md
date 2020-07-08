---
title: 設定您自己的金鑰來加密待用 Azure 事件中樞資料
description: 本文提供有關如何設定您自己的金鑰來加密 Azure 事件中樞資料其餘部分的資訊。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 2d82fc8c962496246196331c7d191c0fc057694f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85479822"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>使用 Azure 入口網站，設定客戶管理的金鑰來加密待用 Azure 事件中樞資料
Azure 事件中樞使用 Azure 儲存體服務加密（Azure SSE）提供待用資料的加密。 事件中樞依賴 Azure 儲存體來儲存資料，而且根據預設，與 Azure 儲存體一起儲存的所有資料都會使用 Microsoft 管理的金鑰進行加密。 

## <a name="overview"></a>總覽
Azure 事件中樞現在支援使用 Microsoft 管理的金鑰或客戶管理的金鑰（攜帶您自己的金鑰– BYOK）來加密待用資料的選項。 這項功能可讓您建立、輪替、停用及撤銷用來加密待用資料 Azure 事件中樞的客戶管理金鑰的存取權。

啟用 BYOK 功能是在命名空間上進行的一次性設定程式。

> [!NOTE]
> [事件中樞專用的單一租](event-hubs-dedicated-overview.md)使用者叢集支援 BYOK 功能。 無法針對標準事件中樞命名空間啟用此功能。

您可以使用 Azure Key Vault 來管理金鑰，並審核金鑰使用方式。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)

本文說明如何使用 Azure 入口網站，以客戶管理的金鑰來設定金鑰保存庫。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../key-vault/secrets/quick-create-portal.md)。

> [!IMPORTANT]
> 將客戶管理的金鑰與 Azure 事件中樞搭配使用時，金鑰保存庫必須設定兩個必要的屬性。 它們是：虛**刪除**且**不會清除**。 當您在 Azure 入口網站中建立新的金鑰保存庫時，預設會啟用這些屬性。 不過，如果您需要在現有的金鑰保存庫上啟用這些屬性，則必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰
若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的事件中樞專用叢集。
1. 選取您要啟用 BYOK 的命名空間。
1. 在事件中樞命名空間的 [**設定**] 頁面上，選取 [**加密**]。 
1. 選取 [待用**客戶管理的金鑰加密**]，如下圖所示。 

    ![啟用客戶管理的金鑰](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>使用金鑰設定金鑰保存庫
啟用客戶管理的金鑰之後，您必須將客戶管理的金鑰與您的 Azure 事件中樞命名空間產生關聯。 事件中樞只支援 Azure Key Vault。 如果您在上一節中啟用 [**使用客戶管理的金鑰進行加密**] 選項，則必須將金鑰匯入 Azure Key Vault。 此外，索引鍵必須具有虛**刪除**，而且**不會**針對金鑰進行清除設定。 這些設定可以使用[PowerShell](../key-vault/general/soft-delete-powershell.md)或[CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)來設定。

1. 若要建立新的金鑰保存庫，請遵循 Azure Key Vault[快速入門](../key-vault/general/overview.md)。 如需匯入現有金鑰的詳細資訊，請參閱[關於金鑰、秘密和憑證](../key-vault/about-keys-secrets-and-certificates.md)。
1. 若要在建立保存庫時開啟虛刪除和清除保護，請使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要將清除保護新增至現有的保存庫（已啟用虛刪除），請使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 依照下列步驟建立金鑰：
    1. 若要建立新的金鑰，從 [設定]**** 下方的 [金鑰]**** 功能表中選取 [產生/匯入]****。
        
        ![選取 [產生/匯入] 按鈕](./media/configure-customer-managed-key/select-generate-import.png)
    1. 將 [選項]**** 設定為 [產生]****，並為金鑰提供名稱。

        ![建立金鑰](./media/configure-customer-managed-key/create-key.png) 
    1. 您現在可以從下拉式清單中選取此金鑰，使其與要加密的事件中樞命名空間產生關聯。 

        ![從 key vault 選取金鑰](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 填入金鑰的詳細資料，然後按一下 [**選取**]。 這可讓您使用客戶管理的金鑰，對命名空間中的待用資料進行加密。 


## <a name="rotate-your-encryption-keys"></a>輪替加密金鑰
您可以使用 Azure 金鑰保存庫迴圈機制來輪替金鑰保存庫中的金鑰。 啟用和到期日期也可以設定為自動執行金鑰輪替。 事件中樞服務會偵測新的金鑰版本，並開始自動使用它們。

## <a name="revoke-access-to-keys"></a>撤銷金鑰的存取權
撤銷加密金鑰的存取權並不會從事件中樞清除資料。 不過，資料無法從事件中樞命名空間存取。 您可以透過存取原則或藉由刪除金鑰來撤銷加密金鑰。 深入瞭解存取原則和保護您的金鑰保存庫，[以安全地存取金鑰保存庫](../key-vault/general/secure-your-key-vault.md)。

撤銷加密金鑰之後，加密命名空間上的事件中樞服務就會變成無法運作。 如果已啟用金鑰的存取權，或還原刪除金鑰，事件中樞服務會挑選金鑰，讓您可以從加密的事件中樞命名空間存取資料。

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄 
設定已啟用 BYOK 之命名空間的診斷記錄，可在使用客戶管理的金鑰加密命名空間時，提供有關作業的必要資訊。 這些記錄可以啟用並于稍後串流至事件中樞，或透過 log analytics 分析，或串流至儲存體來執行自訂分析。 若要深入瞭解診斷記錄，請參閱[Azure 診斷記錄的總覽](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="enable-user-logs"></a>啟用使用者記錄
請遵循下列步驟來啟用客戶管理金鑰的記錄。

1. 在 [Azure 入口網站中，流覽至已啟用 BYOK 的命名空間。
1. 選取 [**監視**] 下的 [**診斷設定**]。

    ![選取 [診斷設定]](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. 選取 [ **+ 新增診斷設定**]。 

    ![選取 [新增診斷設定]](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. 提供**名稱**，然後選取您要將記錄串流至其中的位置。
1. 選取 [ **CustomerManagedKeyUserLogs** ]，然後按一下 [**儲存**]。 此動作會在命名空間上啟用 BYOK 的記錄。

    ![選取 [客戶管理的金鑰使用者記錄檔] 選項](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>記錄檔結構描述 
所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個專案都有使用下表所述格式的字串欄位。 

| Name | 描述 |
| ---- | ----------- | 
| TaskName | 失敗工作的描述。 |
| ActivityId | 用於追蹤的內部識別碼。 |
| category | 定義工作的分類。 例如，如果您的金鑰保存庫中的金鑰已停用，則它會是資訊類別目錄，或者，如果無法解除包裝金鑰，可能會發生錯誤。 |
| resourceId | Azure Resource Manager 資源識別碼 |
| keyVault | 金鑰保存庫的完整名稱。 |
| 索引鍵 | 用來加密事件中樞命名空間的索引鍵名稱。 |
| version | 所使用的金鑰版本。 |
| operation (作業) | 金鑰保存庫中金鑰上執行的作業。 例如，停用/啟用金鑰、換行或解除包裝 |
| 代碼 | 與作業相關聯的程式碼。 範例：錯誤碼404表示找不到索引鍵。 |
| message | 與作業相關聯的任何錯誤訊息 |

以下是客戶管理的金鑰的記錄範例：

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用 Resource Manager 範本來啟用加密
本節說明如何使用**Azure Resource Manager 範本**執行下列工作。 

1. 建立具有受控服務識別的**事件中樞命名空間**。
2. 建立**金鑰保存庫**，並將金鑰保存庫的存取權授與服務識別。 
3. 使用金鑰保存庫資訊（索引鍵/值）更新事件中樞的命名空間。 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>建立具有受控服務識別的事件中樞叢集和命名空間
本節說明如何使用 Azure Resource Manager 範本和 PowerShell，建立具有受控服務識別的 Azure 事件中樞命名空間。 

1. 建立 Azure Resource Manager 範本，以使用受控服務識別建立事件中樞命名空間。 將檔案命名為： **CreateEventHubClusterAndNamespace.js開啟**： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 建立名為的範本參數檔案： **CreateEventHubClusterAndNamespaceParams.json**。 

    > [!NOTE]
    > 取代下列值： 
    > - `<EventHubsClusterName>`-事件中樞叢集的名稱    
    > - `<EventHubsNamespaceName>`-您事件中樞命名空間的名稱
    > - `<Location>`-事件中樞命名空間的位置

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 執行下列 PowerShell 命令來部署範本，以建立事件中樞命名空間。 然後，取出事件中樞命名空間的識別碼，以供稍後使用。 執行命令之前，請將取代 `{MyRG}` 為資源群組的名稱。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>授與金鑰保存庫的事件中樞命名空間身分識別存取權

1. 執行下列命令，以建立已啟用**清除保護**和虛**刪除**的金鑰保存庫。 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (或)    
    
    執行下列命令來更新現有的**金鑰保存庫**。 執行命令之前，請先指定資源群組和金鑰保存庫名稱的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 設定 key vault 存取原則，讓事件中樞命名空間的受控識別可以存取金鑰保存庫中的金鑰值。 使用上一節中事件中樞命名空間的識別碼。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>使用 key vault 中客戶管理的金鑰來加密事件中樞命名空間中的資料
您目前已完成下列步驟： 

1. 已建立具有受控識別的 premium 命名空間。
2. 建立金鑰保存庫，並將金鑰保存庫的存取權授與受控識別。 

在此步驟中，您將使用金鑰保存庫資訊來更新事件中樞命名空間。 

1. 使用下列內容，**在上**建立名為CreateEventHubClusterAndNamespace.js的 JSON 檔案： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. 建立範本參數檔案： **UpdateEventHubClusterAndNamespaceParams.js開啟**。 

    > [!NOTE]
    > 取代下列值： 
    > - `<EventHubsClusterName>`-事件中樞叢集的名稱。        
    > - `<EventHubsNamespaceName>`-您事件中樞命名空間的名稱
    > - `<Location>`-事件中樞命名空間的位置
    > - `<KeyVaultName>`-金鑰保存庫的名稱
    > - `<KeyName>`-金鑰保存庫中的金鑰名稱

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>疑難排解
最佳做法是一律啟用記錄，如前一節所示。 當啟用 BYOK 加密時，它有助於追蹤活動。 它也有助於界定問題的範圍。

以下是啟用 BYOK 加密時，要尋找的常見錯誤代碼。

| 動作 | 錯誤碼 | 產生的資料狀態 |
| ------ | ---------- | ----------------------- | 
| 從金鑰保存庫移除 wrap/解除包裝許可權 | 403 |    無法存取 |
| 從授與 wrap/解除包裝許可權的 AAD 主體移除 AAD 角色成員資格 | 403 |  無法存取 |
| 從金鑰保存庫中刪除加密金鑰 | 404 | 無法存取 |
| 刪除金鑰保存庫 | 404 | 無法存取（假設已啟用虛刪除，這是必要的設定）。 |
| 變更加密金鑰的到期時間，使其已過期 | 403 |   無法存取  |
| 變更 NBF （而不是之前），讓金鑰加密金鑰不在使用中 | 403 | 無法存取  |
| 針對金鑰保存庫防火牆選取 [**允許 MSFT 服務**] 選項，或封鎖具有加密金鑰的金鑰保存庫的網路存取 | 403 | 無法存取 |
| 將金鑰保存庫移至不同的租使用者 | 404 | 無法存取 |  
| 間歇性的網路問題或 DNS/AAD/MSI 中斷 |  | 可使用快取資料加密金鑰來存取 |

> [!IMPORTANT]
> 若要在使用 BYOK 加密的命名空間上啟用異地 DR，配對的次要命名空間必須在專用叢集中，而且必須在其上啟用系統指派的受控識別。 若要深入瞭解，請參閱[適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

## <a name="next-steps"></a>後續步驟
查看下列文章：
- [事件中心概觀](event-hubs-about.md)
- [Key Vault 總覽](../key-vault/general/overview.md)




