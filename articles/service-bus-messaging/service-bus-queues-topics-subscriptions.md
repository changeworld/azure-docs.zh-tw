---
title: Azure 服務匯流排訊息-佇列、主題和訂閱
description: 本文概述) 的 Azure 服務匯流排訊息實體 (佇列、主題和訂閱。
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 54b6a1fd2d4e8e5ef5bb6522374646257213e4b4
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791614"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>服務匯流排佇列、主題和訂用帳戶
Azure 服務匯流排支援一套以雲端為基礎、訊息導向的中介軟體技術，包括可靠的訊息佇列和持久的發佈/訂閱訊息。 這些代理訊息功能可以視為低耦合訊息功能，這些功能可支援使用服務匯流排訊息工作負載的發佈-訂閱、時態性分離和負載平衡案例。 分離通訊有許多優點。 例如，用戶端和伺服器可以視需要連接，並以非同步方式進行作業。

在服務匯流排中形成訊息功能核心的訊息實體是 **佇列**、 **主題和訂閱**，以及規則/動作。

## <a name="queues"></a>佇列
如果有一或多個競爭取用者，佇列會採取「先進先出」(FIFO) 訊息傳遞機制。 也就是說，接收者通常會依將訊息新增至佇列的順序來接收和處理訊息。 而且，只有一個訊息取用者會接收和處理每個訊息。 使用佇列的主要優點是達成 **應用程式元件的時態性分離**。 換句話說，生產者 (傳送者) 和取用者 (接收者) 不需要同時傳送和接收訊息。 這是因為訊息會永久儲存在佇列中。 此外，生產者不需要等待取用者的回復，即可繼續處理及傳送訊息。

相關的優點是 **負載平衡**，可讓生產者和取用者以不同的速率傳送和接收訊息。 在許多應用程式中，系統負載會隨時間而改變。 不過，每個工作單位所需的處理時間通常是常數。 使用佇列作為訊息產生者和取用者表示取用應用程式只需要能夠處理平均負載，而不是尖峰負載。 佇列的深度會隨著連入負載的改變而增加和縮短。 就處理應用程式負載所需的基礎結構數量而言，這個功能可直接節省金錢。 當負載增加時，可以新增更多的背景工作角色程序來讀取佇列中的訊息。 每個訊息僅由其中一個背景工作程序處理。 此外，這個提取型負載平衡可讓背景工作電腦獲得最佳使用，即使以自己的最大速率處理 power pull 訊息的工作者電腦也是一樣。 此模式通常稱為 **競爭消費者** 模式。

使用佇列在訊息產生者與取用者之間居中協調，可提供元件之間的固有鬆散結合。 因為生產者和取用者不知道彼此，所以可以升級取用者，而不會對生產者產生任何影響。

### <a name="create-queues"></a>建立佇列
您可以使用 [Azure 入口網站](service-bus-quickstart-portal.md)、 [PowerShell](service-bus-quickstart-powershell.md)、 [CLI](service-bus-quickstart-cli.md)或 [Resource Manager 範本](service-bus-resource-manager-namespace-queue.md)來建立佇列。 然後，使用以 [c #](service-bus-dotnet-get-started-with-queues.md)、 [JAVA](service-bus-java-how-to-use-queues.md)、 [Python](service-bus-python-how-to-use-queues.md)、 [JavaScript](service-bus-nodejs-how-to-use-queues.md)、 [PHP](service-bus-php-how-to-use-queues.md)和 [Ruby](service-bus-ruby-how-to-use-queues.md)撰寫的用戶端來傳送和接收訊息。 

### <a name="receive-modes"></a>接收模式
您可以指定兩種不同的服務匯流排訊息接收模式：**ReceiveAndDelete** 或 **PeekLock**。 在 [>receiveanddelete](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式中，當服務匯流排收到來自取用者的要求時，它會將訊息標示為已取用，並將其傳回給取用者應用程式。 此模式是最簡單的模型。 它最適合用於應用程式在發生失敗時可容忍不處理訊息的案例。 若要了解此案例，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 當服務匯流排將訊息標示為已使用時，應用程式會在重新開機時開始取用訊息。 它會遺漏在損毀之前所耗用的訊息。

在 [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式中，接收作業變成兩個階段，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會執行下列作業：

1. 尋找要使用的下一個訊息。
1. 將其鎖定以防止其他取用者接收它。
1. 然後，將訊息傳回給應用程式。 

在應用程式完成處理訊息或可靠地儲存此訊息以供未來處理之後，它會在訊息上呼叫，以完成接收程式的第二個階段 [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 。 當服務匯流排收到 **CompleteAsync** 要求時，它會將訊息標示為已取用。

如果應用程式因為某些原因而無法處理訊息，它可以 [`AbandonAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 在訊息 (上呼叫方法，而不是 [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)) 。 這個方法可讓服務匯流排將訊息解除鎖定，讓此訊息可被相同的取用者或其他競爭取用取再次接收。 其次，有一個與鎖定相關聯的超時時間。 如果應用程式無法在鎖定超時到期之前處理訊息，服務匯流排會將訊息解除鎖定，並使其可供再次接收。

如果應用程式在處理訊息之後，但在呼叫之前當機 [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ，服務匯流排會在應用程式重新開機時將訊息重新傳遞至該應用程式。 此程式通常至少會被呼叫 **一次** 處理。 也就是說，每個訊息至少會被處理一次。 不過，但在特定狀況下，可能會重新傳遞相同訊息。 如果您的案例無法容許重複處理，請在您的應用程式中加入其他邏輯，以偵測重複項。 您可以使用訊息的 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 屬性來達到此目的，該屬性會在各個傳遞嘗試中保持不變。 這項功能只稱為 **一次** 處理。

## <a name="topics-and-subscriptions"></a>主題和訂用帳戶
佇列允許單一取用者處理訊息。 相對於佇列、主題和訂用帳戶會在 **發佈和訂閱** 模式中提供一對多的通訊形式。 它適合用來調整為大量的收件者。 每個已發佈的訊息都會提供給每個已註冊主題的訂用帳戶。 發行者會將訊息傳送至主題，而一或多個訂閱者會收到訊息的複本，這取決於這些訂用帳戶上設定的篩選規則。 訂用帳戶可以使用其他篩選器來限制他們想要接收的訊息。 發行者會以傳送訊息至佇列的相同方式，將訊息傳送至主題。 但取用者不會直接從主題接收訊息。 相反地，取用者會從主題的訂用帳戶接收訊息。 主題訂用帳戶類似於虛擬佇列，同樣可接收已傳送到主題的訊息複本。 取用者從訂用帳戶接收訊息的方式，與從佇列接收訊息的方式相同。

佇列的訊息傳送功能會直接對應至主題，而其訊息接收功能會對應至訂用帳戶。 除此之外，這個功能表示訂用帳戶支援本節前面所述有關佇列的相同模式︰競爭取用者、暫時分離、負載調節和負載平衡。

### <a name="create-topics-and-subscriptions"></a>建立主題和訂用帳戶
按照上一節所述，建立主題類似於建立佇列。 您可以使用 [Azure 入口網站](service-bus-quickstart-topics-subscriptions-portal.md)、 [PowerShell](service-bus-quickstart-powershell.md)、 [CLI](service-bus-tutorial-topics-subscriptions-cli.md)或 [Resource Manager 範本](service-bus-resource-manager-namespace-topic.md)來建立主題和訂用帳戶。 然後，將訊息傳送至主題，並使用以 [c #](service-bus-dotnet-how-to-use-topics-subscriptions.md)、 [JAVA](service-bus-java-how-to-use-topics-subscriptions.md)、 [Python](service-bus-python-how-to-use-topics-subscriptions.md)、 [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)、 [PHP](service-bus-php-how-to-use-topics-subscriptions.md)和 [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)撰寫的用戶端，從訂用帳戶接收訊息。 

### <a name="rules-and-actions"></a>執行和動作
在許多情況下，必須以不同的方式處理具有特定特性的訊息。 若要啟用這項處理，您可以設定訂用帳戶以尋找具有所需屬性的訊息，然後對這些屬性進行一些修改。 雖然服務匯流排訂用帳戶可看見所有傳送至主題的訊息，但您只可以將部分的訊息複製到虛擬訂用帳戶佇列。 使用訂用帳戶篩選器即可完成這個篩選。 這類修改稱之為「篩選器動作」。 建立訂閱時，您可以提供可在訊息屬性上運作的篩選運算式。 這些屬性可以是系統屬性 (例如 **標籤**) 和自訂應用程式 (屬性（例如， **StoreName**）。在此情況下，) SQL 篩選運算式是選擇性的。 如果沒有 SQL 篩選運算式，就會在訂閱上定義的所有訊息上執行任何篩選動作。

如需完整的實用範例，請參閱 GitHub 上的 [TopicSubscriptionWithRuleOperationsSample 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)。

如需可能篩選值的詳細資訊，請參閱 [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) 和 [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) 類別的文件。

## <a name="java-message-service-jms-20-entities-preview"></a>JAVA 訊息服務 (JMS) 2.0 實體 (預覽) 
下列實體可透過 JAVA 訊息服務存取 (JMS) 2.0 API。

  * 暫存佇列
  * 暫存主題
  * 共用持久訂閱
  * 未共用的持久訂閱
  * 共用的非持久訂閱
  * 未共用的非持久訂閱

深入瞭解 [JMS 2.0 實體](java-message-service-20-entities.md) ，以及如何 [使用它們](how-to-use-java-message-service-20.md)。

## <a name="next-steps"></a>後續步驟

如需使用服務匯流排傳訊的詳細資訊和範例，請參閱下列進階主題：

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [快速入門：使用 Azure 入口網站和 .NET 傳送和接收訊息](service-bus-quickstart-portal.md)
* [教學課程：使用 Azure 入口網站和主題/訂用帳戶來更新庫存](service-bus-tutorial-topics-subscriptions-portal.md)


