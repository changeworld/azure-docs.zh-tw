---
title: 什麼是 Azure HDInsight 中的 Apache HBase？
description: HDInsight 中的 Apache HBase (建置在 Hadoop 的 NoSQL 資料庫) 簡介。 了解使用案例，並將 HBase 與其他 Hadoop 叢集進行比較。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: e977d4d68a330f57c4b53da4270e4515d4e69ee0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729816"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>什麼是 Azure HDInsight 中的 Apache HBase

[Apache HBase](https://hbase.apache.org/) 是開放原始碼的 NoSQL 資料庫，以 Apache Hadoop 作為建置基礎，並仿照 [Google BigTable](https://cloud.google.com/bigtable/) 建立模型。 HBase 可針對無結構描述資料庫中的大量資料，提供隨機存取功能和強大一致性。 資料庫會依資料行系列來組織。

從使用者觀點來看，HBase 類似於資料庫。 資料會儲存在資料表的資料列和資料行中，而資料列中的資料會依據資料行系列進行分組。 HBase 是無結構描述資料庫。 在使用資料行和資料類型之前，您可以先將其取消定義。 開放原始碼的程式碼會以線性方式延展，以處理數千個節點上的 PB 資料。 其可依賴 Hadoop 環境中分散式應用程式所提供的資料備援、批次處理及其他功能。

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache HBase 是如何實作的？

HDInsight HBase 會以受控叢集的形式提供，並整合到 Azure 環境中。 叢集依設定會將資料直接儲存至 [Azure 儲存體](./../hdinsight-hadoop-use-blob-storage.md)中，使其在效能與成本的選擇中提供低延遲性與高度彈性。 此屬性可讓客戶建立可使用大型資料集的互動式網站。 建立服務以儲存來自數百萬端點的感應器和遙測資料。 並使用 Hadoop 作業來分析此資料。 HBase 和 Hadoop 是 Azure 中巨量資料專案的良好起點。 這些服務可以讓即時應用程式與大型資料集搭配使用。

HDInsight 實作會使用 HBase 的向外延展架構來提供資料表的自動分區。 以及讀取和寫入的強大一致性及自動容錯移轉。 透過在記憶體內部快取讀取和高輸送量的串流寫入，來提高效能。 可以在虛擬網路內建立 HBase 叢集。 如需詳細資訊，請參閱[在 Azure 虛擬網路上建立 HDInsight 叢集](./apache-hbase-provision-vnet.md)。

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>如何在 HDInsight HBase 中管理資料？

要管理 HBase 中的資料，可使用 HBase Shell 的 `create`,`get`, `put` 和 `scan` 命令。 將資料寫入資料庫，需使用 `put`，讀取則使用 `get` `scan` 命令可用來取得資料表中多個資料列裡的資料。 您也可以使用 HBase C# API 管理資料，其在 HBase REST API 之上提供用戶端程式庫。 HBase 資料庫也可使用 [Apache Hive](https://hive.apache.org/) 進行查詢。 如需這些程式設計模型的簡介，請參閱[開始在 HDInsight 中搭配使用 Apache HBase 與 Apache Hadoop](./apache-hbase-tutorial-get-started-linux.md)。 同時也提供共同處理器，其允許在主控資料庫的節點中進行資料處理。

> [!NOTE]  
> Thrift 不受 HDInsight 中的 HBase 所支援。

## <a name="use-cases-for-apache-hbase"></a>Apache HBase 的使用案例

從 Web 搜尋建立 BigTable (以及由此延伸出的 HBase) 的正式使用案例。 搜尋引擎會建置索引，以將字詞對應到包含這些字詞的網頁。 除此之外，HBase 還有其他許多適用的使用案例，本節會列舉其中幾個。

|狀況 |描述 |
|---|---|
|索引鍵-值存放區|HBase 可作為索引鍵-值存放區，也很適合用來管理訊息系統。 Facebook 在訊息系統中使用 HBase，其用來儲存和管理網際網路通訊相當理想。 WebTable 使用 HBase 來搜尋和管理從網頁擷取的資料表。|
|感應器資料|HBase 適合用來擷取從多個來源收集累加的資料。 此資料包含社交分析和時間序列。 此外，可讓互動式儀表板保有最新的趨勢與計數器，以及管理稽核記錄系統。 範例包括 Bloomberg 公司的股市資訊終端機和 Open Time Series Database (OpenTSDB)。 OpenTSDB 可以儲存收集到的伺服器系統健康情況計量，並提供其存取權。|
|即時查詢|[Apache Phoenix](https://phoenix.apache.org/) 是適用於 Apache HBase 的 SQL 查詢引擎。 其以 JDBC 驅動程式的形式進行存取，並可使用 SQL 查詢和管理 HBase 資料表。|
|HBase 即平台|應用程式可以將 HBase 做為資料存放區，並在此基礎上執行。 範例包括 Phoenix、OpenTSDB、`Kiji` 及 Titan。 應用程式也可與 HBase 整合。 範例包括：[Apache Hive](https://hive.apache.org/)、Apache Pig、[Solr](https://lucene.apache.org/solr/)、Apache Storm、Apache Flume、[Apache Impala](https://impala.apache.org/)、Apache Spark、`Ganglia` 和 Apache Drill。|

## <a name="next-steps"></a>後續步驟

* [開始在 HDInsight 中搭配 Apache Hadoop 使用 Apache HBase](./apache-hbase-tutorial-get-started-linux.md)
* [在 Azure 虛擬網路上建立 HDInsight 叢集](./apache-hbase-provision-vnet.md)
* [在 HDInsight 中設定 Apache HBase 複寫](apache-hbase-replication.md)
