---
title: Azure 服務匯流排訊息例外狀況 |Microsoft Docs
description: 本文提供在發生例外狀況時所要採取的 Azure 服務匯流排訊息例外狀況和建議的動作清單。
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
ms.openlocfilehash: f1a4caf6ffd5740b4227aff2f38d9cb709c77b48
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739342"
---
# <a name="service-bus-messaging-exceptions"></a>服務匯流排傳訊例外狀況
本文列出 .NET Framework Api 所產生的 .NET 例外狀況。 

## <a name="exception-categories"></a>例外狀況類別
傳訊 API 會產生下列類別的例外狀況，以及可用來嘗試修正它們的相關動作。 例外狀況的原因可能會因為訊息實體的類型而不同：

1. 使用者程式碼撰寫錯誤（[ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)、 [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)、 [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)、 [system.web SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)）。 一般動作：請先嘗試修正此程式碼，再繼續執行。
2. 安裝程式/設定錯誤（[microsoft.servicebus.messaging.messagingentitynotfoundexception](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception)、 [system.unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)。 一般動作：檢閱您的組態並視需要進行變更。
3. 暫時性的例外狀況（MessagingException， [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception)， [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)）.. （[microsoft](/dotnet/api/microsoft.servicebus.messaging.messagingexception)服務匯流排）。 一般動作：重試此操作或通知使用者。 客戶`RetryPolicy`端 SDK 中的類別可以設定為自動處理重試。 如需詳細資訊，請參閱[重試指引](/azure/architecture/best-practices/retry-service-specific#service-bus)。
4. 其他例外狀況 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)、[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)、[Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)、[Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception))。 一般動作：例外狀況類型特有;請參閱下一節中的表格： 

## <a name="exception-types"></a>例外狀況類型
下表列出傳訊例外狀況類型及其原因，並指出您可以採取的建議動作。

