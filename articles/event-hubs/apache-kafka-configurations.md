---
title: Apache Kafka 用戶端的建議設定-Azure 事件中樞
description: 本文提供建議的 Apache Kafka 設定，讓用戶端與 Apache Kafka 的 Azure 事件中樞互動。
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096132"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>適用于 Apache Kafka 用戶端的建議設定
以下是從 Apache Kafka 用戶端應用程式使用 Azure 事件中樞的建議設定。 

## <a name="java-client-configuration-properties"></a>JAVA 用戶端設定屬性

### <a name="producer-and-consumer-configurations"></a>生產者和取用者設定

屬性 | 建議值 | 允許的範圍 | 注意
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (大約)  | < 240000 | 可以減少，以便更快挑選中繼資料變更。
`connections.max.idle.ms`   | 180000 | < 240000 | Azure 會關閉輸入 TCP 閒置 > 240000 ms，這可能會導致因為傳送超時) ，而導致傳送過期的連接 (顯示為過期的批次。

### <a name="producer-configurations-only"></a>僅限生產者設定
您可以在 [這裡](https://kafka.apache.org/documentation/#producerconfigs)找到生產者的進行。

屬性 | 建議值 | 允許的範圍 | 注意
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | 如果傳送超過1046528個位元組的要求，服務會關閉連接。  *此值 **必須** 變更，而且會在高輸送量產生案例中造成問題。*
`retries` | > 0 | | 可能需要增加 delivery.timeout.ms 值，請參閱檔。
`request.timeout.ms` | 30000 .。。 60000 | > 20000| EH 將在內部預設為最少20000毫秒。  *如果接受具有較低 timeout 值的要求，則不保證用戶端行為。*
`metadata.max.idle.ms` | 180000 | > 5000 | 控制生產者將為閒置的主題快取中繼資料的時間長度。 如果自上一次產生主題以來經過的時間超過中繼資料閒置時間，則會忘記該主題的中繼資料，而它的下一個存取將會強制執行中繼資料提取要求。
`linger.ms` | > 0 | | 針對高輸送量案例，延遲值應等於最高可容忍值，以利用批次處理。
`delivery.timeout.ms` | | | 根據公式 (`request.timeout.ms`  +  `linger.ms`) * 進行設定 `retries` 。
`enable.idempotence` | false | | 目前不支援等冪性。
`compression.type` | `none` | | 目前不支援壓縮。

### <a name="consumer-configurations-only"></a>僅取用者設定
您可以在 [這裡](https://kafka.apache.org/documentation/#consumerconfigs)找到取用者的內容。

屬性 | 建議值 | 允許的範圍 | 注意
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000是預設值，不應變更。
`session.timeout.ms` | 30000 |6000 .。。 300000| 從30000開始，如果因為遺失的心跳而頻繁地重新平衡，則會增加。


## <a name="librdkafka-configuration-properties"></a>librdkafka 設定屬性
主要 `librdkafka` 設定檔 ([連結](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) 包含下列屬性的延伸描述。

### <a name="producer-and-consumer-configurations"></a>生產者和取用者設定

屬性 | 建議值 | 允許的範圍 | 注意
---|---:|-----:|---
`socket.keepalive.enable` | true | | 如果預期連接需要閒置，則為必要項。  Azure 會關閉輸入 TCP 閒置 > 240000 毫秒。
`metadata.max.age.ms` | ~ 180000| < 240000 | 可以減少，以便更快挑選中繼資料變更。

### <a name="producer-configurations-only"></a>僅限生產者設定

屬性 | 建議值 | 允許的範圍 | 注意
---|---:|-----:|---
`retries` | > 0 | | 預設值為 2。 建議您保留這個值。 
`request.timeout.ms` | 30000 .。。 60000 | > 20000| EH 將在內部預設為最少20000毫秒。  `librdkafka` 預設值為5000，這可能會有問題。 *如果接受具有較低 timeout 值的要求，則不保證用戶端行為。*
`partitioner` | `consistent_random` | 請參閱 librdkafka 檔 | `consistent_random` 是預設值，而且是最佳的。  在大部分情況下，會處理空白和 null 索引鍵。
`enable.idempotence` | false | | 目前不支援等冪性。
`compression.codec` | `none` || 目前不支援壓縮。

### <a name="consumer-configurations-only"></a>僅取用者設定

屬性 | 建議值 | 允許的範圍 | 注意
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000是預設值，不應變更。
`session.timeout.ms` | 30000 |6000 .。。 300000| 從30000開始，如果因為遺失的心跳而頻繁地重新平衡，則會增加。


## <a name="further-notes"></a>其他注意事項

檢查下列常見設定相關錯誤案例的表格。

徵兆 | 問題 | 解決方法
----|---|-----
因為重新平衡，所以位移認可失敗 | 您的取用者在輪詢 ( # A1 的呼叫之間等候太長，而服務正在開始群組中的取用者。 | 您有幾種選項： <ul><li>增加會話超時時間</li><li>減少訊息批次大小以加速處理</li><li>改善處理平行處理，以避免封鎖取用者。輪詢 ( # A1</li></ul> 套用這三種三種組合可能會 wisest。
高產生輸送量的網路例外狀況 | 您使用的是 JAVA 用戶端 + 預設最大要求大小。  您的要求可能太大。 | 請參閱上述的 JAVA 進行。

## <a name="next-steps"></a>後續步驟
請參閱 [Azure 訂用帳戶和服務限制、配額與限制](..//azure-resource-manager/management/azure-subscription-service-limits.md)，以了解 Azure 服務的配額和限制。 
