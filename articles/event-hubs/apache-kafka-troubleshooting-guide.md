---
title: 針對 Apache Kafka Azure 事件中樞的問題進行疑難排解
description: 本文說明如何針對 Apache Kafka Azure 事件中樞的問題進行疑難排解
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061422"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>適用于事件中樞的 Apache Kafka 疑難排解指南
本文提供使用適用于 Apache Kafka 的事件中樞時，您可能遇到之問題的疑難排解秘訣。 

## <a name="server-busy-exception"></a>伺服器忙碌例外狀況
由於 Kafka 節流，您可能會收到伺服器忙碌例外狀況。 使用 AMQP 用戶端時，事件中樞會在服務節流時立即傳回 **伺服器忙碌** 的例外狀況。 相當於「稍後再試一次」訊息。 在 Kafka 中，訊息會在完成之前延遲。 延遲長度會以毫秒為單位傳回，就像 `throttle_time_ms` 在產生/提取回應中一樣。 在大部分情況下，這些延遲的要求不會記錄為事件中樞儀表板上的伺服器忙碌例外狀況。 相反地，回應的 `throttle_time_ms` 值應該用來作為輸送量已超過布建配額的指標。

如果流量過多，服務會有下列行為：

- 如果產生要求的延遲超過要求超時，事件中樞會傳回 **原則違規** 錯誤碼。
- 如果 fetch 要求的延遲超過要求的超時時間，事件中樞會將要求記錄為已節流，並以空的記錄集回應，而且沒有錯誤碼。

[專用](event-hubs-dedicated-overview.md) 叢集沒有節流機制。 您可以隨意使用所有的叢集資源。

## <a name="no-records-received"></a>未收到任何記錄
您可能會看到取用者未取得任何記錄並不斷重新平衡。 在此案例中，取用者不會取得任何記錄並不斷重新平衡。 發生例外狀況或錯誤時，Kafka 記錄會顯示取用者正在嘗試重新加入群組並指派磁碟分割。 有幾個可能的原因：

- 請確定您 `request.timeout.ms` 至少是建議的60000值，而且 `session.timeout.ms` 至少是建議的30000值。 當這些設定過低時，可能會導致取用者超時，進而造成重新平衡 (，進而造成更多的的重新平衡，依此類推)  
- 如果您的設定符合這些建議的值，而且您仍看到持續重新平衡，則可以開啟問題 (請務必在問題中包含整個設定，讓我們可以協助進行) 的偵錯工具！

## <a name="compressionmessage-format-version-issue"></a>壓縮/訊息格式版本問題
Kafka 支援壓縮，而適用于 Kafka 的事件中樞目前不支援。 提及訊息格式版本的錯誤 (例如， `The message format version on the broker does not support the request.` 當用戶端嘗試將壓縮的 Kafka 訊息傳送給我們的訊息代理程式時，) 。

如果需要壓縮的資料，請先壓縮資料再將它傳送給訊息代理程式，並在接收後解壓縮，是有效的解決方法。 訊息主體只是服務的位元組陣列，因此用戶端壓縮/解壓縮不會造成任何問題。

## <a name="unknownserverexception"></a>UnknownServerException
您可能會收到來自 Kafka 用戶端程式庫的 UnknownServerException，類似于下列範例： 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

使用 Microsoft 支援服務開啟票證。  調試層級的記錄和 UTC 時間戳記（UTC）有助於對問題進行錯錯。 

## <a name="other-issues"></a>其他問題
如果您在事件中樞上使用 Kafka 時遇到問題，請檢查下列專案。

- **防火牆封鎖流量** -請確定您的防火牆未封鎖埠 **9093** 。
- **TopicAuthorizationException** -此例外狀況最常見的原因包括：
    - 您設定檔中的連接字串有打字錯誤，或
    - 嘗試在基本層命名空間上使用事件中樞進行 Kafka。 適用于 Kafka 的事件中樞功能 [只支援標準和專用層命名空間](https://azure.microsoft.com/pricing/details/event-hubs/)。
- **Kafka 版本不符** -Kafka 生態系統的事件中樞支援 Kafka 1.0 版和更新版本。 某些使用 Kafka 0.10 版和更新版本的應用程式有時可能會因為 Kafka 通訊協定的回溯相容性而無法運作，但強烈建議您不要使用舊的 API 版本。 Kafka 0.9 版和更早版本不支援所需的 SASL 通訊協定，且無法連線至事件中樞。
- **使用 Kafka 時，AMQP 標頭的奇怪編碼** 方式是在透過 AMQP 將事件傳送到事件中樞時，任何 AMQP 承載標頭都會以 AMQP 編碼方式序列化。 Kafka 取用者不會將標頭從 AMQP 還原序列化。 若要讀取標頭值，請手動解碼 AMQP 標頭。 或者，如果您知道將會透過 Kafka 通訊協定取用，則可以避免使用 AMQP 標頭。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)。
- 使用**sasl 驗證**：讓您的架構與事件中樞所需的 SASL 驗證通訊協定共同合作，可能會比能滿足您的需求更困難。 查看您是否可以在 SASL 驗證上使用架構的資源對設定進行疑難排解。 

## <a name="limits"></a>限制
Apache Kafka 與事件中樞 Kafka 的比較。 在大部分的情況下，Azure 事件中樞的 Kafka 介面具有與 Apache Kafka 相同的預設值、屬性、錯誤碼和一般行為。 這兩個明確不同 (或事件中樞強加 Kafka 未) 限制的實例如下所示：

- 屬性的最大長度 `group.id` 為256個字元
- 的大小上限 `offset.metadata.max.bytes` 為1024個位元組
- 每個分割區的位移認可會節流為每秒4個呼叫，且最大內部記錄檔大小為 1 MB


## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [適用于事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
- [適用于事件中樞的 Apache Kafka 遷移指南](apache-kafka-migration-guide.md)
- [常見問題-Apache Kafka 的事件中樞](apache-kafka-frequently-asked-questions.md)
- [建議設定](apache-kafka-configurations.md)