| **例外狀況類型** | **描述/原因/範例** | **建議的動作** | **自動/立即重試附註** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |伺服器不會在指定的時間內回應要求的作業，這是由[OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)所控制。 伺服器可能已完成要求的作業。 這可能是因為網路或其他基礎結構延遲所造成。 |檢查系統狀態的一致性，並視需要重試。 請參閱 [逾時例外狀況](#timeoutexception)。 |在某些情況下，重試也許有幫助；將重試邏輯新增至程式碼。 |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |伺服器或服務內不允許要求的使用者作業。 如需詳細資訊，請參閱例外狀況訊息。 例如，如果在[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)模式中收到訊息， [Complete （）](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)就會產生這個例外狀況。 |檢查程式碼和文件。 確定要求的作業無效。 |[重試] 不會有説明。 |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |嘗試在已關閉、中止或處置的物件上叫用作業。 極少數的情況下，環境交易是已處置狀態。 |檢查程式碼，並確定它不會在已處置的物件上叫用作業。 |[重試] 不會有説明。 |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider)物件無法取得權杖、權杖無效，或權杖未包含執行作業所需的宣告。 |確定權杖提供者是以正確的值建立。 檢查存取控制服務的設定。 |在某些情況下，重試也許有幫助；將重試邏輯新增至程式碼。 |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |提供給方法的一個或多個引數無效。<br /> 提供給 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 包含路徑區段。<br /> 提供給 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 配置無效。 <br />屬性值大於 32 KB。 |檢查呼叫程式碼，並確定引數正確無誤。 |[重試] 不會有説明。 |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |與作業相關聯的實體不存在或已被刪除。 |確定實體已存在。 |[重試] 不會有説明。 |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |嘗試接收具有特定序號的訊息。 找不到此訊息。 |請確定尚未收到訊息。 檢查寄不出信件佇列，查看訊息是否已停止傳送。 |[重試] 不會有説明。 |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |用戶端無法建立與服務匯流排的連接。 |確定提供的主機名稱正確，且主機可以連線。 |如果有間歇性的連線問題，重試也許有幫助。 |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |服務目前無法處理要求。 |用戶端可以等待一段時間，然後再重試作業。 |用戶端可以在特定間隔後重試。 如果重試產生不同的例外狀況，請檢查該例外狀況的重試行為。 |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |可能會在下列情況中擲回的一般傳訊例外狀況：<p>利用屬於不同實體類型 (例如主題) 的名稱或路徑嘗試建立 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 。</p><p>嘗試傳送大於 256 KB 的訊息。 </p>處理要求時伺服器或服務發生錯誤。 如需詳細資訊，請參閱例外狀況訊息。 這通常是暫時性例外狀況。</p><p>要求已終止，因為實體正在進行節流。 錯誤碼：50001、50002、50008。 </p> | 查看程式碼，並確定訊息內文只使用可序列化的物件 (或使用自訂序列化程式)。 <p>查看文件來了解支援的屬性值類型，並且只使用支援的類型。</p><p> 查看 [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) 屬性。 如果是**true**，您可以重試此作業。 </p>| 如果例外狀況是因為節流所造成，請等候幾秒鐘，然後再次嘗試操作。 重試行為未定義，在其他情況下可能不會有説明。|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |嘗試在該服務命名空間中以另一個實體已在使用的名稱建立實體。 |刪除現有的實體，或選擇不同的名稱來建立實體。 |[重試] 不會有説明。 |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |傳訊實體已達到允許的大小上限，或已超過命名空間的連線數目上限。 |從實體或其子佇列接收訊息，在實體中建立空間。 請參閱 [QuotaExceededException](#quotaexceededexception)。 |如果在此同時已移除訊息，重試可能會有幫助。 |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |如果您嘗試建立無效的規則動作，服務匯流排會傳回此例外狀況。 如果處理停止傳送的訊息的規則動作時發生錯誤，服務匯流排會將此例外狀況附加至該訊息。 |檢查規則動作的正確性。 |[重試] 不會有説明。 |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |如果您嘗試建立無效的篩選，服務匯流排會傳回此例外狀況。 如果處理停止傳送的訊息的篩選時發生錯誤，服務匯流排會將此例外狀況附加至該訊息。 |檢查篩選的正確性。 |[重試] 不會有説明。 |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |嘗試接受含有特定工作階段識別碼的工作階段，但該工作階段目前被另一個用戶端鎖定。 |確定其他用戶端已解除鎖定工作階段。 |如果工作階段在過渡期間被解除鎖定，重試可能會有幫助。 |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |交易包含太多作業。 |減少此交易的作業數目。 |[重試] 不會有説明。 |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |在停用的實體上要求執行階段作業。 |啟用實體。 |如實體在過渡期間被啟用，重試可能會有幫助。 |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |如果您將訊息傳送至已啟用預先篩選的主題，但沒有符合的篩選，服務匯流排就會傳回此例外狀況。 |確定至少有一個篩選相符。 |[重試] 不會有説明。 |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |訊息承載超過 256 KB 的限制。 256 KB 的限制是總訊息大小，可包括系統屬性和任何 .NET 負荷。 |減少訊息裝載大小，然後再重試作業。 |[重試] 不會有説明。 |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |環境交易 (*Transaction.Current*) 無效。 其可能已完成或中止。 內部例外狀況可能會提供其他資訊。 | |[重試] 不會有説明。 |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |嘗試在不確定的交易上執行作業，或嘗試認可交易而交易變得不確定。 |應用程式必須處理這個例外狀況 (當成特殊狀況)，因為交易可能已被認可。 |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) 指出已超過某特定實體的配額。

### <a name="queues-and-topics"></a>佇列和主題
對於佇列和主題，這通常是佇列的大小。 錯誤訊息屬性會包含進一步的詳細資訊，如下例所示：

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

訊息指出主題超過其大小限制，本例為 1 GB (預設大小限制)。 

### <a name="namespaces"></a>命名空間

針對命名空間，[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) 可能表示應用程式已超過命名空間的連線數目上限。 例如：

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>常見的原因
這個錯誤有兩個常見的原因︰無效信件佇列和訊息接收者未作用。

1. **[無效信件佇列](service-bus-dead-letter-queues.md)** 讀取器無法完成訊息，當鎖定到期時，訊息會傳回佇列/主題。 如果讀取器遇到導致無法呼叫[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)的例外狀況，就可能發生這種情況。 訊息讀取 10 次後，預設會移至無效信件佇列。 這種行為由 [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 屬性控制，預設值是 10。 訊息堆積在無效信件佇列中會佔用空間。
   
    若要解決此問題，請閱讀和完成無效信件佇列中的訊息，就像您處理任何其他佇列一樣。 您可以使用 [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) 方法來協助格式化無效信件佇列路徑。
2. **接收者停止**。 收件者已停止接收佇列或訂用帳戶的訊息。 識別這個原因的方法是查看 [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) 屬性，它會顯示訊息的完整解析。 如果[ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount)屬性很高或不斷增加，則訊息的讀取速度不會快于寫入。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 表示使用者啟始作業所用的時間長過作業逾時。 

您應該檢查 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) 屬性的值，因為達到這個限制可能也會造成 [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)。

### <a name="queues-and-topics"></a>佇列和主題
佇列和主題的逾時是在 [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) 屬性中指定，作為連接字串的一部分，或透過 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) 指定。 錯誤訊息本身可能不盡相同，但它一定會包含目前作業的指定逾時值。 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>原因

