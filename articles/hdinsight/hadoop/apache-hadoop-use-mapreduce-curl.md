---
title: 搭配使用 MapReduce 和 Curl 與 HDInsight 中的 Apache Hadoop - Azure
description: 了解如何使用 Curl 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Apache Hadoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302707"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>使用 REST 搭配 HDInsight 上的 Apache Hadoop 執行 MapReduce 作業

瞭解如何使用 Apache Hive WebHCat REST API 在 HDInsight 叢集上的 Apache Hadoop 上執行 MapReduce 工作。 Curl 用來示範如何使用原始 HTTP 要求與 HDInsight 互動，以執行 MapReduce 工作。

> [!NOTE]  
> 如果您已熟悉 Linux 型 Hadoop 伺服器的用法，但不熟悉 HDInsight，請參閱 [Linux 型 HDInsight 上的 Apache Hadoop 須知](../hdinsight-hadoop-linux-information.md)文件。

## <a name="prerequisites"></a>先決條件

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop](../hdinsight-hadoop-create-linux-clusters-portal.md)叢集。

下列任一方法︰
  * Windows PowerShell 或、
  * [Jq](https://stedolan.github.io/jq/)的[捲曲](https://curl.haxx.se/)

## <a name="run-a-mapreduce-job"></a>執行 MapReduce 作業

> [!NOTE]  
> 在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集管理員使用者名稱和密碼來驗證要求。 您必須使用叢集名稱，作為用來將要求傳送至伺服器之 URI 的一部分。
>
> 使用 [基本存取驗證](https://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 您應該一律使用 HTTPS 提出要求，確保認證安全地傳送至伺服器。

### <a name="curl"></a>Curl

1. 為了方便使用，請設定下列變數。 這個範例是以 Windows 環境為基礎，視您的環境需要進行修訂。

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    此命令中使用的參數如下：

   * **-u**：指出用來驗證要求的使用者名稱和密碼
   * **-G**：指出此作業是 GET 要求

   URI 的開頭 `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` 適用於所有要求。

    您應該會收到類似以下 JSON 的回應：

    ```output
    {"version":"v1","status":"ok"}
    ```

1. 若要提交 MapReduce 工作，請使用下列命令。 視需要修改**jq**的路徑。

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    URI 結尾 (/mapreduce/jar) 會告訴 WebHCat，此要求會從 jar 檔案中的類別啟動 MapReduce 作業。 此命令中使用的參數如下：

   * **-d**： `-G`未使用，因此要求會預設為 POST 方法。 `-d` 可指定與要求一起傳送的資料值。
     * **user.name**：執行命令的使用者
     * **jar**：包含要執行之類別的 jar 檔案位置
     * **class**：包含 MapReduce 邏輯的類別
     * **arg**︰要傳遞到 MapReduce 作業的引數。 在此案例中，是用於輸出的輸入文字檔和目錄

    此命令應該會傳回可用來檢查工作狀態的工作識別碼：

       job_1415651640909_0026

1. 若要檢查工作的狀態，請使用下列命令。 將的值取代`JOBID`為上一個步驟中所傳回的**實際**值。 視需要修改**jq**的位置。

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. 為了方便使用，請設定下列變數。 將`CLUSTERNAME`取代為您實際的叢集名稱。 執行命令，並在出現提示時輸入叢集登入密碼。

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. 使用下列命令來確認您可以連線到您的 HDInsight 叢集：

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    您應該會收到類似以下 JSON 的回應：

    ```output
    {"version":"v1","status":"ok"}
    ```

1. 若要提交 MapReduce 工作，請使用下列命令：

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    URI 結尾 (/mapreduce/jar) 會告訴 WebHCat，此要求會從 jar 檔案中的類別啟動 MapReduce 作業。 此命令中使用的參數如下：

    * **user.name**：執行命令的使用者
    * **jar**：包含要執行之類別的 jar 檔案位置
    * **class**：包含 MapReduce 邏輯的類別
    * **arg**︰要傳遞到 MapReduce 作業的引數。 在此案例中，是用於輸出的輸入文字檔和目錄

   此命令應該會傳回可用來檢查工作狀態的工作識別碼：

       job_1415651640909_0026

1. 若要檢查作業的狀態，請使用下列命令：

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing

    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

### <a name="both-methods"></a>這兩種方法

1. 如果作業已完成，傳回的狀態會是 `SUCCEEDED`。

1. 當作業狀態變更為 `SUCCEEDED` 之後，您就可以從 Azure Blob 儲存體擷取作業結果。 隨查詢一起傳遞的 `statusdir` 參數包含輸出檔案的位置。 在此範例中，位置是 `/example/curl`。 此位址會將作業的輸出儲存在叢集預設儲存體的 `/example/curl` 中。

您可以使用 [Azure CLI](/cli/azure/install-azure-cli) 列出並下載這些檔案。 如需有關使用 Azure CLI 來處理 Azure Blob 儲存體的詳細資訊，請參閱[快速入門：使用 Azure CLI 建立、下載及列出 blob](../../storage/blobs/storage-quickstart-blobs-cli.md)。

## <a name="next-steps"></a>後續步驟

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)
* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hdinsight-use-hive.md)

如需本文中使用的 REST 介面的詳細資訊，請參閱 [WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。
