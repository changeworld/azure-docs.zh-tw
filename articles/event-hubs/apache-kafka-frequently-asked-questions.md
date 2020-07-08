---
title: 常見問題-適用于 Apache Kafka 的 Azure 事件中樞
description: 本文將說明使用不同通訊協定 (AMQP、Apache Kafka 及 HTTPS) 的取用者和產生者如何在使用 Azure 事件中樞時交換事件。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 066024f657e011aac1449106ec32689f145c5534
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314186"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>常見問題-適用于 Apache Kafka 的事件中樞 
本文提供一些關於遷移至 Apache Kafka 事件中樞常見問題的解答。

## <a name="do-you-run-apache-kafka"></a>您是否執行 Apache Kafka？

否。  我們會針對事件中樞基礎結構執行 Kafka API 作業。  由於 Apache Kafka 與事件中樞 AMQP 功能（也就是產生、接收、管理等等）之間有緊密的關聯性，因此我們能夠將已知的事件中樞可靠性帶入 Kafka PaaS 空間。

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>事件中樞取用者群組與 Kafka 取用者群組
事件中樞取用者群組與事件中樞上 Kafka 取用者群組之間的差異為何？ 事件中樞上的 Kafka 取用者群組完全有別于標準事件中樞取用者群組。

**事件中樞取用者群組**

- 它們是透過入口網站、SDK 或 Azure Resource Manager 範本，透過建立、取得、更新和刪除（CRUD）作業進行管理。 無法由事件中樞的取用者群組。
- 它們是事件中樞的子系實體。 這表示相同的取用者組名可以在相同命名空間中的事件中樞之間重複使用，因為它們是個別的實體。
- 它們不會用來儲存位移。 協調的 AMQP 耗用量是使用外部位移儲存體來完成，例如 Azure 儲存體。

**Kafka 取用者群組**

- 它們是由的。  您可以透過 Kafka 取用者群組 Api 來管理 Kafka 群組。
- 他們可以在事件中樞服務中儲存位移。
- 在有效的位移索引鍵/值存放區中，會使用它們做為索引鍵。 針對一組唯一的 `group.id` 和 `topic-partition` ，我們會在 Azure 儲存體（3倍複寫）中儲存位移。 事件中樞使用者不會因為儲存 Kafka 位移而產生額外的儲存成本。 位移會透過 Kafka 取用者群組 Api 來 manipulable，但不會直接顯示或 manipulable 事件中樞使用者的位移儲存體*帳戶*。  
- 它們會跨越命名空間。 針對多個主題的多個應用程式使用相同的 Kafka 組名，表示每當只有單一應用程式需要重新平衡時，就會重新平衡所有應用程式及其 Kafka 用戶端。  明智地選擇您的組名。
- 它們完全有別于事件中樞取用者群組。 您**不**需要使用 ' $Default '，也不需要擔心干擾 AMQP 工作負載的 Kafka 用戶端。
- 它們無法在 Azure 入口網站中看到。 取用者群組資訊可透過 Kafka Api 存取。

## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [適用于事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
- [適用于事件中樞的 Apache Kafka 遷移指南](apache-kafka-migration-guide.md)
- [適用于事件中樞的 Apache Kafka 疑難排解指南](apache-kafka-troubleshooting-guide.md)
- [建議設定](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

