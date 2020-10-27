---
title: 從 HDFS 相容的 Azure 儲存體查詢資料 - Azure HDInsight
description: 了解如何從 Azure 儲存體和 Azure Data Lake Storage 查詢資料以儲存分析的結果。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ead9b775b8c61d0d89abd4821bef2b1aaaea0d76
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547430"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Azure 儲存體

您可以將資料儲存在 [Azure Blob 儲存體](../storage/common/storage-introduction.md)、 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)或 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)中。 或是這些選項的組合。 這些儲存體選項都可讓您安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

Apache Hadoop 支援預設檔案系統的概念。 預設檔案系統意指預設配置和授權。 也可用來解析相對路徑。 進行 HDInsight 叢集建立程序期間，您可以指定 Azure 儲存體中的 Blob 容器作為預設檔案系統。 或使用 HDInsight 3.6，您可以選取 Azure Blob 儲存體或 Azure Data Lake Storage Gen1/Azure Data Lake Storage Gen2 作為預設的檔案系統，但有一些例外狀況。 如需使用 Data Lake Storage Gen1 作為預設和連結儲存體的可支援性，請參閱 HDInsight 叢集的 [可用性](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters)。

在本文中，您將了解 Azure 儲存體與 HDInsight 叢集搭配運作的方式。 
* 若要瞭解 Data Lake Storage Gen1 如何與 HDInsight 叢集搭配運作，請參閱搭配 [使用 Azure Data Lake Storage Gen1 與 Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen1.md)叢集。
* 若要瞭解 Data Lake Storage Gen2 如何與 HDInsight 叢集搭配運作，請參閱搭配 [使用 Azure Data Lake Storage Gen2 與 Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen2.md)叢集。
* 如需建立 HDInsight 叢集的詳細資訊，請參閱[在 HDInsight 中建立 Apache Hadoop 叢集](./hdinsight-hadoop-provision-linux-clusters.md)。

> [!IMPORTANT]  
> 儲存體帳戶種類 **BlobStorage** 只能作為 HDInsight 叢集的次要儲存體。

| 儲存體帳戶種類 | 支援的服務 | 支援的效能層級 |不支援的效能層級| 支援的存取層 |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (一般用途 v2)  | Blob     | 標準                    |Premium| 經常性存取層、非經常性存取層、封存\*   |
| 儲存體 (一般用途 v1)   | Blob     | 標準                    |Premium| N/A                    |
| BlobStorage                    | Blob     | 標準                    |Premium| 經常性存取層、非經常性存取層、封存\*   |

不建議您使用預設的 Blob 容器來儲存商務資料。 最好在每次使用後刪除預設的 Blob 容器，以減少儲存成本。 預設容器包含應用程式與系統記錄。 請務必先擷取記錄再刪除容器。

不支援多個叢集共用一個 Blob 容器作為預設檔案系統。

