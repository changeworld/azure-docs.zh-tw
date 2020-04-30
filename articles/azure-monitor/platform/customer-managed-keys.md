---
title: Azure 監視器客戶管理的金鑰設定
description: 使用 Azure Key Vault 金鑰，設定客戶管理的金鑰（CMK）來加密 Log Analytics 工作區中資料的資訊和步驟。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758815"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure 監視器客戶管理的金鑰設定 

本文提供背景資訊和步驟，為您的 Log Analytics 工作區和 Application Insights 元件設定客戶管理的金鑰（CMK）。 設定之後，任何傳送到您的工作區或元件的資料都會以您的 Azure Key Vault 金鑰進行加密。

我們建議您在設定之前，先審查下面的[限制和條件約束](#limitations-and-constraints)。

## <a name="disclaimers"></a>免責聲明

- Azure 監視器 CMK 是早期存取功能，並已針對註冊的訂用帳戶啟用。

- 本文中所述的 CMK 部署會以生產環境的品質來提供，並支援這種情況，雖然它是早期存取功能。

- CMK 功能是在專用的資料存放區叢集上提供，這是 Azure 資料總管（ADX）叢集，適用于每天傳送1TB 或更多的客戶。 

- CMK 定價模式目前無法使用，本文並未涵蓋此模型。 在行事歷年度的第二季（CY）2020中，需要專用 ADX 叢集的計價模式，並適用于任何現有的 CMK 部署。

- 本文說明 Log Analytics 工作區的 CMK 設定。 本文章也支援 CMK for Application Insights 元件，但附錄中會列出差異。

> [!NOTE]
> Log Analytics 和 Application Insights 使用相同的資料存放區平臺和查詢引擎。
> 我們會透過將 Application Insights 整合到 Log Analytics 中，將這兩個存放區結合在一起，以在 Azure 監視器下建立單一的整合記錄存放區。 這項變更是針對日曆年度2020的第二季計畫。 如果您不需要再為 Application Insights 資料部署 CMK，建議您等待合併完成，因為這類部署將會中斷匯總，而且您必須在遷移至 Log Analytics 工作區之後重新設定 CMK。 每日1TB 的最小值適用于叢集層級，直到匯總在第二季完成時，Application Insights 和 Log Analytics 需要個別的叢集。

## <a name="customer-managed-key-cmk-overview"></a>客戶管理的金鑰（CMK）總覽

待用[加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是組織中的常見隱私權和安全性需求。 您可以讓 Azure 完全管理待用加密，而您有各種選項可嚴密管理加密或加密金鑰。

Azure 監視器的資料存放區可確保在儲存于 Azure 儲存體時，使用 Azure 管理的金鑰進行待用加密的所有資料。 Azure 監視器也會提供使用您自己的金鑰（儲存在[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)中）的資料加密選項，使用系統指派的[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)驗證來存取。 此金鑰可能是[軟體或硬體 HSM 保護](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的。
Azure 監視器使用加密與[Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)運作的方式相同。

Azure 監視器儲存體存取包裝和解除封裝作業 Key Vault 的頻率是介於6到60秒之間。Azure 監視器儲存體一律會在一小時內尊重金鑰許可權的變更。

過去14天內的內嵌資料也會保留在快取記憶體（SSD 支援）中，以進行有效率的查詢引擎作業。 無論 CMK 設定為何，這項資料都會以 Microsoft 金鑰保持加密，但我們正努力在2020的前半年中，使用 CMK 加密 SSD。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure 監視器中的運作方式

Azure 監視器會利用系統指派的受控識別，將存取權授與您的 Azure Key Vault。系統指派的受控識別只能與單一 Azure 資源相關聯。 叢集層級支援 Azure 監視器資料存放區（ADX 叢集）的識別，這表示 CMK 功能是在專用的 ADX 叢集上傳遞。 為了支援多個工作區上的 CMK，新的 Log Analytics 資源（*叢集）會*在您的 Key Vault 與 Log analytics 工作區之間以中繼身分識別連線的形式執行。 此概念符合系統指派的身分識別條件約束，且身分識別會在 ADX 叢集與*Log Analytics 叢集*資源之間進行維護，而所有相關聯工作區的資料會以您的 Key Vault 金鑰來保護。 Underlay ADX 叢集儲存體會\'使用與叢集資源相關聯的受控識別*Cluster* ，透過 Azure Active Directory 來驗證和存取您的 Azure Key Vault。

![CMK 總覽](media/customer-managed-keys/cmk-overview-8bit.png)
1.    客戶的 Key Vault。
2.    客戶的*Log Analytics 叢集*資源具有具有 Key Vault 許可權的受控識別–此身分識別受到資料存放區（ADX 叢集）層級的支援。
3.    Azure 監視器專用 ADX 叢集。
4.    *與叢集資源相關*聯的客戶工作區，以進行 CMK 加密。

## <a name="encryption-keys-management"></a>加密金鑰管理

儲存體資料加密牽涉到3種類型的金鑰：

- **KEK**金鑰加密金鑰（CMK）
- **AEK** -帳戶加密金鑰
- **DEK** -資料加密金鑰

適用的規則如下：

- ADX 儲存體帳戶會針對每個儲存體帳戶產生唯一的加密金鑰，也就是所謂的 AEK。

- AEK 是用來衍生 Dek，這是用來加密寫入磁片的每個資料區塊的索引鍵。

- 當您在 Key Vault 中設定金鑰，*並在叢集資源中*參考它時，Azure 儲存體會將要求傳送至您的 Azure Key Vault，以包裝和解除封裝 AEK，以執行資料加密和解密作業。

- 您的 KEK 永遠不會離開 Key Vault，而且在 HSM 金鑰的情況下，它絕不會離開硬體。

- Azure 儲存體使用與*叢集資源相關聯的受控*識別，透過 Azure Active Directory 來驗證和存取 Azure Key Vault。

## <a name="cmk-provisioning-procedure"></a>CMK 布建程式

如 Application Insights CMK 設定，請遵循步驟3和6的附錄內容。

1. 訂用帳戶允許清單--這是此早期存取功能的必要項
2. 建立 Azure Key Vault 和儲存金鑰
3. *建立叢集資源*
4. Azure 監視器資料存放區（ADX 叢集）布建
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

此資源是用來做為您的 Key Vault 與 Log Analytics 工作區之間的中繼身分識別連線。 在您收到訂用帳戶列入允許清單的確認之後，請在您的工作區所在的區域建立*Log Analytics 叢集*資源。 Application Insights 和 Log Analytics*需要個別的*叢集資源類型。 叢集資源的類型是在建立時定義的 *，其方式*是將*clusterType*屬性設定為*LogAnalytics*或*ApplicationInsights*。 叢集資源類型無法在之後變更。

如 Application Insights CMK 設定，請遵循附錄內容。

建立*叢集資源時*，您必須指定容量保留層級（sku）。 容量保留層級的範圍可以是每日1000到 2000 GB，而您可以在稍後的100步驟中更新它。 如果您每天都需要超過 2000 GB 的容量保留層級，請聯繫您的 Microsoft 連絡人加以啟用。 此屬性不會影響目前的計費--一旦引進專用叢集的計價模式，就會將計費套用到任何現有的 CMK 部署。

**建立**

此 Resource Manager 要求是非同步作業。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
識別會在建立時指派給*叢集資源。*

**回應**

200 OK 和 header。
在此功能的早期存取期間，ADX 叢集會以手動方式布建。 當它需要布建是 ADX 叢集時，您可以透過兩種方式來檢查布建狀態：
1. 複製回應中的 [Azure AsyncOperation URL] 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. *在叢集資源上*傳送 GET 要求，並查看*provisioningState*值。 在布建時*ProvisioningAccount* ，並在完成時*成功*。

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure 監視器資料存放區（ADX 叢集）布建

在此功能的早期存取期間，產品小組會在先前的步驟完成後，手動布建 ADX 叢集。 在此步驟中使用您的 Microsoft 通道，並提供*叢集資源回應*。 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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

> [!Warning]
> 您必須在包含身分*識別*、 *sku*、 *KeyVaultProperties*和*位置**的叢集資源更新*中提供完整主體。 遺失*KeyVaultProperties*詳細資料將會從*叢集資源中*移除金鑰識別碼，並導致[金鑰撤銷](#cmk-kek-revocation)。

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
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>*與叢集*資源的工作區關聯
如 Application Insights CMK 設定，請遵循此步驟的附錄內容。

您必須具有工作*區和叢集*資源的「寫入」許可權，才能執行這項作業，包括下列動作：

- 在工作區中： Microsoft.operationalinsights/工作區/寫入
- 在叢集資源中： Microsoft.operationalinsights/ *Cluster* /write

> [!IMPORTANT]
> 只有在 ADX 叢集布建之後，才應該執行此步驟。 如果您在布建之前建立工作區與內嵌資料的關聯，內嵌資料將會遭到捨棄，且無法復原。

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

您可以藉由停用您的金鑰或刪除 Key Vault 中的叢集資源存取*原則，撤銷*您對資料的存取權。 Azure 監視器儲存體一律會在一小時內遵循金鑰許可權的變更（通常較早），而且儲存體將會變成無法使用。 已卸載與*叢集資源相關*聯之工作區的任何資料內嵌，且查詢將會失敗。 只要*您是叢集資源，* 而且您的工作區不會被刪除，先前內嵌的資料就會在 Azure 監視器儲存體中保持無法存取。 無法存取的資料是由資料保留原則所控管，並會在達到保留期時清除。

儲存體會定期輪詢您的 Key Vault 以嘗試解除包裝加密金鑰，並在存取之後，在30分鐘內進行資料內嵌和查詢繼續。

## <a name="cmk-kek-rotation"></a>CMK （KEK）旋轉

CMK 的輪替需要在 Azure Key Vault 中，以新的金鑰版本明確更新*叢集資源。* 若要使用新的金鑰版本更新 Azure 監視器，請依照「使用金鑰識別碼詳細資料更新*叢集資源」* 步驟中的指示進行。 如果您在 Key Vault 中更新金鑰版本，而不更新*叢集資源中的新*金鑰識別碼詳細資料，Azure 監視器儲存體會繼續使用您先前的金鑰。
您所有的資料都可以在金鑰輪替作業之後存取，包括旋轉之前和之後的資料內嵌，因為所有資料都會由帳戶加密金鑰（AEK）加密，而 AEK 現在會由新的金鑰加密金鑰（KEK）版本加密。

## <a name="limitations-and-constraints"></a>限制和限制

- ADX 叢集層級支援 CMK 功能，且需要專用的 Azure 監視器 ADX 叢集，且每一天或更多的要求必須傳送1TB。

- 每個訂用*帳戶的叢集資源數目*上限為2

- 只有在您確認 ADX 叢集布建完成之後，才應該執行與工作區的*叢集資源關聯*。 在布建完成之前傳送至工作區的資料將會卸載，而且無法復原。

- CMK 加密適用于 CMK 設定後的新內嵌資料。 在 CMK 設定之前所內嵌的資料會以 Microsoft 金鑰保持加密。 您可以順暢地查詢 CMK 設定前後的資料內嵌。

- 在決定特定工作區不需要 CMK 時，您可以將工作區從*叢集資源中*移除關聯性。 在解除關聯作業之後，新的內嵌資料會儲存在共用的 Log Analytics 儲存體中，就像在它與*叢集資源相關*聯之前一樣。 如果您的叢集資源已布建並設定有效的 Key Vault 金鑰，*您就*可以順暢地查詢取消關聯前後的資料內嵌。

- Azure Key Vault 必須設定為可復原。 這些屬性預設不會啟用，而且應該使用 CLI 或 PowerShell 來設定：

  - 必須開啟虛[刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - 應該開啟[清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止強制刪除秘密/保存庫，即使在虛刪除之後也一樣

- Application Insights 和 Log Analytics*需要個別的*叢集資源。 叢集資源的類型是在建立時定義的 *，其方式*是將 "clusterType" 屬性設定為 ' LogAnalytics ' 或 ' ApplicationInsights '。 無法更改*叢集資源類型*。

- 目前不支援將*叢集資源移*到另一個資源群組或訂用帳戶。

- 您的 Azure Key Vault *、叢集*資源和相關聯的工作區必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以位於不同的訂用帳戶中。

- 如果叢集資源與*另一個*叢集資源相關*聯，則*其工作區關聯將會失敗

## <a name="troubleshooting-and-management"></a>疑難排解和管理

- Key Vault 可用性
    - 在正常作業中，儲存體快取 AEK 的時間很短，並會回到 Key Vault 以定期解除包裝。
    
    - 暫時性連線錯誤-儲存體處理暫時性錯誤（超時、連線失敗、DNS 問題）的方式，是允許金鑰長時間保持在快取中，而且這會克服可用性的任何小型短暫中斷。 查詢和內嵌功能會繼續進行，而不會中斷。
    
    - 即時網站--30 分鐘內無法使用，將導致儲存體帳戶變成無法使用。 查詢功能無法使用，而且內嵌的資料會以 Microsoft 金鑰快取數小時，以避免資料遺失。 當還原 Key Vault 的存取權時，查詢就會變成可用，而且暫時快取的資料會內嵌至資料存放區，並使用 CMK 加密。

- 如果您建立叢集*資源並*立即指定 KeyVaultProperties，此作業可能會失敗，因為在系統識別指派給*叢集資源之前*，無法定義存取原則。

- 如果您使用 KeyVaultProperties 來*更新現有的*叢集資源，但 Key Vault 中遺漏了「Get」金鑰存取原則，則作業將會失敗。

- 如果您嘗試刪除與工作區相關*聯的叢集資源，* 刪除作業將會失敗。

- 如果您在建立叢集資源時收到衝突，可能是因為您在過去14天內*刪除了**叢集資源，* 而且它是在虛刪除期間。 叢集*資源名稱*會在虛刪除期間保持保留，而且您無法使用該名稱建立新的叢集。 當刪除*叢集資源時，此名稱*會在虛刪除期間釋放。

- 取得資源群組*的所有叢集*資源：

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
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- 取得訂用帳戶*的所有叢集*資源：

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **回應**
    
  與「資源群組的叢集*資源」* 相同的回應，但在訂用帳戶範圍中。

- 更新*叢集資源中*的*容量保留*--當相關聯的工作區的資料量變更，而您想要更新計費考慮的容量保留層級時，請遵循[更新叢集資源*Cluster* ](#update-cluster-resource-with-key-identifier-details)並提供新的容量值。 容量保留層級的範圍可以是每天1000到 2000 GB，而在100的步驟中。 針對高於每天 2000 GB 的層級，請聯繫您的 Microsoft 連絡人加以啟用。

- 刪除您的叢集資源--執行虛刪除作業可在14天內*復原您的**叢集資源，* 包括其資料（不論是意外或故意刪除）。 叢集*資源名稱*會在虛刪除期間保持保留，而且您無法使用該名稱建立新的叢集。 在虛刪除期間，*叢集資源名稱*會釋出，您的叢集*資源和*資料會永久刪除且無法復原。 任何相關聯的工作區都會在刪除作業時 *，與叢集資源解除*關聯。 新的內嵌資料會儲存在共用的 Log Analytics 儲存體中，並使用 Microsoft 金鑰進行加密。 工作區取消關聯作業是非同步。

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定

- 復原您的叢集資源和資料--在過去14天內*刪除的**叢集資源處於*虛刪除狀態，而且可以復原。 這項作業目前是由產品小組手動執行。 使用您的 Microsoft 通道來取得復原要求。

## <a name="appendix"></a>附錄

同時也支援 Application Insights 客戶管理的金鑰（CMK），但您應該考慮下列變更，以協助您規劃應用程式深入解析元件的 CMK 部署。

Log Analytics 和 Application Insights 使用相同的資料存放區平臺和查詢引擎。 我們會透過將 Application Insights 整合到 Log Analytics 中，將這兩個存放區結合在一起，以在第二季的 Azure 監視器下提供單一的整合記錄儲存
2020. 這項變更會將您的應用程式深入解析資料帶入 Log Analytics 工作區，並在您工作區上的 CMK 設定時，讓查詢、深入解析和其他改善也適用于您的 Application Insights 資料。

> [!NOTE]
> 如果您不需要在整合之前部署應用程式深入解析資料的 CMK，建議您等待 Application Insights CMK，因為這類部署將會中斷，而您必須在遷移至 Log Analytics 工作區之後重新設定 CMK。 每日1TB 的最小值適用于叢集層級，直到匯總在第二季完成時，Application Insights 和 Log Analytics 需要個別的叢集。

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK 設定

Application Insights CMK 的設定與本文中所述的程式完全相同，包括條件約束和疑難排解，但下列步驟除外：

- 建立叢集*資源*
- *建立元件與叢集資源的*關聯

設定 Application Insights 的 CMK 時，請使用下列步驟，而不是上述所列的步驟。

### <a name="create-a-cluster-resource"></a>建立叢集*資源*

此資源是用來做為 Key Vault 與您的元件之間的中繼身分識別連接。 在您收到訂用帳戶列入允許清單的確認之後，請在您的元件所在的區域建立*Log Analytics 叢集*資源。 叢集資源的類型是在建立時定義的 *，其方式*是將*clusterType*屬性設定為*LogAnalytics*或*ApplicationInsights*。 它應該是針對 Application Insights CMK 而*ApplicationInsights*的。 設定之後，就無法改變*clusterType*設定。

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**回應**

200 OK 和 header。
在此功能的早期存取期間，ADX 叢集會以手動方式布建。 當它需要布建是 ADX 叢集時，您可以透過兩種方式來檢查布建狀態：
1. 複製回應中的 [Azure AsyncOperation URL] 值，並遵循[非同步作業狀態檢查](#asynchronous-operations-and-status-check)。
2. *在叢集資源上*傳送 GET 要求，並查看*provisioningState*值。 在布建時*ProvisioningAccount* ，並在完成時*成功*。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>使用元件建立元件與*叢集資源的*關聯[-建立或更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)API

您必須在*元件和叢集*資源上具有「寫入」許可權，才能執行這項作業，包括下列動作：

- 在元件中： Microsoft Insights/component/write
- 在叢集資源中： Microsoft.operationalinsights/ *Cluster* /write

> [!IMPORTANT]
> 只有在 ADX 叢集布建之後，才應該執行此步驟。 如果您在布建之前建立元件與內嵌資料的關聯，內嵌資料將會被捨棄，而且無法復原。
> 若要確認已布建 ADX 叢集，請*執行叢集*資源取得 REST API，並檢查*ProvisioningState*值是否*成功*。

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> 複製並保留回應，因為在接下來的步驟中將會用到它。

**關聯元件**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" 是在上一個步驟的回應中提供的 "clusterId" 值。
「種類」範例是「web」。

**回應**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" 是與此元件相關聯的*叢集資源識別碼*。

關聯之後，傳送至您的元件的資料會以您的受控金鑰進行加密。
