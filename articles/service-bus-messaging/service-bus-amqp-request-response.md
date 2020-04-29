---
title: Azure 服務匯流排中的 AMQP 1.0 要求/回應作業
description: 本文定義 Microsoft Azure 服務匯流排中以要求/回應為基礎之作業的清單。
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: aschhab
ms.openlocfilehash: 338b411eade35fa357f64131c64b8ef2034c92b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76761078"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Microsoft Azure 服務匯流排中的 AMQP 1.0：要求/回應架構作業

本文定義 Microsoft Azure 服務匯流排要求/回應架構作業的清單。 這項資訊是根據 AMQP 管理版本 1.0 工作草稿。  
  
如需詳細的有線等級 AMQP 1.0 通訊協定指南，說明服務匯流排如何實作和建置 OASIS AMQP 技術規格，請參閱 [Azure 服務匯流排和事件中樞的 AMQP 1.0 通訊協定指南][AMQP 1.0 通訊協定指南]。  
  
## <a name="concepts"></a>概念  
  
### <a name="entity-description"></a>實體描述  

實體描述指是服務匯流排 [QueueDescription 類別](/dotnet/api/microsoft.servicebus.messaging.queuedescription)、[TopicDescription 類別](/dotnet/api/microsoft.servicebus.messaging.topicdescription)或 [SubscriptionDescription 類別](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)物件。  
  
### <a name="brokered-message"></a>代理訊息  

代表服務匯流排中對應至 AMQP 訊息的訊息。 此對應會在[服務匯流排 AMQP 通訊協定指南](service-bus-amqp-protocol-guide.md)中定義。  
  
## <a name="attach-to-entity-management-node"></a>附加至實體管理節點  

這份文件中所述的所有作業會遵循要求/回應模式、限定範圍至實體，並需要附加至實體管理節點。  
  
### <a name="create-link-for-sending-requests"></a>建立傳送要求的連結  

建立傳送要求的管理節點連結。  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>建立接收回應的連結  

建立連結，以便接收來自管理節點的回應。  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>傳輸要求訊息  

傳輸要求訊息。  
支援交易的作業可以選擇性地新增交易狀態。

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>接收回應訊息  

從回應連結接收回應訊息。  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
回應訊息的格式如下：
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>服務匯流排實體位址  

必須處理服務匯流排實體，如下所示︰  
  
|實體類型|位址|範例|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|主題|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|訂用帳戶|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>訊息作業  
  
### <a name="message-renew-lock"></a>訊息更新鎖定  

在實體描述中指定時擴充訊息的鎖定。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
 要求訊息本文必須由包含對應與下列項目的 amqp-value 區段所組成：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|UUID 的陣列|是|要更新的訊息鎖定權杖。|  

> [!NOTE]
> 鎖定權杖是接收之訊息上的 `DeliveryTag` 屬性。 請參閱 [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) 中的下列範例，它會擷取這些項目。 權杖可能也會在 'DeliveryAnnotations' 中以 'x-opt-lock-token' 的形式出現，但是不保證一定如此，而 `DeliveryTag` 是慣用的。 
> 
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗。|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息本文必須由包含對應與下列項目的 amqp-value 區段所組成：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|到期日|時間戳記的陣列|是|對應到要求鎖定權杖的訊息鎖定權杖新期限。|  
  
### <a name="peek-message"></a>查看訊息  

查看訊息而不用鎖定。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|是|要開始查看的序號。|  
|`message-count`|int|是|要查看的訊息數目上限。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 有多個訊息<br /><br /> 204︰沒有內容 – 沒有其他訊息|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|messages|對應的清單|是|當中每個對應都代表一則訊息的訊息清單。|  
  
代表訊息的對應必須包含下列項目：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|訊息|位元組的陣列|是|AMQP 1.0 連線編碼訊息。|  
  
### <a name="schedule-message"></a>排程訊息  

排程訊息。 此作業支援交易。
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|messages|對應的清單|是|當中每個對應都代表一則訊息的訊息清單。|  
  
代表訊息的對應必須包含下列項目：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|message-id|字串|是|`amqpMessage.Properties.MessageId` 為字串|  
|session-id|字串|否|`amqpMessage.Properties.GroupId as string`|  
|partition-key|字串|否|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|字串|否|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|訊息|位元組的陣列|是|AMQP 1.0 連線編碼訊息。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗。|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的對應：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|長整數的陣列|是|排定訊息的序號。 序號用來取消。|  
  
