---
title: 常見問題集 - Azure 事件中樞 | Microsoft Docs
description: 本文提供 Azure 事件中樞的常見問題集 (FAQ) 清單及其答案。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: d2d74a90d6712089cff681381e11b1caec9d469d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283662"
---
# <a name="event-hubs-frequently-asked-questions"></a>事件中樞常見問題集

## <a name="general"></a>一般

### <a name="what-is-an-event-hubs-namespace"></a>什麼是事件中樞命名空間？
命名空間是適用於事件中樞/Kafka 主題的範圍容器。 它可為您提供唯一的 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) \(英文\)。 命名空間會用來作為應用程式容器，可裝載多個事件中樞/Kafka 主題。 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>何時創建新命名空間與使用現有命名空間？
容量分配 （[輸送量單位 （TUs）](#throughput-units)在命名空間級別計費。 命名空間也與區域相關聯。

您可能希望在以下方案之一中創建新的命名空間，而不是使用現有命名空間： 

- 您需要一個與新區域關聯的事件中心。
- 您需要一個與其他訂閱關聯的事件中心。
- 您需要具有不同容量分配的事件中心（即，添加的事件中心的名稱空間的容量將超過 40 TU 閾值，並且您不想轉到專用群集）  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>事件中樞基本層和標準層之間的差異為何？

Azure 事件中樞的標準層提供比基本層更多的功能。 標準層包含下列功能︰

* 較長的事件保留期
* 其他代理連線，超過包含的數目時支付超額費用
* 超過單一[消費者群組](event-hubs-features.md#consumer-groups)
* [捕獲](event-hubs-capture-overview.md)
* [Kafka 整合](event-hubs-for-kafka-ecosystem-overview.md)

如需有關定價層的詳細資訊，包括專用事件中樞，請參閱[事件中樞定價詳細資料](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="where-is-azure-event-hubs-available"></a>哪裡可以取得 Azure 事件中樞？

在所有支援的 Azure 區域皆提供 Azure 事件中樞。 如需清單，請瀏覽 [Azure 區域](https://azure.microsoft.com/regions/)頁面。  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>我是否可以使用單一 AMQP 連線，從多個事件中樞進行傳送及接收？

是。只要所有事件中樞都位於相同的命名空間內即可。

### <a name="what-is-the-maximum-retention-period-for-events"></a>事件的最大保留期間是多少？

事件中樞標準層目前支援的最大保留期間為七天。 事件中心不用作永久資料存儲。 保留期大於 24 小時，適用于方便將事件流重播到同一系統中的情況;例如，根據現有資料訓練或驗證新的機器學習模型。 如果您需要保留訊息七天以上，在事件中樞上啟用[事件中樞擷取](event-hubs-capture-overview.md)，會將資料從事件中樞提取到您選擇的儲存體帳戶或 Azure Data Lake 服務帳戶。 啟用擷取將會產生費用，費用根據您購買的輸送量單位而定。

您可以為存儲帳戶上捕獲的資料配置保留期。 Azure 存儲的**生命週期管理**功能為通用 v2 和 Blob 存儲帳戶提供了豐富的、基於規則的策略。 使用原則可將資料轉換到適當的存取層，或在資料的生命週期結束時過期。 有關詳細資訊，請參閱管理[Azure Blob 存儲生命週期](../storage/blobs/storage-lifecycle-management-concepts.md)。 

### <a name="how-do-i-monitor-my-event-hubs"></a>如何監視事件中樞？
事件中樞會發出詳盡的計量，以便將您的資源狀態提供給 [Azure 監視器](../azure-monitor/overview.md)。 它們也可以讓您存取事件中樞服務的整體健康情況 (不僅是在命名空間層級，還包括在實體層級)。 了解針對 [Azure 事件中樞](event-hubs-metrics-azure-monitor.md)所提供的監視功能。

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>我需要在防火牆上打開哪些埠？ 
可以使用以下與 Azure 服務匯流排的協定來發送和接收消息：

- 進階訊息佇列通訊協定 (AMQP)
- HTTP
- Apache Kafka

有關使用這些協定與 Azure 事件中心通信需要打開的出站埠，請參閱下表。 

| 通訊協定 | 連接埠 | 詳細資料 | 
| -------- | ----- | ------- | 
| AMQP | 5671 和 5672 | 請參閱[AMQP 協定指南](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP、HTTPS | 80、443 |  |
| Kafka | 9093 | 請參閱[使用卡夫卡應用程式中的事件中心](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>我需要哪些 IP 位址才能進行白名單？
要查找連接的右側 IP 位址到白清單，請按照以下步驟操作：

1. 從命令提示符運行以下命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 記下在 中返回的`Non-authoritative answer`IP 位址。 它唯一更改的時間是，如果將命名空間還原到其他群集。

如果對命名空間使用區域冗余，則需要執行一些其他步驟： 

1. 首先，在命名空間上運行 ns 查找。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 記下**非權威答案**部分中的名稱，該部分採用以下格式之一： 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. 運行每個帶有尾碼 s1、s2 和 s3 的 nslook，以獲取在三個可用性區域中運行的所有三個實例的 IP 位址， 

## <a name="apache-kafka-integration"></a>Apache Kafka 整合

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>如何將現有的 Kafka 應用程式與事件中樞整合？
事件中樞提供的 Kafka 端點可供您現有的 Apache Kafka 型應用程式使用。 只需進行設定變更，就能取得 PaaS Kafka 體驗。 它會提供替代方案來執行您自己的 Kafka 叢集。 事件中樞支援 Apache Kafka 1.0 和更新的用戶端版本，並且可與您現有的 Kafka 應用程式、工具及架構搭配使用。 如需詳細資訊，請參閱[適用於 Kafka 的事件中樞存放庫](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)。

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>需要針對我現有的應用程式進行哪些設定變更，才能與事件中樞通訊？
要連接到事件中心，您需要更新 Kafka 用戶端配置。 它通過創建事件中心命名空間和獲取[連接字串](event-hubs-get-connection-string.md)來完成。 變更 bootstrap.servers 以指向事件中樞 FQDN，並將連接埠變更為 9093。 更新 sasl.jaas.config 以將 Kafka 用戶端定向到事件中心終結點（即您獲得的連接字串），並進行正確的身份驗證，如下所示：

bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

範例：

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

注意：如果 sasl.jaas.config 不是框架中受支援的配置，請找到用於設置 SASL 使用者名和密碼的配置，然後改用這些配置。 將使用者名稱設定為 $ConnectionString，並將密碼設定為您的事件中樞連接字串。

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>事件中心的消息/事件大小是多少？
事件中心允許的最大消息大小為 1 MB。

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
自動擴充功能可讓您相應增加輸送量單位 (TU)。 這表示您一開始可以購買較低數目的 TU，然後自動擴充會隨著您的輸入增加而相應增加您的 TU。 它會為您提供符合成本效益的選項及可完全控制的 TU 數目，以進行管理。 此功能是一個**只能相應增加**的功能，而您可以藉由更新 TU 數目，完全控制 TU 數目的相應減少。 

您可能想要從較低的輸送量單位 (TU) 開始，例如 2 個 TU。 如果您預測流量可能增加到 15 個 TU，請在您的命名空間上開啟自動擴充功能，然後將上限設定為 15 個 TU。 您現在可讓 TU 隨著流量增加而自動增加。

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>當我開啟成本自動擴充功能時，是否有相關聯的成本？
此功能**無需相關費用**。 

### <a name="how-are-throughput-limits-enforced"></a>如何強制執行輸送量限制？
如果命名空間內所有事件中樞的輸入輸送量總計或輸入事件率總計超過彙總輸送量單位額度，傳送者將遭受節流處置並會收到錯誤，指出已超過輸入配額。

如果命名空間內所有事件中樞的輸出輸送量總計或輸出事件率總計超過彙總輸送量單位額度，接收者將遭受節流處置並會收到錯誤，指出已超過輸出配額。 輸入和輸出配額採單獨實施，如此一來，傳送者無法使事件取用速率變慢，接收者也不能阻止事件傳送到事件中樞。

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>可供保留/選取的輸送量單位 (TU) 數目是否有任何限制？
在多租用戶供應項目上，輸送量單位最多可以增加為 40 個 TU (您最多可以在入口網站中選取 20 個 TU，然後提出支援票證，在同一個命名空間中上將它提高為 40 個 TU)。 超過 40 個 TU，事件中樞就會提供稱為**事件中樞專用叢集**的資源/容量型模型。 專用叢集會以容量單位 (Cu) 來販售。

## <a name="dedicated-clusters"></a>專用叢集

### <a name="what-are-event-hubs-dedicated-clusters"></a>事件中樞專用叢集有哪些？
事件中樞專用叢集可為需求最高的客戶提供單一租用戶部署。 此供應項目會建置未透過輸送量單位繫結的容量型叢集。 這意味著您可以使用群集根據群集的 CPU 和記憶體使用方式來引入和資料流資料。 如需詳細資訊，請參閱[事件中樞專用叢集](event-hubs-dedicated-overview.md)。

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>單一容量單位可讓我達成哪些目標？
對於專用群集，您可以攝用和資料流多少取決於各種因素，例如您的生產者、消費者、攝入和處理的速度等等。 

下表顯示我們在測試期間達成的基準測試結果：

| 承載圖形 | 接收者 | 輸入頻寬| 輸入訊息 | 輸出頻寬 | 輸出訊息 | TU 總計 | 每個 CU 的 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 的批次 | 2 | 400 MB/秒 | 400k 消息/秒 | 800 MB/秒 | 800k 消息/秒 | 400 個 TU | 100 個 TU | 
| 10x10KB 的批次 | 2 | 666 MB/秒 | 66.6k 消息/秒 | 1.33 GB/秒 | 133k 消息/秒 | 666 個 TU | 166 個 TU |
| 6x32KB 的批次 | 1 | 1.05 GB/秒 | 34k 消息 / 秒 | 1.05 GB/秒 | 34k 消息/秒 | 1000 個 TU | 250 個 TU |

測試期間使用了下列準則：

- 使用了含四個容量單位 (CU) 的專用事件中樞叢集。 
- 用於擷取的事件中樞具有 200 個分割區。 
- 已擷取的資料會透過接收自所有分割區的兩個接收者應用程式來接收。

結果讓您了解可透過專用事件中樞叢集來達成的目標。 此外，專用叢集隨附已針對您的微批次和長期保留案例啟用的事件中樞擷取。

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>如何建立事件中樞專用叢集？
您可以藉由提交[加大配額支援要求](https://portal.azure.com/#create/Microsoft.Support)或連絡[事件中樞小組](mailto:askeventhubs@microsoft.com)，來建立事件中樞專用叢集。 通常需要大約兩週的時間，才能部署該叢集並將控制權移交給您以供使用。 此過程是臨時的，直到通過 Azure 門戶或 Azure 資源管理器範本提供完整的自助服務，這些範本部署群集大約需要兩個小時。

## <a name="best-practices"></a>最佳作法

### <a name="how-many-partitions-do-i-need"></a>我需要多少個分割區？
資料分割數目是在建立時指定，值必須介於 2 到 32 之間。 分區計數不可更改，因此在設置分區計數時應考慮長期比例。 資料分割是一種資料組織機制，與取用端應用程式所需的下游平行處理原則有關。 事件中樞內的資料分割數目，與您預期有的並行讀取器數目直接相關。 有關分區的詳細資訊，請參閱[分區](event-hubs-features.md#partitions)。

您可能希望將其設置為創建時的最高值，即 32。 請記住，具有多個分區將導致發送到多個分區的事件而不保留順序，除非您將發送方配置為僅發送到 32 個分區中的單個分區，使其餘 31 個分區變得冗余。 在前一種情況下，您必須讀取所有 32 個分區的事件。 在後一種情況下，除了在事件處理器主機上必須進行的額外配置外，沒有明顯的額外費用。

事件中樞的設計目的是讓每個取用者群組均可擁有單一分割區讀取器。 在大多數使用案例中，四個分割區的預設設定就已足夠。 如果要擴展事件處理，可能需要考慮添加其他分區。 分區上沒有特定的輸送量限制，但是命名空間中的聚合輸送量受輸送量單位數的限制。 當您提高命名空間中的輸送量單位數目時，可能需要額外的分割區，讓並行讀取器能夠達成它們自己的最大輸送量。

不過，如果在您的模型中應用程式對特定分割區具有同質性，則提高分割區數目可能不會為您帶來任何好處。 如需詳細資訊，請參閱[可用性和一致性](event-hubs-availability-and-consistency.md)。

## <a name="pricing"></a>定價

### <a name="where-can-i-find-more-pricing-information"></a>哪裡可以找到更多定價資訊？

如需事件中樞定價的完整資訊，請參閱[事件中樞定價詳細資料](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>保留事件中樞事件超過 24 小時需要計費嗎？

事件中樞標準層允許 24 小時以上的訊息保留期間，最多達七天。 如果儲存之事件總數的大小超過選定輸送量單位數目的儲存額度 (每個輸送量單位 84 GB)，超過額度的大小將以已發佈的 Azure Blob 儲存體費率計費。 即使您已將輸送量單位的最大輸入額度用盡，每個輸送量單位的儲存額度依然涵蓋 24 小時保留期間 (預設值) 的所有儲存費用。

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>事件中樞儲存空間大小如何計算及收費？

儲存在所有事件中樞內之事件，包括事件標頭的所有內部負荷或磁碟儲存結構的所有內部負荷，是以全天為單位來測量。 我們會在一天結束時計算儲存空間大小峰值。 每日儲存額度的計算乃以當天選定之輸送量單位的最小數目為基準 (每個輸送量單位提供 84 GB 的額度)。 如果總大小超過計算出來的每日儲存額度，我們會採用 Azure Blob 儲存體費率來計算超出的儲存空間 (依照**本地備援儲存體**費率)。

### <a name="how-are-event-hubs-ingress-events-calculated"></a>事件中樞輸入事件的計算方式為何？

每個傳送到事件中樞的事件都算是可計費訊息。 *輸入事件*的定義為小於或等於 64 KB 的資料單位。 任何大小小於或等於 64 KB 的事件均視為一個可計費事件。 如果事件大於 64 KB，可計費事件的數目乃根據事件大小來計算 (64 KB 的倍數)。 例如，一個傳送到事件中樞的 8 KB 事件將視為一個事件來計費，不過，一則傳送到事件中樞的 96 KB 訊息將視為兩個事件來計費。

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

事件中樞的技術支援可透過[社群論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)取得。 計費及訂用帳戶管理支援均為免費提供。

若要深入了解 SLA，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)頁面。

## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](event-hubs-what-is-event-hubs.md)
* [創建事件中心](event-hubs-create.md)
* [事件中樞自動擴大](event-hubs-auto-inflate.md)
