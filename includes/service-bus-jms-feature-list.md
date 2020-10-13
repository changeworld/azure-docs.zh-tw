---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 9030080d0b8c8e032cb2992a62275efcdb04aabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798132"
---
下表列出 Azure 服務匯流排目前支援的 (JMS) 功能的 JAVA 訊息服務。 它也會顯示不支援的功能。


| 功能 | API |狀態 |
|---|---|---|
| 佇列   | <ul> <li> JMSCoNtext. createQueue ( String queueName)  </li> </ul>| **支援** |
| 主題   | <ul> <li> JMSCoNtext. createTopic ( String topicName)  </li> </ul>| **支援** |
| 暫存佇列 |<ul> <li> JMSCoNtext. createTemporaryQueue ( # A1 </li> </ul>| **支援** |
| 暫存主題 |<ul> <li> JMSCoNtext. createTemporaryTopic ( # A1 </li> </ul>| **支援** |
| 訊息生產者/<br/> JMSProducer |<ul> <li> JMSCoNtext. createProducer ( # A1 </li> </ul>| **支援** |
| 佇列瀏覽器 |<ul> <li> JMSCoNtext. createBrowser (佇列佇列)  </li> <li> JMSCoNtext. createBrowser (佇列佇列，字串 messageSelector)  </li> </ul> | **支援** |
| 訊息取用者/ <br/> JMSConsumer | <ul> <li> JMSCoNtext. createConsumer ( 目的地目的地)  </li> <li> JMSCoNtext. createConsumer ( 目的地目的地，字串 messageSelector)  </li> <li> JMSCoNtext. createConsumer ( 目的地目的地，字串 messageSelector，布林值 noLocal) </li> </ul>  <br/> 目前不支援 noLocal | **支援** |
| 共用持久訂閱 | <ul> <li> JMSCoNtext. createSharedDurableConsumer (主題主題，字串名稱)  </li> <li> JMSCoNtext. createSharedDurableConsumer (主題主題、字串名稱、字串 messageSelector)  </li> </ul>| **支援**|
| 未共用的持久訂閱 | <ul> <li> JMSCoNtext. createDurableConsumer (主題主題，字串名稱)  </li> <li> createDurableConsumer (主題主題、字串名稱、字串 messageSelector、布林 noLocal)  </li> </ul> <br/> 目前不支援 noLocal，應設定為 false | **支援** |
| 共用的非持久訂閱 |<ul> <li> JMSCoNtext. createSharedConsumer (主題主題，字串 sharedSubscriptionName)  </li> <li> JMSCoNtext. createSharedConsumer (主題主題，字串 sharedSubscriptionName，字串 messageSelector)  </li> </ul> | **支援** |
| 未共用的非持久訂閱 |<ul> <li> JMSCoNtext. createConsumer (目的地目的地)  </li> <li> JMSCoNtext. createConsumer ( 目的地目的地，字串 messageSelector)  </li> <li> JMSCoNtext. createConsumer ( 目的地目的地，字串 messageSelector，布林值 noLocal)  </li> </ul> <br/> 目前不支援 noLocal，應設定為 false | **支援** |
| 訊息選取器 | 取決於建立的取用者 | **支援** |
|  (排程訊息的傳遞延遲)  | <ul> <li> JMSProducer. setDeliveryDelay ( long deliveryDelay)  </li> </ul>|**支援**|
| 已建立訊息 |<ul> <li> JMSCoNtext. createMessage ( # A1 </li> <li> JMSCoNtext. createBytesMessage ( # A1 </li> <li> JMSCoNtext. createMapMessage ( # A1 </li> <li> JMSCoNtext. createObjectMessage ( 可序列化物件)  </li> <li> JMSCoNtext. createStreamMessage ( # A1 </li> <li> JMSCoNtext. createTextMessage ( # A1 </li> <li> JMSCoNtext. createTextMessage ( 字串文字)  </li> </ul>| **支援** |
| 分散式交易 || 不支援 |
