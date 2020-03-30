---
title: Azure 服務匯流排的故障排除指南 |微軟文檔
description: 本文提供了 Azure 服務匯流排消息傳遞異常的清單，以及發生異常時要執行的操作。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: fb27befadcf8e6d201d020e758cfd1ef9b695f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240810"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure 服務匯流排的故障排除指南
本文提供了服務匯流排 .NET 框架 API 生成的一些 .NET 異常，以及故障排除問題的其他提示。 

## <a name="service-bus-messaging-exceptions"></a>服務匯流排傳訊例外狀況
本節列出了 .NET 框架 API 生成的 .NET 異常。 

### <a name="exception-categories"></a>例外狀況類別
傳訊 API 會產生下列類別的例外狀況，以及可用來嘗試修正它們的相關動作。 例外狀況的原因可能會因為訊息實體的類型而不同：

1. 使用者編碼錯誤（[系統.參數異常](https://msdn.microsoft.com/library/system.argumentexception.aspx)，[系統.無效操作異常](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)，[系統.操作取消異常](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)，[系統.運行時.序列化.序列化異常](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)）。 一般動作：請先嘗試修正此程式碼，再繼續執行。
2. 設置/配置錯誤（[微軟.服務匯流排.消息傳遞.消息實體未發現異常](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception)，[系統.未經授權的訪問異常](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)）。 一般動作：檢閱您的組態並視需要進行變更。
3. 瞬態異常（[微軟.服務匯流排.消息傳遞異常](/dotnet/api/microsoft.servicebus.messaging.messagingexception)，[微軟.服務匯流排.消息.伺服器忙異常](/dotnet/api/microsoft.azure.servicebus.serverbusyexception)，[微軟.服務匯流排.消息傳遞異常](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)）。 一般動作：重試此操作或通知使用者。 可以將`RetryPolicy`用戶端 SDK 中的類配置為自動處理重試。 有關詳細資訊，請參閱[重試指南](/azure/architecture/best-practices/retry-service-specific#service-bus)。
4. 其他例外狀況 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)、[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)、[Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)、[Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception))。 一般操作：特定于異常類型;請參閱以下部分中的表： 

### <a name="exception-types"></a>例外狀況類型
下表列出傳訊例外狀況類型及其原因，並指出您可以採取的建議動作。

