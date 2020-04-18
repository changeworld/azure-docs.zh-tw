---
title: 常見問題 - 阿帕奇卡夫卡的 Azure 事件中心
description: 本文將說明使用不同通訊協定 (AMQP、Apache Kafka 及 HTTPS) 的取用者和產生者如何在使用 Azure 事件中樞時交換事件。
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
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606739"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>常見問題 - 阿帕奇卡夫卡事件中心 
本文提供了一些常見問題的解答,這些常見問題是遷移到 Apache Kafka 的事件中心。

## <a name="do-you-run-apache-kafka"></a>你經營阿帕奇卡夫卡嗎?

否。  我們針對事件中心基礎結構執行 Kafka API 操作。  由於 Apache Kafka 和事件中心 AMQP 功能(即生成、接收、管理等)之間存在緊密關聯,因此我們能夠將事件中心的已知可靠性引入 Kafka PaaS 空間。

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>事件中心消費者組與卡夫卡消費者組
事件中心消費者組和事件中心上的 Kafka 消費者組之間的區別是什麼? 事件中心的 Kafka 消費者組與標準事件中心消費者組完全不同。

**事件中心消費者組**

- 通過門戶、SDK 或 Azure 資源管理器範本創建、檢索、更新和刪除 (CRUD) 操作管理它們。 無法自動創建事件中心消費者組。
- 它們是事件中心的子實體。 這意味著同一使用者組名稱可以在同一命名空間中的事件中心之間重複使用,因為它們是單獨的實體。
- 它們不用於存儲偏移量。 協調的 AMQP 消耗使用外部偏移儲存(例如 Azure 存儲)完成。

**卡夫卡消費群體**

- 它們是自動創建的。  卡夫卡組可以通過卡夫卡消費群體 API 進行管理。
- 它們可以在事件中心服務中存儲偏移量。
- 它們用作有效偏移鍵值存儲中的鍵。 對於唯一的`group.id``topic-partition`和 ,我們在 Azure 存儲(3 倍複製)中存儲偏移量。 事件中心使用者不會因存儲 Kafka 偏移而產生額外的存儲成本。 偏移可以通過 Kafka 消費者組 API 進行操縱,但偏移存儲*帳戶*對於事件中心用戶來說不是直接可見或可操作的。  
- 它們跨越一個命名空間。 對多個主題上的多個應用程式使用相同的 Kafka 組名稱意味著,只要單個應用程式需要重新平衡,所有應用程式及其 Kafka 用戶端都將重新平衡。  明智地選擇群組名稱。
- 它們與事件中心消費者組完全不同。 您**無需**使用"$Default",也無需擔心 Kafka 客戶端干擾 AMQP 工作負載。
- 它們無法在 Azure 門戶中查看。 可通過 Kafka API 訪問消費者組資訊。

## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [阿帕奇卡夫卡活動中心開發人員指南](apache-kafka-developer-guide.md)
- [事件中心的阿帕奇卡夫卡遷移指南](apache-kafka-migration-guide.md)
- [事件中心的阿帕奇卡夫卡故障排除指南](apache-kafka-troubleshooting-guide.md)
- [建議的設定](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

