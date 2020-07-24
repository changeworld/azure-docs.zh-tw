---
title: Azure 監視器客戶管理的金鑰
description: 提供資訊和步驟來設定客戶管理的金鑰 (CMK)，以使用 Azure Key Vault 金鑰來加密 Log Analytics 工作區中的資料。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/05/2020
ms.openlocfilehash: 3835046e50180e1d1091f5083f276c7c1ad56612
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117376"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure 監視器客戶管理的金鑰 

本文提供背景資訊和步驟來為 Log Analytics 工作區設定客戶管理的金鑰 (CMK)。 設定後，任何傳送至工作區的資料都會以 Azure Key Vault 金鑰加密。

建議先檢閱以下[限制和條件約束](#limitationsandconstraints)，再進行設定。

## <a name="customer-managed-key-cmk-overview"></a>客戶管理的金鑰 (CMK) 概觀

[待用資料加密](../../security/fundamentals/encryption-atrest.md)是組織中常見的隱私權和安全性需求。 您可讓 Azure 完全管理待用資料加密，同時有各種選項可密切管理加密或加密金鑰。

Azure 監視器可確保所有資料和已儲存的查詢都會使用 Microsoft 管理的金鑰（MMK）在待用時加密。 Azure 監視器也會提供使用您自己的金鑰進行加密的選項，其儲存在您的[Azure Key Vault](../../key-vault/general/overview.md)中，並使用系統指派的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)驗證來存取儲存體。 此金鑰（CMK）可能是[軟體或硬體 HSM 保護](../../key-vault/general/overview.md)的。

Azure 監視器使用加密的方式與  [Azure 儲存體加密](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)其運作方式相同。 

