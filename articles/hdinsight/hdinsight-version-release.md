---
title: HDInsight 4.0 概觀 - Azure
description: 比較 HDInsight 3.6 與 HDInsight 4.0 功能、限制及升級建議。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 466c47492de9c2fc86f309f4cb74df1ff5411dcb
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929293"
---
# <a name="azure-hdinsight-40-overview"></a>Azure HDInsight 4.0 概觀

Azure HDInsight 是適用于 Apache Hadoop 和 Apache Spark 的企業客戶之間最受歡迎的服務之一。 HDInsight 4.0 是 Apache Hadoop 元件的雲端發佈。 本文提供有關最新 Azure HDInsight 版本及如何升級的資訊。

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4.0 有何新功能？

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 和低延遲的分析處理

Apache Hive 低延遲分析處理（LLAP）會使用持續性查詢伺服器和記憶體內部快取。 此程式會針對遠端雲端儲存體中的資料提供快速的 SQL 查詢結果。 Hive LLAP 會使用一組持續性的守護程式來執行 Hive 查詢的片段。 LLAP 上的查詢執行類似於沒有 LLAP 的 Hive，其中含有要在 LLAP 精靈 (而非容器) 內執行的背景工作。

Hive LLAP 的權益包括：

* 能夠執行深入的 SQL 分析，而不會犧牲效能和適應性。 例如複雜聯結、子查詢、視窗化函數、排序、使用者定義函數和複雜匯總。

* 對資料已就緒之相同儲存體中的資料進行互動式查詢，不需要將資料從儲存體移到另一個引擎，就能進行分析處理。

* 快取查詢結果可以重複使用先前計算的查詢結果。 此快取可節省執行查詢所需之叢集工作所花費的時間和資源。

### <a name="hive-dynamic-materialized-views"></a>Hive 動態具體化檢視

Hive 現在支援動態具體化視圖，或預先計算相關摘要。 Views 會加速資料倉儲中的查詢處理。 具體化檢視可以原生方式儲存於 Hive 中，而且可以順暢地使用 LLAP 加速。

### <a name="hive-transactional-tables"></a>Hive 交易式資料表

HDI 4.0 包含 Apache Hive 3。 Hive 3 針對存在於 Hive 倉儲中的交易式資料表，需要有不可部分完成性、一致性、隔離和耐久性。 符合 ACID 規範的資料表和資料表資料均會透過 Hive 進行存取與管理。 建立、取得、更新和刪除（CRUD）資料表中的資料必須是優化的資料列行（ORC）檔案格式。 僅限插入的資料表支援所有檔案格式。

* ACID v2 已對儲存體格式和執行引擎進行效能改進。

* 預設會啟用 ACID 以完整支援資料更新。

* 改進的 ACID 功能可讓您在資料列層級進行更新和刪除。

* 沒有效能額外負荷。

* 不需要任何貯體。

* Spark 可以透過 Hive 資料倉儲連接器讀取和寫入至 Hive ACID 資料表。

深入了解 [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html) \(英文\)。

### <a name="apache-spark"></a>Apache Spark

Apache Spark 利用 Hive 資料倉儲連接器來取得可更新的資料表與 ACID 交易。 Hive 資料倉儲連接器可讓您將 Hive 交易式資料表註冊為 Spark 中的外部資料表，以存取完整的交易式功能。 舊版只支援資料表分割區操作。 Hive 倉儲連接器也支援串流資料框架。  此程式會將讀取和寫入串流處理至 Spark 中的交易式和串流 Hive 資料表。

Spark 執行程式可以直接連線到 Hive LLAP 精靈，以交易方式擷取和更新資料，從而讓 Hive 能夠保有對資料的控制。

HDInsight 4.0 上的 Apache Spark 支援下列案例：

* 執行機器學習模型，此模型會透過用來報告的相同交易式資料表進行定型。
* 使用 ACID 交易，安全地將資料行從 Spark ML 新增到 Hive 資料表。
* 從 Hive 串流資料表，對變更摘要執行 Spark 串流作業。
* 直接從 Spark 結構化串流作業建立 ORC 檔案。

您不再需要擔心不小心嘗試直接從 Spark 存取 Hive 交易資料表。 導致不一致的結果、重複的資料或資料損毀。 在 HDInsight 4.0 中，Spark 資料表和 Hive 資料表均會保留於個別的中繼存放區。 使用 Hive 資料倉儲連接器，明確地將 Hive 交易式資料表註冊為 Spark 外部資料表。

深入了解 [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html) \(英文\)。

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 隨附於 HDI 4.0，並包含下列變更：

* Oozie 不再執行 Hive 動作。 Hive CLI 已遭移除並由 BeeLine 所取代。

* 您可以在 **job.properties** 檔案中包含排除模式，藉以從共用程式庫中排除不必要的相依性。

深入了解 [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html) \(英文\)。

## <a name="how-to-upgrade-to-hdinsight-40"></a>如何升級至 HDInsight 4.0

在生產環境中執行最新版本之前，請徹底測試您的元件。 HDInsight 4.0 可供您開始進行升級程式。 HDInsight 3.6 是防止意外事故的預設選項。

沒有從舊版 HDInsight 到 HDInsight 4.0 的支援升級路徑。 由於中繼存放區和 blob 資料格式已變更，4.0 與舊版不相容。 請務必讓新的 HDInsight 4.0 環境與您目前的生產環境分開。 如果您將 HDInsight 4.0 部署到目前的環境，您的中繼存放區將會永久升級。  

## <a name="limitations"></a>限制

* HDInsight 4.0 不支援 Apache Hive 的 MapReduce。 請改用 Apache Tez。 深入了解 [Apache Tez](https://tez.apache.org/) \(英文\)。
* HDInsight 4.0 不支援 Apache Storm。
* HDInsight 4.0 中已不再提供 Hive 檢視。
* Spark 和互動式查詢叢集不支援 Apache Zeppelin 中的 Shell 解譯器。
* 您不能「停用」** Spark-LLAP 叢集上的 LLAP。 您只能關閉 LLAP。
* Azure Data Lake Storage Gen2 無法在 Spark 叢集中儲存 Juypter Notebook。
* Apache pig 預設會在 Tez 上執行，不過您可以將它變更為 Mapreduce
* 資料列和資料行安全性的 Spark SQL Ranger 整合已被取代
* Spark 2.4 和 Kafka 2.1 可在 HDInsight 4.0 中取得，因此不再支援 Spark 2.3 和 Kafka 1.1。 我們建議您在 HDInsight 4.0 中使用 Spark 2.4 & Kafka 2.3 和更新版本。


## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 檔](index.yml)
* [版本資訊](hdinsight-release-notes.md)
