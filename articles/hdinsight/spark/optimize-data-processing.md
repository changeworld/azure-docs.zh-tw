---
title: 最佳化 Apache Spark 的資料處理 - Azure HDInsight
description: 了解如何選擇最有效率的作業，利用 Azure HDInsight 在 Apache Spark 上處理您的資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 3ed4f8d4d8ca0a68a4ccf01a38ae5f8e66cc26df
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757824"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Apache Spark 的資料處理優化

本文討論如何最佳化 Apache Spark 叢集的設定，讓 Azure HDInsight 發揮最佳效能。

## <a name="overview"></a>概觀

如果在聯結或重組有緩慢的作業，原因可能是「資料扭曲」。 資料扭曲就是作業資料的不對稱。 例如，對應作業可能需要 20 秒。 但是執行連結或重組資料的作業需要數小時。 若要修正資料扭曲，您應該將整個索引鍵設定為 salt，或僅針對一部分的索引鍵使用「隔離 salt」。 如果您使用隔離 salt，應該進一步篩選來隔離對應聯結中一部分的 salt 索引鍵。 另一個選項是導入貯體資料行並先在貯體中預先彙總。

造成緩慢聯結的另一個因素可能是聯結類型。 Spark 預設使用 `SortMerge` 聯結類型。 這種聯結最適用於大型資料集。 但是佔用大量運算資源，因為其必須先將左邊和右邊的資料排序，再合併資料。

`Broadcast` 聯結最適合使用較小的資料集，或聯結的一端遠小於另一端的情況。 這種聯結會將一端廣播到所有的執行程式，因此一般而言需要較多記憶體用於廣播。

您也可以設定 `spark.sql.autoBroadcastJoinThreshold` 在您的設定中變更聯結類型，也可以使用資料框架 API (`dataframe.join(broadcast(df2))`) 設定聯結提示。

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

如果您使用貯體資料表，則您有第三個聯結類型：`Merge` 聯結。 經過正確預先資料分割和預先排序的資料集將略過 `SortMerge` 聯結中佔用大量資源的排序階段。

聯結順序相當重要，尤其是更複雜的查詢。 先從大多數選擇性聯結開始。 此外，盡可能移動彙總之後會增加資料列數目的聯結。

若要管理笛卡兒聯結的平行處理，您可以加入巢狀結構、視窗化，而且可略過 Spark 作業中的一或多個步驟。

## <a name="optimize-job-execution"></a>最佳化作業執行

* 必要時進行快取，例如，如果您使用資料兩次，則快取資料。
* 將變數廣播到所有執行程式。 變數只會序列化一次，因此查閱更快。
* 使用驅動程式上的執行緒集區，會使許多工作的作業加快。

定期監視執行中工作的效能問題。 如果您需要深入了解特定問題，請考慮下列其中一個效能分析工具：

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) 會監視 CPU、儲存體和網路頻寬使用量。
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) 可分析 Spark 和執行程式的程式碼。

Spark 2.x 查詢效能的關鍵是 Tungsten 引擎，這取決於整體階段程式碼產生。 在某些情況下，可能會停用整體階段程式碼產生。 例如，如果您在彙總運算式中使用不可變類型 (`string`)，會出現 `SortAggregate` 而非 `HashAggregate`。 例如，為了提升效能，請嘗試下列方法，然後重新啟用程式碼產生：

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>後續步驟

* [最佳化 Apache Spark 的資料儲存體](optimize-data-storage.md)
* [最佳化 Apache Spark 的記憶體使用量](optimize-memory-usage.md)
* [最佳化 Apache Spark 的叢集設定](optimize-cluster-configuration.md)
