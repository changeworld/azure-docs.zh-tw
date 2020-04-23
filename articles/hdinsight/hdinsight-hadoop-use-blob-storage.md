---
title: 從 HDFS 相容的 Azure 儲存體查詢資料 - Azure HDInsight
description: 了解如何從 Azure 儲存體和 Azure Data Lake Storage 查詢資料以儲存分析的結果。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: cd6ba50cf81b93da887134e89d75313acb6bd936
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869867"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Azure 儲存體

您可以將資料儲存在[Azure 儲存](../storage/common/storage-introduction.md)[、Azure 資料湖儲存第 1 代](../data-lake-store/data-lake-store-overview.md)或 Azure[資料儲存湖儲存第 2 代](../storage/blobs/data-lake-storage-introduction.md)中。 或這些選項的組合。 這些儲存選項使您能夠安全地刪除用於計算的 HDInsight 叢集,而不會丟失用戶數據。

Apache Hadoop 支援預設檔案系統的概念。 預設檔案系統意指預設配置和授權。 也可用來解析相對路徑。 在 HDInsight 叢集建立過程中,可以將 Azure 儲存中的 Blob 容器指定為預設檔案系統。 或者使用 HDInsight 3.6,您可以選擇 Azure 儲存或 Azure 資料儲存第 1 代/Azure 資料儲存第 2 代作為預設檔案系統,但有一些例外。 如需了解使用 Data Lake Storage Gen 1 作為預設及連結儲存體的支援能力，請參閱 [HDInsight 叢集的可用性](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)。

在本文中，您將了解 Azure 儲存體與 HDInsight 叢集搭配運作的方式。 若要深入了解 Data Lake Storage Gen 1 與 HDInsight 叢集搭配運作的方式，請參閱[使用 Azure Data Lake Storage 搭配 Azure HDInsight 叢集](hdinsight-hadoop-use-data-lake-store.md)。 如需建立 HDInsight 叢集的詳細資訊，請參閱[在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!IMPORTANT]  
> 儲存帳戶類型**BlobStorage**只能用作 HDInsight 叢集的輔助存儲。

| 儲存帳戶類型 | 支援的服務 | 支援的效能層級 |不支援的效能層| 支援的存取層 |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (一般用途 v2)  | Blob     | 標準                    |Premium| 熱, 酷, 存檔\*   |
| 儲存 (一般 v1)   | Blob     | 標準                    |Premium| N/A                    |
| BlobStorage                    | Blob     | 標準                    |Premium| 熱, 酷, 存檔\*   |

不建議您使用預設的 Blob 容器來儲存商務資料。 最好在每次使用後刪除預設的 Blob 容器，以減少儲存成本。 預設容器包含應用程式與系統記錄。 請務必先擷取記錄再刪除容器。

不支援將一個 Blob 容器共用為多個群集的預設檔案系統。

> [!NOTE]  
> 存檔存取層是具有幾個小時檢索延遲的脫機層,不建議與 HDInsight 一起使用。 如需詳細資訊，請參閱[封存存取層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="access-files-from-within-cluster"></a>從群組集中存取檔案

有數種方式可讓您從 HDInsight 叢集存取 Data Lake Storage 中的檔案。 URI 方案提供未加密的訪問(帶*wasb:* 首碼)和 TLS 加密存取(帶*wasb)。* 建議盡可能使用 wasbs ** ，即使存取 Azure 中相同區域內的資料也一樣。

* **使用完整格式名稱**。 使用這種方法，您可以針對想要存取的檔案提供完整路徑。

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **使用簡短路徑格式**。 使用此方法,您將叢集根的路徑取代為:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **使用相對路徑**。 使用這種方法，您可以針對想要存取的檔案，只提供相對路徑。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>資料存取範例

範例以與叢集頭節點的[ssh 連線](./hdinsight-hadoop-linux-use-ssh-unix.md)。 這些範例使用所有三個URI方案。 `STORAGEACCOUNT`取代為`CONTAINERNAME`相關值

#### <a name="a-few-hdfs-commands"></a>幾個 hdfs 命令

1. 在本地存儲上創建檔。

    ```bash
    touch testFile.txt
    ```

1. 在群集儲存上創建目錄。

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 將數據從本地存儲複製到群集存儲。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 在群集存儲上列出目錄內容。

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> 在 HDInsight 外部使用 Blob 時，大部分的公用程式無法辨識 WASB 格式而改為預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

#### <a name="creating-a-hive-table"></a>建立蜂巢表

顯示三個檔位置以進行說明。 對於實際執行,僅使用其中一個`LOCATION`條目。

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

## <a name="access-files-from-outside-cluster"></a>從外部群組存取檔案

Microsoft 提供以下工具來使用 Azure 儲存:

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [阿茲比貝](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>識別來自安巴里的儲存路徑

* 要識別設定的預設儲存的完整路徑,請瀏覽到:

    **HDFS** > **配置**並`fs.defaultFS`輸入 篩選器輸入框。

* 要檢查 wasb 儲存是否設定為輔助存儲,請瀏覽到:

    **HDFS** > **配置**並`blob.core.windows.net`輸入 篩選器輸入框。

要使用 Ambari REST API 取得路徑,請參考[預設儲存](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)。

## <a name="blob-containers"></a>Blob 容器

要使用 blob,請先建立[Azure 儲存帳戶](../storage/common/storage-create-storage-account.md)。 作為此步驟的一部分,您可以指定創建存儲帳戶的 Azure 區域。 叢集與儲存體帳戶必須在相同區域內託管。 Hive 儲存儲存 SQL Server 資料庫和 Apache Oozie 儲存儲存 SQL Server 資料庫必須位於同一區域。

您所建立的每個 Blob 不論位於何處，都屬於 Azure 儲存體帳戶中的某個容器。 此容器可能是 HDInsight 之外創建的現有 Blob。 或者,它可能是為 HDInsight 群集創建的容器。

預設 Blob 容器會儲存叢集特定資訊，例如作業歷程記錄和記錄。 不要與多個 HDInsight 叢集共用預設 Blob 容器。 此操作可能會損壞作業歷史記錄。 建議為每個群集使用不同的容器。 將共享數據放在為所有相關群集指定的連結存儲帳戶上,而不是預設存儲帳戶上。 如需如何設定連結儲存體帳戶的詳細資訊，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。 不過，在刪除原始的 HDInsight 叢集後，您可以重複使用預設儲存容器。 對於 HBase 叢集,實際上可以透過使用已刪除的 HBase 叢集使用的預設 Blob 容器建立新的 HBase 叢集來保留 HBase 表架構和資料

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>使用其他儲存體帳戶

在建立 HDInsight 叢集時，您會指定要與它相關聯的 Azure 儲存體帳戶。 此外,您可以在創建過程中從同一 Azure 訂閱或不同的 Azure 訂閱中添加其他儲存帳戶。 或在創建群集後。 如需關於新增其他儲存體帳戶的指示，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用其他儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何搭配 HDInsight 使用 HDFS 相容的 Azure 儲存體。 此存儲允許您構建適應性強、長期的存檔數據採集解決方案,並使用 HDInsight 解鎖儲存的結構化和非結構化資料中的資訊。

如需詳細資訊，請參閱

* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [開始使用 Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [將資料上傳到 HDInsight](hdinsight-upload-data.md)
* [使用 Azure 儲存體共用存取簽章來限制使用 HDInsight 對資料的存取](hdinsight-storage-sharedaccesssignature-permissions.md)
* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [教學課程：使用 Azure HDInsight 上的互動式查詢來擷取、轉換和載入資料](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
