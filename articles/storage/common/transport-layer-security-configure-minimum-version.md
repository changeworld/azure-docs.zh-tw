---
title: 為儲存體帳戶設定傳輸層安全性 (TLS) 的最低必要版本
titleSuffix: Azure Storage
description: 針對針對 Azure 儲存體提出要求的用戶端，將儲存體帳戶設定為要求傳輸層安全性的最低版本 (TLS) 。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209384"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>為儲存體帳戶設定傳輸層安全性 (TLS) 的最低必要版本

用戶端應用程式和 Azure 儲存體帳戶之間的通訊是使用傳輸層安全性 (TLS) 進行加密。 TLS 是標準密碼編譯通訊協定，可確保用戶端與服務之間的隱私權和資料完整性。 如需 TLS 的詳細資訊，請參閱[傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)。

Azure 儲存體目前支援三種 TLS 通訊協定版本：1.0、1.1 和1.2。 TLS 1.2 是最安全的 TLS 版本。 Azure 儲存體在公用 HTTPs 端點上使用 TLS 1.2，但仍支援 TLS 1.0 和 TLS 1.1 以提供回溯相容性。

根據預設，Azure 儲存體帳戶會允許用戶端以最舊版本的 TLS、TLS 1.0 和更新版本來傳送和接收資料。 若要強制執行更嚴格的安全性措施，您可以將儲存體帳戶設定為要求用戶端以較新的 TLS 版本來傳送和接收資料。 如果儲存體帳戶需要最低的 TLS 版本，則使用較早版本提出的任何要求都會失敗。

本文說明如何設定儲存體帳戶，要求用戶端傳送具有最低 TLS 版本的要求。 如需如何在從用戶端應用程式傳送要求時指定 TLS 特定版本的詳細資訊，請參閱[設定用戶端應用程式的傳輸層安全性 (TLS) ](transport-layer-security-configure-client-version.md)。

## <a name="detect-the-tls-version-used-by-client-applications"></a>偵測用戶端應用程式所使用的 TLS 版本

當您對儲存體帳戶強制執行最低的 TLS 版本時，您會有風險，拒絕使用舊版 TLS 傳送資料的用戶端所提出的要求。 若要瞭解如何設定最低 TLS 版本可能會影響用戶端應用程式，Microsoft 建議您為您的 Azure 儲存體帳戶啟用記錄，並在一段時間後分析記錄，以判斷 TLS 用戶端應用程式使用的版本。

若要將要求記錄到您的 Azure 儲存體帳戶，並判斷用戶端所使用的 TLS 版本，您可以使用 Azure 監視器 (preview) 中的 Azure 儲存體記錄。 如需詳細資訊，請參閱[Monitor Azure 儲存體](monitor-storage.md)。

Azure 監視器中的 Azure 儲存體記錄支援使用記錄查詢來分析記錄資料。 若要查詢記錄，您可以使用 Azure Log Analytics 工作區。 若要深入瞭解記錄查詢，請參閱[教學課程：開始使用 Log Analytics 查詢](../../azure-monitor/log-query/get-started-portal.md)。

若要使用 Azure 監視器記錄 Azure 儲存體資料，並使用 Azure Log Analytics 加以分析，您必須先建立診斷設定，以指出您想要記錄資料的要求類型和儲存體服務。 若要在 Azure 入口網站中建立診斷設定，請遵循下列步驟：

