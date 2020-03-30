---
title: Azure 監視器客戶管理的關鍵配置
description: 配置客戶管理金鑰 （CMK） 以使用 Azure 金鑰保存庫金鑰加密日誌分析工作區中的資料的資訊和步驟。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 563a50d4589a83f710caa8ff2d2d95065909fc5f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384172"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure 監視器客戶管理的關鍵配置 

本文提供了後臺資訊和步驟，用於為日誌分析工作區和應用程式見解元件配置客戶管理金鑰 （CMK）。 配置完成後，發送到工作區或元件的任何資料都使用 Azure 金鑰保存庫金鑰進行加密。

我們建議您在配置之前查看下面的[限制和約束](#limitations-and-constraints)。

## <a name="disclaimers"></a>免責聲明

- Azure 監視器 CMK 是一種早期訪問功能，已為註冊訂閱啟用。

- 本文中描述的 CMK 部署以生產品質提供，並且支援此類部署，儘管它是早期訪問功能。

- CMK 功能在專用資料存儲群集上提供，該群集是 Azure 資料資源管理器 （ADX） 群集，適用于每天發送 1TB 或更多的客戶。 

- CMK 定價模型當前不可用，本文未介紹此版本。 專用 ADX 群集的定價模型預計將在 2020 日曆年 （CY） 2020 年第二季度使用，並將應用於任何現有的 CMK 部署。

- 本文介紹了日誌分析工作區的 CMK 配置。 在附錄中列出了差異時，還支援使用本文支援 CMK 應用程式見解元件。

> [!NOTE]
> 日誌分析和應用程式見解使用相同的資料存儲平臺和查詢引擎。
> 我們通過將應用程式見解集成到日誌分析中來將這兩個存儲整合在一起，以創建 Azure 監視器下的單一統一日誌存儲。 此更改計畫于 2020 日曆年第二季度進行。 如果到那時不必為應用程式見解資料部署 CMK，我們建議您等待合併的完成，因為此類部署將因合併而中斷，並且在遷移到 Log 後必須重新配置 CMK分析工作區。 每天 1TB 的最小值適用于群集級別，直到第二季度合併完成，應用程式見解和日誌分析需要單獨的群集。

## <a name="customer-managed-key-cmk-overview"></a>客戶管理金鑰 （CMK） 概述

[靜態加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是組織中常見的隱私和安全要求。 您可以讓 Azure 完全管理靜態加密，同時您可以有各種選項來密切管理加密或加密金鑰。

Azure 監視器資料存儲可確保在 Azure 存儲中存儲時使用 Azure 管理的金鑰組靜態加密的所有資料。 Azure 監視器還提供一個選項，用於使用存儲在[Azure 金鑰保存庫中](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的金鑰進行資料加密，該金鑰使用系統分配的[託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)身份驗證進行訪問。 此金鑰可以是[軟體或硬體-HSM保護](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。
Azure 監視器使用加密與[Azure 存儲加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)操作方式相同。

Azure 監視器存儲訪問金鑰保存庫進行換行和解包操作的頻率為 6 到 60 秒。Azure 監視器存儲始終在一小時內尊重金鑰許可權的更改。

過去 14 天內的引入資料也保存在熱緩存（SSD 支援）中，以便高效執行查詢引擎。 無論 CMK 配置如何，此資料仍使用 Microsoft 金鑰進行加密，但我們正在努力在 2020 年初使用 CMK 加密 SSD。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure 監視器中的工作方式

Azure 監視器利用系統分配的託管標識來授予對 Azure 金鑰保存庫的存取權限。系統分配的託管標識只能與單個 Azure 資源關聯。 群集級別支援 Azure 監視器資料存儲 （ADX 群集）的標識，這要求 CMK 功能在專用 ADX 群集上傳遞。 為了在多個工作區上支援 CMK，新的日誌分析資源 （*群集*） 充當金鑰保存庫和日誌分析工作區之間的中間標識連接。 此概念符合系統分配的標識約束，並且標識在 ADX 群集和日誌分析*群集*資源之間保留，而所有關聯的工作區的資料都使用金鑰保存庫金鑰進行保護。 底層 ADX 群集存儲使用與*群集*資源\'關聯的託管標識通過 Azure 活動目錄對 Azure 金鑰保存庫進行身份驗證和訪問。

![CMK 概述](media/customer-managed-keys/cmk-overview.png)
1.  客戶金鑰庫。
2.  具有對金鑰保存庫許可權的託管標識的客戶日誌分析*群集*資源 - 在資料存儲 （ADX 群集） 級別支援該標識。
3.  Azure 監視器專用 ADX 群集。
4.  與 CMK 加密群集資源關聯的客戶工作區。

## <a name="encryption-keys-management"></a>加密金鑰管理

存儲資料加密涉及 3 種類型的金鑰：

- **KEK** - 金鑰保存庫 （CMK） 中的金鑰加密金鑰
- **AEK** - 帳戶加密金鑰
- **DEK** - 資料加密金鑰

適用的規則如下：

- ADX 存儲帳戶為每個存儲帳戶（稱為 AEK）生成唯一的加密金鑰。

- AEK 用於派生 DEK，SDK 是用於加密寫入磁片的每個資料塊的金鑰。

- 在金鑰保存庫中配置金鑰並在*群集*資源中引用金鑰時，Azure 存儲會向 Azure 金鑰保存庫發送請求，以包裝和解包 AEK 以執行資料加密和解密操作。

- 您的 KEK 永遠不會離開金鑰保存庫，如果是 HSM 金鑰，它永遠不會離開硬體。

- Azure 存儲使用與*群集*資源關聯的託管標識通過 Azure 活動目錄對 Azure 金鑰保存庫進行身份驗證和訪問。

## <a name="cmk-provisioning-procedure"></a>CMK 預配過程

對於應用見解 CMK 配置，請按照附錄內容執行步驟 3 和 6。

1. 訂閱白名單 -- 此早期訪問功能是必需的
2. 創建 Azure 金鑰保存庫並存儲金鑰
3. 創建*群集*資源
4. Azure 監視器資料存儲 （ADX 群集） 預配
5. 向金鑰保存庫授予許可權
6. 關聯日誌分析工作區

UI 當前不支援該過程，並且通過 REST API 執行預配過程。

> [!IMPORTANT]
> 任何 API 請求都必須在請求標頭中包含承載授權權杖。

例如：

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

*其中 eyJ0eXAiO....* 

可以使用以下方法之一獲取權杖：

1. 使用[應用註冊](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。

2. 在 Azure 入口網站
    1. 在"開發人員工具 （F12） 中導航到 Azure 門戶
    1. 在"批次處理 api 版本"實例之一的"請求標頭"下查找授權字串。 它看起來像："授權：承載\<權杖"。\> 
    1. 根據以下示例複製並將其添加到 API 呼叫中。

3. 導航到 Azure REST 文檔網站。 按任何 API 上的"試用"並複製承載權杖。

### <a name="subscription-whitelisting"></a>訂閱白名單

CMK 功能是一種早期訪問功能。 計畫創建*群集*資源的訂閱必須事先由 Azure 產品組列入白名單。 使用 Microsoft 的連絡人提供訂閱 ID。

> [!IMPORTANT]
> CMK 功能是區域性的。 Azure 金鑰保存庫、*群集*資源和關聯的日誌分析工作區必須位於同一區域中，但它們可以位於不同的訂閱中。

### <a name="storing-encryption-key-kek"></a>存儲加密金鑰 （KEK）

創建或使用已必須生成的 Azure 金鑰保存庫，或導入用於資料加密的金鑰。 Azure 金鑰保存庫必須配置為可恢復，以保護金鑰和對 Azure 監視器中資料的訪問。 您可以在金鑰保存庫中的屬性下驗證此配置，應啟用*虛刪除*和*清除保護*。

這些設置可通過 CLI 和 PowerShell 獲得：
- [虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)防護裝置，防止強制刪除機密/保存庫，即使在虛刪除後

### <a name="create-cluster-resource"></a>創建*群集*資源

此資源用作金鑰保存庫和工作區之間的中間標識連接。 收到訂閱已白名單的確認後，在工作區所在的區域創建日誌分析*群集*資源。 應用程式見解和日誌分析需要單獨的*群集*資源類型。 *群集*資源的類型通過在創建時通過將"群集類型"屬性設置為"LogAnalytics"或"應用程式見解"來定義。 群集資源類型後無法更改。

有關應用見解 CMK 配置，請遵循附錄內容。

創建*群集*資源時，必須指定*群集*資源的容量預留級別 （sKU）。 容量預留級別可以在 1000 到 2000 之間，稍後可以以 100 步的步驟更新它。 如果您需要容量預留級別高於 2000，請聯繫 Microsoft 連絡人以啟用它。 此屬性當前不會影響計費 - 一旦引入了專用群集的定價模型，計費將應用於任何現有的 CMK 部署。

**建立**

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
標識在創建時分配給*群集*資源。

**回應**

202 已接受。 這是非同步作業的標準資源管理器回應。

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure 監視器資料存儲 （ADX 群集） 預配

在功能的早期訪問期間，一旦完成前面的步驟，產品團隊會手動預配 ADX 群集。 在提供*群集*資源回應的同時，使用 Microsoft 通道進行預配。 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 複製並保存回應，因為在後面的步驟中需要這些詳細資訊

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

>[!Important]
> 完成不足 ADX 群集的預配需要幾分鐘時間。 *預配狀態*值指示其狀態，在預配時預配*帳戶*，預配完成後為"成功"。
> "主體 Id" GUID 由*群集*資源的託管標識服務生成。

### <a name="grant-key-vault-permissions"></a>授予金鑰保存庫許可權

使用向*群集*資源授予許可權的新訪問策略更新金鑰保存庫。 這些許可權由下基礎 Azure 監視器存儲用於資料加密。 在 Azure 門戶中打開金鑰保存庫，然後按一下"訪問策略"然後按一下"+ 添加訪問策略"以創建具有以下設置的策略：

- 金鑰許可權：選擇"獲取"，"包裝鍵"和"取消包裝金鑰"許可權。
- 選擇主體：輸入在上一步中回應中返回的主體 ID 值。

![授予金鑰保存庫許可權](media/customer-managed-keys/grant-key-vault-permissions.png)

需要*Get*許可權來驗證金鑰保存庫是否配置為可恢復，以保護金鑰和對 Azure 監視器資料的訪問。

### <a name="update-cluster-resource-with-key-identifier-details"></a>使用金鑰識別碼詳細資訊更新群集資源

此步驟適用于金鑰保存庫中的初始和將來金鑰版本更新。 它通知 Azure 監視器存儲有關新的金鑰版本。

要使用金鑰保存庫*金鑰識別碼*詳細資訊更新*群集*資源，請在 Azure 金鑰保存庫中選擇金鑰的當前版本，以獲取有關金鑰識別碼詳細資訊。

![授予金鑰保存庫許可權](media/customer-managed-keys/key-identifier-8bit.png)

使用金鑰識別碼詳細資訊更新*群集*資源 KeyVault 屬性。

**更新**

>[!Warning]
> 您必須在*群集*資源更新中提供一個完整的正文，其中包括*標識**、sKU、KeyVault**屬性*和*位置*。 缺少*KeyVault屬性*詳細資訊將從*群集*資源中刪除金鑰識別碼，並導致[金鑰吊銷](#cmk-kek-revocation)。

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
"KeyVault 屬性"包含金鑰保存庫金鑰識別碼詳細資訊。

**回應**

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

### <a name="workspace-association-to-cluster-resource"></a>工作區關聯到*群集*資源

對於應用見解 CMK 配置，請按照此步驟的附錄內容操作。

> [!IMPORTANT]
> 此步驟應僅在 ADX 群集預配後執行。 如果在預配之前關聯工作區和引入資料，則引入的資料將被刪除，並且不可恢復。
> 要驗證 ADX 群集是否預配，請執行*群集*資源獲取 REST API 並檢查*預配狀態*值是否*成功*。

執行此操作（包括以下操作）需要對工作區和*群集*資源具有"寫入"許可權：

- 在工作區中：微軟.操作見解/工作空間/寫入
- 在*群集*資源中：微軟.運營見解/集群/寫入

**關聯工作區**
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**回應**

```json
{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

工作區關聯後，引入到工作區的資料將隨託管金鑰加密存儲。

### <a name="workspace-association-verification"></a>工作區關聯驗證
您可以通過查看[工作區和獲取](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)回應來驗證工作區是否與*Custer*資源相關聯。 關聯的工作區將具有具有*群集*資源識別碼 的"群集資源 Id"屬性。

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

## <a name="cmk-kek-revocation"></a>CMK （KEK） 吊銷

您可以通過禁用金鑰或刪除金鑰保存庫中的*群集*資源訪問策略來撤銷對資料的訪問。 Azure 監視器存儲始終在一小時內尊重金鑰許可權的更改，通常更快，並且存儲將不可用。 引入到與*群集*資源關聯的工作區的任何資料都將被刪除，查詢將失敗。 以前引入的資料在 Azure 監視器存儲中仍然無法訪問，只要您的金鑰被吊銷，並且您的工作區不會被刪除。 無法訪問的資料受資料保留原則的約束，並在達到保留時清除。

存儲將定期輪詢金鑰保存庫，以嘗試解包加密金鑰，一旦訪問，資料引入和查詢將在 30 分鐘內恢復。

## <a name="cmk-kek-rotation"></a>CMK （KEK） 旋轉

CMK 的輪換需要使用 Azure 金鑰保存庫中的新金鑰版本顯式更新*群集*資源。 要使用新金鑰版本更新 Azure 監視器，請按照"使用金鑰識別碼詳細資訊更新*群集*資源"步驟中的說明進行操作。 如果在金鑰保存庫中更新金鑰版本，並且未在*群集*資源中更新新的金鑰識別碼詳細資訊，Azure 監視器存儲將繼續使用以前的金鑰。
在金鑰輪換操作（包括旋轉之前和輪換後引入的資料）後，可以訪問所有資料，因為所有資料仍由帳戶加密金鑰 （AEK） 加密，而這些資料現在由新的金鑰加密金鑰 （KEK） 版本加密。

## <a name="limitations-and-constraints"></a>限制和約束

- CMK 功能在 ADX 群集級別受支援，需要專用的 Azure 監視器 ADX 群集

- 每個訂閱的最大*群集*資源數限制為 2

- 只有在您收到產品組確認 ADX 群集預配已完成後，才能將*群集*資源關聯與工作區進行。 在此預配之前發送的資料將被刪除，並且無法恢復。

- CMK 加密適用于 CMK 配置後新引入的資料。 在 CMK 配置之前引入的資料，仍使用 Microsoft 金鑰進行加密。 您可以在 CMK 配置之前和之後無縫查詢資料。

- 工作區與*群集*資源關聯後，無法與*群集*資源取消關聯，因為資料使用金鑰加密，如果沒有 Azure 金鑰保存庫中的 KEK，則無法訪問。

- Azure 金鑰保存庫必須配置為可恢復。 預設情況下，這些屬性未啟用，應使用 CLI 和 PowerShell 進行配置：

  - [必須打開虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - [應打開清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止強制刪除機密/保存庫，即使在虛刪除後

- 應用程式見解和日誌分析需要單獨的*群集*資源。 *群集*資源的類型通過在創建時通過將"群集類型"屬性設置為"LogAnalytics"或"應用程式見解"來定義。 無法更改*群集*資源類型。

- *群集*資源移動到其他資源組或訂閱當前不受支援。

- Azure 金鑰保存庫、*群集*資源和相關工作區必須位於同一區域和同一 Azure 活動目錄 （Azure AD） 租戶中，但它們可以位於不同的訂閱中。

- 如果群集資源關聯到其他*群集*資源，則與*群集*資源的工作區關聯將失敗

## <a name="troubleshooting-and-management"></a>故障排除和管理

- 金鑰保存庫可用性
    - 在正常操作中，存儲緩存 AEK 會定期返回金鑰保存庫以展開。
    
    - 瞬態連接錯誤。 存儲通過允許金鑰在緩存中停留一段時間來處理瞬態錯誤（超時、連接故障、DNS 問題），從而克服了可用性方面的任何小問題。 查詢和引入功能不會中斷地繼續。
    
    - 即時網站，不可用約 30 分鐘將導致存儲帳戶不可用。 查詢功能不可用，並且使用 Microsoft 金鑰緩存了幾個小時的引入資料，以避免資料丟失。 還原對金鑰保存庫的訪問後，查詢將變為可用，臨時緩存資料將被引入資料存儲並使用 CMK 進行加密。

- 如果創建*群集*資源並立即指定 KeyVault 屬性，則操作可能會失敗，因為在將系統標識分配給*群集*資源之前無法定義訪問策略。

- 如果使用 KeyVault 屬性更新現有*群集*資源，並且金鑰保存庫中缺少"獲取"金鑰訪問策略，則操作將失敗。

- 如果嘗試刪除與工作區關聯的*群集*資源，則刪除操作將失敗。

- 獲取資源組的所有*群集*資源：

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

- 獲取訂閱的所有*群集*資源：

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **回應**
    
  與"資源組的*群集*資源"相同的回應，但在訂閱範圍內。
    
- 刪除*群集*資源 -- 執行虛刪除操作，以便在 14 天內恢復*群集*資源、資料和相關工作區，無論刪除是意外的還是有意的。 在虛刪除期間之後 *，群集資源和*資料不可恢復。 群集*資源*名稱在虛刪除期間保持保留狀態，無法創建具有該名稱的新群集。

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定

- 恢復*群集*資源和資料 -- 在虛刪除期間，創建具有相同名稱且位於同一訂閱、資源組和區域中的*群集*資源。 按照**創建*群集*資源**步驟恢復*群集*資源。


## <a name="appendix"></a>附錄

應用程式洞察客戶託管金鑰 （CMK） 也受支援，但應考慮以下更改，以説明您規劃針對應用程式洞察元件部署 CMK。

日誌分析和應用程式見解使用相同的資料存儲平臺和查詢引擎。 我們將通過應用程式見解集成到日誌分析中，將這兩個存儲整合在一起，以便在第二季度提供 Azure 監視器下的單一統一日誌存儲。
2020. 此更改將使應用程式見解資料進入日誌分析工作區，並使查詢、見解和其他改進成為可能，而工作區上的 CMK 配置也將應用於您的應用程式見解資料。

> [!NOTE]
> 如果在集成之前不必為應用程式洞察資料部署 CMK，我們建議您等待使用應用程式見解 CMK，因為此類部署將因集成而中斷，並且在遷移到 Log 後必須重新配置 CMK分析工作區。 每天 1TB 的最小值適用于群集級別，直到第二季度合併完成，應用程式見解和日誌分析需要單獨的群集。

## <a name="application-insights-cmk-configuration"></a>應用洞察 CMK 配置

應用見解 CMK 的配置與本文中說明的過程相同，包括約束和故障排除，這些步驟除外：

- 創建*群集*資源
- 將元件關聯到*群集*資源

為應用程式見解配置 CMK 時，請使用以下步驟而不是上面列出的步驟。

### <a name="create-a-cluster-resource"></a>創建*群集*資源

此資源用作金鑰保存庫和元件之間的中間標識連接。 收到訂閱已白名單的確認後，請在元件所在的區域創建日誌分析*群集*資源。 *群集*資源的類型通過在創建時通過將*群集類型*屬性設置為*LogAnalytics*或*應用程式見解*來定義。 它應該是應用程式*見解*的 CMK 應用程式見解。 配置後無法更改*群集類型*設置。

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

標識在創建時分配給*群集*資源。

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
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
"原則 id"是由託管標識服務生成的 GUID。

> [!IMPORTANT]
> 複製並保留"原則 id"值，因為在接下來的步驟中您將需要它。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>使用元件將元件關聯到*群集*資源[- 創建或更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)API

執行此操作（包括以下操作）的元件和*群集*資源都需要具有"寫入"許可權：

- 元件：微軟.見解/元件/寫入
- 在*群集*資源中：微軟.運營見解/集群/寫入

> [!IMPORTANT]
> 此步驟應僅在 ADX 群集預配後執行。 如果在預配之前關聯元件和引入資料，則引入的資料將被刪除，並且不可恢復。
> 要驗證 ADX 群集是否預配，請執行*群集*資源獲取 REST API 並檢查*預配狀態*值是否*成功*。

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
"群集定義 Id"是上一步驟的回應中提供的"群集 Id"值。
"好"的例子是"Web"。

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
"群集定義Id"是與此元件關聯的*群集*資源識別碼。

關聯後，發送到元件的資料將隨託管金鑰進行加密存儲。
