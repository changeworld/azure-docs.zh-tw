---
title: 使用 Power Query 將 Excel 連線到 Apache Hadoop - Azure HDInsight
description: 了解如何利用商業智慧元件和使用 Power Query for Excel 來存取 HDInsight 上的 Hadoop 中儲存的資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 8664efd40bb5392f56803515f09cccc800fdf21c
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397108"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>使用 Power Query 將 Excel 連線到 Apache Hadoop

Microsoft 巨量資料方案的主要功能之一，是將 Microsoft 商業智慧 (BI) 元件與 Azure HDInsight 上的 Apache Hadoop 叢集相整合。 舉例來說，可讓您使用 Microsoft Power Query for Excel 增益集，將 Excel 連接到包含 Hadoop 叢集相關聯資料的 Azure 儲存體帳戶。 本文將逐步解說如何設定及使用 Power Query，以查詢受 HDInsight 管理的 Hadoop 叢集相關聯資料。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。
* 執行 Windows 10、7、Windows Server 2008 R2 或更新版本作業系統的工作站。
* 適用于 enterprise、Office 2016、Office 2013 Professional Plus、Excel 2013 獨立或 Office 2010 Professional Plus 的 Microsoft 365 應用程式。

## <a name="install-microsoft-power-query"></a>安裝 Microsoft Power Query

Power Query 可匯入在 HDInsight 叢集上執行的 Hadoop 工作所匯出或產生的資料。

在 Excel 2016 中，Power Query 已整合至 [Get & Transform] \(取得並轉換\) 區段下方的 [Data] \(資料\) 功能區。 如果是舊的 Excel 版本，請從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?LinkID=286689)下載並安裝 Microsoft Power Query for Excel。

## <a name="import-hdinsight-data-into-excel"></a>將 HDInsight 資料匯入 Excel 中

Power Query add-in for Excel 可協助您將 HDInsight 叢集中的資料匯入至 Excel，以便使用 PowerPivot 和 Power Map 等 BI 工具來檢查、分析及呈現資料。

1. 啟動 Excel。

1. 建立新的空白活頁簿。

1. 依據 Excel 版本執行下列步驟：

   * Excel 2016

     * **Data**  >  **Get Data**  >  **From Azure**  >  **從 Azure HDInsight (HDFS) **選取 > 資料從 Azure 取得資料。

       ![Hdi。PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * **Power Query**  >  **From Azure**  >  **從 Microsoft Azure HDInsight**選取 Azure 的 Power Query。

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **注意：** 如果您沒有看到 [ **Power Query** ] 功能表，請**移至**  >  [檔案**選項**  >  **增益集**]，然後從頁面底部的下拉式清單 [**管理**] 方塊中選取 [ **COM 增益集**]。 選取 [移至...]**** 按鈕，並驗證 Power Query for Excel 增益集的方塊已勾選。

       **注意：** Power Query 也可讓您 **從其他來源中**選取，以從 HDFS 匯入資料。

1. 從 **Azure HDInsight (HDFS) ** ] 對話方塊的 [ **帳戶名稱或 URL** ] 文字方塊中，輸入與您的叢集相關聯的 Azure Blob 儲存體帳戶的名稱。 然後選取 [確定]。 此帳戶可以是預設儲存體帳戶或連結儲存體帳戶。  格式為 `https://StorageAccountName.blob.core.windows.net/`。

1. 針對 [ **帳戶金鑰**]，輸入 Blob 儲存體帳戶的金鑰，然後選取 **[連線]**。 (只有在您第一次存取這個存放區時才必須輸入帳戶資訊)。

1. 在 [查詢編輯器] 左側的導覽 **器** 窗格中，按兩下與您叢集相關聯的 Blob 儲存體容器名稱。 依預設，容器名稱與叢集名稱相同。

1. 在 [**名稱**] 欄中找出**HiveSampleData.txt** ， (資料夾路徑為 **。/hive/warehouse/hivesampletable/**) ，然後選取 HiveSampleData.txt 左邊的 [**二進位**檔]。 HiveSampleData.txt 隨附於所有叢集。 您也可以選擇使用您自己的檔案。

    ![HDI Excel power query 匯入資料](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. 如有需要，您可以將欄名稱重新命名。 當您準備好時，請選取 [ **關閉 & 載入**]。  資料已載入至您的活頁簿：

    ![HDI Excel power query 匯入資料表](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解到如何使用 Power Query 將 HDInsight 中的資料擷取至 Excel。 同樣地，您也可以將 HDInsight 中的資料擷取至 Azure SQL Database。 也可以將資料上傳至 HDInsight。 如需詳細資訊，請參閱下列文章：

* [使用 Azure HDInsight 中的 Microsoft Power BI 來視覺化 Apache Hive 資料](apache-hadoop-connect-hive-power-bi.md)。
* [使用 Azure HDInsight 中的 Power BI 將 Interactive Query Hive 資料視覺化](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用 Azure HDInsight 工具進行 Visual Studio Code](../hdinsight-for-vscode.md)。
* [將資料上傳至 HDInsight](./../hdinsight-upload-data.md)。