### <a name="cancel-scheduled-message"></a>取消排定的訊息  

取消排定的訊息。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|長整數的陣列|是|要取消之排程訊息的序號。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗。|  
|statusDescription|字串|否|狀態的描述。|   
  
## <a name="session-operations"></a>工作階段作業  
  
### <a name="session-renew-lock"></a>工作階段更新鎖定  

在實體描述中指定時擴充訊息的鎖定。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|session-id|字串|是|工作階段識別碼。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 有多個訊息<br /><br /> 204︰沒有內容 – 沒有其他訊息|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的對應：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|是|新的到期日。|  
  
### <a name="peek-session-message"></a>查看工作階段訊息  

查看工作階段訊息而不用鎖定。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|是|要開始查看的序號。|  
|message-count|int|是|要查看的訊息數目上限。|  
|session-id|字串|是|工作階段識別碼。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 有多個訊息<br /><br /> 204︰沒有內容 – 沒有其他訊息|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的對應：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|messages|對應的清單|是|當中每個對應都代表一則訊息的訊息清單。|  
  
 代表訊息的對應必須包含下列項目：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|訊息|位元組的陣列|是|AMQP 1.0 連線編碼訊息。|  
  
### <a name="set-session-state"></a>設定工作階段狀態  

設定工作階段的狀態。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|session-id|字串|是|工作階段識別碼。|  
|session-state|位元組的陣列|是|不透明的二進位資料。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗|  
|statusDescription|字串|否|狀態的描述。|  
  
### <a name="get-session-state"></a>取得工作階段狀態  

取得工作階段的狀態。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|session-id|字串|是|工作階段識別碼。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|session-state|位元組的陣列|是|不透明的二進位資料。|  
  
### <a name="enumerate-sessions"></a>列舉工作階段  

列舉訊息實體上的工作階段。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|是|篩選以在指定時間之後只包括更新的工作階段。|  
|skip|int|是|略過工作階段數。|  
|top|int|是|工作階段數上限。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 有多個訊息<br /><br /> 204︰沒有內容 – 沒有其他訊息|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|skip|int|是|如果狀態碼為 200 所略過的工作階段數。|  
|sessions-ids|字串的陣列|是|如果狀態碼為 200 的工作階段識別碼陣列。|  
  
## <a name="rule-operations"></a>規則作業  
  
### <a name="add-rule"></a>新增規則  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|rule-name|字串|是|規則名稱，不包括訂用帳戶和主題名稱。|  
|rule-description|map|是|如下一節中指定的規則描述。|  
  
**規則描述**對應必須包含下列專案，其中**sql 篩選**和相互**關聯篩選**互斥：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|是|`sql-filter`，如下一節中所指定。|  
|correlation-filter|map|是|`correlation-filter`，如下一節中所指定。|  
|sql-rule-action|map|是|`sql-rule-action`，如下一節中所指定。|  
  
sql-filter 對應必須包含下列項目：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|expression|字串|是|Sql 篩選運算式。|  
  
相互**關聯篩選**對應必須至少包含下列其中一個專案：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|correlation-id|字串|否||  
|message-id|字串|否||  
|to|字串|否||  
|reply-to|字串|否||  
|標籤|字串|否||  
|session-id|字串|否||  
|reply-to-session-id|字串|否||  
|Content-Type|字串|否||  
|properties|map|否|對應至服務匯流排 [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)。|  
  
[ **Sql-規則-動作**對應] 必須包含下列專案：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|expression|字串|是|Sql 動作運算式。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗|  
|statusDescription|字串|否|狀態的描述。|  
  
### <a name="remove-rule"></a>移除規則  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|rule-name|字串|是|規則名稱，不包括訂用帳戶和主題名稱。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗|  
|statusDescription|字串|否|狀態的描述。|  
  
### <a name="get-rules"></a>取得規則

#### <a name="request"></a>要求

要求訊息必須包含下列應用程式屬性：

|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  

要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|top|int|是|頁面中要提取的規則數目。|  
|skip|int|是|要略過的規則數目。 定義規則清單中的起始索引 (+ 1)。 | 

#### <a name="response"></a>回應

回應訊息包含下列屬性：

|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗|  
|規則| 對應的陣列|是|規則的陣列。 每個規則都是以對應表示。|

