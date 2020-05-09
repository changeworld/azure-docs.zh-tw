---
title: 在 Azure HDInsight 中使用 Apache Ambari 優化 Apache Pig
description: 使用 Apache Ambari web UI 來設定和優化 Apache Pig。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 469019eb1e90654d1953156337593d5de99b46c0
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796677"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 Apache Ambari 優化 Apache Pig

Apache Ambari 是用來管理和監視 HDInsight 叢集的 web 介面。 如需 Ambari Web UI 的簡介，請參閱[使用 Apache Ambari WEB Ui 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)叢集。

可以從 Ambari Web UI 微調 Pig 查詢，以便修改 Apache Pig 屬性。 直接從 Ambari 修改 Pig 屬性會修改 `/etc/pig/2.4.2.0-258.0/pig.properties` 檔案中的 Pig 屬性。

1. 若要修改 Pig 屬性，請瀏覽至 Pig [設定]**** 索引標籤，然後展開 [進階 pig 屬性]**** 窗格。

1. 尋找、取消註解並變更您想要修改的屬性值。

1. 選取視窗右上方的 [**儲存**]，以儲存新的值。 某些屬性可能需要服務重新啟動。

    ![Advanced Apache pig 屬性](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> 任何工作階段層級設定都會覆寫 `pig.properties` 檔案中的屬性值。

## <a name="tune-execution-engine"></a>調整執行引擎

兩個執行引擎可用來執行 Pig 指令碼：MapReduce 和 Tez。 Tez 是最佳化引擎，速度比 MapReduce 更快。

1. 若要修改執行引擎，請在 [進階 pig 屬性]**** 窗格中找出屬性 `exectype`。

1. 預設值為 **MapReduce**。 將它變更為 [Tez]****。

## <a name="enable-local-mode"></a>啟用本機模式

和 Hive 類似，本機模式可用來加速資料量相對較小的作業。

1. 若要啟用本機模式，請將 `pig.auto.local.enabled` 設定為 **true**。 預設值為 false。

1. 輸入資料大小比 `pig.auto.local.input.maxbytes` 屬性值小的作業會被視為小型作業。 預設值為 1 GB。

## <a name="copy-user-jar-cache"></a>複製使用者 jar 快取

Pig 會將 UDF 需要的 JAR 檔案複製到分散式快取，以供工作節點使用。 這些 jar 不會經常變更。 如果啟用，`pig.user.cache.enabled` 設定可將 jar 放置在快取中，以供同一位使用者執行的工作重複使用。 這項設定會導致作業效能的輕微增加。

1. 若要啟用，請將 `pig.user.cache.enabled` 設定為 true。 預設值為 false。

1. 若要設定快取 jar 的基底路徑，請將 `pig.user.cache.location` 設定為基底路徑。 預設值為 `/tmp`。

## <a name="optimize-performance-with-memory-settings"></a>使用記憶體設定將效能最佳化

下列記憶體設定有助於將 Pig 指令碼效能最佳化。

* `pig.cachedbag.memusage`：指定給包的記憶體數量。 包是元組的集合。 元組是欄位的排序集合，欄位則是一段資料。 如果包中的資料超過指定的記憶體，則會溢出至磁片。 預設值為 0.2，這表示可用記憶體的 20%。 這個記憶體由應用程式中的所有包共用。

* `pig.spill.size.threshold`：大於此溢出大小臨界值 (單位為位元組) 的包會溢出到磁碟。 預設值是 5 MB。

## <a name="compress-temporary-files"></a>壓縮暫存檔

Pig 會在作業執行期間產生暫存檔。 壓縮暫存檔會提升在磁碟中讀取或寫入檔案的效能。 下列設定可用來壓縮暫存檔。

* `pig.tmpfilecompression`：若為 true，啟用暫存檔壓縮。 預設值為 false。

* `pig.tmpfilecompression.codec`：壓縮轉碼器用來壓縮暫存檔。 建議的壓縮編解碼器是 LZO 和 Snappy，用於較低的 CPU 使用。

## <a name="enable-split-combining"></a>啟用分割合併

啟用時，小型檔案會合併，以減少對應工作。 這項設定可改善具有許多小型檔案之作業的效率。 若要啟用，請將 `pig.noSplitCombination` 設定為 true。 預設值為 false。

## <a name="tune-mappers"></a>微調對應程式

修改屬性 `pig.maxCombinedSplitSize` 可控制對應程式的數目。 這個屬性會指定要由單一對應工作處理的資料大小。 預設值是檔案系統的預設區塊大小。 增加此值會產生較少的對應程式工作。

## <a name="tune-reducers"></a>微調歸納器

歸納器的數目是根據 `pig.exec.reducers.bytes.per.reducer` 參數計算而得。 該參數指定每個歸納器處理的位元組數目，預設為 1 GB。 若要限制歸納器數目上限，請設定`pig.exec.reducers.max`屬性，預設為999。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [優化叢集](./hdinsight-changing-configs-via-ambari.md)
* [將 Apache HBase 優化](./optimize-hbase-ambari.md)
* [優化 Apache Hive](./optimize-hive-ambari.md)
