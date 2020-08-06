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
ms.openlocfilehash: 16ca4ad1204301542ffaa0177d88b2c2800f13b4
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836270"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>將您的 IaaS 資源遷移到2023年3月1日 Azure Resource Manager 

在2014中，我們已在 Azure Resource Manager 上啟動 IaaS，並已在之後增強功能。 因為[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)現在具有完整的 IaaS 功能和其他改進，所以我們已淘汰在2020年2月28日透過[Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic)管理 IaaS vm，而這項功能將于2023年3月1日完全淘汰。 

今天，大約有90% 的 IaaS Vm 正在使用 Azure Resource Manager。 如果您透過 Azure Service Manager (ASM) 來使用 IaaS 資源，請立即開始規劃您的遷移，並于2023年3月1日前完成，以利用[Azure Resource Manager](../azure-resource-manager/management/index.yml)。

傳統 Vm 將會遵循[新式生命週期原則](https://support.microsoft.com/help/30881/modern-lifecycle-policy)來淘汰。

## <a name="how-does-this-affect-me"></a>此變更會對我造成什麼影響？ 

- 從2020年2月28日開始，未透過 Azure Service Manager 使用 IaaS Vm 的客戶 (在2020年2月的 ASM) ，將不再能夠建立傳統 Vm。 
- 2023年3月1日之後，客戶將無法再使用 Azure Service Manager 啟動 IaaS Vm，而且任何仍在執行或已配置的系統將會停止並解除配置。 
- 在2023年3月1日，未遷移至 Azure Resource Manager 的訂用帳戶將會收到刪除任何剩餘傳統 Vm 之時程表的相關通知。  

此淘汰**不**會影響下列 Azure 服務和功能： 
- 雲端服務 
- 傳統 Vm**未**使用的儲存體帳戶 
- 傳統 Vm**不會**使用虛擬網路 (vnet) 。 
- 其他傳統資源

## <a name="what-actions-should-i-take"></a>我應該採取哪些動作？ 

- 立即開始規劃您的遷移至 Azure Resource Manager。 

- [深入瞭解](./windows/migration-classic-resource-manager-overview.md)如何將您的傳統[Linux](./linux/migration-classic-resource-manager-plan.md)和[Windows](./windows/migration-classic-resource-manager-plan.md) vm 遷移至 Azure Resource Manager。

- 如需詳細資訊，請參閱[關於傳統的常見問題以 Azure Resource Manager 遷移](./windows/migration-classic-resource-manager-faq.md)

- 如需技術問題、問題，以及將訂閱新增至允許清單，請[聯絡支援](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})人員。

- 針對其他不屬於常見問題和意見反應的問題，請在下方留言。

- 儘快完成遷移，以避免業務衝擊並運用改良的效能、安全性 & Azure Resource Manager 提供的新功能。 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>此遷移會提供哪些資源給我？

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html)： microsoft & 社區支援以進行遷移

- [Azure 遷移支援](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})：在遷移期間提供技術協助的專屬支援小組

- [Microsoft 快速追蹤](https://www.microsoft.com/fasttrack)： Microsoft fast track 小組可以在遷移至合格客戶時提供技術協助。 

- 如果您的公司/組織已與 microsoft 合作，並（或）與 Microsoft 代表 (雲端解決方案架構師 (CSA) ， (TAMs) # A5 的技術帳戶管理員，請與他們合作以取得其他資源供您進行遷移。 

