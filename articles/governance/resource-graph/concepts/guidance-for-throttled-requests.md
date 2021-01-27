---
title: 節流要求指引
description: 了解如何以平行方式分組、錯開、編頁和查詢，以避免要求受到 Azure Resource Graph 的節流。
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd3cf4d411733e831c94039c3bc9aeaf0e95271
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917702"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure Resource Graph 中的節流要求指導方針

建立 Azure Resource Graph 資料的程式設計和頻繁使用時，應該考慮節流如何影響查詢結果。 變更要求資料的方式，可協助您和您的組織避免受到節流，並維護 Azure 資源的即時資料流量。

本文涵蓋了與在 Azure Resource Graph 中建立查詢相關的四個領域和模式：

- 了解節流標頭
- 群組查詢
- 錯開查詢
- 編頁的影響

## <a name="understand-throttling-headers"></a>了解節流標頭

Azure Resource Graph 會根據時間範圍，為每個使用者配置配額編號。 例如，使用者在每 5 秒的時間範圍內，最多可以傳送 15 個查詢而不會進行節流。 配額值是由許多因素所決定，而且可能會變更。

在每個查詢回應中，Azure Resource Graph 都會加入兩個節流標頭：

- `x-ms-user-quota-remaining` (int)：使用者的剩餘資源配額。 此值會對應至查詢計數。
- `x-ms-user-quota-resets-after` (hh:mm:ss)：使用者配額耗用量重設之前的剩餘時間長度。

當安全性主體可存取租使用者或管理群組 [查詢範圍](./query-language.md#query-scope)內超過5000的訂用帳戶時，回應會限制為第一個5000訂用帳戶，而且 `x-ms-tenant-subscription-limit-hit` 標頭會傳回為 `true` 。

為了說明標頭的作用，讓我們看一下具有標頭和值 `x-ms-user-quota-remaining: 10` 和 `x-ms-user-quota-resets-after: 00:00:03` 的查詢回應。

- 在接下來的 3 秒內，最多可以提交 10 個查詢，而不會進行節流。
- 在 3 秒內，`x-ms-user-quota-remaining` 和 `x-ms-user-quota-resets-after` 的值會分別重設為 `15` 和 `00:00:05`。

若要查看在查詢要求中使用標頭進行輪詢的範例，請參閱[以平行方式查詢](#query-in-parallel)中的範例。

## <a name="grouping-queries"></a>群組查詢

依訂用帳戶、資源群組或個別資源來分組查詢，會比平行處理查詢更有效率。 較大查詢的配額成本通常小於許多小型和目標查詢的配額成本。 建議群組大小小於 _300_。

- 最佳化方法不佳的範例

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

- 最佳化群組方法的範例 #1

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

- 在單一查詢中取得多個資源的最佳化群組方法範例 #2

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

由於強制執行節流的方式，因此建議您將查詢錯開。 也就是說，不會同時傳送 60 個查詢，而是將查詢錯開成四個 5 秒的窗口：

- 非交錯查詢排程

  | 查詢計數         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 時間間隔 (秒) | 0-5 | 5-10 | 10-15 | 15-20 |

- 交錯查詢排程

  | 查詢計數         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 時間間隔 (秒) | 0-5 | 5-10 | 10-15 | 15-20 |

以下是查詢 Azure Resource Graph 時遵循節流標頭的範例：

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

### <a name="query-in-parallel"></a>以平行方式查詢

雖然建議在平行處理時使用群組，但有時候查詢無法輕鬆地分組。 在這些情況下，您可能會想要以平行方式傳送多個查詢來查詢 Azure Resource Graph。 以下是如何根據這類案例中的節流標 _頭進行輪詢_ 的範例：

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

因為 Azure Resource Graph 在單一查詢回應中最多傳回 1000 個項目，所以您可能需要[編頁](./work-with-data.md#paging-results)查詢，以取得您要尋找的完整資料集。 不過，某些 Azure Resource Graph 的用戶端處理編頁的方式與其他用戶端不同。

- C# SDK

  使用 ResourceGraph SDK 時，您必須將先前查詢回應所傳回的跳過權杖傳遞至下一個編頁查詢，以處理編頁。 這種設計表示您需要從所有編頁呼叫中收集結果，並將其結合在一起。 在此情況下，您傳送的每個編頁查詢都會採用一個查詢配額：

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

- Azure CLI / Azure PowerShell

  使用 Azure CLI 或 Azure PowerShell 時，將自動對 Azure Resource Graph 的查詢進行編頁，以擷取最多 5000 個項目。 查詢結果會從所有編頁呼叫傳回結合的項目清單。 在此情況下，視查詢結果中的項目數目而定，單一編頁查詢可能會耗用一個以上的查詢配額。 例如，在下列範例中，單一執行查詢最多可能耗用五個查詢配額：

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>還是受到節流？

如果您在執行上述建議後受到節流，請與團隊聯繫：[resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)。

提供下列詳細資料：

- 您的特定使用案例和商務驅動程式需要更高的節流限制。
- 您有多少資源可以存取？ 有多少資源會從單一查詢傳回？
- 您感興趣的資源類型為何？
- 您的查詢模式是什麼？ 每 Y 秒 X 個查詢，依此類推。

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言。
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式。
- 深入了解如何[探索資源](explore-resources.md)。