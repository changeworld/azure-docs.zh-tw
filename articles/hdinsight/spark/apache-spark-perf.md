---
title: 最佳化 Spark 作業的效能 - Azure HDInsight
description: 顯示讓 Azure HDInsight 中的 Apache Spark 叢集發揮最佳效能的常用策略。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780100"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>將 HDInsight 中的 Apache Spark 作業最佳化

本文提供將 Azure HDInsight 上的 Apache Spark 作業最佳化的策略概觀。

## <a name="overview"></a>概觀

Apache Spark 作業的效能取決於多個因素。 這些效能因素包括：資料的儲存方式、叢集的設定方式，以及處理資料時使用的作業。

您可能面臨的常見挑戰包括因為執行程式大小不當而受限於記憶體、作業長時間執行，以及工作需要笛卡兒運算。

另有各種策略可協助您克服這些挑戰，例如快取和允許資料扭曲。

在下列每一篇文章中，您可以找到 Spark 最佳化各種層面的常見挑戰和解決方案。

* [最佳化資料儲存體](optimize-data-storage.md)
* [最佳化資料處理](optimize-data-processing.md)
* [最佳化記憶體使用量](optimize-memory-usage.md)
* [最佳化叢集設定](optimize-cluster-configuration.md)

## <a name="next-steps"></a>後續步驟

* [對 Azure HDInsight 上執行的 Apache Spark 作業進行偵錯](apache-spark-job-debugging.md)
* [在 HDInsight 上管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [設定 Apache Spark 設定](apache-spark-settings.md)
