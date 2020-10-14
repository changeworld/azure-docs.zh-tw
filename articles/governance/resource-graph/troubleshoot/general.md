---
title: 常見問題疑難排解
description: 瞭解如何在使用 Azure Resource Graph 查詢 Azure 資源時，針對各種 SDK 的問題進行疑難排解。
ms.date: 10/14/2020
ms.topic: troubleshooting
ms.openlocfilehash: 13c5d5ffde8b0b82fcafa5e8149400555a0b18a6
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056952"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>針對使用 Azure Resource Graph 發生的錯誤進行疑難排解

使用 Azure Resource Graph 查詢 Azure 資源時，可能會遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

大部分的錯誤是使用 Azure Resource Graph 執行查詢時的問題所造成。 查詢失敗時，SDK 會提供有關失敗查詢的詳細資料。 此資訊會指出問題，以便能夠進行修正，而使後續查詢能成功。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>案例：節流的要求

#### <a name="issue"></a>問題

進行大型或經常進行資源查詢的客戶要求節流。

#### <a name="cause"></a>原因

Azure Resource Graph 會根據時間範圍，為每個使用者配置配額數字。 例如，使用者在每 5 秒的時間範圍內，最多可以傳送 15 個查詢而不會進行節流。 配額值是由許多因素所決定，而且可能會變更。 如需詳細資訊，請參閱 [Azure Resource Graph 中的節流](../overview.md#throttling)。

#### <a name="resolution"></a>解決方案

有數種方法可以處理節流要求：

- [群組查詢](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [錯開查詢](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [以平行方式查詢](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [分頁](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>案例：太多訂閱

#### <a name="issue"></a>問題

具有超過 1000 個訂閱存取權的客戶 (包括具有 [Azure Lighthouse](../../../lighthouse/overview.md) 的跨租使用者訂閱)，在 Azure Resource Graph 的單一呼叫中，無法跨所有訂閱提取資料。

#### <a name="cause"></a>原因

Azure CLI 和 PowerShell 只會將前 1000 個訂閱轉送至 Azure Resource Graph。 Azure Resource Graph 的 REST API 會接受執行查詢的最大訂閱數。

#### <a name="resolution"></a>解決方案

具有訂閱子集的查詢批次要求會受限於 1000 個訂閱限制以下。 解決方案是使用 PowerShell 中的**訂閱**參數。

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>案例：不支援的內容類型 REST 標題

#### <a name="issue"></a>問題

客戶在查詢 Azure Resource Graph REST API 時傳回 _500_ (內部伺服器錯誤) 的回應。

#### <a name="cause"></a>原因

Azure Resource Graph REST API 僅支援 **application/json** 的 `Content-Type`。 某些 REST 工具或代理程式預設為 **text/plain**，REST API 不支援這種方式。

#### <a name="resolution"></a>解決方案

驗證您用來查詢 Azure Resource Graph 的工具或代理程式是否已針對 **application/json**設定 REST API 標頭 `Content-Type`。

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>案例：沒有清單中所有訂閱的讀取權限

#### <a name="issue"></a>問題

客戶在使用 Azure Resource Graph 查詢明確傳遞的訂閱清單時，會得到 _403_ (禁止) 回應。

#### <a name="cause"></a>原因

如果客戶對所有提供訂閱皆未具有讀取權限，要求就會遭到拒絕，因為缺乏適當的安全性權限。

#### <a name="resolution"></a>解決方案

在訂閱清單中至少包含一個執行查詢的客戶至少具有讀取存取權的訂閱。 如需詳細資訊，請參閱 [Azure Resource Graph 中的權限](../overview.md#permissions-in-azure-resource-graph)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
- 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
- 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。