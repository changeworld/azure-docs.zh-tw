---
title: 訊息複寫工作模式-Azure 服務匯流排 |Microsoft Docs
description: 本文提供執行特定訊息複寫工作模式的詳細指引
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657365"
---
# <a name="message-replication-tasks-patterns"></a>訊息複寫工作模式

[同盟總覽](service-bus-federation-overview.md)和複寫器函式[概述](service-bus-federation-replicator-functions.md)將說明和複寫工作的基本要素，並建議您先熟悉這些專案，再繼續閱讀本文。

在本文中，我們會詳細說明 [總覽] 區段中已醒目提示之數個模式的執行指南。 

## <a name="replication"></a>複寫 

複寫模式會將訊息從一個佇列或主題複製到下一個佇列或主題，或從佇列或主題複製到其他目的地（例如事件中樞）。 訊息會轉送，而不會對訊息承載進行任何修改。 

這種模式的執行是由 Azure 服務匯流排範例的 [訊息](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) 複寫所涵蓋。

### <a name="sequences-and-order-preservation"></a>順序和順序保留

複寫模型不會將來源佇列或主題的訊息絕對順序保留在目標佇列或主題中，但會在需要時專注于保留應用程式所需之訊息的相對順序。 應用程式會啟用來源實體的會話支援，並使用相同的 [工作階段金鑰](message-sessions.md)將相關訊息分組，以啟用此功能。

會話感知的預先建立複寫函式可確保使用從來源實體抓取之相同會話識別碼的訊息序列，會提交到目標佇列或主題中，做為原始序列中的批次，並使用相同的會話識別碼。 

### <a name="service-assigned-metadata"></a>服務指派的中繼資料 

從來源佇列或主題（原始排入佇列時間和序號）取得之訊息的服務指派中繼資料會以目標佇列或主題中新的服務指派值取代，但是在我們的範例中提供的預設複寫工作，會將原始值保留在使用者屬性中： `repl-enqueue-time` (ISO8601 字串) 和 `repl-sequence` 。

這些屬性的類型為字串，而且包含各自原始屬性的 stringified 值。  如果訊息轉送多次，則會將即時來源的服務指派中繼資料附加至已經存在的屬性，並以分號分隔值。 

### <a name="failover"></a>容錯移轉

如果您使用複寫來進行嚴重損壞修復，以防止服務匯流排服務中的區域可用性訊息，或針對網路中斷，任何這類失敗案例都需要執行從某個佇列或主題到下一個佇列或主題的容錯移轉，以告知生產者和/或取用者使用次要端點。

在所有容錯移轉案例中，假設命名空間的必要元素都是結構相同的，這表示佇列和主題的名稱相同，而且共用存取簽章規則和/或角色型存取控制規則會以相同方式設定。 您可以遵循 [移動命名空間的指引](move-across-regions.md) ，並省略清除步驟，來建立 (，並更新) 次要命名空間。

若要強制生產者和取用者切換，您必須將有關要使用哪個命名空間的資訊提供給可在容易存取及更新的位置中進行查閱。 如果生產者或取用者遇到頻繁或持續性的錯誤，則應查閱該位置並調整其設定。 有許多方式可共用該設定，但我們在下列兩個方法中指出： DNS 和檔案共用。

#### <a name="dns-based-failover-configuration"></a>以 DNS 為基礎的容錯移轉設定

其中一個候選方法是在您所控制的 DNS 中保存 DNS SRV 記錄中的資訊，並指向各自的佇列或主題端點。 請注意，訊息中樞不允許其端點直接以 CNAME 記錄為別名，這表示您將使用 DNS 作為端點位址的復原查閱機制，而不是直接解析 IP 位址資訊。 

假設您擁有網域， `example.com` 以及應用程式的區域 `test.example.com` 。 針對兩個替代服務匯流排，您現在會在每個區域中建立兩個額外的嵌套區域和 SRV 記錄。 

SRV 記錄是遵循一般慣例， `_azure_servicebus._amqp` 並保留兩個端點記錄：一個用於埠5671上的 AMQP OVER TLS，另一個用於埠443上的 AMQP Over websocket，兩者都指向對應至該區域之命名空間的服務匯流排端點。

| 區域                 | SRV 記錄
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

在您的應用程式區域中，您接著會建立 CNAME 專案，指向對應至主要佇列或主題的從屬區域：

| CNAME 記錄                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

