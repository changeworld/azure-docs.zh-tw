---
title: Excel & 具有開放式資料庫連接（ODBC）驅動程式的 Apache Hadoop-Azure HDInsight
description: 了解如何設定和使用 Excel 的 Microsoft Hive ODBC 驅動程式，從 Microsoft Excel 查詢 HDInsight 叢集中的資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 388f59f5090be43469acfde5197a658942e817f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82182441"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 Azure HDInsight 中的 Apache Hadoop

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft 的 Big Data 解決方案將 Microsoft 商業智慧（BI）元件與部署在 HDInsight 中的 Apache Hadoop 叢集整合在一起。 例如，將 Excel 連接到 Hadoop 叢集的 Hive 資料倉儲的能力。 使用 Microsoft Hive 開放式資料庫連接（ODBC）驅動程式連接。

您可以使用適用于 Excel 的 Microsoft Power Query 增益集，從 Excel 連接與 HDInsight 叢集相關聯的資料。 如需詳細資訊，請參閱[使用 Power Query 將 Excel 連接到 HDInsight](../hdinsight-connect-excel-power-query.md)。

## <a name="prerequisites"></a>必要條件

開始閱讀本文之前，您必須有下列各項：

* HDInsight Hadoop 叢集。 若要建立，請參閱[開始使用 Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)。
* 具有 Office 2010 專業增強版或更新版本，或是 Excel 2010 或更新版本的工作站。

## <a name="install-microsoft-hive-odbc-driver"></a>安裝 Microsoft Hive ODBC 驅動程式

下載並安裝[Microsoft Hive ODBC 驅動程式](https://www.microsoft.com/download/details.aspx?id=40886)。 選擇與您要使用 ODBC 驅動程式的應用程式版本相符的版本。  在本文中，驅動程式是用於 Office Excel。

## <a name="create-apache-hive-odbc-data-source"></a>建立 Apache Hive ODBC 資料來源

下列步驟將說明如何建立 Hive ODBC 資料來源。

1. 在 Windows 中，流覽至 **[開始] > [Windows 系統管理工具] [> ODBC 資料來源（32位）/（64位）**]。  此動作會開啟 [ **ODBC 資料來源管理員**] 視窗。

    ![OBDC data source administrator](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "使用 ODBC 資料來源管理員設定 DSN")

1. 從 [使用者 DSN]**** 索引標籤，選取 [新增]**** 以開啟 [建立新資料來源]**** 視窗。

1. 選取 [Microsoft Hive ODBC 驅動程式]****，然後選取 [完成]**** 以開啟 [Microsoft Hive ODBC 驅動程式 DSN 設定]**** 視窗。

1. 輸入或選取下列值：

   | 屬性 | 說明 |
   | --- | --- |
   |  資料來源名稱 |為資料來源指定名稱 |
   |  主機 |輸入 `HDInsightClusterName.azurehdinsight.net`。 例如： `myHDICluster.azurehdinsight.net` 。 注意： `HDInsightClusterName-int.azurehdinsight.net` 只要用戶端 VM 對等互連到相同的虛擬網路，就會受到支援。 |
   |  Port |使用 **443** (此連接埠已從 563 變更為 443)。 |
   |  資料庫 |使用**預設值**。 |
   |  機制 |選取 [Windows Azure HDInsight 服務]**** |
   |  使用者名稱 |輸入 HDInsight 叢集 HTTP 使用者的使用者名稱。 預設的使用者名稱為 **admin**。 |
   |  密碼 |輸入 HDInsight 叢集使用者的密碼。 選取 [儲存密碼 (加密)]**** 核取方塊。|

1. 選擇性：選取 [ **Advanced Options** ]  

   | 參數 | 描述 |
   | --- | --- |
   |  使用原生查詢 |選取此選項時，ODBC 驅動程式不會嘗試將 TSQL 轉換為 HiveQL。 只有在100% 確定您正在提交純 HiveQL 語句時，才應使用它。 連接到 SQL Server 或 Azure SQL Database 時，您應將它保留為未勾選。 |
   |  每個區塊擷取的資料列 |在擷取大量記錄時，可能必須調整此參數，以確保最佳效能。 |
   |  預設字串資料行長度、二進位資料行長度、十進位資料行小數位數 |資料類型的長度和精確度可能會影響傳回資料的方式。 由於失去有效位數和或截斷，因此會導致傳回不正確的資訊。 |

    ![進階 DSN 設定選項](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "進階 DSN 設定選項")

1. 選取 [測試]**** 以測試資料來源。 當資料來源正確設定時，測試結果會顯示**成功！**

1. 選取 [確定]**** 以關閉 [測試] 視窗。  

1. 選取 [確定]**** 以關閉 [Microsoft Hive ODBC 驅動程式 DSN 設定]**** 視窗。  

1. 選取 [確定]**** 以關閉 [ODBC 資料來源管理員]**** 視窗。  

## <a name="import-data-into-excel-from-hdinsight"></a>從 HDInsight 將資料匯入 Excel 中

下列步驟將說明如何使用您在上一節中建立的 ODBC 資料來源，將資料從 Hive 資料表匯入 Excel 活頁簿中。

1. 在 Excel 中開啟新的或現有的活頁簿。

2. 從 [資料]**** 索引標籤，瀏覽至 [取得資料]**** > [從其他來源]**** > [從 ODBC]**** 以啟動 [從 ODBC]**** 視窗。

    ![開啟 Excel 資料連線嚮導](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "開啟 Excel 資料連線嚮導")

3. 從下拉式清單中，選取您在上一節中建立的資料來源名稱，然後選取 **[確定]**。

4. 第一次使用時，會開啟 [ **ODBC 驅動程式**] 對話方塊。 從左側功能表中選取 [ **Windows** ]。 然後選取 **[連線]** 以開啟 [導覽**器**] 視窗。

5. 從 [導覽器]**** 中，瀏覽至 [HIVE]**** > [預設值]**** > [hivesampletable]****，然後選取 [載入]****。 資料需要一些時間才會匯入至 Excel。

    ![HDInsight Excel Hive ODBC 導覽器](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel Hive ODBC 導覽器")

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用 Microsoft Hive ODBC 驅動程式將 HDInsight 服務中的資料擷取至 Excel。 同樣地，您也可以將 HDInsight 服務中的資料擷取至 SQL Database。 也可以將資料上傳至 HDInsight 服務。 若要深入了解，請參閱：

* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Apache Hive 資料視覺化](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 將互動式查詢 Hive 資料視覺化](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](apache-hadoop-visual-studio-tools-get-started.md)。
