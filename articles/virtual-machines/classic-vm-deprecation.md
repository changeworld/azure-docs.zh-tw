---
title: 我們將在 2023 年 3 月 1 日停用 Azure 經典 VM
description: 本文提供了經典 VM 停用的高級概述
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 764567bffd2a08ebb5beb17e3063998848b3f110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127327"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>到 2023 年 3 月 1 日前將 IaaS 資源遷移到 Azure 資源管理器 

2014 年，我們在 Azure 資源管理器上推出了 IaaS，並自那時以來一直在增強功能。 由於[Azure 資源管理器](https://azure.microsoft.com/features/resource-manager/)現在具有完整的 IaaS 功能和其他進步，因此我們于 2020 年 2 月 28 日通過 Azure 服務管理員棄用 IaaS VM 的管理，此功能將于 2023 年 3 月 1 日完全停用。 

如今，大約 90% 的 IaaS VM 正在使用 Azure 資源管理器。 如果通過 Azure 服務管理員 （ASM） 使用 IaaS 資源，請立即開始規劃遷移，並在 2023 年 3 月 1 日前完成遷移，以利用[Azure 資源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/)。

經典 VM 將遵循[現代生命週期政策](https://support.microsoft.com/help/30881/modern-lifecycle-policy)進行退休。

## <a name="how-does-this-affect-me"></a>此變更對我造成什麼影響？ 

1) 從 2020 年 2 月 28 日起，未在 2020 年 2 月通過 Azure 服務管理員 （ASM） 使用 IaaS VM 的客戶將無法再創建經典 VM。 
2) 2023 年 3 月 1 日，客戶將不再能夠使用 Azure 服務管理員啟動 IaaS VM，任何仍在運行或分配的使用者都將停止並進行處理。 
2) 2023 年 3 月 1 日，將通知尚未遷移到 Azure 資源管理器的訂閱刪除任何剩餘的經典 VM 的時程表。  

以下 Azure 服務和**功能不會受**此停用的影響： 
- 雲端服務 
- 經典 VM**未使用的**存儲帳戶 
- 經典 VM**未使用的**虛擬網路 （VNet）。 
- 其他經典資源

## <a name="what-actions-should-i-take"></a>我應該採取什麼行動？ 

- 立即開始規劃遷移到 Azure 資源管理器。 

- [詳細瞭解](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)如何將經典[Linux](./linux/migration-classic-resource-manager-plan.md)和[Windows](./windows/migration-classic-resource-manager-plan.md) VM 遷移到 Azure 資源管理器。

- 有關詳細資訊，請參閱有關 Azure[資源管理器遷移的經典問題](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- 有關技術問題和問題，[請聯繫支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)人員。

- 對於不屬於常見問題解答和回饋的其他問題，請評論如下。