CMK 可供控制資料的存取權，並可隨時予以撤銷。 Azure 監視器儲存體的金鑰權限變更一律會在一小時內生效。 過去 14 天內擷取的資料也會保留在經常性快取 (支援 SSD) 中，以進行有效率的查詢引擎作業。 不論 CMK 設定為何，此資料都會持續以 Microsoft 金鑰加密，但您對 SSD 資料的控制則會遵守 [金鑰撤銷](#cmk-kek-revocation)。 我們正致力於在 2020 年下半年以 CMK 加密 SSD 資料。

CMK 功能會在專用的 Log Analytics 叢集上提供。 若要確認您的區域中有所需的容量，我們需要事先允許您的訂用帳戶。 開始設定 CMK 之前，請先使用您的 Microsoft 連絡人來取得您的訂用帳戶。

 [Log Analytics 叢集定價模式](./manage-cost-storage.md#log-analytics-dedicated-clusters)使用從「1000 GB/天」層級起的容量保留。 

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure 監視器中的運作方式

Azure 監視器利用系統所指派受控識別來授與 Azure Key Vault 的存取權。 系統指派的受控識別只能與單一 Azure 資源建立關聯，且 Log Analytics 叢集的身分識別是在叢集層級上支援 -- 這表示 CMK 功能是在專用的 Log Analytics 叢集上提供。 為了支援多個工作區上的 CMK，新的 Log Analytics「叢集」資源會在 Key Vault 與 Log Analytics 工作區之間以中繼身分識別連線的形式執行。 Log Analytics 叢集儲存體使用與「叢集」資源建立關聯的受控識別，以透過 Azure Active Directory 向 Azure Key Vault 進行驗證。 

設定 CMK 之後，任何擷取到與「叢集」資源建立關聯工作區的資料，都會以 Key Vault 中的金鑰加密。 您可隨時解除工作區與「叢集」資源的關聯。 新的資料會擷取到 Log Analytics 儲存體並以 Microsoft 金鑰加密，同時可順暢地查詢新舊資料。


![CMK 概觀](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. 具有受控識別及 Key Vault 權限的 Log Analytics「叢集」資源 -- 此身分識別會傳播至專用的基礎 Log Analytics 叢集儲存體
3. 專用的 Log Analytics 叢集
4. 與「叢集」資源建立關聯的工作區，用於 CMK 加密

## <a name="encryption-keys-operation"></a>加密金鑰作業

儲存體資料加密牽涉到 3 種金鑰類型：

- **KEK** - 金鑰加密金鑰 (CMK)
- **AEK** - 帳戶加密金鑰
- **DEK** - 資料加密金鑰

適用的規則如下：

- Log Analytics 叢集儲存體帳戶會針對每個儲存體帳戶產生唯一的加密金鑰，也就是所謂的 AEK。

- AEK 會用來衍生作為金鑰的 DEK，其可用來加密寫入磁碟的每個資料區塊。

- 當在 Key Vault 中設定金鑰並在「叢集」資源中參考時，Azure 儲存體會將要求傳送至 Azure Key Vault 來包裝及解除包裝 AEK，以執行資料加密和解密作業。

- KEK 絕不會離開 Key Vault，且在 HSM 金鑰的情況下，絕不會離開硬體。

- Azure 儲存體使用與「叢集」資源建立關聯的受控識別，以透過 Azure Active Directory 向 Azure Key Vault 進行驗證和存取。

## <a name="cmk-provisioning-procedure"></a>CMK 佈建程序

1. 允許訂用帳戶--CMK 功能會在專用的 Log Analytics 叢集上傳遞。 若要確認您的區域中有所需的容量，我們需要事先允許您的訂用帳戶。 使用您的 Microsoft 連絡人來允許您的訂用帳戶。
2. 建立 Azure Key Vault 並儲存金鑰
3. 建立「叢集」資源
4. 授與 Key Vault 的權限
5. 建立與 Log Analytics 工作區的關聯

Azure 入口網站不支援此程式，而且布建是透過 PowerShell 或 REST 要求來執行。

> [!IMPORTANT]
> 任何 REST 要求都必須在要求標頭中包含持有人授權權杖。

例如：

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

其中 *eyJ0eXAiO....* 代表完整授權權杖。 

您可使用下列其中一種方法來取得權杖：

1. 使用[應用程式註冊](/graph/auth/auth-concepts#access-tokens)方法。
2. 在 Azure 入口網站
    1. 在「開發人員工具」(F12) 中巡覽至 Azure 入口網站
    1. 在其中一個 "batch?api-version" 執行個體中，尋找「要求標頭」底下的授權字串。 其看起來像："authorization:Bearer eyJ0eXAiO...."。 
    1. 將其複製並新增至 API 呼叫，如下列範例所示。
3. 巡覽至 Azure REST 文件網站。 在任何 API 上按 [試試看]，然後複製持有人權杖。

### <a name="asynchronous-operations-and-status-check"></a>非同步作業和狀態檢查

此設定程序中的某些作業因為無法快速完成而以非同步方式執行。 在設定中使用 REST 要求時，回應一開始會傳回 HTTP 狀態碼200（OK）和標頭，並在接受時使用*Azure AsyncOperation*屬性：
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

然後，您可以藉由將 GET 要求傳送至*AsyncOperation*標頭值來檢查非同步作業的狀態：
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

其回應會包含作業及其「狀態」的相關資訊。 其可以是下列其中一項：

作業進行中
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

金鑰識別碼更新作業進行中
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

「叢集」資源刪除進行中 -- 當刪除具有相關聯工作區的「叢集」資源時，會以非同步作業對每個工作區執行解除關聯作業，這可能需要一段時間。
當刪除沒有相關聯工作區的「叢集」時，則與此無關 -- 在此情況下，會立即刪除「叢集」資源。
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

作業完成
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

作業失敗
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>允許 CMK 部署的訂用帳戶

CMK 功能會在專用的 Log Analytics 叢集上提供。若要確認您的區域中有所需的容量，我們需要事先允許您的訂用帳戶。 請透過 Microsoft 連絡人提供訂用帳戶識別碼。

> [!IMPORTANT]
> CMK 功能有區域性。 Azure Key Vault、「叢集」資源和相關聯的 Log Analytics 工作區必須位於相同區域中，但可位於不同訂用帳戶中。

### <a name="storing-encryption-key-kek"></a>儲存加密金鑰 (KEK)

建立 Azure Key Vault 或使用現有 Azure Key Vault 來產生或匯入將用於資料加密的金鑰。 必須將 Azure Key Vault 設定為可復原，才能保護金鑰和 Azure 監視器中資料的存取權。 您可在 Key Vault 的 [屬性] 下確認此設定，其中應該已啟用 [虛刪除] 和 [清除保護]。

![[虛刪除] 和 [清除保護] 設定](media/customer-managed-keys/soft-purge-protection.png)

這些設定可以透過 CLI 和 PowerShell 更新：

- [虛刪除](../../key-vault/general/overview-soft-delete.md)
- [清除保護](../../key-vault/general/overview-soft-delete.md#purge-protection)可防止強制刪除祕密/保存庫，即使在虛刪除之後也一樣

### <a name="create-cluster-resource"></a>建立「叢集」資源

此資源可作為 Key Vault 與 Log Analytics 工作區之間的中繼身分識別連線。 在您收到允許的訂用帳戶確認之後，請在您的工作區所在的區域建立*Log Analytics 叢集*資源。

建立「叢集」資源時，您必須指定「容量保留」層級 (SKU)。 *容量保留*層級的範圍可以是每日1000到 3000 GB，而您可以在100的步驟中加以更新。 如果您每天都需要超過 3000 GB 的容量保留層級，請與我們聯絡 LAIngestionRate@microsoft.com 。 [深入了解](./manage-cost-storage.md#log-analytics-dedicated-clusters)

*billingType* 屬性可判斷「叢集」資源及其資料的計費屬性：
- 叢集（預設值）--*叢集的容量*保留成本會針對叢集資源*進行分類。*
- *工作區*--叢集的容量保留成本會根據叢集中的工作區按比例進行屬性化，而如果當天的總內嵌資料低於容量保留 *，則會使用叢集資源來*計費。 若要深入瞭解叢集定價模式，請參閱[Log Analytics 專用](manage-cost-storage.md#log-analytics-dedicated-clusters)叢集。 

> [!NOTE]
> * 建立「叢集」資源之後，即可使用 PATCH REST 要求來更新其 *sku*、*keyVaultProperties* 或 *billingType*。
> * 您目前可以使用 REST 要求更新*billingType* ，PowerShell 中不支援這項功能

這是非同步作業，而且可能需要一段時間才能完成。

> [!IMPORTANT]
> 複製並儲存回應，因為接下來的步驟將需要詳細資料。
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity daily-ingestion-gigabyte 
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

建立時會為「叢集」資源指派身分識別。

**回應**

200 確定和標頭。

雖然佈建 Log Analytics 叢集需要一段時間才能完成，但可透過兩種方式來檢查佈建狀態：

1. 從回應複製 Azure-AsyncOperation URL 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. 傳送「叢集」資源的 GET 要求，並查看 *provisioningState* 值。 若正在佈建，則為 *ProvisioningAccount*；若已完成，則為 *Succeeded*。

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

受控識別服務會為「叢集」資源產生 "principalId" GUID。

### <a name="grant-key-vault-permissions"></a>授與 Key Vault 權限

使用新的存取原則更新您的 Key Vault，以*授與叢集資源的*許可權。 基礎 Azure 監視器儲存體會使用這些權限來進行資料加密。 在 Azure 入口網站中開啟 Key Vault 並按一下 [存取原則]，然後按一下 [+ 新增存取原則] 以使用下列設定來建立原則：

- 金鑰權限：選取 [取得]、[包裝金鑰] 和 [將金鑰解除包裝] 權限。
- 選取 [主體]：輸入在上一個步驟的回應中傳回*的 [叢集*資源名稱] 或 [主體-識別碼] 值。

![授與 Key Vault 權限](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要 [取得] 權限，才能確認 Key Vault 已設定為可復原來保護金鑰和 Azure 監視器資料的存取權。

### <a name="update-cluster-resource-with-key-identifier-details"></a>使用金鑰識別碼詳細資料更新叢集資源

此步驟會在 Key Vault 中的初始和未來金鑰版本更新期間執行。 它會通知 Azure 監視器儲存體有關要用於資料加密的金鑰版本。 更新之後，將會使用新金鑰來包裝儲存體金鑰 (AEK) 及將其解除包裝。

若要使用 Key Vault「金鑰識別碼」詳細資料來更新「叢集」資源，請在 Azure Key Vault 中選取金鑰的目前版本，以取得金鑰識別碼詳細資料。

![授與 Key Vault 權限](media/customer-managed-keys/key-identifier-8bit.png)

使用金鑰識別碼詳細資料更新「叢集」資源的 KeyVaultProperties。

這項作業在更新金鑰識別碼詳細資料時是非同步，而且可能需要一段時間才能完成。 更新容量值時，它是同步的。

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> 您可以使用 PATCH 來更新*叢集資源* *sku*、 *keyVaultProperties*或*billingType* 。

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" 包含 Key Vault 金鑰識別碼詳細資料。

**回應**

200 確定和標頭。
傳播金鑰識別碼需要幾分鐘的時間才能完成。 您可透過兩種方式來檢查更新狀態：
1. 從回應複製 Azure-AsyncOperation URL 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. 傳送「叢集」資源的 GET 要求，並查看 *KeyVaultProperties* 屬性。 您最近更新的金鑰識別碼詳細資料應該會在回應中傳回。

當金鑰識別碼更新完成時，「叢集」資源的 GET 要求回應應如下所示：

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>「叢集」資源的工作區關聯

您必須具有工作區和「叢集」資源的「寫入」權限才能執行此作業，包括下列動作：

- 在工作區中：Microsoft.OperationalInsights/workspaces/write
- 在「叢集」資源中：Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> 只有在完成 Log Analytics 叢集佈建之後，才能執行此步驟。 如果在佈建之前建立工作區的關聯並擷取資料，則擷取的資料將會遭到捨棄且無法復原。

這是非同步作業，而且可能需要一段時間才能完成。

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**回應**

200 確定和標頭。

擷取的資料會在關聯作業之後以受控金鑰加密儲存，最多可能需要 90 分鐘的時間才能完成。 您可透過兩種方式來檢查工作區關聯狀態：

1. 從回應複製 Azure-AsyncOperation URL 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. 傳送[工作區 - 取得](/rest/api/loganalytics/workspaces/get)要求並觀察回應，相關聯的工作區在 "features" 下會有 clusterResourceId。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**回應**

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

## <a name="cmk-kek-revocation"></a>CMK (KEK) 撤銷

您可藉由停用金鑰，或刪除 Key Vault 中「叢集」資源存取原則來撤銷資料的存取權。 Log Analytics 叢集儲存體的金鑰權限變更一律會在一小時或更短的時間內生效，且儲存體會變成無法使用。 任何擷取到與「叢集」 ** 資源建立關聯的工作區新資料都會遭到捨棄且無法復原，因此無法存取資料，且這些工作區的查詢會失敗。 只要「叢集」資源和工作區未被刪除，先前擷取的資料就會保留在儲存體中。 無法存取的資料是由資料保留原則所控管，並會在達到保留期時加以清除。 

過去 14 天內擷取的資料也會保留在經常性快取 (支援 SSD) 中，以進行有效率的查詢引擎作業。 這會在金鑰撤銷作業中刪除，且也會變成無法存取。

儲存體會定期輪詢 Key Vault 以嘗試將加密金鑰解除包裝，且一旦存取，資料擷取和查詢就會在 30 分鐘內繼續進行。

## <a name="cmk-kek-rotation"></a>CMK (KEK) 輪替

CMK 的輪替需要使用 Azure Key Vault 中新金鑰版本來明確更新「叢集」資源。 請遵循＜使用金鑰識別碼詳細資料更新「叢集」資源＞步驟中的指示進行。 如果未在「叢集」資源中更新新的金鑰識別碼詳細資料，則 Log Analytics 叢集儲存體會繼續使用先前的金鑰進行加密。 如果先停用或刪除舊金鑰，再更新「叢集」資源中的新金鑰，則會進入[金鑰撤銷](#cmk-kek-revocation)狀態。

您所有的資料在金鑰輪替作業之後仍可供存取，因為資料一律會以帳戶加密金鑰 (AEK) 加密，而 AEK 現在會以 Key Vault 中的新金鑰加密金鑰 (KEK) 版本來加密。

## <a name="cmk-for-queries"></a>查詢的 CMK

Log Analytics 中使用的查詢語言是可表達的，而且可以包含您新增至查詢或查詢語法中的批註中的機密資訊。 有些組織要求這類資訊在 CMK 原則中會受到保護，而且您需要儲存以金鑰加密的查詢。 當連接到您的工作區時，Azure 監視器可讓您將*已儲存的搜尋*和*記錄警示*查詢，儲存在您自己的儲存體帳戶中，並以您的金鑰加密。 

> [!NOTE]
> Log Analytics 查詢可以儲存在不同的存放區，視使用的案例而定。 在下列案例中，查詢會使用 Microsoft 金鑰（MMK）保持加密，無論 CMK 設定： Azure 監視器、Azure 儀表板、Azure 邏輯應用程式、Azure Notebooks 和自動化 Runbook 中的活頁簿。

當您攜帶自己的儲存體（BYOS）並將它與您的工作區產生關聯時，服務會將*已儲存的搜尋*和*記錄警示*查詢上傳至您的儲存體帳戶。 這表示您可以使用您用來加密 Log Analytics 叢集中資料的相同金鑰或不同的金鑰，來控制儲存體帳戶和待用[加密原則](../../storage/common/encryption-customer-managed-keys.md)。 不過，您會負責與該儲存體帳戶相關聯的成本。 

**設定查詢的 CMK 之前的考慮**
* 您必須對您的工作區和儲存體帳戶具有「寫入」許可權
* 請務必在您的 Log Analytics 工作區所在的相同區域中建立您的儲存體帳戶
* 儲存體中的儲存*搜尋*會視為服務成品，其格式可能會變更
* 現有的儲存*搜尋*會從您的工作區中移除。 複製和設定之前所需的任何儲存*搜尋*。 您可以使用[PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)來查看*已儲存的搜尋*
* 不支援查詢歷程記錄，而且您將無法看到您執行的查詢
* 您可以針對儲存查詢的目的，將單一儲存體帳戶與工作區產生關聯，但可用於儲存*的搜尋*和*記錄警示*查詢
* 不支援釘選到儀表板

**為儲存的搜尋查詢設定 BYOS**

將*查詢*的儲存體帳戶關聯至您的工作區--*已儲存-搜尋*查詢會儲存在您的儲存體帳戶中。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

設定之後，任何新*儲存的搜尋*查詢都會儲存在儲存體中。

**設定記錄-警示查詢的 BYOS**

將*警示*的儲存體帳戶與您的工作區產生關聯--*記錄警示*查詢會儲存在您的儲存體帳戶中。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

設定之後，任何新的警示查詢都會儲存在您的儲存體中。

## <a name="cmk-management"></a>CMK 管理

- **取得資源群組的所有「叢集」資源**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **回應**
  
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

- **取得訂用帳戶的所有「叢集」資源**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **回應**
    
  與「資源群組其「叢集」資源」相同的回應，但在訂用帳戶範圍內。

- **更新「叢集」資源中的「容量保留」**

  在相關聯的工作區資料量隨著時間變更，且想要適當地更新容量保留層級時發生。 請遵循[更新「叢集」資源](#update-cluster-resource-with-key-identifier-details)，並提供新的容量值。 其可在1000到 3000 GB 的範圍內，並在100的步驟中。 針對高於每天 3000 GB 的層級，請聯繫您的 Microsoft 連絡人加以啟用。 請注意，您不需要提供完整的 REST 要求主體，但應包含 sku：

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **更新「叢集」資源中的 *billingType***

  *billingType* 屬性可判斷「叢集」資源及其資料的計費屬性：
  - 叢集 (預設) -- 計費會歸類到裝載叢集資源的訂用帳戶
  - 工作區 -- 計費會按比例歸類到裝載工作區的訂用帳戶
  
  請遵循[更新「叢集」資源](#update-cluster-resource-with-key-identifier-details)，並提供新的 billingType 值。 請注意，您不需要提供完整的 REST 要求本文，但應該包含 *billingType*：

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **解除工作區的關聯**

  您需要工作區和「叢集」資源的「寫入」權限才能執行此作業。 您可隨時解除工作區與「叢集」資源的關聯。 解除關聯作業之後新擷取的資料會儲存在 Log Analytics 儲存體中，並以 Microsoft 金鑰加密。 只要使用有效的 Key Vault 金鑰佈建並設定「叢集」資源，即可順暢地查詢在解除關聯前後擷取到工作區的資料。

  這是非同步作業，而且可能需要一段時間才能完成。

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定和標頭。

  解除關聯作業之後擷取的資料會儲存在 Log Analytics 儲存體中，這可能需要 90 分鐘的時間才能完成。 您可透過兩種方式來檢查工作區解除關聯狀態：

  1. 從回應複製 Azure-AsyncOperation URL 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
  2. 傳送[工作區 - 取得](/rest/api/loganalytics/workspaces/get)要求並觀察回應，解除關聯的工作區在 *features* 下不會有 *clusterResourceId*。

- **檢查工作區關聯狀態**
  
  在工作區上執行取得作業，並觀察*clusterResourceId*屬性是否存在於 [*功能*] 下的回應中。 相關聯的工作區會有*clusterResourceId*屬性。

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **刪除「叢集」資源**

  您需要「叢集」資源的「寫入」權限才能執行此作業。 執行虛刪除作業可在 14 天內復原「叢集」資源，包括其資料 (無論刪除是意外還是刻意進行的)。 「叢集」資源名稱仍會在虛刪除期間保留，且無法使用該名稱建立新的叢集。 虛刪除期間之後會釋出「叢集」資源名稱，「叢集」資源和資料會永久刪除且無法復原。 執行刪除作業時，任何相關聯的工作區都會與「叢集」資源解除關聯。 新擷取的資料會儲存在 Log Analytics 儲存體中，並以 Microsoft 金鑰加密。 
  
  工作區解除關聯作業是非同步的，且最多可能需要 90 分鐘的時間才能完成。

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定

- **復原「叢集」資源和資料** 
  
  過去 14 天內遭到刪除的「叢集」資源處於虛刪除狀態，並可連同其資料一起復原。 由於所有工作區都可透過「叢集」資源刪除來與「叢集」資源解除關聯，因此必須在復原後重新建立工作區的關聯，才能進行 CMK 加密。 目前是由產品群組手動執行復原作業。 請針對復原要求使用 Microsoft 通道。

## <a name="limitationsandconstraints"></a>限制和條件約束

- 專用 Log Analytics 叢集支援 CMK，且適用于每天傳送1TB 或以上的客戶。

- 每個區域與訂用*帳戶的叢集資源數目*上限為2

- 如果工作區不需要 CMK，您可以將工作區關聯至*您的叢集資源，* 然後將其取消關聯。  - 在 30 天期間內，特定工作區上的工作區關聯數目限制為 2

- 只有在確認已完成 Log Analytics 叢集布建之後，才應該執行與*叢集資源的*工作區關聯。  在完成之前傳送至工作區的資料將會遭到捨棄且無法復原。

- CMK 加密適用于 CMK 設定後的新內嵌資料。  在 CMK 設定之前擷取的資料會持續以 Microsoft 金鑰加密。  您可順暢地查詢在 CMK 設定前後擷取的資料。

- Azure Key Vault 必須設定為可復原。 這些屬性預設不會啟用，而且應該使用 CLI 或 PowerShell 來設定：<br>
  - [虛刪除](../../key-vault/general/overview-soft-delete.md)
  - 應開啟[清除保護](../../key-vault/general/overview-soft-delete.md#purge-protection)，以防止強制刪除秘密/保存庫，即使在虛刪除之後也一樣。

- 目前不支援將*叢集資源移*到另一個資源群組或訂用帳戶。

- 您的 Azure Key Vault *、叢集*資源和相關聯的工作區必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以位於不同的訂用帳戶中。

- 如果叢集資源與*另一個*叢集資源相關*聯，則*其工作區關聯將會失敗

## <a name="troubleshooting"></a>疑難排解

- Key Vault 可用性的行為
  - 在正常作業中 -- 儲存體會短時間快取 AEK，並定期回到 Key Vault 以解除包裝。
    
  - 暫時性連線錯誤 -- 儲存體處理暫時性錯誤 (逾時、連線失敗、DNS 問題) 的方式是允許將金鑰更長時間保留在快取中，這會克服可用性中的任何小型暫時性問題。 查詢和擷取功能會繼續進行，而不會中斷。
    
  - 即時網站 -- 約有 30 分鐘無法使用會導致儲存體帳戶變成無法使用。 查詢功能會無法使用，且擷取的資料會使用 Microsoft 金鑰快取數小時以避免資料遺失。 還原 Key Vault 的存取權之後，查詢會變成可供使用，且暫時快取的資料會擷取到資料存放區並以 CMK 加密。

  - Key Vault 存取率 -- Azure 監視器儲存體存取 Key Vault 以進行包裝和解除包裝作業的頻率介於 6 到 60 秒之間。

- 如果建立「叢集」資源並立即指定 KeyVaultProperties，則作業可能會失敗，因為在將系統識別指派給「叢集」資源之前無法定義存取原則。

- 如果使用 KeyVaultProperties 來更新現有的「叢集」資源，且 Key Vault 中遺漏 [取得] 金鑰存取原則，則作業將會失敗。

- 如果在建立「叢集」資源時收到衝突錯誤，可能是已在虛刪除期間的過去 14 天內刪除了「叢集」資源。 「叢集」資源名稱仍會在虛刪除期間保留，且無法使用該名稱建立新的叢集。 若永久刪除「叢集」資源，則會在虛刪除期間之後釋出名稱。

- 如果在其他作業仍在進行時更新「叢集」資源，則更新作業將會失敗。

- 如果無法部署「叢集」資源，請確認 Azure Key Vault「叢集」 ** 資源和相關聯的 Log Analytics 工作區位於相同區域中。 但其可位於不同的訂用帳戶中。

- 如果在 Key Vault 中更新金鑰版本，但未在「叢集」資源中更新新的金鑰識別碼詳細資料，則 Log Analytics 叢集會繼續使用先前的金鑰，且資料會變成無法存取。 請在「叢集」資源中更新新的金鑰識別碼詳細資料，以繼續進行資料擷取並能夠查詢資料。

- 某些作業很長，而且可能需要一段時間才能完成--這些是*叢集建立、* *叢集金鑰更新**和叢集*刪除。 您可以透過兩種方式來檢查作業狀態：
  1. 使用 REST 時，請從回應複製 AsyncOperation URL 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
  2. 將 GET 要求傳送至*叢集或工作*區，並觀察回應。 例如，解除關聯的工作區在 [*功能*] 底下不會有*clusterResourceId* 。

- 如需客戶管理的金鑰相關支援和說明，請連絡 Microsoft 連絡人。

- 錯誤訊息
  
  叢集*資源建立*：
  -  400--叢集名稱無效。 叢集名稱可以包含字元 a-z、a-z、0-9 和長度3-63。
  -  400--要求的主體為 null 或格式不正確。
  -  400--SKU 名稱無效。 將 [SKU 名稱] 設定為 capacityReservation。
  -  400--已提供容量，但未 capacityReservation SKU。 將 [SKU 名稱] 設定為 capacityReservation。
  -  400--SKU 缺少容量。 將 [容量] 值設定為1000或更高的步驟（以100（GB）為單位）。
  -  400--SKU 中的容量不在範圍內。 最小值應為1000，最多可達您工作區中 [使用量和估計成本] 底下的 [允許的最大容量]。
  -  400--容量已鎖定30天。 更新後30天內允許降低容量。
  -  400--未設定 SKU。 將 [SKU 名稱] 設為 capacityReservation，並將 [容量] 值設定為1000或更高的步驟（以100為單位）。
  -  400--Identity 為 null 或空白。 使用 systemAssigned 類型來設定身分識別。
  -  400--KeyVaultProperties 是在建立時設定的。 在叢集建立之後更新 KeyVaultProperties。
  -  400--無法立即執行作業。 非同步作業處於 [成功] 以外的狀態。 在執行任何更新作業之前，叢集必須完成其作業。

  叢集*資源更新*
  -  400--叢集處於刪除狀態。 非同步作業進行中。 在執行任何更新作業之前，叢集必須完成其作業。
  -  400--KeyVaultProperties 不是空的，但格式不正確。 請參閱[金鑰識別碼更新](#update-cluster-resource-with-key-identifier-details)。
  -  400--無法驗證 Key Vault 中的金鑰。 可能是因為許可權不足或索引鍵不存在。 確認您已在 Key Vault 中[設定金鑰和存取原則](#grant-key-vault-permissions)。
  -  400--金鑰無法復原。 Key Vault 必須設定為虛刪除和清除保護。 請參閱[Key Vault 檔](../../key-vault/general/overview-soft-delete.md)
  -  400--無法立即執行作業。 請等候非同步作業完成，然後再試一次。
  -  400--叢集處於刪除狀態。 請等候非同步作業完成，然後再試一次。

    叢集*資源取得*：
    -  404--找不到叢集，可能已刪除叢集。 如果您嘗試使用該名稱建立叢集並取得衝突，叢集會在14天內進行虛刪除。 您可以聯絡支援人員來加以復原，或使用其他名稱來建立新的叢集。 

  叢集*資源刪除*
    -  409--在布建狀態下無法刪除叢集。 請等候非同步作業完成，然後再試一次。

  工作區關聯：
  -  404--找不到工作區。 您指定的工作區不存在或已刪除。
  -  409--工作區關聯或進程中的解除關聯作業。
  -  400--找不到叢集，您指定的叢集不存在或已刪除。 如果您嘗試使用該名稱建立叢集並取得衝突，叢集會在14天內進行虛刪除。 您可以聯絡支援人員來復原它。

  工作區解除與：
  -  404--找不到工作區。 您指定的工作區不存在或已刪除。
  -  409--工作區關聯或進程中的解除關聯作業。
