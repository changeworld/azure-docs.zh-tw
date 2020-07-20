---
title: 將 JAVA Message Service （JMS）應用程式從 ActiveMQ 遷移至 Azure 服務匯流排 |Microsoft Docs
description: 本文說明如何遷移與 Active MQ 互動的現有 JMS 應用程式，以與 Azure 服務匯流排互動。
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
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122289"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>將現有的 JAVA Message Service （JMS）2.0 應用程式從 Active MQ 遷移至 Azure 服務匯流排

Azure 服務匯流排支援使用 JAVA 訊息服務（JMS） 2.0 API 透過 Advanced Message 佇列通訊協定（AMQP）通訊協定的 JAVA/J2EE 和春季工作負載。

本指南說明當您想要修改現有的 JAVA Message Service （JMS）2.0 應用程式，以與 JMS 代理人（特別是 Apache ActiveMQ 或 Amazon MQ）互動以與 Azure 服務匯流排互動時，您應該注意的事項。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure 服務匯流排與 Apache ActiveMQ 之間的差異

Azure 服務匯流排和 Apache ActiveMQ 都是做為 JMS 提供者的訊息代理程式，可供用戶端應用程式用來傳送和接收訊息。 它們都會啟用具有**佇列**的點對點語義和含有**主題**和訂用帳戶的發佈-訂閱的**語義。** 

就算如此，這兩者還是有一些差異。

| 類別 | 現用 MQ | Azure 服務匯流排 |
| --- | --- | --- |
| 應用程式分層 | 叢集單體 | 雙層 <br> （閘道 + 後端） |
| 通訊協定支援 | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| 佈建模式 | <ul> <li> IaaS （內部部署） </li> <li> Amazon MQ （受控 PaaS） </li> | 受控 PaaS |
| 訊息大小 | 客戶可設定 | 1 MB （Premium 層） |
| 高可用性 | 由客戶管理 | 平臺管理 |
| 災害復原 | 由客戶管理 | 平臺管理 | 

### <a name="current-supported-and-unsupported-features"></a>目前支援和不支援的功能

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>警示

Azure 服務匯流排的兩層式本質提供各種商務持續性功能（高可用性和嚴重損壞修復）。 不過，利用 JMS 功能時，有一些考慮。

#### <a name="service-upgrades"></a>服務升級

在服務匯流排升級和重新開機的情況下，將會刪除暫存佇列或主題。

如果應用程式對臨時佇列或主題的資料遺失很敏感，建議您**不要**使用暫存佇列或主題，而改用永久性佇列、主題和訂用帳戶。

#### <a name="data-migration"></a>資料移轉

在遷移/修改用戶端應用程式以與 Azure 服務匯流排互動的過程中，保留在 ActiveMQ 中的資料將不會遷移至服務匯流排。

可能需要自訂應用程式，才能清空 ActiveMQ 的佇列、主題和訂用帳戶，以及重新執行訊息以服務匯流排的佇列、主題和訂用帳戶。

#### <a name="authentication-and-authorization"></a>驗證與授權

Azure ActiveDirectory 支援的角色型存取控制（RBAC）是 Azure 服務匯流排慣用的驗證機制。

不過，因為目前不支援 RBAC，因為 Apache QPID JMS 缺少以宣告為基礎的驗證支援。

目前僅支援使用 SAS 金鑰進行驗證。

## <a name="pre-migration"></a>移轉前

### <a name="version-check"></a>版本檢查

以下是撰寫 JMS 應用程式時所使用的元件，以及所支援的特定版本。 

| 元件 | 版本 |
|---|---|
| JAVA 訊息服務（JMS） API | 1.1 或更高版本 |
| AMQP 通訊協定 | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>確定 AMQP 埠已開啟

Azure 服務匯流排支援透過 AMQP 通訊協定進行通訊。 基於此目的，必須啟用透過埠5671（AMQP）和443（TCP）的通訊。 視用戶端應用程式的裝載位置而定，您可能需要支援票證才能允許透過這些埠進行通訊。

> [!IMPORTANT]
> Azure 服務匯流排**僅**支援 AMQP 1.0 通訊協定。

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>設定企業設置（VNET、防火牆、私人端點等）

