---
title: 在 Linux 型 Azure HDInsight 上使用 Hadoop Oozie 工作流程
description: 在 Linux 型 HDInsight 上使用 Hadoop Oozie。 了解如何定義 Oozie 工作流程，以及提交 Oozie 作業。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 1e88fc64ea297f70f56478588312675fb233f221
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085934"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>在 Linux 型 Azure HDInsight 上搭配 Apache Hadoop 使用 Apache Oozie 來定義並執行工作流程

了解如何在 Azure HDInsight 上搭配 Apache Hadoop 使用 Apache Oozie。 Oozie 是可管理 Hadoop 作業的工作流程和協調系統。 Oozie 已與 Hadoop 堆疊整合，並支援下列作業：

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

您也可以使用 Oozie 來排程系統的特定作業，例如 Java 程式或 Shell 指令碼。

> [!NOTE]  
> 還有另一個選項可以定義與 HDInsight 搭配的工作流程，那就是 Azure Data Factory。 若要深入了解 Data Factory，請參閱 [將 Apache Pig 和 Apache Hive 與 Data Factory 搭配使用](../data-factory/transform-data.md)。 若要在使用企業安全性套件的叢集上使用 Oozie，請參閱[在具有企業安全性套件的 HDInsight Hadoop 叢集中執行 Apache Oozie](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)。

## <a name="prerequisites"></a>必要條件

