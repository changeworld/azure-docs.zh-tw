---
title: 使用 Livy Spark 將作業提交至 Azure HDInsight 上的 Spark 叢集
description: 了解如何使用 Apache Spark REST API 從遠端將 Spark 作業提交至 Azure HDInsight 叢集。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ff63f4fbadd7cb9e7584e2aa045583a35e0363fd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930121"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>使用 Apache Spark REST API 將遠端作業提交至 HDInsight Spark 叢集

了解如何使用可將遠端作業提交至 Azure HDInsight Spark 叢集的 [Apache Livy](https://livy.incubator.apache.org/) (也就是 Apache Spark REST API)。 如需詳細檔，請參閱 [Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)。

您可以使用 Livy 執行互動式 Spark 殼層，或提交要在 Spark 上執行的批次作業。 本文將討論如何使用 Livy 提交批次作業。 本文中的程式碼片段會使用 cURL 向 Livy Spark 端點發出 REST API 呼叫。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

## <a name="submit-an-apache-livy-spark-batch-job"></a>提交 Apache Livy Spark 批次作業

在提交批次作業之前，您必須將應用程式 jar 上傳至與叢集相關聯的叢集儲存體。 您可以使用命令列公用程式 [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 來執行此動作。 此外也有各種用戶端可用來上傳資料。 [在 HDInsight 上將 Apache Hadoop 作業的資料上傳](../hdinsight-upload-data.md)中可找到其詳細資訊。

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>範例

* 如果 jar 檔案位於叢集儲存體 (WASBS) 

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* 如果您需要在輸入檔案 (在此範例中為 input.txt) 中傳遞 jar 檔案名稱和類別名稱

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>取得在叢集上執行之 Livy Spark 批次的相關資訊

語法：

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>範例

* 如果您想要擷取在叢集上執行的所有 Livy Spark 批次：

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* 如果您想要使用指定的批次識別碼來取出特定批次

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>將 Livy Spark 批次作業刪除

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>範例

正在刪除具有批次識別碼的批次作業 `5` 。

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark 與高可用性

Livy 可為在叢集上執行的 Spark 作業提供高可用性。 以下是一些範例。

* 當您從遠端將作業提交至 Spark 叢集之後，如果 Livy 服務中斷，則工作會繼續在背景執行。 當 Livy 恢復運作時，它會還原作業的狀態並回報。
* 適用于 HDInsight 的 Jupyter 筆記本是由後端中的 Livy 提供技術支援。 如果在 Notebook 執行 Spark 作業時，Livy 服務重新啟動，Notebook 就會繼續執行程式碼單元。

## <a name="show-me-an-example"></a>請舉例說明

在本節中，我們將透過範例了解如何使用 Livy Spark 來提交批次作業、監視作業的進度，然後加以刪除。 我們在此範例中使用的應用程式，就是 [建立獨立 Scala 應用程式，並在 HDInsight Spark 叢集上執行](apache-spark-create-standalone-application.md)一文中所開發的應用程式。 這裡的步驟假設：

* 您已將應用程式 jar 複製到與叢集相關聯的儲存體帳戶。
* 您已在嘗試執行這些步驟的電腦上安裝了捲曲。

執行下列步驟：

1. 為了方便使用，請設定環境變數。 這個範例是以 Windows 環境為基礎，視您的環境需要修訂變數。 `CLUSTERNAME` `PASSWORD` 以適當的值取代和。

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. 確認 Livy Spark 正在叢集中執行。 我們可以取得執行中的批次清單，加以確認。 如果您是第一次使用 Livy 執行作業，則輸出應該會傳回零。

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    您應該會看到如下列程式碼片段的輸出：

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    請留意到輸出的最後一行顯示為 **total:0**，這表示沒有執行中的批次。

1. 現在，我們要提交批次作業。 下列程式碼片段會使用輸入檔案 (input.txt) 傳遞 jar 名稱和類別名稱來作為參數。 如果您是從 Windows 電腦執行這些步驟，則建議使用輸入檔作為建議的方法。

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    檔案 **input.txt** 中的參數定義如下：

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    您應該會看到如下列程式碼片段的輸出：

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    請留意到輸出的最後一行顯示為 **state:starting**。 此外也顯示 **id:0**。 在這裡，批次識別碼是 **0**。

1. 現在，您可以使用批次識別碼來擷取此批次的狀態。

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    您應該會看到如下列程式碼片段的輸出：

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    輸出此時顯示 **state:success**，這表示作業已順利完成。

1. 現在，您可以視需要刪除批次。

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    您應該會看到如下列程式碼片段的輸出：

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    輸出的最後一行顯示批次已成功刪除。 當作業正在執行時，刪除作業也會終止作業。 如果您刪除已完成的作業，無論成功與否，這將會完全刪除作業資訊。

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>從 HDInsight 3.5 版開始對 Livy 設定的更新

根據預設，HDInsight 3.5 叢集與更新版本會停用以本機檔案路徑存取範例資料檔案或 jar。 建議您使用 `wasbs://` 路徑，而不是從叢集存取 jar 或範本資料檔案。

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>在 Azure 虛擬網路內提交叢集的 Livy 作業

如果您是從 Azure 虛擬網路內連線到 HDInsight Spark 叢集，可以直接連線到叢集上的 Livy。 在此案例中，Livy 端點的 URL 是 `http://<IP address of the headnode>:8998/batches`。 在這裡，**8998** 是 Livy 在叢集前端節點上執行的連接埠。 如需有關在非公用連接埠上存取服務的詳細資訊，請參閱 [HDInsight 上 Apache Hadoop 服務所使用的連接埠](../hdinsight-hadoop-port-settings-for-services.md)。

## <a name="next-steps"></a>後續步驟

* [Apache Livy REST API 文件](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)