---
title: 常見問題疑難排解
description: 瞭解如何在使用 Azure Resource Graph 查詢 Azure 資源時，針對各種 Sdk 的問題進行疑難排解。
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74303909"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>使用 Azure Resource Graph 疑難排解錯誤

使用 Azure Resource Graph 查詢 Azure 資源時，可能會遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

大部分的錯誤是使用 Azure Resource Graph 執行查詢時的問題所造成。 當查詢失敗時，SDK 會提供有關失敗查詢的詳細資料。 這項資訊指出問題，讓它可以修正，而稍後的查詢會成功。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>案例：訂閱太多

#### <a name="issue"></a>問題

存取超過1000個訂用帳戶（包括[Azure 燈塔](../../../lighthouse/overview.md)的跨租使用者訂用帳戶）的客戶，在 Azure Resource Graph 的單一呼叫中，無法跨所有訂用帳戶提取資料。

#### <a name="cause"></a>原因

Azure CLI 和 PowerShell 只轉送前1000個訂用帳戶，以 Azure Resource Graph。 Azure Resource Graph 的 REST API 會接受執行查詢的最大訂閱數。

#### <a name="resolution"></a>解決方法

具有訂用帳戶子集的查詢批次要求會保留在1000訂閱限制之下。 解決方案會使用 PowerShell 中的**訂**用帳戶參數。

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>案例：不支援的內容類型 REST 標頭

#### <a name="issue"></a>問題

查詢 Azure Resource Graph REST API 收到傳回_500_ （內部伺服器錯誤）回應的客戶。

#### <a name="cause"></a>原因

Azure Resource Graph REST API 僅支援`Content-Type` **application/json**的。 某些 REST 工具或代理程式預設為**text/純文字**，REST API 不支援。

#### <a name="resolution"></a>解決方法

驗證您用來查詢 Azure Resource Graph 的工具或代理程式是否已為`Content-Type` **application/json**設定 REST API 標頭。

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>案例：沒有清單中所有訂用帳戶的讀取權限

#### <a name="issue"></a>問題

使用 Azure Resource Graph 查詢明確傳遞訂用帳戶清單的客戶會收到_403_ （禁止）回應。

#### <a name="cause"></a>原因

如果客戶沒有所有提供之訂用帳戶的讀取權限，則要求會因為缺乏適當的安全性許可權而遭到拒絕。

#### <a name="resolution"></a>解決方法

在訂用帳戶清單中至少包含一個訂用帳戶，而執行查詢的客戶至少具有的讀取存取權。 如需詳細資訊，請參閱[Azure Resource Graph 中的許可權](../overview.md#permissions-in-azure-resource-graph)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
- Connect [@AzureSupport](https://twitter.com/azuresupport) –官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。
- 如果需要更多協助，您可以提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。