---
title: Azure 監視器客戶管理的金鑰
description: 使用 Azure Key Vault 金鑰，設定客戶管理的金鑰來加密 Log Analytics 工作區中資料的資訊和步驟。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 6c1f323828eb48b61b38370bc2fe56d4c93bf036
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127204"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure 監視器客戶管理的金鑰 

Azure 監視器中的資料會使用 Microsoft 管理的金鑰進行加密。 您可以使用自己的加密金鑰來保護工作區中的資料和儲存的查詢。 當您指定客戶管理的金鑰時，該金鑰是用來保護和控制資料的存取權，一旦設定之後，傳送至工作區的任何資料都會使用您的 Azure Key Vault 金鑰進行加密。 客戶自控金鑰可提供更大的彈性來管理存取控制。

建議先檢閱以下[限制和條件約束](#limitationsandconstraints)，再進行設定。

## <a name="customer-managed-key-overview"></a>客戶管理的金鑰總覽

[靜態加密](../../security/fundamentals/encryption-atrest.md) 是組織中常見的隱私權和安全性需求。 您可以讓 Azure 完全管理待用加密，而您可以使用各種選項來更緊密地管理加密和加密金鑰。

Azure 監視器使用 Microsoft 管理的金鑰 (MMK) ，確保所有資料和儲存的查詢都會加密。 Azure 監視器也會提供使用您自己的金鑰（儲存在 [Azure Key Vault](../../key-vault/general/overview.md)中）進行加密的選項，讓您可以隨時撤銷對資料的存取權。 Azure 監視器使用加密等同于 [Azure 儲存體加密](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) 的運作方式。

客戶管理的金鑰會在 [專用](../log-query/logs-dedicated-clusters.md) 叢集上傳遞，以提供更高的保護層級和控制。 使用 Microsoft 管理的金鑰或客戶管理的金鑰，在服務層級進行資料內嵌的資料加密兩次，而在基礎結構層級使用兩個不同的加密演算法和兩個不同的金鑰。 [雙精確度加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) 可防止其中一個加密演算法或金鑰可能會遭到入侵的案例。 在此情況下，額外的加密層級會繼續保護您的資料。 專用叢集也可讓您使用加密 [箱](#customer-lockbox-preview) 控制來保護您的資料。

過去 14 天內擷取的資料也會保留在經常性快取 (支援 SSD) 中，以進行有效率的查詢引擎作業。 無論客戶管理的金鑰設定為何，此資料仍會以 Microsoft 金鑰加密，但您對 SSD 資料的控制會遵守 [金鑰撤銷](#key-revocation)。 我們正在努力將 SSD 資料以客戶管理的金鑰加密，在2021的前半部。

Log Analytics 專用叢集會使用每日 1000 GB 起的容量保留 [定價模型](../log-query/logs-dedicated-clusters.md#cluster-pricing-model) 。

> [!IMPORTANT]
> 由於有暫時性的容量限制，我們要求您在建立叢集之前預先註冊。 使用您的連絡人進入 Microsoft，或開啟支援要求以註冊您的訂用帳戶識別碼。

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>客戶管理的金鑰在 Azure 監視器中的運作方式

Azure 監視器使用受控識別將存取權授與您的 Azure Key Vault。 叢集層級支援 Log Analytics 叢集的身分識別。 為了在多個工作區上允許客戶管理的金鑰保護，新的 Log Analytics *叢集資源會* 以您 Key Vault 與 Log Analytics 工作區之間的中繼身分識別連線形式來執行。 叢集的儲存體會使用與叢集資源相關聯的受控識別，透過 \' Azure Active Directory 向您的 Azure Key Vault 進行驗證。  

在客戶管理的金鑰設定之後，連結至您專用叢集之工作區的新內嵌資料會以您的金鑰加密。 您可以隨時從叢集取消連結工作區。 然後，新的資料會內嵌至 Log Analytics 儲存體，並使用 Microsoft 金鑰進行加密，而您可以順暢地查詢新的和舊的資料。

> [!IMPORTANT]
> 客戶管理的金鑰功能是區域性的。 您的 Azure Key Vault、叢集和連結的 Log Analytics 工作區必須位於相同的區域中，但它們可以在不同的訂用帳戶中。

![客戶管理的金鑰總覽](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. 具有受控識別及 Key Vault 權限的 Log Analytics「叢集」資源 -- 此身分識別會傳播至專用的基礎 Log Analytics 叢集儲存體
3. 專用的 Log Analytics 叢集
4. 連結 *至叢集* 資源的工作區 

### <a name="encryption-keys-operation"></a>加密金鑰作業

儲存體資料加密牽涉到 3 種金鑰類型：

- **KEK** 金鑰加密金鑰 (客戶管理的金鑰) 
- **AEK** - 帳戶加密金鑰
- **DEK** - 資料加密金鑰

適用的規則如下：

- Log Analytics 叢集儲存體帳戶會為每個儲存體帳戶產生唯一的加密金鑰，也就是所謂的 AEK。
- AEK 用來衍生 Dek，也就是用來加密寫入磁片之每個資料區塊的金鑰。
- 當您在 Key Vault 中設定金鑰並在叢集中參考時，Azure 儲存體會將要求傳送至 Azure Key Vault 以包裝和解除包裝 AEK，以執行資料加密和解密作業。
- 您的 KEK 永遠不會離開 Key Vault，在 HSM 金鑰的情況下，它永遠不會離開硬體。
- Azure 儲存體使用與 *叢集資源相關* 聯的受控識別，透過 Azure Active Directory 驗證和存取 Azure Key Vault。

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed 金鑰布建步驟

1. 註冊您的訂用帳戶以允許叢集建立
1. 建立 Azure Key Vault 並儲存金鑰
1. 正在建立叢集
1. 授與 Key Vault 的權限
1. 使用金鑰識別碼詳細資料更新叢集
1. 連結 Log Analytics 工作區

目前的 Azure 入口網站不支援客戶管理的金鑰設定，並可透過 [PowerShell](/powershell/module/az.operationalinsights/)、 [CLI](/cli/azure/monitor/log-analytics) 或 [REST](/rest/api/loganalytics/) 要求執行布建。

### <a name="asynchronous-operations-and-status-check"></a>非同步作業和狀態檢查

部分設定步驟會以非同步方式執行，因為它們無法快速完成。 `status`In 回應可以是下列其中一項： ' InProgress '、' 更新 '、' 刪除 '、' Succeeded ' 或 ' Failed '，錯誤碼為。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

不適用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

不適用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="rest"></a>[REST](#tab/rest)

使用 REST 時，回應一開始會傳回 HTTP 狀態碼 200 (確定) 和標頭，並在接受時使用 *Azure AsyncOperation* 屬性：
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

您可以藉由將 GET 要求傳送至 *Azure AsyncOperation* 標頭中的端點，來檢查非同步作業的狀態：
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

### <a name="allowing-subscription"></a>允許訂用帳戶

使用您的連絡人進入 Microsoft，或在 Log Analytics 中開啟支援要求，以提供您的訂用帳戶識別碼。

## <a name="storing-encryption-key-kek"></a>儲存加密金鑰 (KEK)

建立 Azure Key Vault 或使用現有 Azure Key Vault 來產生或匯入將用於資料加密的金鑰。 必須將 Azure Key Vault 設定為可復原，才能保護金鑰和 Azure 監視器中資料的存取權。 您可在 Key Vault 的 [屬性] 下確認此設定，其中應該已啟用 [虛刪除] 和 [清除保護]。

![[虛刪除] 和 [清除保護] 設定](media/customer-managed-keys/soft-purge-protection.png)

您可以透過 CLI 和 PowerShell 在 Key Vault 中更新這些設定：

- [虛刪除](../../key-vault/general/soft-delete-overview.md)
- [清除保護](../../key-vault/general/soft-delete-overview.md#purge-protection)可防止強制刪除祕密/保存庫，即使在虛刪除之後也一樣

## <a name="create-cluster"></a>建立叢集

> [!NOTE]
> 叢集支援兩種 [受控識別類型](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)：系統指派和使用者指派，且每一種都可以根據您的案例而定。 系統指派的受控識別更簡單，而且當身分識別設定為 "SystemAssigned" 時，會在叢集建立時自動建立 `type` --此身分識別可稍後用來授與叢集對您 Key Vault 的存取權。** 如果您想要在叢集建立期間定義客戶管理的金鑰時建立叢集，您應該事先在 Key Vault 中定義金鑰和使用者指派的身分識別，然後使用這些設定來建立叢集：身分識別 `type` 為 "*UserAssigned*"， `UserAssignedIdentities` 包含身分識別的資源識別碼和 `keyVaultProperties` 金鑰詳細資料。

> [!IMPORTANT]
> 您目前無法使用使用者指派的受控識別來定義客戶管理的金鑰，如果您的 Key Vault 位於 Private-Link (vNet) ，而且您可以在此情況下使用系統指派的受控識別。

請依照 [專用叢集文章](../log-query/logs-dedicated-clusters.md#creating-a-cluster)中所述的程式進行操作。 

## <a name="grant-key-vault-permissions"></a>授與 Key Vault 權限

在 Key Vault 中建立存取原則，以將許可權授與您的叢集。 基礎 Azure 監視器儲存體會使用這些許可權。 在 Azure 入口網站中開啟您的 Key Vault，然後按一下 *[存取原則]* ，然後按一下 *[+ 新增存取原則]* ，以使用這些設定來建立原則：

- 金鑰許可權：選取 [ *取得*]、[ *包裝金鑰* ] 和 [解除包裝 *金鑰*]。
- 選取 [主體]：根據叢集 (系統或使用者指派的受控識別中使用的身分識別類型) 為系統指派的受控識別或使用者指派的受控識別名稱輸入叢集名稱或叢集主體識別碼。

![授與 Key Vault 權限](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要 [取得] 權限，才能確認 Key Vault 已設定為可復原來保護金鑰和 Azure 監視器資料的存取權。

## <a name="update-cluster-with-key-identifier-details"></a>使用金鑰識別碼詳細資料更新叢集

叢集中的所有作業都需要 `Microsoft.OperationalInsights/clusters/write` 採取動作許可權。 您可以透過包含動作的擁有者或參與者，或透過 `*/write` 包含動作的 Log Analytics 參與者角色，授與此許可權 `Microsoft.OperationalInsights/*` 。

此步驟會使用要用於資料加密的金鑰和版本來更新 Azure 監視器儲存體。 更新時，您的新金鑰會用來包裝和解除包裝儲存體金鑰 (AEK) 。

在 Azure Key Vault 中選取您金鑰的目前版本，以取得金鑰識別碼的詳細資料。

![授與 Key Vault 權限](media/customer-managed-keys/key-identifier-8bit.png)

使用金鑰識別碼詳細資料更新叢集中的 KeyVaultProperties。

作業是非同步，可能需要一段時間才能完成。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

不適用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**回應**

它會將金鑰傳播幾分鐘才能完成。 您可透過兩種方式來檢查更新狀態：
1. 從回應複製 Azure-AsyncOperation URL 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. 在叢集中傳送 GET 要求，並查看 *KeyVaultProperties* 屬性。 您最近更新的金鑰應該會在回應中傳回。

當金鑰更新完成時，GET 要求的回應應該如下所示：200確定和標頭
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

---

## <a name="link-workspace-to-cluster"></a>將工作區連結至叢集

> [!IMPORTANT]
> 只有在完成 Log Analytics 叢集佈建之後，才能執行此步驟。 如果您在布建之前連結工作區和內嵌資料，將會捨棄內嵌資料且無法復原。

您必須擁有工作區和叢集的「寫入」許可權，才能執行這項作業，包括 `Microsoft.OperationalInsights/workspaces/write` 和 `Microsoft.OperationalInsights/clusters/write` 。

請依照 [專用叢集文章](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-cluster)中所述的程式進行操作。

## <a name="key-revocation"></a>金鑰撤銷

您可以停用金鑰，或刪除 Key Vault 中叢集的存取原則，以撤銷資料的存取權。 

> [!IMPORTANT]
> - 如果您的叢集是以使用者指派的受控識別來設定，則設定會 `UserAssignedIdentities` `None` 暫停叢集並防止存取您的資料，但您無法在不開啟支援要求的情況下還原撤銷並啟用叢集。 這項限制不適用於系統指派的受控識別。
> - 建議的金鑰撤銷動作是在您的 Key Vault 中停用您的金鑰。

叢集儲存體一律會在一小時內或更快的時間內取得金鑰許可權的變更，而且儲存體將會變成無法使用。 與您的叢集連結之工作區的任何新資料內嵌都會被捨棄且無法復原，資料會變成無法存取，而且這些工作區上的查詢會失敗。 只要您的叢集和您的工作區未刪除，先前內嵌的資料就會保留在儲存體中。 無法存取的資料是由資料保留原則所控管，並會在達到保留期時加以清除。 過去 14 天內擷取的資料也會保留在經常性快取 (支援 SSD) 中，以進行有效率的查詢引擎作業。 這會在金鑰撤銷作業中刪除，且也會變成無法存取。

叢集的儲存體會定期輪詢您的 Key Vault，以嘗試解除包裝加密金鑰，並在存取之後，在30分鐘內將資料內嵌和查詢繼續進行。

## <a name="key-rotation"></a>金鑰輪替

客戶管理的金鑰輪替需要以 Azure Key Vault 中的新金鑰版本來明確更新叢集。 [更新具有金鑰識別碼詳細資料的](#update-cluster-with-key-identifier-details)叢集。 如果您未更新叢集中的新金鑰版本，Log Analytics 叢集儲存體將繼續使用您先前的金鑰進行加密。 如果您在更新叢集中的新金鑰之前，停用或刪除舊金鑰，將會進入 [金鑰撤銷](#key-revocation) 狀態。

您所有的資料在金鑰輪替作業之後仍可供存取，因為資料一律會以帳戶加密金鑰 (AEK) 加密，而 AEK 現在會以 Key Vault 中的新金鑰加密金鑰 (KEK) 版本來加密。

## <a name="customer-managed-key-for-queries"></a>客戶管理的查詢金鑰

Log Analytics 中使用的查詢語言是可表達的，且可以包含您新增至查詢或在查詢語法中的批註中的機密資訊。 某些組織要求將這類資訊保持受客戶管理的金鑰原則保護，而您需要儲存以金鑰加密的查詢。 Azure 監視器可讓您在連線到您的工作區時，將 *已儲存的搜尋* 和 *記錄警示* 查詢儲存在您自己的儲存體帳戶中，並以您的金鑰加密。 

> [!NOTE]
> 您可以根據所使用的案例，將 Log Analytics 查詢儲存在不同的存放區。 無論客戶管理的金鑰設定： Azure 監視器、Azure 儀表板、Azure 邏輯應用程式、Azure Notebooks 和自動化 Runbook 的活頁簿，在下列案例中，查詢仍會以 Microsoft key (MMK) 加密。

當您將自己的儲存體 (BYOS) 並將其連結至您的工作區時，服務會將 *已儲存的搜尋* 和 *記錄警示* 查詢上傳至您的儲存體帳戶。 這表示您可以使用您用來將 Log Analytics 叢集中的資料加密的相同金鑰或不同的金鑰，來控制儲存體帳戶和 [靜態加密原則](../../storage/common/customer-managed-keys-overview.md) 。 不過，您要負責支付與該儲存體帳戶相關聯的成本。 

**針對查詢設定客戶管理的金鑰之前的考慮**
* 您必須具有您的工作區和儲存體帳戶的「寫入」許可權
* 請務必在您的 Log Analytics 工作區所在的相同區域中建立儲存體帳戶
* 儲存體中的儲存 *搜尋* 會被視為服務成品，而其格式可能會變更
* 現有的儲存 *搜尋* 會從您的工作區中移除。 複製及任何您需要的儲存 *搜尋* ，再進行設定。 您可以使用 [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)來查看 *已儲存的搜尋*
* 不支援查詢記錄，您將無法看到您執行的查詢
* 您可以將單一儲存體帳戶連結至工作區以儲存查詢，但可用於 *儲存的搜尋* 和 *記錄警示* 查詢
* 不支援釘選到儀表板

**針對儲存的搜尋查詢設定 BYOS**

將 *查詢* 的儲存體帳戶連結至您的工作區--儲存 *的搜尋* 查詢會儲存在您的儲存體帳戶中。 

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

不適用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
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

---

設定之後，任何新 *儲存的搜尋* 查詢都會儲存在您的儲存體中。

**設定 BYOS 的記錄警示查詢**

將 *警示* 的儲存體帳戶連結至您的工作區-- *記錄-警示* 查詢會儲存在您的儲存體帳戶中。 

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

不適用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
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

---

設定之後，任何新的警示查詢都會儲存在您的儲存體中。

## <a name="customer-lockbox-preview"></a>客戶加密箱 (preview) 

加密箱可讓您控制在支援要求期間，核准或拒絕 Microsoft 工程師要求存取您的資料。

在 Azure 監視器中，您可以控制連結至 Log Analytics 專用叢集之工作區中的資料。 加密密碼控制適用于儲存在 Log Analytics 專用叢集中的資料，其會在您的加密箱保護的訂用帳戶下，保持隔離在叢集的儲存體帳戶中。  

深入瞭解 [Microsoft Azure 的客戶加密箱](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed 金鑰作業

專用叢集上提供 Customer-Managed 金鑰，而這些作業是在[專用](../log-query/logs-dedicated-clusters.md#change-cluster-properties)叢集發行項中參考

- 取得資源群組中的所有叢集  
- 取得訂用帳戶中的所有叢集
- 更新叢集中的 *容量保留*
- 更新叢集中的 *billingType*
- 從叢集取消連結工作區
- 刪除叢集

## <a name="limitations-and-constraints"></a>限制和條件約束

- 專用的 Log Analytics 叢集支援客戶管理的金鑰，適用于每天傳送1TB 或更多的客戶。

- 每個區域和訂用帳戶的叢集數目上限為2

- 連結的工作區到叢集的最大值是1000

- 您可以將工作區連結至您的叢集，然後將它取消連結。 在30天的期間內，特定工作區的工作區連結作業數限制為2。

- 只有在您確認 Log Analytics 叢集布建完成後，才應將工作區連結傳送至叢集。  在完成之前傳送至工作區的資料將會遭到捨棄且無法復原。

- 客戶管理的金鑰加密會在設定時間之後套用至新內嵌的資料。 在設定之前所內嵌的資料，會以 Microsoft 金鑰維持加密狀態。 您可以順暢地查詢客戶管理的金鑰設定之前和之後的資料內嵌。

- Azure Key Vault 必須設定為可復原。 這些屬性預設不會啟用，而且應該使用 CLI 或 PowerShell 進行設定：<br>
  - [虛刪除](../../key-vault/general/soft-delete-overview.md)
  - [清除保護](../../key-vault/general/soft-delete-overview.md#purge-protection) 應該開啟，以防止在虛刪除之後強制刪除秘密/保存庫。

- 目前不支援將叢集移至另一個資源群組或訂用帳戶。

- 您的 Azure Key Vault、叢集和連結的工作區必須位於相同的區域中，且在相同的 Azure Active Directory (Azure AD) 租使用者中，但它們可以在不同的訂用帳戶中。

- 如果叢集連結至另一個叢集，則工作區連結將會失敗。

- 目前中國未提供加密箱。 

- 針對在支援區域中從2020年10月建立的叢集，會自動設定[雙重加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)。 您可以透過叢集上的 GET 要求，確認您的叢集是否已設定雙精度加密，並觀察 `"isDoubleEncryptionEnabled"` 屬性值- `true` 適用于已啟用雙重加密的叢集。 
  - 如果您建立叢集並收到錯誤「<的區功能變數名稱稱> 不支援叢集的雙重加密」，您仍然可以建立不含雙重加密的叢集。 將 `"properties": {"isDoubleEncryptionEnabled": false}` 屬性新增至 REST 要求主體。
  - 建立叢集之後，就無法變更雙重加密設定。

  - 如果您的叢集是以使用者指派的受控識別來設定，則設定會 `UserAssignedIdentities` `None` 暫停叢集並防止存取您的資料，但您無法在不開啟支援要求的情況下還原撤銷並啟用叢集。 這項限制不適用於系統指派的受控識別。

  - 您目前無法使用使用者指派的受控識別來定義客戶管理的金鑰，如果您的 Key Vault 位於 Private-Link (vNet) ，而且您可以在此情況下使用系統指派的受控識別。

## <a name="troubleshooting"></a>疑難排解

- Key Vault 可用性的行為
  - 在正常作業中 -- 儲存體會短時間快取 AEK，並定期回到 Key Vault 以解除包裝。
    
  - 暫時性連線錯誤 -- 儲存體處理暫時性錯誤 (逾時、連線失敗、DNS 問題) 的方式是允許將金鑰更長時間保留在快取中，這會克服可用性中的任何小型暫時性問題。 查詢和擷取功能會繼續進行，而不會中斷。
    
  - 即時網站 -- 約有 30 分鐘無法使用會導致儲存體帳戶變成無法使用。 查詢功能會無法使用，且擷取的資料會使用 Microsoft 金鑰快取數小時以避免資料遺失。 當還原 Key Vault 的存取權時，查詢就會變成可用，而且暫存快取的資料會內嵌到資料存放區，並使用客戶管理的金鑰進行加密。

  - Key Vault 存取率 -- Azure 監視器儲存體存取 Key Vault 以進行包裝和解除包裝作業的頻率介於 6 到 60 秒之間。

- 如果您建立叢集並立即指定 KeyVaultProperties，此作業可能會失敗，因為在將系統身分識別指派給叢集之前無法定義存取原則。

- 如果您使用 KeyVaultProperties 來更新現有的叢集，但 Key Vault 中缺少 ' Get ' 金鑰存取原則，此作業將會失敗。

- 如果您在建立叢集時收到衝突錯誤，可能是因為您在過去14天內刪除了您的叢集，而且它是在虛刪除期間。 在虛刪除期間，叢集名稱仍保持保留，且您無法使用該名稱建立新的叢集。 當您永久刪除叢集時，此名稱會在虛刪除期限之後釋放。

- 如果您在作業進行時更新您的叢集，此作業將會失敗。

- 如果您無法部署叢集，請確認您的 Azure Key Vault、叢集和連結的 Log Analytics 工作區位於相同的區域中。 但其可位於不同的訂用帳戶中。

- 如果您在 Key Vault 中更新金鑰版本，但未更新叢集中的新金鑰識別碼詳細資料，Log Analytics 叢集將會繼續使用您先前的金鑰，而您的資料將變成無法存取。 更新叢集中的新金鑰識別碼詳細資料，以繼續資料內嵌和查詢資料的能力。

- 某些作業很長，可能需要一段時間才能完成，這些作業包括叢集建立、叢集金鑰更新和叢集刪除。 您可以透過兩種方式來檢查作業狀態：
  1. 使用 REST 時，請從回應中複製 Azure-AsyncOperation URL 值，並遵循 [非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
  2. 將 GET 要求傳送至叢集或工作區，並觀察回應。 例如，未連結的工作區不會有 [*功能*] 下的 *clusterResourceId* 。

- 錯誤訊息
  
  **叢集建立**
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

  **叢集更新**
  -  400--叢集處於刪除中狀態。 非同步作業正在進行中。 叢集必須在執行任何更新操作之前完成其作業。
  -  400--KeyVaultProperties 不是空的，但格式不正確。 請參閱 [金鑰識別碼更新](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details)。
  -  400--無法驗證 Key Vault 中的金鑰。 可能是因為許可權不足或索引鍵不存在。 確認您已在 Key Vault 中 [設定金鑰和存取原則](../platform/customer-managed-keys.md#grant-key-vault-permissions) 。
  -  400--金鑰無法復原。 Key Vault 必須設定為虛刪除和清除保護。 請參閱 [Key Vault 檔](../../key-vault/general/soft-delete-overview.md)
  -  400--目前無法執行作業。 等候非同步作業完成，然後再試一次。
  -  400--叢集處於刪除中狀態。 等候非同步作業完成，然後再試一次。

  **叢集 Get**
    -  404--找不到叢集，可能已刪除叢集。 如果您嘗試建立具有該名稱的叢集，併發生衝突，則叢集會處於虛刪除14天。 您可以聯絡支援人員加以復原，或使用另一個名稱來建立新的叢集。 

  **叢集刪除**
    -  409--無法在布建狀態下刪除叢集。 等候非同步作業完成，然後再試一次。

  **工作區連結**
  -  404--找不到工作區。 您指定的工作區不存在或已刪除。
  -  409--進程中的工作區連結或取消連結作業。
  -  400--找不到叢集，您指定的叢集不存在或已刪除。 如果您嘗試建立具有該名稱的叢集，併發生衝突，則叢集會處於虛刪除14天。 您可以聯絡支援人員來復原。

  **工作區取消連結**
  -  404--找不到工作區。 您指定的工作區不存在或已刪除。
  -  409--進程中的工作區連結或取消連結作業。
## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Log Analytics 專用叢集計費](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- 深入瞭解 [Log Analytics 工作區的正確設計](../platform/design-logs-deployment.md)