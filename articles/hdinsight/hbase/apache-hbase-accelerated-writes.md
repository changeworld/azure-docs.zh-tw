---
title: 適用於 Apache HBase 的 Azure HDInsight 加速寫入
description: 概述 Azure HDInsight 加速寫入功能，其使用 premium 受控磁片來改善 Apache HBase 寫入記錄檔的效能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76764603"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>適用於 Apache HBase 的 Azure HDInsight 加速寫入

本文提供 Azure HDInsight 中 Apache HBase**加速寫入**功能的背景，以及如何有效地使用它來改善寫入效能。 **加速寫入**會使用[Azure premium SSD 受控磁片](../../virtual-machines/linux/disks-types.md#premium-ssd)來改善 Apache HBASE 寫入記錄（WAL）的效能。 若要深入瞭解 Apache HBase，請參閱[什麼是 HDInsight 中的 Apache hbase](apache-hbase-overview.md)。

## <a name="overview-of-hbase-architecture"></a>HBase 架構的總覽

在 HBase 中，資料列是由一個或多個資料**行**所組成，而且是由資料**列**索引**鍵**所識別。 多個資料列組成一個**資料表**。 資料行包含資料**格**，也就是該資料行中值的時間戳記版本。 資料行會分組為數據**行系列**，而資料行系列中的所有資料行都會一起儲存在稱為**HFiles**的儲存體檔案中。

HBase 中的**區域**是用來平衡資料處理負載。 HBase 會先將資料表的資料列儲存在單一區域中。 當資料表中的資料量增加時，資料列會散佈到多個區域。 **區域伺服器**可以處理多個區域的要求。

## <a name="write-ahead-log-for-apache-hbase"></a>針對 Apache HBase 先行撰寫記錄檔

HBase 會先將資料更新寫入一種認可記錄，稱為「寫入前記錄」（WAL）。 在 WAL 中儲存更新之後，它就會寫入至記憶體內部**MemStore**。 當記憶體中的資料達到其最大容量時，它會以**HFile**的形式寫入磁片。

如果**RegionServer**損毀，或在清除 MemStore 之前變成無法使用，則可以使用「直接寫入記錄」來重新執行更新。 沒有 WAL，如果**RegionServer**在排清**HFile**更新之前損毀，所有更新都會遺失。

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Apache HBase Azure HDInsight 中的加速寫入功能

加速寫入功能可解決使用雲端儲存體中預先寫入記錄檔所造成的較高寫入延遲問題。  HDInsight Apache HBase 叢集的加速寫入功能會將高階 SSD 受控磁片附加到每個 RegionServer （背景工作節點）。 然後，預先寫入的記錄會寫入裝載于這些高階受控磁片（而不是雲端儲存體）上的 Hadoop 檔案系統（HDFS）。  Premium 受控磁片會使用固態硬碟（Ssd），並提供容錯的絕佳 i/o 效能。  不同于非受控磁片，如果某個儲存裝置停止運作，它不會影響相同可用性設定組中的其他儲存單位。  因此，受控磁片可為您的應用程式提供低寫入延遲和更佳的復原能力。 若要深入瞭解 Azure 受控磁片，請參閱[azure 受控磁片簡介](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>如何在 HDInsight 中啟用 HBase 的加速寫入

若要使用加速寫入功能建立新的 HBase 叢集，請遵循在[HDInsight 中設定](../hdinsight-hadoop-provision-linux-clusters.md)叢集中的步驟，直到您到達**步驟3，儲存體**為止。 在 [**中繼存放區設定**] 下，選取 [**啟用 HBase 加速寫入**] 旁的核取方塊。 然後，繼續進行叢集建立的其餘步驟。

![啟用 HDInsight Apache HBase 的加速寫入選項](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>其他考量

若要保留資料持久性，請建立至少有三個背景工作節點的叢集。 建立之後，您就無法將叢集相應減少到三個以上的背景工作節點。

請先清除或停用 HBase 資料表，再刪除叢集，這樣您就不會遺失寫入的記錄資料。

```
flush 'mytable'
```

```
disable 'mytable'
```

相應減少叢集時，請遵循類似的步驟：清除資料表並停用資料表以停止傳入的資料。 您無法將叢集擴充至少於三個節點。

遵循這些步驟可確保成功相應減少，並避免因複寫中或暫存檔案而導致 namenode 進入安全模式的可能性。

如果您的 namenode 在相應減少之後進入安全模式，請使用 hdfs 命令來重新複寫不受複寫的區塊，並從安全模式取得 hdfs。 此重新複寫可讓您順利重新開機 HBase。

## <a name="next-steps"></a>後續步驟

* 「[先行寫入記錄」功能](https://hbase.apache.org/book.html#wal)的官方 Apache HBase 檔
* 若要升級 HDInsight Apache HBase 叢集以使用加速寫入，請參閱將[Apache hbase 叢集遷移至新版本](apache-hbase-migrate-new-version.md)。
