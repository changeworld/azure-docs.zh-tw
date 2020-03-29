---
title: 適用於 Apache HBase 的 Azure HDInsight 加速寫入
description: 概述了 Azure HDInsight 加速寫入功能，該功能使用高級託管磁片來提高 Apache HBase 預寫記錄檔的性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764603"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>適用於 Apache HBase 的 Azure HDInsight 加速寫入

本文提供了 Azure HDInsight 中 Apache HBase**的加速寫入**功能的背景，以及如何有效地使用它來提高寫入性能。 **加速寫入**使用[Azure 高級 SSD 託管磁片](../../virtual-machines/linux/disks-types.md#premium-ssd)來提高 Apache HBase 預寫記錄檔 （WAL） 的性能。 要瞭解有關 Apache HBase 的更多內容，請參閱[HDInsight 中的 Apache HBase](apache-hbase-overview.md)。

## <a name="overview-of-hbase-architecture"></a>HBase 架構概述

在 HBase 中，**行**由一個或多個**列**組成，並由**行鍵**標識。 多行組成一個**表**。 列包含**儲存格**，儲存格是該列中值的時間戳記版本。 列被分組到**列族**中，列族中的所有列都存儲在名為**HFiles**的存儲檔中。

HBase 中的**區域**用於平衡資料處理負載。 HBase 首先將表的行存儲在單個區域中。 隨著表中資料量的增加，行將分佈在多個區域。 **區域伺服器**可以處理多個區域的請求。

## <a name="write-ahead-log-for-apache-hbase"></a>為 Apache HBase 預寫記錄檔

HBase 首先將資料更新寫入稱為預寫記錄檔 （WAL） 的提交日誌類型。 更新存儲在 WAL 中後，它將寫入記憶體中的**MemStore**。 當記憶體中的資料達到其最大容量時，它將作為**HFile**寫入磁片。

如果**區域伺服器**崩潰或在刷新 MemStore 之前不可用，則"預寫記錄檔"可用於重播更新。 如果沒有 WAL，如果**區域伺服器**在刷新**HFile**更新之前崩潰，則所有這些更新都將丟失。

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>用於 Apache HBase 的 Azure HDInsight 中的加速寫入功能

"加速寫入"功能解決了使用雲存儲中的預寫記錄檔而導致的寫入延遲較高的問題。  HDInsight Apache HBase 群集的加速寫入功能將高級 SSD 管理的磁片附加到每個區域伺服器（輔助節點）。 然後，將預寫記錄檔寫入安裝在這些高級託管磁片上的 Hadoop 檔案系統 （HDFS）， 而不是雲存儲。  高級託管磁片使用固態磁片 （SSD），具有出色的 I/O 性能，容錯能力。  與非託管磁片不同，如果一個存儲單元出現故障，則不會影響同一可用性集中的其他存儲單元。  因此，託管磁片為您的應用程式提供低寫入延遲和更好的恢復能力。 要瞭解有關 Azure 託管磁片的更多詳細資訊，請參閱[Azure 託管磁片簡介](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>如何在 HDInsight 中為 HBase 啟用加速寫入

要使用加速寫入功能創建新的 HBase 群集，請按照[HDInsight 中設置群集](../hdinsight-hadoop-provision-linux-clusters.md)中的步驟操作，直到達到**步驟 3 存儲**。 在**Metastore 設置**下，選擇啟用**HBase 加速寫入**旁邊的核取方塊。 然後，繼續創建群集的剩餘步驟。

![為 HDInsight Apache HBase 啟用加速寫入選項](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>其他考量

要保持資料的持久性，請創建一個至少具有三個輔助節點的群集。 創建後，無法將群集縮減為少於三個輔助節點。

在刪除群集之前刷新或禁用 HBase 表，這樣您就不會丟失預寫記錄檔資料。

```
flush 'mytable'
```

```
disable 'mytable'
```

縮減群集時，請遵循類似步驟：刷新表並禁用表以停止傳入資料。 不能將群集縮減到少於三個節點。

執行這些步驟將確保成功縮小規模，並避免 namenode 由於複製不足或暫存檔案而進入安全模式的可能性。

如果命名節點在縮小規模後進入安全模式，請使用 hdfs 命令重新複製未複製的塊，並將 hdfs 從安全模式中排除。 此重新複製將允許您成功重新開機 HBase。

## <a name="next-steps"></a>後續步驟

* 關於["預寫記錄檔"功能](https://hbase.apache.org/book.html#wal)的官方 Apache HBase 文檔
* 要升級 HDInsight Apache HBase 群集以使用加速寫入，請參閱[將 Apache HBase 群集遷移到新版本](apache-hbase-migrate-new-version.md)。