陣列中的每個對應項目都包含下列屬性：

|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|rule-description|描述物件的陣列|是|`com.microsoft:rule-description:list` 以及 AMQP 描述代碼 0x0000013700000004| 

`com.microsoft.rule-description:list` 是描述物件的陣列。 此陣列包含下列各項：

|索引|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
| 0 | 描述物件的陣列 | 是 | `filter` 詳述如下。 |
| 1 | 描述物件的陣列 | 是 | `ruleAction` 詳述如下。 |
| 2 | 字串 | 是 | 規則的名稱。 |

`filter` 可以是下列類型之一：

| 描述項名稱 | 描述項代碼 | 值 |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL 篩選 |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | 相互關聯篩選 |
| `com.microsoft:true-filter:list` | 0x000001370000007 | 表示 1=1 的 True 篩選 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | 表示 1=0 的 False 篩選 |

`com.microsoft:sql-filter:list` 是描述陣列，其中包括：

|索引|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
| 0 | 字串 | 是 | Sql 篩選運算式 |

`com.microsoft:correlation-filter:list` 是描述陣列，其中包括：

|索引 (若存在)|數值類型|值內容|  
|---------|----------------|--------------|
| 0 | 字串 | 相互關連識別碼 |
| 1 | 字串 | 訊息 ID |
| 2 | 字串 | 至 |
| 3 | 字串 | 回覆地址 |
| 4 | 字串 | 標籤 |
| 5 | 字串 | 工作階段識別碼 |
| 6 | 字串 | 回覆至工作階段識別碼|
| 7 | 字串 | 內容類型 |
| 8 | 對應 | 應用程式定義屬性的對應 |

`ruleAction` 可以是下列類型之一：

| 描述項名稱 | 描述項代碼 | 值 |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | 空白規則動作 - 沒有規則動作 |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL 規則動作 |

`com.microsoft:sql-rule-action:list` 是描述物件的陣列，它的第一個項目是字串，其中包含 SQL 規則動作的運算式。

## <a name="deferred-message-operations"></a>延遲的訊息作業  
  
### <a name="receive-by-sequence-number"></a>依照序號接收  

依序號接收延遲的訊息。  
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|長整數的陣列|是|序號。|  
|receiver-settle-mode|ubyte|是|如 AMQP core v1.0 中所指定的**接收者結算**模式。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗|  
|statusDescription|字串|否|狀態的描述。|  
  
回應訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|messages|對應的清單|是|當中每個對應都代表一則訊息的訊息清單。|  
  
代表訊息的對應必須包含下列項目：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|是|如果 `receiver-settle-mode` 為 1 則鎖定權杖。|  
|訊息|位元組的陣列|是|AMQP 1.0 連線編碼訊息。|  
  
### <a name="update-disposition-status"></a>更新配置狀態  

更新延遲訊息的配置狀態。 此作業支援交易。
  
#### <a name="request"></a>要求  

要求訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|operation (作業)|字串|是|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|否|作業伺服器逾時以毫秒為單位。|  
  
要求訊息內文必須由**amqp 值**區段組成，其中包含具有下列專案的**對應**：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|disposition-status|字串|是|完成<br /><br /> 放棄<br /><br /> 暫止|  
|lock-tokens|UUID 的陣列|是|要更新配置狀態的訊息鎖定權杖。|  
|deadletter-reason|字串|否|如果配置狀態設定為 **暫停** 則可能會設定。|  
|deadletter-description|字串|否|如果配置狀態設定為 **暫停** 則可能會設定。|  
|properties-to-modify|map|否|要修改的服務匯流排代理訊息屬性清單。|  
  
#### <a name="response"></a>回應  

回應訊息必須包含下列應用程式屬性：  
  
|Key|數值類型|必要|值內容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|是|HTTP 回應碼 [RFC2616]<br /><br /> 200：確定 – 成功，否則失敗|  
|statusDescription|字串|否|狀態的描述。|

## <a name="next-steps"></a>後續步驟

若要深入了解 AMQP 和服務匯流排，請造訪下列連結：

* [服務匯流排 AMQP 概觀]
* [AMQP 1.0 通訊協定指南]
* [Windows Server 服務匯流排中的 AMQP]

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[AMQP 1.0 通訊協定指南]: service-bus-amqp-protocol-guide.md
[Windows Server 服務匯流排中的 AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