使用 DNS 用戶端，以明確地 (JAVA 和 .NET 的內建用戶端明確地查詢 CNAME 和 SRV 記錄，只允許對 IP 位址的名稱進行簡單的解析) ，然後您就可以解析所需的端點。 例如，使用 [DnsClient.NET](https://dnsclient.michaco.net/)時，lookup 函數為：

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

此函式會傳回針對目前以 CNAME 為別名之區域的埠5671所註冊的目標主機名稱，如上所示。 

執行容錯移轉需要編輯 CNAME 記錄，並將其指向替代區域。 

使用 DNS （尤其是 [Azure DNS](../dns/dns-overview.md)）的優點是，Azure DNS 資訊會進行全域複寫，因此可在單一區域中斷時復原。

此程式類似于 [服務匯流排異地 DR](service-bus-geo-dr.md) 的運作方式，但完全在您自己的控制之下，也適用于主動/主動案例。

#### <a name="file-share-based-failover-configuration"></a>以檔案共用為基礎的容錯移轉設定

使用 DNS 來共用端點資訊的最簡單方式，就是將主要端點的名稱放入純文字檔案中，並從穩固的基礎結構提供檔案，以防止中斷並仍允許更新。 

如果您已經使用全球可用性和內容複寫來執行高可用性的網站基礎結構，請在該處新增這類檔案，並在需要切換時重新發佈檔案。

## <a name="merge"></a>合併

合併模式有一或多個指向一個目標的複寫工作，可能與一般產生者同時將訊息傳送到相同的目標。 

這種模式的變化如下：
- 有兩個或多個複寫函數可同時從不同的來源取得訊息，並將訊息傳送至相同的目標。
- 另外還有一個複寫函數可從來源取得訊息，而產生者也直接使用目標。 
- 先前的模式，但是在兩個或多個主題之間鏡像的訊息，會導致這些主題包含相同的訊息，而不管訊息產生的位置為何。

前兩個模式的變化很簡單，而且不會與一般複寫工作不同。

最後一個案例需要排除已複寫的訊息，而不會再次複寫。 這項技術會在主動/主動範例中示範和說明。

## <a name="editor"></a>編輯器

編輯器模式是以 [複寫模式為](#replication) 基礎，但在轉送訊息之前會先修改訊息。 這類修改的範例包括：

- **_轉碼_* _-如果訊息內容 (也稱為「主體」或「承載」 ) 從使用 _Apache Avro * 格式或某些專屬序列化格式的來源進行編碼，但是，如果系統擁有的目標是要以 *json* 編碼內容，轉碼複寫工作會先將 *Apache Avro* 的承載還原序列化為記憶體中的物件圖形，然後再將該圖形序列化為正在轉送之訊息的 *JSON* 格式。 轉碼也包含 **內容壓縮** 和解壓縮工作。
- **_轉換_* _-包含結構化資料的訊息可能需要重新安排該資料，以方便下游取用者取用。 這可能涉及像是簡維化的嵌套結構、剪除無關的資料元素，或將裝載調整為完全符合指定的架構。
- _*_批次處理_*_ -訊息可能會以批次方式接收 (單一傳送) 中的多個訊息會從來源傳送，但必須單獨轉送到目標，反之亦然。 因此，工作可能會根據單一輸入訊息傳輸來轉送多個訊息，或匯總一組接著一起傳送的訊息。 
- _*_驗證_*_ ：來自外部來源的訊息資料通常需要檢查它們是否符合一組規則，然後才可以轉寄。 這些規則可以使用架構或程式碼來表示。 找不到相容性的訊息可能會遭到捨棄，並在記錄中注明問題，或是轉送到特殊的目標目的地來進一步處理。   
- _*_擴充_*_ -來自某些來源的訊息資料可能需要擴充和進一步的內容，才能在目標系統中使用。 這可能牽涉到查閱參考資料，並使用訊息來內嵌該資料，或加入複寫工作已知的來源相關資訊，但不包含在訊息中。 
- _*_篩選_*_ -來自來源的某些訊息可能必須根據某些規則從目標中進行清除。 篩選準則會根據規則測試訊息，如果訊息不符合規則，則會卸載訊息。 藉由觀察特定準則來篩選出重複的訊息，並使用相同的值來刪除後續的訊息，就是篩選的形式。
- _*_路由和分割_*_ ：某些複寫工作可能會允許兩個以上的替代目標，並根據訊息的中繼資料或內容，定義針對任何特定訊息選擇複寫目標的規則。 一種特殊形式的路由是資料分割，其中的工作會根據規則明確指派一個複寫目標中的資料分割。
- _*_密碼_*_ 編譯-複寫工作可能必須解密來自來源的內容，以及/或加密轉送到目標的內容，以及/或可能必須驗證內容和中繼資料的完整性（相對於訊息中包含的簽章），或附加這類簽章。 
- _*_證明_*_ -複寫工作可將中繼資料（可能受數位簽章保護）附加到訊息，證明訊息已透過特定通道或在特定時間收到。     
- _ *_連結_**-複寫工作可能會將簽章套用至訊息的順序，讓順序的完整性受到保護，而且可以偵測到遺漏的訊息。

所有這些模式都可以使用 Azure Functions 來執行，使用 [訊息中樞觸發](../azure-functions/functions-bindings-service-bus-trigger.md) 程式來取得訊息，並使用 [佇列或主題輸出](../azure-functions/functions-bindings-service-bus-output.md) 系結來傳遞它們。 

## <a name="routing"></a>路由

路由 [模式是以複寫模式為](#replication) 基礎，但不是具有一個來源和一個目標，而是在 c # 中說明了多個目標，如下所示：

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

路由函式會考慮訊息中繼資料和/或訊息內容，然後挑選其中一個可用目的地來傳送至。

## <a name="next-steps"></a>後續步驟

- [Azure Functions 中的訊息複製器應用程式][1]
- [在服務匯流排之間複寫訊息][2]
- [將訊息複寫至 Azure 事件中樞][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub