---
title: HDInsight 中的 Azure Data Lake Storage Gen2 總覽
description: HDInsight 中的 Data Lake Storage Gen2 總覽。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: a7ae1a0f77d0d19fc48695ddc5cc3d3a14d65ab9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82195123"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>HDInsight 中的 Azure Data Lake Storage Gen2 總覽

Azure Data Lake Storage Gen2 採用 Azure Data Lake Storage Gen1 的核心功能，並將這些功能整合到 Azure Blob 儲存體。 這些功能包括與 Hadoop 相容的檔案系統、Azure Active Directory (Azure AD) 和 POSIX 型存取控制清單 (ACL)。 此組合可讓您利用 Azure Data Lake Storage Gen1 的效能。 同時也使用 Blob 儲存體的分層和資料生命週期管理。

如需 Azure Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)。

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的核心功能

* **與 Hadoop 相容的存取權：** 在 Azure Data Lake Storage Gen2 中，您可以管理和存取資料，就如同使用 Hadoop 分散式檔案系統 (HDFS) 一樣。 Azure Blob 檔案系統 (ABFS) 驅動程式可在所有 Apache Hadoop 環境中使用，包括 Azure HDInsight 和 Azure Databricks。 使用 ABFS 存取儲存在 Data Lake Storage Gen2 中的資料。

* **POSIX 許可權的超集合：** Data Lake Gen2 的安全性模型支援 ACL 和 POSIX 許可權，以及 Data Lake Storage Gen2 特定的額外細微性。 這些設定可透過系統管理工具或 Apache Hive 和 Apache Spark 這類架構來配置。

* **成本效益：** Data Lake Storage Gen2 提供低成本的儲存容量和交易。 Azure Blob 儲存體生命週期可在資料于其生命週期內移動時調整計費費率，以協助降低成本。

* **與 Blob 儲存體工具、架構和應用程式的相容性：** Data Lake Storage Gen2 會繼續使用適用于 Blob 儲存體的各種工具、架構和應用程式。

* **優化的驅動程式：** ABFS 驅動程式專門針對大型資料分析進行優化。 對應的 REST API 會透過分散式檔案系統 (DFS) 端點 dfs.core.windows.net 呈現。

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的新功能

### <a name="managed-identities-for-secure-file-access"></a>用於安全存取檔案的受控識別

Azure HDInsight 會使用受控識別來保護叢集對 Azure Data Lake Storage Gen2 中檔案的存取。 受控識別是 Azure Active Directory 的功能，可提供一組自動受控的認證給 Azure 服務。 這些認證可用來向任何支援 Active Directory 驗證的服務進行驗證。 使用受控識別不需要將認證儲存在程式碼或組態檔中。

如需詳細資訊，請參閱[適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-blob-file-system-driver"></a>Azure Blob 檔案系統驅動程式

Apache Hadoop 應用程式原本就預期會從本機磁碟儲存體讀取和寫入資料。 Hadoop 檔案系統驅動程式（例如 ABFS）可讓 Hadoop 應用程式使用雲端存放裝置。 藉由模擬一般 Hadoop 檔案系統作業來運作。 驅動程式會將從應用程式接收的命令，轉換為實際雲端儲存空間平台理解的作業。

先前，Hadoop 檔案系統驅動程式會將所有檔案系統作業轉換成在用戶端 Azure 儲存體 REST API 呼叫。 然後叫用 REST API。 不過，此用戶端轉換會造成單一檔案系統作業 (例如檔案重新命名) 有多個 REST API 呼叫。 ABFS 已將 Hadoop 檔案系統邏輯從用戶端移至伺服器端。 Azure Data Lake Storage Gen2 API 現在會與 Blob API 平行執行。 此移轉可改善效能，因為現在的一般 Hadoop 檔案系統作業可透過一個 REST API 呼叫來執行。

如需詳細資訊，請參閱 [Azure Blob 檔案系統驅動程式 (ABFS) ：適用于 Hadoop 的專用 Azure 儲存體驅動程式](../storage/blobs/data-lake-storage-abfs-driver.md)。

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的 URI 配置

Azure Data Lake Storage Gen2 會使用新的 URI 配置，從 HDInsight 存取 Azure 儲存體中的檔案：

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI 配置會提供 SSL 加密的存取。

`<FILE_SYSTEM_NAME>` 可識別檔案系統 Data Lake Storage Gen2 的路徑。

`<ACCOUNT_NAME>` 可識別 Azure 儲存體帳戶名稱。 需要使用完整網域名稱 (FQDN)。

`<PATH>` 是檔案或目錄 HDFS 路徑名稱。

如果未指定 `<FILE_SYSTEM_NAME>` 和 `<ACCOUNT_NAME>` 的值，則會使用預設檔案系統。 對於預設檔案系統上的檔案，使用相對路徑或絕對路徑。 例如，可使用下列其中一個路徑來參考 HDInsight 叢集隨附的 `hadoop-mapreduce-examples.jar` 檔案：

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> 在 HDInsight 2.1 和 1.6 版叢集中的檔案名稱是 `hadoop-examples.jar`。 當您使用 HDInsight 外部的檔案時，您會發現大部分的公用程式無法辨識 ABFS 格式，但可預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

如需詳細資訊，請參閱[使用 Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)。

## <a name="next-steps"></a>接下來的步驟

* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1 概觀](./overview-data-lake-storage-gen1.md)