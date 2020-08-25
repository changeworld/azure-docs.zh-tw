---
title: 以 Apache Spark 最佳化記憶體使用量 - Azure HDInsight
description: 了解如何在 Azure HDInsight 以 Apache Spark 最佳化記憶體使用量。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 056060f8b94747651c78c757150d5e5a5982c7af
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757773"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Apache Spark 的記憶體使用量優化

本文討論如何最佳化 Apache Spark 叢集的記憶體管理，讓 Azure HDInsight 發揮最佳效能。

## <a name="overview"></a>概觀

Spark 的運作方式是將資料放在記憶體中。 因此管理記憶體資源是 Spark 工作執行最佳化的重要層面。  有數種技術可以有效使用叢集的記憶體。

* 偏好較小的資料分割區，並在分割策略中考量資料大小、類型和發佈。
* 請考慮更新、更有效率的 [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo)，而不是預設的 Java 序列化。
* 習慣使用 YARN，因為它會按批次區分 `spark-submit`。
* 監視和微調 Spark 組態設定。

下圖顯示 Spark 記憶體結構，以及部分索引鍵執行程式記憶體參數，以供您參考。

## <a name="spark-memory-considerations"></a>Spark 記憶體考量

如果您使用 Apache Hadoop YARN，則 YARN 會控制每個 Spark 節點上的所有容器使用的記憶體。  下圖顯示索引鍵物件及其關聯性。

![YARN Spark 記憶體管理](./media/apache-spark-perf/apache-yarn-spark-memory.png)

若要處理「記憶體不足」訊息，請嘗試：

* 檢閱 DAG 管理重組。 以對應端減少、預先分割 (或貯體分類) 來源資料加以減少、最大化單一重組，並減少傳送的資料量。
* 偏好固定記憶體限制為 `GroupByKey` 的 `ReduceByKey`，這會提供彙總、視窗化和其他功能，但是有無限制的記憶體限制。
* 偏好對於執行程式或資料分割進行較多作業的 `TreeReduce`，而不偏好對於驅動程式進行所有作業的 `Reduce`。
* 使用資料框架，而非使用較低層級 RDD 物件。
* 建立封裝動作的 ComplexTypes，例如「前 N 項」、各種彙總或視窗化作業。

如需其他疑難排解步驟，請參閱 [Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 例外狀況](apache-spark-troubleshoot-outofmemory.md)。

## <a name="next-steps"></a>後續步驟

* [最佳化 Apache Spark 的資料處理](optimize-cluster-configuration.md)
* [最佳化 Apache Spark 的資料儲存體](optimize-data-storage.md)
* [最佳化 Apache Spark 的叢集設定](optimize-cluster-configuration.md)
