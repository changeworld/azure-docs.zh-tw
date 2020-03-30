---
title: 使用 DistCp 將資料從 WASB 複製到 Azure 資料湖存儲第 1 代
description: 使用 DistCp 工具將資料從 Azure 存儲 Blob 複製到 Azure 資料湖存儲第 1 代
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638828"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>使用 DistCp 在 Azure 存儲 Blob 和 Azure 資料存儲單元 1 之間複製資料

> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

如果您有一個有權訪問 Azure 資料湖存儲第 1 代的 HDInsight 群集，則可以使用 Hadoop 生態系統工具（DistCp）將資料從 HDInsight 群集存儲 （WASB） 複製到資料存儲庫第 1 代帳戶中。 本文演示如何使用 DistCp 工具。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 如需有關如何建立帳戶的指示，請參閱[開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)。
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>使用來自 HDInsight Linux 叢集的 DistCp

HDInsight 群集附帶 DistCp 工具，該工具可用於將來自不同源的資料複製到 HDInsight 群集中。 如果將 HDInsight 群集配置為使用 Data Lake 存儲 Gen1 作為附加存儲，則可以使用 DistCp 開箱即用來將資料複製到資料湖存儲 Gen1 帳戶中。 在本節中，我們將介紹如何使用 DistCp 工具。

1. 從您的桌上型電腦，使用 SSH 連線到叢集。 請參閱 [連線至以 Linux 為基礎的 HDInsight 叢集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。 從 SSH 提示字元執行命令。

1. 驗證是否可以訪問 Azure 存儲 Blob （WASB）。 執行以下命令：

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   輸出提供存儲 Blob 中的內容清單。

1. 同樣地，請確認您是否可從叢集存取 Data Lake Storage Gen1 帳戶。 執行以下命令：

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    輸出提供資料存儲庫第 1 代帳戶中的檔和資料夾的清單。

1. 使用 DistCp 將資料從 WASB 複製到資料湖存儲第 1 代帳戶。

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    命令會將 WASB 中的 **/example/data/gutenberg/** 資料夾的內容複製到 Data Lake Storage Gen1 帳戶中的 **/myfolder**。

1. 同樣，使用 DistCp 將資料從資料存儲第 1 代帳戶複製到 WASB。

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    該命令將資料存儲庫第 1 代帳戶中的 **/myfolder**的內容複寫到 WASB 中的 **/示例/資料/古騰堡/** 資料夾。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 時的效能考量

由於 DistCp 工具的細微性最低是單個檔，因此設置最大同時副本數是針對 Data Lake 存儲 Gen1 對其進行優化的最重要參數。 您可以通過在命令列上設置映射器 （'m'） 參數的數量來控制同時複製的數量。 這個參數指定用來複製資料的對應程式數目上限。 預設值為 20。

範例：

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>如何確定要使用的映射器數量

以下是一些您可以使用的指引。

* **步驟 1︰判斷 YARN 記憶體總計** - 第一個步驟是判斷您執行 DistCp 作業的叢集可用的 YARN 記憶體。 您可以在與叢集相關聯的 Ambari 入口網站中取得這項資訊。 導航到 YARN 並查看**Configs**選項卡以查看 YARN 記憶體。 若要計算 YARN 記憶體總計，請將每個節點的 YARN 記憶體乘以您在叢集中的節點數目。

* **步驟 2︰計算對應程式數目** - **m** 的值等於 YARN 記憶體總計除以 YARN 容器大小的商數。 YARN 容器大小資訊也可在 Ambari 門戶中提供。 導航到 YARN 並查看 **"配置"** 選項卡。YARN 容器大小顯示在此視窗中。 到達映射器數 （**m**） 的方程是：

   `m = (number of nodes * YARN memory for each node) / YARN container size`

範例：

假設您的群集中有 4 個 D14v2s 節點，並且希望從 10 個不同的資料夾中傳輸 10 TB 的資料。 每個資料夾包含不同的資料量，且每個資料夾內的檔案大小都不同。

* YARN 記憶體總計 - 從 Ambari 入口網站，您可以判斷 D14 節點的 YARN 記憶體是 96 GB。 因此，四節點叢集的 YARN 記憶體總計為︰

   `YARN memory = 4 * 96GB = 384GB`

* 對應程式數目 - 從 Ambari 入口網站，您可以判斷 D14 叢集節點的 YARN 容器大小是 3072。 因此，映射器的數量是：

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

如果其他應用程式正在使用記憶體，您可以選擇僅將群集的 YARN 記憶體的一部分用於 DistCp。

### <a name="copying-large-datasets"></a>複製大型資料集

當要移動的資料集大小較大（例如，> 1 TB）或有多個不同的資料夾時，請考慮使用多個 DistCp 作業。 可能沒有性能提升，但它分散了作業，因此，如果任何作業失敗，只需重新開機該特定作業，而不是整個作業。

### <a name="limitations"></a>限制

* DistCp 會嘗試建立較小的對應程式，以最佳化效能。 增加對應程式數目不見得會提高效能。

* DistCp 受限於每個檔案只有一個對應程式。 因此，不應擁有比檔更多的映射器。 由於 DistCp 只能向檔分配一個映射器，因此這限制了可用於複製大型檔的併發量。

* 如果您有少量大型檔，請將它們拆分為 256 MB 檔塊，以便為您提供更多潛在的併發性。

* 如果要從 Azure Blob 存儲帳戶複製，則複製作業可能會在 Blob 存儲端受到限制。 這會降低複製作業的效能。 要瞭解有關 Azure Blob 存儲限制的更多情況，請參閱 Azure[訂閱和服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md)處的 Azure 存儲限制。

## <a name="see-also"></a>另請參閱

* [將資料從 Azure 存儲 blob 複製到資料湖存儲第 1 代](data-lake-store-copy-data-azure-storage-blob.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Storage Gen1 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
