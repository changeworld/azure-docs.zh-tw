---
title: 我們即將于2023年3月1日淘汰 Azure 傳統 Vm
description: 文章提供傳統 VM 淘汰的高階總覽
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668797"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>將您的 IaaS 資源遷移到2023年3月1日 Azure Resource Manager 

在2014中，我們已在 Azure Resource Manager 上啟動 IaaS，並已在之後增強功能。 因為[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)現在具有完整的 IaaS 功能和其他改進，所以我們已淘汰在2020年2月28日透過 Azure Service Manager 管理 IaaS vm，而這項功能將于2023年3月1日完全淘汰。 

今天，大約有90% 的 IaaS Vm 正在使用 Azure Resource Manager。 如果您透過 Azure Service Manager （ASM）使用 IaaS 資源，請立即開始規劃您的遷移，並于2023年3月1日前完成，以利用[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/)。

傳統 Vm 將會遵循[新式生命週期原則](https://support.microsoft.com/help/30881/modern-lifecycle-policy)來淘汰。

## <a name="how-does-this-affect-me"></a>此變更對我造成什麼影響？ 

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

- [深入瞭解](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)如何將您的傳統[Linux](./linux/migration-classic-resource-manager-plan.md)和[Windows](./windows/migration-classic-resource-manager-plan.md) vm 遷移至 Azure Resource Manager。

- 如需詳細資訊，請參閱[關於傳統的常見問題以 Azure Resource Manager 遷移](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- 如需技術問題、問題和訂用帳戶允許清單[聯絡支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

- 針對其他不屬於常見問題和意見反應的問題，請在下方留言。
