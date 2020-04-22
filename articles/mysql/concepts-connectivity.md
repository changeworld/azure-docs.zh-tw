---
title: 瞬態連線錯誤 ─MySQL 的 Azure 資料庫
description: 瞭解如何處理瞬態連接錯誤並有效地連接到 MySQL 的 Azure 資料庫。
keywords: mysql 連線、連接字串、連接問題、暫態錯誤、連接錯誤、高效連線
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4f9101b4108f5512ee9779f4633845b34fdfad5a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767872"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>處理暫態錯誤並有效地連接到 MySQL 的 Azure 資料庫

本文介紹如何處理暫時性錯誤並有效地連接到 MySQL 的 Azure 資料庫。

## <a name="transient-errors"></a>暫時性錯誤

暫時性錯誤 (也稱為暫時性故障) 係指會自行解決的錯誤。 這些錯誤最常顯現在與正在卸除之資料庫伺服器的連線。 此外，與伺服器的新連線也無法開啟。 舉例來說，當發生硬體或網路故障時，就可能發生暫時性錯誤。 另一個原因可能是正在推出的 PaaS 服務的新版本。系統可在 60 秒內自動緩解大多數這些事件。 設計及開發雲端應用程式時，最佳做法是將暫時性錯誤納入考量。 假設這些錯誤可能隨時在任何元件發生，並備妥適當的邏輯來處理這些情況。

## <a name="handling-transient-errors"></a>處理暫時性錯誤

您應該使用重試邏輯來處理暫時性錯誤。 必須考量的情況：

* 您嘗試開啟連線時發生錯誤
* 伺服器端卸除閒置的連線。 當您嘗試發出命令時卻無法執行該命令
* 目前正在執行命令的作用中連線被卸除。

第一種和第二種情況的處理方式相當直接。 請嘗試重新開啟連線。 成功時，即表示系統已解決暫時性錯誤。 您可以再次使用「適用於 MySQL 的 Azure 資料庫」。 建議您先稍候，再重試連線。 如果初始重試失敗，請先放棄。 如此一來，系統才能使用所有可用資源來解決錯誤情況。 建議採用的模式為：

* 先等候 5 秒，再進行第一次重試。
* 針對接下來的每次重試，依指數遞增等候時間，最多可達 60 秒。
* 設定應用程式將作業視為失敗的重試次數上限。

當具有作用中交易的連線失敗時，比較難正確地處理復原。 有兩種情況：如果交易本質上是唯讀的，您可以放心地重新開啟連線並重試交易。 但是,如果事務也寫入資料庫,則必須確定事務是回滾,還是在瞬態錯誤發生之前成功。 在這種情況下,您可能尚未收到來自資料庫伺服器的提交確認。

其中一種做法是，在用戶端上產生一個用於所有重試的唯一識別碼。 您需將這個唯一識別碼隨著交易一起傳遞給伺服器，並將它與一個唯一條件約束一起儲存在資料行中。 如此一來，您便可以放心地重試交易。 如果回滾了以前的事務,並且系統中尚未存在用戶端生成的唯一 ID,則它將成功。 如果因先前的交易已順利完成而使得先前已儲存唯一識別碼，交易便會因發生重複索引鍵違規而失敗。

當您的程式透過第三方中介軟體與「適用於 MySQL 的 Azure 資料庫」進行通訊時，請詢問廠商中介軟體是否包含暫時性錯誤的重試邏輯。

請務必測試您的重試邏輯。 例如,嘗試在向上或向下擴展 MySQL 伺服器 Azure 資料庫的計算資源時執行代碼。 您的應用程式應該要能夠毫無問題地處理在此作業期間發生的短暫停機情況。

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>高效連線到 MySQL 的 Azure 資料庫

資料庫連接是一種有限的資源,因此有效利用連接池訪問 Azure 資料庫以進行 MySQL 會優化性能。 以下部分介紹如何使用連接池或持久連接更有效地訪問 MySQL 的 Azure 資料庫。

## <a name="access-databases-by-using-connection-pooling-recommended"></a>使用連接池存取資料庫(建議)

管理資料庫連接會對整個應用程式的性能產生重大影響。 為了優化應用程式的性能,目標應該是減少建立連接的次數和在密鑰代碼路徑中建立連接的時間。 我們強烈建議使用資料庫連接池或持久連接連接到 MySQL 的 Azure 資料庫。 資料庫連接池處理資料庫連接的創建、管理和分配。 當程式請求資料庫連接時,它優先分配現有空閒資料庫連接,而不是創建新連接。 程式完成使用資料庫連接後,將恢復連接,準備進一步使用,而不是簡單地關閉。

為了更好地說明,本文提供了[一個範例代碼](./sample-scripts-java-connection-pooling.md),該代碼使用 JAVA 作為示例。 有關詳細資訊,請參閱[Apache 通用 DBCP](https://commons.apache.org/proper/commons-dbcp/)。

> [!NOTE]
> 伺服器配置超時機制以關閉處於空閒狀態一段時間的連接以釋放資源。 請務必設置驗證系統,以確保在使用它們時持久連接的有效性。 有關詳細資訊,請參閱[在用戶端設定驗證系統,以確保持久連接的有效性](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)。

## <a name="access-databases-by-using-persistent-connections-recommended"></a>使用持久連線存取資料庫(建議)

持久連接的概念類似於連接池的概念。 使用持久連接替換短連接只需要對代碼進行細微更改,但它對提高許多典型應用程式方案中的性能有重大影響。

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>使用連線短的等待與重試機制存取資料庫

如果您有資源限制,我們強烈建議您使用資料庫池或持久連接來訪問資料庫。 如果應用程式使用短連接,並且在接近併發連接數的上限時遇到連接失敗,則可以嘗試等待和重試機制。 您可以設置適當的等待時間,在第一次嘗試后縮短等待時間。 此後,您可以嘗試多次等待事件。

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>在用戶端中設定驗證機制,以確認持久連接的有效性

伺服器配置超時機制以關閉處於空閒狀態一段時間的連接以釋放資源。 當用戶端再次訪問資料庫時,它等效於在用戶端和伺服器之間創建新的連接請求。 為了確保連接在使用過程中的有效性,請在用戶端上配置驗證機制。 如以下範例所示,您可以使用 Tomcat JDBC 連接池來配置此驗證機制。

通過設置 TestOnBorrow 參數,當有新請求時,連接池會自動驗證任何可用空閒連接的有效性。 如果此類連接有效,則直接返回其連接池將撤回連接。 然後,連接池創建新的有效連接並返回它。 此過程可確保有效地訪問資料庫。 

有關特定設定的資訊,請參閱[JDBC 連接池官方介紹文件](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes)。 您主要需要設置以下三個參數:TestOnBorrow(設置為 true)、驗證查詢(設置為 SELECT 1)和驗證查詢超時(設置為 1)。 具體範例代碼如下所示:

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

## <a name="next-steps"></a>後續步驟

* [針對適用於 MySQL 的 Azure 資料庫的連線問題進行疑難排解](howto-troubleshoot-common-connection-issues.md)
