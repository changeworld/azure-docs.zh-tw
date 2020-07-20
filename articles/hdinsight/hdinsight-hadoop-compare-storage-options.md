---
title: 比較與 Azure HDInsight 叢集搭配使用的儲存體選項
description: 提供儲存體類型以及它們如何與 Azure HDInsight 一起使用的概觀。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ed93ba937a843618f36bac6e88b15ff77355ca75
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82610695"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比較與 Azure HDInsight 叢集搭配使用的儲存體選項

建立 HDInsight 叢集時，您可以在幾個不同的 Azure 儲存體服務之間進行選擇：

* [Azure 儲存體](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md) \(部分機器翻譯\)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)

本文提供了這些儲存體類型和其獨特功能的概觀。

## <a name="storage-types-and-features"></a>儲存類型和功能

下表摘要說明不同 HDInsight 版本所支援的 Azure 儲存體服務：

| 儲存體服務 | 帳戶類型 | 命名空間類型 | 支援的服務 | 支援的效能層級 | 支援的存取層 | HDInsight 版本 | 叢集類型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 一般用途 V2 | 階層式（filesystem） | Blob | 標準 | 經常性存取層、非經常性存取層、封存 | 3.6+ | 除了 Spark 2.1 和2.2 以外的所有|
|Azure 儲存體| 一般用途 V2 | Object | Blob | 標準 | 經常性存取層、非經常性存取層、封存 | 3.6+ | 全部 |
|Azure 儲存體| 一般用途 V1 | Object | Blob | 標準 | 不適用 | 全部 | 全部 |
|Azure 儲存體| Blob 儲存體 * * | Object | 區塊 Blob | 標準 | 經常性存取層、非經常性存取層、封存 | 全部 | 全部 |
|Azure Data Lake Storage Gen1| 不適用 | 階層式（filesystem） | 不適用 | 不適用 | 不適用 | 僅限3。6 | HBase 以外的所有 |

* * 對於 HDInsight 叢集，只有次要儲存體帳戶可以屬於類型 BlobStorage，而分頁 Blob 則不是支援的儲存體選項。

如需 Azure 儲存體帳戶類型的詳細資訊，請參閱[Azure 儲存體帳戶總覽](../storage/common/storage-account-overview.md)

如需 Azure 儲存體存取層的詳細資訊，請參閱[Azure Blob 儲存體： Premium （預覽）、](../storage/blobs/storage-blob-storage-tiers.md)經常性存取、非經常性存取和封存儲存層

您可以使用適用于主要和選擇性次要儲存體的服務組合來建立叢集。 下表摘要說明 HDInsight 目前支援的叢集儲存體設定：

| HDInsight 版本 | 主要儲存體 | 次要儲存體 | 支援 |
|---|---|---|---|
| 3.6 & 4。0 | 一般用途 V1，一般用途 V2 | 一般用途 V1、一般用途 V2、BlobStorage （區塊 Blob） | 是 |
| 3.6 & 4。0 | 一般用途 V1，一般用途 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 & 4。0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | 是 |
| 3.6 & 4。0 | Data Lake Storage Gen2 * | 一般用途 V1、一般用途 V2、BlobStorage （區塊 Blob） | 是 |
| 3.6 & 4。0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 是 |
| 3.6 | Data Lake Storage Gen1 | 一般用途 V1、一般用途 V2、BlobStorage （區塊 Blob） | 是 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | 任意 | 否 |
| 4.0 | 一般用途 V1，一般用途 V2 | Data Lake Storage Gen1 | 否 |

* = 這可以是一個或多個 Data Lake Storage Gen2 帳戶，只要它們都設定為使用相同的受控識別來進行叢集存取即可。

> [!NOTE]
> Spark 2.1 或2.2 叢集不支援 Data Lake Storage Gen2 主要儲存體。

## <a name="next-steps"></a>後續步驟

* [Azure 儲存體概觀](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen1 概觀](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen2 概觀](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
