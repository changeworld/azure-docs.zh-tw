---
title: 在 Azure HDInsight 中使用 Apache Ambari 將 Apache HBase 優化
description: 使用 Apache Ambari web UI 來設定和優化 Apache HBase。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a7da6bc23d797e0e89b2338f446fc850b0fd0577
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82797158"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 Apache Ambari 將 Apache HBase 優化

Apache Ambari 是用來管理和監視 HDInsight 叢集的 web 介面。 如需 Ambari Web UI 的簡介，請參閱[使用 Apache Ambari WEB Ui 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)叢集。

從 [ **HBase**設定] 索引標籤修改 Apache HBase 設定。下列各節將說明一些會影響 HBase 效能的重要設定。

## <a name="set-hbase_heapsize"></a>設定 HBASE_HEAPSIZE

HBase 堆積大小會指定*區域*和*主要*伺服器將使用的堆積最大數量 (單位為 MB)。 預設值是 1,000 MB。 此值應針對叢集工作負載進行調整。

1. 若要修改，請瀏覽至 HBase [設定]**** 索引標籤的 [進階 HBase-env]**** 窗格，然後尋找 `HBASE_HEAPSIZE` 設定。

1. 將預設值變更為 5,000 MB。

    ![' Apache Ambari HBase memory heapsize '](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>將大量讀取工作負載最佳化

下列設定對於改善大量讀取工作負載的效能很重要。

### <a name="block-cache-size"></a>區塊快取大小

區塊快取是讀取快取。 其大小由 `hfile.block.cache.size` 參數控制。 預設值是 0.4，這是區域伺服器記憶體總計的 40%。 區塊快取大小愈大，隨機讀取愈快速。

1. 若要修改此參數，請瀏覽至 HBase [設定]**** 索引標籤的 [設定]**** 索引標籤，然後尋找 [配置於讀取緩衝區的 RegionServer 百分比]****。

    ![Apache HBase 記憶體區塊快取大小](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. 若要變更該值，請選取 [編輯]**** 圖示。

### <a name="memstore-size"></a>Memstore 大小

所有的編輯均儲存於稱為 *Memstore* 的記憶體緩衝區。 這個緩衝區會增加單一作業中可寫入磁片的資料總量。 它也會加速對最近編輯的存取。 Memstore 大小是由下列兩個參數所定義：

* `hbase.regionserver.global.memstore.UpperLimit`：定義合併的 Memstore 可使用的區域伺服器百分比上限。

* `hbase.regionserver.global.memstore.LowerLimit`：定義合併的 Memstore 可使用的區域伺服器百分比下限。

若要將隨機讀取最佳化，可以減少 Memstore 上限和下限。

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>從磁碟掃描時擷取的資料列數目

`hbase.client.scanner.caching` 設定會定義在掃描器上呼叫 `next` 方法時，從磁碟讀取的資料列數目。  預設值是 100。 該數字愈高，從用戶端向區域伺服器進行的遠端呼叫愈少，因此可加快掃描。 不過，此設定也會增加用戶端上的記憶體壓力。

![已提取的 Apache HBase 資料列數目](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> 請勿將該值設定過高，以免造成掃描器引動 next 方法的相隔時間大於掃描程式逾時。 掃描器逾時持續期間是由 `hbase.regionserver.lease.period` 屬性所定義。

## <a name="optimize-write-heavy-workloads"></a>將大量寫入工作負載最佳化

下列設定對於改善大量寫入工作負載的效能很重要。

### <a name="maximum-region-file-size"></a>區域檔案大小上限

HBase 會將資料儲存為稱為 *HFile* 的內部檔案格式。 屬性 `hbase.hregion.max.filesize` 會定義區域的單一 HFile 大小。  如果一個區域中的所有 HFiles 總和大於此設定，則一個區域會分割為兩個區域。

![' Apache HBase HRegion max filesize '](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

區域檔案大小愈大，分割數目愈小。 您可以增加檔案大小，以判斷達到最大寫入效能的值。

### <a name="avoid-update-blocking"></a>避免更新封鎖

* 屬性 `hbase.hregion.memstore.flush.size` 會定義 Memstore 排清至磁碟的大小。 預設大小為 128 MB。

* HBase 區域區塊乘數是由`hbase.hregion.memstore.block.multiplier`所定義。 預設值為 4。 允許的上限為 8。

* 如果 Memstore 是 (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) 位元組，HBase 區塊會更新。

    使用排清大小及區塊乘數的預設值時，在 Memstore 大小是 128 * 4 = 512 MB 的情況，將封鎖更新。 若要減少更新封鎖計數，請增加值 `hbase.hregion.memstore.block.multiplier`。

![Apache HBase 區域區塊乘數](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>定義 Memstore 大小

Memstore 大小是由 `hbase.regionserver.global.memstore.UpperLimit` 和 `hbase.regionserver.global.memstore.LowerLimit` 參數所定義。 設定這些彼此相等的值會減少寫入期間的暫停 (也會導致更頻繁清除)，並且可提升寫入效能。

## <a name="set-memstore-local-allocation-buffer"></a>設定 Memstore 本機配置緩衝區

Memstore 本機配置緩衝區的使用方式取決於屬性 `hbase.hregion.memstore.mslab.enabled`。 啟用（true）時，此設定可防止大量寫入作業期間的堆積片段。 預設值為 true。

![hbase.hregion.memstore.mslab.enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>後續步驟

* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [優化叢集](./hdinsight-changing-configs-via-ambari.md)
* [優化 Apache Hive](./optimize-hive-ambari.md)
* [將 Apache Pig 優化](./optimize-pig-ambari.md)
