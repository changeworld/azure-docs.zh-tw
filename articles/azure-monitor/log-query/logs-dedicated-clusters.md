---
title: Azure 監視器記錄專用叢集
description: 在監視資料中內嵌超過 1 TB 的客戶，可能會使用專用而非共用叢集
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 93b05a5535b80d0e0d1a07c88aa9b19052f1b703
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562670"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure 監視器記錄專用叢集

Azure 監視器記錄專用叢集是部署選項，可為 Azure 監視器記錄客戶啟用先進的功能。 具有專用叢集的客戶可以選擇要在這些叢集上裝載的工作區。

需要專用叢集的功能如下：

- **[客戶管理的金鑰](../platform/customer-managed-keys.md)** -使用由客戶提供及控制的金鑰來加密叢集資料。
- 加密 **[箱](../platform/customer-managed-keys.md#customer-lockbox-preview)**-客戶可以控制 Microsoft 支援工程師存取資料的要求。
- **[雙精確度加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** 可防止其中一個加密演算法或金鑰可能會遭到入侵的案例。 在此情況下，額外的加密層級會繼續保護您的資料。
- **[多個工作區](../log-query/cross-workspace-query.md)** -如果客戶在生產環境中使用一個以上的工作區，則使用專用叢集可能是合理的。 如果所有工作區都在相同的叢集上，跨工作區查詢的執行速度會更快。 使用專用叢集的方式也可能更符合成本效益，因為指派的容量保留層會將所有叢集內嵌納入考慮，並套用至其所有的工作區，即使其中有些較小，而且不符合容量保留折扣的資格。

專用叢集會要求客戶使用每日至少有 1 TB 資料的容量進行認可。 遷移至專用叢集很簡單。 資料遺失或服務中斷。 

> [!IMPORTANT]
> 專用叢集已獲得核准，而且可在生產環境部署中完全支援。 不過，由於暫時的容量限制，我們需要預先註冊才能使用此功能。 請透過 Microsoft 連絡人提供訂用帳戶識別碼。

## <a name="management"></a>管理性 

專用叢集是透過代表 Azure 監視器記錄叢集的 Azure 資源來管理。 所有作業都是使用 PowerShell 或 REST API 在此資源上完成。

一旦建立叢集之後，就可以將它設定和連結的工作區。 當工作區連結至叢集時，傳送至工作區的新資料就會位於叢集上。 只有與叢集位於相同區域的工作區可以連結至叢集。 您可以從叢集中 unliked 工作區，但有一些限制。 本文將包含這些限制的詳細資料。 

使用 Microsoft 管理的金鑰或 [客戶管理的金鑰](../platform/customer-managed-keys.md)，將資料內嵌到專用叢集的兩次，並使用兩個不同的加密演算法和兩個不同的金鑰一次，在基礎結構層級進行加密。 [雙精確度加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) 可防止其中一個加密演算法或金鑰可能會遭到入侵的案例。 在此情況下，額外的加密層級會繼續保護您的資料。 專用叢集也可讓您使用加密 [箱](../platform/customer-managed-keys.md#customer-lockbox-preview) 控制來保護您的資料。

叢集層級上的所有作業都需要叢集的「 `Microsoft.OperationalInsights/clusters/write` 動作」許可權。 您可以透過包含動作的擁有者或參與者，或透過 `*/write` 包含動作的 Log Analytics 參與者角色，授與此許可權 `Microsoft.OperationalInsights/*` 。 如需有關 Log Analytics 許可權的詳細資訊，請參閱 [Azure 監視器中的記錄管理資料和工作區的存取](../platform/manage-access.md)。 


## <a name="cluster-pricing-model"></a>叢集定價模型

Log Analytics 專用叢集會使用至少 1000 GB/天的容量保留定價模型。 高於保留層級的使用量則會以隨用隨付費率計費。  容量保留定價資訊可在 [Azure 監視器定價] 頁面]( https://azure.microsoft.com/pricing/details/monitor/)取得。  

叢集容量保留層級是使用中的參數，以程式設計方式透過 Azure Resource Manager 來設定 `Capacity` `Sku` 。 `Capacity` 會以 GB 為單位來指定，而且可以有「1000 GB/天」以上、增量單位為「100 GB/天」的值。

叢集上的使用量有兩種計費模式。 設定叢集時，參數可以指定這些 `billingType` 參數。 

1. 叢集 **：在** 此案例中 (是預設) ，內嵌資料的計費是在叢集層級進行。 系統會匯總與叢集相關聯之每個工作區的內嵌資料數量，以計算叢集的每日帳單。 

2. **工作區**：您叢集的容量保留成本會依叢集內的工作區進行比例化， (在會計入每個工作區 [Azure 資訊安全中心](../../security-center/index.yml) 的每個節點配置。 ) 

如果您的工作區使用舊版的每個節點定價層，則在連結至叢集時，將會根據針對叢集容量保留所內嵌的資料進行計費，而且不再是每個節點的費用。 Azure 資訊安全中心的每個節點資料配置都將繼續套用。

[這裡]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)提供 Log Analytics 專用叢集的詳細資料。

## <a name="asynchronous-operations-and-status-check"></a>非同步作業和狀態檢查

部分設定步驟會以非同步方式執行，因為它們無法快速完成。 回應中的狀態可以是下列其中一項：「InProgress」、「更新」、「刪除」、「成功」或「失敗」，包括錯誤碼。 使用 REST 時，回應一開始會傳回 HTTP 狀態碼 202 (接受的) 和標頭與 Azure-AsyncOperation 屬性：

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

您可將 GET 要求傳送至 Azure-AsyncOperation 標頭值來檢查非同步作業的狀態：

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>建立叢集

您會先建立叢集資源以開始建立專用叢集。

必須指定下列屬性：

- **ClusterName**：用於管理用途。 使用者不會對此名稱公開。
- **ResourceGroupName**：針對任何 Azure 資源，叢集都屬於資源群組。 建議您使用中央 IT 資源群組，因為叢集通常是由組織中的許多小組所共用。 如需更多設計考慮，請參閱 [設計 Azure 監視器記錄部署](../platform/design-logs-deployment.md)
- **位置**：叢集位於特定的 Azure 區域。 只有位於此區域的工作區可以連結至此叢集。
- **SkuCapacity**：您必須在建立 *叢集資源時*，指定 (sku) 的 *容量保留* 層級。 *容量保留* 層級的範圍可以是每日 1000 gb 到 3000 gb。 如有需要，您可以在稍後的100步驟中加以更新。 如果您每天需要超過 3000 GB 的容量保留層級，請洽詢我們 LAIngestionRate@microsoft.com 。 如需叢集成本的詳細資訊，請參閱 [管理 Log Analytics 叢集的成本](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

建立 *叢集資源之後* ，您可以編輯其他屬性，例如 *Sku*、* keyVaultProperties 或 *billingType*。 請參閱下方的詳細資料。

> [!WARNING]
> 叢集建立會觸發資源配置和布建。 此作業最多可能需要一小時的時間才能完成。 建議您以非同步方式執行它。

建立叢集的使用者帳戶必須具有標準的 Azure 資源建立許可權：和叢集 `Microsoft.Resources/deployments/*` 寫入權限 `(Microsoft.OperationalInsights/clusters/write)` 。

### <a name="create"></a>建立 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*呼叫* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*回應*

應為 202 (接受的) 和標頭。

### <a name="check-cluster-provisioning-status"></a>檢查叢集布建狀態

布建 Log Analytics 叢集需要一些時間才能完成。 您可以透過數種方式來檢查布建狀態：

- 使用資源組名執行 Get-AzOperationalInsightsCluster PowerShell 命令，並檢查 ProvisioningState 屬性。 此值在布建時 *ProvisioningAccount* ，而且會在完成時 *成功* 。
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- 從回應複製 Azure-AsyncOperation URL 值，並遵循非同步作業狀態檢查。

- 傳送「叢集」資源的 GET 要求，並查看 *provisioningState* 值。 此值在布建時 *ProvisioningAccount* ，而且會在完成時 *成功* 。

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **回應**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*PrincipalId* GUID 是由 *叢集資源的* 受控識別服務所產生。

## <a name="link-a-workspace-to-cluster"></a>將工作區連結至叢集

當工作區連結到專用叢集時，內嵌到工作區的新資料會路由傳送至新的叢集，而現有的資料仍會保留在現有的叢集上。 如果使用客戶管理的金鑰來加密專用叢集 (CMK) ，則只有新的資料會以金鑰加密。 系統會將這項差異與使用者抽象化，而使用者只會在系統于後端執行跨叢集查詢時，照常查詢工作區。

叢集最多可連結至100個工作區。 連結的工作區位於與叢集相同的區域中。 若要保護系統後端並避免資料片段，每個月的工作區無法連結至叢集兩次以上。

若要執行連結作業，您必須擁有工作區和 *叢集資源的* 「寫入」許可權：

- 在工作區中： *OperationalInsights/workspace/write*
- 在叢集資源中： *OperationalInsights/cluster/write*

除了計費層面以外，連結的工作區會保留其本身的設定，例如資料保留的長度。
工作區和叢集可以位於不同的訂用帳戶中。 如果 Azure Lighthouse 是用來將這兩個專案對應到單一租使用者，則工作區和叢集可能會位於不同的租使用者中。

在任何叢集作業中，只有在完成 Log Analytics 叢集布建之後，才可以執行連結工作區。

> [!WARNING]
> 將工作區連結至叢集需要同步處理多個後端元件，並確保快取序列化。 此作業最多可能需要兩個小時才能完成。 建議您以非同步方式執行。


**PowerShell**

使用下列 PowerShell 命令來連結至叢集：

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

使用下列 REST 呼叫來連結至叢集：

*發送*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*回應*

202 (已接受) 和標頭。

### <a name="check-workspace-link-status"></a>檢查工作區連結狀態
  
如果您使用客戶管理的金鑰，則在關聯作業之後，內嵌資料會以您的受控金鑰加密儲存，最多可能需要90分鐘的時間才能完成。 

您可透過兩種方式來檢查工作區關聯狀態：

- 從回應複製 Azure-AsyncOperation URL 值，並遵循非同步作業狀態檢查。

- 在工作區上執行取得作業，並觀察 [ *clusterResourceId* ] 屬性是否存在於 [ *功能*] 下的 [回應] 中。

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*呼叫*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*回應*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>變更叢集屬性

在 *您建立叢集資源並* 完整布建之後，您可以使用 PowerShell 或 REST API 在叢集層級編輯其他屬性。 除了在叢集建立期間可用的屬性以外，您還可以在布建叢集之後設定額外的屬性：

- **keyVaultProperties** -更新 Azure Key Vault 中的金鑰。 請參閱 [更新叢集與金鑰識別碼詳細資料](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details)。 它包含下列參數： *KeyVaultUri*、 *KeyName*、 *KeyVersion*。 
- **billingType** - *billingType* 屬性會決定 *叢集資源和其資料的計費* 屬性：
  - 叢集 (預設) -*叢集的容量* 保留成本會對叢集資源 **進行分類。**
  - **工作區** -叢集的容量保留成本會依叢集中的工作區比例分類，而如果當天的總內嵌資料是在容量保留下 *，則會以叢集資源計費* 部分使用量。 若要深入瞭解叢集定價模型，請參閱 [Log Analytics 專用](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 叢集。 

> [!NOTE]
> PowerShell 不支援 *billingType* 屬性。

### <a name="get-all-clusters-in-resource-group"></a>取得資源群組中的所有叢集
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*呼叫*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*回應*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>取得訂用帳戶中的所有叢集

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*呼叫*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*回應*
    
與「資源群組中的叢集」相同，但在訂用帳戶範圍中。



### <a name="update-capacity-reservation-in-cluster"></a>更新叢集中的容量保留

當連結的工作區的資料量隨著時間變更，而且您想要適當地更新容量保留層級時。 容量是以 GB 為單位來指定，且每天可有 1000 GB 或更高的值，以 100 GB/天為單位遞增。 請注意，您不需要提供完整的 REST 要求主體，但應該包含 sku。

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*呼叫*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>更新叢集中的 billingType

*BillingType* 屬性會決定叢集和其資料的計費屬性：
- 叢集 (預設) -- 計費會歸類到裝載叢集資源的訂用帳戶
- 工作區 -- 計費會按比例歸類到裝載工作區的訂用帳戶

**REST**

*呼叫*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>從叢集取消連結工作區

您可以從叢集取消連結工作區。 從叢集取消連結工作區之後，與此工作區相關聯的新資料不會傳送到專用叢集。 此外，也不會再透過叢集完成工作區計費。 未連結工作區的舊資料可能會留在叢集上。 如果此資料是使用客戶管理的金鑰進行加密 (CMK) ，則會保留 Key Vault 秘密。 系統會將這項變更從 Log Analytics 使用者抽象化。 使用者可以像往常一樣直接查詢工作區。 系統會視需要在後端執行跨叢集查詢，而不會向使用者指示。  

> [!WARNING] 
> 一個月內的特定工作區有兩個連結作業的限制。 請花一些時間考慮並據以規劃取消連結動作。

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

使用下列 PowerShell 命令，將工作區從叢集取消連結：

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>刪除叢集

可以刪除專用的叢集資源。 在刪除所有工作區之前，您必須先將其從叢集取消連結。 您需要「叢集」資源的「寫入」權限才能執行此作業。 

一旦刪除叢集資源之後，實體叢集就會進入清除和刪除流程。 刪除叢集會刪除儲存在叢集上的所有資料。 資料可能來自過去連結到叢集的工作區。

過去 14 天內遭到刪除的「叢集」資源處於虛刪除狀態，並可連同其資料一起復原。 由於 *所有的工作* 區都已從叢集資源刪除解除 *關聯，因此* 您必須在復原後重新建立工作區的關聯。 使用者無法執行復原作業，請聯絡您的 Microsoft 通道或支援復原要求。

在刪除後的14天內，叢集資源名稱會保留，且無法供其他資源使用。

> [!WARNING] 
> 每個訂用帳戶有三個群集的限制。 作用中和已虛刪除的叢集都會計入這一部分。 客戶不應建立可建立和刪除叢集的週期性程式。 它對 Log Analytics 後端系統有顯著的影響。

**PowerShell**

使用下列 PowerShell 命令來刪除叢集：

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

使用下列 REST 呼叫來刪除叢集：

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定

## <a name="limits-and-constraints"></a>限制和條件約束

- 每個區域和訂用帳戶的叢集數目上限為2

- 連結的工作區到叢集的最大值是1000

- 您可以將工作區連結至您的叢集，然後將它取消連結。 在30天的期間內，特定工作區的工作區連結作業數限制為2。

- 只有在您確認 Log Analytics 叢集布建完成後，才應將工作區連結傳送至叢集。  在完成之前傳送至工作區的資料將會遭到捨棄且無法復原。

- 目前不支援將叢集移至另一個資源群組或訂用帳戶。

- 如果叢集連結至另一個叢集，則工作區連結將會失敗。

- 目前中國未提供加密箱。 

- 針對在支援區域中從2020年10月建立的叢集，會自動設定[雙重加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)。 您可以透過叢集上的 GET 要求，確認您的叢集是否已設定雙精度加密，並觀察 `"isDoubleEncryptionEnabled"` 屬性值- `true` 適用于已啟用雙重加密的叢集。 
  - 如果您建立叢集並收到錯誤「<的區功能變數名稱稱> 不支援叢集的雙重加密」，您仍然可以建立不含雙重加密的叢集。 將 `"properties": {"isDoubleEncryptionEnabled": false}` 屬性新增至 REST 要求主體。
  - 建立叢集之後，就無法變更雙重加密設定。

## <a name="troubleshooting"></a>疑難排解

- 如果您在建立叢集時收到衝突錯誤，可能是因為您在過去14天內刪除了您的叢集，而且它處於虛刪除狀態。 在虛刪除期間，叢集名稱仍保持保留，且您無法使用該名稱建立新的叢集。 當您永久刪除叢集時，此名稱會在虛刪除期限之後釋放。

- 如果您在作業進行時更新您的叢集，此作業將會失敗。

- 某些作業很長，可能需要一段時間才能完成，這些作業包括叢集建立、叢集金鑰更新和叢集刪除。 您可以透過兩種方式來檢查作業狀態：
  - 使用 REST 時，請從回應中複製 Azure-AsyncOperation URL 值，並遵循 [非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
  - 將 GET 要求傳送至叢集或工作區，並觀察回應。 例如，未連結的工作區不會有 [*功能*] 下的 *clusterResourceId* 。

- 錯誤訊息
  
  叢集建立：
  -  400--叢集名稱無效。 叢集名稱可包含字元 a-z、a-z、0-9 和長度3-63。
  -  400--要求的主體為 null 或格式不正確。
  -  400--SKU 名稱無效。 將 SKU 名稱設定為 capacityReservation。
  -  400--已提供容量，但未 capacityReservation SKU。 將 SKU 名稱設定為 capacityReservation。
  -  400--SKU 缺少容量。 在 100 (GB) 的步驟中，將容量值設定為1000或更高。
  -  400--SKU 中的容量不在範圍內。 最小值應為1000，最高可達您工作區中 [使用量和估計成本] 下可用的最大容量。
  -  400--容量已鎖定30天。 更新後的30天內允許減少容量。
  -  400--未設定 SKU。 在 100 (GB) 的步驟中，將 SKU 名稱設為 capacityReservation，並將容量值設定為1000或更高。
  -  400--Identity 為 null 或空白。 設定具有 systemAssigned 類型的身分識別。
  -  400--KeyVaultProperties 是在建立時設定。 在叢集建立之後更新 KeyVaultProperties。
  -  400--目前無法執行作業。 非同步作業處於 [成功] 以外的狀態。 叢集必須在執行任何更新操作之前完成其作業。

  叢集更新
  -  400--叢集處於刪除中狀態。 非同步作業正在進行中。 叢集必須在執行任何更新操作之前完成其作業。
  -  400--KeyVaultProperties 不是空的，但格式不正確。 請參閱 [金鑰識別碼更新](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details)。
  -  400--無法驗證 Key Vault 中的金鑰。 可能是因為許可權不足或索引鍵不存在。 確認您已在 Key Vault 中 [設定金鑰和存取原則](../platform/customer-managed-keys.md#grant-key-vault-permissions) 。
  -  400--金鑰無法復原。 Key Vault 必須設定為虛刪除和清除保護。 請參閱 [Key Vault 檔](../../key-vault/general/soft-delete-overview.md)
  -  400--目前無法執行作業。 等候非同步作業完成，然後再試一次。
  -  400--叢集處於刪除中狀態。 等候非同步作業完成，然後再試一次。

  Cluster Get：
    -  404--找不到叢集，可能已刪除叢集。 如果您嘗試建立具有該名稱的叢集，併發生衝突，則叢集會處於虛刪除14天。 您可以聯絡支援人員加以復原，或使用另一個名稱來建立新的叢集。 

  叢集刪除
    -  409--無法在布建狀態下刪除叢集。 等候非同步作業完成，然後再試一次。

  工作區連結：
  -  404--找不到工作區。 您指定的工作區不存在或已刪除。
  -  409--進程中的工作區連結或取消連結作業。
  -  400--找不到叢集，您指定的叢集不存在或已刪除。 如果您嘗試建立具有該名稱的叢集，併發生衝突，則叢集會處於虛刪除14天。 您可以聯絡支援人員來復原。

  工作區取消連結：
  -  404--找不到工作區。 您指定的工作區不存在或已刪除。
  -  409--進程中的工作區連結或取消連結作業。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Log Analytics 專用叢集計費](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- 深入瞭解 [Log Analytics 工作區的正確設計](../platform/design-logs-deployment.md)
