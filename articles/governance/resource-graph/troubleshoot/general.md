---
title: 常見問題疑難排解
description: 瞭解如何在使用 Azure 資源圖查詢 Azure 資源時解決各種 SDK 的問題。
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303909"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>使用 Azure 資源圖排除錯誤

使用 Azure 資源圖查詢 Azure 資源時可能會遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

大多數錯誤都是使用 Azure 資源圖執行查詢時出現問題的結果。 當查詢失敗時，SDK 提供有關失敗查詢的詳細資訊。 此資訊指示問題，以便可以修復該問題，並且以後的查詢成功。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>方案：訂閱太多

#### <a name="issue"></a>問題

有權訪問 1000 多個訂閱（包括使用[Azure 燈塔](../../../lighthouse/overview.md)的跨租戶訂閱）的客戶無法在單個調用 Azure 資源圖時跨所有訂閱獲取資料。

#### <a name="cause"></a>原因

Azure CLI 和 PowerShell 僅轉發到 Azure 資源圖的前 1000 個訂閱。 Azure 資源圖的 REST API 接受要執行查詢的最大訂閱數。

#### <a name="resolution"></a>解決方案

具有訂閱子集的查詢的批次處理請求將保持在 1000 訂閱限制之下。 解決方案使用 PowerShell 中的**訂閱**參數。

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>方案：不支援的內容類型 REST 標頭

#### <a name="issue"></a>問題

查詢 Azure 資源圖 REST API 的客戶將獲得返回_的 500（_ 內部伺服器錯誤）回應。

#### <a name="cause"></a>原因

Azure 資源圖 REST API`Content-Type`僅支援**應用程式/json**。 某些 REST 工具或代理預設為**文本/純**，這是 REST API 不支援的。

#### <a name="resolution"></a>解決方案

驗證用於查詢 Azure 資源圖的工具或代理是否為**應用程式/json**配置`Content-Type`REST API 標頭。

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>方案：對清單中的所有訂閱沒有讀取權限

#### <a name="issue"></a>問題

使用 Azure 資源圖查詢顯式傳遞訂閱清單的客戶將獲得_403（_ 禁止）回應。

#### <a name="cause"></a>原因

如果客戶沒有對所有提供的訂閱的讀取權限，則請求將因缺少適當的擔保權而被拒絕。

#### <a name="resolution"></a>解決方案

在訂閱清單中至少包括一個訂閱，執行查詢的客戶至少具有讀取存取許可權。 有關詳細資訊，請參閱[Azure 資源圖中的許可權](../overview.md#permissions-in-azure-resource-graph)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
- 與[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帳戶連接，通過將 Azure 社區連接到正確的資源（答案、支援和專家），從而改善客戶體驗。
- 如果需要更多協助，您可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。