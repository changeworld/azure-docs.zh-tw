---
title: Azure 資料存儲第 1 代性能調優 - 映射縮減
description: Azure Data Lake Storage Gen1 MapReduce 效能微調方針
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904596"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight 和 Azure Data Lake Storage Gen1 上的 MapReduce 效能微調方針

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 有關如何創建一個說明的說明，請參閱[開始使用 Azure 資料存儲第 1 代](data-lake-store-get-started-portal.md)
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。
* **在 HDInsight 上使用地圖縮減**。 如需詳細資訊，請參閱[在 HDInsight 上的 Hadoop 中使用 MapReduce](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **查看資料存儲庫第 1 代的性能調優指南**。 有關一般性能概念，請參閱[資料存儲庫第 1 代性能調優指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>參數

在執行 MapReduce 作業時，您可以設定以下最重要的參數，以在 Data Lake Storage Gen1 上增加效能︰

|參數      | 描述  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  要分配給每個映射器的記憶體量。  |
|`Mapreduce.job.maps`     |  每個作業的映射任務數。  |
|`Mapreduce.reduce.memory.mb`     |  要分配給每個減量器的記憶體量。  |
|`Mapreduce.job.reduces`    |   每個作業的縮減任務數。  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>映射減少.map.記憶體/映射減少.減少.記憶體

根據地圖和/或減少任務所需的記憶體量調整此數位。 您可以通過 Yarn 配置查看`mapreduce.map.memory`和`mapreduce.reduce.memory`的預設值。 在 Ambari 中，導航到 YARN 並查看 **"配置"** 選項卡。將顯示 YARN 記憶體。

### <a name="mapreducejobmaps--mapreducejobreduces"></a>映射減少.作業.地圖/映射縮減.作業.減少

這將確定要創建的映射器或縮減器的最大數量。 拆分數確定為 MapReduce 作業創建的映射器數。 因此，如果拆分數少於請求的映射器數，則獲取的映射器可能比請求的映射器數少。

## <a name="guidance"></a>指引

### <a name="step-1-determine-number-of-jobs-running"></a>第 1 步：確定正在運行的作業數

根據預設，MapReduce 會為您的作業使用整個叢集。 通過使用較少的映射器，可以使用較少的群集。 本文中的指導方針假設您的應用程式是叢集上唯一執行的應用程式。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>步驟 2：設置映射縮減.map.記憶體/映射縮減.減值.記憶體

對應和縮減工作的記憶體大小會取決於您的特定作業。 如果您想要增加並行能力，您可以減少記憶體大小。 並行執行工作的數目取決於容器數目。 藉由降低每個對應器或歸納器的記憶體數量，即可建立更多容器，而讓更多的對應器或歸納器並行執行。 減少太多的記憶體數量可能會導致某些處理程序耗盡記憶體。 如果在運行作業時出現堆錯誤，請增加每個映射器或縮減器的記憶體。 請考慮添加更多容器會為每個附加容器增加額外的開銷，這可能會降低性能。 另一個方式是使用擁有較高數量記憶體的叢集，或增加叢集中的節點數目，以獲得更多的記憶體。 更多的記憶體就能使用更多的容器，亦即會有更多並行能力。

### <a name="step-3-determine-total-yarn-memory"></a>第 3 步：確定 YARN 記憶體總數

要調整 mapreduce.job.map/mapreduce.job.job.reduce.減少，請考慮可以使用的 YARN 記憶體總量。 這項資訊可在 Ambari 中取得。 導航到 YARN 並查看 **"配置"** 選項卡。YARN 記憶體將顯示在此視窗中。 將 YARN 記憶體與群集中的節點數相乘，以獲得總 YARN 記憶體。

`Total YARN memory = nodes * YARN memory per node`

如果使用空群集，則記憶體可以是群集的總 YARN 記憶體。 如果有其他應用程式使用記憶體，則您可以選擇僅使用部分的叢集記憶體，方法是將對應器或歸納器的數量減少為您想要使用的容器數量。

### <a name="step-4-calculate-number-of-yarn-containers"></a>第 4 步：計算 YARN 容器的數量

YARN 容器會決定作業可用的並行數量。 取得 YARN 記憶體總數，然後除以 mapreduce.map.memory。

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>步驟 5：設定 mapreduce.job.maps/mapreduce.job.reduces

將 mapreduce.job.maps/mapreduce.job.reduces 設定為至少是可用容器的數目。 您可以進一步試驗，將對應器和歸納器的數目增加，看看是否能獲得更好的效能。 請記住，更多的對應器會產生額外的負擔，因此對應器過多可能會降低效能。

CPU 排程和 CPU 隔離預設會關閉，因此 YARN 容器的數目會受記憶體所限制。

## <a name="example-calculation"></a>計算範例

設想您目前有由 8 個 D14 節點所組成的叢集，而且您想要執行 I/O 密集作業。 以下是您應該進行的計算︰

### <a name="step-1-determine-number-of-jobs-running"></a>第 1 步：確定正在運行的作業數

對於我們的示例，我們假設我們的工作是唯一正在運行的工作。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>步驟 2：設置映射縮減.map.記憶體/映射縮減.減值.記憶體

對於我們的示例，您正在運行 I/O 密集型作業，並決定地圖任務的 3 GB 記憶體就足夠了。

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>第 3 步：確定 YARN 記憶體總數

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>步驟 4︰計算 YARN 容器的數目

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>步驟 5：設定 mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>限制

**Data Lake Storage Gen1 節流**

Data Lake Storage Gen1 為多租用戶服務，因此會設定帳戶層級的頻寬限制。 如果您達到這些限制，您將會開始看到工作失敗。 透過觀察工作記錄中的節流錯誤即可加以識別。 如果您的作業需要更多頻寬，請與我們連絡。

若要檢查您是否遭到節流，您必須在用戶端啟用偵錯記錄。 做法如下：

1. 將下列屬性放在 [Ambari] > [YARN] > [設定] > [進階 yarn-log4j] 的 log4j 屬性中：log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. 重新啟動所有節點/服務，以便讓設定生效。

3. 如果您受到限制，您將在 YARN 日誌檔中看到 HTTP 429 錯誤代碼。 YARN 記錄檔位於 /tmp/&lt;user&gt;/yarn.log 中

## <a name="examples-to-run"></a>要執行的範例

為了演示 MapReduce 如何在 Data Lake 存儲 Gen1 上運行，下面是在具有以下設置的群集上運行的一些示例代碼：

* 16 節點 D14v2
* 執行 HDI 3.6 的 Hadoop 叢集

以下是一些用來執行 MapReduce Teragen、Terasort 和 Teravalidate 的範例命令，您可以從這邊來著手。 您可以根據您的資源調整這些命令。

### <a name="teragen"></a>泰拉根

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>泰拉索

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>泰拉驗證

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
