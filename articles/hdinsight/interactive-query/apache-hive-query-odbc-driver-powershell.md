---
title: 使用 ODBC 驅動程式與 PowerShell 查詢 Apache Hive - Azure HDInsight
description: 使用 Microsoft Hive ODBC 驅動程式和 PowerShell 查詢 Azure HDInsight 上的 Apache Hive 叢集。
keywords: hive, hive odbc,powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494319"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>教學課程：使用 ODBC 和 PowerShell 查詢 Apache Hive

Microsoft ODBC 驅動程式提供彈性的方式來與不同資料來源類型 (包括 Apache Hive) 互動。 您可以在 PowerShell 等指令碼語言中撰寫程式碼，這些語言會使用 ODBC 驅動程式來開啟與您 Hive 叢集的連線，然後傳遞您選擇的查詢並顯示結果。

在此教學課程中，您將執行下列工作：

> [!div class="checklist"]
> * 下載並安裝 Microsoft Hive ODBC 驅動程式
> * 建立連結至您叢集的 Apache Hive ODBC 資料來源
> * 使用 PowerShell 從您的叢集中查詢範例資訊

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，您必須具備下列項目：

* HDInsight 上的互動式查詢叢集。 若要建立，請參閱[開始使用 Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)。 選擇 [互動式查詢]  作為叢集類型。

## <a name="install-microsoft-hive-odbc-driver"></a>安裝 Microsoft Hive ODBC 驅動程式

下載並安裝 [Microsoft Hive ODBC 驅動程式](https://www.microsoft.com/download/details.aspx?id=40886)。

## <a name="create-apache-hive-odbc-data-source"></a>建立 Apache Hive ODBC 資料來源

下列步驟將說明如何建立 Apache Hive ODBC 資料來源。

1. 從 Windows 中，瀏覽至 [開始]   > [Windows 系統管理工具]   > [ODBC 資料來源 (32 位元)/(64 位元)]  。  [ODBC 資料來源管理員]  視窗會隨即開啟。

    ![ODBC 資料來源管理員](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "使用 ODBC 資料來源管理員設定 DSN")

1. 從 [使用者 DSN]  索引標籤，選取 [新增]  以開啟 [建立新資料來源]  視窗。

1. 選取 [Microsoft Hive ODBC 驅動程式]  ，然後選取 [完成]  以開啟 [Microsoft Hive ODBC 驅動程式 DSN 設定]  視窗。

1. 輸入或選取下列值：

   | 屬性 | 描述 |
   | --- | --- |
   |  資料來源名稱 |為資料來源指定名稱 |
   |  主機 |輸入 `CLUSTERNAME.azurehdinsight.net`。 例如， `myHDICluster.azurehdinsight.net` |
   |  連接埠 |使用 **443**|
   |  資料庫 |使用**預設值**。 |
   |  機制 |選取 [Windows Azure HDInsight 服務]  |
   |  使用者名稱 |輸入 HDInsight 叢集 HTTP 使用者的使用者名稱。 預設的使用者名稱為 **admin**。 |
   |  密碼 |輸入 HDInsight 叢集使用者的密碼。 選取 [儲存密碼 (加密)]  核取方塊。|

1. 選擇性：選取 [進階選項]  。  

   | 參數 | 描述 |
   | --- | --- |
   |  使用原生查詢 |選取此選項時，ODBC 驅動程式不會嘗試將 TSQL 轉換為 HiveQL。 只有當您 100% 確定要送出純 HiveQL 陳述式時，再使用此選項。 連接到 SQL Server 或 Azure SQL Database 時，您應將它保留為未勾選。 |
   |  每個區塊擷取的資料列 |在擷取大量記錄時，可能必須調整此參數，以確保最佳效能。 |
   |  預設字串資料行長度、二進位資料行長度、十進位資料行小數位數 |資料類型的長度和精確度可能會影響傳回資料的方式。 如果失去精確度且發生截斷狀況，會傳回不正確的資訊。 |

    ![進階 DSN 設定選項](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "進階 DSN 設定選項")

1. 選取 [測試]  以測試資料來源。 正確設定資料來源時，測試結果就會顯示**成功**。  

1. 選取 [確定]  以關閉 [測試] 視窗。  

1. 選取 [確定]  以關閉 [Microsoft Hive ODBC 驅動程式 DSN 設定]  視窗。  

1. 選取 [確定]  以關閉 [ODBC 資料來源管理員]  視窗。  

## <a name="query-data-with-powershell"></a>使用 PowerShell 查詢資料

下列 PowerShell 指令碼是 ODBC 用來查詢 Hive 叢集的函式。

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

下列程式碼片段會使用上述函數，在本教學課程開頭所建立的互動式查詢叢集上執行查詢。 將 `DATASOURCENAME` 取代為您在 [Microsoft Hive ODBC 驅動程式 DSN 設定]  畫面上指定的**資料來源名稱**。 出現提供認證的提示時，請輸入建立叢集時於 [叢集登入使用者名稱]  和 [叢集登入密碼]  下輸入的使用者名稱和密碼。

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、HDInsight 叢集和儲存體帳戶。 若要這樣做，請選取建立叢集所在的資源群組，然後按一下 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Microsoft Hive ODBC 驅動程式和 PowerShell，從您的 Azure HDInsight 互動式查詢叢集中擷取資料。

> [!div class="nextstepaction"]
> [將 Excel 連線到使用 ODBC 的 Apache Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
