---
title: 在 HDInsight 中上傳 Apache Hadoop 作業的資料
description: 瞭解如何在 HDInsight 中上傳及存取 Apache Hadoop 作業的資料。 使用 Azure 傳統 CLI、Azure 儲存體總管、Azure PowerShell、Hadoop 命令列或 Sqoop。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931334"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>在 HDInsight 中上傳 Apache Hadoop 作業的資料

HDInsight 提供 Hadoop 分散式檔案系統 (HDFS) Azure 儲存體和 Azure Data Lake Storage。 此儲存體包含 Gen1 和 Gen2。 Azure 儲存體和 Data Lake Storage Gen1 和 Gen2 是設計為 HDFS 延伸模組。 它們可在 Hadoop 環境中啟用一組完整的元件，直接在其管理的資料上操作。 Azure 儲存體、Data Lake Storage Gen1 和 Gen2 都是不同的檔案系統。 系統會針對資料的儲存和計算進行優化。 如需使用 Azure 儲存體之優點的相關資訊，請參閱搭配 [HDInsight 使用 Azure 儲存體](hdinsight-hadoop-use-blob-storage.md)。 另請參閱搭配 [Hdinsight 使用 Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-storage-gen1.md)，以及搭配 [hdinsight 使用 Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

## <a name="prerequisites"></a>先決條件

開始進行之前，請注意下列需求：

* Azure HDInsight 叢集。 如需相關指示，請參閱 [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* 下列文章的知識：
    * [搭配 HDInsight 使用 Azure 儲存體](hdinsight-hadoop-use-blob-storage.md)
    * [搭配 HDInsight 使用 Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [搭配 HDInsight 使用 Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>將資料上傳至 Azure 儲存體

### <a name="utilities"></a>公用程式

Microsoft 提供下列公用程式來使用 Azure 儲存體：

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop 命令](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop 命令只能在 HDInsight 叢集上使用。 此命令只允許從本機檔案系統將資料載入 Azure 儲存體中。  

### <a name="hadoop-command-line"></a>Hadoop 命令列

Hadoop 命令列僅適用於當資料已存在於叢集前端節點時，將資料儲存到 Azure 儲存體 blob。

若要使用 Hadoop 命令，您必須先使用 [SSH 或 PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)連接到前端節點。

連線之後，您就可以使用下列語法來將檔案上傳到儲存體。

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

例如， `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

因為 HDInsight 的預設檔案系統位於 Azure 儲存體，所以/example/data/data.txt 實際上在 Azure 儲存體。 您也可以用下列語法來參考此檔案：

`wasbs:///example/data/data.txt`

或

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

如需使用檔案的其他 Hadoop 命令清單，請參閱。 [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> 在 Apache HBase 叢集上，寫入資料時所使用的預設區塊大小是 256 KB。 雖然在使用 HBase API 或 REST API 時此大小可正常運作，但使用 `hadoop` 或 `hdfs dfs` 命令寫入大於 ~12 GB 的資料會導致錯誤。 如需詳細資訊，請參閱 [在 blob 上寫入的儲存體例外](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob)狀況。

### <a name="graphical-clients"></a>圖形化用戶端

其他還有數個應用程式也會提供可搭配 Azure 儲存體使用的圖形化介面。 以下資料表提供數個這類應用程式的清單：

| 用戶端 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure 儲存體總管](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [適用於 Microsoft Azure 的 CloudBerry 總管](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>將 Azure 儲存體掛接為本機磁碟機

請參閱[將 Azure 儲存體掛接為本機磁碟機](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive)。

## <a name="upload-using-services"></a>使用服務上傳

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory 服務是一項完全受控的服務，用來撰寫資料：儲存、處理和移動服務到簡化、可調整且可靠的資料生產管線。

|儲存體類型|文件|
|----|----|
|Azure Blob 儲存體|[使用 Azure Data Factory 將資料複製到 Azure Blob 儲存體或從該處複製資料](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[使用 Azure Data Factory 從 Azure Data Lake Storage Gen1 來回複製資料](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[使用 Azure Data Factory 將資料載入 Azure Data Lake Storage Gen2 中](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop 是一種專門在 Hadoop 和關聯式資料庫之間傳送資料的工具。 您可以使用它，從關係資料庫管理系統匯入資料， (RDBMS) ，例如 SQL Server、MySQL 或 Oracle。 然後進入 Hadoop 分散式檔案系統 (HDFS) 。 使用 MapReduce 或 Hive 轉換 Hadoop 中的資料，然後將資料匯出回 RDBMS。

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

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何將資料匯入 HDInsight，請閱讀下列文章以瞭解分析：

* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [以程式設計方式提交 Apache Hadoop 作業](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)