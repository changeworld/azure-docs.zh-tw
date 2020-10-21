---
title: 快速入門：使用 Kafka 通訊協定透過 Azure 事件中樞進行資料串流處理
description: 快速入門：本文提供關於如何使用 Kafka 通訊協定和 API 串流至 Azure 事件中樞的資訊。
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: e87d9b1e4a444695d64dc6acfa0e29f7b72e37f2
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319359"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>快速入門：透過使用 Kafka 通訊協定的事件中樞串流資料 | Microsoft Docs
本快速入門說明如何串流至事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 您將了解如何僅在應用程式中變更設定，就能讓產生者和取用者與事件中樞交談。 

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) 上取得此範例

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，請確定您具備下列必要條件︰

* 請參閱[適用於 Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)一文。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support)。
* [下載](https://maven.apache.org/download.cgi)及[安裝](https://maven.apache.org/install.html) Maven 二進位封存檔。
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
當您建立**標準**層事件中樞命名空間時，命名空間的 Kafka 端點會自動啟用。 您可以將事件從使用 Kafka 通訊協定的應用程式串流到標準層事件中樞。 遵循[使用 Azure 入口網站建立事件中樞](event-hubs-create.md)中的逐步指示來建立**標準**層事件中樞命名空間。 

> [!NOTE]
> Kafka 的事件中樞只能在**標準**和**專用**層使用。 **基本**層不支援在事件中樞上使用 Kafka。

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>使用事件中樞內的 Kafka 傳送及接收訊息

1. 複製[適用於 Kafka 的 Azure 事件中樞存放庫](https://github.com/Azure/azure-event-hubs-for-kafka)。

2. 瀏覽至 `azure-event-hubs-for-kafka/quickstart/java/producer`。

3. 在 `src/main/resources/producer.config` 中更新產生者的組態詳細資料，如下所示：

    **TLS/SSL：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    您可以在 GitHub 的[此處](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)，找到範例處理常式類別 CustomAuthenticateCallbackHandler 的原始程式碼。
4. 執行產生者程式碼，並將事件串流至事件中樞：
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. 瀏覽至 `azure-event-hubs-for-kafka/quickstart/java/consumer`。

6. 在 `src/main/resources/consumer.config` 中更新取用者的組態詳細資料，如下所示：
   
    **TLS/SSL：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    您可以在 GitHub 的[此處](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)，找到範例處理常式類別 CustomAuthenticateCallbackHandler 的原始程式碼。

    您可以在[這裡](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)找到適用於 Kafka 事件中樞的所有 OAuth 範例。
7. 執行取用者程式碼，並使用 Kafka 用戶端從事件中樞處理事件：

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

如果事件中樞 Kafka 叢集包含事件，您現在應該會開始從取用者接收到這些事件。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何串流至事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 若要深入了解，請參閱[適用於 Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)。