---
title: 比較與 Azure HDInsight 叢集搭配使用的儲存體選項
description: 提供儲存體類型以及它們如何與 Azure HDInsight 一起使用的概觀。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869849"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比較與 Azure HDInsight 叢集搭配使用的儲存體選項

建立 HDInsight 叢集時,可以在幾個不同的 Azure 儲存服務之間進行選擇:

* [Azure 儲存體](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md) \(部分機器翻譯\)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

本文提供了這些儲存體類型和其獨特功能的概觀。

## <a name="storage-types-and-features"></a>儲存類型和功能

下表總結了不同版本的 HDInsight 支援的 Azure 儲存服務:

| 儲存服務 | 帳戶類型 | 命名空間類型 | 支援的服務 | 支援的效能層級 | 支援的存取層 | HDInsight 版本 | 叢集類型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 一般用途 V2 | 階層(檔案系統) | Blob | 標準 | 熱, 酷, 存檔 | 3.6+ | 除火花 2.1 和 2.2 外,所有|
|Azure 儲存體| 一般用途 V2 | Object | Blob | 標準 | 熱, 酷, 存檔 | 3.6+ | 全部 |
|Azure 儲存體| 一般用途 V1 | Object | Blob | 標準 | N/A | 全部 | 全部 |
|Azure 儲存體| Blob 儲存 | Object | 區塊 Blob | 標準 | 熱, 酷, 存檔 | 全部 | 全部 |
|Azure Data Lake Storage Gen1| N/A | 階層(檔案系統) | N/A | N/A | N/A | 3.6 僅 | 除 HBase 外 |

*對於 HDInsight 群集,只有輔助儲存帳戶的類型可以是 Blob 儲存,頁面 Blob 不是受支援的儲存選項。

有關 Azure 儲存帳戶類型的詳細資訊,請參閱[Azure 儲存帳戶概述](../storage/common/storage-account-overview.md)

有關 Azure 儲存存取層的詳細資訊,請參閱[Azure Blob 儲存:進階(預覽)、熱存儲、酷和存檔儲存層](../storage/blobs/storage-blob-storage-tiers.md)

您可以使用主儲存和可選輔助儲存的服務組合創建群集。 下表總結了 HDInsight 中目前支援的群集儲存配置:

| HDInsight 版本 | 主要儲存 | 儲存記憶體 | 支援 |
|---|---|---|---|
| 3.6 & 4.0 | 通用 V1,通用 V2 | 通用 V1、一般 V2、Blob 儲存(區塊來檢視) | 是 |
| 3.6 & 4.0 | 通用 V1,通用 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 & 4.0 | 資料儲存第二代* | Data Lake Storage Gen2 | 是 |
| 3.6 & 4.0 | 資料儲存第二代* | 通用 V1、一般 V2、Blob 儲存(區塊來檢視) | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 是 |
| 3.6 | Data Lake Storage Gen1 | 通用 V1、一般 V2、Blob 儲存(區塊來檢視) | 是 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | 任意 | 否 |
| 4.0 | 通用 V1,通用 V2 | Data Lake Storage Gen1 | 否 |

*這可能是一個或多個數據存儲 Gen2 帳戶,只要它們都設置為使用相同的託管標識進行群集訪問。

> [!NOTE]
> Spark 2.1 或 2.2 群集不支援數據存儲湖存儲 Gen2 主存儲。

## <a name="next-steps"></a>後續步驟

* [Azure 儲存概述](./overview-azure-storage.md)
* [Azure 資料儲存第 1 代概述](./overview-data-lake-storage-gen1.md)
* [Azure 資料儲存第 2 代概述](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