> [!NOTE]  
> 「封存」存取層是一個有數小時擷取延遲的離線層，不建議與 HDInsight 搭配使用。 如需詳細資訊，請參閱[封存存取層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="access-files-from-within-cluster"></a>從叢集存取檔案

有數種方式可讓您從 HDInsight 叢集存取 Data Lake Storage 中的檔案。 URI 配置提供未加密存取 (使用 *wasb:* 首碼) 和 TLS 加密存取 (使用 *wasbs* )。 建議盡可能使用 wasbs  ，即使存取 Azure 中相同區域內的資料也一樣。

* **使用完整格式名稱** 。 使用這種方法，您可以針對想要存取的檔案提供完整路徑。

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **使用簡短路徑格式** 。 使用這種方法，您可以利用以下方式取代到叢集根目錄的路徑：

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **使用相對路徑** 。 使用這種方法，您可以針對想要存取的檔案，只提供相對路徑。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>資料存取範例

範例是以叢集前端節點的 [ssh 連線](./hdinsight-hadoop-linux-use-ssh-unix.md)為基礎。 這些範例會使用這三個 URI 配置。 請將 `CONTAINERNAME` 和 `STORAGEACCOUNT` 取代為相關值

#### <a name="a-few-hdfs-commands"></a>一些 hdfs 命令

1. 在本機儲存體上建立檔案。

    ```bash
    touch testFile.txt
    ```

1. 在叢集儲存體上建立目錄。

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 將資料從本機儲存體複製到叢集儲存體。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 列出叢集儲存體上的目錄內容。

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> 在 HDInsight 外部使用 Blob 時，大部分的公用程式無法辨識 WASB 格式而改為預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

#### <a name="creating-a-hive-table"></a>建立 Hive 資料表

顯示三個檔案位置以供說明之用。 若為實際執行，請僅使用其中一個 `LOCATION` 項目。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>從外部叢集存取檔案

Microsoft 提供下列工具來使用 Azure 儲存體：

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>識別來自 Ambari 的儲存體路徑

* 若要識別已設定之預設存放區的完整路徑，請瀏覽至：

    **HDFS** > **Configs** ，並在篩選輸入方塊中輸入 `fs.defaultFS`。

* 若要檢查 wasb 儲存體是否設定為次要儲存體，請瀏覽至：

    **HDFS** > **Configs** ，並在篩選輸入方塊中輸入 `blob.core.windows.net`。

若要使用 Ambari REST API 取得路徑，請參閱[取得預設儲存體](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)。

## <a name="blob-containers"></a>Blob 容器

若要使用 Blob，您必須先建立 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)。 在這個步驟中，您可以指定建立儲存體帳戶所在的 Azure 區域。 叢集與儲存體帳戶必須在相同區域內託管。 Hive 中繼存放區 SQL Server 資料庫和 Apache Oozie 中繼存放區 SQL Server 資料庫必須位在相同的區域內。

您所建立的每個 Blob 不論位於何處，都屬於 Azure 儲存體帳戶中的某個容器。 此容器可能是在 HDInsight 外部建立的現有 blob。 或者，其可能是針對 HDInsight 叢集所建立的容器。

預設 Blob 容器會儲存叢集特定資訊，例如作業歷程記錄和記錄。 不要與多個 HDInsight 叢集共用預設 Blob 容器。 此動作可能會損毀作業歷程記錄。 建議您為每個叢集使用不同的容器。 將共用資料放在為所有相關叢集指定的連結儲存體帳戶上，而不是預設儲存體帳戶上。 如需如何設定連結儲存體帳戶的詳細資訊，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。 不過，在刪除原始的 HDInsight 叢集後，您可以重複使用預設儲存容器。 對於 HBase 叢集，您可以利用已刪除的 HBase 叢集所使用的預設 Blob 容器來建立一個新的 HBase 叢集，藉此實際保留 HBase 資料表結構描述和資料

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>使用其他儲存體帳戶

在建立 HDInsight 叢集時，您會指定要與它相關聯的 Azure 儲存體帳戶。 此外，您也可以在建立程序中或在叢集建立後，從相同或不同的 Azure 訂用帳戶中新增其他儲存體帳戶。 如需關於新增其他儲存體帳戶的指示，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用其他儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何搭配 HDInsight 使用 HDFS 相容的 Azure 儲存體。 此儲存體可讓您建置可調整、長期封存的資料取得解決方案，並利用 HDInsight 來將儲存的結構化和非結構化資料內的資訊解除鎖定。

如需詳細資訊，請參閱

* [快速入門：建立 Apache Hadoop 叢集](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [教學課程：建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [將資料上傳至 HDInsight](hdinsight-upload-data.md)
* [教學課程：使用 Azure HDInsight 上的互動式查詢來擷取、轉換和載入資料](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [使用 Azure 儲存體共用存取簽章來限制使用 HDInsight 對資料的存取](hdinsight-storage-sharedaccesssignature-permissions.md)