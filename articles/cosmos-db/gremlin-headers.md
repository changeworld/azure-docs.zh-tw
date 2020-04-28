---
title: Azure Cosmos DB Gremlin 回應標頭
description: 伺服器回應中繼資料的參考檔，可進行其他疑難排解
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "72755087"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin 伺服器回應標頭
本文涵蓋 Cosmos DB Gremlin 伺服器在要求執行時傳回給呼叫者的標頭。 這些標頭有助於針對要求效能進行疑難排解、建立與 Cosmos DB 服務原生整合的應用程式，以及簡化客戶支援。

請記住，您可以將應用程式的可攜性限制為其他 Gremlin 的執行方式，以依賴這些標頭。 傳回時，您會更緊密地與 Cosmos DB Gremlin 整合。 這些標頭不是 TinkerPop 標準。

## <a name="headers"></a>headers

| 頁首 | 類型 | 範例值 | 包含時 | 說明 |
| --- | --- | --- | --- | --- |
| **x-ms-要求-費用** | double | 11.3243 | 成功和失敗 | 部分回應訊息的[要求單位（RU/秒或 RU）](request-units.md)所耗用的集合或資料庫輸送量數量。 在具有多個區塊的要求的每個接續中，都會出現這個標頭。 它會反映特定回應區塊的費用。 僅針對由單一回應區塊組成的要求，此標頭會符合總的遍歷成本。 不過，對於大部分的複雜周遊，此值代表部分成本。 |
| **x-毫秒-總要求費用** | double | 423.987 | 成功和失敗 | 整個要求的[要求單位（RU/秒）](request-units.md)所耗用的集合或資料庫輸送量數量。 在具有多個區塊的要求的每個接續中，都會出現這個標頭。 這表示自要求開始後的累積費用。 在最後一個區塊中，此標頭的值表示完整的要求費用。 |
| **x-ms-伺服器-時間-毫秒** | double | 13.75 | 成功和失敗 | 此標頭包含在延遲疑難排解的目的中。 它會指出 Cosmos DB Gremlin 伺服器執行和產生部分回應訊息所花費的時間量（以毫秒為單位）。 使用此標頭的值，並與整體要求延遲應用程式比較，可以計算網路延遲的額外負荷。 |
| **x-ms-總-伺服器-時間-毫秒** | double | 130.512 | 成功和失敗 | Cosmos DB Gremlin 伺服器執行整個遍歷所花費的總時間（以毫秒為單位）。 這個標頭會包含在每個部分回應中。 它代表自要求開始後的累計執行時間。 最後一個回應會指出總執行時間。 此標頭有助於將用戶端和伺服器區別為延遲的來源。 您可以比較用戶端上的「遍歷執行時間」與此標頭的值。 |
| **x-ms-狀態-代碼** | long | 200 | 成功和失敗 | 標頭表示要求完成或終止的內部原因。 建議使用應用程式查看此標頭的值，並採取更正動作。 |
| **x-ms-子狀態碼** | long | 1003 | 僅失敗 | Cosmos DB 是建立在統一儲存層之上的多模型資料庫。 此標頭包含在高可用性堆疊的較低層級中發生失敗時，有關失敗原因的其他見解。 建議應用程式儲存此標頭，並在聯繫 Cosmos DB 客戶支援服務時使用。 此標頭的值有助於 Cosmos DB 工程師快速進行疑難排解。 |
| **x-ms-重試-毫秒** | 字串（TimeSpan） | "00：00： 03.9500000" | 僅失敗 | 此標頭是 .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan)類型的字串標記法。 此值只會包含在要求失敗，因為布建的輸送量耗盡。 應用程式應該在經過指示的一段時間之後再次重新提交遍歷。 |
| **x-ms-活動-識別碼** | 字串（Guid） | "A9218E01-3A3A-4716-9636-5BD86B056613" | 成功和失敗 | 標頭包含要求的唯一伺服器端識別碼。 伺服器會為每個要求指派一個唯一識別碼，以供追蹤之用。 應用程式應記錄伺服器傳回的活動識別碼，以尋找客戶可能想要與客戶支援人員聯繫的要求。 Cosmos DB 支援人員可以在 Cosmos DB 服務遙測中找到這些識別碼的特定要求。 |

