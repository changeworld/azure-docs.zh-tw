---
title: 教學課程：HDInsight Apache Storm 至儲存體 - Azure/Data Lake
description: 教學課程 - 了解如何使用 Apache Storm 寫入 Azure HDInsight 的 HDFS 相容儲存體。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 579163180f6c7ba19927ca66d20bd92d1b2de52e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73241203"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>教學課程：從 Azure HDInsight 上的 Apache Storm 寫入 Apache Hadoop HDFS

本教學課程會示範如何使用 Apache Storm 將資料寫入 HDInsight 上 Apache Storm 所使用的 HDFS 相容儲存體。 HDInsight 可以同時使用 Azure 儲存體以及 Azure Data Lake Storage 作為 HDFS 相容儲存體。 Storm 提供了將資料寫入 HDFS 的 [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 元件。 本文件提供從 HdfsBolt 寫入任一類型儲存體的資訊。

本文件使用的範例拓撲依賴 Storm on HDInsight 隨附的元件。 它可能需要進行修改，才能在與其他 Apache Storm 叢集搭配使用時使用 Azure Data Lake Storage。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用指令碼動作來設定叢集
> * 建置和封裝拓撲
> * 部署並執行拓撲
> * 檢視輸出資料
> * 停止拓撲

## <a name="prerequisites"></a>Prerequisites

* [Java Developer Kit (JDK) 第 8 版](https://aka.ms/azure-jdks)

* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* 您叢集主要儲存體的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 `wasb://` 適用於 Azure 儲存體，`abfs://` 適用於 Azure Data Lake Storage Gen2 或 `adl://` 適用於 Azure Data Lake Storage Gen1。 如果已對 Azure 儲存體啟用安全傳輸，URI 會是 `wasbs://`。  另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

### <a name="example-configuration"></a>範例設定

下列 YAML 是摘錄自範例中隨附的 `resources/writetohdfs.yaml` 檔案。 此檔案會使用 Apache Storm 的 [Flux](https://storm.apache.org/releases/current/flux.html) 架構定義 Storm 拓撲。

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

此 YAML 會定義下列項目：

* `syncPolicy`:定義檔案何時同步處理/排清到檔案系統。 在此範例中，為每 1000 個 Tuple。
* `fileNameFormat`:定義寫入檔案時要使用的路徑和檔案名稱模式。 在此範例中，會使用篩選條件在執行階段提供路徑，而檔案的副檔名為 `.txt`。
* `recordFormat`:定義所寫入檔案的內部格式。 在此範例中，會以 `|` 字元分隔欄位。
* `rotationPolicy`:定義何時輪替檔案。 在此範例中，不會執行輪替。
* `hdfs-bolt`:使用舊版元件作為 `HdfsBolt` 類別的設定參數。

如需有關 Flux 架構的詳細資訊，請參閱 [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)。

## <a name="configure-the-cluster"></a>設定叢集

根據預設，Storm on HDInsight 未包含 `HdfsBolt` 用來與 Storm Classpath 中出現的 Azure 儲存體或 Data Lake Storage 進行通訊的元件。 請使用下列指令碼動作，將這些元件新增至叢集上 Storm 的 `extlib` 目錄：

| 屬性 | 值 |
|---|---|
|指令碼類型 |- 自訂|
|Bash 指令碼 URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|節點類型 |Nimbus、Supervisor|
|參數 |None|

如需搭配叢集使用此指令碼的資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](./../hdinsight-hadoop-customize-cluster-linux.md)文件。

## <a name="build-and-package-the-topology"></a>建置和封裝拓撲

1. 從 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下載範例專案到您的部署環境。

2. 從命令提示字元、終端機或 Shell 工作階段，將目錄變更為所下載專案的根目錄。 若要建置和封裝拓撲，請使用下列命令：

    ```cmd
    mvn compile package
    ```

    建置和封裝完成之後，會有名為 `target` 的新目錄，其中包含名為 `StormToHdfs-1.0-SNAPSHOT.jar` 的檔案。 此檔案包含已編譯的拓撲。

## <a name="deploy-and-run-the-topology"></a>部署並執行拓撲

1. 使用下列命令將拓撲複製到 HDInsight 叢集。 將 `CLUSTERNAME` 取代為叢集的名稱。

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. 上傳完成後，使用下列命令來透過 SSH 連接到 HDInsight 叢集。 將 `CLUSTERNAME` 取代為叢集的名稱。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 連接之後，使用下列命令來建立名為 `dev.properties` 的檔案：

    ```bash
    nano dev.properties
    ```

1. 使用下列文字做為 `dev.properties` 檔案的內容。 根據您的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)視需要進行修訂。

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    若要儲存檔案，使用 __Ctrl + X__，然後是 __Y__，最後按 __Enter__。 此檔案中的值會設定儲存體 URL，以及要寫入資料的目錄名稱。

1. 使用下列命令來啟動拓撲：

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    此命令藉由將拓撲提交給叢集的 Nimbus 節點，以使用 Flux 架構來啟動拓撲。 拓撲是由 jar 中包含的 `writetohdfs.yaml` 檔案所定義。 `dev.properties` 檔案會傳遞來做為篩選，而檔案中包含的值是透過拓撲來讀取。

## <a name="view-output-data"></a>檢視輸出資料

若要檢視資料，請使用下列命令：

  ```bash
  hdfs dfs -ls /stormdata/
  ```

隨即會顯示此拓撲所建立的檔案清單。 下列清單是前一個命令所傳回的資料範例：

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>停止拓撲

Storm 拓撲會一直執行，直到其停止或叢集遭到刪除為止。 若要停止拓撲，請使用下列命令：

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>清除資源

若要清除本教學課程所建立的資源，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的 HDInsight 叢集，以及與資源群組相關聯的任何其他資源。

若要使用 Azure 入口網站移除資源群組：

1. 在 Azure 入口網站中展開左側功能表，以開啟服務的功能表，然後選擇 [資源群組]  以顯示資源群組的清單。
2. 找出要刪除的資源群組，然後以滑鼠右鍵按一下清單右側的 [更多]  按鈕 (...)。
3. 選取 [刪除資源群組]  ，並加以確認。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您會了解如何使用 Apache Storm 將資料寫入 HDInsight 上 Apache Storm 所使用的 HDFS 相容儲存體。

> [!div class="nextstepaction"]
> 探索其他[適用於 HDInsight 的 Apache Storm 範例](apache-storm-example-topology.md)