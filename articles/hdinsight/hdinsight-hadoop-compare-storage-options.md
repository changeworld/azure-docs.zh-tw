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
ms.openlocfilehash: ce1c6bdfb38e37c18a18cf970d2dd08683967da3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536743"
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
|Azure 儲存體| 一般用途 V1 | Object | Blob | 標準 | N/A | 全部 | 全部 |
|Azure 儲存體| Blob 儲存體 * * | Object | 區塊 Blob | 標準 | 經常性存取層、非經常性存取層、封存 | 全部 | 全部 |
|Azure Data Lake Storage Gen1| N/A | 階層式（filesystem） | N/A | N/A | N/A | 僅限3。6 | HBase 以外的所有 |

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

## <a name="data-replication"></a>資料複寫

Azure HDInsight 不會儲存客戶資料。 叢集的主要儲存體方式是其相關聯的儲存體帳戶。 您可以將叢集連結至現有的儲存體帳戶，或在叢集建立程式期間建立新的儲存體帳戶。 如果建立新的帳戶，則會將它建立為本機的多餘儲存體（LRS）帳戶，並滿足區域內資料的常駐需求，包括在 [[信任中心](https://azuredatacentermap.azurewebsites.net)] 中指定的要求。

您可以確認 HDInsight 已正確設定，以便將資料儲存在單一區域中，方法是確保與 HDInsight 相關聯的儲存體帳戶 LRS，或[信任中心](https://azuredatacentermap.azurewebsites.net)所提及的另一個儲存體選項。
 
## <a name="next-steps"></a>後續步驟

* [Azure 儲存體概觀](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen1 概觀](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen2 概觀](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
