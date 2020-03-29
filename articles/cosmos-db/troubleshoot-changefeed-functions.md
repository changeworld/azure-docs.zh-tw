---
title: 在為 Cosmos DB 使用 Azure 函數觸發器時排除問題
description: 在使用 Cosmos DB 的 Azure 函數觸發器時，常見問題、解決方法和診斷步驟
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365503"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>在為 Cosmos DB 使用 Azure 函數觸發器時診斷和排除問題

本文介紹在使用 Cosmos DB 的[Azure 函數觸發器](change-feed-functions.md)時的常見問題、解決方法和診斷步驟。

## <a name="dependencies"></a>相依性

Cosmos DB 的 Azure 函數觸發器和綁定取決於基本 Azure 函數運行時的擴展包。 始終保持這些包的更新，因為它們可能包含可能解決您可能會遇到的任何潛在問題的修補程式和新功能：

* 有關 Azure 函數 V2，請參閱[Microsoft.Azure.Web 作業.擴展.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。
* 有關 Azure 函數 V1，請參閱[Microsoft.Azure.Web 作業.擴展.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)。

除非顯式指定，否則每當提到運行時時，本文將始終引用 Azure 函數 V2。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>獨立使用 Azure 宇宙 DB SDK

擴展包的關鍵功能是為 Cosmos DB 提供 Azure 函數觸發器和綁定的支援。 它還包括[Azure Cosmos DB .NET SDK，](sql-api-sdk-dotnet-core.md)如果要在不使用觸發器和綁定的情況下以程式設計方式與 Azure Cosmos DB 進行交互，這非常有用。

如果要使用 Azure Cosmos DB SDK，請確保不要向專案添加另一個 NuGet 包引用。 相反，**讓 SDK 引用通過 Azure 函數的擴展包解析**。 將 Azure 宇宙 DB SDK 與觸發器和綁定分開使用

此外，如果要手動創建[Azure Cosmos DB SDK 用戶端](./sql-api-sdk-dotnet-core.md)的實例，則應遵循使用[Singleton 模式方法](../azure-functions/manage-connections.md#documentclient-code-example-c)僅具有一個用戶端實例的模式。 此過程將避免操作中的潛在通訊端問題。

## <a name="common-scenarios-and-workarounds"></a>常見方案和解決方法

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure 函數失敗，錯誤訊息集合不存在

Azure 函數失敗，錯誤訊息"源集合'集合名稱'（在資料庫'資料庫名稱'中）或租約集合'集合2名稱'（在資料庫'資料庫2名稱'中）不存在。 在攔截器開始之前，兩個集合都必須存在。 要自動創建租約集合，請將"創建租約集合"設置為"true"

這意味著觸發器工作所需的一個或兩個 Azure Cosmos 容器不存在或無法訪問 Azure 函數。 **錯誤本身將告訴您哪個 Azure Cosmos 資料庫和容器是**基於您的配置查找的觸發器。

1. 驗證該`ConnectionStringSetting`屬性，並引用**Azure 函數應用中存在的設置**。 此屬性上的值不應是連接字串本身，而是配置設置的名稱。
2. 驗證 Azure `databaseName` `collectionName` Cosmos 帳戶中是否存在 的 。 如果使用自動值替換（使用`%settingName%`模式），請確保設置的名稱存在於 Azure 函數應用中。
3. 如果不指定 ，`LeaseCollectionName/leaseCollectionName`則預設值為"租約"。 驗證此類容器是否存在。 或者，您可以在觸發器中設置`CreateLeaseCollectionIfNotExists`屬性以`true`自動創建它。
4. 驗證[Azure Cosmos 帳戶的防火牆配置](how-to-configure-firewall.md)，以查看它不是阻止 Azure 函數。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 函數無法從"共用輸送量集合應具有分區鍵"開頭

Azure Cosmos DB 擴展的早期版本不支援使用在[共用輸送量資料庫中](./set-throughput.md#set-throughput-on-a-database)創建的租約容器。 要解決此問題，請更新[Microsoft.Azure.WebJobs.擴展.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)擴展，以獲得最新版本。

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure 函數無法以"必須為此操作提供分區金鑰"開頭。

此錯誤意味著您當前使用的是具有舊[擴展依賴項](#dependencies)的分區租約集合。 升級到最新版本。 如果當前在 Azure 函數 V1 上運行，則需要升級到 Azure 函數 V2。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 函數無法以"如果分區，必須具有等於 id 的分區金鑰"開頭。

此錯誤表示當前租約容器已分區，但分區金鑰路徑不是`/id`。 要解決此問題，您需要重新創建作為`/id`分區鍵的租約容器。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>您將看到"值不能為空。 嘗試運行觸發器時，Azure 函數日誌中的參數名稱：o"

如果使用 Azure 門戶，並且在檢查使用觸發器的 Azure 函數時嘗試選擇螢幕上的 **"運行**"按鈕，則會出現此問題。 觸發器不需要選擇"運行"才能啟動，它將在部署 Azure 函數時自動啟動。 如果要在 Azure 門戶上檢查 Azure 函數的日誌流，只需轉到受監視的容器並插入一些新專案，將自動看到觸發器執行。

### <a name="my-changes-take-too-long-to-be-received"></a>我的更改需要很長時間才能收到

此方案可以有多個原因，並且應檢查所有這些原因：

1. Azure 函式是否部署在與 Azure Cosmos 帳戶相同的區域中？ 為了獲得理想的網路延遲時間，Azure 函式和 Azure Cosmos 帳戶應該共存於相同 Azure 區域。
2. Azure Cosmos 容器中的變更是持續發生還是偶爾發生？
如果是偶爾發生，則在儲存變更與 Azure 函式取得變更之間會有一些延遲。 這是因為在內部，當觸發程序檢查 Azure Cosmos 容器中的變更卻發現沒有任何擱置待讀取的變更時，其便會進入睡眠時間 (時間長短可設定，預設為 5 秒)，之後再檢查是否有新的變更 (以避免耗用過多 RU)。 您可以在觸發程序[設定](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中透過 `FeedPollDelay/feedPollDelay` 設定來設定此睡眠時間 (值應以毫秒為單位)。
3. Azure Cosmos 容器可能[受速率限制](./request-units.md)。
4. 可以使用觸發器中`PreferredLocations`的屬性指定 Azure 區域的逗號分隔清單來定義自訂首選連接順序。

### <a name="some-changes-are-repeated-in-my-trigger"></a>一些更改在我的觸發器中重複

"更改"的概念是文檔的操作。 收到同一文檔事件的最常見方案是：
* 帳戶使用的是最終一致性。 在"最終一致性"級別使用更改源時，後續更改源讀取操作之間可能存在重複事件（一個讀取操作的最後一個事件顯示為下一個讀取操作中的第一個事件）。
* 正在更新文檔。 更改源可以包含同一文檔的多個操作，如果該文檔正在接收更新，它可以拾取多個事件（每次更新一個）。 區分同一文檔的不同操作的一種簡單方法是跟蹤`_lsn`[每個更改的屬性](change-feed.md#change-feed-and-_etag-_lsn-or-_ts)。 如果它們不匹配，則這些更改與同一文檔不同。
* 如果僅通過`id`標識文檔，請記住文檔的唯一識別碼是`id`及其分區鍵（可以有兩個具有相同`id`但不同的分區鍵的文檔）。

### <a name="some-changes-are-missing-in-my-trigger"></a>觸發器中缺少某些更改

如果發現 Azure Cosmos 容器中發生的一些更改未由 Azure 函數拾取，則需要執行初步調查步驟。

當 Azure 函數收到更改時，它通常會處理這些更改，並且可以選擇將結果發送到其他目標。 在調查缺少的更改時，請確保**測量在引入點**（Azure 函數啟動時）而不是目標上接收的更改。

如果目標上缺少某些更改，則可能意味著在收到更改後 Azure 函數執行期間會發生一些錯誤。

在這種情況下，最佳操作方案是在代碼中和可能正在處理更改`try/catch`的迴圈內添加塊，以檢測特定項子集的任何故障並相應地處理它們（將它們發送到其他存儲以進行進一步分析或重試）。 

> [!NOTE]
> 根據預設，如果在程式碼執行期間發生未處理的例外狀況，Azure Functions 的 Cosmos DB 觸發程序將不會重試一批變更。 這意味著更改未到達目的地的原因是您未能處理它們。

如果發現觸發器根本不收到某些更改，最常見的方案是正在**運行另一個 Azure 函數**。 它可能是在 Azure 中部署的另一個 Azure 函數，也可以是本地在開發人員電腦上運行的 Azure 函數，該函數具有**完全相同的配置**（相同的監視和租用容器），並且此 Azure 函數正在竊取您希望 Azure 函數處理的更改的子集。

此外，如果您知道正在運行的 Azure 函數應用實例數，則可以驗證該方案。 如果檢查租約容器並計算其中的租約項數，則其中`Owner`屬性的不同值應等於函數應用的實例數。 如果擁有者數多於已知的 Azure 函數應用實例，則意味著這些額外的擁有者是"竊取"更改的。

解決這種情況的一個簡單方法是使用新的/不同值`LeaseCollectionPrefix/leaseCollectionPrefix`對函數應用，或者使用新的租約容器進行測試。

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>需要從一開始就重新開機和重新處理容器中的所有專案 
要從一開始就重新處理容器中的所有項：
1. 如果 Azure 函數當前正在運行，請停止它。 
1. 刪除租約集合中的文檔（或刪除並重新創建租約集合，使其為空）
1. 將函數中的["開始從開始](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)宇宙DB觸發器"屬性設置為 true。 
1. 重新開機 Azure 函數。 現在，它將從一開始就讀取和處理所有更改。 

將["開始從開始"](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)設置為 true 將告訴 Azure 函數從集合歷史記錄的開頭開始讀取更改，而不是目前時間。 僅當沒有已創建的租約（即租約集合中的文檔）時，這才有效。 當已創建租約時，將此屬性設置為 true 不起作用;在這種情況下，當函數停止並重新啟動時，它將開始從租約集合中定義的最後一個檢查點讀取。 要從一開始就重新處理，請按照上述步驟 1-4 進行操作。  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>綁定只能使用 IReadOnlylist\<文檔>或 JArray 完成

如果 Azure 函數專案（或任何引用的專案）包含對 Azure Cosmos DB SDK 的手動 NuGet 引用，其版本與[Azure 函數 Cosmos DB 擴展提供的](./troubleshoot-changefeed-functions.md#dependencies)版本不同，則會發生此錯誤。

要解決此問題，請刪除添加的手動 NuGet 引用，讓 Azure Cosmos DB SDK 引用通過 Azure 函數 Cosmos DB 擴展包解析。

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>更改 Azure 函數的輪詢間隔以檢測更改

如前面為["我的更改"所述，Azure](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)函數在檢查新更改（以避免高 RU 消耗）之前，將休眠一段時間（預設情況下為 5 秒）。 您可以在觸發程序[設定](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中透過 `FeedPollDelay/feedPollDelay` 設定來設定此睡眠時間 (值應以毫秒為單位)。

## <a name="next-steps"></a>後續步驟

* [為 Azure 函數啟用監視](../azure-functions/functions-monitoring.md)
* [Azure 宇宙 DB .NET SDK 故障排除](./troubleshoot-dot-net-sdk.md)
