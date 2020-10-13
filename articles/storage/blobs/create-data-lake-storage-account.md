---
title: 建立 Azure Data Lake Storage Gen2 的儲存體帳戶
description: 瞭解如何建立與 Azure Data Lake Storage Gen2 搭配使用的儲存體帳戶。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6b63933fc625079bb490942cf3a32232a484fe38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89270075"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>建立要與 Azure Data Lake Storage Gen2 搭配使用的儲存體帳戶

若要使用 Data Lake Storage Gen2 功能，請建立具有階層命名空間的儲存體帳戶。

## <a name="choose-a-storage-account-type"></a>選擇儲存體帳戶類型

下列類型的儲存體帳戶支援 Data Lake Storage 功能：

- 一般用途 v2
- BlockBlobStorage

如需如何在兩者之間進行選擇的詳細資訊，請參閱 [儲存體帳戶總覽](../common/storage-account-overview.md)。

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>建立具有階層命名空間的儲存體帳戶

建立[一般用途 V2 帳戶](../common/storage-account-create.md)或已啟用**階層命名空間**設定的[BlockBlobStorage](storage-blob-create-account-block-blob.md)帳戶。

當您建立帳戶時，請在 [**建立儲存體帳戶** **] 頁面的 [** 設定] 索引標籤中啟用**階層式命名空間**設定，以解除鎖定 Data Lake Storage 功能。 當您建立帳戶時，必須啟用此設定。 您之後無法啟用它。

下圖顯示 [ **建立儲存體帳戶** ] 頁面中的這項設定。

> [!div class="mx-imgBorder"]
> ![階層命名空間設定](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

如果您有想要與 Data Lake Storage 搭配使用的現有儲存體帳戶，且已停用階層命名空間設定，則必須將資料移轉至已啟用設定的新儲存體帳戶。

## <a name="next-steps"></a>後續步驟

- [儲存體帳戶概觀](../common/storage-account-overview.md)
- [使用 Azure Data Lake Storage Gen2 處理巨量資料需求](data-lake-storage-data-scenarios.md)
- [Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)