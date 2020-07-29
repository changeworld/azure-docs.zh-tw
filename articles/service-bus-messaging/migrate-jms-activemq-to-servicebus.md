---
title: 將 JAVA Message Service （JMS）應用程式從 Apache ActiveMQ 遷移至 Azure 服務匯流排 |Microsoft Docs
description: 本文說明如何遷移與 Apache ActiveMQ 互動的現有 JMS 應用程式，以與 Azure 服務匯流排互動。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: 35e2e86f68e1f53febabc75fcc537dbdd4481882
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369028"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>將現有的 JAVA Message Service （JMS）2.0 應用程式從 Apache ActiveMQ 遷移至 Azure 服務匯流排

本文討論如何修改現有的 JAVA Message Service （JMS）2.0 應用程式，以與 JMS 代理人互動，改為與 Azure 服務匯流排互動。 特別是，本文涵蓋從 Apache ActiveMQ 或 Amazon MQ 進行遷移。

Azure 服務匯流排支援 JAVA 2 平臺、企業版和春季工作負載，其使用 JMS 2.0 API over Advanced Message 佇列通訊協定（AMQP）。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure 服務匯流排與 Apache ActiveMQ 之間的差異

Azure 服務匯流排和 Apache ActiveMQ 都是訊息代理人，做為用戶端應用程式的 JMS 提供者，以傳送訊息至並從中接收訊息。 它們都會啟用佇列的點對點語義，以及主題和訂用帳戶的發佈-訂閱語義。 

就算如此，這兩者之間還是有一些差異，如下表所示：

| 類別 | ActiveMQ | Azure 服務匯流排 |
| --- | --- | --- |
| 應用程式分層 | 叢集單體 | 雙層 <br> （閘道 + 後端） |
| 通訊協定支援 | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| 佈建模式 | <ul> <li> 基礎結構即服務（IaaS），內部部署 </li> <li> Amazon MQ （受控平臺即服務） </li> | 受控平臺即服務（PaaS） |
| 訊息大小 | 客戶可設定 | 1 MB （Premium 層） |
| 高可用性 | 由客戶管理 | 平臺管理 |
| 災害復原 | 由客戶管理 | 平臺管理 | 

### <a name="current-supported-and-unsupported-features"></a>目前支援和不支援的功能

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>考量

Azure 服務匯流排的兩層式本質提供各種商務持續性功能（高可用性和嚴重損壞修復）。 不過，當您使用 JMS 功能時，會有一些考慮。

#### <a name="service-upgrades"></a>服務升級

在服務匯流排升級和重新開機的情況下，會刪除暫存佇列或主題。 如果您的應用程式對暫存佇列或主題的資料遺失很敏感，請勿使用暫存佇列或主題。 請改用持久性佇列、主題和訂用帳戶。

#### <a name="data-migration"></a>資料移轉

在遷移和修改用戶端應用程式以與 Azure 服務匯流排互動的過程中，保留在 ActiveMQ 中的資料不會遷移至服務匯流排。 您可能需要自訂應用程式來清空 ActiveMQ 佇列、主題和訂用帳戶，然後將訊息重新執行至佇列、主題和訂用帳戶服務匯流排。

#### <a name="authentication-and-authorization"></a>驗證與授權

角色型存取控制（RBAC）（由 Azure Active Directory 支援）是服務匯流排慣用的驗證機制。 因為 RBAC 或宣告式驗證目前不受 Apache QPID JMS 的支援，不過，您應該使用 SAS 金鑰進行驗證。

## <a name="pre-migration"></a>移轉前

### <a name="version-check"></a>版本檢查

撰寫 JMS 應用程式時，您會使用下列元件和版本： 

| 元件 | 版本 |
|---|---|
| JAVA 訊息服務（JMS） API | 1.1 或更高版本 |
| AMQP 通訊協定 | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>確定 AMQP 埠已開啟

服務匯流排支援透過 AMQP 通訊協定進行通訊。 基於此目的，請透過埠5671（AMQP）和443（TCP）啟用通訊。 視用戶端應用程式的裝載位置而定，您可能需要支援票證才能允許透過這些埠進行通訊。

> [!IMPORTANT]
> 服務匯流排僅支援 AMQP 1.0 通訊協定。

### <a name="set-up-enterprise-configurations"></a>設定企業配置

