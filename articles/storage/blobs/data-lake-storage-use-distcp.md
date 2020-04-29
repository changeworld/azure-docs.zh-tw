---
title: 使用 DistCp 將資料複製到 Azure Data Lake Storage Gen2 | Microsoft Docs
description: 使用 DistCp 工具將資料複製到 Data Lake Storage Gen2 或從中複製資料
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255531"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>使用 DistCp 在 Azure 儲存體 Blob 與 Azure Data Lake Storage Gen2 之間複製資料

您可以使用 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)，在一般用途 V2 儲存體帳戶和已啟用階層命名空間的一般用途 V2 儲存體帳戶之間複製資料。 本文提供如何使用 DistCp 工具的相關指示。

DistCp 提供各種不同的命令列參數，我們強烈建議您閱讀這篇文章，以最佳化此工具的使用。 本文會說明基本功能，同時將焦點放在如何使用此工具將資料複製到已啟用階層命名空間的帳戶。

## <a name="prerequisites"></a>先決條件

* **Azure 訂**用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Data Lake Storage Gen2 功能 (階層命名空間) 的現有 Azure 儲存體帳戶**。
* **啟用 Data Lake Storage Gen2 功能的 Azure 儲存體帳戶**。 如需如何建立的指示，請參閱[建立 Azure Data Lake Storage Gen2 儲存體帳戶](data-lake-storage-quickstart-create-account.md)
* 已在儲存體帳戶 (已啟用階層命名空間) 中建立的**檔案系統**。
* 可存取已啟用 Data Lake Storage Gen2 之儲存體帳戶的 **Azure HDInsight 叢集**。 請參閱[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 請確實為叢集啟用遠端桌面。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>使用來自 HDInsight Linux 叢集的 DistCp

HDInsight 叢集隨附 DistCp 公用程式，可用來將不同來源的資料複製到 HDInsight 叢集。 如果您已將 HDInsight 叢集設定為同時使用 Azure Blob 儲存體和 Azure Data Lake Storage，則也可以使用現成可用的 DistCp 公用程式，在兩者之間複製資料。 在本節中，我們將討論如何使用 DistCp 公用程式。

1. 建立 HDI 叢集的 SSH 工作階段。 請參閱 [連線至以 Linux 為基礎的 HDInsight 叢集](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 確認您是否可以存取現有的一般用途 V2 帳戶 (未啟用階層命名空間)。

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    輸出應會提供容器中的內容清單。

3. 同樣地，請確認您是否可以從叢集存取已啟用階層命名空間的儲存體帳戶。 執行下列命令：

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    輸出應會提供 Data Lake Storage 帳戶中的檔案/資料夾清單。

4. 使用 DistCp 將資料從 WASB 複製到 Data Lake Storage 帳戶。

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    命令會將 Blob 儲存體中的 **/example/data/gutenberg/** 資料夾內容複製到 Data Lake Storage 帳戶中的 **/myfolder**。

5. 同樣地，請使用 DistCp 將資料從 Data Lake Storage 帳戶複製到 Blob 儲存體 (WASB)。

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    命令會將 Data Lake Store 帳戶中的 **/myfolder** 的內容複製到 WASB 中的 **/example/data/gutenberg/** 資料夾。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 時的效能考量

因為 DistCp 以單一檔案為最低資料粒度，若要針對 Data Lake Storage 而達到最佳化，設定同步複本數目上限是最重要的參數。 同步複本數目會等於命令列上的對應程式 (**m**) 參數數目。 這個參數指定用來複製資料的對應程式數目上限。 預設值為 20。

**範例**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>如何決定要使用的對應程式數目？

以下是一些您可以使用的指引。

* **步驟1：判斷可供「預設」 YARN 應用程式佇列使用的記憶體總計**-第一個步驟是判斷可供「預設」 YARN 應用程式佇列使用的記憶體。 您可以在與叢集相關聯的 Ambari 入口網站中取得這項資訊。 瀏覽至 YARN，檢視 [設定] 索引標籤以查看可供「預設」應用程式佇列使用的 YARN 記憶體。 這是您 DistCp 作業 (也就是實際的 MapReduce 作業) 可用的記憶體總計。

* **步驟 2︰計算對應程式數目** - **m** 的值等於 YARN 記憶體總計除以 YARN 容器大小的商數。 Ambari 入口網站中也提供 YARN 容器大小的資訊。 流覽至 YARN，並查看 [[]] 索引標籤。YARN 容器大小會顯示在此視窗中。 計算對應程式數目 (**m**) 的方程式是

        m = (number of nodes * YARN memory for each node) / YARN container size

**範例**

假設您有 4 個 D14v2s 叢集，且嘗試從 10 個不同的資料夾傳輸 10 TB 的資料。 每個資料夾包含不同的資料量，且每個資料夾內的檔案大小都不同。

* **YARN 記憶體總計**：您可以從 Ambari 入口網站判斷 D14 節點的 YARN 記憶體是 96 GB。 因此，四節點叢集的 YARN 記憶體總計為︰ 

        YARN memory = 4 * 96GB = 384GB

* **對應程式數目**：從 Ambari 入口網站，您可以判斷 D14 叢集節點的 YARN 容器大小為 3072 MB。 因此，對應程式數目為︰

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

如果有其他應用程式在使用記憶體，您可以選擇讓 DistCp 只使用叢集的一部分 YARN 記憶體。

### <a name="copying-large-datasets"></a>複製大型資料集

如果要移動的資料集很大 (例如 > 1 TB) 或您有許多不同的資料夾，請考慮使用多個 DistCp 作業。 雖然可能不會提高效能，但可分散作業，如果有任何作業失敗，您只需要重新啟動該特定的作業，而不是整個作業。

### <a name="limitations"></a>限制

* DistCp 會嘗試建立較小的對應程式，以最佳化效能。 增加對應程式數目不見得會提高效能。

* DistCp 受限於每個檔案只有一個對應程式。 因此，對應程式數目不應該超過您擁有的檔案數目。 因為 DistCp 只能將一個對應程式指派給一個檔案，這會限制可用於複製大量檔案的並行程度。

* 如果您的大型檔案很少，則應該將它們分割成 256 MB 的檔案區塊，以提高並行潛力。
