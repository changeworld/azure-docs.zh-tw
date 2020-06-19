---
title: Azure 服務匯流排傳訊概觀 | Microsoft Docs
description: 本文提供 Azure 服務匯流排的高階總覽，其為完全受控的企業整合訊息代理程式。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707885"
---
# <a name="what-is-azure-service-bus"></a>什麼是 Azure 服務匯流排？

Microsoft Azure 服務匯流排是完全受控的企業整合訊息代理程式。 服務匯流排可以將應用程式與服務分離。 服務匯流排針對非同步資料和狀態傳輸提供了可靠且安全的平台。

資料會使用「訊息」  在不同的應用程式和服務之間傳輸。 訊息採用二進位格式，且可包含 JSON、XML 或純文字。 如需詳細資訊，請參閱 [Integration Services](https://azure.com/integration)。

一些常見的傳訊案例如下：

* *傳訊*。 傳輸商務資料，例如銷售或採購單、日誌或庫存移動。
* *將應用程式分離*。 改善應用程式和服務的可靠性和延展性。 用戶端和服務不需要同時上線。
* *主題和訂用帳戶*。 讓「發行者」與「訂閱者」之間有 1:n  的關聯性。
* *訊息工作階段*。 實作需要訊息排序或訊息延遲的工作流程。

## <a name="namespaces"></a>命名空間

命名空間是所有訊息元件的容器。 多個佇列和主題可以位於單一命名空間中，且命名空間通常會作為應用程式容器。

## <a name="queues"></a>佇列

「佇列」  會收發訊息。 在接收端應用程式能夠接收並處理訊息之前，佇列會儲存訊息。

![佇列](./media/service-bus-messaging-overview/about-service-bus-queue.png)

佇列中的訊息會進行排序並加上抵達時間戳記。 訊息一經接受就會安全地保存在備援儲存體中。 訊息會採用「提取」  模式來傳遞，而僅在收到要求時傳遞訊息。

## <a name="topics"></a>主題

您也可以使用「主題」  來收送訊息。 佇列通常用於點對點通訊，主題則適用於發佈/訂閱案例。

![主題](./media/service-bus-messaging-overview/about-service-bus-topic.png)

主題可以有多個獨立的訂用帳戶。 主題的訂閱者會收到該主題所接收每則訊息的複本。 訂用帳戶是具名實體。 訂用帳戶會持續保存，但可能會到期或自動刪除。

您可能會不想讓個別訂用帳戶接收某個主題所收到的所有訊息。 如果是這樣，您可以使用*規則*和*篩選*來定義各種條件，以觸發選擇性*動作*。 您可以篩選指定的訊息，以及設定或修改訊息屬性。 如需詳細資訊，請參閱[主題篩選和動作](topic-filters.md)。

## <a name="advanced-features"></a>進階功能

服務匯流排包含進階功能可讓您解決更複雜的傳訊問題。 以下各節將說明其中幾項功能。

### <a name="message-sessions"></a>訊息工作階段

若要在服務匯流排中建立先進先出 (FIFO) 保證，請使用工作階段。 訊息工作階段能夠聯結和依序處理無限制的相關訊息序列。 如需詳細資訊，請參閱[訊息工作階段：先進先出 (FIFO)](message-sessions.md)。

### <a name="autoforwarding"></a>自動轉送

自動轉送功能可將佇列或訂用帳戶鏈結至另一個佇列或主題。 它們必須是相同命名空間的一部分。 透過自動轉送，服務匯流排會自動移除佇列或訂用帳戶中的訊息，並將其放在不同的佇列或主題中。 如需詳細資訊，請參閱[使用自動轉送鏈結服務匯流排實體](service-bus-auto-forwarding.md)。

### <a name="dead-letter-queue"></a>無效信件佇列

服務匯流排支援無效信件佇列 (DLQ)。 DLQ 會保存無法傳遞給任何接收者的訊息。 它會保存無法處理的訊息。 服務匯流排可讓您從 DLQ 中移除訊息並加以檢查。 如需詳細資訊，請參閱[服務匯流排寄不出的信件佇列的概觀](service-bus-dead-letter-queues.md)。

### <a name="scheduled-delivery"></a>排程傳遞

您可以將訊息提交至佇列或主題，以進行延遲處理。 您可以排定作業讓系統在某個時間處理。 如需詳細資訊，請參閱[已排程的訊息](message-sequencing.md#scheduled-messages)。

### <a name="message-deferral"></a>訊息延遲

佇列或訂用帳戶用戶端可將訊息的擷取延遲到稍後執行。 此延遲可能是基於應用程式中的特殊情況而設定的。 訊息會保留於佇列或訂用帳戶中，但會將它擱置於一旁。 如需詳細資訊，請參閱[訊息延遲](message-deferral.md)。

### <a name="batching"></a>批次處理

用戶端批次處理可讓佇列或主題用戶端將訊息的傳送延遲一段時間。 如果用戶端在此期間傳送其他訊息，它將以單一批次傳輸訊息。 如需詳細資訊，請參閱[用戶端批次處理](service-bus-performance-improvements.md#client-side-batching)。

### <a name="transactions"></a>交易

交易會將兩個或更多作業一起分組到*執行範圍*中。 服務匯流排支援對單一交易範圍內的單一傳訊實體進行分組作業。 訊息實體可以是佇列、主題或訂用帳戶。 如需詳細資訊，請參閱[服務匯流排交易處理概觀](service-bus-transactions.md)。

### <a name="filtering-and-actions"></a>篩選和動作

訂閱者可以定義他們想要接收某個主題的哪些訊息。 這些訊息會以一或多個具名訂用帳戶規則的形式來指定。 對於每個比對規則條件，訂用帳戶會產生一份能針對每條比對規則加上不同註解的訊息。 如需詳細資訊，請參閱[主題篩選和動作](topic-filters.md)。

### <a name="autodelete-on-idle"></a>閒置時自動刪除

閒置時自動刪除可讓您指定閒置間隔，此時間過後就自動刪除佇列。 最小持續期間為 5 分鐘。 如需詳細資訊，請參閱 [QueueDescription.AutoDeleteOnIdle 屬性](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)。

### <a name="duplicate-detection"></a>重複偵測

錯誤可能會導致用戶端不確定傳送作業的結果。 重複項偵測可讓傳送者重新傳送相同的訊息。 另一個選項是讓佇列或主題捨棄任何重複的複本。 如需詳細資訊，請參閱[重複偵測](duplicate-detection.md)。

### <a name="security-protocols"></a>安全性通訊協定
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

服務匯流排支援安全性通訊協定，例如[共用存取簽章](service-bus-sas.md) (SAS)、[角色型存取控制](authenticate-application.md) (RBAC) 和[Azure 資源的受控識別](service-bus-managed-service-identity.md)。

### <a name="geo-disaster-recovery"></a>異地災害復原

當 Azure 區域或資料中心停止運作時，異地災害復原能繼續在不同的區域或資料中心進行資料處理。 如需詳細資訊，請參閱 [Azure 服務匯流排地理災害復原](service-bus-geo-dr.md)。

### <a name="security"></a>安全性

服務匯流排支援標準的 [AMQP 1.0](service-bus-amqp-overview.md) 和 [HTTP/REST](/rest/api/servicebus/) 通訊協定。

## <a name="client-libraries"></a>用戶端程式庫

服務匯流排支援 [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master)、[Java](https://github.com/Azure/azure-service-bus-java/tree/master) 和 [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client) 用戶端程式庫。

## <a name="integration"></a>整合

服務匯流排可與下列 Azure 服務完全整合︰

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>後續步驟

若要開始使用服務匯流排傳訊，請參閱下列文章：

* 若要比較 Azure 傳訊服務，請參閱[服務比較](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)。
* 嘗試進行 [.NET](service-bus-dotnet-get-started-with-queues.md)、[Java](service-bus-java-how-to-use-queues.md) 或 [JMS](service-bus-java-how-to-use-jms-api-amqp.md) 的快速入門。
* 若要管理服務匯流排資源，請參閱[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/releases)。
* 若要深入了解標準和進階層及其定價，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。
* 若要了解進階層的效能和延遲，請參閱[進階傳訊](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)。
