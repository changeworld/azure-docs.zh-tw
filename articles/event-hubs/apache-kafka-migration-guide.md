---
title: 遷移至適用于 Apache Kafka 的 Azure 事件中樞
description: 本文將說明使用不同通訊協定 (AMQP、Apache Kafka 及 HTTPS) 的取用者和產生者如何在使用 Azure 事件中樞時交換事件。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8f6c4cbdcbbc1d589b0803f36305f9a9fe6eebfa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322743"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>遷移到 Apache Kafka 生態系統的 Azure 事件中樞
Azure 事件中樞公開 Apache Kafka 端點，可讓您使用 Kafka 通訊協定連接到事件中樞。 藉由對現有的 Kafka 應用程式進行最少的變更，您可以連接到 Azure 事件中樞，並獲得 Azure 生態系統的優點。 適用于 Kafka 的事件中樞支援[Apache Kafka 1.0 版](https://kafka.apache.org/10/documentation.html)和更新版本。

## <a name="pre-migration"></a>移轉前 

### <a name="create-an-azure-account"></a>建立 Azure 帳戶
如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

### <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
遵循[建立事件中樞](event-hubs-create.md)一文中的逐步指示，建立事件中樞命名空間和事件中樞。 

### <a name="connection-string"></a>連接字串
遵循從[入口網站取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)一文中的步驟。 並記下連接字串，以供稍後使用。 

### <a name="fully-qualified-domain-name-fqdn"></a>完整網域名稱 (FQDN)
您也可能需要指向事件中樞命名空間的 FQDN。 您可以在連接字串中找到 FQDN，如下所示：

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

如果您的事件中樞命名空間部署在非公用雲端上，您的功能變數名稱可能會有所不同（例如， \* . servicebus.chinacloudapi.cn、 \* . servicebus.usgovcloudapi.net 或 \* servicebus.cloudapi.de）。

## <a name="migration"></a>移轉 

### <a name="update-your-kafka-client-configuration"></a>更新您的 Kafka 用戶端設定

若要連接到已啟用 Kafka 的事件中樞，您必須更新 Kafka 用戶端設定。 如果您找不到自己的問題，請嘗試搜尋在 `bootstrap.servers` 應用程式中設定的位置。

在您的應用程式中有意義的地方插入下列各項。 請務必更新 `bootstrap.servers` 和值， `sasl.jaas.config` 以使用正確的驗證將用戶端導向您的事件中樞 Kafka 端點。 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

如果 `sasl.jaas.config` 在您的架構中不是支援的設定，請尋找用來設定 SASL 使用者名稱和密碼的設定，並改為使用它們。 將 [使用者名稱] 設為 `$ConnectionString` ，並將密碼設定為您事件中樞連接字串。

## <a name="post-migration"></a>移轉後
執行 Kafka 應用程式，以將事件傳送至事件中樞。 然後，使用 Azure 入口網站確認事件中樞是否收到事件。 在事件中樞命名空間的 [**總覽**] 頁面上，切換至 [**計量**] 區段中的 [**訊息**] 視圖。 請重新整理頁面來更新圖表。 可能需要幾秒鐘的時間，頁面才會顯示已收到訊息。 

[![確認事件中樞已收到訊息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

- [適用于事件中樞的 Apache Kafka 疑難排解指南](apache-kafka-troubleshooting-guide.md)
- [常見問題-適用于 Apache Kafka 的事件中樞](apache-kafka-frequently-asked-questions.md)
- [適用于 Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
- [建議設定](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)