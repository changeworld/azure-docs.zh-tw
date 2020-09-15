---
title: 常見問題-Apache Kafka 的 Azure 事件中樞
description: 本文將針對未涵蓋于其他地方的 Apache Kafka 用戶端支援，回答有關 Azure 事件中樞的常見問題。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ca54bf314d795b88b727ddb648f3e1e74133fd3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061456"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>常見問題-Apache Kafka 的事件中樞 
本文提供有關遷移至適用于 Apache Kafka 的事件中樞常見問題的解答。

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Azure 事件中樞在 Apache Kafka 上執行嗎？

不會。 Azure 事件中樞是雲端原生的多層式代理程式，其支援由 Microsoft 開發及維護的多個通訊協定，且不會使用任何 Apache Kafka 程式碼。 其中一個支援的通訊協定是 Kafka 用戶端消費者和生產者 Api 的 Kafka RPC 通訊協定。 事件中樞適用于許多現有的 Kafka 應用程式。 如需詳細資訊，請參閱 [適用于 Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)。 由於 Apache Kafka 和 Azure 事件中樞的概念非常類似 (但) 不同，因此我們可以為具有現有 Apache Kafka 投資的客戶提供無可匹敵的 Azure 事件中樞可靠性。 

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>事件中樞取用者群組與 Kafka 取用者群組
事件中樞取用者群組和事件中樞上的 Kafka 取用者群組之間有何差異？ 事件中樞上的 Kafka 取用者群組與標準事件中樞取用者群組完全不同。

**事件中樞取用者群組**

- 您可以透過入口網站、SDK 或 Azure Resource Manager 範本，透過建立、取得、更新和刪除 (CRUD) 作業來管理它們。 無法自動建立事件中樞取用者群組。
- 它們是事件中樞的子系實體。 這表示相同的取用者組名可以在相同命名空間中的事件中樞之間重複使用，因為它們是個別的實體。
- 它們不會用來儲存位移。 協調的 AMQP 耗用量是使用外部位移儲存體來完成，例如 Azure 儲存體。

**Kafka 取用者群組**

- 它們是自動建立。  您可以透過 Kafka 取用者群組 Api 來管理 Kafka 群組。
- 他們可以在事件中樞服務中儲存位移。
- 它們在有效的位移索引鍵/值存放區中，用來做為索引鍵。 針對與的唯一配對 `group.id` `topic-partition` ，我們會在 Azure 儲存體 (3 倍複寫) 中儲存位移。 事件中樞使用者不會因為儲存 Kafka 位移而產生額外的儲存成本。 位移是透過 Kafka 取用者群組 Api 來 manipulable，但不會直接顯示或 manipulable 事件中樞使用者的位移儲存體 *帳戶* 。  
- 它們橫跨命名空間。 針對多個主題的多個應用程式使用相同的 Kafka 組名，表示每當只有單一應用程式需要重新平衡時，所有應用程式及其 Kafka 用戶端就會重新平衡。  明智地選擇您的組名。
- 它們與事件中樞取用者群組完全不同。 您 **不** 需要使用 ' $Default '，也不需要擔心 KAFKA 干擾 AMQP 工作負載的用戶端。
- 它們無法在 Azure 入口網站中看到。 取用者群組資訊可透過 Kafka Api 來存取。

## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [適用于事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
- [適用于事件中樞的 Apache Kafka 遷移指南](apache-kafka-migration-guide.md)
- [適用于事件中樞的 Apache Kafka 疑難排解指南](apache-kafka-troubleshooting-guide.md)
- [建議設定](apache-kafka-configurations.md)

