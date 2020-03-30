---
title: 配置您自己的金鑰以加密靜態 Azure 事件中心資料
description: 本文提供有關如何配置自己的金鑰以加密 Azure 事件中心資料靜態的資訊。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 43e626355feaf1e51fc840f82506c559a1859b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621996"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>配置客戶管理的金鑰，以便使用 Azure 門戶加密靜態 Azure 事件中心資料
Azure 事件中心通過 Azure 存儲服務加密 （Azure SSE） 提供靜態資料加密。 事件中心依賴于 Azure 存儲來存儲資料，預設情況下，與 Azure 存儲一起存儲的所有資料都使用 Microsoft 管理的金鑰進行加密。 

## <a name="overview"></a>總覽
Azure 事件中心現在支援使用 Microsoft 管理的金鑰或客戶管理的金鑰（自帶金鑰 + BYOK）加密靜態資料的選項。 此功能使您能夠創建、旋轉、禁用和撤銷對用於加密靜態 Azure 事件中心資料的客戶託管金鑰的存取權限。

啟用 BYOK 功能是命名空間上的一次性設置過程。

> [!NOTE]
> BYOK 功能由[事件中心專用單租戶](event-hubs-dedicated-overview.md)群集支援。 無法為標準事件中心命名空間啟用它。

您可以使用 Azure 金鑰保存庫來管理金鑰並審核金鑰使用方式。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-overview.md)

本文演示如何使用 Azure 門戶使用客戶管理的金鑰配置金鑰保存庫。 若要瞭解如何使用 Azure 門戶創建金鑰保存庫，請參閱[快速入門：使用 Azure 門戶從 Azure 金鑰保存庫設置和檢索機密](../key-vault/quick-create-portal.md)。

> [!IMPORTANT]
> 將客戶管理的金鑰與 Azure 事件中心一起使用需要金鑰保存庫配置兩個必需屬性。 它們是：**虛刪除**和**不清除**。 預設情況下，在 Azure 門戶中創建新金鑰保存庫時，將啟用這些屬性。 但是，如果需要在現有金鑰保存庫上啟用這些屬性，則必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰
要在 Azure 門戶中啟用客戶管理的金鑰，請按照以下步驟操作：

