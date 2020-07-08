---
title: Apache Spark 效能-Azure HDInsight IO 快取（預覽）
description: 了解 Azure HDInsight IO 快取以及如何使用它來改進 Apache Spark 效能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 3e724e6336163a092c9b4385324b1aa037295bb6
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86081752"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>使用 Azure HDInsight IO 快取來改善 Apache Spark 工作負載的效能

IO 快取是適用於 Azure HDInsight 的資料快取服務，可改進 Apache Spark 作業效能。 IO 快取也可搭配 [Apache TEZ](https://tez.apache.org/) 與 [Apache Hive](https://hive.apache.org/) 工作負載使用，這些工作負載可在 [Apache Spark](https://spark.apache.org/) 叢集上執行。 IO 快取使用稱為 RubiX 的開放原始碼快取元件。 RubiX 是可搭配巨量資料分析引擎使用的本機磁碟快取，這些引擎通常會從雲端儲存體系統存取資料。 RubiX 在快取系統中是獨一無二的，因為它針對快取用途使用固態硬碟 (SSDs) 而非保留作業記憶體。 IO 快取服務會在叢集的每個背景工作角色上啟動並管理 RubiX 中繼資料伺服器。 它也會設定叢集的所有服務，在背景使用 RubiX 快取。

大部分的 SSD 都提供每秒超過 1 GB 的頻寬。 此頻寬加上作業系統記憶體內檔案快取的幫助，提供足夠的頻寬來載入巨量資料計算處理引擎，例如 Apache Spark。 作業系統會保留給 Apache Spark 來處理耗用大量記憶體的繁重工作，例如輪換。 擁有專屬的作業記憶體使用權讓 Apache Spark 可以達成最佳資源使使用狀況。  

> [!Note]  
> IO 快取目前使用 RubiX 做為快取元件，但在未來的服務版本中可能會變更。 請使用 IO 快取介面，而不要直接相依於任何 RubiX 實作。
>目前只有 Azure BLOB 儲存體支援 IO 快取。

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Azure HDInsight IO 快取的優點

使用 IO 快取提供可為從 Azure Blob 儲存體讀取資料的作業提高效能。

使用 IO 快取時，您不需要對您的 Spark 作業進行任何變更，就可以看到效能改進。 當停用 IO 快取時，此 Spark 程式碼可從遠端 Azure Blob 儲存體讀取資料：`spark.read.load('wasbs:///myfolder/data.parquet').count()`。 當啟用 IO 快取時，相同的程式碼會讓應用程式透過 IO 快取進行快取讀取。 在後續的讀取作業中，會從本機 SSD 讀取資料。 HDInsight 叢集上的背景工作角色節點是本機連結的專屬 SSD 磁碟機。 HDInsight IO 快取使用這些本機 SSD 來進行快取，這樣可提供最低層級的延遲並讓頻寬最佳化。

## <a name="getting-started"></a>開始使用

Azure HDInsight IO 快取在預覽版中預設是停用的。 您可以在 Azure HDInsight 3.6+ Spark 叢集中使用 IO 快取，這些叢集執行 Apache Spark 2.3。  若要在 HDInsight 4.0 上啟用 IO 快取，請執行下列步驟：

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 選取左邊的 [IO 快取]****。

1. 選取 [**動作**] （HDI 3.6 中的**服務動作**）和 [**啟動**]。

    ![在 Ambari 中啟用 IO 快取服務](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "在 Ambari 中啟用 IO 快取服務")

1. 確認叢集上的所有受影響服務都已重新啟動。

> [!NOTE]  
> 雖然進度列顯示已啟用，但 IO 快取在您重新啟動其他受影響的服務之前實際上不會啟用。

## <a name="troubleshooting"></a>疑難排解
  
啟用 IO 快取之後，執行 Spark 作業時可能會發生磁碟空間錯誤。 發生這些錯誤的原因是 Spark 在輪換作業期間也使用本機磁碟儲存體來存放資料。 當 IO 快取已啟用且用於 Spark 儲存體的空間減少時，Spark 可能會用盡 SSD 空間。 IO 快取所使用的空間量預設是總 SSD 空間的一半。 您可以在 Ambari 中設定 IO 快取的磁碟空間使用量。 若發生磁碟空間錯誤，請減少用於 IO 快取的 SSD 空間量，並重新啟動服務。 若要變更為 IO 快取設定的空間，請執行下列步驟：

1. 在 Apache Ambari 中，選取左邊的 **HDFS** 服務。

1. 選取 [設定]**** 與 [進階]**** 索引標籤。

    ![編輯 HDFS Advanced Configuration](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "編輯 HDFS Advanced Configuration")

1. 向下捲動並展開 [自訂核心網站]**** 區域。

1. 尋找 **hadoop.cache.data.fullness.percentage** 屬性。

1. 在方塊中變更值。

    ![編輯 IO 快取飽和度百分比](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "編輯 IO 快取飽和度百分比")

1. 選取右上角的 [儲存]****。

1. 選取 [**重新**啟動  >  **所有受影響**的]。

    ![Apache Ambari 會重新開機所有受影響的](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "重新開機所有受影響的")

1. 選取 [**確認全部重新開機**]。

如果沒有作用，請停用 IO 快取。

## <a name="next-steps"></a>後續步驟

在此部落格文章中深入了解 IO 快取，包括效能基準測試：[Apache Spark 作業透過 HDInsight IO 快取獲得快 9 倍的速度](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/) \(英文\)