服務匯流排可提供各種企業安全性和高可用性功能。 如需詳細資訊，請參閱 

  * [虛擬網路服務端點](service-bus-service-endpoints.md)
  * [防火牆](service-bus-ip-filtering.md)
  * [使用客戶管理的金鑰進行服務端加密（BYOK）](configure-customer-managed-key.md)
  * [私人端點](private-link-service.md)
  * [驗證與授權](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>監視、警示和追蹤

針對每個服務匯流排命名空間，您可以將計量發佈至 Azure 監視器。 您可以使用這些計量來警示和動態調整配置給命名空間的資源。

如需不同計量以及如何設定警示的詳細資訊，請參閱[Azure 監視器中的服務匯流排計量](service-bus-metrics-azure-monitor.md)。 您也可以進一步瞭解[資料作業的用戶端追蹤](service-bus-end-to-end-tracing.md)，以及[管理作業的操作/診斷記錄](service-bus-diagnostic-logs.md)。

### <a name="metrics---new-relic"></a>計量-新增 New relic

您可以將 ActiveMQ 對應的計量與 Azure 服務匯流排中的計量相互關聯。 請參閱新的 New relic 網站的下列內容：

  * [ActiveMQ/Amazon MQ New New relic 計量](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure 服務匯流排新的 New relic 計量](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 目前，新的 New relic 沒有與 ActiveMQ 的直接、緊密整合，但它們有適用于 Amazon MQ 的計量。 由於 Amazon MQ 衍生自 ActiveMQ，因此下表會將來自 Amazon MQ 的新 New relic 計量對應至 Azure 服務匯流排。
>

|度量群組| Amazon MQ/ActiveMQ 度量 | Azure 服務匯流排度量 |
|------------|---------------------------|--------------------------|
|Broker|`CpuUtilization`|`CPUXNS`|
|Broker|`MemoryUsage`|`WSXNS`|
|Broker|`CurrentConnectionsCount`|`activeConnections`|
|Broker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Broker|`InactiveDurableTopicSubscribersCount`|使用訂用帳戶計量|
|Broker|`TotalMessageCount`|使用佇列/主題/訂用帳戶層級`activeMessages`|
|佇列/主題|`EnqueueCount`|`incomingMessages`|
|佇列/主題|`DequeueCount`|`outgoingMessages`|
|佇列|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>遷移

若要遷移現有的 JMS 2.0 應用程式以與服務匯流排互動，請遵循接下來幾節中的步驟。

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>從 ActiveMQ 匯出拓撲並在服務匯流排中建立實體（選擇性）

若要確保用戶端應用程式可以順暢地與服務匯流排連線，請將拓撲（包括佇列、主題和訂用帳戶）從 Apache ActiveMQ 遷移至服務匯流排。

> [!NOTE]
> 針對 JMS 應用程式，您可以將佇列、主題和訂用帳戶建立為執行時間作業。 大部分的 JMS 提供者（訊息代理程式）都可讓您在執行時間建立這些服務。 這就是為什麼會將此匯出步驟視為選擇性。 若要確保您的應用程式具有在執行時間建立拓撲的許可權，請使用具有 SAS 許可權的連接字串 `Manage` 。

作法：

1. 使用[ActiveMQ 命令列工具](https://activemq.apache.org/activemq-command-line-tools-reference)來匯出拓撲。
1. 使用[Azure Resource Manager 範本](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)重新建立相同的拓撲。
1. 執行 Azure Resource Manager 範本。


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>匯入服務匯流排 JMS 執行的 maven 相依性

若要確保與服務匯流排的順暢連線，請將套件當做相依性新增 `azure-servicebus-jms` 至 Maven 檔案 `pom.xml` ，如下所示：

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>應用程式伺服器設定變更

此元件是針對裝載用戶端應用程式（連接到 ActiveMQ）的應用程式伺服器而自訂的。

#### <a name="tomcat"></a>Tomcat

在這裡，您會從 ActiveMQ 特有的設定開始，如檔案中所示 `/META-INF/context.xml` 。

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

您可以將此調整為指向服務匯流排，如下所示：

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>春季應用程式

##### <a name="update-the-applicationproperties-file"></a>更新檔案 `application.properties`

如果您使用春季開機應用程式連線至 ActiveMQ，您會想要從檔案中移除 ActiveMQ 特有的屬性 `application.properties` 。

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

然後，將服務匯流排特定的屬性新增至檔案 `application.properties` 。

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>將 `ActiveMQConnectionFactory` 取代為 `ServiceBusJmsConnectionFactory`

下一個步驟是將的實例取代為 `ActiveMQConnectionFactory` `ServiceBusJmsConnectionFactory` 。

> [!NOTE] 
> 實際的程式碼變更僅適用于應用程式和相依性的管理方式，但下列範例提供應變更之專案的指引。
>

之前，您可能已具現化的物件 `ActiveMQConnectionFactory` ，如下所示：

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

現在，您要將它變更為具現化的物件 `ServiceBusJmsConnectionFactory` ，如下所示：

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>移轉後

現在您已修改應用程式，以開始從服務匯流排傳送和接收訊息，您應該驗證它是否如預期般運作。 完成後，您可以繼續進一步調整並現代化應用程式堆疊。

## <a name="next-steps"></a>後續步驟

使用[適用于 AZURE 服務匯流排 JMS 的春天開機 Starter](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) ，與服務匯流排無縫整合。

若要深入瞭解服務匯流排訊息和 JMS，請參閱：

* [服務匯流排 JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
