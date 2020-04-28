---
title: 針對 Azure HDInsight 中的 HDFS 進行疑難排解
description: 取得有關使用 HDFS 和 Azure HDInsight 的常見問題解答。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 6de9e31c3e79f6d704ef8b4749d41329dcc0bddb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190670"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>使用 Azure HDInsight 針對 Apache Hadoop HDFS 問題進行疑難排解

瞭解使用 Hadoop 分散式檔案系統（HDFS）時的最佳問題和解決方法。 如需完整的命令清單，請參閱[HDFS 命令指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)和[檔案系統 Shell 指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)。

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>如何從叢集內部存取本機 HDFS？

### <a name="issue"></a>問題

從命令列和應用程式程式碼存取本機 HDFS，而不是從 HDInsight 叢集內部使用 Azure Blob 儲存體或 Azure Data Lake Storage 進行存取。

### <a name="resolution-steps"></a>解決步驟

1. 在命令提示字元中依照字面使用 `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...`，如下列命令所示：

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. 從原始程式碼依照字面使用 URI `hdfs://mycluster/`，如下列範例應用程式所示：

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. 使用下列命令在 HDInsight 叢集上執行已編譯的 .jar 檔案 (例如名為 `java-unit-tests-1.0.jar` 的檔案)：

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>在 Blob 上寫入時的儲存體例外狀況

### <a name="issue"></a>問題

當您使用`hadoop`或`hdfs dfs`命令在 HBase 叢集上寫入 ~ 12 GB 或更大的檔案時，可能會遇到下列錯誤：

```error
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
```

### <a name="cause"></a>原因

在寫入 Azure 儲存體時，HDInsight 叢集上的 HBase 會將區塊大小預設為 256 KB。 雖然這適用於 HBase API 或 REST API，但會導致在使用 `hadoop` 或 `hdfs dfs` 命令列公用程式時發生錯誤。

### <a name="resolution"></a>解決方法

使用 `fs.azure.write.request.size` 來指定較大的區塊大小。 您可以使用`-D`參數，根據每次使用來執行這項修改。 以下命令是搭配使用此參數與 `hadoop` 命令的範例︰

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

您也可以使用 Apache Ambari 來全域提高 `fs.azure.write.request.size` 的值。 使用下列步驟即可變更 Ambari Web UI 中的值︰

1. 在瀏覽器中，移至叢集的 Ambari Web UI。 URL 是`https://CLUSTERNAME.azurehdinsight.net`，其中`CLUSTERNAME`是您的叢集名稱。 出現提示時，請輸入該叢集的管理員名稱和密碼。
2. 在畫面左側選取 [HDFS]****，然後選取 [設定]**** 索引標籤。
3. 在 [篩選...]**** 欄位中，輸入 `fs.azure.write.request.size`。
4. 將值從 262144 (256 KB) 變更為新值。 例如，4194304 (4 MB)。

    ![透過 Ambari Web UI 變更值的影像](./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png)

如需有關使用 Ambari 的詳細資訊，請參閱[使用 Apache Ambari Web UI 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="du"></a>du

[`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du)命令會顯示指定目錄中包含的檔案和目錄大小，或檔案的長度（如果檔案只是檔案）。

`-s`選項會產生所顯示檔案長度的匯總摘要。  
`-h`選項會格式化檔案大小。

範例：

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>rm

[-Rm](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm)命令會刪除指定為引數的檔案。

範例：

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
