---
title: 針對 Apache Kafka 的 Azure 事件中樞問題進行疑難排解
description: 本文說明如何針對 Apache Kafka 的 Azure 事件中樞問題進行疑難排解
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606726"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>適用于事件中樞的 Apache Kafka 疑難排解指南
本文提供在使用 Apache Kafka 的事件中樞時，您可能遇到之問題的疑難排解秘訣。 

## <a name="server-busy-exception"></a>伺服器忙碌例外狀況
由於 Kafka 節流，您可能會收到伺服器忙碌例外狀況。 使用 AMQP 用戶端時，事件中樞會在服務節流時立即傳回**伺服器忙碌**例外狀況。 這相當於「稍後再試一次」訊息。 在 Kafka 中，訊息會在完成之前延遲。 延遲長度會以毫秒為單位傳回`throttle_time_ms` ，如產生/提取回應中所示。 在大多數情況下，這些延遲的要求不會記錄為事件中樞儀表板上的 ServerBusy 例外狀況。 相反地，回應的`throttle_time_ms`值應該用來做為輸送量已超過布建配額的指標。

如果流量過高，服務就會具有下列行為：

- 如果產生要求的延遲超過要求超時，事件中樞會傳回**原則違規**的錯誤碼。
- 如果提取要求的延遲超過要求超時，事件中樞會將要求記錄為已節流，並以一組空的記錄回應，而不會有任何錯誤代碼。

[專用](event-hubs-dedicated-overview.md)叢集沒有節流機制。 您可以免費取用所有的叢集資源。

## <a name="no-records-received"></a>未收到任何記錄
您可能會看到取用者未取得任何記錄並不斷重新平衡。 在此案例中，取用者不會取得任何記錄，也不會持續重新平衡。 發生例外狀況或錯誤時，Kafka 記錄會顯示取用者正嘗試重新加入群組並指派磁碟分割。 有幾個可能的原因：

- 請確定您`request.timeout.ms`的至少是建議的值60000，而且至少`session.timeout.ms`是建議的值30000。 這些設定過低可能會導致取用者超時，因而導致重新平衡（因而導致更多的超時，因而導致重新平衡等） 
- 如果您的設定符合這些建議的值，而您仍看到持續重新平衡，請放心開啟問題（請務必在問題中包含整個設定，讓我們可以協助進行調試）！

## <a name="compressionmessage-format-version-issue"></a>壓縮/訊息格式版本問題
Kafka 支援壓縮，而適用于 Kafka 的事件中樞目前沒有。 當用戶端嘗試將壓縮的 Kafka 訊息傳送到我們`The message format version on the broker does not support the request.`的訊息代理程式時，會造成錯誤（例如）。

如果需要壓縮的資料，請在將資料傳送到訊息代理程式之前先將其壓縮，並在接收之後解壓縮，這是有效的解決方法。 訊息主體只是服務的位元組陣列，因此用戶端壓縮/解壓縮不會造成任何問題。

## <a name="unknownserverexception"></a>UnknownServerException
您可能會收到來自 Kafka 用戶端程式庫的 UnknownServerException，類似于下列範例： 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

向 Microsoft 支援服務開啟票證。  Debug 層級的記錄和 UTC 的例外狀況時間戳記有助於對問題進行調試。 

## <a name="other-issues"></a>其他問題
如果您在事件中樞上使用 Kafka 時看到問題，請檢查下列專案。

- **防火牆封鎖流量**-請確定您的防火牆未封鎖埠**9093** 。
- **TopicAuthorizationException** -此例外狀況最常見的原因包括：
    - 您的設定檔中的連接字串中有錯誤的錯誤，或
    - 嘗試在基本層命名空間上使用事件中樞 Kafka。 [只有標準和專用層命名空間支援](https://azure.microsoft.com/pricing/details/event-hubs/)Kafka 的事件中樞。
- **Kafka 版本不符**-適用于 Kafka 生態系統的事件中樞支援 Kafka 1.0 和更新版本。 某些使用 Kafka 0.10 和更新版本的應用程式有時可能會因為 Kafka 通訊協定的回溯相容性而無法使用，但我們強烈建議您不要使用舊的 API 版本。 Kafka 0.9 版和更早版本不支援所需的 SASL 通訊協定，也無法連接到事件中樞。
- **使用 Kafka 時，AMQP 標頭上的奇怪編碼**-當透過 AMQP 將事件傳送至事件中樞時，任何 AMQP 承載標頭都會以 AMQP 編碼方式序列化。 Kafka 取用者不會還原序列化 AMQP 的標頭。 若要讀取標頭值，請手動將 AMQP 標頭解碼。 或者，如果您知道您會透過 Kafka 通訊協定來使用，您可以避免使用 AMQP 標頭。 如需詳細資訊，請參閱[這個 GitHub 問題](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56) \(英文\)。
- **SASL 驗證**-讓您的架構與事件中樞所需的 sasl 驗證通訊協定進行合作，可能會比達到眼睛來得容易。 查看您是否可以在 SASL 驗證上使用架構的資源對設定進行疑難排解。 

## <a name="limits"></a>限制
Apache Kafka 與事件中樞 Kafka。 在大部分的情況下，Kafka 生態系統的事件中樞具有與 Apache Kafka 相同的預設值、屬性、錯誤碼和一般行為。 下面列出這兩個明確不同的實例（或事件中樞強加 Kafka 不是的限制）：

- `group.id`屬性的最大長度為256個字元
- 的大小上限`offset.metadata.max.bytes`為1024個位元組
- 位移認可會針對每個分割區以每秒4個呼叫進行節流，最大的內部記錄大小為 1 MB


## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [適用于事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
- [適用于事件中樞的 Apache Kafka 遷移指南](apache-kafka-migration-guide.md)
- [常見問題-適用于 Apache Kafka 的事件中樞](apache-kafka-frequently-asked-questions.md)
- [建議的設定](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
