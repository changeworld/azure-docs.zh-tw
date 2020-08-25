---
title: 我們即將于2023年3月1日淘汰 Azure 傳統 Vm
description: 文章提供傳統 VM 淘汰的高階總覽
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 0b2b995a6fe4cedd14b2e4ceeddc5747ec2423cf
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754798"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>在2023年3月1日前將 IaaS 資源遷移至 Azure Resource Manager 

在2014中，我們已在 Azure Resource Manager 上啟動 IaaS，而且自之後一直以來都有增強的功能。 因為 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 現在已有完整的 IaaS 功能和其他改進，所以我們已在2020年2月28日淘汰透過 [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) 管理 iaas vm，且此功能將于2023年3月1日完全淘汰。 

目前，大約90% 的 IaaS Vm 會使用 Azure Resource Manager。 如果您透過 Azure Service Manager (ASM) 使用 IaaS 資源，請立即開始規劃您的遷移，並在2023年3月1日前完成，以利用 [Azure Resource Manager](../azure-resource-manager/management/index.yml)。

傳統 Vm 將遵循 [新式生命週期原則](https://support.microsoft.com/help/30881/modern-lifecycle-policy) 來淘汰。

## <a name="how-does-this-affect-me"></a>此變更會對我造成什麼影響？ 

- 自2020年2月28日起，未在2020年2月的 Azure Service Manager (ASM) 使用 IaaS Vm 的客戶將不再能夠建立傳統 Vm。 
- 在2023年3月1日，客戶將無法再使用 Azure Service Manager 啟動 IaaS Vm，而且任何仍在執行或配置的虛擬機器都將停止並解除配置。 
- 在2023年3月1日，未遷移至 Azure Resource Manager 的訂用帳戶，將會收到有關刪除任何剩餘傳統 Vm 的時程表。  

下列 Azure 服務和功能 **不** 會受到此淘汰的影響： 
- 雲端服務 
- 傳統 Vm **未** 使用的儲存體帳戶 
- 傳統 Vm **未** 使用 (vnet) 虛擬網路。 
- 其他傳統資源

## <a name="what-actions-should-i-take"></a>我應該採取哪些動作？ 

- 立即開始規劃您的遷移至 Azure Resource Manager。 

- 建立所有受影響 Vm 的清單。 [Azure 入口網站 vm](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines)分頁上的 VM 類型「虛擬機器 (傳統) 」是訂用帳戶中所有受影響的 vm。 

- [深入瞭解](./windows/migration-classic-resource-manager-overview.md) 如何將傳統 [Linux](./linux/migration-classic-resource-manager-plan.md) 和 [Windows](./windows/migration-classic-resource-manager-plan.md) vm 遷移至 Azure Resource Manager。

- 如需詳細資訊，請參閱 [傳統的常見問題以 Azure Resource Manager 遷移](./migration-classic-resource-manager-faq.md)

- 如有技術問題、問題，以及將訂閱新增到允許清單，請 [聯絡支援人員](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})。

- 針對其他不屬於常見問題和意見反應的問題，請在下方留言。

- 請儘快完成遷移，以防止業務影響並利用改良的效能、安全性 & Azure Resource Manager 所提供的新功能。 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>此遷移會提供哪些資源給我？

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html)：適用于遷移的 microsoft & 支援小組

- [Azure 遷移支援](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})：在遷移期間針對技術協助提供專屬的支援小組

- [Microsoft Fast track](https://www.microsoft.com/fasttrack)： Microsoft fast track 小組可以在遷移至合格客戶時，提供技術協助。 

- 如果您的公司/組織與 Microsoft 合作，或與 microsoft 代表合作，例如 (雲端解決方案架構師 (CSA) 、技術客戶經理 (TAMs) # A5，請與他們合作以取得其他資源以進行遷移。 

