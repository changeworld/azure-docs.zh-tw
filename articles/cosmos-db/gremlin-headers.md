---
title: Azure 宇宙 DB 格雷姆林回應標頭
description: 支援其他故障排除的伺服器回應中繼資料參考文檔
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755087"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure 宇宙 DB 格雷姆林伺服器回應標頭
本文涵蓋 Cosmos DB Gremlin 伺服器在要求執行時傳回給呼叫者的標頭。 這些標頭有助於針對要求效能進行疑難排解、建立與 Cosmos DB 服務原生整合的應用程式，以及簡化客戶支援。

請記住，依賴這些標頭，即將應用程式的可攜性限制為其他 Gremlin 實現。 作為回報，您正在與 Cosmos DB Gremlin 進行更緊密的集成。 這些標頭不是 TinkerPop 標準。

## <a name="headers"></a>headers

| 頁首 | 類型 | 範例值 | 包含時 | 說明 |
| --- | --- | --- | --- | --- |
| **x ms-請求-充電** | double | 11.3243 | 成功和失敗 | 部分回應訊息[的請求單位（RU/s 或 RU）](request-units.md)中消耗的集合或資料庫輸送量。 對於具有多個區塊的請求，此標頭存在於每個延續中。 它反映了特定回應塊的電荷。 僅對於由單個回應塊組成的請求，此標頭與遍歷總成本相匹配。 但是，對於大多數複雜的遍歷，此值表示部分成本。 |
| **x-ms-總請求-充電** | double | 423.987 | 成功和失敗 | 請求[單位（RU/s 或 RU）](request-units.md)中在整個請求中消耗的集合或資料庫輸送量。 對於具有多個區塊的請求，此標頭存在於每個延續中。 它指示自請求開始以來的累計費用。 最後一個塊中此標頭的值表示完整的請求費用。 |
| **x-ms-伺服器-時間-ms** | double | 13.75 | 成功和失敗 | 此標頭包含在進行延遲故障排除中。 它指示 Cosmos DB Gremlin 伺服器執行並生成部分回應訊息所花費的時間量（以毫秒為單位）。 使用此標頭的值並將其與整體請求延遲應用程式進行比較可以計算網路延遲開銷。 |
| **x-ms-總伺服器-時間-ms** | double | 130.512 | 成功和失敗 | Cosmos DB Gremlin 伺服器執行整個遍歷所花的總時間（以毫秒為單位）。 此標頭包含在每個部分回應中。 它表示自請求開始以來的累積執行時間。 最後一個回應指示總執行時間。 此標頭可用於區分用戶端和伺服器作為延遲源。 您可以將用戶端上的遍歷執行時間與此標頭的值進行比較。 |
| **x-ms 狀態碼** | long | 200 | 成功和失敗 | 標頭指示請求完成或終止的內部原因。 建議應用程式查看此標頭的值並採取糾正措施。 |
| **x-ms-子狀態碼** | long | 1003 | 僅失敗 | Cosmos DB 是一個多模型資料庫，構建在統一存儲層之上。 此標頭包含有關在高可用性堆疊下層中發生故障時故障原因的其他見解。 建議應用程式存儲此標頭，並在聯繫 Cosmos DB 客戶支援時使用它。 此標頭的值對於 Cosmos DB 工程師的快速故障排除非常有用。 |
| **x-ms-重新重試後 ms** | 字串（時間跨度） | "00:00:03.9500000" | 僅失敗 | 此標頭是 .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan)類型的字串表示形式。 此值將僅包含在請求失敗時，由於預配輸送量耗盡。 申請應在指示的時間段後再次重新提交遍歷。 |
| **x-ms-活動-id** | 字串（吉德） | "A9218E01-3A3A-4716-9636-5BD86B056613" | 成功和失敗 | 標頭包含請求的唯一伺服器端識別碼。 伺服器為每個請求分配了一個唯一識別碼，用於跟蹤目的。 應用程式應記錄伺服器返回的活動識別碼，以便記錄客戶可能希望聯繫客戶支援的請求。 Cosmos DB 支援人員可以在 Cosmos DB 服務遙測中通過這些識別碼查找特定請求。 |