1. 在[Azure 監視器 preview 中註冊 Azure 儲存體記錄](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在訂用帳戶中建立新的 Log Analytics 工作區，其中包含您的 Azure 儲存體帳戶。 在您設定儲存體帳戶的記錄之後，記錄將會在 Log Analytics 工作區中提供。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [監視] 區段中，選取 [**診斷設定] (預覽) **。
1. 選取您要記錄要求的 Azure 儲存體服務。 例如，選擇**blob**以將要求記錄至 blob 儲存體。
1. 選取 [**新增診斷設定**]。
1. 提供診斷設定的名稱。
1. 在 [**類別細節**] 底下的 [**記錄**檔] 區段中，選擇要記錄的要求類型。 您可以記錄讀取、寫入和刪除要求。 例如，選擇 [ **StorageRead** ] 和 [ **StorageWrite** ] 會將讀取和寫入要求記錄到選取的服務。
1. 在 [**目的地詳細資料**] 底下，選取 [**傳送至 Log Analytics**]。 選取您稍早建立的訂用帳戶和 Log Analytics 工作區，如下圖所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="顯示如何建立記錄要求之診斷設定的螢幕擷取畫面":::

建立診斷設定之後，系統會根據該設定來記錄對儲存體帳戶的要求。 如需詳細資訊，請參閱[建立診斷設定以收集 Azure 中的資源記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)。

如需 Azure 監視器的 Azure 儲存體記錄中可用欄位的參考，請參閱[ (preview) 的資源記錄](monitor-storage-reference.md#resource-logs-preview)。

### <a name="query-logged-requests-by-tls-version"></a>依 TLS 版本查詢已記錄的要求

Azure 監視器中的 Azure 儲存體記錄包含用來將要求傳送至儲存體帳戶的 TLS 版本。 使用**TlsVersion**屬性來檢查已記錄要求的 TLS 版本。

若要取出過去7天的記錄，並判斷每個 TLS 版本對 Blob 儲存體提出的要求數，請開啟您的 Log Analytics 工作區。 接下來，將下列查詢貼入新的記錄查詢並加以執行。 請記得以您自己的值取代括弧中的預留位置值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

結果會顯示每個 TLS 版本所提出的要求數計數：

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="顯示 log analytics 查詢結果以傳回 TLS 版本的螢幕擷取畫面":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>依呼叫端 IP 位址和使用者代理程式標頭查詢已記錄的要求

Azure 監視器中的 Azure 儲存體記錄也包含呼叫者 IP 位址和使用者代理程式標頭，以協助您評估哪些用戶端應用程式存取了儲存體帳戶。 您可以分析這些值，以決定是否必須將用戶端應用程式更新為使用較新的 TLS 版本，或是否可接受用戶端的要求（如果不是以最低的 TLS 版本傳送）。

若要取出過去7天的記錄，並判斷哪些用戶端是以 TLS 1.2 之前的 TLS 版本提出要求，請將下列查詢貼入新的記錄查詢並加以執行。 請記得以您自己的值取代括弧中的預留位置值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>設定帳戶的最低 TLS 版本

若要設定儲存體帳戶的最低 TLS 版本，請使用 [Azure 入口網站] 或 [Azure CLI] 來設定帳戶的**minimumTlsVersion**版本。 此屬性適用于使用 Azure Resource Manager 部署模型建立的所有儲存體帳戶。 如需詳細資訊，請參閱[儲存體帳戶總覽](storage-account-overview.md)。

# <a name="portal"></a>[入口網站](#tab/portal)

若要使用 Azure 入口網站來設定儲存體帳戶的最低 TLS 版本，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 選取 [**設定**]。
1. 在 [**最低 tls 版本**] 底下，使用下拉式選取在此儲存體帳戶中存取資料所需的最低 tls 版本，如下圖所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="螢幕擷取畫面，顯示如何在 Azure 入口網站中設定 TLS 的最低版本":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 來設定儲存體帳戶的最低 TLS 版本，請先呼叫[az resource show](/cli/azure/resource#az-resource-show)命令，以取得儲存體帳戶的資源識別碼。 接下來，呼叫[az resource update](/cli/azure/resource#az-resource-update)命令來設定儲存體帳戶的**minimumTlsVersion**屬性。 **MinimumTlsVersion**的有效值為 `TLS1_0` 、 `TLS1_1` 和 `TLS1_2` 。

下列範例會將最低的 TLS 版本設定為1.2。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> 在您更新儲存體帳戶的最低 TLS 版本之後，最多可能需要30秒的時間，才能完全傳播變更。

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>檢查帳戶的最低必要 TLS 版本

若要判斷為儲存體帳戶設定的最低必要 TLS 版本，請檢查 Azure Resource Manager **minimumTlsVersion**屬性。 若要一次檢查一個大型儲存體帳戶的這個屬性，請使用 [Azure Resource Graph Explorer]。

預設不會設定**minimumTlsVersion**屬性，而且在您明確設定之前不會傳回值。 儲存體帳戶預設為允許以 TLS 1.0 或更高版本傳送的要求（如果屬性值為 null）。

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>檢查單一儲存體帳戶所需的最低 TLS 版本

若要使用 Azure CLI 檢查單一儲存體帳戶所需的最低 TLS 版本，請呼叫[az resource show](/cli/azure/resource#az-resource-show)命令，並查詢**minimumTlsVersion**屬性：

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>檢查一組儲存體帳戶的最低必要 TLS 版本

若要在具有最佳效能的一組儲存體帳戶上檢查所需的最低 TLS 版本，您可以使用 Azure 入口網站中的 [Azure Resource Graph Explorer]。 若要深入瞭解如何使用 Resource Graph Explorer，請參閱[快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](/azure/governance/resource-graph/first-query-portal)。

在 Resource Graph Explorer 中執行下列查詢，會傳回儲存體帳戶的清單，並顯示每個帳戶的最低 TLS 版本：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>從用戶端測試最低的 TLS 版本

若要測試儲存體帳戶所需的最低 TLS 版本是否禁止使用舊版進行呼叫，您可以將用戶端設定為使用舊版的 TLS。 如需將用戶端設定為使用特定 TLS 版本的詳細資訊，請參閱[為用戶端應用程式設定 (TLS) 的傳輸層安全性](transport-layer-security-configure-client-version.md)。

當用戶端使用不符合為帳戶所設定的最低 TLS 版本的 TLS 版本來存取儲存體帳戶時，Azure 儲存體會傳回錯誤碼400錯誤 (不正確的要求) 以及訊息，指出所使用的 TLS 版本不允許對此儲存體帳戶提出要求。

## <a name="next-steps"></a>後續步驟

- [為用戶端應用程式設定 (TLS) 的傳輸層安全性](transport-layer-security-configure-client-version.md)
- [Blob 儲存體的安全性建議](../blobs/security-recommendations.md)
