---
title: 使用 DistCp 將資料從 WASB 複製到 Azure Data Lake Storage Gen1
description: 您可以使用 DistCp 工具，將資料複製到 Azure 儲存體 blob，並將其從 Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: c608f357eb1eff9fd36e583b98d26250a71cb923
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515685"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>使用 DistCp 在 Azure 儲存體 blob 和 Azure Data Lake Storage Gen1 之間複製資料

> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

如果您有可存取 Azure Data Lake Storage Gen1 的 HDInsight 叢集，您可以使用 Hadoop 生態系統工具（例如 DistCp），將資料複製到 HDInsight 叢集儲存體（WASB），並將其複製到 Data Lake Storage Gen1 帳戶。 本文說明如何使用 DistCp 工具。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 如需有關如何建立帳戶的指示，請參閱[開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)。
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>使用來自 HDInsight Linux 叢集的 DistCp

HDInsight 叢集隨附 DistCp 工具，可用來將不同來源的資料複製到 HDInsight 叢集中。 如果您已將 HDInsight 叢集設定為使用 Data Lake Storage Gen1 做為額外的儲存體，您可以使用現成可用的 DistCp，將資料複製到 Data Lake Storage Gen1 帳戶，或從中複製資料。 在本節中，我們將探討如何使用 DistCp 工具。

1. 從您的桌上型電腦，使用 SSH 連線到叢集。 請參閱 [連線至以 Linux 為基礎的 HDInsight 叢集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。 從 SSH 提示字元執行命令。

1. 確認您是否可以存取 Azure 儲存體的 blob （WASB）。 執行下列命令：

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   輸出會提供儲存體 blob 中的內容清單。

1. 同樣地，請確認您是否可從叢集存取 Data Lake Storage Gen1 帳戶。 執行下列命令：

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    輸出會提供 Data Lake Storage Gen1 帳戶中的檔案和資料夾清單。

1. 使用 DistCp 將資料從 WASB 複製到 Data Lake Storage Gen1 帳戶。

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    命令會將 WASB 中的 **/example/data/gutenberg/** 資料夾的內容複製到 Data Lake Storage Gen1 帳戶中的 **/myfolder**。

1. 同樣地，請使用 DistCp 將資料從 Data Lake Storage Gen1 帳戶複製到 WASB。

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    命令會將 Data Lake Storage Gen1 帳戶中的 **/myfolder**內容複寫到 WASB 中的 **/example/data/gutenberg/** 資料夾。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 時的效能考量

由於 DistCp 工具的最低資料細微性是單一檔案，因此設定同步複製的數目上限是最重要的參數，可將它優化以 Data Lake Storage Gen1。 您可以在命令列上設定對應程式（' m '）參數數目，以控制同時複製的數目。 這個參數指定用來複製資料的對應程式數目上限。 預設值為 20。

範例：

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>如何判斷要使用的對應程式數目

以下是一些您可以使用的指引。

* **步驟 1︰判斷 YARN 記憶體總計** - 第一個步驟是判斷您執行 DistCp 作業的叢集可用的 YARN 記憶體。 您可以在與叢集相關聯的 Ambari 入口網站中取得這項資訊。 流覽至 YARN，並查看 [**配置] 索引**標籤以查看 YARN 記憶體。 若要計算 YARN 記憶體總計，請將每個節點的 YARN 記憶體乘以您在叢集中的節點數目。

* **步驟 2：計算對應程式數目** - **m** 的值等於 YARN 記憶體總計除以 YARN 容器大小的商數。 YARN 容器大小資訊也可在 Ambari 入口網站中取得。 流覽至**YARN，並查看 [[** ]] 索引標籤。YARN 容器大小會顯示在此視窗中。 要抵達對應程式（**m**）數目的方程式是：

   `m = (number of nodes * YARN memory for each node) / YARN container size`

範例：

假設您在叢集中有四個個 d14v2s 節點，而且您想要從10個不同的資料夾傳輸 10 TB 的資料。 每個資料夾包含不同的資料量，且每個資料夾內的檔案大小都不同。

* YARN 記憶體總計 - 從 Ambari 入口網站，您可以判斷 D14 節點的 YARN 記憶體是 96 GB。 因此，四節點叢集的 YARN 記憶體總計為︰

   `YARN memory = 4 * 96GB = 384GB`

* 對應程式數目 - 從 Ambari 入口網站，您可以判斷 D14 叢集節點的 YARN 容器大小是 3072。 因此，對應程式的數目為：

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

如果有其他應用程式正在使用記憶體，您可以選擇只使用部分叢集的 YARN 記憶體來進行 DistCp。

### <a name="copying-large-datasets"></a>複製大型資料集

如果要移動的資料集大小很大（例如 > 1 TB），或如果您有許多不同的資料夾，請考慮使用多個 DistCp 作業。 可能沒有效能提升，但它會散佈作業，因此如果有任何作業失敗，您只需要重新開機該特定作業，而不是整個作業。

### <a name="limitations"></a>限制

* DistCp 會嘗試建立較小的對應程式，以最佳化效能。 增加對應程式數目不見得會提高效能。

* DistCp 受限於每個檔案只有一個對應程式。 因此，您的對應程式應該不會超過您擁有的檔案。 因為 DistCp 只能將一個對應程式指派給一個檔案，所以這會限制可用於複製大型檔案的平行存取量。

* 如果您有少量的大型檔案，請將它們分割成 256 MB 的檔案區塊，以提供更多潛在的並行處理。

* 如果您是從 Azure Blob 儲存體帳戶複製，您的複製作業可能會在 Blob 儲存體端進行節流。 這會降低複製作業的效能。 若要深入瞭解 Azure Blob 儲存體的限制，請參閱[azure 訂用帳戶和服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md)的 Azure 儲存體限制。

## <a name="see-also"></a>另請參閱

* [將資料從 Azure 儲存體 blob 複製到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Storage Gen1 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
