---
title: Azure HDInsight 中的互動式查詢是什麼？
description: Azure HDInsight 中的互動式查詢 (也稱為 Apache Hive LLAP) 簡介
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 09/17/2019
ms.openlocfilehash: f03797a8c7df1609a32f934bc090c7adc899aa9a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198917"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Azure HDInsight 中的互動式查詢是什麼

互動式查詢 (又稱為 Apache Hive LLAP 或[低延遲分析處理](https://cwiki.apache.org/confluence/display/Hive/LLAP) \(英文\)) 是一種 Azure HDInsight [叢集類型](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)。 互動式查詢支援記憶體內快取，可讓 Apache Hive 查詢速度更快且更具互動性。 客戶使用互動式查詢，以非常快速的方式查詢儲存在 Azure 儲存體和 Azure Data Lake Storage 中的資料。 互動式查詢讓開發人員和資料科學家更方便地使用他們最愛的 BI 工具，來運用巨量資料。 HDInsight 互動式查詢支援數種工具，以簡單的方式來存取巨量資料。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

互動式查詢叢集與 Apache Hadoop 叢集不同。 它只包含 Hive 服務。

您只能透過 Apache Ambari Hive 檢視、Beeline 和 Microsoft Hive 開放式資料庫連線驅動程式 (Hive ODBC)，存取互動式查詢叢集中的 Hive 服務。 您無法透過 Hive 主控台、Templeton、Azure 傳統 CLI 或 Azure PowerShell 來存取它。

## <a name="create-an-interactive-query-cluster"></a>建立互動式查詢叢集

如需建立 HDInsight 叢集的相關資訊，請參閱[在 HDInsight 中建立 Apache Hadoop 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。 選擇互動式查詢叢集類型。

> [!IMPORTANT]
> 互動式查詢叢集的最小前端節點大小為 Standard_D13_v2。 如需詳細資訊，請參閱 [Azure VM 調整大小圖表](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)。

## <a name="execute-apache-hive-queries-from-interactive-query"></a>從互動式查詢執行 Apache Hive 查詢

若要執行 Hive 查詢，您有下列選項：

* 使用 Microsoft Power BI

    請參閱[在 Azure HDInsight 中使用 Power BI 將互動式查詢 Apache Hive 資料視覺化](./apache-hadoop-connect-hive-power-bi-directquery.md) 請參閱[在 Azure HDInsight 中使用 Power BI 將巨量資料視覺化](../hadoop/apache-hadoop-connect-hive-power-bi.md)。

* 使用 Visual Studio

    請參閱[使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)。

* 使用 Visual Studio Code

    請參閱[使用適用於 Apache Hive、LLAP 或 pySpark 的 Visual Studio Code](../hdinsight-for-vscode.md)。
* 使用 Apache Ambari Hive 檢視執行 Apache Hive。
  
    請參閱[在 HDInsight 中搭配 Apache Hadoop 使用 Apache Hive 檢視](../hadoop/apache-hadoop-use-hive-ambari-view.md)。

* 使用 Beeline 執行 Apache Hive。
  
    請參閱[利用 Beeline 搭配使用 Apache Hive 與 HDInsight 中的 Apache Hadoop](../hadoop/apache-hadoop-use-hive-beeline.md)。
  
    您可以從前端節點或空白邊緣節點使用 Beeline。 我們的建議是從空白邊緣節點使用 Beeline。 如需使用空白邊緣節點建立 HDInsight 叢集的詳細資訊，請參閱[在 HDInsight 中使用空白邊緣節點](../hdinsight-apps-use-edge-node.md)。
* 使用 Hive ODBC 執行 Apache Hive。
  
    請參閱[使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 Apache Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。

若要尋找 Java 資料庫連線 (JDBC) 連接字串：

1. 藉由使用下列 URL 來登入 Apache Ambari：`https://<cluster name>.AzureHDInsight.net`。
2. 在左側功能表中，選取 **Hive**。
3. 若要複製 URL，選取剪貼簿圖示：

   ![HDInsight Hadoop 互動式查詢 LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>後續步驟

* 了解如何[在 HDInsight 中建立互動式查詢叢集](../hdinsight-hadoop-provision-linux-clusters.md)。
* 了解如何[在 Azure HDInsight 中使用 Power BI 將巨量資料視覺化](../hadoop/apache-hadoop-connect-hive-power-bi.md)。
* 了解如何[使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