* **HDInsight 上的 Hadoop**叢集。 請參閱[開始在 Linux 上使用 HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

* **SSH 用戶端**。 請參閱[使用 SSH 連接到 HDInsight （Apache Hadoop）](hdinsight-hadoop-linux-use-ssh-unix.md)。

* **Azure SQL Database**。  請參閱[Azure 入口網站中的 Azure SQL Database 建立資料庫](../sql-database/sql-database-get-started.md)。  本文使用名為**oozietest 做**的資料庫。

* 您叢集主要儲存體的 URI 配置。 `wasb://`針對 Azure 儲存體， `abfs://` Azure Data Lake Storage Gen2 或 `adl://` 用於 Azure Data Lake Storage Gen1。 如果已對 Azure 儲存體啟用安全傳輸，URI 會是 `wasbs://`。 另請參閱[安全傳輸](../storage/common/storage-require-secure-transfer.md)。

## <a name="example-workflow"></a>範例工作流程

此文件中使用的工作流程包含兩個動作。 動作就是工作的定義，例如執行 Hive、Sqoop、MapReduce 或其他處理序：

![HDInsight oozie 工作流程圖](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Hive 動作會執行 HiveQL 腳本，以從 HDInsight 隨附的中解壓縮記錄 `hivesampletable` 。 每個資料列均代表來自特定行動裝置的瀏覽。 顯示的記錄格式類似下列文字：

    ```output
    8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
    23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
    23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1
    ```

    本文件中使用的 Hive 指令碼會計算每個平台 (例如 Android 或 iPhone) 的總瀏覽次數，並將計數儲存到新的 Hive 資料表。

    如需 Hive 的詳細資訊，請參閱 [搭配 HDInsight 使用 Apache Hive] [HDInsight-使用-Hive]。

2. Sqoop 動作會將新的 Hive 資料表內容匯出至 Azure SQL Database 中建立的資料表。 如需 Sqoop 的詳細資訊，請參閱[將 Apache Sqoop 與 HDInsight 搭配使用](hadoop/apache-hadoop-use-sqoop-mac-linux.md)。

> [!NOTE]  
> 如需 HDInsight 叢集上支援的 Oozie 版本，請參閱[hdinsight 所提供 Hadoop 叢集版本的新功能](hdinsight-component-versioning.md)。

## <a name="create-the-working-directory"></a>建立工作目錄

Oozie 預計您會將作業所需的所有資源儲存在同一個目錄中。 此範例會使用 `wasbs:///tutorials/useoozie`。 若要建立此目錄，請完成下列步驟：

1. 編輯以下程式碼，將取代為叢集 `sshuser` 的 SSH 使用者名稱，並將取代 `CLUSTERNAME` 為叢集的名稱。  然後輸入程式碼，以[使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)連接到 HDInsight 叢集。  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 若要建立目錄，請使用下列命令：

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > `-p` 參數會使系統建立路徑中的所有目錄。 `data`目錄是用來保存腳本所使用的資料 `useooziewf.hql` 。

3. 編輯以下程式碼，將取代為 `sshuser` 您的 SSH 使用者名稱。  為了確保 Oozie 可以模擬您的使用者帳戶，請使用下列命令：

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > 您可以忽略指出使用者已是 `users` 群組之成員的錯誤。

## <a name="add-a-database-driver"></a>新增資料庫驅動程式

此工作流程會使用 Sqoop 將資料匯出至 SQL 資料庫。 因此，您必須提供用來與 SQL database 互動的 JDBC 驅動程式複本。 若要將 JDBC 驅動程式複製到工作目錄，請從 SSH 工作階段使用下列命令：

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> 確認存在於的實際 JDBC 驅動程式 `/usr/share/java/` 。

如果工作流程已使用其他資源，例如含有 MapReduce 應用程式的 jar，則您也必須新增這些資源。

## <a name="define-the-hive-query"></a>定義 Hive 查詢

請使用下列步驟來建立 Hive 查詢語言 (HiveQL) 指令碼，以定義查詢。 您將在本檔稍後的 Oozie 工作流程中使用查詢。

1. 從 SSH 連線，使用下列命令來建立名為 `useooziewf.hql` 的檔案：

    ```bash
    nano useooziewf.hql
    ```

1. 在 GNU nano 編輯器開啟後，請使用下列查詢做為檔案的內容：

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    指令碼中使用了兩個變數：

   * `${hiveTableName}`：包含要建立的資料表名稱。

   * `${hiveDataFolder}`：包含要儲存資料表之資料檔案的位置。

     本文中 workflow.xml 的工作流程定義檔，會在執行時間將這些值傳遞至這個 HiveQL 腳本。

1. 若要儲存檔案，請選取**Ctrl + X**，輸入**Y**，然後選取**enter**。  

1. 使用下列命令將複製 `useooziewf.hql` 到 `wasbs:///tutorials/useoozie/useooziewf.hql` ：

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    此命令會將 `useooziewf.hql` 檔案儲存在叢集的 HDFS 相容儲存體中。

## <a name="define-the-workflow"></a>定義工作流程

Oozie 工作流程定義是以 Hadoop 流程定義語言 (hPDL)，也就是 XML 流程定義語言所撰寫的。 使用下列步驟定義工作流程：

1. 使用以下陳述式建立和編輯新的檔案：

    ```bash
    nano workflow.xml
    ```

2. 在 nano 編輯器開啟後，請輸入下列 XML 做為檔案內容：

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    此工作流程中定義了兩個動作：

   * `RunHiveScript`：這是起始動作，會執行 `useooziewf.hql` Hive 指令碼。

   * `RunSqoopExport`：此動作會使用 Sqoop 將從 Hive 指令碼建立的資料匯出到 SQL 資料庫。 只有當 `RunHiveScript` 動作成功時，才會執行此動作。

     工作流程有數個項目，例如 `${jobTracker}`。 您會將這些專案取代為您在工作定義中使用的值。 您稍後會在本檔中建立作業定義。

     此外也請注意 Sqoop 區段中的 `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` 項目。 此項目會指示 Oozie 在此動作執行時，讓 Sqoop 可取得此封存。

3. 若要儲存檔案，請選取**Ctrl + X**，輸入**Y**，然後選取**enter**。  

4. 使用下列命令將 `workflow.xml` 檔案複製到 `/tutorials/useoozie/workflow.xml`：

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>建立資料表

> [!NOTE]  
> 連接至 SQL Database 建立資料表的方法有很多種。 下列步驟會從 HDInsight 叢集使用 [FreeTDS](https://www.freetds.org/) 。

1. 使用以下命令在 HDInsight 叢集上安裝 FreeTDS：

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. 編輯以下程式碼，將取代為 `<serverName>` 您的[邏輯 SQL server](../azure-sql/database/logical-servers.md)名稱，並 `<sqlLogin>` 使用伺服器登入。  輸入命令以連接到必要條件 SQL database。  在提示字元中輸入密碼。

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    您會收到如下列文字所示的輸出：

    ```output
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to oozietest
    1>
    ```

3. 在 `1>` 提示字元輸入下列幾行：

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    輸入 `GO` 陳述式後，將評估先前的陳述式。 這些語句會建立工作流程所使用的資料表，名為 `mobiledata` 。

    若要確認資料表是否已建立，請使用下列命令：

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    您會看到如下列文字所示的輸出：

    ```output
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo             mobiledata      BASE TABLE
    ```

4. 在提示字元中輸入，以結束 tsql 公用程式 `exit` `1>` 。

## <a name="create-the-job-definition"></a>建立工作定義

工作定義描述 workflow.xml 位於何處。 它也描述工作流程使用的其他檔案 (例如 `useooziewf.hql`) 位於何處。 此外，它也會定義工作流程中所使用的屬性值，以及相關聯的檔案。

1. 若要取得預設儲存體的完整位址，請使用下列命令。 此位址會用在您於下一個步驟建立的組態檔中。

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    此命令會傳回類似以下 XML 的資訊：

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > 若 HDInsight 叢集使用 Azure 儲存體做為預設儲存體，`<value>` 元素內容的開頭將會是 `wasbs://`。 若改為使用 Azure Data Lake Storage Gen1，則其開頭將會是 `adl://`。 若使用 Azure Data Lake Storage Gen2，則其開頭將會是 `abfs://`。

    儲存 `<value>` 元素的內容，因為在後續步驟將會用到它。

2. 編輯下方的 xml，如下所示：

    |預留位置值| 已取代值|
    |---|---|
    |wasbs://mycontainer \@ mystorageaccount.blob.core.windows.net| 從步驟1收到的值。|
    |admin| HDInsight 叢集的登入名稱（如果不是 admin）。|
    |serverName| Azure SQL Database 的伺服器名稱。|
    |sqlLogin| Azure SQL Database 伺服器登入。|
    |sqlPassword| Azure SQL Database 伺服器登入密碼。|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    此檔案中大部分的資訊都會用來填入 workflow.xml 或 ooziewf.hql 檔案中所使用的值 (例如 `${nameNode}`)。  若該路徑是 `wasbs` 路徑，您必須使用完整路徑。 請不要將它縮短為 `wasbs:///` 。 `oozie.wf.application.path` 項目會定義用來尋找 workflow.xml 檔案的位置。 此檔案包含此作業所執行的工作流程。

3. 若要建立 Oozie 作業定義組態，請使用下列命令：

    ```bash
    nano job.xml
    ```

4. 在 nano 編輯器開啟後，將編輯過的 XML 貼入檔案的內容。

5. 若要儲存檔案，請選取**Ctrl + X**，輸入**Y**，然後選取**enter**。

## <a name="submit-and-manage-the-job"></a>提交和管理工作

下列步驟使用 Oozie 命令提交及管理叢集上的 Oozie 工作流程。 Oozie 命令是 [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)上的易記介面。

> [!IMPORTANT]  
> 在使用 Oozie 命令時，您必須使用 HDInsight 前端節點的 FQDN。 只有從叢集才能存取此 FQDN，或者，如果叢集位於 Azure 虛擬網路，就必須從同一個網路中的其他電腦來存取。

1. 若要取得 Oozie 服務的 URL，請使用下列命令：

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    這會傳回類似以下 XML 的資訊：

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` 部分是要搭配 Oozie 命令使用的 URL。

2. 編輯程式碼，將 URL 取代為您稍早收到的 URL。 若要建立 URL 的環境變數，請使用下列命令，這樣您就不必為每個命令輸入該 URL：

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. 若要提交作業，請使用下列程式碼：

    ```bash
    oozie job -config job.xml -submit
    ```

    此命令會從載入作業資訊， `job.xml` 並將其提交至 Oozie，但不會執行它。

    在命令完成後，系統應該會傳回作業的識別碼，例如 `0000005-150622124850154-oozie-oozi-W`。 此 ID 將用來管理工作。

4. 編輯以下的程式碼，將取代為 `<JOBID>` 上一個步驟中所傳回的識別碼。  若要檢視作業的狀態，請使用下列命令：

    ```bash
    oozie job -info <JOBID>
    ```

    這會傳回類似以下文字的資訊：

    ```output
    Job ID : 0000005-150622124850154-oozie-oozi-W
    ------------------------------------------------------------------------------------------------------------------------------------
    Workflow Name : useooziewf
    App Path      : wasb:///tutorials/useoozie
    Status        : PREP
    Run           : 0
    User          : USERNAME
    Group         : -
    Created       : 2015-06-22 15:06 GMT
    Started       : -
    Last Modified : 2015-06-22 15:06 GMT
    Ended         : -
    CoordAction ID: -
    ------------------------------------------------------------------------------------------------------------------------------------
    ```

    這項工作的狀態為 `PREP`。 這個狀態表示作業雖已建立但未啟動。

5. 編輯以下的程式碼，將取代為 `<JOBID>` 先前傳回的識別碼。  若要啟動作業，請使用下列命令：

    ```bash
    oozie job -start <JOBID>
    ```

    如果您在使用此命令後檢查狀態，會發現作業為執行中狀態，並傳回作業內的動作資訊。  作業需要幾分鐘的時間才能完成。

6. 編輯以下程式碼，將取代為 `<serverName>` 您的伺服器名稱，並以 `<sqlLogin>` 伺服器登入。  *工作順利完成之後*，您可以使用下列命令來確認資料已產生並匯出至 SQL 資料庫資料表。  在提示字元中輸入密碼。

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    在 `1>` 提示字元輸入下列查詢：

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    所傳回的資訊類似下列文字︰

    ```output
    deviceplatform  count
    Android 31591
    iPhone OS       22731
    proprietary development 3
    RIM OS  3464
    Unknown 213
    Windows Phone   1791
    (6 rows affected)
    ```

如需 Oozie 命令的詳細資訊，請參閱 [Apache Oozie 命令列工具](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)。

## <a name="oozie-rest-api"></a>Oozie REST API

透過 Oozie REST API，您可以建置自己的工具來搭配 Oozie 使用。 下列有關使用 Oozie REST API 的 HDInsight 特定資訊：

* **URI**：您可以從叢集的外部 (位於 `https://CLUSTERNAME.azurehdinsight.net/oozie`) 存取 REST API。

* **驗證**：若要進行驗證，請使用 API 以及叢集 HTTP 帳戶 (admin) 和密碼。 例如：

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

如需如何使用 Oozie REST API 的詳細資訊，請參閱 [Apache Oozie Web 服務 API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)。

## <a name="oozie-web-ui"></a>Oozie Web UI

Oozie Web UI 可讓您用網頁檢視叢集上 Oozie 作業的狀態。 透過 Web UI，您可以檢視下列資訊：

   * 工作狀態
   * 工作定義
   * 設定
   * 工作中的動作圖表
   * 工作的記錄

您也可以檢視作業內的動作詳細資料。

若要存取 Oozie Web UI，請完成下列步驟：

1. 建立 HDInsight 叢集的 SSH 通道。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)。

2. 建立通道之後，請使用 URI 在網頁瀏覽器中開啟 Ambari web UI `http://headnodehost:8080` 。

3. 從頁面左側，選取 [Oozie] [ **Oozie**  >  **快速連結**] [  >  **Oozie Web UI**]。

    ![Apache Ambari oozie web ui 步驟](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Oozie Web UI 預設會顯示執行中的工作流程作業。 若要查看所有的工作流程作業，請選取 [所有作業]****。

    ![Oozie web 主控台工作流程作業](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. 若要檢視作業的詳細資訊，請選取作業。

    ![HDInsight Apache Oozie 作業資訊](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. 您可以在 [作業資訊]**** 索引標籤中看到基本的作業資訊，以及作業內的個別動作。 您可以使用頂端的索引標籤，來檢視 [作業定義]****、[作業組態]****，以及存取 [作業記錄]****，或檢視 [作業 DAG]**** 底下之作業的有向非循環圖 (DAG)。

   * **作業記錄**：選取 [取得記錄]**** 按鈕，以取得作業的所有記錄，或使用 [輸入搜尋篩選條件]**** 欄位來篩選記錄。

       ![HDInsight Apache Oozie 作業記錄檔](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **作業 DAG**：DAG 是將整個工作流程所採取的資料路徑予以圖形化的概觀。

       ![' HDInsight Apache Oozie 作業 dag '](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. 如果您在 [作業資訊]**** 索引標籤中選取其中一個動作，它將會顯示該動作的資訊。 例如，選取 **RunSqoopExport** 動作。

    ![HDInsight oozie 作業動作資訊](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. 您可以查看動作的詳細資料，例如 **Console URL** (主控台 URL) 的連結。 請使用此連結來檢視作業的作業追蹤器資訊。

## <a name="schedule-jobs"></a>排程作業

您可以使用協調器指定作業的開始時間、結束時間和發生頻率。 若要定義工作流程的排程，請完成下列步驟：

1. 使用下列命令建立名為 **coordinator.xml** 的檔案：

    ```bash
    nano coordinator.xml
    ```

    使用下列 XML 做為檔案的內容：

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > `${...}` 變數在執行階段將被作業定義中的值取代。 這些變數分為別：
    >
    > * `${coordFrequency}`：執行作業執行個體的間隔時間。
    > * `${coordStart}`：作業開始時間。
    > * `${coordEnd}`：工作結束時間。
    > * `${coordTimezone}`：協調器作業位在固定時區，不受日光節約時間影響 (通常使用 UTC 表示)。 此時區稱為「 *Oozie 處理時區」。*
    > * `${wfPath}`：workflow.xml 的路徑。

2. 若要儲存檔案，請選取**Ctrl + X**，輸入**Y**，然後選取**enter**。

3. 若要將該檔案複製到此作業的工作目錄，請使用下列命令：

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. 若要修改 `job.xml` 您稍早建立的檔案，請使用下列命令：

    ```bash
    nano job.xml
    ```

    進行下列變更：

   * 若要指示 Oozie 執行協調器檔案而非工作流程，請將 `<name>oozie.wf.application.path</name>` 變更為 `<name>oozie.coord.application.path</name>`。

   * 若要設定協調器使用的 `workflowPath` 變數，請新增下列 XML：

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       將 `wasbs://mycontainer@mystorageaccount.blob.core.windows` 文字取代為在 job.xml 檔案之其他項目中使用的值。

   * 若要定義協調器的開始時間、結束時間和頻率，請新增下列 XML：

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       這些值會將開始時間設定為5月 10 2018 日下午12:00，而結束時間則設為2018。 用來執行此作業的間隔會設為每日。 頻率的單位是分鐘，因此 24 小時 x 60 分鐘 = 1440 分鐘。 最後，時區會設為 UTC。

5. 若要儲存檔案，請選取**Ctrl + X**，輸入**Y**，然後選取**enter**。

6. 若要提交並啟動工作，請使用下列命令：

    ```bash
    oozie job -config job.xml -run
    ```

7. 如果您移至 Oozie Web UI，並選取 [協調器作業]**** 索引標籤，您會看到如下圖所示的資訊：

    ![[Oozie web 主控台協調器工作] 索引標籤](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    [Next Materialization (下次具體化)]**** 項目包含下次工作執行的時間。

8. 與先前的工作流程作業類似，若您在 Web UI 中選取作業項目，它便會顯示作業的資訊：

    ![Apache Oozie 協調器工作資訊](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > 此影像只會顯示作業的成功執行項目，而不是排程工作流程內的個別動作。 若要查看個別動作，請選取其中一個**動作**項目。

    ![[OOzie web 主控台作業資訊] 索引標籤](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何定義 Oozie 工作流程，以及如何執行 Oozie 作業。 若要深入了解如何使用 HDInsight，請參閱下列文章：

* [在 HDInsight 中上傳 Apache Hadoop 作業的資料](hdinsight-upload-data.md)
* [在 HDInsight 中將 Apache Sqoop 與 Apache Hadoop 搭配使用](hadoop/apache-hadoop-use-sqoop-mac-linux.md)
* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hadoop/hdinsight-use-hive.md)
* [Apache Oozie 疑難排解](./troubleshoot-oozie.md)
