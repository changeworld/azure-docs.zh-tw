---
title: 了解保留折扣如何套用至 Azure 磁碟儲存體
description: 了解如何將 Azure 保留磁碟的折扣，套用至進階 SSD 受控磁碟。
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902137"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>了解如何將您的保留折扣套用至 Azure 磁碟儲存體

購買 Azure 磁碟保留容量之後，保留折扣會自動套用至符合保留條款的磁碟資源。 保留折扣僅適用於磁碟容量，磁碟快照則會依隨用隨付費率計費。

如需 Azure 磁碟保留的詳細資訊，請參閱[使用 Azure 磁碟保留來節省成本](../../virtual-machines/linux/disks-reserved-capacity.md)。
如需 Azure 磁碟保留的價格資訊，請參閱 [Azure 磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="how-the-reservation-discount-is-applied"></a>保留折扣的套用方式

Azure 磁碟保留折扣是一種「不用即失去」的折扣，會每小時套用到受控磁碟資源。 如果您沒有任何受控磁碟資源符合指定小時的保留條款，則會遺失該小時的保留數量，未使用的時數不會結轉。

當您刪除資源時，保留折扣會自動套用至指定範圍中另一個相符的資源。 如果在指定的範圍內找不到相符的資源，則會失去保留時數。

## <a name="discount-examples"></a>折扣範例

下列範例顯示 Azure 磁碟保留折扣如何根據部署套用折扣：

假設您在美國西部 2 區域中購買了 1 年期 100 P30 磁碟 (每一磁碟 ~1 TB) 的保留容量。 假設此範例保留的成本為 140,100 美元。 在接下來的十二個月內，您可以選擇全額預付，或每月固定支付 11,675 美元的分期付款。
在這些範例中，假設您已註冊每月保留付款方案。 下列案例說明當您未充分使用、過度使用或分層使用保留容量時所發生的情況。

### <a name="underusing-your-capacity"></a>未充分使用您的容量

假設在保留期間內的指定小時內，您只部署了 100 P30 磁碟保留中的 99 P30 進階 SSD。 剩餘的 1 P30 不會套用到該小時，也不會結轉。

### <a name="overusing-your-capacity"></a>過度使用您的容量

假設在保留期間內的指定小時內，您使用了 101 P30 的進階 SSD。 保留折扣只會套用至 100 P30 的磁碟，剩餘的 1 P30 磁碟則會依該小時的隨用隨付費率計費。 在下一個小時，如果您的使用量增加至 100 P30 磁碟，則保留會涵蓋所有使用量。

### <a name="tiering-your-capacity"></a>分層使用您的容量

假設在保留期間內的指定小時內，您想要使用總共 200 P30 的進階 SSD。 在前 30 分鐘您只會使用 100 P30。 在這段期間，因為您已保留了 100 P30 磁碟，所以能完全涵蓋您的使用。 如果您接著中止使用第一個 100 (因此您使用零)，然後開始在剩餘的 30 分鐘使用其他 100，則該使用量也會涵蓋在保留範圍內。

![未充分使用、過度使用、分層使用容量的範例描述](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 磁碟保留來節省成本 (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [使用 Azure 磁碟保留來節省成本 (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)