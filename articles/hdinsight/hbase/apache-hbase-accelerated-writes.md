---
title: 適用於 Apache HBase 的 Azure HDInsight 加速寫入
description: 概述 Azure HDInsight 加速寫入功能，其使用高階受控磁片來改善 Apache HBase 寫入記錄檔的效能。
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: 1f5a9f05b94f43db9e1d883ef5e86f8621f39251
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943023"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>適用於 Apache HBase 的 Azure HDInsight 加速寫入

本文提供 Azure HDInsight 中 Apache HBase 之 **加速寫入** 功能的背景，以及如何有效地使用它來改善寫入效能。 **加速寫入** 會使用 [Azure 高階 SSD 受控磁片](../../virtual-machines/disks-types.md#premium-ssd) 來改善 Apache HBase 提前寫入記錄檔 (WAL) 的效能。 若要深入瞭解 Apache HBase，請參閱 [什麼是 HDInsight 中的 Apache hbase](apache-hbase-overview.md)。

## <a name="overview-of-hbase-architecture"></a>HBase 架構總覽

在 HBase 中，資料列 **是由一** 或多個資料行所組成，而且是以資料 **列** 索引 **鍵** 來識別。 組成 **資料表** 的多個資料列。 資料行包含資料 **格**，這是該資料行中值的時間戳記版本。 資料行會分組為數據 **行系列**，資料行系列中的所有資料行都會儲存在稱為 **HFiles** 的儲存體檔案中。

HBase 中的 **區域** 是用來平衡資料處理負載。 HBase 會先將資料表的資料列儲存在單一區域中。 當資料表中的資料量增加時，資料列會分散到多個區域。 **區域伺服器** 可以處理多個區域的要求。

## <a name="write-ahead-log-for-apache-hbase"></a>預先撰寫 Apache HBase 的記錄檔

HBase 會先將資料更新寫入至一種認可記錄類型，稱為「立即寫入」記錄檔 (WAL) 。 將更新儲存在 WAL 之後，它會寫入記憶體內部 **MemStore**。 當記憶體中的資料達到其最大容量時，就會以 **HFile** 的形式寫入磁片。

如果 **RegionServer** 損毀或在清除 MemStore 之前無法使用，則可以使用預先寫入記錄來重新執行更新。 若沒有 WAL，則在清除 **HFile** 的更新之前，如果 **RegionServer** 損毀，則所有更新都會遺失。

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Apache HBase Azure HDInsight 中的加速寫入功能

加速寫入功能可透過使用雲端儲存體中的預先寫入記錄，來解決寫入延遲較高的問題。  HDInsight Apache HBase 叢集的加速寫入功能會將高階 SSD 受控磁片附加至每個 RegionServer (背景工作節點) 。 然後，系統會將預先寫入的記錄寫入 Hadoop 檔案系統 (HDFS) 掛接在這些高階受控磁片上，而不是雲端存放裝置上。  高階受控磁片使用 Solid-State 磁片 (Ssd) 並提供具有容錯能力的絕佳 i/o 效能。  不同于非受控磁片，如果某個儲存裝置停止運作，則不會影響相同可用性設定組中的其他儲存體單位。  因此，受控磁片可為您的應用程式提供低寫入延遲和更佳的復原能力。 若要深入瞭解 Azure 受控磁片，請參閱 [azure 受控磁片簡介](../../virtual-machines/managed-disks-overview.md)。

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>如何在 HDInsight 中為 HBase 啟用加速寫入

若要使用加速寫入功能建立新的 HBase 叢集，請遵循在 [HDInsight 中設定](../hdinsight-hadoop-provision-linux-clusters.md)叢集的步驟。 在 [ **基本** ] 索引標籤上，選取 [叢集類型為 HBase]，指定元件版本，然後按一下 [ **啟用 HBase 加速寫入**] 旁的核取方塊。 然後，繼續進行叢集建立的其餘步驟。

![啟用 HDInsight Apache HBase 的加速寫入選項](./media/apache-hbase-accelerated-writes/azure-portal-create-hbase-wals.png)

## <a name="other-considerations"></a>其他考量

若要保留資料耐久性，請建立至少有三個背景工作節點的叢集。 一旦建立之後，您就無法將叢集縮小至小於三個背景工作節點。

請先清除或停用 HBase 資料表，然後再刪除叢集，如此您就不會遺失預先寫入的記錄資料。

```
flush 'mytable'
```

```
disable 'mytable'
```

調整叢集時，請遵循類似的步驟：排清您的資料表，並停用資料表以停止內送資料。 您無法將叢集縮小至少於三個節點。

遵循這些步驟可確保能成功地向下延展，並避免 namenode 因為複寫或暫存檔而進入安全模式的可能性。

如果您的 namenode 在縮小後進入安全模式，請使用 hdfs 命令重新複寫複寫中的區塊，並從安全模式取得 hdfs。 此重新複寫可讓您成功重新開機 HBase。

## <a name="next-steps"></a>後續步驟

* [預先寫入記錄功能](https://hbase.apache.org/book.html#wal)的官方 Apache HBase 檔
* 若要將 HDInsight Apache HBase 叢集升級為使用加速寫入，請參閱將 [Apache hbase 叢集遷移至新的版本](apache-hbase-migrate-new-version.md)。
