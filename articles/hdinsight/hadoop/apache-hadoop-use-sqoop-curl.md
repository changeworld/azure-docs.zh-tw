---
title: 使用 Curl 在 Azure HDInsight 中使用 Apache Sqoop 匯出資料
description: 瞭解如何使用 Curl 遠端將 Apache Sqoop 作業提交到 Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767581"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>使用 Curl 在 HDInsight 中運行 Apache Sqoop 作業

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Curl 在 HDInsight 中的 Apache Hadoop 叢集上執行 Apache Sqoop 作業。 本文演示如何從 Azure 存儲匯出資料，並使用 Curl 將其導入 SQL Server 資料庫。 本文是[使用阿帕奇奇普與哈多普在HDInsight](./hdinsight-use-sqoop.md)的延續。

本文件使用 Curl 示範如何使用未經處理的 HTTP 要求來與 HDInsight 互動，以便執行、監視和擷取 Sqoop 作業的結果。 要想執行這些作業，就要使用 HDInsight 叢集所提供的 WebHCat REST API (先前稱為 Templeton)。

## <a name="prerequisites"></a>Prerequisites

* 完成[設置測試環境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)從[使用阿帕奇與Hadoop在HDInsight。](./hdinsight-use-sqoop.md)

* 要查詢 Azure SQL 資料庫的用戶端。 請考慮使用[SQL 伺服器管理工作室](../../sql-database/sql-database-connect-query-ssms.md)或[視覺化工作室代碼](../../sql-database/sql-database-connect-query-vscode.md)。

* [捲曲](https://curl.haxx.se/)。 Curl 是將資料傳送至 HDInsight 叢集或從 HDInsight 叢集傳送資料的工具。

* [jq](https://stedolan.github.io/jq/)。 jq 公用程式用來處理從 REST 要求傳回的 JSON 資料。

* 熟悉 Sqoop。 有關詳細資訊，請參閱[Sqoop 使用者指南](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)。

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>使用 Curl 提交 Apache Sqoop 作業

使用 Curl 使用 Apache Sqoop 作業從 Azure 存儲匯出到 SQL 伺服器的資料。

> [!NOTE]  
> 在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集系統管理員的使用者名稱和密碼來驗證要求。 您也必須在用來將要求傳送至伺服器的統一資源識別項 (URI) 中使用叢集名稱。

對於本節中的命令，請替換為`USERNAME`使用者以對群集進行身份驗證，然後替換為`PASSWORD`使用者帳戶的密碼。 將 `CLUSTERNAME` 取代為您的叢集名稱。

透過 [基本驗證](https://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 您應該一律使用安全 HTTP (HTTPS) 提出要求，確保認證安全地傳送至伺服器。

1. 為便於使用，設置下面的變數。 此示例基於 Windows 環境，根據需要修改環境。

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    您應該會收到如下所示的回應：

    ```json
    {"status":"ok","version":"v1"}
    ```

1. 使用以下命令提交 Sqoop 作業：

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    此命令中使用的參數如下：

   * **-d** -`-G`由於未使用，因此請求預設為 POST 方法。 `-d` 可指定與要求一起傳送的資料值。

       * **user.name** - 執行命令的使用者。

       * **命令** - 要執行的 Sqoop 命令。

       * **statusdir** - 要寫入此工作狀態的目錄。

     此命令將返回可用於檢查作業狀態的作業 ID。

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. 若要檢查工作的狀態，請使用下列命令。 將 `JOBID` 取代為上一個步驟中所傳回的值。 例如，如果傳回值為`{"id":"job_1415651640909_0026"}`，則`JOBID`將為`job_1415651640909_0026`。 根據需要修改位置`jq`。

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    如果工作已完成，則狀態會是 [ **成功**]。

   > [!NOTE]  
   > 此 Curl 要求會傳回含有工作資訊的 JavaScript Object Notation (JSON) 文件；jq 可用來僅擷取狀態值。

1. 工作狀態變更為 [成功]**** 之後，即可從 Azure Blob 儲存體擷取工作結果。 與查詢一起傳遞的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 `wasb:///example/data/sqoop/curl`。 此位址將作業的輸出存儲在 HDInsight`example/data/sqoop/curl`群集使用的預設存儲容器的目錄中。

    您可以使用 Azure 入口網站存取 stderr 和 stdout Blob。

1. 要驗證資料是否匯出，請使用 SQL 用戶端的以下查詢來查看匯出的資料：

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>限制

* 批量匯出 - 使用基於 Linux 的 HDInsight，用於將資料匯出到 Microsoft SQL Server 或 Azure SQL 資料庫的 Sqoop 連接器當前不支援批量插入。
* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

## <a name="summary"></a>總結

如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集上的 Sqoop 作業結果。

如需本文中使用的 REST 介面的詳細資訊，請參閱 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API 指南</a>。

## <a name="next-steps"></a>後續步驟

[在 HDInsight 將 Apache Sqoop 與 Apache Hadoop 搭配使用](hdinsight-use-sqoop.md)

如需涉及 curl 的其他 HDInsight 文章：

* [使用 Azure REST API 建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [使用 REST 以 HDInsight 中的 Apache Hadoop 執行 Apache Hive 查詢](apache-hadoop-use-hive-curl.md)
* [使用 REST 搭配 HDInsight 上的 Apache Hadoop 執行 MapReduce 作業](apache-hadoop-use-mapreduce-curl.md)