Azure 服務匯流排可提供各種企業安全性和高可用性功能。 若要深入瞭解它們，請遵循下列檔連結。

  * [虛擬網路服務端點](service-bus-service-endpoints.md)
  * [防火牆](service-bus-ip-filtering.md)
  * [使用客戶管理的金鑰進行服務端加密（BYOK）](configure-customer-managed-key.md)
  * [私人端點](private-link-service.md)
  * [驗證和授權](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>監視、警示和追蹤

系統會將每個服務匯流排命名空間的計量發佈到 Azure 監視器，並可用於對配置給命名空間的資源進行警示和動態調整。

若要深入瞭解不同的計量，以及如何在[Azure 監視器中的服務匯流排計量](service-bus-metrics-azure-monitor.md)上設定警示，請參閱。

此外，深入瞭解[資料作業的用戶端追蹤](service-bus-end-to-end-tracing.md)，以及[管理作業的操作/診斷記錄](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>計量-NewRelic

以下是一個方便的指南，其中 ActiveMQ 的計量會對應到 Azure 服務匯流排中的計量。 以下是從 NewRelic 參考的。

  * [ActiveMQ/Amazon MQ NewRelic 計量](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure 服務匯流排 NewRelic 計量](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 目前，NewRelic 不會直接與 ActiveMQ 緊密整合，但它們有適用于 Amazon MQ 的計量。
> 由於 Amazon MQ 衍生自 ActiveMQ，因此下列指南會將 NewRelic 計量從 AmazonMQ 對應到 Azure 服務匯流排。
>

|度量群組| AmazonMQ/主動 MQ 公制 | Azure 服務匯流排度量 |
|------------|---------------------------|--------------------------|
|Broker|`CpuUtilization`|`CPUXNS`|
|Broker|`MemoryUsage`|`WSXNS`|
|Broker|`CurrentConnectionsCount`|`activeConnections`|
|Broker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Broker|`InactiveDurableTopicSubscribersCount`|利用訂用帳戶計量|
|Broker|`TotalMessageCount`|利用佇列/主題/訂用帳戶層級`activeMessages`|
|佇列/主題|`EnqueueCount`|`incomingMessages`|
|佇列/主題|`DequeueCount`|`outgoingMessages`|
|佇列|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>移轉

若要遷移現有的 JMS 2.0 應用程式以與 Azure 服務匯流排互動，必須執行下列步驟。

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>從 ActiveMQ 匯出拓撲並在 Azure 服務匯流排中建立實體（選擇性）

若要確保用戶端應用程式可以順暢地與 Azure 服務匯流排連線，拓撲（包括佇列、主題和訂用帳戶）必須從**Apache ActiveMQ**遷移至**Azure 服務匯流排**。

> [!NOTE]
> 針對 JAVA 訊息服務（JMS）應用程式，建立佇列、主題和訂用帳戶是執行時間作業。 大部分的 JAVA 訊息服務（JMS）提供者（訊息代理程式）都會提供在執行時間建立*佇列*、*主題*和*訂閱*的功能。
>
> 因此，上述步驟是選擇性的。
>
> 若要確保您的應用程式具有在執行時間建立拓撲的許可權，請確定使用具有 SAS 「***管理***」許可權的連接字串。

作法 
  * 利用[ActiveMQ 命令列工具](https://activemq.apache.org/activemq-command-line-tools-reference)來匯出拓撲
  * 使用[Azure Resource Manager 範本](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)重新建立相同的拓撲
  * 執行 Azure Resource Manager 範本。


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>匯入服務匯流排 JMS 執行的 maven 相依性

為了確保與 Azure 服務匯流排的順暢連線，必須將***Azure-匯流排 jms***封裝新增為 Maven 檔案的相依性， `pom.xml` 如下所示。

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

這個部分是針對裝載用戶端應用程式的應用程式伺服器所自訂，而此應用程式是連接到 Active MQ。

#### <a name="tomcat"></a>Tomcat

在這裡，我們會從 Active MQ 特有的設定開始，如檔案中所示 `/META-INF/context.xml` 。

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

可以調整如下，以指向 Azure 服務匯流排

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

##### <a name="update-applicationproperties-file"></a>更新 `application.properties` 檔案

如果使用春季開機應用程式來連線至 ActiveMQ。

這裡的目標是要從檔案中***移除***ActiveMQ 特有的屬性 `application.properties` 。

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

然後***將***服務匯流排特定的屬性新增至檔案 `application.properties` 。

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>將 ActiveMQConnectionFactory 取代為 ServiceBusJmsConnectionFactory

下一個步驟是將 ActiveMQConnectionFactory 的實例取代為 ServiceBusJmsConnectionFactory。

> [!NOTE] 
> 實際的程式碼變更僅適用于應用程式和相依性的管理方式，但下列範例***提供應該變更***之專案的指引。
>

先前，您可以將 ActiveMQConnectionFactory 的物件具現化，如下所示。

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

這會變更為具現化 ServiceBusJmsConnectionFactory 的物件。

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>移轉後

現在您已修改應用程式，以開始從 Azure 服務匯流排傳送和接收訊息，您應該驗證它是否如預期般運作。 完成後，您可以繼續進一步調整並現代化應用程式堆疊。

## <a name="next-steps"></a>後續步驟

利用適用于[AZURE 服務匯流排 JMS 的春天開機 Starter](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) ，與 Azure 服務匯流排無縫整合。

若要深入瞭解服務匯流排訊息和 JAVA 訊息服務（JMS），請參閱下列主題：

* [服務匯流排 JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
