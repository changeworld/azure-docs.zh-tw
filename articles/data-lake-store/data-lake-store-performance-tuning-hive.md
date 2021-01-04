---
title: 效能微調-Azure Data Lake Storage Gen1 上的 Hive
description: 瞭解 HdInsight 上的 Hive 和 Azure Data Lake Storage Gen1 的效能微調。 針對 i/o 密集的查詢，請調整 Hive 以取得較佳的效能。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723776"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight 和 Azure Data Lake Storage Gen1 上的 Hive 效能微調指導方針

預設設定已設定好，以便在許多不同的使用案例中提供良好的效能。  針對 I/O 密集的查詢，Hive 可進行微調，以在 Azure Data Lake Storage Gen1 取得更佳效能。  

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Data Lake Storage Gen1 帳戶**。 如需有關如何建立的指示，請參閱 [開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。
* **在 HDInsight 上執行 Hive**。  若要了解如何在 HDInsight 上執行 Hive 作業，請參閱[ HDInsight 上使用 Hive](../hdinsight/hadoop/hdinsight-use-hive.md)
* **Data Lake Storage Gen1 的效能微調方針**。  如需一般效能概念，請參閱[Data Lake Storage Gen1 效能微調指導](./data-lake-store-performance-tuning-guidance.md)方針

## <a name="parameters"></a>參數

以下是要改善 Data Lake Storage Gen1 效能所應微調的最重要設定：

* **hive.tez.container.size** – 每個工作所使用的記憶體數量

* **tez.grouping.min-size** – 每個對應器的大小下限

* **tez.grouping.max-size** – 每個對應器的大小上限

* **hive.exec.reducer.bytes.per.reducer** – 每個歸納器的大小

**hive.tez.container.size** - 容器大小會決定每個工作可以使用多少記憶體。  這是用來控制 Hive 中之並行能力的主要輸入。  

**tez.grouping.min-size** – 此參數可讓您設定每個對應器的大小下限。  如果 Tez 選擇的對應器數目小於此參數的值，Tez 會使用此處設定的值。

**tez.grouping.max-size** – 此參數可讓您設定每個對應器的大小上限。  如果 Tez 選擇的對應器數目大於此參數的值，Tez 會使用此處設定的值。

**hive.exec.reducer.bytes.per.reducer** – 此參數會設定每個歸納器的大小。  根據預設，每個歸納器為 256 MB。  

## <a name="guidance"></a>指引

**Set hive.exec.reducer.bytes.per.reducer** – 資料若未壓縮，預設值就很適用。  資料若有壓縮，則應縮減歸納器的大小。  

**Set hive.tez.container.size** – 在每個節點中，記憶體會由 yarn.nodemanager.resource.memory-mb 指定，且預設應該會在 HDI 叢集上正確設定。  如需在 YARN 中設定適當記憶體的詳細資訊，請參閱這篇[文章](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md)。

I/O 密集工作負載可以透過減少 Tez 容器大小，而從更符合平行處理原則受益。 這會讓使用者獲得更多容器，而增加並行能力。  不過，某些 Hive 查詢需要大量的記憶體 (例如 MapJoin)。  如果工作沒有足夠的記憶體，您會在執行階段期間遇到記憶體不足的例外狀況。  如果您遇到記憶體不足的例外狀況，則應增加記憶體。   

並行執行的工作數或平行處理原則會受到 YARN 記憶體總數的限制。  YARN 容器數目會決定可以執行多少並行工作。  若要尋找每個節點的 YARN 記憶體，您可以前往 Ambari。  流覽至 YARN，並查看 [選項] 索引標籤。 YARN 記憶體會顯示在此視窗中。  

> 總 YARN 記憶體 = 節點 * YARN 容器的每個節點 YARN 記憶體數目 = YARN 記憶體/Tez 容器大小總計

使用 Data Lake Storage Gen1 來改善效能的關鍵是盡可能地增加並行能力。  Tez 會自動計算應該建立的工作數目，因此您並不需要設定。   

## <a name="example-calculation"></a>範例計算

假設您有 8 節點的 D14 叢集。  

> 總 YARN 記憶體 = 節點 * 每個節點的 YARN 記憶體總計 YARN 記憶體 = 8 個節點 * 96GB = 768GB YARN 的容器數目 = 768GB/3072MB = 256

## <a name="limitations"></a>限制

**Data Lake Storage Gen1 節流** 

如果您達到 Data Lake Storage Gen1 所提供的頻寬限制，則會開始看到工作失敗。 透過觀察工作記錄中的節流錯誤即可加以識別。  您可以藉由增加 Tez 容器大小來減少平行處理原則。  如果您的作業需要更多並行能力，請與我們連絡。

若要檢查您是否遭到節流，您必須在用戶端啟用偵錯記錄。 做法如下：

1. 將下列屬性放在 Hive 設定的 log4j 屬性中。這可以從 Ambari view： log4j 完成，例如，會重新開機所有節點/服務，以便讓設定生效。

2. 如果您遭到節流，您會看到 Hive 記錄檔中有 HTTP 429 錯誤碼。 Hive 記錄檔位於 /tmp/&lt;user&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>關於微調 Hive 的進一步資訊

以下是一些有助於微調 Hive 查詢的部落格︰
* [在 Hdinsight 中最佳化 Hadoop 的 Hive 查詢](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [在 Azure HDInsight 中編碼 Hive 查詢檔案](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [Ignite 講解如何將 HDInsight 上的 Hive 最佳化](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)