| **異常類型** | **描述/原因/範例** | **建議的行動** | **自動/立即重試附註** |
| --- | --- | --- | --- |
| [超時例外](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |伺服器未在指定時間內回應請求的操作，該操作由[操作超時](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)控制。 伺服器可能已完成要求的作業。 由於網路或其他基礎結構延遲，可能會發生這種情況。 |檢查系統狀態的一致性，並視需要重試。 請參閱 [逾時例外狀況](#timeoutexception)。 |在某些情況下，重試也許有幫助；將重試邏輯新增至程式碼。 |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |伺服器或服務中不允許請求的使用者操作。 如需詳細資訊，請參閱例外狀況訊息。 例如，如果消息是在[接收和刪除](/dotnet/api/microsoft.azure.servicebus.receivemode)模式下接收的，[則 Complete（）](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)將生成此異常。 |檢查程式碼和文件。 確定要求的作業無效。 |重試沒有説明。 |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |嘗試在已關閉、中止或處置的物件上叫用作業。 極少數的情況下，環境交易是已處置狀態。 |檢查代碼並確保它不會調用已釋放物件的操作。 |重試沒有説明。 |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[權杖提供程式](/dotnet/api/microsoft.servicebus.tokenprovider)物件無法獲取權杖，權杖無效，或者權杖不包含執行操作所需的聲明。 |確定權杖提供者是以正確的值建立。 檢查存取控制服務的配置。 |在某些情況下，重試也許有幫助；將重試邏輯新增至程式碼。 |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |提供給方法的一個或多個引數無效。<br /> 提供給 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 包含路徑區段。<br /> 提供給 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 配置無效。 <br />屬性值大於 32 KB。 |檢查呼叫程式碼，並確定引數正確無誤。 |重試沒有説明。 |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |與操作關聯的實體不存在或已被刪除。 |確定實體已存在。 |重試沒有説明。 |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |嘗試接收具有特定序號的訊息。 找不到此消息。 |確保郵件尚未收到。 檢查寄不出信件佇列，查看訊息是否已停止傳送。 |重試沒有説明。 |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |用戶端無法建立與服務匯流排的連接。 |確定提供的主機名稱正確，且主機可以連線。 |如果有間歇性的連線問題，重試也許有幫助。 |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |服務此時無法處理請求。 |用戶端可以等待一段時間，然後再重試作業。 |用戶端可以在特定間隔後重試。 如果重試產生不同的例外狀況，請檢查該例外狀況的重試行為。 |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |與郵件關聯的鎖權杖已過期，或者找不到鎖權杖。 |處置訊息。 |重試沒有説明。 |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |與此工作階段相關聯的鎖定遺失。 |中止 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 物件。 |重試沒有説明。 |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |可能會在下列情況中擲回的一般傳訊例外狀況：<p>利用屬於不同實體類型 (例如主題) 的名稱或路徑嘗試建立 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 。</p><p>嘗試傳送大於 256 KB 的訊息。 </p>處理要求時伺服器或服務發生錯誤。 如需詳細資訊，請參閱例外狀況訊息。 它通常是暫時性的異常。</p><p>請求已終止，因為實體被限制。 錯誤代碼：50001、50002、50008。 </p> | 查看程式碼，並確定訊息內文只使用可序列化的物件 (或使用自訂序列化程式)。 <p>查看文件來了解支援的屬性值類型，並且只使用支援的類型。</p><p> 查看 [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) 屬性。 如果**這是真的**，您可以重試該操作。 </p>| 如果異常是由於限制造成的，請等待幾秒鐘，然後重試該操作。 重試行為未定義，在其他方案中可能不起作用。|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |嘗試在該服務命名空間中以另一個實體已在使用的名稱建立實體。 |刪除現有的實體，或選擇不同的名稱來建立實體。 |重試沒有説明。 |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |傳訊實體已達到允許的大小上限，或已超過命名空間的連線數目上限。 |從實體或其子佇列接收訊息，在實體中建立空間。 請參閱 [QuotaExceededException](#quotaexceededexception)。 |如果在此同時已移除訊息，重試可能會有幫助。 |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |如果您嘗試建立無效的規則動作，服務匯流排會傳回此例外狀況。 如果處理停止傳送的訊息的規則動作時發生錯誤，服務匯流排會將此例外狀況附加至該訊息。 |檢查規則動作的正確性。 |重試沒有説明。 |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |如果您嘗試建立無效的篩選，服務匯流排會傳回此例外狀況。 如果處理停止傳送的訊息的篩選時發生錯誤，服務匯流排會將此例外狀況附加至該訊息。 |檢查篩選的正確性。 |重試沒有説明。 |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |嘗試接受含有特定工作階段識別碼的工作階段，但該工作階段目前被另一個用戶端鎖定。 |確定其他用戶端已解除鎖定工作階段。 |如果工作階段在過渡期間被解除鎖定，重試可能會有幫助。 |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |交易包含太多作業。 |減少此交易的作業數目。 |重試沒有説明。 |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |在停用的實體上要求執行階段作業。 |啟用實體。 |如實體在過渡期間被啟用，重試可能會有幫助。 |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |如果您將訊息傳送至已啟用預先篩選的主題，但沒有符合的篩選，服務匯流排就會傳回此例外狀況。 |確定至少有一個篩選相符。 |重試沒有説明。 |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |訊息承載超過 256 KB 的限制。 256 KB 的限制是總訊息大小，可包括系統屬性和任何 .NET 負荷。 |減少訊息裝載大小，然後再重試作業。 |重試沒有説明。 |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |環境交易 (*Transaction.Current*) 無效。 其可能已完成或中止。 內部例外狀況可能會提供其他資訊。 | |重試沒有説明。 |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |嘗試在不確定的交易上執行作業，或嘗試認可交易而交易變得不確定。 |應用程式必須處理這個例外狀況 (當成特殊狀況)，因為交易可能已被認可。 |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) 指出已超過某特定實體的配額。

#### <a name="queues-and-topics"></a>佇列和主題
對於佇列和主題，它通常是佇列的大小。 錯誤訊息屬性會包含進一步的詳細資訊，如下例所示：

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

訊息指出主題超過其大小限制，本例為 1 GB (預設大小限制)。 

#### <a name="namespaces"></a>命名空間

針對命名空間，[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) 可能表示應用程式已超過命名空間的連線數目上限。 例如：

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>常見的原因
這個錯誤有兩個常見的原因︰無效信件佇列和訊息接收者未作用。

1. **[無效信件佇列](service-bus-dead-letter-queues.md)** 讀取器無法完成消息，並且當鎖過期時，消息將返回到佇列/主題。 如果讀取器遇到阻止其調用[BrokeredMessage 的](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)異常，則可能發生該事件。 訊息讀取 10 次後，預設會移至無效信件佇列。 這種行為由 [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 屬性控制，預設值是 10。 訊息堆積在無效信件佇列中會佔用空間。
   
    若要解決此問題，請閱讀和完成無效信件佇列中的訊息，就像您處理任何其他佇列一樣。 您可以使用 [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) 方法來協助格式化無效信件佇列路徑。
2. **接收者停止**。 收件者已停止接收佇列或訂用帳戶的訊息。 識別這個原因的方法是查看 [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) 屬性，它會顯示訊息的完整解析。 如果[ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount)屬性高或增長，則消息的讀取速度不如寫入時快。

### <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 表示使用者啟始作業所用的時間長過作業逾時。 

您應該檢查 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) 屬性的值，因為達到這個限制可能也會造成 [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)。

#### <a name="queues-and-topics"></a>佇列和主題
佇列和主題的逾時是在 [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) 屬性中指定，作為連接字串的一部分，或透過 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) 指定。 錯誤訊息本身可能不盡相同，但它一定會包含目前作業的指定逾時值。 

## <a name="connectivity-certificate-or-timeout-issues"></a>連接、證書或超時問題
以下步驟可以説明您解決 *.servicebus.windows.net 下所有服務的連接/證書/超時問題。 

- 流覽到 或[wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`。 它有助於檢查您是否存在 IP 篩選或虛擬網路或憑證連結問題（在使用 java SDK 時最為常見）。

    成功消息的示例：
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    失敗錯誤訊息的示例：

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 運行以下命令以檢查防火牆上是否有任何埠被阻止。 使用的埠為 443 （HTTPS）、5671 （AMQP） 和 9354（網路消息/SBMP）。 根據您使用的庫，也會使用其他埠。 下面是檢查 5671 埠是否被阻止的示例命令。 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    在 Linux 上：

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 當出現間歇性連接問題時，運行以下命令以檢查是否有任何丟棄的資料包。 此命令將嘗試每 1 秒與服務建立 25 個不同的 TCP 連接。 然後，您可以檢查它們中有多少成功/失敗，還可以查看 TCP 連接延遲。 你可以`psping`[從這裡](/sysinternals/downloads/psping)下載該工具。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    如果使用其他工具（如`tnc`、`ping`等），則可以使用等效命令。 
- 如果前面的步驟不起作用，請獲取網路跟蹤，並使用[Wireshark](https://www.wireshark.org/)等工具對其進行分析。 如果需要[，請與微軟支援部門](https://support.microsoft.com/)聯繫。 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>服務升級/重新開機時可能出現的問題
後端服務升級和重新開機可能會對應用程式造成以下影響：

- 請求可能會暫時受到限制。
- 傳入消息/請求可能會下降。
- 日誌檔可能包含錯誤訊息。
- 應用程式可能會斷開與服務幾秒鐘的時間。

如果應用程式代碼使用 SDK，則重試策略已內置並處於活動狀態。 應用程式將重新連接，而不會對應用程式/工作流造成重大影響。

## <a name="next-steps"></a>後續步驟

如需完整的服務匯流排 .NET API 參考資料，請參閱 [Azure .NET API 參考資料](/dotnet/api/overview/azure/service-bus)。

要瞭解有關[服務匯流排](https://azure.microsoft.com/services/service-bus/)的更多詳細資訊，請參閱以下文章：

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [服務匯流排架構](service-bus-architecture.md)

