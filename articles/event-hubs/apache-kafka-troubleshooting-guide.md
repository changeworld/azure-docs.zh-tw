---
title: 阿帕契卡夫卡 Azure 事件中心的問題排除問題
description: 本文展示如何解決 Apache Kafka 的 Azure 事件中心的問題
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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606726"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>事件中心的阿帕奇卡夫卡故障排除指南
本文提供有關在使用 Apache Kafka 事件中心時可能會遇到的問題的疑難解答提示。 

## <a name="server-busy-exception"></a>伺服器忙碌
由於 Kafka 限制,您可能會收到伺服器忙異常。 對於 AMQP 用戶端,事件中心在服務限制時立即返回**伺服器忙**異常。 它等效於「稍後再試」 消息。 在卡夫卡,消息在完成之前會延遲。 延遲長度返回以毫秒為單位,如`throttle_time_ms`在生產/提取回應中。 在大多數情況下,這些延遲請求不會記錄為事件中心儀錶板上的 ServerBusy 異常。 相反,回應`throttle_time_ms`的值應用作輸送量超過預配配額的指示器。

如果流量過大,則服務具有以下行為:

- 如果生成請求的延遲超過請求超時,事件中心將返回**策略衝突**錯誤代碼。
- 如果提取請求的延遲超過請求超時,事件中心將請求記錄為受限,並回應空記錄集和無錯誤代碼。

[專用群集](event-hubs-dedicated-overview.md)沒有限制機制。 您可以自由使用所有群集資源。

## <a name="no-records-received"></a>未收到任何紀錄
您可能會看到消費者沒有得到任何記錄,並不斷重新平衡。 在這種情況下,消費者不會獲得任何記錄並不斷重新平衡。 發生異常或錯誤時,沒有異常或錯誤,但 Kafka 日誌將顯示消費者在嘗試重新加入組並分配分區時遇到卡住。 有幾個可能的原因:

- 請確保您的推薦值`request.timeout.ms`至少為 60000,`session.timeout.ms`並且您的 建議值至少為 30000。 這些設置過低可能會導致消費者超時,進而導致重新平衡(進而導致更多超時,從而導致更多重新平衡,等等) 
- 如果您的設定與這些建議值匹配,並且您仍然看到不斷的重新平衡,請隨時打開問題(請確保在問題中包括您的整個配置,以便我們可以幫助調試)!

## <a name="compressionmessage-format-version-issue"></a>壓縮/訊息格式版本問題
卡夫卡支援壓縮,卡夫卡的活動中心目前不支援壓縮。 當用戶端嘗試向我們的經紀人發送壓縮 Kafka`The message format version on the broker does not support the request.`消息時,將會導致提及消息格式版本(例如),錯誤。

如果需要壓縮數據,在將數據發送到代理之前壓縮數據並在接收後進行解壓縮是一種有效的解決方法。 消息正文只是服務的位元組,因此用戶端壓縮/解壓縮不會導致任何問題。

## <a name="unknownserverexception"></a>未知伺服器例外
您可能會收到來自 Kafka 用戶端函式庫的未知伺服器例外,類似於以下範例: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

在 Microsoft 支援下打開票證。  UTC 中的調試級日誌記錄和異常時間戳有助於調試問題。 

## <a name="other-issues"></a>其他問題
如果您在事件中心上使用 Kafka 時看到問題,請檢查以下專案。

- **防火牆阻止流量**- 確保埠**9093**未被防火牆阻止。
- **主題授權例外**- 此例外的最常見原因是:
    - 設定檔中連接字串中的拼字串, 或
    - 嘗試在基本層命名空間上使用 Kafka 的事件中心。 Kafka 的事件中心[只支援標準和專用層命名空間](https://azure.microsoft.com/pricing/details/event-hubs/)。
- **卡夫卡版本不匹配**- 卡夫卡生態系統的事件中心支援卡夫卡版本 1.0 及更高版本。 由於 Kafka 協定的向後相容性,某些使用 Kafka 版本 0.10 和更高版本的應用程式有時可能工作,但我們強烈建議不要使用舊的 API 版本。 Kafka 版本 0.9 和更早版本不支援所需的 SASL 協定,並且無法連接到事件中心。
- **使用 Kafka 時,AMQP 標頭上的奇怪編碼**- 透過 AMQP 將事件發送到事件中心時,任何 AMQP 有效負載標頭都將在 AMQP 編碼中序列化。 卡夫卡消費者不會從 AMQP 中解序列化標頭。 要讀取標頭值,請手動解碼 AMQP 標頭。 或者,如果您知道將通過 Kafka 協定使用 AMQP 標頭,則可以避免使用 AMQP 標頭。 如需詳細資訊，請參閱[這個 GitHub 問題](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56) \(英文\)。
- **SASL 身份驗證**- 讓架構與事件中心所需的 SASL 身份驗證協定協作可能比滿足眼睛更難。 查看是否可以使用 SASL 身份驗證上的框架資源對配置進行故障排除。 

## <a name="limits"></a>限制
阿帕契卡夫卡 vs. 事件中心卡夫卡。 在大多數情況下,卡夫卡生態系統的事件中心與 Apache Kafka 具有相同的預設值、屬性、錯誤代碼和常規行為。 下面列出了這兩個顯式不同的實例(或事件中心施加 Kafka 未施加的限制的情況):

- `group.id`屬性的最大長度為 256 個字元
- 最大大小`offset.metadata.max.bytes`為 1024 位元組
- 偏移提交在每次分區 4 次調用/秒時受到限制,最大內部日誌大小為 1 MB


## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [阿帕奇卡夫卡活動中心開發人員指南](apache-kafka-developer-guide.md)
- [事件中心的阿帕奇卡夫卡遷移指南](apache-kafka-migration-guide.md)
- [常見問題 - 阿帕奇卡夫卡事件中心](apache-kafka-frequently-asked-questions.md)
- [建議的設定](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
