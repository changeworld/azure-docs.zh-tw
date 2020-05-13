---
title: Azure 監視器客戶管理的金鑰
description: 使用 Azure Key Vault 金鑰，設定客戶管理的金鑰（CMK）來加密 Log Analytics 工作區中資料的資訊和步驟。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/07/2020
ms.openlocfilehash: 2838051d8e75ffbe3b7ecc9fbc655f24b57199e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198691"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure 監視器客戶管理的金鑰 

本文提供背景資訊和步驟，為您的 Log Analytics 工作區設定客戶管理的金鑰（CMK）。 一旦設定之後，任何傳送至您工作區的資料都會以您的 Azure Key Vault 金鑰進行加密。

我們建議您在設定之前，先審查下面的[限制和條件約束](#limitations-and-constraints)。

## <a name="disclaimers"></a>免責聲明

- CMK 功能是在專用的 Log Analytics 叢集上提供，這是實體叢集和資料存放區，適用于每天傳送1TB 或更多的客戶

- CMK 定價模式目前無法使用，本文並未涵蓋此模型。 在行事歷年度（CY）2020的第二季中，需要專用 Log Analytics 叢集的計價模式，並適用于任何現有的 CMK 部署。

## <a name="customer-managed-key-cmk-overview"></a>客戶管理的金鑰（CMK）總覽

待用[加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是組織中的常見隱私權和安全性需求。 您可以讓 Azure 完全管理待用加密，而您有各種選項可嚴密管理加密或加密金鑰。

Azure 監視器儲存體可確保在儲存于 Azure 儲存體時，使用 Azure 管理的金鑰進行待用加密的所有資料。 Azure 監視器也會提供使用您自己的金鑰（儲存在[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)中）的資料加密選項，使用系統指派的[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)驗證來存取。 此金鑰可能是[軟體或硬體 HSM 保護](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的。
Azure 監視器使用加密與[Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)運作的方式相同。

Azure 監視器儲存體存取包裝和解除封裝作業 Key Vault 的頻率是介於6到60秒之間。Azure 監視器儲存體一律會在一小時內尊重金鑰許可權的變更。

過去14天內的內嵌資料也會保留在快取記憶體（SSD 支援）中，以進行有效率的查詢引擎作業。 無論 CMK 設定為何，這項資料仍會使用 Microsoft 金鑰進行加密，但您對 SSD 資料的控制會遵守[金鑰撤銷](#cmk-kek-revocation)並無法存取。 我們正努力讓 SSD 資料在2020的後半部中以 CMK 加密。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure 監視器中的運作方式

Azure 監視器會利用系統指派的受控識別，將存取權授與您的 Azure Key Vault。系統指派的受控識別只能與單一 Azure 資源相關聯。 叢集層級支援專用 Log Analytics 叢集的識別，這表示 CMK 功能會在專用的 Log Analytics 叢集上傳遞。 為了支援多個工作區上的 CMK，新的*Log Analytics 叢集*資源會在您的 Key Vault 與 Log Analytics 工作區之間以中繼身分識別連線的形式執行。 此概念會維護專用 Log Analytics 叢集與 Log Analytics*叢集資源之間*的身分識別，而相關聯的工作區資料則會以您的 Key Vault 金鑰來保護。 專用的 Log Analytics 叢集儲存體會使用與叢集資源相關聯的受控識別，透過 \' Azure Active Directory 來驗證和存取您的 Azure Key Vault。 *Cluster*

![CMK 總覽](media/customer-managed-keys/cmk-overview-8bit.png)
1.    客戶的 Key Vault。
2.    客戶的 Log Analytics*叢集資源具有*具有 Key Vault 許可權的受控識別–在專用 Log analytics 叢集層級支援此身分識別。
3.    專用的 Log Analytics 叢集。
4.    *與叢集資源相關*聯的客戶工作區，以進行 CMK 加密。

## <a name="encryption-keys-operation"></a>加密金鑰作業

儲存體資料加密牽涉到3種類型的金鑰：

- **KEK**金鑰加密金鑰（CMK）
- **AEK** -帳戶加密金鑰
- **DEK** -資料加密金鑰

適用的規則如下：

- 專用的 Log Analytics 叢集儲存體帳戶會針對每個儲存體帳戶產生唯一的加密金鑰，也就是所謂的 AEK。

- AEK 是用來衍生 Dek，這是用來加密寫入磁片的每個資料區塊的索引鍵。

- 當您在 Key Vault 中設定金鑰，*並在叢集資源中*參考它時，Azure 儲存體會將要求傳送至您的 Azure Key Vault，以包裝和解除封裝 AEK，以執行資料加密和解密作業。

- 您的 KEK 永遠不會離開 Key Vault，而且在 HSM 金鑰的情況下，它絕不會離開硬體。

- Azure 儲存體使用與*叢集資源相關聯的受控*識別，透過 Azure Active Directory 來驗證和存取 Azure Key Vault。

## <a name="cmk-provisioning-procedure"></a>CMK 布建程式

1. 訂用帳戶允許清單--若要確保您的專用 Log Analytics 叢集區域具有所需的容量，我們必須事先驗證您的訂用帳戶並將其列入允許清單
2. 建立 Azure Key Vault 和儲存金鑰
3. *建立叢集資源*
5. 授與許可權給您的 Key Vault
6. 建立 Log Analytics 工作區的關聯

UI 目前不支援此程式，而且布建程式是透過 REST API 來執行。

> [!IMPORTANT]
> 任何 API 要求都必須在要求標頭中包含持有人授權權杖。

例如：

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

其中*eyJ0eXAiO ...* 表示完整授權 token。 

您可以使用下列其中一種方法來取得權杖：

1. 使用[應用程式註冊](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。
2. 在 Azure 入口網站
    1. 在 [開發人員工具] （F12）中流覽至 Azure 入口網站
    1. 在「批次？ api 版本」實例中，尋找「要求標頭」底下的授權字串。 如下所示： "authorization：持有人 eyJ0eXAiO ...."。 
    1. 根據下列範例，將它複製並新增至您的 API 呼叫。
3. 流覽至 Azure REST 檔網站。 在任何 API 上按「試試看」，並複製持有人權杖。

### <a name="asynchronous-operations-and-status-check"></a>非同步作業和狀態檢查

此設定程式中的部分作業會以非同步方式執行，因為它們無法快速完成。 非同步作業的回應一開始會傳回 HTTP 狀態碼200（OK）和標頭，並在接受時使用*Azure AsyncOperation*屬性：
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

您可以藉由將 GET 要求傳送至*AsyncOperation*標頭值來檢查非同步作業的狀態：
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

回應包含作業和其*狀態*的相關資訊。 它可以是下列其中一個：

作業進行中
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

作業已完成
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作失敗
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

### <a name="subscription-whitelisting"></a>訂用帳戶允許清單

CMK 功能是早期的存取功能。 您*打算建立叢集*資源的訂用帳戶必須事先由 Azure 產品群組列入允許清單。 在 Microsoft 中使用您的連絡人，以提供您的訂用帳戶識別碼。

> [!IMPORTANT]
> CMK 功能是區域。 您*的 Azure Key Vault、叢集資源和*相關聯的 Log Analytics 工作區必須位於相同的區域中，但它們可以位於不同的訂用帳戶中。

### <a name="storing-encryption-key-kek"></a>儲存加密金鑰（KEK）

建立或使用您已經需要產生的 Azure Key Vault，或匯入用於資料加密的金鑰。 Azure Key Vault 必須設定為可復原，以保護您的金鑰和 Azure 監視器中資料的存取權。 您可以在 Key Vault 的 [內容] 下確認此設定，應該啟用 [虛*刪除*] 和 [*清除保護*]。

![虛刪除和清除保護設定](media/customer-managed-keys/soft-purge-protection.png)

這些設定可透過 CLI 和 PowerShell 取得：
- [虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)會防止強制刪除秘密/保存庫，即使在虛刪除之後也一樣

### <a name="create-cluster-resource"></a>建立*叢集*資源

此資源是用來做為您的 Key Vault 與 Log Analytics 工作區之間的中繼身分識別連線。 在您收到訂用帳戶列入允許清單的確認之後，請在您的工作區所在的區域建立*Log Analytics 叢集*資源。

建立*叢集資源時*，您必須指定*容量保留*層級（sku）。 *容量保留*層級的範圍可以是每日1000到 2000 GB，而您可以在稍後的100步驟中更新它。 如果您每天都需要超過 2000 GB 的容量保留層級，請與我們聯絡 LAIngestionRate@microsoft.com 。 [深入了解](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)
    
*BillingType*屬性會決定*叢集資源和其資料的計費*特性：
- 叢集 *（預設*值）--計費的屬性為裝載*叢集資源的*訂用帳戶
- *工作區*--計費是依比例裝載您的工作區的訂用帳戶 

> [!NOTE]
> 建立*叢集資源之後，您可以*使用 PATCH REST 要求，以*sku*、 *keyVaultProperties*或*billingType*來更新它。

**建立**

此 Resource Manager 要求是非同步作業。

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
識別會在建立時指派給*叢集資源。*

**回應**

200 OK 和 header。

當您需要布建專用 Log Analytics 叢集一段時間才能完成時，您可以透過兩種方式來檢查布建狀態：

1. 複製回應中的 [Azure AsyncOperation URL] 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. *在叢集資源上*傳送 GET 要求，並查看*provisioningState*值。 在布建時*ProvisioningAccount* ，並在完成時*成功*。


```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 複製並儲存回應，因為接下來的步驟將需要詳細資料。

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
    "clusterType": "LogAnalytics",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

「PrincipalId」 GUID 是由*叢集資源的*受控識別服務所產生。

### <a name="grant-key-vault-permissions"></a>授與 Key Vault 許可權

使用將許可權授與*叢集資源的*新存取原則，更新您的 Key Vault。 Underlay Azure 監視器儲存體會使用這些許可權來進行資料加密。 在 Azure 入口網站中開啟您的 Key Vault，然後依序按一下 [存取原則] 和 [+ 新增存取原則]，以使用這些設定來建立原則：

- 金鑰許可權：選取 [取得]、[將金鑰換行] 和 [解除包裝金鑰] 許可權。
- 選取 [主體]：輸入在上一個步驟的回應中所傳回的主體識別碼值。

![授與 Key Vault 許可權](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要*取得*許可權，才能確認您的 Key Vault 已設定為可復原，以保護您的金鑰和 Azure 監視器資料的存取權。

### <a name="update-cluster-resource-with-key-identifier-details"></a>以金鑰識別碼詳細資料更新叢集資源

此步驟會在初始期間和 Key Vault 中的未來金鑰版本更新期間執行。 它會通知 Azure 監視器儲存體有關要用於資料加密的金鑰版本。 更新時，您的新金鑰會用來包裝和解除封裝儲存體金鑰（AEK）。

若要使用 Key Vault*金鑰識別碼*的詳細資料來更新*叢集資源，* 請在 Azure Key Vault 中選取金鑰的目前版本，以取得金鑰識別碼的詳細資料。

![授與 Key Vault 許可權](media/customer-managed-keys/key-identifier-8bit.png)

以金鑰識別碼詳細資料更新*叢集資源 KeyVaultProperties* 。

**更新**

此 Resource Manager 要求是更新金鑰識別碼詳細資料時的非同步作業，而在更新容量值時則是同步的。

> [!Note]
> 您可以在*叢集資源中*提供部分主體，以更新*sku*、 *keyVaultProperties*或*billingType*。

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
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       }
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" 包含 Key Vault 的金鑰識別碼詳細資料。

**回應**

200 OK 和 header。
需要幾分鐘的時間才能完成金鑰識別碼的傳播。 您可以透過兩種方式來檢查更新狀態：
1. 複製回應中的 [Azure AsyncOperation URL] 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. *在叢集資源上*傳送 GET 要求，並查看*KeyVaultProperties*屬性。 您最近更新的金鑰識別碼詳細資料應該會在回應中傳回。

當金鑰識別碼更新完成*時，對叢集資源的*GET 要求回應應如下所示：

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
      keyVaultUri: "https://key-vault-name.vault.azure.net",
      kyName: "key-name",
      keyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>*與叢集*資源的工作區關聯

您必須具有工作*區和叢集*資源的「寫入」許可權，才能執行這項作業，包括下列動作：

- 在工作區中： Microsoft.operationalinsights/工作區/寫入
- 在叢集資源中： Microsoft.operationalinsights/ *Cluster* /write

> [!IMPORTANT]
> 只有在完成專用的 Log Analytics 叢集布建之後，才應該執行此步驟。 如果您在布建之前建立工作區與內嵌資料的關聯，內嵌資料將會遭到捨棄，且無法復原。

**建立工作區的關聯**

此 Resource Manager 要求是非同步作業。

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

200 OK 和 header。

在關聯作業之後，會以您的受控金鑰加密內嵌資料，最多可能需要90分鐘的時間才能完成。 您可以透過兩種方式來檢查工作區關聯狀態：

1. 複製回應中的 [Azure AsyncOperation URL] 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. 傳送[工作區-Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)要求和觀察回應，相關聯的工作區在 [功能] 底下會有 clusterResourceId。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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
    "retentionInDays": days,
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

## <a name="cmk-kek-revocation"></a>CMK （KEK）撤銷

您可以藉由停用金鑰或刪除 Key Vault 中的叢集資源存取原則，來撤銷*資料的存取*權。 專用的 Log Analytics 叢集儲存體一律會在一小時或更快的時間內遵守金鑰許可權的變更，而且儲存體將會變成無法使用。 與*您的*叢集資源相關聯之工作區的任何資料內嵌都會被捨棄，且查詢將會失敗。 先前內嵌的資料在儲存體中仍無法存取，因為*您的叢集資源和*工作區不會被刪除。 無法存取的資料是由資料保留原則所控管，並會在達到保留期時清除。 

過去14天內的內嵌資料也會保留在快取記憶體（SSD 支援）中，以進行有效率的查詢引擎作業。 這會在金鑰撤銷作業中刪除，而且也會變成無法存取。

儲存體會定期輪詢您的 Key Vault 以嘗試解除包裝加密金鑰，一旦存取之後，資料內嵌和查詢就會在30分鐘內繼續執行。

## <a name="cmk-kek-rotation"></a>CMK （KEK）旋轉

CMK 的輪替需要在 Azure Key Vault 中使用新的金鑰版本來明確更新*叢集資源。* 依照「使用金鑰識別碼詳細*資料更新叢集*資源」步驟中的指示進行。 如果您未在*叢集資源中更新新的金鑰*識別碼詳細資料，專用的 Log Analytics 叢集儲存體會繼續使用您先前的金鑰。

您的所有資料在金鑰輪替作業之後仍可供存取，包括旋轉之前和之後的資料內嵌，因為資料一律以帳戶加密金鑰（AEK）加密，而 AEK 現在使用 Key Vault 中的新金鑰加密金鑰（KEK）版本加密。

## <a name="limitations-and-constraints"></a>限制和限制

- 專用的 Log Analytics 叢集支援 CMK，適用于每天傳送1TB 或更多的客戶。

- 每個區域與訂用*帳戶的叢集資源數目*上限為2

- 您可以將工作區與您的*叢集資源建立*關聯，然後在不再需要 CMK 的資料或任何其他原因時將它解除關聯。 在30天內，您可以在工作區上執行的工作區關聯數目限制為2

- 只有在您確認已完成專用的 Log Analytics 叢集布建之後，才應該執行與*叢集資源的*工作區關聯。 在完成之前傳送至工作區的資料將會卸載，而且無法復原。

- CMK 加密適用于 CMK 設定後的新內嵌資料。 在 CMK 設定之前所內嵌的資料會以 Microsoft 金鑰保持加密。 您可以順暢地查詢 CMK 設定前後的資料內嵌。

- Azure Key Vault 必須設定為可復原。 這些屬性預設不會啟用，而且應該使用 CLI 或 PowerShell 來設定：

  - 必須開啟虛[刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - 應開啟[清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止強制刪除秘密/保存庫，即使在虛刪除之後也一樣。

- 目前不支援將*叢集資源移*到另一個資源群組或訂用帳戶。

- 您的 Azure Key Vault *、叢集*資源和相關聯的工作區必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以位於不同的訂用帳戶中。

- 如果叢集資源與*另一個*叢集資源相關*聯，則*其工作區關聯將會失敗


## <a name="management"></a>管理性

- **取得資源群組*的所有叢集*資源**

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
              keyVaultUri: "https://key-vault-name.vault.azure.net",
              keyName: "key-name",
              keyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- **取得訂用帳戶*的所有叢集*資源**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **回應**
    
  與「資源群組的叢集*資源」* 相同的回應，但在訂用帳戶範圍中。

- **更新*叢集資源中*的*容量保留***

  當相關聯的工作區資料量隨著時間變更，而且您想要適當地更新容量保留層級時。 遵循[更新叢集*Cluster*資源](#update-cluster-resource-with-key-identifier-details)，並提供新的容量值。 其可在1000到 2000 GB 的範圍內，並在100的步驟中。 針對高於每天 2000 GB 的層級，請聯繫您的 Microsoft 連絡人加以啟用。 請注意，您不需要提供完整的 REST 要求主體，而且應該包含 sku：

  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ``` 

- **更新*叢集資源中*的*billingType***

  *BillingType*屬性會決定*叢集資源和其資料的計費*特性：
  - 叢集 *（預設*值）--計費的屬性為裝載叢集資源的訂用帳戶
  - *工作區*--計費是依比例裝載您的工作區的訂用帳戶
  
  遵循[更新叢集*Cluster*資源](#update-cluster-resource-with-key-identifier-details)，並提供新的 billingType 值。 請注意，您不需要提供完整的 REST 要求主體，而且應該包含*billingType*：

  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **取消關聯工作區**

  您需要工作區和叢集資源的「寫入」*許可權，才能*執行此作業。 您可以隨時將工作區與您的*叢集資源相關*聯。 取消關聯作業之後的新內嵌資料會儲存在 Log Analytics 儲存體中，並以 Microsoft 金鑰加密。 只要使用有效的 Key Vault 金鑰來布建和設定叢集資源，您就可以查詢在取消關聯前後內嵌至工作*區的資料*。

  此 Resource Manager 要求是非同步作業。

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  ```

  **回應**

  200 OK 和 header。

  內嵌資料在取消關聯作業儲存在 Log Analytics 儲存體之後，這可能需要90分鐘的時間才能完成。 您可以透過兩種方式來檢查工作區取消關聯狀態：

  1. 複製回應中的 [Azure AsyncOperation URL] 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
  2. 傳送[工作區-Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)要求和觀察回應，解除關聯的工作區在 [*功能*] 下將不會有*clusterResourceId* 。


- **刪除您*的*叢集資源**

  您需要叢集資源的「寫入」*許可權，才能*執行此作業。 執行虛刪除作業可在14天內*復原您的*叢集資源，包括其資料（不論是意外或故意刪除）。 叢集*資源名稱*會在虛刪除期間保持保留，而且您無法使用該名稱建立新的叢集。 在虛刪除期間，*叢集資源名稱*會釋出，您的叢集*資源和*資料會永久刪除且無法復原。 任何相關聯的工作區都會在刪除作業時 *，與叢集資源解除*關聯。 新的內嵌資料會儲存在 Log Analytics 儲存體中，並使用 Microsoft 金鑰進行加密。 工作區取消關聯作業是非同步，最多可能需要90分鐘的時間才能完成。

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定

- **復原您的*叢集資源和*資料** 
  
  在過去14天內*刪除的叢集資源處於*虛刪除狀態，而且可以使用其資料來復原。 由於所有工作區都會在刪除*時與叢集資源解除*關聯，因此您必須在復原 CMK 加密之後重新關聯您的工作區。 目前產品群組會手動執行復原操作。 使用您的 Microsoft 通道來取得復原要求。

## <a name="troubleshooting"></a>疑難排解
- Key Vault 可用性的行為
  - 在正常作業中，儲存體快取 AEK 的時間很短，並會回到 Key Vault 以定期解除包裝。
    
  - 暫時性連線錯誤-儲存體處理暫時性錯誤（超時、連線失敗、DNS 問題）的方式，是允許金鑰長時間保持在快取中，而且這會克服可用性的任何小型短暫中斷。 查詢和內嵌功能會繼續進行，而不會中斷。
    
  - 即時網站--30 分鐘內無法使用，將導致儲存體帳戶變成無法使用。 查詢功能無法使用，而且內嵌的資料會以 Microsoft 金鑰快取數小時，以避免資料遺失。 當還原 Key Vault 的存取權時，查詢就會變成可用，而且暫時快取的資料會內嵌至資料存放區，並使用 CMK 加密。

- 如果您建立叢集*資源並*立即指定 KeyVaultProperties，此作業可能會失敗，因為在系統識別指派給*叢集資源之前*，無法定義存取原則。

- 如果您使用 KeyVaultProperties 來*更新現有的*叢集資源，但 Key Vault 中遺漏了「Get」金鑰存取原則，則作業將會失敗。

- 如果您嘗試刪除與工作區相關*聯的叢集資源，* 刪除作業將會失敗。

- 如果您在建立叢集資源時收到衝突，可能是因為您在過去14天內*刪除了**叢集資源，* 而且它是在虛刪除期間。 叢集*資源名稱*會在虛刪除期間保持保留，而且您無法使用該名稱建立新的叢集。 當刪除*叢集資源時，此名稱*會在虛刪除期間釋放。

- 當作業正在進行時，*如果您更新叢集資源，* 作業將會失敗。

- 如果*您無法部署叢集資源，* 請確認您的 *Azure Key Vault、叢集*   資源和相關聯的 Log Analytics 工作區都位於相同的區域。 可以在不同的訂用帳戶中。

- 如果您在 Key Vault 中更新金鑰版本，而不更新*叢集資源中的新*金鑰識別碼詳細資料，Log Analytics 叢集會繼續使用您先前的金鑰，而您的資料將會變成無法存取。 更新*叢集資源中*的新金鑰識別碼詳細資料，以繼續內嵌資料並能夠查詢資料。

- 如需與客戶管理的金鑰相關的支援和說明，請在 Microsoft 中使用您的連絡人。

