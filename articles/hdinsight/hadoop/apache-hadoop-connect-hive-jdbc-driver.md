---
title: 透過 JDBC 驅動程式查詢 Apache Hive - Azure HDInsight
description: 從 Java 應用程式使用 JDBC 驅動程式，將 Apache Hive 查詢提交到 HDInsight 上的 Hadoop。 以程式設計方式連接和透過 SQuirrel SQL 用戶端連接。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 8129239f152f6b359b930e56466052da12ef4d42
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437021"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>透過 JDBC 驅動程式在 HDInsight 中查詢 Apache Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

了解如何從 Java 應用程式使用 JDBC 驅動程式將 Apache Hive 查詢提交到 Azure HDInsight 中的 Apache Hadoop。 本文件中的資訊展示如何以程式設計方式從 SQuirreL SQL 用戶端進行連接。

如需有關 Hive JDBC 介面的詳細資訊，請參閱 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)。

## <a name="prerequisites"></a>Prerequisites

* HDInsight Hadoop 叢集。 若要建立，請參閱[開始使用 Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)。 確保服務蜂巢伺服器2正在運行。
* [Java 開發人員工具組 (JDK) 版本 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)或更高版本。
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL 是 JDBC 用戶端應用程式。

## <a name="jdbc-connection-string"></a>JDBC 連接字串

與 Azure 上的 HDInsight 群集的 JDBC 連接透過連接埠 443 進行,並且流量使用 TLS/SSL 進行保護。 背後有叢集的公用閘道器會將流量重新導向至 HiveServer2 實際接聽的連接埠。 下列連接字串會顯示用於 HDInsight 的格式：

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

將 `CLUSTERNAME` 替換為 HDInsight 叢集的名稱。

或者,您可以通過**安巴里 UI > Hive > 配置>高級**獲得連接。

