---
title: 針對傳入要求強制執行傳輸層安全性（TLS）的最低必要版本
titleSuffix: Azure Storage
description: 針對提出要求的用戶端，將儲存體帳戶設定為需要最低版本的傳輸層安全性（TLS）以 Azure 儲存體。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/24/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: eaa00716e8f86552a077fb527993f619fc9756b5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87275774"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>針對儲存體帳戶的要求強制執行傳輸層安全性（TLS）的最低必要版本

用戶端應用程式和 Azure 儲存體帳戶之間的通訊是使用傳輸層安全性（TLS）進行加密。 TLS 是標準密碼編譯通訊協定，可確保用戶端與服務之間的隱私權和資料完整性。 如需 TLS 的詳細資訊，請參閱[傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)。

Azure 儲存體目前支援三種 TLS 通訊協定版本：1.0、1.1 和1.2。 Azure 儲存體在公用 HTTPS 端點上使用 TLS 1.2，但仍支援 TLS 1.0 和 TLS 1.1 以提供回溯相容性。

根據預設，Azure 儲存體帳戶會允許用戶端以最舊版本的 TLS、TLS 1.0 和更新版本來傳送和接收資料。 若要強制執行更嚴格的安全性措施，您可以將儲存體帳戶設定為要求用戶端以較新的 TLS 版本來傳送和接收資料。 如果儲存體帳戶需要最低的 TLS 版本，則使用較舊版本提出的任何要求都會失敗。

本文說明如何使用拖曳（偵測-補救-Audit-治理）架構，為您的儲存體帳戶持續管理安全的 TLS。

如需如何在從用戶端應用程式傳送要求時指定特定 TLS 版本的詳細資訊，請參閱[設定用戶端應用程式的傳輸層安全性（TLS）](transport-layer-security-configure-client-version.md)。

## <a name="detect-the-tls-version-used-by-client-applications"></a>偵測用戶端應用程式所使用的 TLS 版本

當您對儲存體帳戶強制執行最低的 TLS 版本時，您會有風險，拒絕使用舊版 TLS 傳送資料的用戶端所提出的要求。 若要瞭解如何設定最低 TLS 版本可能會影響用戶端應用程式，Microsoft 建議您為您的 Azure 儲存體帳戶啟用記錄，並在一段時間後分析記錄，以偵測 TLS 用戶端應用程式的使用版本。

若要將要求記錄到您的 Azure 儲存體帳戶，並判斷用戶端所使用的 TLS 版本，您可以在 Azure 監視器（預覽）中使用 Azure 儲存體記錄。 如需詳細資訊，請參閱[Monitor Azure 儲存體](monitor-storage.md)。

Azure 監視器中的 Azure 儲存體記錄支援使用記錄查詢來分析記錄資料。 若要查詢記錄，您可以使用 Azure Log Analytics 工作區。 若要深入瞭解記錄查詢，請參閱[教學課程：開始使用 Log Analytics 查詢](../../azure-monitor/log-query/get-started-portal.md)。

若要使用 Azure 監視器記錄 Azure 儲存體資料，並使用 Azure Log Analytics 加以分析，您必須先建立診斷設定，以指出您想要記錄資料的要求類型和儲存體服務。 若要在 Azure 入口網站中建立診斷設定，請遵循下列步驟：

