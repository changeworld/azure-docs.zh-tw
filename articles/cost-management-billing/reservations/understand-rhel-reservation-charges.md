---
title: Red Hat 保留方案折扣 - Azure
description: 了解 Red Hat 方案折扣如何套用至虛擬機器上的 Red Hat 軟體。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 4314a52488473bcacd4aa62a0e8f44d88d2b75f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681595"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>了解 Azure 會如何套用 Red Hat Linux Enterprise 軟體保留方案折扣

在您購買 Red Hat Linux 方案之後，折扣就會自動套用到符合保留的已部署 Red Hat 虛擬機器 (VM)。 Red Hat Linux 方案涵蓋在 Azure VM 上執行 Red Hat 軟體的成本。

若要購買適當的 Red Hat Linux 方案，您需要了解您執行哪些 Red Hat VM，以及這些 VM 上的 vCPU 數目。 使用下列各節，可協助您從使用量 CSV 檔案中找出所要購買的方案。

## <a name="discount-applies-to-different-vm-sizes"></a>折扣會套用於不同的 VM 大小

如同保留的 VM 執行個體，購買 Red Hat 方案時具有執行個體大小彈性。 這表示您的折扣甚至適用於當您部署具有不同 vCPU 計數的 VM 時。 折扣適用於軟體方案內的各種 VM 大小。

折扣金額取決於下列表格中所列的比例。 此比例會比較該群組中每個 VM 大小的相對使用量。 此比例取決於 VM vCPU。 使用比例值來計算有多少 VM 執行個體取得 Red Hat Linux 方案折扣。

例如，如果您針對具有 1 到 4 個 vCPU 的 VM 購買適用於 Red Hat Linux Enterprise Server 的方案，則該保留的比例為 1。 此折扣涵蓋以下的 Red Hat 軟體成本：

- 1 個已部署的 VM (具有 1 到 4 個 vCPU)，
- 或約 46% 的 Red Hat Enterprise Linux 成本 (具有 5 個或更多 vCPU 的 VM)。

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure 入口網站市集名稱：
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[檢查方案適用的 Red Hat Enterprise Linux 計量](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>後續步驟

若要深入了解保留，請參閱下列文章：

- [什麼是 Azure 的保留](save-compute-costs-reservations.md)
- [使用 Azure 保留來預付 Red Hat 軟體方案](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理 Azure 的保留](manage-reserved-vm-instance.md)
- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
