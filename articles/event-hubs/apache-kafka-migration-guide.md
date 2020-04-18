---
title: 遷移到 Apache Kafka 的 Azure 事件中心
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
ms.openlocfilehash: 806a1f16327ad72a7f3527c813b355e1ba807dda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606752"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>遷移到阿帕奇卡夫卡生態系統的 Azure 事件中心
Azure 事件中心公開 Apache Kafka 終結點,使您能夠使用 Kafka 協定連接到事件中心。 通過對現有 Kafka 應用程式進行最少的更改,可以連接到 Azure 事件中心並享受 Azure 生態系統的優勢。 卡夫卡的事件中心支援[阿帕奇卡夫卡版本1.0](https://kafka.apache.org/10/documentation.html)及更高版本。

## <a name="pre-migration"></a>移轉前 

### <a name="create-an-azure-account"></a>建立 Azure 帳戶
如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

### <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
按照["創建事件中心](event-hubs-create.md)"文章中的分步說明創建事件中心命名空間和事件中心。 

### <a name="connection-string"></a>連接字串
按照門戶文章中獲取[連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)的步驟操作。 並且,記下連接字串,以便以後使用。 

### <a name="fully-qualified-domain-name-fqdn"></a>完整網域名稱 (FQDN)
您可能還需要指向事件中心命名空間的 FQDN。 FQDN 可以在連接字串中找到,如下所示:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

如果事件中心命名空間部署在非公共雲上,則功能變數名稱可能會有所不同(例如\*\*.servicebus.chinacloudapi.cn、.servicebus.usgovcloudapi.net\*或 .servicebus.cloudapi.de)。

## <a name="migration"></a>遷移 

### <a name="update-your-kafka-client-configuration"></a>更新卡夫卡客戶端設定

要連接到啟用卡夫卡的事件中心,您需要更新 Kafka 用戶端配置。 如果您在尋找您的應用程式時遇到問題,請試著在應用程式中搜尋設定的位置`bootstrap.servers`。

在應用程式中任何有意義的位置插入以下配置。 請確保更新`bootstrap.servers`和`sasl.jaas.config`值,以便使用正確的身份驗證將用戶端定向到事件中心 Kafka 終結點。 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

如果`sasl.jaas.config`框架中不支援的配置,請找到用於設置 SASL 使用者名和密碼的配置,然後改用它們。 將使用者名稱`$ConnectionString`和密碼設置為事件中心連接字串。

## <a name="post-migration"></a>移轉後
運行將事件發送到事件中心的 Kafka 應用程式。 然後,驗證事件中心是否使用 Azure 門戶接收事件。 在事件中心命名空間的 **「概述」** 頁上,切換到 **「指標」** 部分中的 **「消息**」檢視。 請重新整理頁面來更新圖表。 可能需要幾秒鐘的時間，頁面才會顯示已收到訊息。 

[![確認事件中樞已收到訊息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [事件中心的阿帕奇卡夫卡故障排除指南](apache-kafka-troubleshooting-guide.md)
- [常見問題 - 阿帕奇卡夫卡事件中心](apache-kafka-frequently-asked-questions.md)
- [Azure 活動中心 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
- [建議的設定](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
- 