## <a name="status-codes"></a>狀態碼

伺服器傳回的最常見狀態碼如下所示。

| 狀態 | 說明 |
| --- | --- |
| **401** | 當驗證`"Unauthorized: Invalid credentials provided"`密碼不符合 Cosmos DB 帳戶金鑰時，就會傳回錯誤訊息。 在 Azure 入口網站中流覽至您的 Cosmos DB Gremlin 帳戶，並確認金鑰正確。|
| **404** | 同時嘗試刪除和更新相同邊緣或頂點的並行作業。 錯誤訊息 `"Owner resource does not exist"` 表示在 `/dbs/<database name>/colls/<collection or graph name>` 格式的連線參數中，指定的資料庫或集合不正確。|
| **408** | `"Server timeout"`指出遍歷花費超過**30 秒**，而且已由伺服器取消。 藉由篩選每個回合躍點上的頂點或邊緣，將您的周遊優化以快速執行，以縮小搜尋範圍。|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."`通常，當頂點或邊緣的識別碼已存在圖形中時，就會發生這種情形。| 
| **412** | 狀態碼會加上錯誤訊息`"PreconditionFailedException": One of the specified pre-condition is not met`的補充。 此錯誤表示在讀取邊緣或頂點，並在修改後將其寫回存放區時，開放式並行存取控制違規。 例如`g.V('identifier').property('name','value')`，在發生此錯誤時，最常見的情況是修改屬性。 Gremlin 引擎會讀取頂點、修改它，並將它寫回。 如果在嘗試寫入相同的頂點或邊緣時，有另一個執行中的遍歷正在執行，其中一個會收到這個錯誤。 應用程式應該再次將遍歷提交至伺服器。| 
| **429** | 要求已節流處理，並且應該在 **x-ms-retry-after-ms** 的值之後重試| 
| **500** | 包含 `"NotFoundException: Entity with the specified id does not exist in the system."` 的錯誤訊息表示資料庫和/或集合使用了相同的名稱來重新建立。 此錯誤會隨著的傳播而在 5 分鐘內消失，並因此讓不同 Cosmos DB 元件中的快取失效。 若要避免此問題，每次請都使用唯一的資料庫和集合名稱。| 
| **1000** | 當伺服器成功剖析訊息但無法執行時，就會傳回此狀態碼。 這通常表示查詢發生問題。| 
| **1001** | 當伺服器完成執行遍歷，但無法將回應序列化回用戶端時，就會傳回此程式碼。 當遍歷產生複雜的結果，但太大或不符合 TinkerPop 通訊協定規格時，就會發生此錯誤。 應用程式應該在遇到此錯誤時，簡化遍歷。 | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`當遍歷超過允許的記憶體限制時，會傳回。 記憶體限制為每個遍歷**2 GB** 。| 
| **1004** | 此狀態碼表示圖形要求格式不正確。 當還原序列化失敗時，要求的格式可能不正確、非實數值型別正在還原序列化為實數值型別，或要求的 gremlin 作業不受支援。 應用程式不應該重試要求，因為它不會成功。 | 
| **1007** | 通常會傳回此狀態碼，並出現`"Could not process request. Underlying connection has been closed."`錯誤訊息。 如果用戶端驅動程式嘗試使用伺服器所關閉的連接，就會發生這種情況。 應用程式應該在不同的連接上重試遍歷。
| **1008** | Cosmos DB Gremlin 伺服器可以終止連線，以重新平衡叢集中的流量。 用戶端驅動程式應該處理這種情況，而且只使用即時連線將要求傳送至伺服器。 有時候用戶端驅動程式可能不會偵測到該連線已關閉。 當應用程式遇到錯誤時`"Connection is too busy. Please retry after sometime or open more connections."` ，應該在不同的連接上重試遍歷。

## <a name="samples"></a>範例

以 Gremlin.Net 為基礎的範例用戶端應用程式，它會讀取一個狀態屬性：

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

示範如何從 Gremlin java 用戶端讀取狀態屬性的範例：

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>後續步驟
* [Azure Cosmos DB 的 HTTP 狀態碼](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [一般 Azure Cosmos DB REST 回應標頭](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop Graph 驅動程式提供者需求]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
