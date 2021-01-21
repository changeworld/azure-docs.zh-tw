---
title: Azure 服務匯流排傳訊概觀 | Microsoft Docs
description: 本文提供 Azure 服務匯流排的高階總覽，其為完全受控的企業整合訊息代理程式。
ms.topic: overview
ms.date: 11/20/2020
ms.openlocfilehash: d9103557eb7b32f376ba6590bbca5f557bf32764
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632862"
---
# <a name="what-is-azure-service-bus"></a>什麼是 Azure 服務匯流排？
Microsoft Azure 服務匯流排是完全受控的企業訊息代理程式，具有訊息佇列和發佈/訂閱主題。 服務匯流排可用來將應用程式和服務彼此分離，而提供下列優點：

- 在競爭的背景工作之間進行工作的負載平衡
- 跨服務和應用程式界限安全地路由和傳輸資料與控制
- 協調需要高程度可靠性的交易式工作 

## <a name="overview"></a>概觀
資料會使用「訊息」  在不同的應用程式和服務之間傳輸。 訊息是以中繼資料裝飾的容器，內含資料。 資料可以是任何種類的資訊，包括以一般格式編碼的結構化資料，例如下列格式：JSON、XML、Apache Avro、純文字。

一些常見的傳訊案例如下：

* *傳訊*。 傳輸商務資料，例如銷售或採購單、日誌或庫存移動。
* *將應用程式分離*。 改善應用程式和服務的可靠性和延展性。 產生者和取用者不需要同時上線或可供使用。 [負載會進行調節](/azure/architecture/patterns/queue-based-load-leveling)，而使流量尖峰不會導致服務的負擔過度。 
* *負載平衡*。 讓多個[競爭取用者](/azure/architecture/patterns/competing-consumers)能夠同時讀取佇列，且各自都能安全地取得特定訊息的專屬所有權。 
* *主題和訂用帳戶*。 在 [發行者與訂閱者](/azure/architecture/patterns/publisher-subscriber)之間啟用 1:*n* 關聯性，讓訂閱者能夠從已發佈的訊息資料流中選取特定訊息。
* *Transactions*。 可讓您執行數項作業，且全都在不可部分完成的交易範圍內。 例如，下列作業可在一個交易的範圍內完成。  

    1. 從一個佇列取得訊息。
    2. 將處理的結果張貼至一或多個不同的佇列。
    3. 移動原始佇列的輸入訊息。 
    
    只有在成功時，下游取用者才會看到結果，包括成功處置輸入訊息，允許僅限一次的處理語意。 此交易模型可供較大解決方案內容中的[補償交易](https://docs.microsoft.com/azure/architecture/patterns/compensating-transaction)模式作為健全的基礎。 
* *訊息工作階段*。 針對需要嚴格訊息排序或訊息延遲的工作流程和多工傳輸，實作大規模的協調。

如果您熟悉其他訊息代理程式 (例如 Apache ActiveMQ)，您對服務匯流排的概念就不會感到陌生。 服務匯流排是一個平台即服務 (PaaS) 供應項目，主要的差異在於，您無須費心處理下列動作。 Azure 會為您代勞。 

- 放置記錄和管理磁碟空間
- 處理備份
- 持續修補作業系統或產品
- 擔心硬體失敗 
- 容錯移轉至保留機器

## <a name="compliance-with-standards-and-protocols"></a>符合標準和通訊協定

服務匯流排的主要線路通訊協定是[進階訊息佇列通訊協定 (AMQP) 1.0](service-bus-amqp-overview.md)，這是開放的 ISO/IEC 標準。 此通訊協定可讓客戶撰寫適用於服務匯流排和內部部署代理程式 (例如 ActiveMQ 或 RabbitMQ) 的應用程式。 [AMQP 通訊協定指南](service-bus-amqp-protocol-guide.md)提供了您想要建置這類抽象概念時所需的詳細資訊。

[服務匯流排進階版](service-bus-premium-messaging.md)完全符合 Java/Jakarta EE [Java 訊息服務 (JMS) 2.0](how-to-use-java-message-service-20.md) API 的規範。 此外，服務匯流排標準版支援著重於佇列的 JMS 1.1 子集。 JMS 是訊息代理程式的一般抽象概念，可與許多應用程式和架構整合，包括廣受使用的 Spring 架構。 若要從其他訊息代理程式切換至 Azure 服務匯流排，您只需重新建立佇列和主題的拓撲，並變更用戶端提供者相依性和設定即可。 如需範例，請參閱 [ActiveMQ 移轉指南](migrate-jms-activemq-to-servicebus.md)。

## <a name="concepts-and-terminology"></a>概念和術語 
本節將討論服務匯流排的概念和術語。

### <a name="namespaces"></a>命名空間
命名空間是所有訊息元件的容器。 多個佇列和主題可以位於單一命名空間中，且命名空間通常會作為應用程式容器。 

命名空間與其他訊息代理程式適用術語中的「伺服器」相仿，但兩者的概念並非直接對等。 服務匯流排命名空間是您在大型叢集中擁有的容量配量，由數十個全數作用中的虛擬機器組成。 命名空間可以選擇性地跨越三個 [Azure 可用性區域](../availability-zones/az-overview.md)。 因此，您能夠獲得大規模執行訊息代理程式的所有可用性和健全性優勢。 而且，您無須擔心基礎複雜性。 服務匯流排是「無伺服器」傳訊。

### <a name="queues"></a>佇列
「佇列」  會收發訊息。 在接收端應用程式能夠接收並處理訊息之前，佇列會儲存訊息。

![佇列](./media/service-bus-messaging-overview/about-service-bus-queue.png)

佇列中的訊息會進行排序並加上抵達時間戳記。 經訊息代理程式接受後，訊息就會在三重備援儲存體中持續保留，且若命名空間已啟用區域，訊息將會分散於多個可用性區域。 在訊息報告至用戶端並獲得接受後，服務匯流排絕不會將訊息保留在記憶體或變動性儲存體中。

訊息會採用「提取」  模式來傳遞，而僅在收到要求時傳遞訊息。 不同於某些其他雲端佇列的忙碌輪詢模型，提取作業可能會長期存留，且在訊息可用時才會完成。 

### <a name="topics"></a>主題

您也可以使用「主題」  來收送訊息。 佇列通常用於點對點通訊，主題則適用於發佈/訂閱案例。

![主題](./media/service-bus-messaging-overview/about-service-bus-topic.png)

主題可以有多個獨立的訂用帳戶，這些訂用帳戶會連結至主題，而其他方面的運作方式則與接收者端的佇列完全相同。 主題的訂閱者會收到該主題所接收每則訊息的複本。 訂用帳戶是具名實體。 訂用帳戶預設為持久性，但可以設定為會到期繼而自動刪除。 透過 JMS API，服務匯流排進階版也可讓您建立存在於連線期間的變動性訂用帳戶。

您可以定義訂用帳戶的規則。 訂用帳戶規則具有 *篩選器*，用以定義要複製到訂用帳戶中的訊息，並且有可修改訊息中繼資料的選擇性 *動作*。 如需詳細資訊，請參閱[主題篩選和動作](topic-filters.md)。 此功能在下列案例中很有幫助：

- 您不想讓訂用帳戶接收所有傳送至主題的訊息。
- 您想要在訊息通過訂用帳戶時以額外的中繼資料標記訊息。

## <a name="advanced-features"></a>進階功能

服務匯流排包含進階功能可讓您解決更複雜的傳訊問題。 以下各節將說明其中幾項功能。

### <a name="message-sessions"></a>訊息工作階段

若要在服務匯流排中建立先進先出 (FIFO) 保證，請使用工作階段。 訊息工作階段能夠獨佔地依序處理無限制的相關訊息序列。 為了能夠在高延展性、高可用性的系統中處理工作階段，工作階段功能也允許儲存工作階段狀態，讓工作階段能夠安全地在處理常式之間移動。 如需詳細資訊，請參閱[訊息工作階段：先進先出 (FIFO)](message-sessions.md)。

### <a name="autoforwarding"></a>自動轉送

自動轉送功能可將佇列或訂用帳戶鏈結至相同命名空間內的另一個佇列或主題。 當您使用這項功能時，服務匯流排會自動將佇列或訂用帳戶中的訊息移至目標佇列或主題。 這類移動全都會以交易方式完成。 如需詳細資訊，請參閱[使用自動轉送鏈結服務匯流排實體](service-bus-auto-forwarding.md)。

### <a name="dead-letter-queue"></a>無效信件佇列

所有的服務匯流排佇列和主題訂用帳戶都有相關聯的無效信件佇列 (DLQ)。 DLQ 會保留符合下列準則的訊息： 

- 訊息無法成功傳遞給任何接收者。
- 訊息已逾時。
- 接收應用程式已明確排除訊息。 

無效信件佇列中的訊息會標註其放置於該處的原因。 無效信件佇列具有特殊端點，但其他方面的運作方式則與任何一般佇列相同。 應用程式或工具可瀏覽 DLQ 或從中清除佇列。 您也可以從無效信件佇列進行自動轉寄。 如需詳細資訊，請參閱[服務匯流排寄不出的信件佇列的概觀](service-bus-dead-letter-queues.md)。

### <a name="scheduled-delivery"></a>排程傳遞

您可以將訊息提交至佇列或主題以進行延遲處理，設定訊息可供取用的時間。 排定的訊息也可以取消。 如需詳細資訊，請參閱[已排程的訊息](message-sequencing.md#scheduled-messages)。

### <a name="message-deferral"></a>訊息延遲

佇列或訂用帳戶用戶端可將已接收訊息的擷取延遲到稍後執行。 訊息可能是以非預期的順序張貼的，而用戶端想要等到收到另一則訊息再處理。 延遲的訊息會保留在佇列或訂用帳戶中，且必須使用服務指派的序號明確地重新啟用。 如需詳細資訊，請參閱[訊息延遲](message-deferral.md)。

### <a name="batching"></a>批次處理

用戶端批次處理可讓佇列或主題用戶端累積一組訊息，並將其一併傳輸。 這通常是為了節省頻寬或增加輸送量。 如需詳細資訊，請參閱[用戶端批次處理](service-bus-performance-improvements.md#client-side-batching)。

### <a name="transactions"></a>交易

交易會將兩個或更多作業一起分組到 *執行範圍* 中。 服務匯流排可讓您對單一交易範圍內的多個傳訊實體進行作業分組。 訊息實體可以是佇列、主題或訂用帳戶。 如需詳細資訊，請參閱[服務匯流排交易處理概觀](service-bus-transactions.md)。

### <a name="autodelete-on-idle"></a>閒置時自動刪除

閒置時自動刪除可讓您指定閒置間隔，此時間過後就自動刪除佇列或主題訂用帳戶。 最小持續期間為 5 分鐘。 如需詳細資訊，請參閱 [QueueDescription.AutoDeleteOnIdle 屬性](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)。

### <a name="duplicate-detection"></a>重複偵測
重複項偵測功能可讓傳送者重新傳送相同的訊息，並且讓訊息代理程式捨棄可能的重複項。 重複項偵測是以追蹤訊息的 `message-id` 屬性為基礎，這表示在重新傳送訊息時，應用程式必須使用相同的值，而該值可能是直接從某些應用程式特定的內容衍生而來的。 如需詳細資訊，請參閱[重複偵測](duplicate-detection.md)。

### <a name="geo-disaster-recovery"></a>異地災害復原

當 Azure 區域停止運作時，災害復原功能能繼續在不同的區域或資料中心進行資料處理。 此功能會保留次要區域中可用命名空間的結構化鏡像，並允許命名空間身分識別切換至次要命名空間。 已張貼的訊息會保留在先前的主要命名空間中，以便在可用性事件平息後進行復原。 如需詳細資訊，請參閱 [Azure 服務匯流排地理災害復原](service-bus-geo-dr.md)。

### <a name="security"></a>安全性

服務匯流排支援標準 [AMQP 1.0](service-bus-amqp-overview.md) 和 [HTTP 或 REST](/rest/api/servicebus/) 通訊協定及其各自的安全功能，包括傳輸層級安全性 (TLS)。 用戶端可以使用[共用存取簽章](service-bus-sas.md)或 [Azure Active Directory](service-bus-authentication-and-authorization.md) 角色型安全性來取得存取權。 

為了防範不當流量，服務匯流排提供了相關[安全性功能](network-security.md)，例如 IP 防火牆以及與虛擬網路的整合。 

## <a name="client-libraries"></a>用戶端程式庫

完整支援的服務匯流排用戶端程式庫可透過 Azure SDK 來使用。

- [適用於 .NET 的 Azure 服務匯流排](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [適用於 Java 的 Azure 服務匯流排程式庫](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2.0 的 Azure 服務匯流排提供者](how-to-use-java-message-service-20.md)
- [適用於 JavaScript 和 TypeScript 的 Azure 服務匯流排模組](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [適用於 Python 的 Azure 服務匯流排程式庫](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure 服務匯流排的主要通訊協定是 AMQP 1.0](service-bus-amqp-overview.md)，可從任何 AMQP 1.0 相容的通訊協定用戶端使用。 數個開放原始碼 AMQP 用戶端有範例可明確示範服務匯流排互通性。 請參閱 [AMQP 1.0 通訊協定指南](service-bus-amqp-protocol-guide.md)，以了解如何將服務匯流排的功能直接用於 AMQP 1.0 用戶端。

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>整合

服務匯流排可與許多 Microsoft 和 Azure 服務完全整合，例如：

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Power Platform](https://powerplatform.microsoft.com/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>後續步驟

若要開始使用服務匯流排傳訊，請參閱下列文章：

* 若要比較 Azure 傳訊服務，請參閱[服務比較](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)。
* 嘗試進行 [.NET](service-bus-dotnet-get-started-with-queues.md)、[Java](service-bus-java-how-to-use-queues.md) 或 [JMS](service-bus-java-how-to-use-jms-api-amqp.md) 的快速入門。
* 若要管理服務匯流排資源，請參閱[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/releases)。
* 若要深入了解標準和進階層及其定價，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。
* 若要了解進階層的效能和延遲，請參閱[進階傳訊](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)。
