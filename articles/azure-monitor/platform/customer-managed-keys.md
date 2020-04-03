---
title: Azure 監視器客戶管理的關鍵設定
description: 設定客戶管理金鑰 (CMK) 以使用 Azure 密鑰保管庫密鑰加密日誌分析工作區中的資料的資訊和步驟。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 18c926d16319eb8a8736a51d5f10e434b94d0ebe
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582484"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure 監視器客戶管理的關鍵設定 

本文提供了後台資訊和步驟,用於為日誌分析工作區和應用程式見解元件配置客戶管理密鑰 (CMK)。 配置完成後,發送到工作區或元件的任何數據都使用 Azure 密鑰保管庫密鑰進行加密。

我們建議您在設定之前查看下面的[限制和約束](#limitations-and-constraints)。

## <a name="disclaimers"></a>免責聲明

- Azure 監視器 CMK 是一種早期訪問功能,已為註冊訂閱啟用。

- 本文中描述的 CMK 部署以生產品質提供,並且支援此類部署,儘管它是早期訪問功能。

- CMK 功能在專用資料儲存群集上提供,該群集是 Azure 資料資源管理員 (ADX) 叢集,適用於每天發送 1TB 或更多的客戶。 

- CMK 定價模型當前不可用,本文未介紹此版本。 專用 ADX 群集的定價模型預計將在 2020 日曆年 (CY) 2020 年第二季度使用,並將應用於任何現有的 CMK 部署。

- 本文介紹了日誌分析工作區的 CMK 配置。 在附錄中列出了差異時,還支援使用本文支援 CMK 應用程式見解元件。

> [!NOTE]
> 日誌分析和應用程式見解使用相同的數據存儲平台和查詢引擎。
> 我們通過將應用程式見解集成到日誌分析中來將這兩個存儲整合在一起,以創建 Azure 監視器下的單一統一日誌存儲。 此更改計劃於 2020 日曆年第二季度進行。 如果到那時不必為應用程式見解數據部署 CMK,我們建議您等待合併的完成,因為此類部署將因合併而中斷,並且在遷移到 Log Analytics 工作區後必須重新配置 CMK。 每天 1TB 的最小值適用於群集級別,直到第二季度合併完成,應用程式見解和日誌分析需要單獨的群集。

## <a name="customer-managed-key-cmk-overview"></a>客戶管理金鑰 (CMK) 概述

[靜態加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是組織中常見的隱私和安全要求。 您可以讓 Azure 完全管理靜態加密,同時您可以有各種選項來密切管理加密或加密金鑰。

Azure 監視器資料儲存可確保在 Azure 儲存中儲存時使用 Azure 管理的密鑰對靜態加密的所有數據。 Azure 監視器還提供一個選項,用於使用存儲在[Azure 密鑰保管庫中](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的您自己的密鑰進行數據加密,該密鑰使用系統分配的[託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)身份驗證進行存取。 此金鑰可以是[軟體或硬體-HSM保護](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。
Azure 監視器使用加密與[Azure 儲存加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)操作方式相同。

Azure 監視器儲存存取金鑰保管庫進行換行和解包操作的頻率為 6 到 60 秒。Azure 監視器記憶體始終在一小時內尊重密鑰許可權的更改。

過去 14 天內的引入數據也保存在熱緩存(SSD 支援)中,以便高效運行查詢引擎。 無論 CMK 設定如何,此數據仍使用 Microsoft 密鑰進行加密,但我們正在努力在 2020 年上半年使用 CMK 加密 SSD。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure 監視器中的工作方式

Azure 監視器利用系統分配的託管標識來授予對 Azure 密鑰保管庫的訪問許可權。系統分配的託管標識只能與單個 Azure 資源關聯。 叢集級別支援 Azure 監視器資料儲存 (ADX 群集)的標識,這要求 CMK 功能在專用 ADX 群集上傳遞。 為了在多個工作區上支援 CMK,新的日誌分析資源 (*群集*) 充當密鑰保管庫和日誌分析工作區之間的中間標識連接。 此概念符合系統分配的標識約束,並且標識在 ADX 群集和日誌分析*群集*資源之間保留,而所有關聯的工作區的數據都使用密鑰保管庫密鑰進行保護。 底層 ADX 群集儲存使用與*群集*資源\'關聯的託管標識通過 Azure 活動目錄對 Azure 密鑰保管庫進行身份驗證和訪問。

![CMK 概述](media/customer-managed-keys/cmk-overview-8bit.png)
1.    客戶金鑰庫。
2.    具有對密鑰保管庫許可權的託管標識的客戶日誌分析*群集*資源 - 在資料存儲 (ADX 叢集) 級別支援該標識。
3.    Azure 監視器專用 ADX 群集。
4.    與*CMK*加密群集資源關聯的客戶工作區。

## <a name="encryption-keys-management"></a>加密金鑰管理

儲存資料加密涉及 3 型態的金鑰:

- **KEK** - 金鑰加密金鑰 (CMK)
- **AEK** - 帳號加密金鑰
- **DEK** - 資料加密金鑰

適用的規則如下：

- ADX 儲存帳戶為每個儲存帳戶(稱為 AEK)生成唯一的加密密鑰。

- AEK 用於派生 DEK,SDK 是用於加密寫入磁碟的每個數據塊的密鑰。

- 在密鑰保管庫中配置密鑰並在*群集*資源中引用密鑰時,Azure 儲存會向 Azure 金鑰保管庫發送請求,以包裝和解包 AEK 以執行數據加密和解密操作。

- 您的 KEK 永遠不會離開金鑰保管庫,如果是 HSM 金鑰,它永遠不會離開硬體。

- Azure 儲存使用與*群集*資源關聯的託管標識通過 Azure 活動目錄對 Azure 密鑰保管庫進行身份驗證和訪問。

## <a name="cmk-provisioning-procedure"></a>CMK 預先設定

對於應用見解 CMK 配置,請按照附錄內容執行步驟 3 和 6。

1. 訂閱白名單 -- 此早期存取功能是必需的
2. 建立 Azure 金鑰保存庫並儲存金鑰
3. 建立*叢集*資源
4. Azure 監視器資料儲存 (ADX 叢集) 預先
5. 給金鑰保存的權限
6. 關聯紀錄分析工作區

UI 目前不支援該過程,並且通過 REST API 執行預配過程。

> [!IMPORTANT]
> 任何 API 請求都必須在請求標頭中包含承載授權權杖。

例如：

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

*其中 eyJ0eXAiO....* 

可以使用以下方法之一抓取權杖:

1. 使用[應用註冊](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。
2. 在 Azure 入口網站
    1. 在開發人員工具 (F12) 中導航到 Azure 門戶
    1. 在「批次處理 api 版本」實體之一的請求標頭下搜尋授權字串。 它看起來像:「授權:承載\<」令牌」。\> 
    1. 根據以下範例複製並將其添加到API呼叫中。
3. 導航到 Azure REST 文件網站。 按任何 API 上的「試用」並複製承載權杖。

### <a name="subscription-whitelisting"></a>訂閱白名單

CMK 功能是一種早期訪問功能。 計劃創建*群集*資源的訂閱必須事先由 Azure 產品組列入白名單。 使用 Microsoft 的聯繫人提供訂閱 ID。

> [!IMPORTANT]
> CMK 功能是區域性的。 Azure 密鑰保管庫、*群集*資源和關聯的日誌分析工作區必須位於同一區域中,但它們可以位於不同的訂閱中。

### <a name="storing-encryption-key-kek"></a>儲存加密金鑰 (KEK)

創建或使用已必須生成的 Azure 金鑰保管庫,或導入用於數據加密的金鑰。 Azure 密鑰保管庫必須配置為可恢復,以保護密鑰和對 Azure 監視器中數據的訪問。 您可以在金鑰保存的屬性下驗證此設定,應開啟*軟刪除*與*清除保護*。

這些設定可透過 CLI 與 PowerShell 取得:
- [虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)防護裝置,防止強制刪除機密/保管庫,即使在軟刪除後

### <a name="create-cluster-resource"></a>建立*叢集*資源

此資源用作密鑰保管庫和日誌分析工作區之間的中間標識連接。 收到訂閱已白名單的確認后,在工作區所在的區域創建日誌分析*群集*資源。 應用程式見解和日誌分析需要單獨的*群集*資源類型。 *叢集*資源的類型通過在創建時通過將*群集類型*屬性設置為*LogAnalytics*或*應用程式見解*來定義。 叢集資源類型後無法更改。

有關應用見解 CMK 配置,請遵循附錄內容。

創建*群集*資源時,必須指定容量預留級別 (sKU)。 容量預留級別可能位於每天 1,000 到 2,000 GB 之間,稍後可以按 100 步更新。 如果您需要每天高於 2,000 GB 的容量預留級別,請聯絡 Microsoft 聯繫人啟用它。 此屬性當前不會影響計費 - 一旦引入了專用群集的定價模型,計費將應用於任何現有的 CMK 部署。

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

202 已接受。 這是非同步操作的標準資源管理員回應。

>[!Important]
> 完成不足 ADX 群集的預配需要一段時間才能完成。 在*群組資源*上執行 GET REST API 呼叫並查看預先狀態值時,可以驗證*預先狀態*。 它是*預配時預配帳戶*,完成後*已成功*預配帳戶。

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure 監視器資料儲存 (ADX 叢集) 預先

在功能的早期訪問期間,一旦完成前面的步驟,產品團隊會手動預配 ADX 群集。 使用 Microsoft 通道執行此步驟並提供*群集*資源回應。 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 複製並保存回應,因為您將在後續步驟中需要其詳細資訊

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

"主體 Id" GUID 由*群集*資源的託管標識服務生成。

### <a name="grant-key-vault-permissions"></a>授金鑰保存庫權限

使用向*群集*資源授予許可權的新訪問策略更新密鑰保管庫。 這些許可權由下基礎 Azure 監視器儲存用於數據加密。 在 Azure 門戶開啟金鑰保管庫,然後按下「存取策略」然後按下「+ 新增存取策略」以建立具有以下設定的策略:

- 金鑰許可權:選擇「獲取」,「包裝鍵」和「取消包裝金鑰」許可權。
- 選擇主體:輸入在上一步中回應中返回的主體 ID 值。

![授金鑰保存庫權限](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要*Get*許可權來驗證金鑰保管庫是否配置為可恢復,以保護密鑰和對 Azure 監視器數據的訪問。

### <a name="update-cluster-resource-with-key-identifier-details"></a>使用金鑰識別碼詳細資訊更新叢集資源

此步驟適用於金鑰保管庫中的初始和將來密鑰版本更新。 它通知 Azure 監視器儲存有關用於數據加密的密鑰版本。 更新時,新密鑰將用於包裝和解包到存儲密鑰 (AEK)。

要使用密鑰保管庫*密鑰標識符*詳細資訊更新*群集*資源,請在 Azure 金鑰保管庫中選擇金鑰的當前版本,以獲取有關密鑰標識符詳細資訊。

![授金鑰保存庫權限](media/customer-managed-keys/key-identifier-8bit.png)

使用金鑰識別碼詳細資訊更新*群組*資源 KeyVault 屬性。

**更新**

>[!Warning]
> 您必須在*群組*資源更新中提供完整的正文,其中包括*識別**、sKU、KeyVault**屬性*和*位置*。 找不到*KeyVault 屬性*詳細資訊會移除金*鑰*的金鑰識別碼,並造成[金鑰撤銷](#cmk-kek-revocation)。

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
"KeyVault 屬性"包含金鑰保管庫金鑰標識符詳細資訊。

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

### <a name="workspace-association-to-cluster-resource"></a>工作區關聯到*叢集*資源

對於應用見解 CMK 配置,請按照此步驟的附錄內容操作。

> [!IMPORTANT]
> 此步驟應僅在 ADX 群集預配後執行。 如果在預配之前關聯工作區和引入數據,則引入的數據將被刪除,並且不可恢復。
> 要驗證 ADX 群組是否預先,請執行*叢集*資源取得 REST API 並檢查*預先狀態*值是否*成功*。

執行此操作(包括以下操作)需要對工作區和*群集*資源具有「寫入」許可權:

- 在工作區中:微軟.操作見解/工作空間/寫入
- 在*群集*資源中:微軟.運營見解/集群/寫入

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

工作區關聯通過資源管理器非同步操作執行,這可能需要長達 90 分鐘才能完成。 下一步將介紹如何檢查工作區關聯狀態。 工作區關聯后,引入到工作區的數據將隨託管密鑰加密存儲。

### <a name="workspace-association-verification"></a>工作區關聯驗證
您可以通過查看[工作區和獲取](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)回應來驗證工作區是否與*群集*資源相關聯。 關聯的工作區將具有具有*群集*資源ID的"群集資源Id"屬性。

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

## <a name="cmk-kek-revocation"></a>CMK (KEK) 撤銷

您可以通過關閉金鑰或刪除金鑰保管庫中的*群集*資源存取策略來撤銷對資料的存取。 Azure 監視器儲存始終在一小時內尊重密鑰許可權的更改,通常更快,並且存儲將不可用。 引入到與*群集*資源關聯的工作區的任何數據都將被刪除,查詢將失敗。 以前引入的數據在 Azure 監視器儲存中仍然無法訪問,只要您是*群集*資源,並且您的工作區不會被刪除。 無法訪問的數據受數據保留策略的約束,並在達到保留時清除。

存儲將定期輪詢金鑰保管庫,以嘗試解包加密密鑰,一旦訪問,數據引入和查詢將在 30 分鐘內恢復。

## <a name="cmk-kek-rotation"></a>CMK (KEK) 旋轉

CMK 的輪換需要使用 Azure 密鑰保管庫中的新密鑰版本顯式更新*群集*資源。 要使用新密鑰版本更新 Azure 監視器,請按照"使用密鑰標識符詳細資訊更新*叢集*資源"步驟中的說明進行操作。 如果在密鑰保管庫中更新密鑰版本,並且未在*群集*資源中更新新的密鑰標識符詳細資訊,Azure 監視器存儲將繼續使用以前的密鑰。
在金鑰輪換操作(包括旋轉之前和輪換後引入的數據)後,可以存取所有資料,因為所有資料仍由帳戶加密金鑰 (AEK) 加密,而 AEK 現在由新的金鑰加密密鑰 (KEK) 版本加密。

## <a name="limitations-and-constraints"></a>限制和約束

- CMK 功能在 ADX 群集級別受支援,需要專用 Azure 監視器 ADX 群集,需要每天發送 1TB 或更多。

- 每個訂閱的最大*叢集*資源數限制為 2

- 只有在驗證 ADX 群集預配已完成後,才應對工作區進行*群集*資源關聯。 在此預配之前發送的數據將被刪除,並且無法恢復。

- CMK 加密適用於 CMK 配置後新引入的數據。 在 CMK 設定之前引入的數據,仍使用 Microsoft 金鑰進行加密。 您可以無縫查詢在 CMK 配置之前和之後引入的數據。

- 工作區與*群集*資源關聯后,無法與*群集*資源取消關聯,因為數據使用密鑰加密,如果沒有 Azure 密鑰保管庫中的 KEK,則無法訪問。

- Azure 密鑰保管庫必須配置為可恢復。 預設情況下,這些屬性未啟用,應使用 CLI 和 PowerShell 進行設定:

  - [必須開啟軟刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - [應開啟清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection),以防止強制刪除機密/保管庫,即使在軟刪除後

- 應用程式見解和日誌分析需要單獨的*群集*資源。 *叢集*資源的類型通過在創建時通過將"群集類型"屬性設置為"LogAnalytics"或"應用程式見解"來定義。 無法更改*群組*資源類型。

- *叢集*資源移動到其他資源組或訂閱當前不受支援。

- Azure 密鑰保管庫、*群集*資源和相關工作區必須位於同一區域和同一 Azure 活動目錄 (Azure AD) 租戶中,但它們可以位於不同的訂閱中。

- 如果群集資源關聯到其他*群集*資源,則與*群集*資源的工作區關聯將失敗

## <a name="troubleshooting-and-management"></a>容錯排除和管理

- 金鑰保存庫可用性
    - 在正常操作中, 儲存快取 AEK 的時間很短,並傳回金鑰保管庫定期解包。
    
    - 瞬態連接錯誤 -- 儲存通過允許密鑰在快取中停留一小段時間來處理瞬態錯誤(超時、連接故障、DNS 問題),從而克服了可用性上的任何小問題。 查詢和引入功能不會中斷地繼續。
    
    - 即時網站 - 不可用約 30 分鐘將導致存儲帳戶不可用。 查詢功能不可用,並且使用 Microsoft 金鑰緩存了幾個小時的引入數據,以避免數據丟失。 還原對密鑰保管庫的訪問后,查詢將變為可用,臨時緩存數據將被引入數據存儲並使用 CMK 進行加密。

- 如果創建*群集*資源並立即指定 KeyVault 屬性,則操作可能會失敗,因為在將系統識別分配給*叢集*資源之前無法定義存取策略。

- 如果使用 KeyVault 屬性更新現有*群集*資源,並且密鑰保管庫中缺少「獲取」金鑰存取策略,則操作將失敗。

- 如果嘗試刪除與工作區關聯的*群集*資源,則刪除操作將失敗。

- 取得資源群組的所有*群組*資源:

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

- 取得訂閱所有*群組*資源:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **回應**
    
  與「資源組的*群集*資源」相同的回應,但在訂閱範圍內。
    
- 刪除*群集*資源 -- 執行軟刪除操作,以便在 14 天內恢復群集資源、數據和相關工作區,無論刪除是意外的還是有意的。 叢集*資源*名稱在軟刪除期間保持保留狀態,無法建立具有該名稱的新群集。 在軟刪除期間之後 *,群集資源和*資料不可恢復。 關聯的工作區從*群集*資源取消關聯,新數據被引入到共用存儲和使用 Microsoft 密鑰進行加密。

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **回應**

  200 確定

- 復原*叢集*資源和資料 -- 在軟刪除期間,創建具有相同名稱且位於同一訂閱、資源組和區域中的*叢集*資源。 按照**創建*群集*資源**步驟恢復*群集*資源。


## <a name="appendix"></a>附錄

應用程式洞察客戶託管金鑰 (CMK) 也受支援,但應考慮以下更改,以説明您規劃針對應用程式洞察元件部署 CMK。

日誌分析和應用程式見解使用相同的數據存儲平台和查詢引擎。 我們將通過應用程式見解集成到日誌分析中,將這兩個存儲整合在一起,以便在第二季度提供 Azure 監視器下的單一統一日誌存儲。
2020. 此更改將使應用程式見解資料進入日誌分析工作區,並使查詢、見解和其他改進成為可能,而工作區上的 CMK 配置也將應用於您的應用程式見解數據。

> [!NOTE]
> 如果在整合的之前不必為應用程式洞察資料部署 CMK,我們建議您等待使用應用程式見解 CMK,因為此類部署將因集成而中斷,並且在遷移到日誌分析工作區後必須重新配置 CMK。 每天 1TB 的最小值適用於群集級別,直到第二季度合併完成,應用程式見解和日誌分析需要單獨的群集。

## <a name="application-insights-cmk-configuration"></a>套用洞察 CMK 設定

應用見解 CMK 的配置與本文中說明的過程相同,包括約束和故障排除,這些步驟除外:

- 建立*叢集*資源
- 將元件關聯到*叢集*資源

為應用程式見解配置 CMK 時,請使用以下步驟而不是上面列出的步驟。

### <a name="create-a-cluster-resource"></a>建立*叢集*資源

此資源用作密鑰保管庫和元件之間的中間標識連接。 收到訂閱已白名單的確認后,請在元件所在的區域創建日誌分析*群集*資源。 *叢集*資源的類型通過在創建時通過將*群集類型*屬性設置為*LogAnalytics*或*應用程式見解*來定義。 它應該是應用程式*見解*的 CMK 應用程式見解。 配置後無法更改*群組類型*設定。

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**回應**

202 已接受。 這是非同步操作的標準資源管理員回應。

>[!Important]
> 完成不足 ADX 群集的預配需要幾分鐘時間。 在*群組資源*上執行 GET REST API 呼叫並查看預先狀態值時,可以驗證*預先狀態*。 它是*預配時預配帳戶*,完成後為"成功"。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>使用元件將元件關聯到*群組*資源 -[建立或更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)API

執行此操作(包括以下操作)的元件和*叢集*資源都需要具有「寫入」許可權:

- 元件:微軟.見解/元件/寫入
- 在*群集*資源中:微軟.運營見解/集群/寫入

> [!IMPORTANT]
> 此步驟應僅在 ADX 群集預配後執行。 如果在預配之前關聯元件和引入數據,則引入的數據將被刪除,並且不可恢復。
> 要驗證 ADX 群組是否預先,請執行*叢集*資源取得 REST API 並檢查*預先狀態*值是否*成功*。

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

> [!IMPORTANT]
> 複製並保留"原則 id"值,因為在接下來的步驟中您將需要它。

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
"群集定義Id"是與此元件關聯的*群集*資源 ID。

關聯後,發送到元件的數據將隨託管金鑰進行加密存儲。
