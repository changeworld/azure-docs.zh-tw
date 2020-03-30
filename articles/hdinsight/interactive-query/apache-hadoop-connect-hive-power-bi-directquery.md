---
title: 在 Azure HDInsight 中使用 Power BI 查看互動式查詢 Hive 資料
description: 使用 Microsoft Power BI 將 Azure HDInsight 的互動式查詢 Hive 資料視覺化
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367987"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>使用 HDInsight 中的直接查詢使用 Microsoft Power BI 視覺化互動式查詢 Apache Hive 資料

本文說明如何將 Microsoft Power BI 連線至 Azure HDInsight 互動式查詢叢集，然後使用直接查詢將 Apache Hive 資料視覺化。 提供的示例將資料從`hivesampletable`Hive 表載入到 Power BI。 `hivesampletable` Hive 表包含一些行動電話使用方式資料。 然後您可在世界地圖上繪製使用量資料：

![HDInsight Power BI 地圖報告](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

您可以利用 [Apache Hive ODBC 驅動程式](../hadoop/apache-hadoop-connect-hive-power-bi.md)，透過 Power BI Desktop 中的一般 ODBC 連接器執行匯入作業。 不過，對於 BI 工作負載則不建議使用此方式，因為 Hive 查詢引擎具有非互動式的本質。 為發揮其效能，[HDInsight 互動式查詢連接器](./apache-hadoop-connect-hive-power-bi-directquery.md)和 [HDInsight Apache Spark 連接器](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)會是較好的選擇。

## <a name="prerequisites"></a>Prerequisites
在閱讀本文之前，您必須有下列各項：

* **HDInsight 集群**. 此叢集可以是含有 Apache Hive 的 HDInsight 叢集或新發行的互動式查詢叢集。 如需建立叢集，請參閱[建立叢集](../hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=45331)下載一份。

## <a name="load-data-from-hdinsight"></a>從 HDInsight 載入資料

`hivesampletable` Hive 表附帶所有 HDInsight 群集。

1. 啟動 Power BI Desktop。

2. 從功能表列，導航到 **"** > **獲取更多資料** > **..."。**

    ![HDInsight 電源 BI 獲取更多資料](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. 在 **"獲取資料"** 視窗中，在搜索框中輸入**hdinsight。**  

4. 從搜尋結果中選擇**HDInsight 互動式查詢**，然後選擇 **"連接**"。  如果您沒有看到**HDInsight 互動式查詢**，則需要將 Power BI 桌面更新為最新版本。

5. 選擇 **"繼續**"以關閉 **"連接到協力廠商服務**"對話方塊。

6. 在**HDInsight 互動式查詢**視窗中，輸入以下資訊，然後選擇 **"確定**" ：

    |屬性 | 值 |
    |---|---|
    |伺服器 |輸入群集名稱，例如*myiqcluster.azurehdinsight.net*。|
    |資料庫 |輸入本文的**預設值**。|
    |資料連線模式 |為本文選擇 **"直接查詢**"。|

    ![HDInsight 互動式查詢 Power BI DirectQuery 連線](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. 輸入 HTTP 憑據，然後選擇"**連接**"。 預設的使用者名稱為 **admin**。

8. 從左側窗格中的 **"導航器"** 視窗中，選擇**示例。**

9. 從主視窗中選擇 **"載入**"。

    ![HDInsight 互動式查詢 Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>將地圖上的資料視覺化

從上一個程序繼續進行。

1. 從"視覺化"窗格中，選擇 **"地圖**"，選擇"地球"圖示。 然後，一個通用映射將顯示在主視窗中。

    ![HDInsight Power BI 自訂報告](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. 從 [欄位] 窗格選取 **country** 和 **devicemake**。 幾分鐘後，主視窗中將顯示一個帶有資料點的世界地圖。

3. 展開地圖。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 Microsoft Power BI 將 HDInsight 中的資料視覺化。  如需資料視覺效果的詳細資訊，請參閱下列文章：

* [在 Azure HDInsight 中使用 ODBC 使用 Microsoft Power BI 視覺化 Apache Hive 資料](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 
* [使用 Apache Zepelin 在 Azure HDInsight 中運行 Apache Hive 查詢](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [使用 Azure HDInsight 工具進行視覺化工作室代碼](../hdinsight-for-vscode.md)。
* [將資料上傳至 HDInsight](./../hdinsight-upload-data.md)。
