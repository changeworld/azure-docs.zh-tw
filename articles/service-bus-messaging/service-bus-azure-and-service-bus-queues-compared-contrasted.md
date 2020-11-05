---
title: 比較 Azure 佇列儲存體和服務匯流排佇列
description: 分析 Azure 所提供之兩種佇列類型之間的差異和相似性。
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 5c65cf5ef2d572417ea70d0e0259cf2c03ab590e
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379565"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>儲存體佇列和服務匯流排佇列 - 異同比較
本文會分析 Microsoft Azure：儲存體佇列和服務匯流排佇列所提供的兩種佇列類型之間的差異和相似性。 您可以使用這項資訊，做出更明智的決策，以決定哪一個解決方案最符合您的需求。

## <a name="introduction"></a>簡介
Azure 支援兩種佇列機制： **儲存體佇列** 和 **服務匯流排佇列** 。

**儲存體佇列** 是 [Azure 儲存體](https://azure.microsoft.com/services/storage/) 基礎結構的一部分。 它們可讓您儲存大量的訊息。 使用 HTTP 或 HTTPS 透過境過驗證的呼叫，存取來自世界各地的訊息。 一則佇列訊息的大小可能高達 64 KB。 佇列可以包含數百萬則訊息，最高可達儲存體帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。 如需詳細資訊，請參閱 [什麼是 Azure 儲存體佇列](../storage/queues/storage-queues-introduction.md)。

**服務匯流排佇列** 屬於更廣泛的 [Azure 訊息](https://azure.microsoft.com/services/service-bus/) 基礎結構，可支援佇列、發佈/訂閱，以及更先進的整合模式。 其設計目的是要整合可能跨越多種通訊協定、資料合約、信任網域或網路環境的應用程式或應用程式元件。 如需服務匯流排佇列/主題/訂閱的詳細資訊，請參閱 [服務匯流排佇列、主題和訂閱](service-bus-queues-topics-subscriptions.md)。


## <a name="technology-selection-considerations"></a>技術選擇考量
儲存體佇列和服務匯流排佇列的功能集稍有不同。 您可以根據特定解決方案的需求，選擇其中一種或兩者。

在判斷哪一種佇列技術符合特定解決方案的目的時，方案架構設計人員和開發人員應考慮這些建議。 

### <a name="consider-using-storage-queues"></a>考慮使用儲存體佇列
身為方案架構設計人員/開發人員，您應該在下列情況下 **考慮使用儲存體佇列** ：

* 您的應用程式必須在佇列中儲存超過 80 gb 的訊息。
* 您的應用程式想要追蹤處理佇列中訊息的進度。 如果處理訊息的工作者損毀，這會很有用。 然後，另一個工作者可以使用該資訊從先前背景工作的剩餘位置繼續。
* 您需要有針對佇列執行之所有交易的伺服器端記錄。

### <a name="consider-using-service-bus-queues"></a>考慮使用服務匯流排佇列
身為方案架構設計人員/開發人員，您應該在下列情況下 **考慮使用服務匯流排佇列** ：

* 您的解決方案必須在不需要輪詢佇列的情況下接收訊息。 透過服務匯流排，您可以使用服務匯流排支援的 TCP 通訊協定，藉由使用長期輪詢接收作業來達成此目標。
* 您的方案需要使用佇列來提供保證的先進先出 (FIFO) 排序傳遞。
* 您的解決方案必須支援自動重複偵測。
* 您想要讓應用程式將訊息當成長時間執行的平行資料流來處理 (訊息是透過訊息上的 [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) 屬性與資料流相關聯)。 在這個模型中，取用端應用程式中的每個節點都會競爭取得資料流而不是訊息。 將資料流提供給取用端節點時，節點可以檢查應用程式資料流使用交易的狀態。
* 從佇列傳送或接收多個訊息時，您的方案需要交易行為和不可部分完成性。
* 您的應用程式會處理可能超過 64 KB 的訊息，但不太可能接近 256 KB 的限制。
* 您需要提供角色型存取模型給佇列，並且針對傳送者和接收者提供不同的權限。 如需詳細資訊，請參閱下列文章：
    - [使用受控識別進行驗證](service-bus-managed-service-identity.md)
    - [從應用程式進行驗證](authenticate-application.md)
* 您的佇列大小不會超過 80 GB。
* 您想要使用 AMQP 1.0 標準型傳訊通訊協定。 如需 AMQP 的詳細資訊，請參閱[服務匯流排 AMQP 概觀](service-bus-amqp-overview.md)。
* 您想像了最終從以佇列為基礎的點對點通訊，到發行-訂閱訊息模式的遷移。 此模式可讓 (訂閱者) 的其他接收器整合。 每個接收者都會接收傳送至佇列之部分或所有訊息的獨立複本。 
* 您的訊息方案必須支援「最多一次」傳遞保證，而不需要您建立額外的基礎結構元件。
* 您的解決方案需要發行和取用訊息批次。

## <a name="compare-storage-queues-and-service-bus-queues"></a>比較儲存體佇列和服務匯流排佇列
下列各節中的表格會提供佇列功能的邏輯群組。 它們可讓您一眼就能比較 Azure 儲存體佇列和服務匯流排佇列中可用的功能。

## <a name="foundational-capabilities"></a>基本功能
本節將比較儲存體佇列和服務匯流排佇列所提供的一些基本佇列功能。

| 比較準則 | 儲存體佇列 | 服務匯流排佇列 |
| --- | --- | --- |
| 排序保證 |**否** <br/><br>如需詳細資訊，請參閱 [其他資訊](#additional-information) 一節中的第一個注意事項。</br> | **是 - 先進先出 (FIFO)**<br/><br> (使用 [訊息會話](message-sessions.md))  |
| 傳遞保證 |**至少一次** |至少 **一次** 使用 PeekLock 接收模式的 (。 這是預設值)  <br/><br/>使用 >receiveanddelete 接收模式的 **最多一次** ()  <br/> <br/> 深入瞭解各種 [接收模式](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| 不可部分完成作業支援 |**否** |**是**<br/><br/> |
| 接收行為 |**非封鎖**<br/><br/>(如果找不到新的訊息，便立即完成) |**封鎖或不使用 timeout**<br/><br/>(提供長期輪詢，或稱為 [Comet 技術](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**非封鎖**<br/><br/>(僅限透過使用 .NET 受控 API) |
| 推送型 API |**否** |**是**<br/><br/>[QueueClient. >onmessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) and [MessageSessionHandler. >ONMESSAGE](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) 話 .net API。 |
| 接收模式 |**查看與租用** |**查看與鎖定**<br/><br/>**接收與刪除** |
| 獨佔存取模式 |**以租用為基礎** |**以鎖定為基礎** |
| 租用/鎖定持續時間 |**30 秒 (預設值)**<br/><br/>**7 天 (上限)** (您可以使用 [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API 更新或釋放訊息租用。) |**60 秒 (預設值)**<br/><br/>您可以使用 [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API 更新訊息鎖定。 |
| 租用/鎖定精確度 |**訊息層級**<br/><br/>每個訊息都可以有不同的超時值，然後您可以在處理訊息時視需要進行更新，方法是使用 [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API。 |**佇列層級**<br/><br/>(每個佇列都有套用至所有訊息的鎖定精確度，但是您可以使用 [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API 更新鎖定。) |
| 批次接收 |**是**<br/><br/>(在擷取訊息時明確指定訊息計數，最多 32 個訊息) |**是**<br/><br/>(隱含啟用預先擷取屬性或明確透過使用交易) |
| 批次傳送 |**否** |**是**<br/><br/>(透過使用交易或用戶端批次) |

### <a name="additional-information"></a>其他資訊
* 儲存體佇列中的訊息通常是先進先出，但有時可能不按順序。 例如，當訊息的可見度-timeout 持續時間過期時，是因為用戶端應用程式在處理訊息時當機。 當可視性逾時到期時，訊息會再次出現在佇列上，以便其他工作者清除佇列中的訊息。 屆時，新顯示的訊息可能會被放入佇列中，以重新排入佇列。
* 服務匯流排佇列中的保證 FIFO 模式需要使用訊息工作階段。 如果應用程式在處理 **查看 & 鎖定** 模式中收到的訊息時當機，下次佇列接收者接受訊息會話時，就會在訊息的存留時間 (TTL) 期限到期之後，從失敗的訊息開始。
* 儲存體佇列是設計來支援標準佇列案例，例如下列各項：
    - 分離應用程式元件以增加失敗的擴充性和容錯能力
    - 負載調節
    - 建立程式工作流程。
* 使用會話狀態來儲存應用程式的狀態（相對於處理會話的訊息序列的進度），以及使用有關已取得的已接收訊息和更新會話狀態的交易，可避免與服務匯流排會話內容中的訊息處理有關的不一致。 這種一致性功能有時在其他廠商的產品中只會標示 *一次處理* 。 任何交易失敗顯然都會導致訊息重新傳遞，這就是為什麼詞彙不夠的原因。
* 儲存體佇列提供跨佇列、資料表和 BLOB 之統一且一致的程式設計模型，適合開發人員和營運團隊使用。
* 服務匯流排佇列支援在單一佇列內容中進行本機交易。
* 服務匯流排所支援的「接收與刪除」模式可讓您降低訊息作業計數 (以及關聯的成本)，但是也會降低傳遞保證。
* 儲存體佇列可讓租用延長訊息的租用。 這項功能可讓工作者進程維持短暫的訊息租用。 因此，如果背景工作當機，另一個背景工作可以快速地處理訊息。 此外，如果背景工作需要處理的時間比目前的租用時間還要長，就可以延長訊息的租用。
* 儲存體佇列提供您可以在將訊息加入佇列或從佇列中清除訊息時設定的可視性逾時。 此外，您可以在執行時間使用不同的租用值來更新訊息，並且在相同佇列的訊息之間更新不同的值。 服務匯流排鎖定超時是在佇列中繼資料中定義。 不過，您可以藉由呼叫 [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) 方法來更新鎖定。
* 在服務匯流排佇列中，封鎖接收作業的最大逾時值是 24 天。 但是，REST 架構的最大逾時值為 55 秒。
* 服務匯流排所提供的用戶端批次允許佇列用戶端將多個訊息批次處理成單一傳送作業。 批次處理僅適用於非同步傳送作業。
* 當您虛擬化帳戶時，如 200-TB 的儲存體佇列上限 (更多的功能，) 和無限制的佇列，使其成為 SaaS 提供者的理想平臺。
* 儲存體佇列提供彈性、高效能的委派存取控制機制。

## <a name="advanced-capabilities"></a>進階功能
本節將比較儲存體佇列和服務匯流排佇列所提供的進階功能。

| 比較準則 | 儲存體佇列 | 服務匯流排佇列 |
| --- | --- | --- |
| 排程傳遞 |**是** |**是** |
| 自動處理無效信件 |**否** |**是** |
| 增加佇列存留時間值 |**是**<br/><br/>(經由可視性逾時的就地更新) |**是**<br/><br/>(由專用 API 函式提供) |
| 有害訊息支援 |**是** |**是** |
| 就地更新 |**是** |**是** |
| 伺服器端交易記錄 |**是** |**否** |
| 儲存體度量 |**是**<br/><br/>**分鐘計量** 提供可用性、TPS、API 呼叫計數、錯誤計數等的即時計量。 這些都是即時的，每分鐘匯總一次，並在生產環境中發生的幾分鐘內回報。 如需詳細資訊，請參閱[關於儲存體分析度量](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics)。 |**是**<br/><br/>(透過呼叫 [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues) 來進行大量查詢) |
| 狀態管理 |**否** |**是**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus)、[Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus)、[Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus)、[Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| 訊息自動轉寄 |**否** |**是** |
| 清除佇列函式 |**是** |**否** |
| 訊息群組 |**否** |**是**<br/><br/>(透過使用訊息工作階段) |
| 每個訊息群組的應用程式狀態 |**否** |**是** |
| 重複偵測 |**否** |**是**<br/><br/>(可在傳送者端設定) |
| 瀏覽訊息群組 |**否** |**是** |
| 依 ID 擷取訊息工作階段 |**否** |**是** |

### <a name="additional-information"></a>其他資訊
* 這兩種佇列技術都可讓訊息排定在稍後傳遞。
* 佇列自動轉寄可讓數以千計的佇列將其訊息 autoforward 至單一佇列，接收應用程式會從中取用訊息。 您可以使用這個機制來達成安全性、控制流程，並在每個訊息發佈者之間隔離儲存體。
* 儲存體佇列支援更新訊息內容。 您可以使用這項功能，將狀態資訊和累加進度更新保存至訊息中，以便從最後已知的檢查點處理訊息，而不用從頭開始處理。 使用服務匯流排佇列時，您可以透過使用訊息工作階段來實現相同案例。 工作階段可讓您使用 [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 和 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) 儲存和擷取應用程式處理狀態。
* 服務匯流排佇列支援 [失效](service-bus-dead-letter-queues.md)信件。 它有助於隔離符合下列準則的訊息：
    - 接收應用程式無法成功處理訊息 
    - 因為過期 (TTL) 屬性過期，所以訊息無法送達目的地。 TTL 值會指定訊息保留在佇列中的時間長度。 在服務匯流排中，當 TTL 期限到期時，訊息將會移至稱為 $DeadLetterQueue 的特殊佇列。
* 為了在儲存體佇列中找出「有害」訊息，應用程式會在清除佇列中的訊息時，檢查訊息的 [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) 屬性。 如果 **DequeueCount** 大於給定的臨界值，應用程式就會將訊息移至應用程式定義的「無效信件」佇列。
* 儲存體佇列可讓您取得針對佇列執行之所有交易的詳細記錄，以及匯總的計量。 這兩個選項有助於偵錯和了解應用程式的儲存體佇列使用狀況。 它們也適合用來調整應用程式的效能，並降低使用佇列的成本。
* 服務匯流排所支援的訊息會話可讓屬於邏輯群組的訊息與接收者產生關聯。 它會在訊息與其個別的接收者之間建立類似會話的親和性。 您可以設定訊息上的 [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) 屬性，以在服務匯流排中啟用這項進階功能。 然後，接收者就可以接聽特定的工作階段 ID，並且接收共用指定之工作階段識別項的訊息。
* 服務匯流排佇列的重複偵測功能會根據 [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) 屬性的值，自動移除傳送至佇列或主題的重複訊息。

## <a name="capacity-and-quotas"></a>容量和配額
本節將從可能適用之[容量和配額](service-bus-quotas.md)的觀點來比較儲存體佇列和服務匯流排佇列。

| 比較準則 | 儲存體佇列 | 服務匯流排佇列 |
| --- | --- | --- |
| 佇列大小上限 |**500 TB**<br/><br/> (限制為 [單一儲存體帳戶容量](../storage/common/storage-introduction.md#queue-storage))  |**1 GB 到 80 GB**<br/><br/>(在建立佇列和[啟用分割](service-bus-partitioning.md)時定義 - 請參閱＜其他資訊＞一節) |
| 訊息大小上限 |**64 KB**<br/><br/>(使用 **Base64** 編碼時則為 48 KB)<br/><br/>Azure 可以結合佇列和 Blob 來支援大型訊息，因此您最多可以將 200 GB 的單一項目加入佇列。 |**256 KB** 或 **1 MB**<br/><br/>(包括標頭和主體，標頭大小上限：64 KB)。<br/><br/>取決於[服務層級](service-bus-premium-messaging.md)。 |
| 訊息 TTL 上限 |**無限** (api 版本2017-07-27 或更新版本)  |**TimeSpan.Max** |
| 佇列數目上限 |**無限制** |**10,000**<br/><br/>(每一服務命名空間) |
| 並行用戶端數目上限 |**無限制** |**無限制**<br/><br/>(100 個並行連接限制只適用於以 TCP 通訊協定為基礎的通訊) |

### <a name="additional-information"></a>其他資訊
* 服務匯流排會強制執行佇列大小限制。 建立佇列時，會指定佇列大小上限。 它可以介於 1 GB 和 80 GB 之間。 如果佇列的大小達到此限制，其他內送訊息將會遭到拒絕，而且呼叫端會收到例外狀況。 如需服務匯流排中配額的詳細資訊，請參閱[服務匯流排配額](service-bus-quotas.md)。
* 進階層不支援資料[分割。](service-bus-premium-messaging.md) 在標準通訊層中，您可以建立 1 (預設) 、2、3、4或 5 GB 大小的服務匯流排佇列和主題。 啟用分割時，服務匯流排會在實體 (16 個分割區) 建立16個複本，每個都指定相同的大小。 因此，如果您建立大小為 5 GB 的佇列，其中有16個數據分割，則佇列大小上限會變成 (5 * 16) = 80 GB。  您可以在 [Azure 入口網站][Azure portal]中查看分割佇列或主題的大小上限。
* 使用儲存體佇列時，如果訊息的內容不是 XML 安全的訊息，則必須以 **Base64** 編碼。 如果您對訊息進行 **Base64** 編碼，使用者承載最多可為 48 KB，而非 64 KB。
* 使用服務匯流排佇列時，儲存在佇列中的每個訊息都包含兩個部分：標頭和主體。 訊息大小總計不能超過服務層所支援的訊息大小上限。
* 當用戶端透過 TCP 通訊協定與服務匯流排佇列通訊時，單一服務匯流排佇列的並行連接數目上限會限制為 100。 這個數目是在傳送者和接收者之間共用的。 如果達到此配額，其他連接的要求將會遭到拒絕，而且呼叫程式碼將會收到例外狀況。 使用 REST 型 API 連接至佇列的用戶端不會施加此限制。
* 如果您的單一服務匯流排服務命名空間需要超過 10,000 個佇列，您可以連絡 Azure 支援小組並要求增加。 若要讓服務匯流排擴充到超過 10,000 個佇列，您也可以使用 [Azure 入口網站][Azure portal]來建立其他命名空間。

## <a name="management-and-operations"></a>管理和作業
本節將比較儲存體佇列和服務匯流排佇列所提供的管理功能。

| 比較準則 | 儲存體佇列 | 服務匯流排佇列 |
| --- | --- | --- |
| 管理通訊協定 |**REST 透過 HTTP/HTTPS** |**REST 透過 HTTPS** |
| 執行階段通訊協定 |**REST 透過 HTTP/HTTPS** |**REST 透過 HTTPS**<br/><br/>**AMQP 1.0 標準 (具 TLS 的 TCP)** |
| .NET API |**是**<br/><br/>(.NET 儲存體用戶端 API) |**是**<br/><br/>(.NET 服務匯流排 API) |
| Native C++ |**是** |**是** |
| Java API |**是** |**是** |
| PHP API |**是** |**是** |
| Node.js API |**是** |**是** |
| 任意中繼資料支援 |**是** |**否** |
| 佇列命名規則 |**長度最多 63 個字元**<br/><br/>(佇列名稱的字母必須是小寫。) |**長度最多 260 個字元**<br/><br/>(佇列路徑和名稱不區分大小寫。) |
| 取得佇列長度函式 |**是**<br/><br/>(如果訊息過了 TTL 而到期卻未遭刪除，則為近似值。) |**是**<br/><br/>(精確的時間點值。) |
| 查看函式 |**是** |**是** |

### <a name="additional-information"></a>其他資訊
* 儲存體佇列支援可套用至佇列描述的任意屬性，格式為名稱/值組。
* 這兩種佇列技術都提供不需要鎖定訊息即可查看訊息的功能，這項功能在實作佇列總管/瀏覽器工具時很有用。
* 與 REST over HTTP 相較之下，服務匯流排 .NET 代理訊息應用程式開發介面會使用全雙工 TCP 連接來改善效能，並支援 AMQP 1.0 標準通訊協定。
* 儲存體佇列名稱長度可以是 3-63 個字元，而且可以包含小寫字母、數字和連字號。 如需詳細資訊，請參閱[為佇列和中繼資料命名](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata)。
* 服務匯流排佇列名稱長度最多 260 個字元，而且命名規則的限制較少。 服務匯流排佇列名稱可以包含字母、數字、句號、連字號和底線。

## <a name="authentication-and-authorization"></a>驗證與授權
本節將討論儲存體佇列和服務匯流排佇列所支援的驗證和授權功能。

| 比較準則 | 儲存體佇列 | 服務匯流排佇列 |
| --- | --- | --- |
| 驗證 |**對稱金鑰** |**對稱金鑰** |
| 安全性模型 |透過 SAS 權杖進行委派存取。 |SAS |
| 識別提供者同盟 |**否** |**是** |

### <a name="additional-information"></a>其他資訊
* 對這兩種佇列技術提出的每項要求都必須經過驗證。 不支援具有匿名存取的公用佇列。 使用 [SAS](service-bus-sas.md) 時，您可以發佈唯寫 SAS、唯讀 SAS 或甚至是完整存取 SAS 來解決這種情況。
* 儲存體佇列所提供的驗證配置包含使用對稱金鑰。 此金鑰是雜湊式訊息驗證碼 (HMAC) ，以 SHA-256 演算法計算，並編碼為 **Base64** 字串。 如需個別通訊協定的詳細資訊，請參閱 [Azure 儲存體服務的驗證](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services)。 服務匯流排佇列支援使用對稱金鑰的類似模型。 如需詳細資訊，請參閱[使用服務匯流排的共用存取簽章驗證](service-bus-sas.md)。

## <a name="conclusion"></a>結論
藉由深入瞭解這兩種技術，您可以更明智地決定要使用的佇列技術，以及時機。 關於何時使用儲存體佇列或服務匯流排佇列的決定明顯取決於許多因素。 這些因素可能主要取決於應用程式及其架構的個別需求。 

基於下列原因，您可能會想要選擇儲存體佇列：

- 如果您的應用程式已使用 Microsoft Azure 的核心功能 
- 如果您需要服務之間的基本通訊和訊息傳送 
- 需要大小可超過 80 GB 的佇列

服務匯流排佇列提供許多先進的功能，如下所示。 因此，如果您要建立混合式應用程式，或如果您的應用程式需要這些功能，它們可能是偏好的選擇。

- [工作階段](message-sessions.md)
- [交易](service-bus-transactions.md)
- [重複偵測](duplicate-detection.md)
- [自動寄不出的信件](service-bus-dead-letter-queues.md)
- [持久的發佈和訂閱功能](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>後續步驟
下列文章提供有關使用儲存體佇列或服務匯流排佇列的更多指引和資訊。

* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用佇列儲存體服務](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [使用服務匯流排代理傳訊的效能改進最佳作法](service-bus-performance-improvements.md)
* [Azure 服務匯流排的佇列和主題簡介 (部落格文章)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [服務匯流排的開發人員指南](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [使用 Azure 中的佇列服務](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

