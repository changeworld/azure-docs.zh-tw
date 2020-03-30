---
title: 節流要求指引
description: 瞭解如何並行分組、交錯、分頁和查詢，以避免請求被 Azure 資源圖限制。
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259847"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure 資源圖中限制請求的指導

在創建 Azure 資源圖資料的程式設計和頻繁使用時，應考慮限制如何影響查詢的結果。 變更要求資料的方式可以説明您和您的組織避免被限制，並維護有關 Azure 資源的及時資料流程。

本文介紹與在 Azure 資源圖中創建查詢相關的四個領域和模式：

- 了解節流標頭
- 分組查詢
- 錯開查詢
- 分形的影響

## <a name="understand-throttling-headers"></a>了解節流標頭

Azure 資源圖根據時間視窗為每個使用者分配配額編號。 例如，使用者每 5 秒的視窗內最多可以發送 15 個查詢，而不會受到限制。 配額值由許多因素決定，並可能發生變化。

在每個查詢回應中，Azure 資源圖都會添加兩個限制標頭：

- `x-ms-user-quota-remaining`（int）：使用者的剩餘資源配額。 此值會對應至查詢計數。
- `x-ms-user-quota-resets-after`（hh：mm：ss）：重置使用者配額消耗的持續時間。

為了說明標頭的工作原理，讓我們看一個查詢回應，該回應具有 和`x-ms-user-quota-remaining: 10``x-ms-user-quota-resets-after: 00:00:03`的標頭和值。

- 在接下來的 3 秒內，最多可能提交 10 個查詢而不受到限制。
- 在 3 秒內，和`x-ms-user-quota-remaining``x-ms-user-quota-resets-after`的值將分別重置`15`為`00:00:05`和。

要查看使用標頭對查詢請求_進行回退_的示例，請參閱["並行"中的"查詢"中](#query-in-parallel)的示例。

## <a name="grouping-queries"></a>分組查詢

按訂閱、資源組或單個資源對查詢進行分組比並行化查詢更有效。 較大查詢的配額成本通常低於許多小型和目標查詢的配額成本。 建議組大小小於_300_。

- 優化方法不足的示例

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 優化分組方法的示例#1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 用於在一個查詢中獲取多個資源的優化分組方法#2示例

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>錯開查詢

由於強制限制的方式，我們建議錯開查詢。 也就是說，不要同時發送 60 個查詢，而是將查詢交錯到四個 5 秒的視窗中：

- 非錯錯查詢計劃

  | Query Count         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 時間間隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

- 交錯查詢計劃

  | Query Count         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 時間間隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

下面是查詢 Azure 資源圖時尊重限制標頭的示例：

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>並行查詢

即使建議通過並行化進行分組，有時查詢無法輕鬆分組。 在這些情況下，您可能希望通過並行發送多個查詢來查詢 Azure 資源圖。 下面是在這種情況下如何根據限制標頭_進行回退_的示例：

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>分頁

由於 Azure 資源圖在單個查詢回應中最多返回 1000 個條目，因此您可能需要[對查詢進行分頁](./work-with-data.md#paging-results)以獲取要查找的完整資料集。 但是，某些 Azure 資源圖用戶端處理分頁的方式與其他用戶端不同。

- C# SDK

  使用 ResourceGraph SDK 時，您需要通過將從上一個查詢回應返回的跳過權杖傳遞到下一個分頁查詢來處理分頁。 此設計意味著您需要從所有分頁調用中收集結果，並在末尾將它們組合在一起。 在這種情況下，您發送的每個分頁查詢需要一個查詢配額：

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI / Azure 電源外殼

  使用 Azure CLI 或 Azure PowerShell 時，對 Azure 資源圖的查詢將自動分頁，最多獲取 5000 個條目。 查詢結果返回來自所有分頁調用的條目的組合清單。 在這種情況下，根據查詢結果中的條目數，單個分頁查詢可能會消耗多個查詢配額。 例如，在下面的示例中，查詢的單個運行可能最多消耗五個查詢配額：

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>還是被限制？

如果您在執行上述建議後受到限制，請與 團隊聯繫[resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)。

提供以下詳細資訊：

- 您的特定用例和業務驅策因素需要更高的限制限制。
- 您有權訪問多少資源？ 有多少是從單個查詢返回的？
- 您感興趣的資源類型是什麼？
- 您的查詢模式是什麼？ X 查詢每 Y 秒等。

## <a name="next-steps"></a>後續步驟

- 請參閱[初學者查詢](../samples/starter.md)中正在使用的語言。
- 請參閱[高級查詢](../samples/advanced.md)中的高級用途。
- 深入了解如何[探索資源](explore-resources.md)。