1. 導航到事件中心專用群集。
1. 選擇要在其上啟用 BYOK 的命名空間。
1. 在事件中心命名空間的 **"設置"** 頁上，選擇 **"加密**"。 
1. 選擇**客戶管理金鑰靜態加密，** 如下圖所示。 

    ![啟用客戶託管金鑰](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>設置帶金鑰的金鑰保存庫
啟用客戶管理的金鑰後，需要將客戶託管金鑰與 Azure 事件中心命名空間相關聯。 事件中心僅支援 Azure 金鑰保存庫。 如果在上一節中啟用**了使用客戶管理的金鑰選項的加密**，則需要將金鑰導入 Azure 金鑰保存庫。 此外，金鑰必須為金鑰配置 **"虛刪除****"和"不清除**"。 這些設置可以使用[PowerShell](../key-vault/key-vault-soft-delete-powershell.md)或[CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)進行配置。

1. 要創建新金鑰保存庫，請按照 Azure 金鑰保存庫[快速入門](../key-vault/key-vault-overview.md)。 有關導入現有金鑰的詳細資訊，請參閱[有關金鑰、機密和證書](../key-vault/about-keys-secrets-and-certificates.md)。
1. 要在創建保存庫時同時打開虛刪除和清除保護，請使用[az 金鑰保存庫創建](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 要向現有保存庫（已啟用虛刪除）添加清除保護，請使用[az keyvault 更新](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 通過以下步驟創建鍵：
    1. 若要建立新的金鑰，從 [設定]**** 下方的 [金鑰]**** 功能表中選取 [產生/匯入]****。
        
        ![選擇"生成/導入"按鈕](./media/configure-customer-managed-key/select-generate-import.png)
    1. 將 [選項]**** 設定為 [產生]****，並為金鑰提供名稱。

        ![建立金鑰](./media/configure-customer-managed-key/create-key.png) 
    1. 現在，您可以選擇此金鑰以與事件中心命名空間關聯，以便從下拉清單中進行加密。 

        ![從金鑰保存庫中選擇金鑰](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 填寫金鑰的詳細資訊，然後按一下 **"選擇**"。 這將啟用使用客戶託管金鑰在命名空間上靜態資料加密。 


## <a name="rotate-your-encryption-keys"></a>旋轉加密金鑰
可以使用 Azure 金鑰保存庫輪換機制在金鑰保存庫中旋轉金鑰。 有關詳細資訊，請參閱[設置金鑰輪換和審核](../key-vault/key-vault-key-rotation-log-monitoring.md)。 啟動和到期日期也可以設置為自動輪換金鑰。 事件中心服務將檢測新的金鑰版本，並自動開始使用它們。

## <a name="revoke-access-to-keys"></a>撤銷對金鑰的訪問
撤銷對加密金鑰的訪問不會從事件中心清除資料。 但是，無法從事件中心命名空間訪問資料。 您可以通過訪問策略或刪除金鑰來撤銷加密金鑰。 詳細瞭解訪問策略和保護金鑰保存庫從[安全訪問金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。

一旦加密金鑰被吊銷，加密命名空間上的事件中心服務將變得無法運行。 如果啟用了對金鑰的訪問或已還原刪除金鑰，事件中心服務將選取該金鑰，以便可以從加密的事件中心命名空間訪問資料。

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄 
為啟用 BYOK 的命名空間設置診斷日誌可提供有關命名空間使用客戶管理的金鑰加密時操作所需的資訊。 這些日誌可以啟用，然後資料流到事件中心，或通過日誌分析進行分析，或資料流到存儲以執行自訂分析。 要瞭解有關診斷日誌的詳細資訊，請參閱[Azure 診斷日誌概述](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="enable-user-logs"></a>啟用使用者日誌
按照以下步驟啟用客戶管理金鑰的日誌。

1. 在 Azure 門戶中，導航到啟用了 BYOK 的命名空間。
1. 選擇 **"監視**"下的**診斷設置**。

    ![選擇診斷設置](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. 選擇 **+添加診斷設置**。 

    ![選擇添加診斷設置](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. 提供**名稱**並選擇要將日誌資料流到的位置。
1. 選擇**客戶管理金鑰使用者日誌**並**保存**。 此操作啟用命名空間上的 BYOK 日誌。

    ![選擇客戶管理的關鍵使用者日誌選項](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>記錄檔結構描述 
所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個條目都有使用下表中描述的格式的字串欄位。 

| 名稱 | 描述 |
| ---- | ----------- | 
| TaskName | 失敗工作的描述。 |
| ActivityId | 用於跟蹤的內部 ID。 |
| category | 定義任務的分類。 例如，如果禁用金鑰保存庫中的金鑰，則該金鑰將是資訊類別，或者如果無法解包金鑰，則該金鑰可能會出錯。 |
| resourceId | Azure Resource Manager 資源識別碼 |
| 鍵庫 | 金鑰保存庫的全名。 |
| 索引鍵 | 用於加密事件中心命名空間的金鑰名稱。 |
| version | 正在使用的金鑰的版本。 |
| operation (作業) | 對金鑰保存庫中的金鑰執行的操作。 例如，禁用/啟用金鑰、換行或取消包裝 |
| 代碼 | 與操作關聯的代碼。 示例：錯誤代碼 404 表示未找到金鑰。 |
| message | 與操作關聯的任何錯誤訊息 |

下面是客戶託管金鑰的日誌示例：

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

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用資源管理器範本啟用加密
本節演示如何使用**Azure 資源管理器範本**執行以下任務。 

1. 使用託管服務標識創建**事件中心命名空間**。
2. 創建**金鑰保存庫**並授予對金鑰保存庫的服務標識存取權限。 
3. 使用金鑰保存庫資訊（鍵/值）更新事件中心命名空間。 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>使用託管服務標識創建事件中心群集和命名空間
本節演示如何使用 Azure 資源管理器範本和 PowerShell 使用託管服務標識創建 Azure 事件中心命名空間。 

1. 創建 Azure 資源管理器範本以創建具有託管服務標識的事件中心命名空間。 命名檔：**創建事件HubCluster和命名空間.json**： 

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
2. 創建名為：**創建事件HubCluster和命名空間帕拉森.json 的**範本參數檔。 

    > [!NOTE]
    > 取代下列值： 
    > - `<EventHubsClusterName>`- 事件中心群集的名稱    
    > - `<EventHubsNamespaceName>`- 事件中心命名空間的名稱
    > - `<Location>`- 事件中心命名空間的位置

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
3. 運行以下 PowerShell 命令以部署範本以創建事件中心命名空間。 然後，檢索事件中心命名空間的 ID 以以後使用它。 在`{MyRG}`運行命令之前，請替換為資源組的名稱。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>授予事件中心命名空間對金鑰保存庫的標識訪問

1. 運行以下命令以創建具有**清除保護和****虛刪除**功能的金鑰保存庫。 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (或)    
    
    運行以下命令以更新**現有金鑰保存庫**。 在運行命令之前，指定資源組和金鑰保存庫名稱的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 設置金鑰保存庫訪問策略，以便事件中心命名空間的託管標識可以訪問金鑰保存庫中的鍵值。 使用上一節中的事件中心命名空間的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>使用金鑰保存庫的客戶託管金鑰組事件中心命名空間中的資料進行加密
到目前為止，您已完成以下步驟： 

1. 使用託管標識創建高級命名空間。
2. 創建金鑰保存庫並授予對金鑰保存庫的託管標識存取權限。 

在此步驟中，您將使用金鑰保存庫資訊更新事件中心命名空間。 

1. 創建名為**CreateEventHubCluster 和Namespace.json**的 JSON 檔，內容如下： 

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

2. 創建範本參數檔：**更新事件HubCluster和命名空間帕拉森.** 

    > [!NOTE]
    > 取代下列值： 
    > - `<EventHubsClusterName>`- 事件中心群集的名稱。        
    > - `<EventHubsNamespaceName>`- 事件中心命名空間的名稱
    > - `<Location>`- 事件中心命名空間的位置
    > - `<KeyVaultName>`- 金鑰保存庫的名稱
    > - `<KeyName>`- 金鑰保存庫中金鑰的名稱

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
3. 運行以下 PowerShell 命令以部署資源管理器範本。 在`{MyRG}`運行命令之前，請替換為資源組的名稱。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>疑難排解
最佳做法是，始終啟用上一節所示的日誌。 它有助於在啟用 BYOK 加密時跟蹤活動。 它還有助於縮小問題的範圍。

以下是啟用 BYOK 加密時要查找的常見錯誤代碼。

| 動作 | 錯誤碼 | 結果的資料狀態 |
| ------ | ---------- | ----------------------- | 
| 從金鑰保存庫中刪除換行/解包許可權 | 403 |    無法存取 |
| 從授予換行/解包許可權的 AAD 主體中刪除 AAD 角色成員身份 | 403 |  無法存取 |
| 從金鑰保存庫中刪除加密金鑰 | 404 | 無法存取 |
| 刪除金鑰保存庫 | 404 | 無法訪問（假定啟用了虛刪除，這是必需的設置。 |
| 更改加密金鑰的過期期限，使其已過期 | 403 |   無法存取  |
| 更改 NBF（以前不），以便金鑰加密金鑰不處於活動狀態 | 403 | 無法存取  |
| 為金鑰保存庫防火牆選擇"**允許 MSFT 服務**"選項，或者阻止網路訪問具有加密金鑰的金鑰保存庫 | 403 | 無法存取 |
| 將金鑰保存庫移動到其他租戶 | 404 | 無法存取 |  
| 間歇性網路問題或 DNS/AAD/MSI 中斷 |  | 使用緩存的資料加密金鑰訪問 |

> [!IMPORTANT]
> 要在使用 BYOK 加密的命名空間上啟用 Geo-DR，用於配對的輔助命名空間必須位於專用群集中，並且必須啟用系統分配的託管標識。 要瞭解更多資訊，請參閱[Azure 資源的託管標識](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="next-steps"></a>後續步驟
查看下列文章：
- [事件中心概觀](event-hubs-about.md)
- [金鑰保存庫概述](../key-vault/key-vault-overview.md)