## <a name="status-codes"></a>狀態碼

下面列出了伺服器返回的最常見狀態碼。

| 狀態 | 說明 |
| --- | --- |
| **401** | 當身份驗證`"Unauthorized: Invalid credentials provided"`密碼與 Cosmos DB 帳戶金鑰不匹配時，將返回錯誤訊息。 導航到 Azure 門戶中的 Cosmos DB Gremlin 帳戶，確認金鑰正確。|
| **404** | 嘗試同時刪除和更新同一邊或頂點的併發操作。 錯誤訊息 `"Owner resource does not exist"` 表示在 `/dbs/<database name>/colls/<collection or graph name>` 格式的連線參數中，指定的資料庫或集合不正確。|
| **408** | `"Server timeout"`表示遍歷時間超過**30 秒**，伺服器已取消。 通過篩選遍歷躍點的頂點或邊以縮小搜尋範圍，優化遍歷以快速運行。|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."`通常，當頂點或邊緣的識別碼已存在圖形中時，就會發生這種情形。| 
| **412** | 狀態碼與錯誤訊息`"PreconditionFailedException": One of the specified pre-condition is not met`相補充。 此錯誤表示讀取邊或頂點之間存在樂觀的併發控制衝突，並在修改後將其寫回存儲。 發生此錯誤的最常見情況是屬性修改，例如`g.V('identifier').property('name','value')`。 Gremlin 引擎將讀取頂點，修改它，並將其寫回。 如果另一個遍歷並行運行，嘗試寫入相同的頂點或邊，其中一個將收到此錯誤。 應用程式應再次向伺服器提交遍歷。| 
| **429** | 要求已節流處理，並且應該在 **x-ms-retry-after-ms** 的值之後重試| 
| **500** | 包含 `"NotFoundException: Entity with the specified id does not exist in the system."` 的錯誤訊息表示資料庫和/或集合使用了相同的名稱來重新建立。 此錯誤會隨著的傳播而在 5 分鐘內消失，並因此讓不同 Cosmos DB 元件中的快取失效。 若要避免此問題，每次請都使用唯一的資料庫和集合名稱。| 
| **1000** | 當伺服器成功解析消息但無法執行時，將返回此狀態碼。 它通常指示查詢有問題。| 
| **1001** | 當伺服器完成遍歷執行但無法序列化回應回用戶端時，將返回此代碼。 當遍歷生成複雜結果、太大或不符合 TinkerPop 協定規範時，可能會發生此錯誤。 當應用程式遇到此錯誤時，應簡化遍歷。 | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`當遍歷超過允許的記憶體限制時返回。 記憶體限制為每遍歷**2 GB。**| 
| **1004** | 此狀態碼指示格式錯誤的圖形請求。 請求在反序列化失敗、非數值型別被反序列化為數值型別或請求的不支援的 gremlin 操作時可能格式錯誤。 應用程式不應重試請求，因為它不會成功。 | 
| **1007** | 通常，此狀態碼返回時帶有錯誤訊息`"Could not process request. Underlying connection has been closed."`。 如果用戶端驅動程式嘗試使用伺服器正在關閉的連接，則可能發生此情況。 應用程式應重試其他連接上的遍歷。
| **1008** | Cosmos DB Gremlin 伺服器可以終止連接以重新平衡群集中的流量。 用戶端驅動程式應處理這種情況，並且僅使用即時連接向伺服器發送請求。 有時，用戶端驅動程式可能無法檢測到連接已關閉。 當應用程式遇到錯誤時，`"Connection is too busy. Please retry after sometime or open more connections."`它應該重試其他連接上的遍歷。

## <a name="samples"></a>範例

基於讀取一個狀態屬性Gremlin.Net的示例用戶端應用程式：

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

演示如何從 Gremlin java 用戶端讀取狀態屬性的示例：

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
* [Azure 宇宙 DB 的 HTTP 狀態碼](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [常見 Azure 宇宙 DB REST 回應標頭](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop 圖形驅動程式提供程式要求]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
