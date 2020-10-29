---
title: 常見問題集 - Azure 事件中樞 | Microsoft Docs
description: 本文提供 Azure 事件中樞的常見問題集 (FAQ) 清單及其答案。
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: 3b55521c9f90192891b450e3e161607a334c3a00
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909704"
---
# <a name="event-hubs-frequently-asked-questions"></a>事件中樞常見問題集

## <a name="general"></a>一般

### <a name="what-is-an-event-hubs-namespace"></a>什麼是事件中樞命名空間？
命名空間是適用於事件中樞/Kafka 主題的範圍容器。 它可為您提供唯一的 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) \(英文\)。 命名空間會用來作為應用程式容器，可裝載多個事件中樞/Kafka 主題。 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>何時建立新的命名空間與使用現有的命名空間？
容量配置 ([輸送量單位 (TU)](#throughput-units)) 會以命名空間層級計費。 命名空間也會與區域相關聯。

在下列其中一個情節中，您可能會想要建立新的命名空間，而不是使用現有的命名空間： 

- 您需要與新區域相關聯的事件中樞。
- 您需要與不同訂用帳戶相關聯的事件中樞。
- 您需要具有不同容量配置的事件中樞 (也就是，新增事件中樞的命名空間所需的容量會超過 40 TU 閾值，而您不想要使用專用叢集)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>事件中樞基本層和標準層之間的差異為何？

Azure 事件中樞的標準層提供比基本層更多的功能。 標準層包含下列功能︰

* 較長的事件保留期
* 其他代理連線，超過包含的數目時支付超額費用
* 超過單一[消費者群組](event-hubs-features.md#consumer-groups)
* [擷取](event-hubs-capture-overview.md)
* [Kafka 整合](event-hubs-for-kafka-ecosystem-overview.md)

如需有關定價層的詳細資訊，包括專用事件中樞，請參閱[事件中樞定價詳細資料](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="where-is-azure-event-hubs-available"></a>哪裡可以取得 Azure 事件中樞？

在所有支援的 Azure 區域皆提供 Azure 事件中樞。 如需清單，請瀏覽 [Azure 區域](https://azure.microsoft.com/regions/)頁面。  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>我可以使用單一 Advanced Message AMQP 通訊協定 () 連線，以從多個事件中樞進行傳送和接收嗎？

是。只要所有事件中樞都位於相同的命名空間內即可。

### <a name="what-is-the-maximum-retention-period-for-events"></a>事件的最大保留期間是多少？

事件中樞標準層目前支援的最大保留期間為七天。 事件中樞不適合用來作為永久資料存放區。 超過24小時的保留期限適用于將事件串流重新執行至相同系統的情況。 例如，在現有的資料上定型或驗證新的機器學習模型。 如果您需要保留訊息七天以上，在事件中樞上啟用[事件中樞擷取](event-hubs-capture-overview.md)，會將資料從事件中樞提取到您選擇的儲存體帳戶或 Azure Data Lake 服務帳戶。 啟用擷取將會產生費用，費用根據您購買的輸送量單位而定。

您可以在儲存體帳戶上設定已擷取資料的保留期限。 Azure 儲存體 **生命週期管理** 功能針對一般用途 v2 與 Blob 儲存體帳戶提供了豐富且以規則為基礎的原則。 使用原則可將資料轉換到適當的存取層，或在資料的生命週期結束時過期。 如需詳細資訊，請參閱[管理 Azure Blob 儲存體生命週期](../storage/blobs/storage-lifecycle-management-concepts.md)。 

### <a name="how-do-i-monitor-my-event-hubs"></a>如何監視事件中樞？
事件中樞會發出詳盡的計量，以便將您的資源狀態提供給 [Azure 監視器](../azure-monitor/overview.md)。 它們也可以讓您存取事件中樞服務的整體健康情況 (不僅是在命名空間層級，還包括在實體層級)。 了解針對 [Azure 事件中樞](event-hubs-metrics-azure-monitor.md)所提供的監視功能。

### <a name="where-does-azure-event-hubs-store-customer-data"></a><a name="in-region-data-residency"></a>Azure 事件中樞儲存客戶資料的位置為何？
Azure 事件中樞儲存客戶資料。 事件中樞會自動將此資料儲存在單一區域中，因此這項服務會自動滿足區域資料落地需求，包括 [信任中心](https://azuredatacentermap.azurewebsites.net/)內指定的需求。

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>我需要在防火牆上開啟哪些連接埠？ 
您可以使用下列通訊協定搭配 Azure 服務匯流排來傳送和接收訊息：

- AMQP
- HTTP
- Apache Kafka

請參閱下表，了解您需要開啟哪些輸出連接埠，以使用這些通訊協定與 Azure 事件中樞進行通訊。 

| 通訊協定 | 連接埠 | 詳細資料 | 
| -------- | ----- | ------- | 
| AMQP | 5671 與 5672 | 請參閱 [AMQP 通訊協定指南](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP、HTTPS | 80、443 |  |
| Kafka | 9093 | 請參閱[從 Kafka 應用程式使用事件中樞](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-allow"></a>我需要允許哪些 IP 位址？
若要尋找要新增至連線允許清單的正確 IP 位址，請遵循下列步驟：

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

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>哪裡可以找到用戶端 IP 傳送或接收到命名空間的訊息？
首先，在命名空間上啟用 [IP 篩選](event-hubs-ip-filtering.md) 。 

然後，遵循「[啟用診斷記錄](event-hubs-diagnostic-logs.md#enable-diagnostic-logs)」中的指示，啟用[事件中樞虛擬網路線上活動](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema)的診斷記錄。 您會看到連線遭到拒絕的 IP 位址。

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

## <a name="apache-kafka-integration"></a>Apache Kafka 整合

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>如何將現有的 Kafka 應用程式與事件中樞整合？
事件中樞提供的 Kafka 端點可供您現有的 Apache Kafka 型應用程式使用。 只需進行設定變更，就能取得 PaaS Kafka 體驗。 它會提供替代方案來執行您自己的 Kafka 叢集。 事件中樞支援 Apache Kafka 1.0 和更新的用戶端版本，並且可與您現有的 Kafka 應用程式、工具及架構搭配使用。 如需詳細資訊，請參閱[適用於 Kafka 的事件中樞存放庫](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)。

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>需要針對我現有的應用程式進行哪些設定變更，才能與事件中樞通訊？
若要連線至事件中樞，您將需要更新 Kafka 用戶端組態。 作法是建立事件中樞命名空間並取得[連接字串](event-hubs-get-connection-string.md)。 變更 bootstrap.servers 以指向事件中樞 FQDN，並將連接埠變更為 9093。 更新 sasl.jaas.config，透過正確的驗證來將 Kafka 用戶端導向到您的事件中樞端點 (此為您取得的連接字串)，如下所示：

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

範例：

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
注意:如果 sasl.jaas.config 不是您架構中支援的設定，請尋找用來設定 SASL 使用者名稱和密碼的設定，並改用那些設定。 將使用者名稱設定為 $ConnectionString，並將密碼設定為您的事件中樞連接字串。

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>適用於事件中樞的訊息/事件大小為何？
針事件中樞所允許的訊息大小上限為 1 MB。

## <a name="throughput-units"></a>輸送量單位

### <a name="what-are-event-hubs-throughput-units"></a>事件中樞輸送量單位是什麼？
事件中樞的輸送量會定義透過事件中樞輸入和輸出的資料量 (以 MB 為單位) 或 1-KB 事件數 (以千為單位)。 此輸送量會以輸送量單位 (TU) 來衡量。 請先購買 TU，然後再開始使用事件中樞服務。 您可以使用入口網站或事件中樞 Resource Manager 範本，明確地選取事件中樞 TU。 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>輸送量單位會套用到命名空間中的所有事件中樞嗎？
是，輸送量單位 (TU) 會套用到事件中樞命名空間中的所有事件中樞。 這表示您要在命名空間層級購買 TU，並在該命名空間下方的事件中樞間共用。 每個 TU 都會為命名空間賦予下列功能：

- 最高每秒 1 MB 的輸入事件 (傳送到事件中樞的事件)，但不超過每秒 1000 個輸入事件、管理作業或控制 API 呼叫。
- 最高每秒 2MB 的輸出事件 (從事件中樞取用的事件)，但是不超過 4096 個輸出事件。
- 最多 84 GB 的事件儲存體 (足以應付預設的 24 小時保留期限)。

### <a name="how-are-throughput-units-billed"></a>輸送量單位如何計費？
輸送量單位 (TU) 會按小時計費。 帳單會以在指定小時內選取的單位數目上限為依據。 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>如何將輸送量單位的使用量最佳化？
您可以從最低的一個輸送量單位 (TU) 開始，並開啟[自動擴充](event-hubs-auto-inflate.md)。 自動擴充功能可讓您隨著流量/承載增加來增加您的 TU。 您也可以設定 TU 數目的上限。

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>事件中樞的自動擴充功能如何運作？
自動擴充功能可讓您擴大輸送量單位 (TU)。 這表示您一開始可以購買較低數目的 TU，然後自動擴充會隨著您的輸入增加而相應增加您的 TU。 它會為您提供符合成本效益的選項及可完全控制的 TU 數目，以進行管理。 此功能是一個 **只能相應增加** 的功能，而您可以藉由更新 TU 數目，完全控制 TU 數目的相應減少。 

您可能想要從較低的輸送量單位 (TU) 開始，例如 2 個 TU。 如果您預測流量可能增加到 15 個 TU，請在您的命名空間上開啟自動擴充功能，然後將上限設定為 15 個 TU。 您現在可讓 TU 隨著流量增加而自動增加。

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>當我開啟成本自動擴充功能時，是否有相關聯的成本？
**沒有任何成本** 與此功能相關聯。 

### <a name="how-are-throughput-limits-enforced"></a>如何強制執行輸送量限制？
如果命名空間內所有事件中樞的 **輸入** 輸送量總計或輸入事件率總計超過彙總輸送量單位額度，傳送者將遭受節流處置並會收到錯誤，指出已超過輸入配額。

如果命名空間內所有事件中樞的 **輸出** 輸送量總計或輸出事件率總計超過彙總輸送量單位額度，接收者將遭受節流處置，但不會產生節流錯誤。 

輸入和輸出配額採單獨實施，如此一來，傳送者無法使事件取用速率變慢，接收者也不能阻止事件傳送到事件中樞。

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>可以保留/選取的輸送量單位數目是否有限制？

在 Azure 入口網站中建立基本或標準層命名空間時，您可以為命名空間選取最多20個 Tu。 若要將其提升為 **正好** 40 tu，請提交  [支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。  

1. 在 [ **事件匯流排命名空間** ] 頁面上，選取左側功能表上的 [ **新增支援要求** ]。 
1. 在 [ **新增支援要求** ] 頁面上，依照下列步驟執行：
    1. 如需 **摘要** ，請使用幾個字來描述問題。 
    1. 針對 [問題類型]  選取 [配額]  。 
    1. 針對 **問題子類型** ，請選取 [ **要求增加或減少輸送量單位** ]。 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="支援要求頁面":::

超過 40 個 TU，事件中樞就會提供稱為事件中樞專用叢集的資源/容量型模型。 專用叢集會以容量單位 (Cu) 來販售。 如需詳細資訊，請參閱 [事件中樞專用-總覽](event-hubs-dedicated-overview.md)。

## <a name="dedicated-clusters"></a>專用叢集

### <a name="what-are-event-hubs-dedicated-clusters"></a>事件中樞專用叢集有哪些？
事件中樞專用叢集可為需求最高的客戶提供單一租用戶部署。 此供應項目會建置未透過輸送量單位繫結的容量型叢集。 這表示您可以使用該叢集，依照叢集的 CPU 和記憶體使用量所指定，來擷取和串流您的資料。 如需詳細資訊，請參閱[事件中樞專用叢集](event-hubs-dedicated-overview.md)。

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>如何建立事件中樞專用叢集？
如需逐步指示及設定事件中樞專用叢集的詳細資訊，請參閱 [快速入門：使用 Azure 入口網站建立專用事件中樞](event-hubs-dedicated-cluster-create-portal.md)叢集。 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>資料分割

### <a name="how-many-partitions-do-i-need"></a>我需要多少個分割區？
分割區數目是在建立時指定，且必須介於1到32之間。 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。 資料分割是一種資料組織機制，與取用端應用程式所需的下游平行處理原則有關。 事件中樞內的資料分割數目，與您預期有的並行讀取器數目直接相關。 如需分割區的詳細資訊，請參閱[分割區](event-hubs-features.md#partitions)。

在建立時，您可能會想要將其設定為最高的可能值，也就是 32。 請記住，有多個分割區會導致事件傳送至多個分割區，而不會保留順序，除非您將傳送者設定為只傳送至 32 個分割區中的單一分割區，而留下剩餘的 31 個分割區做為備援之用。 在前者案例中，您必須跨所有 32 個分割區讀取事件。 在後者案例中，除了您必須在事件處理器主機上進行的額外設定外，沒有明顯的額外成本。

事件中樞的設計目的是讓每個取用者群組均可擁有單一分割區讀取器。 在大多數使用案例中，四個分割區的預設設定就已足夠。 如果您想要調整事件處理，可能就要考慮加入額外的分割區。 分割區上並沒有任何特定的輸送量限制，但是，您命名空間中的彙總輸送量會受限於輸送量單位。 當您提高命名空間中的輸送量單位數目時，可能需要額外的分割區，讓並行讀取器能夠達成它們自己的最大輸送量。

不過，如果在您的模型中應用程式對特定分割區具有同質性，則提高分割區數目可能不會為您帶來任何好處。 如需詳細資訊，請參閱[可用性和一致性](event-hubs-availability-and-consistency.md)。

### <a name="increase-partitions"></a>增加磁碟分割
您可以提交支援要求，要求將分割區計數增加至 40 (精確的) 。 

1. 在 [ **事件匯流排命名空間** ] 頁面上，選取左側功能表上的 [ **新增支援要求** ]。 
1. 在 [ **新增支援要求** ] 頁面上，依照下列步驟執行：
    1. 如需 **摘要** ，請使用幾個字來描述問題。 
    1. 針對 [問題類型]  選取 [配額]  。 
    1. 針對 **問題子類型** ，選取 [ **分割區變更的要求** ]。 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="支援要求頁面":::

分割區計數可以增加到精確的40。 在此情況下，Tu 的數目也必須增加到40。 如果您稍後決定將 TU 限制減少回 <= 20，則最大分割區限制也會減少為32。 

分割區的減少並不會影響現有的事件中樞，因為分割區是在事件中樞層級套用，而且在建立中樞之後是不可變的。 

## <a name="pricing"></a>定價

### <a name="where-can-i-find-more-pricing-information"></a>哪裡可以找到更多定價資訊？

如需事件中樞定價的完整資訊，請參閱[事件中樞定價詳細資料](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>保留事件中樞事件超過 24 小時需要計費嗎？

事件中樞標準層允許 24 小時以上的訊息保留期間，最多達七天。 如果儲存之事件總數的大小超過選定輸送量單位數目的儲存額度 (每個輸送量單位 84 GB)，超過額度的大小將以已發佈的 Azure Blob 儲存體費率計費。 即使您已將輸送量單位的最大輸入額度用盡，每個輸送量單位的儲存額度依然涵蓋 24 小時保留期間 (預設值) 的所有儲存費用。

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>事件中樞儲存空間大小如何計算及收費？

儲存在所有事件中樞內之事件，包括事件標頭的所有內部負荷或磁碟儲存結構的所有內部負荷，是以全天為單位來測量。 我們會在一天結束時計算儲存空間大小峰值。 每日儲存額度的計算乃以當天選定之輸送量單位的最小數目為基準 (每個輸送量單位提供 84 GB 的額度)。 如果總大小超過計算出來的每日儲存額度，我們會採用 Azure Blob 儲存體費率來計算超出的儲存空間 (依照 **本地備援儲存體** 費率)。

### <a name="how-are-event-hubs-ingress-events-calculated"></a>事件中樞輸入事件的計算方式為何？

每個傳送到事件中樞的事件都算是可計費訊息。 *輸入事件* 的定義為小於或等於 64 KB 的資料單位。 任何大小小於或等於 64 KB 的事件均視為一個可計費事件。 如果事件大於 64 KB，可計費事件的數目乃根據事件大小來計算 (64 KB 的倍數)。 例如，一個傳送到事件中樞的 8 KB 事件將視為一個事件來計費，不過，一則傳送到事件中樞的 96 KB 訊息將視為兩個事件來計費。

自事件中樞取用的事件，以及管理作業和控制呼叫 (如檢查點)，不會計入可計費輸入事件，但會累積在輸送量單位額度內。

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>代理連線費用適用於事件中樞嗎？

只有在使用 AMQP 通訊協定時才需要支付連線費用。 不論傳送系統或裝置的數目多寡，使用 HTTP 來傳送事件不需要支付連線費用。 如果您打算使用 AMQP (例如，為了實現更有效率的事件串流，或針對 IoT 命令和控制案例啟用雙向通訊)，請參閱[事件中樞定價資訊](https://azure.microsoft.com/pricing/details/event-hubs/)分頁，以取得關於每個服務層級中包含多少個連線的詳細資訊。

### <a name="how-is-event-hubs-capture-billed"></a>事件中樞擷取如何計費？

當命名空間中的任何事件中樞有啟用擷取選項時，即會啟用擷取。 「事件中樞擷取」會依據購買的輸送量單位每小時計費。 「事件中樞擷取」計費會隨著輸送量單位計數的增減，以一整個小時為增加量反映這些變更。 如需事件中樞擷取計費的詳細資訊，請參閱[事件中樞定價資訊](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>需要針對我為事件中樞擷取所選取的儲存體帳戶付費嗎？

當在事件中樞上啟用擷取時，會使用您提供的儲存體帳戶。 因為它是您的儲存體帳戶，所以，針對此設定的任何變更都將由您的 Azure 訂用帳戶支付相關費用。

## <a name="quotas"></a>配額

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>是否有任何與事件中樞相關聯的配額？

如需所有事件中樞配額的清單，請參閱[配額](event-hubs-quotas.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>在從另一個訂用帳戶中刪除命名空間之後，我為何無法重新建立它？ 
在您從訂用帳戶刪除命名空間之後，必須先等候 4 個小時的時間，才能在另一個訂用帳戶中以相同的名稱重新建立它。 否則，您可能會收到下列錯誤訊息︰`Namespace already exists`。 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>事件中樞所產生的例外狀況有哪些，其建議的動作為何？

如需可能的事件中樞例外狀況清單，請參閱[例外狀況概觀](event-hubs-messaging-exceptions.md)。

### <a name="diagnostic-logs"></a>診斷記錄

事件中樞支援兩種類型的[診斷記錄](event-hubs-diagnostic-logs.md) - 擷取錯誤記錄和作業記錄 - 兩種記錄都是以 JSON 格式代表，且可以透過 Azure 入口網站開啟。

### <a name="support-and-sla"></a>支援與 SLA

事件中樞的技術支援可透過 [Azure 服務匯流排的 Microsoft 問與答頁面](/answers/topics/azure-service-bus.html)取得。 計費及訂用帳戶管理支援均為免費提供。

若要深入了解 SLA，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)頁面。

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>使用 Azure Blob 儲存體做為檢查點存放區時，如何以特定版本的 Azure 儲存體 SDK 為目標？
如果您在 Azure Stack Hub 上執行此程式碼，除非您以特定的儲存體 API 版本為目標，否則會遇到執行階段錯誤。 這是因為事件中樞 SDK 會使用 Azure 中可用的最新可用 Azure 儲存體 API，而這可能無法在您的 Azure Stack Hub 平台上使用。 Azure Stack Hub 支援的儲存體 Blob SDK 版本可能與 Azure 上通常有的不同。 如果您使用 Azure Blog 儲存體作為檢查點存放區，請檢查 [Azure Stack Hub 組建所支援的 Azure 儲存體 API 版本](/azure-stack/user/azure-stack-acs-differences?#api-version)，並在您的程式碼中以該版本作為目標。 

例如，如果您是在 Azure Stack Hub 2005 版上執行，則儲存體服務的最高可用版本是2019-02-02 版。 根據預設，事件中樞 SDK 用戶端程式庫會使用 Azure 上的最高可用版本 (在 SDK 發行時為 2019-07-07)。 在此情況下，除了本節中的以下步驟外，您還需要新增程式碼，以將儲存體服務 API 版本設為 2019-02-02 為目標。 如需如何以特定儲存體 API 版本為目標的範例，請參閱下列 c #、JAVA、Python 和 JavaScript/TypeScript 範例。  

如需如何從程式碼將特定儲存體 API 版本設為目標的範例，請參閱 GitHub 上的下列範例： 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python- [同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)、 [非同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) 和 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](./event-hubs-about.md)
* [建立事件中樞](event-hubs-create.md)
* [事件中樞自動擴大](event-hubs-auto-inflate.md)
