---
title: 針對 Cosmos DB 使用 Azure Functions 觸發程式時的問題進行疑難排解
description: 使用 Cosmos DB 的 Azure Functions 觸發程式時的常見問題、因應措施和診斷步驟
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79365503"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>針對 Cosmos DB 使用 Azure Functions 觸發程式時，診斷和疑難排解問題

本文涵蓋當您使用 [Cosmos DB 的 Azure Functions 觸發程式](change-feed-functions.md)時的常見問題、因應措施和診斷步驟。

## <a name="dependencies"></a>相依性

Cosmos DB 的 Azure Functions 觸發程式和系結取決於基底 Azure Functions 執行時間的擴充套件。 請一律更新這些套件，因為它們可能包含修正程式和新功能，這些功能可能會解決您可能會遇到的任何潛在問題：

* 如 Azure Functions V2，請參閱 [CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。
* 如 Azure Functions V1，請參閱 [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)。

除非明確指定，否則本文一律會在每次提到執行時間時參考 Azure Functions V2。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>獨立使用 Azure Cosmos DB SDK

擴充功能套件的主要功能是為 Cosmos DB 提供 Azure Functions 觸發程式和系結的支援。 它也包含 [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md)，如果您想要以程式設計方式與 Azure Cosmos DB 互動，而不使用觸發程式和系結，這會很有説明。

如果您想要使用 Azure Cosmos DB SDK，請確定您未將其他 NuGet 套件參考新增至您的專案。 相反地，請 **讓 SDK 參考解析 Azure Functions 的延伸模組套件**。 與觸發程式和系結分開使用 Azure Cosmos DB SDK

此外，如果您要以手動方式建立自己的 [AZURE COSMOS DB SDK 用戶端](./sql-api-sdk-dotnet-core.md)實例，則應該遵循 [使用單一模式方法](../azure-functions/manage-connections.md#documentclient-code-example-c)來執行只有一個用戶端實例的模式。 此程式可避免您作業中的潛在通訊端問題。

## <a name="common-scenarios-and-workarounds"></a>常見案例和解決方法

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Function 失敗，並出現錯誤訊息收集

Azure 函式失敗，並出現錯誤訊息：「資料庫 ' 資料庫名稱 ' 中的來源集合 ' collection-name ' () 或資料庫 ' database2 ' 中的租用集合 ' collection2-name ' () 不存在。 在接聽程式啟動之前，這兩個集合都必須存在。 若要自動建立租用集合，請將 ' CreateLeaseCollectionIfNotExists ' 設定為 ' true ' "

這表示觸發程式所需的一或兩個 Azure Cosmos 容器都不存在或無法連線到 Azure 函式。 **錯誤本身會告訴您哪個 Azure Cosmos 資料庫和容器是** 根據您的設定而尋找的觸發程式。

1. 確認 `ConnectionStringSetting` 屬性，並 **參考存在於 Azure 函數應用程式中的設定**。 這個屬性的值不應該是連接字串本身，而是設定設定的名稱。
2. 確認 `databaseName` 和 `collectionName` 存在於您的 Azure Cosmos 帳戶中。 如果您使用自動值取代 (使用 `%settingName%` 模式) ，請確定您的 Azure 函數應用程式中有該設定的名稱。
3. 如果您未指定 `LeaseCollectionName/leaseCollectionName` ，則預設值為「租用」。 確認這類容器是否存在。 您可以選擇性地將 `CreateLeaseCollectionIfNotExists` 觸發程式中的屬性設定為，以 `true` 自動建立。
4. 確認您的 [Azure Cosmos 帳戶的防火牆](how-to-configure-firewall.md) 設定，以查看其不會封鎖 azure Function。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 函式無法啟動，因為「共用輸送量集合應該具有分割區索引鍵」

舊版的 Azure Cosmos DB 延伸模組不支援使用在 [共用輸送量資料庫](./set-throughput.md#set-throughput-on-a-database)中建立的租用容器。 若要解決此問題，請更新 [CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 延伸模組以取得最新版本。

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure Function 無法啟動，因為必須提供此作業的 PartitionKey。

這項錯誤表示您目前使用的是已分割的租用集合與舊的 [擴充](#dependencies)功能相依性。 升級至最新的可用版本。 如果您目前是在 Azure Functions V1 上執行，您將需要升級至 Azure Functions V2。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 函式無法從「租用集合（如果已分割）的資料分割索引鍵必須等於識別碼」開始。

此錯誤表示您目前的租用容器已分割，但資料分割索引鍵路徑不是 `/id` 。 若要解決此問題，您必須以分割區索引鍵重新建立租用容器 `/id` 。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>您會看到「值不可以是 null。 當您嘗試執行觸發程式時，Azure Functions 記錄中的參數名稱： o "

如果您使用 Azure 入口網站，且在檢查使用觸發程式的 Azure 函式時，嘗試在畫面上選取 [ **執行** ] 按鈕，就會出現此問題。 觸發程式不需要您選取 [執行] 即可啟動，它會在部署 Azure 函數時自動啟動。 如果您想要檢查 Azure 入口網站上的 Azure 函式記錄資料流程，只要移至受監視的容器並插入一些新的專案，您就會自動看到執行中的觸發程式。

### <a name="my-changes-take-too-long-to-be-received"></a>我的變更花費太長的時間才會收到

這種情況可能會有多個原因，而且應勾選所有的原因：

1. Azure 函式是否部署在與 Azure Cosmos 帳戶相同的區域中？ 為了獲得理想的網路延遲時間，Azure 函式和 Azure Cosmos 帳戶應該共存於相同 Azure 區域。
2. Azure Cosmos 容器中的變更是持續發生還是偶爾發生？
如果是偶爾發生，則在儲存變更與 Azure 函式取得變更之間會有一些延遲。 這是因為在內部，當觸發程序檢查 Azure Cosmos 容器中的變更卻發現沒有任何擱置待讀取的變更時，其便會進入睡眠時間 (時間長短可設定，預設為 5 秒)，之後再檢查是否有新的變更 (以避免耗用過多 RU)。 您可以在觸發程序[設定](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中透過 `FeedPollDelay/feedPollDelay` 設定來設定此睡眠時間 (值應以毫秒為單位)。
3. 您的 Azure Cosmos 容器可能會有 [速率限制](./request-units.md)。
4. 您可以使用 `PreferredLocations` 觸發程式中的屬性來指定以逗號分隔的 Azure 區域清單，以定義自訂的慣用連接順序。

### <a name="some-changes-are-repeated-in-my-trigger"></a>我的觸發程式中會重複某些變更

「變更」的概念是對檔的操作。 收到相同檔之事件的最常見案例包括：
* 帳戶正在使用最終一致性。 使用最終一致性層級中的變更摘要時，在後續的變更摘要讀取作業之間可能會有重複的事件 (一個讀取作業的最後一個事件會顯示為下一個的第一個)。
* 正在更新檔。 變更摘要可以針對相同的檔包含多項作業，如果該檔正在接收更新，則可以為每個更新) 挑選多個事件 (一個。 有一種簡單的方式可區別相同檔的不同作業，也就是追蹤 `_lsn` [每個變更的屬性](change-feed.md#change-feed-and-_etag-_lsn-or-_ts)。 如果不相符，則會在相同的檔中有不同的變更。
* 如果您只是用來識別檔 `id` ，請記住，檔的唯一識別碼是 `id` 和其分割區索引鍵 (可以有兩份檔具有相同 `id` 但不同的分割區索引鍵) 。

### <a name="some-changes-are-missing-in-my-trigger"></a>我的觸發程式中缺少某些變更

如果您發現 Azure 函式未收取 Azure Cosmos 容器中發生的部分變更，則需要進行初始調查步驟，才能執行。

當您的 Azure 函式收到變更時，通常會進行處理，並可選擇性地將結果傳送至另一個目的地。 當您調查遺失的變更時，請務必 **測量在內嵌點收到哪些變更** (當 Azure 函式啟動) ，而不是目的地時。

如果目的地缺少某些變更，這可能表示在收到變更之後，Azure 函數執行期間發生一些錯誤。

在這種情況下，最佳的作法是在 `try/catch` 您的程式碼中，以及可能正在處理變更的迴圈內新增區塊，以偵測特定專案子集的任何失敗，並據以進行處理， (將它們傳送到另一個儲存體，以供進一步分析或重試) 。 

> [!NOTE]
> 根據預設，如果在程式碼執行期間發生未處理的例外狀況，Azure Functions 的 Cosmos DB 觸發程序將不會重試一批變更。 這表示，變更未抵達目的地的原因是因為您無法處理這些變更。

如果您發現觸發程式完全不會收到某些變更，最常見的案例是有 **另一個 Azure**函式正在執行。 它可能是部署在 Azure 中的另一個 Azure 函式，或是在開發人員電腦本機上執行的 Azure 函式，該電腦上的相同設定 (相同 **的** 受監視和租用容器) ，而此 azure 函式會竊取您預期 Azure 函式所要處理的變更子集。

此外，如果您知道有多少個 Azure 函式應用程式實例正在執行，就可以驗證該案例。 如果您檢查您的租用容器，並計算內的租用專案數， `Owner` 它們中屬性的相異值應該等於函數應用程式的實例數目。 如果擁有者多於已知的 Azure 函數應用程式實例，就表示這些額外的擁有者是「竊取」變更的擁有者。

解決這種情況的一個簡單方法是，使用新的/不同的值將套用至您的函式，或者 `LeaseCollectionPrefix/leaseCollectionPrefix` 使用新的租用容器來測試。

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>需要從頭重新開機並重新處理我容器中的所有專案 
從開始重新處理容器中的所有專案：
1. 如果您的 Azure 函式目前正在執行，請將它停止。 
1. 刪除租用集合中的檔 (或刪除並重新建立租用集合，使其為空白) 
1. 將函數中的 [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger 屬性設定為 true。 
1. 重新開機 Azure 函數。 它現在會從一開始就讀取並處理所有變更。 

將 [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) 設定為 true 會告知 Azure 函式開始從集合歷程記錄的開頭讀取變更，而不是目前的時間。 這僅適用于沒有任何已建立的租用 (也就是租用集合中的檔) 。 當有租用已建立時，將此屬性設定為 true 時，不會有任何作用;在此案例中，當函式已停止並重新啟動時，就會從最後一個檢查點開始讀取，如租用集合中所定義。 若要從頭開始重新處理，請遵循上述步驟1-4。  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>系結只能透過 IReadOnlyList \<Document> 或 JArray 完成

如果您的 Azure Functions 專案 (或任何參考的專案) 包含 Azure Cosmos DB SDK 的手動 NuGet 參考，其版本與 [Azure Functions Cosmos DB 延伸](./troubleshoot-changefeed-functions.md#dependencies)模組所提供的版本不同，就會發生此錯誤。

若要解決這種情況，請移除已新增的手動 NuGet 參考，並讓 Azure Cosmos DB SDK 參考透過 Azure Functions Cosmos DB 擴充功能套件來解析。

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>變更偵測變更的 Azure Function 輪詢間隔

如稍早所述， [我的變更花費的時間太長](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)，Azure 函式會在檢查是否有新的 (變更之前，將可設定的時間)  (5 秒，以避免) 的高 RU 耗用量。 您可以在觸發程序[設定](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中透過 `FeedPollDelay/feedPollDelay` 設定來設定此睡眠時間 (值應以毫秒為單位)。

## <a name="next-steps"></a>後續步驟

* [為您的 Azure Functions 啟用監視](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK 疑難排解](./troubleshoot-dot-net-sdk.md)
