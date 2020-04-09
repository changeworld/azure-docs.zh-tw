---
title: 在 HDInsight 中上傳 Apache Hadoop 作業的資料
description: 瞭解如何上傳和訪問 HDInsight 中的 Apache Hadoop 作業的數據。 使用 Azure 經典 CLI、Azure 儲存資源管理員、Azure PowerShell、Hadoop 命令列或 Sqoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875005"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>在 HDInsight 中上傳 Apache Hadoop 作業的資料

HDInsight 透過 Azure 儲存和 Azure 資料湖儲存提供 Hadoop 分散式檔案系統 (HDFS)。 此存儲包括第 1 代和第 2 代。 Azure 儲存和資料儲存湖儲存第 1 代和第 2 代設計為 HDFS 擴展。 它們使 Hadoop 環境中的完整元件集能夠直接基於其管理的數據進行操作。 Azure 儲存、資料儲存第 1 代和第 2 代是不同的檔案系統。 系統經過優化,可存儲數據並計算該數據。 有關使用 Azure 儲存的好處的資訊,請參閱[將 Azure 儲存與 HDInsight 一起使用](hdinsight-hadoop-use-blob-storage.md)。 另請參閱,[使用資料湖儲存第 1 代與 HDInsight](hdinsight-hadoop-use-data-lake-store.md), 並使用[資料儲存湖儲存 Gen2 與 HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Prerequisites

開始進行之前，請注意下列需求：

* Azure HDInsight 叢集。 如需指示，請參閱 [Azure HDInsight 使用者入門](hadoop/apache-hadoop-linux-tutorial-get-started.md)或[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。
* 下列文章的知識：
    * [將 Azure 儲存與 HDInsight 一起使用](hdinsight-hadoop-use-blob-storage.md)
    * [搭配 HDInsight 使用 Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md)
    * [搭配 HDInsight 使用 Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>將資料上傳至 Azure 儲存體

## <a name="utilities"></a>公用事業

Microsoft 提供下列公用程式來使用 Azure 儲存體：

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 門戶](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [阿茲比貝](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop 命令](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop 命令只能在 HDInsight 叢集上使用。 此命令只允許從本機檔案系統將資料載入 Azure 儲存體中。  

## <a name="hadoop-command-line"></a>Hadoop 命令列

Hadoop 命令列僅適用於當資料已存在於叢集前端節點時，將資料儲存到 Azure 儲存體 blob。

要使用 Hadoop 命令,必須首先使用[SSH 或 PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)連接到頭節點。

連線之後，您就可以使用下列語法來將檔案上傳到儲存體。

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

例如， `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

由於 HDInsight 的預設檔案系統位於 Azure 儲存中,因此 /範例/資料/data.txt 實際上位於 Azure 儲存中。 您也可以用下列語法來參考此檔案：

    wasbs:///example/data/data.txt

或

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

有關處理檔案的其他 Hadoop 命令的清單,請參閱[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> 在 Apache HBase 叢集上，寫入資料時所使用的預設區塊大小是 256 KB。 雖然在使用 HBase API 或 REST API 時此大小可正常運作，但使用 `hadoop` 或 `hdfs dfs` 命令寫入大於 ~12 GB 的資料會導致錯誤。 如需詳細資訊，請參閱本文中[在 Blob 上寫入時的儲存體例外狀況](#storage-exception-for-write-on-blob)一節。

## <a name="graphical-clients"></a>圖形化用戶端

其他還有數個應用程式也會提供可搭配 Azure 儲存體使用的圖形化介面。 以下資料表提供數個這類應用程式的清單：

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure 儲存體總管](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [適用於 Microsoft Azure 的 CloudBerry 總管](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>將 Azure 儲存體掛接為本機磁碟機

請參閱[將 Azure 儲存體掛接為本機磁碟機](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)。

## <a name="upload-using-services"></a>使用服務上傳

### <a name="azure-data-factory"></a>Azure Data Factory

Azure 資料工廠服務是一種完全託管的服務,用於將數據組合:將存儲、處理和行動服務組合到簡化、適應性強和可靠的數據生産管中。

|儲存體類型|文件|
|----|----|
|Azure Blob 儲存體|[使用 Azure 資料工廠將資料複製到或從 Azure Blob 儲存中複製資料](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[使用 Azure Data Factory 從 Azure Data Lake Storage Gen1 來回複製資料](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[使用 Azure Data Factory 將資料載入 Azure Data Lake Storage Gen2 中](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop 是一種專門在 Hadoop 和關聯式資料庫之間傳送資料的工具。 使用它從關係資料庫管理系統 (RDBMS) 匯入數據,例如 SQL Server、MySQL 或 Oracle。 然後進入 Hadoop 分散式檔案系統 (HDFS)。 使用 MapReduce 或 Hive 轉換 Hadoop 中的數據,然後將數據匯出回 RDBMS。

如需詳細資訊，請參閱[搭配 HDInsight 使用 Sqoop](hadoop/hdinsight-use-sqoop.md)。

### <a name="development-sdks"></a>開發 SDK

Azure 儲存體也可以使用 Azure SDK，透過下列程式設計語言進行存取：

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

如需安裝 Azure SDK 的詳細資訊，請參閱 [Azure 下載](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>疑難排解

### <a name="storage-exception-for-write-on-blob"></a>在 Blob 上寫入時的儲存體例外狀況

**症狀**:`hadoop``hdfs dfs`當 使用 或 指令在 HBase 叢集上寫入 +12 GB 或更大的檔案時,可能會遇到以下錯誤:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**原因**:寫入 Azure 儲存時,HDInsight 叢集上的 HBase 預設為 256 KB 的塊大小。 雖然這適用於 HBase API 或 REST API，但會導致在使用 `hadoop` 或 `hdfs dfs` 命令列公用程式時發生錯誤。

**解決方案**︰使用 `fs.azure.write.request.size` 指定較大的區塊大小。 您可以使用 參數在每次使用`-D`的基礎上 執行此修改。 以下命令是搭配使用此參數與 `hadoop` 命令的範例︰

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

您也可以使用 Apache Ambari 來全域提高 `fs.azure.write.request.size` 的值。 使用下列步驟即可變更 Ambari Web UI 中的值︰

1. 在瀏覽器中，移至叢集的 Ambari Web UI。 URL`https://CLUSTERNAME.azurehdinsight.net`是`CLUSTERNAME`, 群集的名稱位於此位置。 出現提示時，請輸入該叢集的管理員名稱和密碼。
2. 在畫面左側選取 [HDFS]****，然後選取 [設定]**** 索引標籤。
3. 在 [篩選...]**** 欄位中，輸入 `fs.azure.write.request.size`。
4. 將值從 262144 (256 KB) 變更為新值。 例如，4194304 (4 MB)。

    ![透過 Ambari Web UI 變更值的影像](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

如需有關使用 Ambari 的詳細資訊，請參閱[使用 Apache Ambari Web UI 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解如何將資料輸入 HDInsight,請閱讀以下文章以瞭解分析:

* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [以程式設計方式提交 Apache Hadoop 作業](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Apache Pig](./use-pig.md)
