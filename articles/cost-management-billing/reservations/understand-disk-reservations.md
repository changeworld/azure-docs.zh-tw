---
title: 了解 Azure 磁碟儲存體的保留折扣
description: 了解如何將 Azure 保留磁碟的折扣，套用至 Azure 進階 SSD 受控磁碟。
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: cd89c3ddc3c58de02f1104109ce7f243c4d1e6fd
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682445"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>了解如何將您的保留折扣套用至 Azure 磁碟儲存體

購買 Azure 磁碟保留容量之後，保留折扣會自動套用至符合保留條款的磁碟資源。 保留折扣僅會套用至磁碟 SKU。 磁碟快照集則會以隨用隨付費率計費。

如需 Azure 磁碟保留的詳細資訊，請參閱[使用 Azure 磁碟保留來節省成本](../../virtual-machines/disks-reserved-capacity.md)。 如需 Azure 磁碟保留的定價資訊，請參閱 [Azure 受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="how-the-reservation-discount-is-applied"></a>保留折扣的套用方式

Azure 磁碟保留折扣是「不用則作廢」的折扣。 每小時都會套用至受控磁碟資源。 在指定的小時內，如果您沒有符合保留條款的受控磁碟資源，就會遺失該小時的保留數量。 未使用的時數不會繼續執行。

當您刪除資源時，保留折扣會自動套用至指定範圍中另一個相符的資源。 如果找不到相符的資源，保留的時數則會遺失。

## <a name="discount-examples"></a>折扣範例

下列範例顯示 Azure 磁碟保留折扣如何根據部署套用折扣。

假設您在美國西部 2 區域購買並保留 100 個 P30 磁碟一年的期限。 每個磁碟大約有 1 TiB 的儲存體。 假設此範例保留的成本為 $140,100 美元。 在接下來的十二個月內，您可以選擇全額預付，或每月固定支付 $11,675‬ 美元的分期付款。

下列案例說明當您未充分使用、過度使用或分層使用保留容量時所發生的情況。 在這些範例中，假設您已註冊每月保留付款方案。

### <a name="underusing-your-capacity"></a>未充分使用您的容量

假設您在保留期間內，只部署 99 個 (共 100 個) 保留的 Azure 進階固態硬碟 (SSD) P30 磁碟一小時。 在該小時內，系統不會套用剩餘的 P30 磁碟。 未用完的保留容量也無法繼續使用。

### <a name="overusing-your-capacity"></a>過度使用您的容量

假設在保留期間內的一小時內，您使用了 101 個進階 SSD P30 磁碟。 保留折扣僅會套用至 100 個 P30 磁碟。 剩餘的 P30 磁碟會依該小時的隨用隨付費率計費。 在下一個小時，如果使用量增加至 100 個 P30 磁碟，則保留會涵蓋所有使用量。

### <a name="tiering-your-capacity"></a>分層使用您的容量

假設在保留期間的一小時內，您想要使用總共 200 個 P30 進階 SSD。 此外，假設您在前 30 分鐘僅使用了 100 個。 在這段期間，因為您已保留了 100 個 P30 磁碟，所以能完全涵蓋您的使用量。 如果您接著中止使用第一個 100 (因此您使用零)，然後開始在剩餘的 30 分鐘使用其他 100，則該使用量也會涵蓋在保留範圍內。

![未充分使用、過度使用、分層使用容量的範例](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 磁碟保留來節省成本](../../virtual-machines/disks-reserved-capacity.md)
- [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
