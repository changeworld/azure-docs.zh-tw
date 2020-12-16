---
title: 針對 Azure Cosmos DB Cassandra API 中常見的錯誤進行疑難排解
description: 本檔將討論在 Azure Cosmos DB 中遇到常見問題的疑難排解方式 Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: f5f2cb5ac8c354df38310cdcb47b98e1da5b6cfa
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521815"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>針對 Azure Cosmos DB Cassandra API 中的常見問題進行疑難排解
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 是相容性層級，可提供受歡迎開放原始碼 Apache Cassandra 資料庫的 [有線通訊協定支援](cassandra-support.md) ，並由 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)提供技術支援。 Azure Cosmos DB 是完全受控的雲端原生服務，可為 Cassandra API 提供 [可用性、輸送量和一致性的保證](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) 。 這些保證無法在舊版的 Apache Cassandra 中執行。 Cassandra API 也有助於進行零維護的平臺作業和零停機的修補。 因此，許多 it 的後端作業與 Apache Cassandra 不同，因此我們建議特定的設定和方法，以避免常見的錯誤。 

本文說明使用 Azure Cosmos DB Cassandra API 之應用程式的常見錯誤和解決方案。

## <a name="common-errors-and-solutions"></a>常見錯誤和解決方案

| 錯誤               |  描述             | 解決方法  |
|---------------------|--------------------------|-----------|
| OverloadedException (JAVA)  | 取用的要求單位總數超過 keyspace 或資料表上布建的要求單位數。 因此會對要求進行節流。 | 請考慮調整指派給 Azure 入口網站的 keyspace 或資料表的輸送量， (請參閱 [這裡](manage-scale-cassandra.md) 以 Cassandra API) 的調整作業，或者您可以執行重試原則。 針對 JAVA，請參閱 [v3. x 驅動](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) 程式和 [v4. x 驅動程式](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)的重試範例。 另請參閱[適用于 JAVA 的 Azure Cosmos Cassandra 延伸](https://github.com/Azure/azure-cosmos-cassandra-extensions)模組 |
| OverloadedException (JAVA) ，甚至有足夠的輸送量 | 雖然針對要求數量及/或取用的要求單位成本布建了足夠的輸送量，但系統似乎是節流要求  | Cassandra API 會針對架構層級的作業 (CREATE TABLE、ALTER TABLE、DROP TABLE) 來實行系統輸送量預算。 這項預算應該足以滿足生產系統中的架構作業。 但是，如果您有大量的架構層級作業，您可能會超過此限制。 因為此預算並非由使用者控制，所以您必須考慮減少執行中的架構作業數目。 如果採取此動作無法解決問題，或是您的工作負載不可行，請 [建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。|
| ClosedConnectionException (JAVA)  | 在成功連線之後的一段閒置時間後，應用程式就無法連接| 此錯誤可能是因為 Azure LoadBalancers 的閒置 timeout （4分鐘）。 在驅動程式中設定 [保持運作] 設定 (查看以下) 並增加作業系統中的持續連線設定，或 [調整 Azure Load Balancer 中的閒置 timeout](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。 |
|  (JAVA) 的其他間歇性連線錯誤 | 連接意外中斷或非預期的時間 | 適用于 JAVA 的 Apache Cassandra 驅動程式提供兩個原生重新連接原則： `ExponentialReconnectionPolicy` 和 `ConstantReconnectionPolicy` 。 預設值為 `ExponentialReconnectionPolicy`。 不過，針對 Azure Cosmos DB Cassandra API，我們建議 `ConstantReconnectionPolicy` 延遲為2秒。 請參閱 JAVA v4. x 驅動程式的 [驅動程式檔](https://docs.datastax.com/developer/java-driver/4.9/manual/core/reconnection/)  集， [此處](https://docs.datastax.com/developer/java-driver/3.7/manual/reconnection/) 提供的 java 3.x 指引 (另請參閱) 的範例。|

如果未列出您的錯誤，而且您 [在 Cassandra API](cassandra-support.md)中執行支援的作業時遇到錯誤，則 *使用原生 Apache Cassandra 時，不會出現* 錯誤，請 [建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="configuring-reconnectionpolicy-for-java-driver"></a>設定 JAVA Driver 的 ReconnectionPolicy

### <a name="version-3x"></a>3.x 版

針對2.x 版的 JAVA 驅動程式，請在建立叢集物件時設定重新連接原則：

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>4.x 版

針對2.x 版的 JAVA 驅動程式，請覆寫檔案中的設定來設定重新連線原則 `reference.conf` ：

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>啟用 JAVA 驅動程式的 keep-alive

### <a name="version-3x"></a>3.x 版

針對2.x 版的 JAVA 驅動程式，請在建立叢集物件時設定保持運作，並確定 [作業系統中已啟用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)keep-alive：

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>4.x 版

針對2.x 版的 JAVA 驅動程式，請透過覆寫中的設定來設定保持 `reference.conf` 運作，並確定 [作業系統中已啟用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)keep-alive：

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>後續步驟

- 瞭解 Azure Cosmos DB Cassandra API 中 [支援的功能](cassandra-support.md) 。
- 瞭解如何 [從原生 Apache Cassandra 遷移至 Azure Cosmos DB Cassandra API](cassandra-migrate-cosmos-db-databricks.md)

