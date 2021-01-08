---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ebb9dbce020086a716872c86221b97b4b7a6653
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978834"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>我需要在防火牆上開啟哪些連接埠？ 
您可以使用下列通訊協定搭配 Azure 事件中樞來傳送和接收事件：

- Advanced Message 佇列 Protocol 1.0 (AMQP) 
- 使用 TLS (HTTPS) 的超文字傳輸通訊協定1。1
- Apache Kafka

請參閱下表，了解您需要開啟哪些輸出連接埠，以使用這些通訊協定與 Azure 事件中樞進行通訊。 

| 通訊協定 | 連接埠 | 詳細資料 | 
| -------- | ----- | ------- | 
| AMQP | 5671 與 5672 | 請參閱 [AMQP 通訊協定指南](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | 此埠用於 HTTP/REST API 以及 AMQP over Websocket。 |
| Kafka | 9093 | 請參閱[從 Kafka 應用程式使用事件中樞](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

當透過埠5671使用 AMQP 時，也需要 HTTPS 埠才能進行輸出通訊，因為用戶端 Sdk 所執行的數項管理作業，以及使用) 透過 HTTPS 執行時，會從 Azure Active Directory (取得權杖。 

正式的 Azure Sdk 通常會使用 AMQP 通訊協定來從事件中樞傳送和接收事件。 AMQP over Websocket 通訊協定選項會透過埠 TCP 443 （如同 HTTP API）來執行，但在功能上與一般 AMQP 相同。 此選項的初始連線延遲較高，因為有額外的交握往返，而且會稍微增加額外的負荷，以因應共用 HTTPS 埠的取捨。 如果選取此模式，則 TCP 埠443已足夠進行通訊。 下列選項可讓您選取 [一般 AMQP] 或 [AMQP Websocket] 模式：

| 語言 | 選項   |
| -------- | ----- |
| .NET     | 具有[EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true)或[EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true)的[EventHubConnectionOptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true)屬性 |
| Java     | [eventhubs. EventProcessorClientBuilder. transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) with [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) or [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| 節點  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) 具有 `webSocketOptions` 屬性。 |
| Python | 使用[TransportType. Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python)或 TransportType 的[EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) [。 AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>我需要允許哪些 IP 位址？
當您使用 Azure 時，有時您必須允許公司防火牆或 proxy 中的特定 IP 位址範圍或 Url 存取您使用的所有 Azure 服務，或嘗試使用的所有服務。 確認事件中樞所使用的 IP 位址上允許流量。 針對 Azure 事件中樞所使用的 IP 位址：請參閱 [AZURE Ip 範圍和服務標籤-公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。

此外，請確認是否允許命名空間的 IP 位址。 若要尋找正確的 IP 位址以允許您的連線，請遵循下列步驟：

1. 從命令提示字元執行下列命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 記下 `Non-authoritative answer` 中傳回的 IP 位址。 

如果您的命名空間使用 **區域冗余** ，您需要執行一些額外的步驟： 

1. 首先，在命名空間上執行 nslookup。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 記下 [非授權回答] 區段中的名稱，其採用下列其中一種格式： 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 針對尾碼為 s1、s2 和 s3 的每個名稱執行 nslookup，以取得三個執行個體全都在三個可用性區域執行的 IP 位址。 

    > [!NOTE]
    > 命令傳回的 IP 位址 `nslookup` 不是靜態 ip 位址。 不過，它會保持不變，直到基礎部署被刪除或移至不同的叢集為止。

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>哪些用戶端 Ip 會將事件傳送至我的命名空間或從中接收事件？
首先，在命名空間上啟用 [IP 篩選](../articles/event-hubs/event-hubs-ip-filtering.md) 。 

然後，遵循「[啟用診斷記錄](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)」中的指示，啟用[事件中樞虛擬網路線上活動](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema)的診斷記錄。 您會看到連線遭到拒絕的 IP 位址。

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> 只有在命名空間允許 (IP 篩選規則) 的 **特定 ip 位址** 進行存取時，才會產生虛擬網路記錄檔。 如果您不想要使用這些功能來限制對命名空間的存取權，但仍想要取得虛擬網路記錄來追蹤連線至事件中樞命名空間之用戶端的 IP 位址，您可以使用下列因應措施：啟用 IP 篩選，並將可定址的 IPv4 範圍總計新增 (1.0.0.0/1-255.0.0.0/1) 。 事件中樞不支援 IPv6 位址範圍。 

> [!NOTE]
> 目前無法判斷個別訊息或事件的來源 IP。 