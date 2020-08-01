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
ms.openlocfilehash: d86805975b082136879c0a98ce2817f4f491a9a0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461199"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>將您的 IaaS 資源遷移到2023年3月1日 Azure Resource Manager 

在2014中，我們已在 Azure Resource Manager 上啟動 IaaS，並已在之後增強功能。 因為[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)現在具有完整的 IaaS 功能和其他改進，所以我們已淘汰在2020年2月28日透過 Azure Service Manager 管理 IaaS vm，而這項功能將于2023年3月1日完全淘汰。 

今天，大約有90% 的 IaaS Vm 正在使用 Azure Resource Manager。 如果您透過 Azure Service Manager （ASM）使用 IaaS 資源，請立即開始規劃您的遷移，並于2023年3月1日前完成，以利用[Azure Resource Manager](../azure-resource-manager/management/index.yml)。

傳統 Vm 將會遵循[新式生命週期原則](https://support.microsoft.com/help/30881/modern-lifecycle-policy)來淘汰。

## <a name="how-does-this-affect-me"></a>此變更會對我造成什麼影響？ 

1) 從2020年2月28日開始，未在2020年2月透過 Azure Service Manager （ASM）利用 IaaS Vm 的客戶將不再能夠建立傳統 Vm。 
2) 2023年3月1日之後，客戶將無法再使用 Azure Service Manager 啟動 IaaS Vm，而且任何仍在執行或已配置的系統將會停止並解除配置。 
2) 在2023年3月1日，尚未遷移至 Azure Resource Manager 的訂用帳戶將會收到刪除任何剩餘傳統 Vm 之時程表的相關通知。  

此淘汰**不**會影響下列 Azure 服務和功能： 
- 雲端服務 
- 傳統 Vm**未**使用的儲存體帳戶 
- 傳統 Vm**未**使用的虛擬網路（vnet）。 
- 其他傳統資源

## <a name="what-actions-should-i-take"></a>我應該採取哪些動作？ 

- 立即開始規劃您的遷移至 Azure Resource Manager。 

- [深入瞭解](./windows/migration-classic-resource-manager-overview.md)如何將您的傳統[Linux](./linux/migration-classic-resource-manager-plan.md)和[Windows](./windows/migration-classic-resource-manager-plan.md) vm 遷移至 Azure Resource Manager。

- 如需詳細資訊，請參閱[關於傳統的常見問題以 Azure Resource Manager 遷移](./windows/migration-classic-resource-manager-faq.md)

- 如需技術問題、問題，以及將訂閱新增至允許清單，請[聯絡支援](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})人員。

- 如需在遷移期間的協助，請[聯繫遷移支援](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})

- 針對其他不屬於常見問題和意見反應的問題，請在下方留言。
