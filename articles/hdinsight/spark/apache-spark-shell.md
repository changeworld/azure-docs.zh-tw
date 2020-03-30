---
title: 在 Azure HDInsight 中使用互動式 Spark Shell
description: 互動式 Spark Shell 會針對執行 Spark 命令 (一次一個) 及查看結果提供「讀取-執行-列印」的流程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162798"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>從 Spark Shell 執行 Apache Spark

互動式 [Apache Spark](https://spark.apache.org/) Shell 會提供 REPL (「讀取、求值、輸出」迴圈) 環境，一次執行一個 Spark 命令並查看結果。 此流程適用於開發和偵錯。 Spark 會為每個支援的語言提供一個殼層：Scala、Python 和 R。

## <a name="run-an-apache-spark-shell"></a>執行 Apache Spark Shell

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到群集。 通過將 CLUSTERNAME 替換為群集的名稱來編輯下面的命令，然後輸入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 火花為 Scala（火花殼）和 Python（火花）提供外殼。 在 SSH 會話中，輸入以下命令*之一*：

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    如果您打算使用任何可選配置，請確保首先查看 Apache [Spark 的"記憶體不足錯誤"異常](./apache-spark-troubleshoot-outofmemory.md)。

1. 幾個基本示例命令。 選擇相關語言：

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. 查詢 CSV 檔。 請注意，以下語言適用于`spark-shell`和`pyspark`。

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. 查詢 CSV 檔並將結果存儲為變數：

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. 顯示結果：

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Exit

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession 和 SparkContext 執行個體

依預設，執行 Spark Shell 時，SparkSession 和 SparkContext 的執行個體會自動具現化。

若要存取 SparkSession 執行個體，請輸入 `spark`。 若要存取 SparkContext 執行個體，請輸入 `sc`。

## <a name="important-shell-parameters"></a>重要的殼層參數

Spark Shell 命令`spark-shell`（`pyspark`或 ） 支援許多命令列參數。 若要查看完整的參數清單，請使用參數 `--help` 啟動 Spark Shell。 其中一些參數可能僅適用于 Spark`spark-submit`外殼包裝 的 。

| switch | description | 範例 |
| --- | --- | --- |
| --master MASTER_URL | 指定主要 URL。 在 HDInsight 中，此值一律為 `yarn`。 | `--master yarn`|
| --jars JAR_LIST | 本機 Jar 的逗號分隔清單，用來包含在驅動程式和執行程式 Classpath 中。 在 HDInsight 中，此清單是由 Azure 儲存體或 Data Lake Storage 中的預設檔案系統路徑組成。 | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Jar 的 Maven 座標逗號分隔清單，用來包含在驅動程式和執行程式 Classpath 中。 依序搜尋本機 Maven 存放庫、Maven 中心和所有以 `--repositories` 指定的遠端存放庫。 座標格式為 *groupId*:*artifactId*:*version*。 | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | (僅適用於 Python) 要放在 PYTHONPATH 中的 .zip、.egg 或 .py 檔案的逗號分隔清單。 | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure HDInsight 上的 Apache Spark 簡介](apache-spark-overview.md)。
- 請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)，搭配使用 Spark 叢集和 SparkSQL。
- 請參閱[什麼是 Apache Spark 結構化串流](apache-spark-streaming-overview.md)，以使用 Spark 撰寫可處理串流資料的應用程式。
