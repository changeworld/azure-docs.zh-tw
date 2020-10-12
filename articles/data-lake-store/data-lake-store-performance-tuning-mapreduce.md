---
title: Azure Data Lake Storage Gen1 效能微調-MapReduce
description: 瞭解 Azure Data Lake Storage Gen1 中 MapReduce 的效能調整，包括參數、指引、範例計算和限制。
author: stewu
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 03c35d0af97cf24d1683d0ff21f10a0371391616
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88190210"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight 和 Azure Data Lake Storage Gen1 上的 MapReduce 效能微調方針

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 如需有關如何建立的指示，請參閱 [開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。
* **在 HDInsight 上使用 MapReduce**。 如需詳細資訊，請參閱[在 HDInsight 上的 Hadoop 中使用 MapReduce](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **複習 Data Lake Storage Gen1 的效能微調指導方針**。 如需一般效能概念，請參閱[Data Lake Storage Gen1 效能微調指導](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)方針

## <a name="parameters"></a>參數

在執行 MapReduce 作業時，您可以設定以下最重要的參數，以在 Data Lake Storage Gen1 上增加效能︰

|參數      | 描述  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  配置給每個對應器的記憶體數量。  |
|`Mapreduce.job.maps`     |  每個作業的對應工作數目。  |
|`Mapreduce.reduce.memory.mb`     |  要配置給每個歸納器的記憶體數量。  |
|`Mapreduce.job.reduces`    |   每項作業的縮減工作數目。  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce. memory/Mapreduce。減少記憶體

根據對應和/或減少工作所需的記憶體數量來調整此數位。 您可以透過 Yarn 設定， `mapreduce.map.memory` 在 Ambari 中查看和的預設值 `mapreduce.reduce.memory` 。 在 Ambari 中，流覽至 YARN 並查看 **[選項** ] 索引標籤。將會顯示 YARN 的記憶體。

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce. maps/Mapreduce. 作業。減少

這會決定要建立的對應程式數目或歸納器的最大數目。 分割數目會決定要為 MapReduce 工作建立多少對應程式數目。 因此，當分割數目少於所要求的對應程式數目數目時，可能會比您所要求的對應程式數目少。

## <a name="guidance"></a>指引

### <a name="step-1-determine-number-of-jobs-running"></a>步驟1：判斷正在執行的作業數目

根據預設，MapReduce 會為您的作業使用整個叢集。 您可以使用少於可用容器的對應程式數目，來使用較少的叢集。 本文中的指導方針假設您的應用程式是叢集上唯一執行的應用程式。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>步驟2：設定 mapreduce. map. memory/mapreduce. memory

對應和縮減工作的記憶體大小會取決於您的特定作業。 如果您想要增加並行能力，您可以減少記憶體大小。 並行執行工作的數目取決於容器數目。 藉由降低每個對應器或歸納器的記憶體數量，即可建立更多容器，而讓更多的對應器或歸納器並行執行。 減少太多的記憶體數量可能會導致某些處理程序耗盡記憶體。 如果您在執行作業時遇到堆積錯誤，請增加每個對應器或歸納器的記憶體。 請考慮新增更多容器會為每個額外的容器增加額外負荷，這可能會降低效能。 另一個方式是使用擁有較高數量記憶體的叢集，或增加叢集中的節點數目，以獲得更多的記憶體。 更多的記憶體就能使用更多的容器，亦即會有更多並行能力。

### <a name="step-3-determine-total-yarn-memory"></a>步驟3：決定總 YARN 記憶體

若要微調 mapreduce. map/mapreduce. 作業，請考慮可供使用的總 YARN 記憶體數量。 這項資訊可在 Ambari 中取得。 流覽至 **YARN，並查看 [選項** ] 索引標籤。YARN 記憶體會顯示在此視窗中。 將 YARN 記憶體乘以叢集中的節點數目，以取得總 YARN 記憶體。

`Total YARN memory = nodes * YARN memory per node`

如果您使用的是空的叢集，則記憶體可以是叢集的 YARN 記憶體總計。 如果有其他應用程式使用記憶體，則您可以選擇僅使用部分的叢集記憶體，方法是將對應器或歸納器的數量減少為您想要使用的容器數量。

### <a name="step-4-calculate-number-of-yarn-containers"></a>步驟4：計算 YARN 容器的數目

YARN 容器會決定作業可用的並行數量。 取得 YARN 記憶體總數，然後除以 mapreduce.map.memory。

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>步驟 5：設定 mapreduce.job.maps/mapreduce.job.reduces

將 mapreduce.job.maps/mapreduce.job.reduces 設定為至少是可用容器的數目。 您可以進一步試驗，將對應器和歸納器的數目增加，看看是否能獲得更好的效能。 請記住，更多的對應器會產生額外的負擔，因此對應器過多可能會降低效能。

CPU 排程和 CPU 隔離預設會關閉，因此 YARN 容器的數目會受記憶體所限制。

## <a name="example-calculation"></a>計算範例

設想您目前有由 8 個 D14 節點所組成的叢集，而且您想要執行 I/O 密集作業。 以下是您應該進行的計算︰

### <a name="step-1-determine-number-of-jobs-running"></a>步驟1：判斷正在執行的作業數目

在我們的範例中，我們假設我們的作業是唯一正在執行的作業。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>步驟2：設定 mapreduce. map. memory/mapreduce. memory

在我們的範例中，您正在執行 i/o 密集的作業，並決定 3 GB 的記憶體用於對應工作。

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>步驟3：決定總 YARN 記憶體

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

3. 如果您正在進行節流，您將會在 YARN 記錄檔中看到 HTTP 429 錯誤碼。 YARN 記錄檔位於 /tmp/&lt;user&gt;/yarn.log 中

## <a name="examples-to-run"></a>要執行的範例

為了示範 MapReduce 在 Data Lake Storage Gen1 上的執行方式，以下是使用下列設定在叢集上執行的一些範例程式碼：

* 16 節點 D14v2
* 執行 HDI 3.6 的 Hadoop 叢集

以下是一些用來執行 MapReduce Teragen、Terasort 和 Teravalidate 的範例命令，您可以從這邊來著手。 您可以根據您的資源調整這些命令。

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
