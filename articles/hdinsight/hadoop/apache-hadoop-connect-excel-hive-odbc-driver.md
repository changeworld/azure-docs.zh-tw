---
title: Excel &阿帕奇哈多普與ODBC驅動程式 - Azure HDInsight
description: 了解如何設定和使用 Excel 的 Microsoft Hive ODBC 驅動程式，從 Microsoft Excel 查詢 HDInsight 叢集中的資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251075"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 Azure HDInsight 中的 Apache Hadoop

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft 的巨量資料解決方案會將 Microsoft 商業智慧 (BI) 元件與已部署於 Azure HDInsight 中的 Apache Hadoop 叢集整合。 舉例來說，此整合可讓您使用 Microsoft Hive 開放式資料庫連線能力 (ODBC) 驅動程式，將 Excel 連線到 HDInsight 中 Hadoop 叢集的 Hive 資料倉儲。

還可以使用 Excel 的 Microsoft 電源查詢外接程式從 Excel 連接與 HDInsight 群集和其他資料來源（包括其他（非 HDInsight） Hadoop 群集關聯的資料。 如需安裝和使用 Power Query 的相關資訊，請參閱[使用 Power Query 將 Excel 連接到 HDInsight](../hdinsight-connect-excel-power-query.md)。

## <a name="prerequisites"></a>Prerequisites

開始閱讀本文之前，您必須有下列各項：

* HDInsight Hadoop 叢集。 若要建立，請參閱[開始使用 Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)。
* 具有 Office 2010 專業增強版或更新版本，或是 Excel 2010 或更新版本的工作站。

## <a name="install-microsoft-hive-odbc-driver"></a>安裝 Microsoft Hive ODBC 驅動程式

下載並安裝與應用程式版本相匹配的應用程式版本（使用 ODBC 驅動程式）的[Microsoft Hive ODBC 驅動程式](https://www.microsoft.com/download/details.aspx?id=40886)版本。  在本文中，驅動程式用於 Office Excel。

## <a name="create-apache-hive-odbc-data-source"></a>建立 Apache Hive ODBC 資料來源

下列步驟將說明如何建立 Hive ODBC 資料來源。

1. 從 Windows 中，瀏覽至 [開始] > [Windows 系統管理工具] > [ODBC 資料來源 (32 位元)/(64 位元)]。  這樣會開啟 [ODBC 資料來源管理員]**** 視窗。

    ![OBDC data source administrator](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "使用 ODBC 資料來源管理員設定 DSN")

1. 從 [使用者 DSN]**** 索引標籤，選取 [新增]**** 以開啟 [建立新資料來源]**** 視窗。

1. 選取 [Microsoft Hive ODBC 驅動程式]****，然後選取 [完成]**** 以開啟 [Microsoft Hive ODBC 驅動程式 DSN 設定]**** 視窗。

1. 輸入或選取下列值：

   | 屬性 | 描述 |
   | --- | --- |
   |  資料來源名稱 |為資料來源指定名稱 |
   |  主機 |輸入 `HDInsightClusterName.azurehdinsight.net`。 例如： `myHDICluster.azurehdinsight.net` 。 注意：`HDInsightClusterName-int.azurehdinsight.net`只要用戶端 VM 與同一虛擬網路對等，就受支援。 |
   |  連接埠 |使用 **443** (此連接埠已從 563 變更為 443)。 |
   |  資料庫 |使用**預設值**。 |
   |  機制 |選取 [Windows Azure HDInsight 服務]**** |
   |  使用者名稱 |輸入 HDInsight 叢集 HTTP 使用者的使用者名稱。 預設的使用者名稱為 **admin**。 |
   |  密碼 |輸入 HDInsight 叢集使用者的密碼。 選取 [儲存密碼 (加密)]**** 核取方塊。|

1. 可選：選擇**高級選項...**  

   | 參數 | 描述 |
   | --- | --- |
   |  使用原生查詢 |選取此選項時，ODBC 驅動程式不會嘗試將 TSQL 轉換為 HiveQL。 只有當您 100% 確信您提交了純 HiveQL 語句時，您才應使用它。 連接到 SQL Server 或 Azure SQL Database 時，您應將它保留為未勾選。 |
   |  每個區塊擷取的資料列 |在擷取大量記錄時，可能必須調整此參數，以確保最佳效能。 |
   |  預設字串資料行長度、二進位資料行長度、十進位資料行小數位數 |資料類型的長度和精確度可能會影響傳回資料的方式。 如果失去精確度且 (或) 發生截斷狀況，會傳回不正確的資訊。 |

    ![進階 DSN 設定選項](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "進階 DSN 設定選項")

1. 選取 [測試]**** 以測試資料來源。 正確配置資料來源後，測試結果將顯示**成功！**。  

1. 選取 [確定]**** 以關閉 [測試] 視窗。  

1. 選取 [確定]**** 以關閉 [Microsoft Hive ODBC 驅動程式 DSN 設定]**** 視窗。  

1. 選取 [確定]**** 以關閉 [ODBC 資料來源管理員]**** 視窗。  

## <a name="import-data-into-excel-from-hdinsight"></a>從 HDInsight 將資料匯入 Excel 中

下列步驟將說明如何使用您在上一節中建立的 ODBC 資料來源，將資料從 Hive 資料表匯入 Excel 活頁簿中。

1. 在 Excel 中開啟新的或現有的活頁簿。

2. 從 [資料]**** 索引標籤，瀏覽至 [取得資料]**** > [從其他來源]**** > [從 ODBC]**** 以啟動 [從 ODBC]**** 視窗。

    ![打開 Excel 資料連線嚮導](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "打開 Excel 資料連線嚮導")

3. 從下拉清單中，選擇您在最後一節中創建的資料來源名稱，然後選擇 **"確定**"。

4. 對於首次使用，將打開**一個 ODBC 驅動程式**對話方塊。 從左側功能表中選擇 **"視窗**"。 然後選擇 **"連接"** 以打開**導航器**視窗。

5. 從 [導覽器]**** 中，瀏覽至 [HIVE]**** > [預設值]**** > [hivesampletable]****，然後選取 [載入]****。 資料需要一些時間才會匯入至 Excel。

    ![HDInsight Excel 蜂巢 ODBC 導航器](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel 蜂巢 ODBC 導航器")

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用 Microsoft Hive ODBC 驅動程式將 HDInsight 服務中的資料擷取至 Excel。 同樣地，您也可以將 HDInsight 服務中的資料擷取至 SQL Database。 還可以將資料上載到 HDInsight 服務。 若要深入了解，請參閱：

* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Apache Hive 資料視覺化](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 將互動式查詢 Hive 資料視覺化](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [使用 Apache Zepelin 在 Azure HDInsight 中運行 Apache Hive 查詢](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用 Azure HDInsight 工具進行視覺化工作室代碼](../hdinsight-for-vscode.md)。
* [將資料上傳到 HDInsight](./../hdinsight-upload-data.md)。
