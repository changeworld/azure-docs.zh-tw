---
title: 暫時性連線錯誤-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何處理暫時性連線錯誤，並有效率地連接到適用於 MySQL 的 Azure 資料庫。
keywords: mysql 連線，連接字串，連線問題，暫時性錯誤，連接錯誤，有效率地連接
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb5adb3787176e3bdbfb7897aa7d7deb9cc2dae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82100136"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>處理暫時性錯誤並有效率地連接到適用於 MySQL 的 Azure 資料庫

本文說明如何處理暫時性錯誤，並有效率地連接到適用於 MySQL 的 Azure 資料庫。

## <a name="transient-errors"></a>暫時性錯誤

暫時性錯誤 (也稱為暫時性故障) 係指會自行解決的錯誤。 這些錯誤最常顯現在與正在卸除之資料庫伺服器的連線。 此外，與伺服器的新連線也無法開啟。 舉例來說，當發生硬體或網路故障時，就可能發生暫時性錯誤。 另一個原因可能是即將推出的 PaaS 服務的新版本。系統會在60秒內自動減緩這些事件。 設計及開發雲端應用程式時，最佳做法是將暫時性錯誤納入考量。 假設這些錯誤可能隨時在任何元件發生，並備妥適當的邏輯來處理這些情況。

## <a name="handling-transient-errors"></a>處理暫時性錯誤

您應該使用重試邏輯來處理暫時性錯誤。 必須考量的情況：

* 您嘗試開啟連線時發生錯誤
* 伺服器端卸除閒置的連線。 當您嘗試發出命令時卻無法執行該命令
* 目前正在執行命令的作用中連線被卸除。

第一種和第二種情況的處理方式相當直接。 請嘗試重新開啟連線。 成功時，即表示系統已解決暫時性錯誤。 您可以再次使用「適用於 MySQL 的 Azure 資料庫」。 建議您先稍候，再重試連線。 如果初始重試失敗，請先放棄。 如此一來，系統才能使用所有可用資源來解決錯誤情況。 建議採用的模式為：

* 先等候 5 秒，再進行第一次重試。
* 針對接下來的每次重試，依指數遞增等候時間，最多可達 60 秒。
* 設定應用程式將作業視為失敗的重試次數上限。

當具有作用中交易的連線失敗時，比較難正確地處理復原。 有兩種情況：如果交易本質上是唯讀的，您可以放心地重新開啟連線並重試交易。 但是，如果交易也是寫入資料庫中，您就必須判斷交易是否已復原，或在發生暫時性錯誤之前是否成功。 在這種情況下，您可能只會收到來自資料庫伺服器的認可通知。

其中一種做法是，在用戶端上產生一個用於所有重試的唯一識別碼。 您需將這個唯一識別碼隨著交易一起傳遞給伺服器，並將它與一個唯一條件約束一起儲存在資料行中。 如此一來，您便可以放心地重試交易。 如果先前的交易已復原，而且用戶端產生的唯一識別碼尚未存在於系統中，則會成功。 如果因先前的交易已順利完成而使得先前已儲存唯一識別碼，交易便會因發生重複索引鍵違規而失敗。

當您的程式透過第三方中介軟體與「適用於 MySQL 的 Azure 資料庫」進行通訊時，請詢問廠商中介軟體是否包含暫時性錯誤的重試邏輯。

請務必測試您的重試邏輯。 例如，請嘗試執行您的程式碼，同時向上或向下擴充適用於 MySQL 的 Azure 資料庫伺服器的計算資源。 您的應用程式應該要能夠毫無問題地處理在此作業期間發生的短暫停機情況。

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>有效率地連接到適用於 MySQL 的 Azure 資料庫

資料庫連接是一項有限的資源，因此充分利用連接共用來存取適用於 MySQL 的 Azure 資料庫可優化效能。 下一節說明如何使用連接共用或持續連線，更有效地存取適用於 MySQL 的 Azure 資料庫。

## <a name="access-databases-by-using-connection-pooling-recommended"></a>使用連接共用來存取資料庫 (建議的) 

管理資料庫連接可能會對整個應用程式的效能造成重大影響。 若要優化應用程式的效能，目標應該是減少建立連線的次數，以及在關鍵程式碼路徑中建立連接的時間。 我們強烈建議使用資料庫連接共用或持續連線來連接到適用於 MySQL 的 Azure 資料庫。 資料庫連接共用會處理資料庫連接的建立、管理和配置。 當程式要求資料庫連接時，它會優先配置現有的閒置資料庫連接，而不是建立新的連接。 當程式完成使用資料庫連接之後，連接會復原以進行進一步的使用，而不是只關閉。

為了更清楚說明，本文提供 [了](./sample-scripts-java-connection-pooling.md) 使用 JAVA 作為範例的範例程式碼。 如需詳細資訊，請參閱 [Apache 一般 DBCP](https://commons.apache.org/proper/commons-dbcp/)。

> [!NOTE]
> 伺服器會設定超時機制，將閒置狀態的連線關閉一段時間，以釋出資源。 在使用時，請務必設定驗證系統，以確保持續連線的有效性。 如需詳細資訊，請參閱在 [用戶端設定驗證系統，以確保持續](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)連線的有效性。

## <a name="access-databases-by-using-persistent-connections-recommended"></a>使用持續性連接存取資料庫 (建議的) 

持續性連接的概念類似于連接共用的概念。 以持續連線取代簡短的連接只需要對程式碼進行輕微的變更，但在許多一般的應用程式案例中，它的效能都有顯著的影響。

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>使用等候和重試機制搭配簡短連接來存取資料庫

如果您有資源限制，強烈建議您使用資料庫共用或持續連線來存取資料庫。 如果您的應用程式使用較短的連接，而且當您接近並行連接數目的上限時遇到連線失敗，您可以嘗試等候和重試機制。 您可以設定適當的等候時間，並在第一次嘗試之後等候較短的等候時間。 之後，您可以試著多次等候事件。

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>在用戶端中設定驗證機制，以確認持續連線的有效性

伺服器會設定超時機制，以關閉閒置狀態的連線，以釋出資源。 當用戶端再次存取資料庫時，就相當於在用戶端與伺服器之間建立新的連接要求。 若要在使用連接的過程中確保連線的有效性，請在用戶端上設定驗證機制。 如下列範例所示，您可以使用 Tomcat JDBC 連接共用來設定此驗證機制。

藉由設定 TestOnBorrow 參數，當有新的要求時，連接集區會自動確認任何可用閒置連接的有效性。 如果這類連接有效，則會直接傳回連接集區，否則會收回連接。 然後，連接集區會建立新的有效連接，並傳回它。 此程式可確保有效率地存取資料庫。 

如需特定設定的詳細資訊，請參閱「 [JDBC 連接集區官方簡介」檔](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes)。 您主要需要設定下列三個參數： TestOnBorrow (設定為 true) 、ValidationQuery (設定為 SELECT 1) 和 ValidationQueryTimeout (設定為 1) 。 特定的範例程式碼如下所示：

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>接下來的步驟

* [針對適用於 MySQL 的 Azure 資料庫的連線問題進行疑難排解](howto-troubleshoot-common-connection-issues.md)
