---
title: 微調效能： Spark、HDInsight & Azure Data Lake Storage Gen2 |Microsoft Docs
description: 瞭解使用 Azure HDInsight 和 Azure Data Lake Storage Gen2 來微調 Spark 效能的指導方針。
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 8ae9f96b42c0eb36a9380589780d141711c7ae4d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88034726"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>微調效能： Spark、HDInsight & Azure Data Lake Storage Gen2

在微調 Spark 的效能時，您必須考慮叢集上會執行的應用程式數目。  根據預設，您可以在 HDI 叢集上並行執行 4 個應用程式 (附註︰預設設定有可能變更)。  您可能會決定使用較少的應用程式，因此您可以覆寫預設設定，並使用更多的叢集來執行這些應用程式。  

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen2 帳戶**。 如需有關如何建立的指示，請參閱[快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶](data-lake-storage-quickstart-create-account.md)。
* 可存取 Data Lake Storage Gen2 帳戶的 **Azure HDInsight 叢集**。 請參閱[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)。 請確實為叢集啟用遠端桌面。
* **在 Data Lake Storage Gen2 上執行 Spark 叢集**。  如需詳細資訊，請參閱[使用 HDInsight Spark 叢集來分析 Data Lake Storage Gen2 中的資料](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Data Lake Storage Gen2 的效能微調方針**。  如需一般的效能概念，請參閱[Data Lake Storage Gen2 效能微調指導](data-lake-storage-performance-tuning-guidance.md)方針 

## <a name="parameters"></a>參數

在執行 Spark 作業時，以下是要增進 Data Lake Storage Gen2 效能所能微調的最重要設定︰

* **Num-executors** - 可執行的並行工作數目。

* **Executor-memory** - 配置給每個執行程式的記憶體數量。

* **Executor-cores** - 配置給每個執行程式的核心數目。                     

**Num-executors** Num-executors 會設定可平行執行的工作數目上限。  可平行執行的實際工作數目會受限於叢集中可用的記憶體和 CPU 資源。

**Executor-memory** - 這是要配置給每個執行程式的記憶體數量。  每個執行程式所需的記憶體取決於作業。  複雜的作業需要較高的記憶體。  讀取和寫入等簡單作業的記憶體需求會較低。  您可以在 Ambari 中檢視每個執行程式的記憶體數量。  在 Ambari 中，瀏覽至 Spark，然後檢視 [設定] 索引標籤。  

**Executor-cores** 這會設定每個執行程式所使用的核心數量，進而決定每個執行程式可以執行的平行執行緒數目。  例如，如果 executor-cores = 2，則每個執行程式可以在執行程式中執行 2 個平行工作。  所需的 executor-cores 取決於該作業。  需要大量 I/O 的作業所需的每一工作記憶體並不需要太多，因此每個執行程式可以處理更多的平行工作。

依預設，在 HDInsight 上執行 Spark 時，會為每個實體核心定義兩個虛擬 YARN 核心。  這個數量能在並行能力與從多個執行緒切換而來的內容數量取得良好平衡。  

## <a name="guidance"></a>指導方針

針對 Data Lake Storage Gen2 中的資料執行 Spark 分析工作負載時，我們建議您使用最新 HDInsight 版本以取得最佳的 Data Lake Storage Gen2 效能。 當您的作業較為 I/O 密集時，您可以設定某些參數以提升效能。  Data Lake Storage Gen2 是具有高擴充性的儲存體平台，可處理高輸送量。  如果作業主要包含讀取和寫入，則提升 Data Lake Storage Gen2 往返 I/O 並行能力可增進效能。

有幾種基本的方法可以提升 I/O 密集作業的並行。

**步驟 1︰確定叢集上所執行的應用程式數目** – 您應該要知道有多少個應用程式在叢集上執行，包括目前的應用程式。  每個 Spark 設定的預設值會假設有 4 個應用程式在並行執行。  因此，每個應用程式只能使用 25% 的叢集。  若要獲得更好的效能，您可以變更執行程式的數目來覆寫預設值。  

**步驟2：設定執行程式-記憶體**–要設定的第一件事是執行程式記憶體。  記憶體會取決於您要執行的作業。  您可以為每個執行程式配置較少的記憶體，以增加並行數量。  如果您在執行作業時看到記憶體不足的例外狀況，則應該增加此參數的值。  另一個方式是使用擁有較高數量記憶體的叢集，或增加叢集大小，以獲得更多的記憶體。  更多的記憶體就能使用更多的執行程式，亦即會有更多並行能力。

**步驟 3︰設定 executor-cores** – 對於沒有複雜作業的 I/O 密集工作負載，最好是先設定較高的 executor-cores 數值，以增加每個執行程式的平行工作數目。  將 executor-cores 設定為 4 是不錯的開始。   

執行程式-核心 = 4

增加 executor-cores 的數字可讓您更符合平行處理原則，因此請試試不同的 executor-cores。  對於具有較複雜作業的工作，您應該減少每個執行程式的核心數目。  如果 executor-cores 設定為高於 4，則記憶體回收可能會變得沒有效率而降低效能。

**步驟 4︰決定叢集中的 YARN 記憶體數量** – 這項資訊可在 Ambari 中取得。  流覽至 YARN，並查看 [[]] 索引標籤。 YARN 記憶體會顯示在此視窗中。  
請注意，當您位於此視窗時，您也可以查看預設的 YARN 容器大小。  YARN 容器大小和每個執行程式參數的記憶體相同。

總 YARN 記憶體 = 節點 * 每個節點的 YARN 記憶體

**步驟 5︰計算 num-executors**

**計算記憶體限制** - num-executors 參數會受到記憶體或 CPU 所限制。  記憶體限制取決於應用程式的可用 YARN 記憶體數量。  您應該取得 YARN 記憶體總數，然後除以 executor-memory。  應用程式數目的限制必須取消調整，因此我們除以應用程式數目。

記憶體條件約束 = (YARN 記憶體/執行程式記憶體總計) /應用程式數目

**計算 CPU 限制**-CPU 限制的計算方式為虛擬核心總數除以每個執行程式的核心數目。  每個實體核心有 2 個虛擬核心。  和記憶體限制類似，我們必須除以應用程式數目。

- 虛擬核心 = 節點中的 (節點 * 2 中的實體核心數) 
- CPU 條件約束 = (虛擬核心總數/每個執行程式的核心數目) /應用程式數目

**設定 num-executors**– num-executors 參數是由記憶體限制和 CPU 限制較小者來決定。 

num-執行程式 = Min (總虛擬核心數/每個執行程式的核心數目，可用的 YARN 記憶體/執行程式-記憶體) 

設定較高的 num-executors 數值未必能提升效能。  您應該考慮到，新增更多執行程式會對每個額外的執行程式新增額外的負擔，而可能降低效能。  Num-executors 受到叢集資源的限制。    

## <a name="example-calculation"></a>計算範例

假設您目前有由 8 個 D4v2 節點所組成的叢集，其中執行了 2 個應用程式 (包括您要執行的應用程式在內)。  

**步驟 1︰確定叢集上所執行的應用程式數目** – 您知道您的叢集上有 2 個應用程式 (包括您要執行的應用程式在內)。  

**步驟 2︰設定 executor-memory** – 在此範例中，我們判斷 6 GB 的 executor-memory 就足夠 I/O 密集作業使用。  

執行程式-記憶體 = 6GB

**步驟 3︰設定 executor-cores** – 這是 I/O 密集作業，因此我們可以將每個執行程式的核心數目設為 4。  將每個執行程式的核心設為大於 4，可能會造成記憶體回收問題。  

執行程式-核心 = 4

**步驟 4︰決定叢集中的 YARN 記憶體數量** – 我們瀏覽至 Ambari，發現每個 D4v2 有 25 GB 的 YARN 記憶體。  由於有 8 個節點，可用的 YARN 記憶體會乘以 8。

- 總 YARN 記憶體 = 節點 * 每個節點的 YARN 記憶體 *
- 總 YARN 記憶體 = 8 個節點 * 25GB = 200GB

**步驟 5︰計算 num-executors** – num-executors 參數是由記憶體限制和 CPU 限制較小者除以 Spark 上執行的應用程式數目來決定。    

**計算記憶體限制** – 記憶體限制的計算方式為 YARN 記憶體總數除以每個執行程式的記憶體。

- 記憶體條件約束 = (YARN 記憶體/執行程式記憶體總計) /應用程式數目
- 記憶體條件約束 = (200GB/6GB) /2
- 記憶體條件約束 = 16 (四捨五入) 

**計算 CPU 限制**-CPU 限制的計算方式為 YARN 核心總數除以每個執行程式的核心數目。

- YARN 核心 = 叢集中的節點 * 每個節點的核心數 * 2
- YARN 核心 = 8 個節點 * 每個 D14 8 個核心 * 2 = 128
- CPU 條件約束 = (總 YARN 核心數/每個執行程式的核心數目) /應用程式數目
- CPU 條件約束 = (128/4) /2
- CPU 條件約束 = 16

**設定 num-executors**

- num-執行次數 = Min (記憶體條件約束，CPU 條件約束) 
- num-執行次數 = Min (16，16) 
- num-執行次數 = 16

