---
title: 最佳化 Spark 作業的效能 - Azure HDInsight
description: 顯示讓 Azure HDInsight 中的 Apache Spark 叢集發揮最佳效能的常用策略。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117954"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>將 HDInsight 中的 Apache Spark 作業最佳化

本文提供將 Azure HDInsight 上的 Apache Spark 作業最佳化的策略概觀。

## <a name="overview"></a>概觀

Apache Spark 作業的效能取決於多個因素。 這些效能因素包括：資料的儲存方式、叢集的設定方式，以及處理資料時使用的作業。

您可能面臨的常見挑戰包括：因不正確調整的執行程式、長時間執行的作業，以及導致笛卡兒作業的工作所造成的記憶體限制。

還有許多優化功能，可協助您克服這些挑戰，例如快取，並允許資料扭曲。

在下列每一篇文章中，您可以找到 Spark 優化不同層面的相關資訊。

* [最佳化 Apache Spark 的資料儲存體](optimize-data-storage.md)
* [最佳化 Apache Spark 的資料處理](optimize-data-processing.md)
* [最佳化 Apache Spark 的記憶體使用量](optimize-memory-usage.md)
* [優化 Apache Spark 的 HDInsight 叢集設定](optimize-cluster-configuration.md)

## <a name="next-steps"></a>後續步驟

* [對 Azure HDInsight 上執行的 Apache Spark 作業進行偵錯](apache-spark-job-debugging.md)
* [在 HDInsight 上管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [設定 Apache Spark 設定](apache-spark-settings.md)
