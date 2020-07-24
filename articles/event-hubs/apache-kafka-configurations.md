---
title: Apache Kafka 用戶端的建議設定-Azure 事件中樞
description: 本文提供與 Apache Kafka 的 Azure 事件中樞進行互動之用戶端的建議 Apache Kafka 設定。
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096132"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Apache Kafka 用戶端的建議設定
以下是使用 Apache Kafka 用戶端應用程式中 Azure 事件中樞的建議設定。 

## <a name="java-client-configuration-properties"></a>JAVA 用戶端設定屬性

### <a name="producer-and-consumer-configurations"></a>生產者和取用者設定

屬性 | 建議值 | 允許的範圍 | 附註
---|---:|-----:|---
`metadata.max.age.ms` | 180000（近似） | < 240000 | 可以降低，以便提早收取中繼資料變更。
`connections.max.idle.ms`   | 180000 | < 240000 | Azure 會關閉輸入 TCP 閒置 > 240000 毫秒，這可能會導致傳送無作用的連線（因為傳送超時而顯示為過期的批次）。

### <a name="producer-configurations-only"></a>僅限生產者設定
[在這裡](https://kafka.apache.org/documentation/#producerconfigs)可以找到產生者的內容。

屬性 | 建議值 | 允許的範圍 | 附註
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | 如果傳送的要求大於1046528個位元組，服務將會關閉連接。  *此值**必須**變更，而且會導致高輸送量產生案例的問題。*
`retries` | > 0 | | 可能需要增加 delivery.timeout.ms 值，請參閱檔。
`request.timeout.ms` | 30000.。 60000 | > 20000| EH 會在內部預設為至少20000毫秒。  *雖然接受較低 timeout 值的要求，但不保證用戶端行為。*
`metadata.max.idle.ms` | 180000 | > 5000 | 控制生產者將為閒置的主題快取中繼資料的時間長度。 如果自主題上次產生以來經過的時間超過中繼資料閒置持續時間，則會忘記該主題的中繼資料，而它的下一次存取將會強制執行中繼資料提取要求。
`linger.ms` | > 0 | | 針對高輸送量案例，逗留值應該等於最高容許值，以充分利用批次處理。
`delivery.timeout.ms` | | | 根據公式（ `request.timeout.ms`  +  `linger.ms` ） * 進行設定 `retries` 。
`enable.idempotence` | false | | 目前不支援等冪性。
`compression.type` | `none` | | 目前不支援壓縮。。

### <a name="consumer-configurations-only"></a>僅限取用者設定
您可以在[這裡](https://kafka.apache.org/documentation/#consumerconfigs)找到取用者的內容。

屬性 | 建議值 | 允許的範圍 | 附註
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000是預設值，不應變更。
`session.timeout.ms` | 30000 |6000.。 300000| 從30000開始，如果因為遺漏了心跳，而看到頻繁的重新平衡，請增加。


## <a name="librdkafka-configuration-properties"></a>librdkafka 設定屬性
主要 `librdkafka` 設定檔（[連結](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)）包含下列屬性的延伸描述。

### <a name="producer-and-consumer-configurations"></a>生產者和取用者設定

屬性 | 建議值 | 允許的範圍 | 附註
---|---:|-----:|---
`socket.keepalive.enable` | true | | 如果連接預期閒置，則為必要。  Azure 會關閉輸入 TCP 閒置 > 240000 毫秒。
`metadata.max.age.ms` | ~ 180000| < 240000 | 可以降低，以便提早收取中繼資料變更。

### <a name="producer-configurations-only"></a>僅限生產者設定

屬性 | 建議值 | 允許的範圍 | 附註
---|---:|-----:|---
`retries` | > 0 | | 預設值為 2。 我們建議您保留此值。 
`request.timeout.ms` | 30000.。 60000 | > 20000| EH 會在內部預設為至少20000毫秒。  `librdkafka`預設值為5000，這可能會造成問題。 *雖然接受較低 timeout 值的要求，但不保證用戶端行為。*
`partitioner` | `consistent_random` | 請參閱 librdkafka 檔 | `consistent_random`是預設值，而且是最佳做法。  在大部分情況下，會以理想的方式處理空的和 null 索引鍵。
`enable.idempotence` | false | | 目前不支援等冪性。
`compression.codec` | `none` || 目前不支援壓縮。

### <a name="consumer-configurations-only"></a>僅限取用者設定

屬性 | 建議值 | 允許的範圍 | 附註
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000是預設值，不應變更。
`session.timeout.ms` | 30000 |6000.。 300000| 從30000開始，如果因為遺漏了心跳，而看到頻繁的重新平衡，請增加。


## <a name="further-notes"></a>進一步注意事項

請檢查下表中常見的設定相關錯誤案例。

徵狀 | 問題 | 解決方案
----|---|-----
因為重新平衡而導致位移認可失敗 | 您的取用者在呼叫輪詢（）時等待的時間太長，而服務會將取用者從群組開始。 | 您有幾種選項： <ul><li>增加會話超時</li><li>減少訊息批次大小以加速處理</li><li>改善處理平行化以避免封鎖取用者。輪詢（）</li></ul> 套用這三種組合可能會 wisest。
高的網路例外狀況產生輸送量 | 您使用的是 JAVA 用戶端 + 預設的最大要求大小嗎？  您的要求可能太大。 | 請參閱上述的 JAVA。

## <a name="next-steps"></a>後續步驟
請參閱 [Azure 訂用帳戶和服務限制、配額與限制](..//azure-resource-manager/management/azure-subscription-service-limits.md)，以了解 Azure 服務的配額和限制。 
