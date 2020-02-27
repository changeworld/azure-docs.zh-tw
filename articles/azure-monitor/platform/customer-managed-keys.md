---
title: Azure 監視器客戶管理的金鑰設定
description: 使用 Azure Key Vault 金鑰，設定客戶管理的金鑰（CMK）來加密 Log Analytics 工作區中資料的資訊和步驟。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/24/2020
ms.openlocfilehash: 2ea77be0a7aabefaf8f6ed9a5bd841ea1fdda263
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620317"
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
> 我們會透過將 Application Insights 整合到 Log Analytics 中，將這兩個存放區結合在一起，以在 Azure 監視器下建立單一的整合記錄存放區。 這項變更是針對日曆年度2020的第二季計畫。 如果您不需要再為 Application Insights 資料部署 CMK，建議您等待合併完成，因為這類部署將會因為匯總而中斷，而且您必須在遷移至記錄之後重新設定 CMKAnalytics 工作區。 每日1TB 的最小值適用于叢集層級，直到匯總在第二季完成時，Application Insights 和 Log Analytics 需要個別的叢集。

## <a name="customer-managed-key-cmk-overview"></a>客戶管理的金鑰（CMK）總覽

待用[加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是組織中的常見隱私權和安全性需求。 您可以讓 Azure 完全管理待用加密，而您有各種選項可嚴密管理加密或加密金鑰。

Azure 監視器的資料存放區可確保在儲存于 Azure 儲存體時，使用 Azure 管理的金鑰進行待用加密的所有資料。 Azure 監視器也會提供使用您自己的金鑰（儲存在[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)中）的資料加密選項，這是使用系統指派的[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)驗證來存取。 此金鑰可能是[軟體或硬體 HSM 保護](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的。
Azure 監視器使用加密與[Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)運作的方式相同。

Azure 監視器儲存體存取包裝和解除封裝作業 Key Vault 的頻率是介於6到60秒之間。 Azure 監視器儲存體一律會在一小時內尊重金鑰許可權的變更。

過去14天內的內嵌資料也會保留在快取記憶體（SSD 支援）中，以進行有效率的查詢引擎作業。 無論 CMK 設定為何，這項資料仍會使用 Microsoft 金鑰進行加密，但我們正努力在2020年初使用 CMK 加密。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure 監視器中的運作方式

Azure 監視器會利用系統指派的受控識別，將存取權授與您的 Azure Key Vault。 系統指派的受控識別只能與單一 Azure 資源相關聯。 叢集層級支援 Azure 監視器資料存放區（ADX 叢集）的識別，這表示 CMK 功能是在專用的 ADX 叢集上傳遞。 為了支援多個工作區上的 CMK，新的 Log Analytics 資源（*叢集）會*在您的 Key Vault 與 Log analytics 工作區之間以中繼身分識別連線的形式執行。 此概念符合系統指派的身分識別條件約束，且身分識別會在 ADX 叢集與*Log Analytics 叢集*資源之間進行維護，而所有相關聯工作區的資料會以您的 Key Vault 金鑰來保護。 Underlay ADX 叢集儲存體會使用與*叢集資源相關聯\'的受控*識別，透過 Azure Active Directory 來驗證和存取您的 Azure Key Vault。

![CMK 總覽](media/customer-managed-keys/cmk-overview.png)
1.  客戶的 Key Vault。
2.  客戶的 Log Analytics 叢集資源具有具有 Key Vault 許可權的受控識別–此身分識別受到資料存放區（ADX 叢集）層級的支援。
3.  Azure 監視器專用 ADX 叢集。
4.  與叢集資源相關聯的客戶工作區，以進行 CMK 加密。

## <a name="encryption-keys-management"></a>加密金鑰管理

儲存體資料加密牽涉到3種類型的金鑰：

- Key Vault （CMK）中的**KEK**金鑰加密金鑰
- **AEK** -帳戶加密金鑰
- **DEK** -資料加密金鑰

適用的規則如下：

- ADX 儲存體帳戶會為每個儲存體帳戶產生唯一的加密金鑰，也就是所謂的 AEK。

- AEK 是用來衍生 Dek，這是用來加密寫入磁片的每個資料區塊的索引鍵。

- 當您在 Key Vault 中設定金鑰，並在*叢集資源中*參考它時，Azure 儲存體會在 Azure Key Vault 中包裝 AEK 與您的 KEK。

- 您的 KEK 永遠不會離開 Key Vault，而且在 HSM 金鑰的情況下，它絕不會離開硬體。

- Azure 儲存體使用與*叢集資源相關聯的受控*識別，透過 Azure Active Directory 來驗證和存取 Azure Key Vault。

- 對於讀取/寫入作業，Azure 儲存體會將要求傳送給 Azure Key Vault，以包裝和解除封裝 AEK，以執行加密和解密作業。

## <a name="cmk-provisioning-procedure"></a>CMK 布建程式

如 Application Insights CMK 設定，請遵循步驟3和6的附錄內容。

1. 訂用帳戶允許清單--這是此早期存取功能的必要項
2. 建立 Azure Key Vault 和儲存金鑰
3. 建立叢集*資源*
4. Azure 監視器資料存放區（ADX 叢集）布建
5. 授與許可權給您的 Key Vault
6. Log Analytics 工作區關聯

UI 目前不支援此程式，而且布建程式是透過 REST API 來執行。

> [!IMPORTANT]
> 任何 API 要求都必須在要求標頭中包含持有人授權權杖。

例如：

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

其中*eyJ0eXAiO ...* 表示完整授權 token。 

您可以使用下列其中一種方法來取得權杖：

1. 使用[應用程式註冊](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。

2. 在 Azure 入口網站
    1. 流覽至「開發人員工具」（F12）中的 Azure 入口網站
    1. 在「批次？ api 版本」實例中，尋找「要求標頭」底下的授權字串。 它看起來像：「authorization：持有人 \<權杖\>」。 
    1. 根據下列範例，將它複製並新增至您的 API 呼叫。

3. 流覽至 Azure REST 檔網站。 在任何 API 上按「試試看」，並複製持有人權杖。

### <a name="subscription-whitelisting"></a>訂用帳戶允許清單

CMK 功能是早期的存取功能。 您*打算建立叢集*資源的訂用帳戶必須事先由 Azure 產品群組列入允許清單。 在 Microsoft 中使用您的連絡人，以提供您的訂用帳戶識別碼。

> [!IMPORTANT]
> CMK 功能是區域。 您的 Azure Key Vault、儲存體帳戶 *、叢集*資源和相關聯的 Log Analytics 工作區必須位於相同的區域中，但它們可以位於不同的訂用帳戶中。

### <a name="storing-encryption-key-kek"></a>儲存加密金鑰（KEK）

建立 Azure Key Vault 資源，然後產生或匯入用於資料加密的金鑰。

Azure Key Vault 必須設定為可復原，以保護您的金鑰和存取您的 Azure 監視器資料。

這些設定可透過 CLI 和 PowerShell 取得：
- 必須開啟虛[刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- 應該開啟[清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止強制刪除秘密/保存庫，即使在虛刪除之後也一樣

### <a name="create-cluster-resource"></a>建立*叢集*資源

此資源是用來做為 Key Vault 與您的工作區之間的中繼身分識別連線。 在您收到訂用帳戶列入允許清單的確認之後，請在您的工作區所在的區域建立*Log Analytics 叢集*資源。 Application Insights 和 Log Analytics 需要個別的叢集資源。 叢集資源的類型是在建立時定義的 *，其方式*是將 "clusterType" 屬性設定為 ' LogAnalytics ' 或 ' ApplicationInsights '。 無法更改叢集資源類型。

如 Application Insights CMK 設定，請遵循此步驟的附錄內容。

**建立**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
識別會在建立時指派給*叢集資源。*
"clusterType" 值為 "ApplicationInsights"，適用于 Application Insights CMK。

**回應**

202已接受。 這是非同步作業的標準 Resource Manager 回應。

如果您因為任何原因而要刪除*叢集資源，* 例如，以不同的名稱或 clusterType 建立它，請使用此 REST API：

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure 監視器資料存放區（ADX 叢集）布建

在此功能的早期存取期間，產品小組會在先前的步驟完成後，手動布建 ADX 叢集。 使用您的 Microsoft 通道來提供*叢集資源詳細*資料。 從*叢集資源 GET* REST API 複製 JSON 回應：

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**回應**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-Id"
    },
  "properties": {
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

「principalId」是叢集資源的受控識別*服務所產生*的 GUID。

> [!IMPORTANT]
> 複製並保留「叢集識別碼」值，因為在接下來的步驟中將會用到它。


### <a name="grant-key-vault-permissions"></a>授與 Key Vault 許可權

> [!IMPORTANT]
> 當您透過 Microsoft 通道（已滿足 Azure 監視器資料存放區（ADX cluster）布建），從產品群組收到確認之後，應該會執行此步驟。 在此布建之前更新 Key Vault 存取原則可能會失敗。

使用*授與叢集資源許可權*的新存取原則，更新您的 Key Vault。 Underlaying Azure 監視器儲存體會使用這些許可權來進行資料加密。
在 Azure 入口網站中開啟您的 Key Vault，然後按一下 [存取原則]，再按 [+ 新增存取原則]，使用下列設定建立新的原則：

- 金鑰許可權：選取 [取得]、[將金鑰換行] 和 [解除包裝金鑰] 許可權。
- 選取 [主體]：輸入在上一個步驟的回應中所傳回的叢集識別碼值。

![授與 Key Vault 許可權](media/customer-managed-keys/grant-key-vault-permissions.png)

需要*取得*許可權，才能確認您的 Key Vault 已設定為可復原，以保護您的金鑰和 Azure 監視器資料的存取權。

### <a name="update-cluster-resource-with-key-identifier-details"></a>以金鑰識別碼詳細資料更新叢集資源

此步驟適用于您 Key Vault 中的未來金鑰版本更新。 以 Key Vault*金鑰識別碼*詳細資料更新*叢集資源，* 以允許 Azure 監視器儲存體使用新的金鑰版本。 在 Azure Key Vault 中選取您金鑰的目前版本，以取得金鑰識別碼的詳細資料。

![授與 Key Vault 許可權](media/customer-managed-keys/key-identifier-8bit.png)

以金鑰識別碼詳細資料更新*叢集資源 KeyVaultProperties* 。

**更新**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>",
   "identity": { 
     "type": "systemAssigned" 
     }
}
```
"KeyVaultProperties" 包含 Key Vault 的金鑰識別碼詳細資料。

**回應**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
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

> [!NOTE]
> 只有在您透過 Microsoft 通道（已滿足**Azure 監視器資料存放區（ADX cluster）布建）** 收到來自產品群組的確認之後，**才**應該執行此步驟。 如果您在布建此布建之前，將工作區與內嵌資料產生關聯，資料將會被**捨棄，而且**無法復原。

如 Application Insights CMK 設定，請遵循此步驟的附錄內容。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
*ClusterDefinitionId*是上一個步驟回應中所提供的*clusterId*值。

**回應**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

關聯之後，傳送至您工作區的資料會以您的受控金鑰進行加密。

### <a name="workspace-association-verification"></a>工作區關聯驗證
您可以藉由查看工作[區–取得](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)回應，確認工作區是否與*叢集*資源相關聯。 相關聯的工作區會有一個具有*叢集資源識別碼*的 ' clusterResourceId ' 屬性。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
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
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
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

Azure 監視器儲存體一律會在一小時內遵循金鑰許可權的變更（通常較早），而且儲存體將會變成無法使用。 已卸載與*叢集資源相關*聯之工作區的任何資料內嵌，且查詢將會失敗。 先前內嵌的資料在您的金鑰被撤銷時，Azure 監視器儲存體中仍然無法存取，且不會刪除您的工作區。 無法存取的資料是由資料保留原則所控管，並會在達到保留期時清除。

儲存體會定期輪詢您的 Key Vault 以嘗試解除包裝加密金鑰，並在存取之後，在30分鐘內進行資料內嵌和查詢繼續。

## <a name="cmk-kek-rotation"></a>CMK （KEK）旋轉

CMK 的輪替需要使用新的 Azure Key Vault 金鑰版本來明確更新*叢集資源。* 若要使用新的金鑰版本更新 Azure 監視器，請依照「使用*金鑰識別碼*詳細資料更新*叢集資源」* 步驟中的指示進行。

如果您在 Key Vault 中更新金鑰，而不更新*叢集資源 * 中的新* *金鑰識別碼*詳細資料，Azure 監視器儲存體會繼續使用您先前的金鑰。

## <a name="limitations-and-constraints"></a>限制和限制

- ADX 叢集層級支援 CMK 功能，且需要專用的 Azure 監視器 ADX 叢集

- 每個訂用*帳戶的叢集資源數目*上限限制為5

- 只有在您從完成 ADX 叢集布建的產品群組收到確認之後，才應該執行與工作區的*叢集資源關聯*。 在此布建之前傳送的資料將會卸載，而且無法復原。

- CMK 加密適用于 CMK 設定後的新內嵌資料。 在 CMK 設定之前內嵌的資料會以 Microsoft 金鑰保持加密。 您可以在設定順暢地前後查詢資料。

- 一旦將工作區與叢集*資源相關*聯，就無法將其與叢集資源解除關聯，因為資料是以*您的金鑰*加密，而且在 Azure Key Vault 中不需要您的 KEK 即可存取。

- Azure Key Vault 必須設定為可復原。 這些屬性預設不會啟用，而且應該使用 CLI 和 PowerShell 來設定：

  - 必須開啟虛[刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - 應該開啟[清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止強制刪除秘密/保存庫，即使在虛刪除之後也一樣

- Application Insights 和 Log Analytics*需要個別的*叢集資源。 叢集資源的類型是在建立時定義的 *，其方式*是將 "clusterType" 屬性設定為 ' LogAnalytics ' 或 ' ApplicationInsights '。 無法更改*叢集資源類型*。

- 目前不支援將*叢集資源移*到另一個資源群組或訂用帳戶。

- 您的 Azure Key Vault *、叢集*資源和相關聯的工作區必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以位於不同的訂用帳戶中。

- 如果叢集資源與*另一個*叢集資源相關*聯，則*其工作區關聯將會失敗

## <a name="troubleshooting-and-management"></a>疑難排解和管理

- Key Vault 可用性
    - 在正常作業中，儲存體會在短時間內快取 AEK，以進行解除包裝的 Key Vault。
    
    - 暫時性連接錯誤。 儲存體會藉由允許金鑰長時間保持在快取中，以處理暫時性錯誤（超時、連線失敗、DNS 問題），而且這會克服可用性中的任何小型短暫中斷。 查詢和內嵌功能會繼續進行，而不會中斷。
    
    - 即時網站無法使用大約30分鐘的時間，將導致儲存體帳戶變成無法使用。 查詢功能無法使用，而且內嵌的資料會以 Microsoft 金鑰快取數小時，以避免資料遺失。 當還原 Key Vault 的存取權時，查詢就會變成可用，而且暫時快取的資料會內嵌至資料存放區，並使用 CMK 加密。

- 如果您建立叢集*資源並*立即指定 KeyVaultProperties，此作業可能會失敗，因為在系統識別指派給*叢集資源之前*，無法定義存取原則。

- 如果您使用 KeyVaultProperties 來*更新現有的*叢集資源，但 Key Vault 中遺漏了「Get」金鑰存取原則，則作業將會失敗。

- 如果您嘗試刪除與工作區相關*聯的叢集資源，* 刪除作業將會失敗。

- 使用此 API 來取得資源群組*的所有叢集*資源：

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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

- 使用此 API 呼叫來取得訂用帳戶*的所有叢集*資源：

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **回應**
    
  與「資源群組的叢集*資源」* 相同的回應，但在訂用帳戶範圍中。
    
- 使用此 API 呼叫來刪除叢集*資源-* -您必須先刪除所有相關聯的工作區，才能刪除叢集資源：

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定


## <a name="appendix"></a>附錄

同時也支援 Application Insights 客戶管理的金鑰（CMK），但您應該考慮下列變更，以協助您規劃應用程式深入解析元件的 CMK 部署。

Log Analytics 和 Application Insights 使用相同的資料存放區平臺和查詢引擎。 我們會透過將 Application Insights 整合到 Log Analytics 中，將這兩個存放區結合在一起，以在第二季的 Azure 監視器下提供單一的整合記錄儲存
2020. 這項變更會將您的應用程式深入解析資料帶入 Log Analytics 工作區，並在您工作區上的 CMK 設定時，讓查詢、深入解析和其他改善也適用于您的 Application Insights 資料。

> [!NOTE]
> 如果您不需要在整合之前為您的應用程式深入解析資料部署 CMK，建議您等待 Application Insights CMK，因為這類部署將會中斷整合，而您必須在遷移至記錄之後重新設定 CMK。Analytics 工作區。 每日1TB 的最小值適用于叢集層級，直到匯總在第二季完成時，Application Insights 和 Log Analytics 需要個別的叢集。

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK 設定

Application Insights CMK 的設定與本文中所述的程式完全相同，包括條件約束和疑難排解，但下列步驟除外：

- 建立叢集*資源*

- *建立元件與叢集資源的*關聯

設定 Application Insights 的 CMK 時，請使用下列步驟，而不是上述所列的步驟。

### <a name="create-a-cluster-resource"></a>建立叢集*資源*

此資源是用來做為 Key Vault 與您的元件之間的中繼身分識別連接。 在您收到訂用帳戶列入允許清單的確認之後，請在您的元件所在的區域建立*Log Analytics 叢集*資源。 叢集資源的類型是在建立時定義的 *，其方式*是將*clusterType*屬性設定為*LogAnalytics*或*ApplicationInsights*。 它應該是針對 Application Insights CMK 而*ApplicationInsights*的。 設定之後，就無法改變*clusterType*設定。

**建立**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**回應**

識別會在建立時指派給*叢集資源。*

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
「principalId」是受控識別服務所產生的 GUID。

> [!IMPORTANT]
> 複製並保留「叢集識別碼」值，因為在接下來的步驟中將會用到它。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>使用元件建立元件與*叢集資源的*關聯[-建立或更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)API

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
