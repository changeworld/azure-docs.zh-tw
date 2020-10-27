---
title: 使用 Power BI 將 Apache Hive 資料視覺化 - Azure HDInsight
description: 了解如何使用 Microsoft Power BI 將 Azure HDInsight 所處理的 Hive 資料視覺化。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: e66329b93ebd7d90258ab4670b77ca849a28189b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547991"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 ODBC 藉由 Microsoft Power BI 將 Apache Hive 資料視覺化

瞭解如何使用 ODBC 將 Microsoft Power BI Desktop 連接 Azure HDInsight，並將 Apache Hive 的資料視覺化。

> [!IMPORTANT]
> 您可以利用 Hive ODBC 驅動程式，透過 Power BI Desktop 中的一般 ODBC 連接器執行匯入作業。 不過，對於 BI 工作負載則不建議使用此方式，因為 Hive 查詢引擎具有非互動式的本質。 為發揮其效能，[HDInsight 互動式查詢連接器](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)和 [HDInsight Spark 連接器](/power-bi/spark-on-hdinsight-with-direct-connect)會是較好的選擇。

在本文中，您會將 Hive 資料表中的資料載入 `hivesampletable` 至 Power BI。 Hive 資料表包含某些行動電話使用量資料。 然後您可在世界地圖上繪製使用量資料：

![HDInsight Power BI 地圖報告](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

此資訊也適用於新的[互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集類型。 若要了解如何使用直接查詢來連線到 HDInsight 互動式查詢，請參閱[在 Azure HDInsight 中使用直接查詢藉由 Microsoft Power BI 將互動式查詢 Hive 資料視覺化](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。

## <a name="prerequisites"></a>必要條件

在閱讀本文之前，您必須有下列各項：

* HDInsight 叢集。 此叢集可以是含有 Hive 的 HDInsight 叢集或新發行的互動式查詢叢集。 如需建立叢集，請參閱[建立叢集](apache-hadoop-linux-tutorial-get-started.md)。

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=45331)下載一份。

## <a name="create-hive-odbc-data-source"></a>建立 Hive ODBC 資料來源

請參閱[建立 Hive ODBC 資料來源](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)。

## <a name="load-data-from-hdinsight"></a>從 HDInsight 載入資料

**Hivesampletable** Hive 資料表隨附于所有 HDInsight 叢集。

1. 啟動 Power BI Desktop。

1. 從頂端功能表中，流覽至 **Home**  >  **Get Data**  >  **更多 ...**

    ![HDInsight Excel Power BI 開啟資料](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. 從 [ **取得資料** ] 對話方塊中，選取左側的 [ **其他** ]，選取右邊的 [ **ODBC** ]，然後選取底部的 **[連接]** 。

1. 在 [ **從 ODBC** ] 對話方塊中，從下拉式清單中選取您在上一節中建立的資料來源名稱。 然後選取 [確定]  。

1. 第一次使用時，會開啟 [ **ODBC 驅動程式** ] 對話方塊。 從左側功能表中選取 [ **預設] 或 [自訂** ]。 然後選取 **[連線]** 以開啟 [導覽 **器** ]。

1. 從 [導覽 **器** ] 對話方塊中，展開 [ **ODBC > HIVE > 預設值** ，選取 [ **hivesampletable** ]，然後選取 [ **載入** ]。

## <a name="visualize-data"></a>顯現資料

從上一個程序繼續進行。

1. 從 [視覺效果] 窗格中，選取 [ **地圖** ]，它是地球圖示。

    ![HDInsight Power BI 自訂報告](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. 從 [ **欄位** ] 窗格中，選取 [ **country** ] 和 [ **devicemake** ]。 您可以看到地圖上繪製的資料。

1. 展開地圖。

## <a name="next-steps"></a>下一步

在本文中，您已了解如何使用 Power BI 將 HDInsight 中的資料視覺化。  如需詳細資訊，請參閱下列文章：

* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 HDInsight](./apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用直接查詢以 Microsoft Power BI 視覺化 Interactive Query Apache Hive 資料](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)