---
title: 了解保留折扣如何套用至 Azure 儲存體 | Microsoft Docs
description: 深入了解如何將 Azure 儲存體保留容量折扣套用至區塊 Blob 和 Azure Data Lake Storage Gen2 資源。
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: 40fba61b173979fb9362de73a87d6192aac9941d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995308"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>了解保留折扣如何套用至 Azure 儲存體

購買 Azure 儲存體保留容量之後，保留折扣會自動套用至符合保留條款的區塊 Blob 和 Azure Data Lake Storage Gen2 資源。 保留折扣只會套用至儲存容量。 頻寬和要求率會以隨用隨付費率計費。

如需 Azure 儲存體保留容量的詳細資訊，請參閱[使用保留容量將 Blob 儲存體的成本最佳化](../../storage/blobs/storage-blob-reserved-capacity.md)。

如需 Azure 儲存體保留定價的詳細資訊，請參閱[區塊 Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen 2 定價](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="how-the-reservation-discount-is-applied"></a>保留折扣的套用方式

Azure 儲存體保留容量折扣會每小時套用至區塊 Blob 和 Azure Data Lake Storage Gen2 資源。

Azure 儲存體保留容量折扣是「不用則作廢」的折扣。 如果您沒有任何區塊 Blob 或 Azure Data Lake Storage Gen2 資源符合指定小時的保留條款，則會遺失該小時的保留數量。 您無法遞轉未使用的保留時數。

當您刪除資源時，保留折扣會自動套用至指定範圍中另一個相符的資源。 如果在指定的範圍內找不到相符的資源，則會失去保留時數。

## <a name="discount-examples"></a>折扣範例

下列範例會根據部署來顯示 Azure 儲存體保留容量折扣的套用方式。

假設您在美國西部 2 區域中購買了 1 年期 100 TB 的保留容量。 您的保留適用於經常性存取層中的本地備援儲存體 (LRS)。

假設此範例保留的成本為 18,540 美元。 在接下來的 12 個月內，您可以選擇全額預付，或每月固定支付 1,545 美元的分期付款。

在這些範例中，假設您已註冊每月保留付款方案。 下列案例說明當您未充分使用或過度使用保留容量時所會發生的情況。

### <a name="underusing-your-capacity"></a>未充分使用您的容量

假設在保留期間內的指定小時內，您只使用了 100 TB 保留容量中的 80 TB。 剩餘的 20 TB 不會套用到該小時，也不會結轉。

### <a name="overusing-your-capacity"></a>過度使用您的容量

假設在保留期間內的指定小時內，您使用了 101 TB 的儲存容量。 保留折扣適用於 100 TB 的資料，而剩餘的 1 TB 會依該小時的隨用隨付費率計費。 如果您在下一個小時的使用量變更為 100 TB，則保留會涵蓋所有使用量。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [使用保留容量將 Blob 儲存體的成本最佳化](../../storage/blobs/storage-blob-reserved-capacity.md)
- [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
