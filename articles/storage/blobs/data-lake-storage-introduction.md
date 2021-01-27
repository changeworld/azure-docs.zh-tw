---
title: Azure Data Lake Storage Gen2 簡介
description: 閱讀 Azure Data Lake Storage Gen2 簡介。 了解主要功能。 檢閱支援的 Blob 儲存體功能、Azure 服務整合和平台。
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879893"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 簡介

Azure Data Lake Storage Gen2 是一組巨量資料分析的專屬功能，內建於 [Azure Blob 儲存體](storage-blobs-introduction.md)。 

Data Lake Storage Gen2 包含 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) 的功能和 Azure Blob 儲存體。 例如，Data Lake Storage Gen2 會提供檔案系統語法、檔案層級安全性和規模調整。 因為這些功能是建置於 Blob 儲存體之上，所以您也可以取得低成本的分層式儲存體，以及高可用性/災害復原功能。

## <a name="designed-for-enterprise-big-data-analytics"></a>針對企業巨量資料分析所設計

Data Lake Storage Gen2 讓 Azure 儲存體成為在 Azure 上打造企業 Data Lake 的基礎。 Data Lake Storage Gen2 從一開始就設計為服務數 PB 的資訊，同時可以維持數百 GB 的輸送量，可讓您輕鬆地管理大量資料。

Data Lake Storage Gen2 的基礎部分是新增至 Blob 儲存體的[階層命名空間](data-lake-storage-namespace.md)。 階層命名空間會將物件/檔案組織成目錄階層，讓資料存取更有效率。 一般物件存放區命名慣例是在名稱中使用斜線來模仿階層式目錄結構。 Data Lake Storage Gen2 會使這樣的結構成真。 重新命名或刪除目錄等操作會成為目錄中單一不可部分完成的中繼資料作業。 不需要列舉及處理共用目錄名稱前置詞的所有物件。

Data Lake Storage Gen2 建置於 Blob 儲存體，並以下列方式增強效能、管理和安全性：

-   **效能** 經過最佳化，因為您不需要複製或轉換資料作為分析的必要條件。 相較於 Blob 儲存體上的一般命名空間，階層命名空間大幅提高目錄管理作業的效能，從而提高整體作業效能。

-   **管理** 更容易，因為您可以透過目錄和子目錄整理和操作檔案。

-   **安全性** 是強制的，因為您可以在目錄或個人檔案上定義 POSIX 權限。

此外，Azure Data Lake Storage Gen2 非常符合成本效益，因為其建立在低成本的 [Azure Blob 儲存體](storage-blobs-introduction.md)上。 額外功能進一步降低了在 Azure 上執行巨量資料分析的擁有權總成本。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 的主要功能

-   **Hadoop 相容存取**：Data Lake Storage Gen2 可讓您管理及存取資料，就如同使用 [Hadoop 分散式檔案系統 (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 一樣。 新的 [ABFS 驅動程式](data-lake-storage-abfs-driver.md) (用來存取資料) 可在所有 Apache Hadoop 環境中使用。 這些環境包括 [Azure HDInsight](../../hdinsight/index.yml) *、* [Azure Databricks](/azure/databricks/) 及 [Azure Synapse Analytics](../../synapse-analytics/index.yml)。

-   **POSIX 權限的超集合**：Data Lake Gen2 的安全性模型可支援 ACL 和 POSIX 權限，以及一些 Data Lake Storage Gen2 特有的額外細微性。 這些設定可透過儲存體總管或 Hive 和 Spark 這類架構來配置。

-   **成本效益**：Data Lake Storage Gen2 提供低成本儲存體容量和異動功能。 [Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)會在資料於生命週期中進行轉換時將成本最佳化。

-   **最佳化的驅動程式**：ABFS 驅動程式已針對巨量資料分析完成 [特別最佳化](data-lake-storage-abfs-driver.md)。 相應的 REST API 透過端點 `dfs.core.windows.net` 呈現。

### <a name="scalability"></a>延展性

無論您是透過 Data Lake Storage Gen2 或 Blob 儲存體介面存取，Azure 儲存體都可以隨設計調整。 而且能夠儲存和使用 *數 EB 的資料*。 這樣的儲存量可用於在每秒高輸入/輸出作業 (IOPS) 時以每秒 GB (Gbps) 為單位測量的輸送量。 處理作業是在近常數的每個要求延遲時執行的，這些延遲是在服務、帳戶及檔案層級上所測得。

### <a name="cost-effectiveness"></a>符合成本效益

由於 Data Lake Storage Gen2 建置於 Azure Blob 儲存體之上，因此儲存容量和交易成本會較低。 不同於其他雲端儲存體服務，您無須在分析資料之前移動或轉換資料。 如需定價的詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage)。

此外，例如[階層式命名空間](data-lake-storage-namespace.md)等功能可大幅提升許多分析作業的整體效能。 效能提升即表示處理數量相同的資料時，所需的計算能力較少，因此可降低端對端分析工作的擁有權總成本 (TCO)。

### <a name="one-service-multiple-concepts"></a>一項服務，多個概念

由於 Data Lake Storage Gen2 建置於 Azure Blob 儲存體之上，因此許多概念都可以描述相同及共用的項目。

下列是以不同概念描述的對等實體。 除非加以指定，否則這些實體是直接同義：

| 概念                                | 最上層組織 | 較低層級組織                                            | 資料容器 |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – 一般用途物件儲存體 | 容器              | 虛擬目錄 (僅限 SDK – 不提供不可部分完成操作) | Blob           |
| Azure Data Lake Storage Gen2 – 分析儲存體          | 容器            | 目錄                                                           | 檔案           |

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

Blob 儲存體功能 (例如[診斷記錄](../common/storage-analytics-logging.md)、[存取層](storage-blob-storage-tiers.md)和 [Blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md)) 都可供您的帳戶使用。 

如需支援的 Blob 儲存體功能清單，請參閱 [Azure Data Lake Storage Gen2 中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支援的 Azure 服務整合

Data Lake Storage gen2 支援數個 Azure 服務。 您可以使用這些服務來內嵌資料、執行分析，以及建立視覺表示法。 如需支援的 Azure 服務清單，請參閱[支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支援的開放原始碼平台

數個開放原始碼平台支援 Data Lake Storage Gen2。 如需完整清單，請參閱[支援 Azure Data Lake Storage Gen2 的開放原始碼平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)