當使用[PeekLock](message-transfers-locks-settlement.md#peeklock)接收模式接收訊息，而且用戶端所持有的鎖定在服務端過期時，就會擲回**microsoft.servicebus.messaging.messagelocklostexception** 。

訊息的鎖定可能會因為各種原因而過期- 

  * 在用戶端應用程式更新之前，鎖定計時器已過期。
  * 用戶端應用程式已取得鎖定，並將其儲存至持續性存放區，然後重新開機。 重新開機之後，用戶端應用程式會查看傳遞訊息，並嘗試完成這些工作。

### <a name="resolution"></a>解決方案

當**microsoft.servicebus.messaging.messagelocklostexception**時，用戶端應用程式無法再處理訊息。 用戶端應用程式可以選擇性地考慮記錄例外狀況以進行分析，但是用戶端*必須*處置訊息。

因為訊息上的鎖定已過期，所以它會回到佇列（或訂用帳戶），並可由呼叫 receive 的下一個用戶端應用程式來處理。

如果已超過**MaxDeliveryCount** ，則訊息可能會移至**DeadLetterQueue**。

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>原因

當接受會話，而且用戶端所持有的鎖定在服務端過期時，就會擲回 **、microsoft.servicebus.messaging.sessionlocklostexception** 。

會話的鎖定可能會因為各種原因而過期- 

  * 在用戶端應用程式更新之前，鎖定計時器已過期。
  * 用戶端應用程式已取得鎖定，並將其儲存至持續性存放區，然後重新開機。 一旦重新開機之後，用戶端應用程式會查看傳遞會話，並嘗試處理這些會話中的訊息。

### <a name="resolution"></a>解決方案

當 **、microsoft.servicebus.messaging.sessionlocklostexception**時，用戶端應用程式無法再處理會話上的訊息。 用戶端應用程式可能會考慮記錄例外狀況以進行分析，但是用戶端*必須*處置訊息。

由於會話的鎖定已過期，因此它會回到佇列（或訂用帳戶），並可由接受會話的下一個用戶端應用程式鎖定。 由於會話鎖定是由單一用戶端應用程式在任何指定時間持有，因此會保證依序處理。

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>原因

在下列案例中會擲回**SocketException** -
   * 當連接嘗試失敗時，因為主機在指定的時間之後未正確回應（TCP 錯誤碼10060）。
   * 已建立的連接失敗，因為連線的主機無法回應。
   * 處理訊息時發生錯誤，或遠端主機超過超時。
   * 基礎網路資源問題。

### <a name="resolution"></a>解決方案

**SocketException**錯誤指出裝載應用程式的 VM 無法將名稱`<mynamespace>.servicebus.windows.net`轉換成對應的 IP 位址。 

檢查下列命令是否成功對應到 IP 位址。

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

應提供輸出，如下所示

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

如果上述名稱**未解析**為 IP 和命名空間別名，請檢查網路系統管理員以進一步調查。 名稱解析是透過 DNS 伺服器來完成，通常是客戶網路中的資源。 如果 DNS 解析是由 Azure DNS 完成，請洽詢 Azure 支援。

如果名稱解析**如預期般運作**，請在[這裡](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)檢查是否允許 Azure 服務匯流排連線


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>原因

**MessagingException**是可能因各種原因而擲回的一般例外狀況。 其中一些原因如下所示。

   * 嘗試在**主題**或**訂**用帳戶上建立**QueueClient** 。
   * 傳送的訊息大小大於指定層的限制。 閱讀更多有關服務匯流排[配額和限制](service-bus-quotas.md)的資訊。
   * 特定資料平面要求（傳送、接收、完成、放棄）因節流而終止。
   * 因服務升級和重新開機所造成的暫時性問題。

> [!NOTE]
> 上述例外狀況清單並不完整。

### <a name="resolution"></a>解決方案

解決步驟取決於導致擲回**MessagingException**的原因。

   * 針對**暫時性問題**（其中***isTransient***設定為***true***）或針對**節流問題**，重試作業可能會解決此問題。 您可以利用 SDK 上的預設重試原則來進行此操作。
   * 對於其他問題，例外狀況中的詳細資料表示可以從相同的推斷出問題和解決步驟。

## <a name="next-steps"></a>後續步驟
如需完整的服務匯流排 .NET API 參考資料，請參閱 [Azure .NET API 參考資料](/dotnet/api/overview/azure/service-bus)。
如需疑難排解秘訣，請參閱[疑難排解指南](service-bus-troubleshooting-guide.md)