![以安巴里取得 JDBC 連接字串](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>連接字串中的主機名稱

連接字串中的主機名"CLUSTERNAME.azurehdinsight.net"與群集 URL 相同。 您可以通過 Azure 門戶獲取它。 

### <a name="port-in-connection-string"></a>連接字串中的連接埠

只能使用埠**443**從 Azure 虛擬網路之外的某些位置連接到群集。 HDInsight 是一種託管服務,這意味著與群集的所有連接都通過安全閘道進行管理。 您不能直接在埠 10001 或 10000 上連接到 HiveServer 2,因為這些埠不會暴露給外部。 

## <a name="authentication"></a>驗證

建立連線時，您必須使用 HDInsight 叢集系統管理員名稱和密碼來通過叢集閘道器驗證。 從 SQuirreL SQL 之類的 JDBC 用戶端連接時，您必須在用戶端設定中輸入系統管理員名稱和密碼。

從 Java 應用程式建立連接時，您必須使用該名稱和密碼。 例如，下列 Java 程式碼會使用連接字串、系統管理員名稱和密碼開啟新的連接：

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>使用 SQuirreL SQL 用戶端連接

SQuirreL SQL 是可用來從遠端以 HDInsight 叢集執行 Hive 查詢的 JDBC 用戶端。 下列步驟假設您已安裝 SQuirreL SQL。

1. 建立目錄以包含要從群集複製的某些檔。

2. 在以下文本中,替換為`sshuser`群集的 SSH 使用者帳戶名稱。  將 `CLUSTERNAME` 取代為 HDInsight 叢集名稱。  從命令列中,將工作目錄更改為上一步中建立的工作目錄,然後輸入以下命令從 HDInsight 叢集複製檔案:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. 啟動 SQuirreL SQL 應用程式。 從視窗左側選取 [驅動程式]****。

    ![視窗左側的 [驅動程式] 索引標籤](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. 從 [驅動程式]**** 對話方塊上方的圖示，選取 [+]**** 圖示以建立驅動程式。

    ![SQuirreL SQL 應用程式驅動程式圖示](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. 在 [新增驅動程式] 對話方塊中，新增下列資訊：

    |屬性 | 值 |
    |---|---|
    |名稱|Hive|
    |範例 URL|jdbc:hive2://localhost:443/預設;傳輸模式_http;ssl_true;httpPath_/hive2|
    |額外類別路徑|使用 **「新增**」按鈕添加之前下載的所有 jar 檔。|
    |類別名稱|org.apache.hive.jdbc.HiveDriver|

   ![新增有參數的驅動程式對話框](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   選擇 **「確定」** 以保存這些設定。

6. 在 SQuirreL SQL 視窗的左側選取 [別名]****。 然後選擇**+** 圖示以創建連接別名。

    ![SQuirreL SQL 新增新別名對話框](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. 新增**別名**對話框使用以下值:

    |屬性 |值 |
    |---|---|
    |名稱|HDInsight 上的 Hive|
    |驅動程式|使用下拉清單選擇**Hive**驅動程式。|
    |URL|jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/預設;傳輸模式_http;ssl_true;httpPath_/hive2。 將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。|
    |使用者名稱|HDInsight 叢集的叢集登入帳戶名稱。 預設值為**管理員**。|
    |密碼|叢集登入帳戶的密碼。|

    ![新增帶有參數的別名對話框](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > 使用 [測試]**** 按鈕來確認連接能正常運作。 當 [連接到︰HDInsight 上的 Hive]**** 對話方塊出現時，請選取 [連接]**** 來執行測試。 如果測試成功，您會看到 [連線成功]**** 對話方塊。 如果發生錯誤，請參閱[疑難排解](#troubleshooting)。

    若要儲存連線別名，請使用 [新增別名]**** 對話方塊底部的 [確定]**** 按鈕。

8. 從 SQuirreL SQL 頂端的 [連接到]**** 下拉式清單選取 [HDInsight 上的 Hive]****。 出現提示時，請選取 [連接]****。

    ![具有參數的連線對話框](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. 連接後,在 SQL 查詢對話框中輸入以下查詢,然後選擇 **「運行」** 圖示(正在運行的人員)。 結果區域應該會顯示查詢的結果。

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![sql 查詢對話方塊，包括結果](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>從範例 Java 應用程式連接

使用 Java 客戶端在 HDInsight 上查詢[https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)Hive 的範例 可在 。 遵循儲存機制中的指示建置和執行範例。

## <a name="troubleshooting"></a>疑難排解

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>嘗試開啟 SQL 連接時，發生意外的錯誤

**徵狀**︰連線到 HDInsight 叢集 3.3 版或更新版本時，您可能會收到發生非預期錯誤的錯誤。 此錯誤的堆疊追蹤開頭為下列幾行︰

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**原因**：此錯誤是由 SQuirreL 所隨附的舊版 commons-codec.jar 檔案所造成。

**解決方法**: 要修復此錯誤,請使用以下步驟:

1. 離開 SQuirreL,然後轉到系統上安裝 SQuirreL 的`C:\Program Files\squirrel-sql-4.0.0\lib`目錄,也許 。 在 SquirreL 目錄的 `lib` 目錄下，使用從 HDInsight 叢集下載的版本來取代現有的 commons-codec.jar。

1. 重新啟動 SQuirreL。 連接到 HDInsight 上的 Hive 時應該不會再出現此錯誤。

### <a name="connection-disconnected-by-hdinsight"></a>HDInsight 斷線連線

**症狀**:當嘗試透過 JDBC/ODBC 下載大量資料(例如多個 GB)時,HDInsight 在下載時意外斷開了連接。 

**原因**:此錯誤是由網關節點的限制引起的。 從 JDBC/ODBC 獲取數據時,所有數據都需要通過網關節點。 但是,閘道不是用於下載大量資料而設計的,因此,如果閘道無法處理流量,則連接可能會關閉。

**解決方法**:避免使用 JDBC/ODBC 驅動程式下載大量數據。 而是直接從 Blob 儲存複製數據。


## <a name="next-steps"></a>後續步驟

現在,您已經瞭解如何使用 JDBC 處理 Hive,請使用以下連結來探索使用 Azure HDInsight 的其他方法。

* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Apache Hive 資料視覺化](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 將互動式查詢 Hive 資料視覺化](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [使用 Apache Zepelin 在 Azure HDInsight 中執行 Apache Hive 查詢](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用 Azure HDInsight 工具進行視覺化工作室程式](../hdinsight-for-vscode.md)。
* [將資料上傳到 HDInsight](../hdinsight-upload-data.md)
* [搭配 HDInsight 使用 Apache Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Apache Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 作業](hdinsight-use-mapreduce.md)
