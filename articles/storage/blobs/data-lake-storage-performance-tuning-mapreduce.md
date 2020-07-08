---
title: 微調效能： MapReduce、HDInsight & Azure Data Lake Storage Gen2 |Microsoft Docs
description: Azure Data Lake Storage Gen2 MapReduce 效能微調方針
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f5de8da90ac3356480fd809af68ab2c8b30540aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465944"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>微調效能： MapReduce、HDInsight & Azure Data Lake Storage Gen2

了解在微調 Map Reduce 作業效能時應考量的因素。 本文將探討各種效能微調指導方針。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen2 帳戶**。 如需有關如何建立的指示，請參閱[快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶](data-lake-storage-quickstart-create-account.md)。
* 可存取 Data Lake Storage Gen2 帳戶的 **Azure HDInsight 叢集**。 請參閱[搭配 Azure HDInsight 叢集使用 Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **在 HDInsight 上使用 MapReduce**。  如需詳細資訊，請參閱[在 HDInsight 上的 Hadoop 中使用 MapReduce](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Data Lake Storage Gen2 的效能微調方針**。  如需一般的效能概念，請參閱[Data Lake Storage Gen2 效能微調指導](data-lake-storage-performance-tuning-guidance.md)方針

## <a name="parameters"></a>參數

在執行 MapReduce 作業時，您可以設定以下參數，以在 Data Lake Storage Gen2 上增加效能︰

* **Mapreduce.map.memory.mb** – 要配置給每個對應器的記憶體數量
* **Mapreduce.job.maps** – 每個作業的對應工作數目
* **Mapreduce.reduce.memory.mb** – 要配置給每個歸納器的記憶體數量
* **Mapreduce.job.reduces** – 每個作業的縮減工作數目

**Mapreduce.map.memory / Mapreduce.reduce.memory** 這個數字應根據對應和/或縮減工作需要多少記憶體來進行調整。  mapreduce.map.memory 和 mapreduce.reduce.memory 的預設值可以在 Ambari 中透過 Yarn 組態來檢視。  在 Ambari 中，流覽至 YARN，並查看 [[]] 索引標籤。 將會顯示 YARN 記憶體。  

**Mapreduce.job.maps / Mapreduce.job.reduces** 這會決定要建立的對應器或歸納器數目上限。  分割數會決定要為 MapReduce 作業建立多少對應器。  因此，如果分割數比要求的對應器數目少，您所得到的對應器可能會比您要求的少。       

## <a name="guidance"></a>指引

> [!NOTE]
> 本文中的指導方針假設您的應用程式是叢集上唯一執行的應用程式。

**步驟1：判斷執行的作業數目**

根據預設，MapReduce 會為您的作業使用整個叢集。  您可以使用比可用容器還少的對應器，來使用較少的叢集。        

**步驟2：設定 mapreduce. map. memory/mapreduce. memory**

對應和縮減工作的記憶體大小會取決於您的特定作業。  如果您想要增加並行能力，您可以減少記憶體大小。  並行執行工作的數目取決於容器數目。  藉由降低每個對應器或歸納器的記憶體數量，即可建立更多容器，而讓更多的對應器或歸納器並行執行。  減少太多的記憶體數量可能會導致某些處理程序耗盡記憶體。  如果您在執行作業時遇到堆積錯誤，您應該增加每個對應器或歸納器的記憶體。  您應該考慮到，新增更多容器會對每個額外的容器新增額外的負擔，而可能降低效能。  另一個方式是使用擁有較高數量記憶體的叢集，或增加叢集中的節點數目，以獲得更多的記憶體。  更多的記憶體就能使用更多的容器，亦即會有更多並行能力。  

**步驟 3︰確定 YARN 記憶體總數**

若要調整 mapreduce.job.maps/mapreduce.job.reduces，您應該考慮可供使用的 YARN 記憶體總數。  這項資訊可在 Ambari 中取得。  流覽至 YARN，並查看 [[]] 索引標籤。 YARN 記憶體會顯示在此視窗中。  您應該將 YARN 記憶體乘上叢集中的節點數目，以算出 YARN 記憶體總數。

    Total YARN memory = nodes * YARN memory per node

如果您使用空白叢集，則記憶體會是叢集的 YARN 記憶體總數。  如果有其他應用程式使用記憶體，則您可以選擇僅使用部分的叢集記憶體，方法是將對應器或歸納器的數量減少為您想要使用的容器數量。  

**步驟4：計算 YARN 容器的數目**

YARN 容器會決定作業可用的並行數量。  取得 YARN 記憶體總數，然後除以 mapreduce.map.memory。  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**步驟 5：設定 mapreduce.job.maps/mapreduce.job.reduces**

將 mapreduce.job.maps/mapreduce.job.reduces 設定為至少是可用容器的數目。  您可以進一步試驗，將對應器和歸納器的數目增加，看看是否能獲得更好的效能。  請記住，更多的對應器會產生額外的負擔，因此對應器過多可能會降低效能。  

CPU 排程和 CPU 隔離預設會關閉，因此 YARN 容器的數目會受記憶體所限制。

## <a name="example-calculation"></a>計算範例

假設我們有由 8 個 D14 節點所組成的叢集，而且我們想要執行 I/O 密集作業。  以下是您應該進行的計算︰

**步驟1：判斷執行的作業數目**

在此範例中，讓我們假設我們的作業是唯一會執行的作業。  

**步驟2：設定 mapreduce. map. memory/mapreduce. memory**

在此範例中，我們會執行 I/O 密集作業，並決定 3GB 的記憶體就足以執行對應工作。

    mapreduce.map.memory = 3GB

**步驟 3︰確定 YARN 記憶體總數**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**步驟 4︰計算 YARN 容器的數目**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**步驟 5：設定 mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>要執行的範例

為示範 MapReduce 在 Data Lake Storage Gen2 中的執行方式，以下提供一些使用下列設定在叢集上執行的範例程式碼︰

* 16 節點 D14v2
* 執行 HDI 3.6 的 Hadoop 叢集

以下是一些用來執行 MapReduce Teragen、Terasort 和 Teravalidate 的範例命令，您可以從這邊來著手。  您可以根據您的資源調整這些命令。

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
