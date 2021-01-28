---
title: 在 Azure HDInsight 上使用 SparkCruise 來加速 Apache Spark 查詢
description: 瞭解如何使用 SparkCruise 優化平臺來改善 Apache Spark 查詢的效率。
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f07ea3007d2e72c179abe38cca56178b564bc231
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928281"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>Azure HDInsight 上的 SparkCruise

本檔討論 Azure HDInsight 功能 *SparkCruise*，它會自動重複使用 Apache Spark 計算來提高查詢效率。

## <a name="overview"></a>概觀

您在分析平臺（例如 Apache Spark）上執行的查詢會分解成包含較小子查詢的查詢計劃。 這些子查詢可能會在多個查詢的查詢計劃中重複顯示。 每次發生時，都會重新執行，以傳回結果。 不過，重新執行相同的查詢可能沒有效率，因而導致不必要的計算成本。

*SparkCruise* 是工作負載優化平臺，可重複使用一般計算，減少整體查詢執行時間和資料傳輸成本。 此平臺使用 *具體化視圖* 的概念，也就是其結果以預先計算的形式儲存的查詢。 然後當查詢本身再次顯示時，就可以重複使用這些結果，而不是重新計算結果。

## <a name="setup-and-installation"></a>設定與安裝

所有具有 Spark 2.3 或2.4 的 HDInsight 4.0 叢集都可使用 SparkCruise。 SparkCruise 程式庫檔案會安裝在 `/opt/peregrine/` 您 HDInsight 叢集上的目錄中。 若要正常運作， *SparkCruise* 需要下列設定屬性（預設設定）。

* `spark.sql.queryExecutionListeners` 設定為 `com.microsoft.peregrine.spark.listeners.PlanLogListener` ，可記錄查詢計劃。
* `spark.sql.extensions` 設定為 `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` ，可啟用線上具體化和重複使用的優化程式規則。

## <a name="computation-reuse-in-spark-sql"></a>在 Spark SQL 中重複使用計算

下列範例案例說明如何使用 *SparkCruise* 來優化 Apache Spark 查詢。 

1. 透過 SSH 連線至 spark 叢集的前端節點。
1. 輸入 `spark-shell`。
1. 執行下列程式碼片段，這會使用叢集上的範例資料執行一些基本查詢。

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. 使用 *SparkCruise* 查詢分析工具來分析先前查詢的查詢計劃，這些查詢會儲存在 Spark 應用程式記錄檔中。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. 查看分析程式的輸出，這是意見檔。 此意見檔包含未來 Spark SQL 查詢的附注。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

此 `analyze` 命令會剖析查詢計劃，並建立工作負載的表格式標記法。 您可以使用 [HDInsight SparkCruise 範例](https://github.com/Azure-Samples/azure-sparkcruise-samples)存放庫中包含的 *WorkloadInsights* 筆記本來查詢此工作負載資料表。 然後，此 `views` 命令會識別常見的子計畫運算式，並選取有趣的子計畫運算式，以供未來具體化和重複使用。 輸出是包含未來 Spark SQL 查詢附注的意見檔。 

此 `show` 命令會顯示類似下列文字的輸出：

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

意見檔包含下列格式的專案： `subplan-identifier [Materialize|Reuse] input/path/to/action` 。 在此範例中，有兩個唯一的簽章：一個代表前兩個重複的查詢，而第二個則代表最後兩個查詢中的篩選述詞。 在此意見檔中，再次提交時的下列查詢會自動具體化並重複使用一般子計畫。 

若要測試優化，請執行另一組範例查詢。

1. 輸入 `spark-shell`。
1. 執行下列程式碼片段

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. 再次查看意見檔，以查看已重複使用之查詢的簽章。

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

此 `show` 命令會提供類似下列文字的輸出：

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

雖然查詢中的常值已從變更 `'11%'` 為 `'12%'` ，但 *SparkCruise* 仍能將先前的查詢與具有輕微變化的新查詢（例如常值和資料集版本的演進）進行比對。 如果查詢有重大變更，您可以重新執行分析工具，以識別可重複使用的新查詢。

在幕後， *SparkCruise* 會觸發子查詢，以從包含它的第一個查詢中具體化選取的子計畫。 稍後的查詢可以直接讀取具體化子計畫，而不是重新計算它們。 在此工作負載中，第一個和第三個查詢會以線上方式具體化子計畫。 我們可以在具體化一般子計畫之後查看查詢的計畫變更。

您可以看到現在有四個新的具體化子運算式可在後續查詢中重複使用。

## <a name="clean-up"></a>清除

意見檔、具體化子計畫和查詢記錄會跨 Spark 會話保存。 若要移除這些檔案，請執行下列命令：

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>後續步驟

* [使用工作負載深入解析筆記本來判斷 SparkCruise 的優點](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [使用 Azure HDInsight IO 快取改善 Apache Spark 工作負載的效能](apache-spark-improve-performance-iocache.md)
* [將 HDInsight 中的 Apache Spark 作業最佳化](./apache-spark-perf.md)
* [SparkCruise： Spark 中的 Handsfree 計算重複使用](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Azure HDInsight Apache Spark 指導方針](./spark-best-practices.md)