1. 在[Azure 監視器 preview 中註冊 Azure 儲存體記錄](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在訂用帳戶中建立新的 Log Analytics 工作區，其中包含您的 Azure 儲存體帳戶。 在您設定儲存體帳戶的記錄之後，記錄將會在 Log Analytics 工作區中提供。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [監視] 區段中，選取 [**診斷設定（預覽）**]。
1. 選取您要記錄要求的 Azure 儲存體服務。 例如，選擇**blob**以將要求記錄至 blob 儲存體。
1. 選取 [**新增診斷設定**]。
1. 提供診斷設定的名稱。
1. 在 [**類別細節**] 底下的 [**記錄**檔] 區段中，選擇要記錄的要求類型。 您可以記錄讀取、寫入和刪除要求。 例如，選擇 [ **StorageRead** ] 和 [ **StorageWrite** ] 會將讀取和寫入要求記錄到選取的服務。
1. 在 [**目的地詳細資料**] 底下，選取 [**傳送至 Log Analytics**]。 選取您稍早建立的訂用帳戶和 Log Analytics 工作區，如下圖所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="顯示如何建立記錄要求之診斷設定的螢幕擷取畫面":::

建立診斷設定之後，系統會根據該設定來記錄對儲存體帳戶的要求。 如需詳細資訊，請參閱[建立診斷設定以收集 Azure 中的資源記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)。

如需 Azure 監視器中 Azure 儲存體記錄可用欄位的參考，請參閱[資源記錄（預覽）](monitor-storage-reference.md#resource-logs-preview)。

### <a name="query-logged-requests-by-tls-version"></a>依 TLS 版本查詢已記錄的要求

Azure 監視器中的 Azure 儲存體記錄包含用來將要求傳送至儲存體帳戶的 TLS 版本。 使用**TlsVersion**屬性來檢查已記錄要求的 TLS 版本。

若要判斷過去七天內對 Blob 儲存體有多少個不同的 TLS 要求，請開啟您的 Log Analytics 工作區。 接下來，將下列查詢貼入新的記錄查詢並加以執行。 請記得以您自己的值取代括弧中的預留位置值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

結果會顯示每個 TLS 版本所提出的要求數計數：

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="顯示 log analytics 查詢結果以傳回 TLS 版本的螢幕擷取畫面":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>依呼叫端 IP 位址和使用者代理程式標頭查詢已記錄的要求

Azure 監視器中的 Azure 儲存體記錄也包含呼叫者 IP 位址和使用者代理程式標頭，以協助您評估哪些用戶端應用程式存取了儲存體帳戶。 您可以分析這些值，以決定是否必須將用戶端應用程式更新為使用較新的 TLS 版本，或是否可接受用戶端的要求（如果不是以最低的 TLS 版本傳送）。

若要判斷過去七天內，哪些用戶端要求的 TLS 版本比 TLS 1.2 還舊，請將下列查詢貼入新的記錄查詢並加以執行。 請記得以您自己的值取代括弧中的預留位置值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>使用 TLS 的最低版本補救安全性風險

當您確信使用舊版 TLS 的用戶端流量很小，或因為使用舊版 TLS 所提出的要求而失敗時，您可以開始在您的儲存體帳戶上強制執行最低的 TLS 版本。 要求用戶端使用最低版本的 TLS 來對儲存體帳戶提出要求，是將資料的安全性風險降至最低的策略的一部分。

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>設定儲存體帳戶的最低 TLS 版本

若要設定儲存體帳戶的最低 TLS 版本，請設定帳戶的**MinimumTlsVersion**版本。 此屬性適用于使用 Azure Resource Manager 部署模型建立的所有儲存體帳戶。 如需 Azure Resource Manager 部署模型的詳細資訊，請參閱[儲存體帳戶總覽](storage-account-overview.md)。

> [!NOTE]
> 預設不會設定**minimumTlsVersion**屬性，而且在您明確設定之前不會傳回值。 如果屬性值為**null**，儲存體帳戶允許以 TLS 1.0 或更新版本傳送的要求。

# <a name="portal"></a>[入口網站](#tab/portal)

若要使用 Azure 入口網站來設定儲存體帳戶的最低 TLS 版本，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 選取 [**設定**]。
1. 在 [**最低 tls 版本**] 底下，使用下拉式選取在此儲存體帳戶中存取資料所需的最低 tls 版本，如下圖所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="螢幕擷取畫面，顯示如何在 Azure 入口網站中設定 TLS 的最低版本":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 來設定儲存體帳戶的最低 TLS 版本，請安裝[Azure PowerShell 版本 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0)或更新版本。 接下來，設定新的或現有儲存體帳戶的**MinimumTLSVersion**屬性。 **MinimumTlsVersion**的有效值為 `TLS1_0` 、 `TLS1_1` 和 `TLS1_2` 。

下列範例會建立儲存體帳戶，並將**MinimumTLSVersion**設定為 tls 1.1，然後更新帳戶並將**MINIMUMTLSVERSION**設定為 tls 1.2。 此範例也會抓取每個案例中的屬性值。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName -AccountName $accountName -Location $location -SkuName Standard_GRS -MinimumTlsVersion TLS1_1
# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName -AccountName $accountName -MinimumTlsVersion TLS1_2
# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 來設定儲存體帳戶的最低 TLS 版本，請安裝 Azure CLI version 2.9.0 版或更新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。 接下來，設定新的或現有儲存體帳戶的**minimumTlsVersion**屬性。 **MinimumTlsVersion**的有效值為 `TLS1_0` 、 `TLS1_1` 和 `TLS1_2` 。

下列範例會建立儲存體帳戶，並將**minimumTLSVersion**設定為 TLS 1.1。 然後，它會更新帳戶，並將**minimumTLSVersion**屬性設定為 TLS 1.2。 此範例也會抓取每個案例中的屬性值。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[範本](#tab/template)

若要使用範本來設定儲存體帳戶的最低 TLS 版本，請建立範本，並將**MinimumTLSVersion**屬性設定為 `TLS1_0` 、 `TLS1_1` 或 `TLS1_2` 。 下列步驟說明如何在 Azure 入口網站中建立範本。

1. 在 [Azure 入口網站中，選擇 [**建立資源**]。
1. 在 **[搜尋 Marketplace**] 中，輸入**範本部署**，然後按**enter**。
1. 選擇 [**範本部署（使用自訂範本部署）**]，選擇 [**建立**]，然後**在編輯器中選擇 [建立您自己的範本**]。
1. 在範本編輯器中，貼上下列 JSON 以建立新的帳戶，並將最低的 TLS 版本設定為 TLS 1.2。 請記得以您自己的值取代角括弧中的預留位置。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "tags": {}
            }
        ]
    }
    ```

1. 儲存範本。
1. 指定資源群組參數，然後選擇 [**審核**] 和 [建立] 按鈕來部署範本，並建立已設定**MinimumTLSVersion**屬性的儲存體帳戶。

---

> [!NOTE]
> 在您更新儲存體帳戶的最低 TLS 版本之後，最多可能需要30秒的時間，才能完全傳播變更。

設定最低的 TLS 版本需要 Azure 儲存體資源提供者的2019-04-01 版或更新版本。 如需詳細資訊，請參閱[Azure 儲存體資源提供者 REST API](/rest/api/storagerp/)。

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>檢查多個帳戶所需的最低 TLS 版本

若要在具有最佳效能的一組儲存體帳戶上檢查所需的最低 TLS 版本，您可以使用 Azure 入口網站中的 [Azure Resource Graph Explorer]。 若要深入瞭解如何使用 Resource Graph Explorer，請參閱[快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](/azure/governance/resource-graph/first-query-portal)。

在 Resource Graph Explorer 中執行下列查詢，會傳回儲存體帳戶的清單，並顯示每個帳戶的最低 TLS 版本：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

### <a name="test-the-minimum-tls-version-from-a-client"></a>從用戶端測試最低的 TLS 版本

若要測試儲存體帳戶所需的最低 TLS 版本是否禁止使用較舊版本所進行的呼叫，您可以將用戶端設定為使用舊版的 TLS。 如需設定用戶端使用特定 TLS 版本的詳細資訊，請參閱[設定用戶端應用程式的傳輸層安全性（TLS）](transport-layer-security-configure-client-version.md)。

當用戶端使用不符合為帳戶設定的最低 TLS 版本的 TLS 版本來存取儲存體帳戶時，Azure 儲存體會傳回錯誤碼400錯誤（不正確的要求），以及一則訊息，指出所使用的 TLS 版本不允許對此儲存體帳戶提出要求。

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure 原則來審查合規性

如果您有大量的儲存體帳戶，您可能會想要執行審核，以確保所有帳戶都已針對您的組織所需的 TLS 最低版本進行設定。 若要針對其合規性來審核一組儲存體帳戶，請使用 Azure 原則。 Azure 原則是一種服務，可讓您用來建立、指派和管理將規則套用至 Azure 資源的原則。 Azure 原則可協助您保留符合公司標準和服務等級協定規範的資源。 如需詳細資訊，請參閱 [Azure 原則概觀](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>建立具有 Audit 效果的原則

Azure 原則支援決定針對資源評估原則規則時所發生狀況的效果。 當資源不符合規範時，此審核效果會建立警告，但不會停止要求。 如需效果的詳細資訊，請參閱[瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)。

若要使用 Azure 入口網站的最低 TLS 版本的審核效果來建立原則，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至 [Azure 原則] 服務。
1. 在 [**撰寫**中] 區段底下，選取 [**定義**]。
1. 選取 [**新增原則定義**] 以建立新的原則定義。
1. 在 [**定義位置**] 欄位中，選取 [**更多**] 按鈕以指定稽核原則資源的所在位置。
1. 指定原則的名稱。 您可以選擇性地指定 [描述] 和 [類別]。
1. 在 [**原則規則**] 底下，將下列原則定義新增至 [ **policyRule** ] 區段。

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 儲存原則。

### <a name="assign-the-policy"></a>指派原則

接下來，將原則指派給資源。 原則的範圍會對應到該資源和其下的所有資源。 如需原則指派的詳細資訊，請參閱[Azure 原則指派結構](../../governance/policy/concepts/assignment-structure.md)。

若要使用 Azure 入口網站指派原則，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至 [Azure 原則] 服務。
1. 在 [**撰寫**中] 區段下，選取 [**指派**]。
1. 選取 [**指派原則**] 以建立新的原則指派。
1. 在 [**範圍**] 欄位中，選取原則指派的範圍。
1. 針對 [**原則定義**] 欄位，選取 [**更多**] 按鈕，然後從清單中選取您在上一節中定義的原則。
1. 提供原則指派的名稱。 描述是選擇性的。
1. 將 [**強制執行原則**] 設為 [*已啟用*]。 此設定不會影響稽核原則。
1. 選取 [**審核] + [建立**] 以建立指派。

### <a name="view-compliance-report"></a>查看合規性報告

指派原則之後，您就可以查看合規性報告。 稽核原則的合規性報告提供有關哪些儲存體帳戶不符合原則的資訊。 如需詳細資訊，請參閱[取得原則合規性資料](../../governance/policy/how-to/get-compliance-data.md)。

建立原則指派之後，可能需要幾分鐘的時間才能讓合規性報告變成可用狀態。

若要在 Azure 入口網站中查看合規性報告，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至 [Azure 原則] 服務。
1. 選取 [**相容性**]。
1. 針對您在上一個步驟中建立的原則指派名稱，篩選其結果。 此報告會顯示有多少資源不符合原則。
1. 您可以向下切入報表以取得其他詳細資料，包括不符合規範的儲存體帳戶清單。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="顯示最低 TLS 版本的稽核原則合規性報告的螢幕擷取畫面":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>使用 Azure 原則強制執行最低的 TLS 版本

Azure 原則藉由確保 Azure 資源符合需求和標準，來支援雲端治理。 若要對組織中的儲存體帳戶強制執行最低的 TLS 版本需求，您可以建立一個原則來防止建立新的儲存體帳戶，將最低 TLS 需求設定為舊版的 TLS，而不是原則所規定的版本。 如果該帳戶的最低 TLS 版本設定與原則不相容，則此原則也會防止現有帳戶的所有設定變更。

強制原則會使用拒絕效果，以防止會建立或修改儲存體帳戶的要求，使最低的 TLS 版本不再符合您組織的標準。 如需效果的詳細資訊，請參閱[瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)。

若要針對小於 TLS 1.2 的最低 TLS 版本建立具有拒絕效果的原則，請遵循[使用 Azure 原則來審核合規性](#use-azure-policy-to-audit-for-compliance)中所述的相同步驟，但在原則定義的**policyRule**區段中提供下列 JSON：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

建立具有 [拒絕] 效果的原則並將它指派給範圍之後，使用者無法建立最低 TLS 版本低於1.2 的儲存體帳戶。 使用者也不能對現有的儲存體帳戶進行任何設定變更，因為目前需要的最低 TLS 版本早于1.2。 嘗試這麼做會導致錯誤。 儲存體帳戶所需的最低 TLS 版本必須設為1.2，才能繼續建立或設定帳戶。

下圖顯示當您嘗試在具有拒絕效果的原則需要將最低 TLS 版本設定為 TLS 1.2 時，建立最低 TLS 版本設定為 TLS 1.0 （新帳戶的預設值）的儲存體帳戶時所發生的錯誤。

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="螢幕擷取畫面，顯示在違反原則時建立儲存體帳戶時所發生的錯誤":::

## <a name="next-steps"></a>後續步驟

- [設定用戶端應用程式的傳輸層安全性（TLS）](transport-layer-security-configure-client-version.md)
- [Blob 儲存體的安全性建議](../blobs/security-recommendations.md)
