---
title: 我們即將于2023年3月1日淘汰 (傳統) 的 Azure Vm
description: 本文提供使用傳統部署模型所建立之 Vm 淘汰的概要說明。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 730a29ff579ce6a1970ceafad5891611b52c059d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972283"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>在2023年3月1日前將 IaaS 資源遷移至 Azure Resource Manager 

在2014中，我們啟動了基礎結構即服務 (IaaS) [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)。 自之後，我們已經增強了功能。 因為 Azure Resource Manager 現在已有完整的 IaaS 功能和其他改進，所以我們已淘汰在2020年2月28日透過 [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) 管理 iaas 虛擬機器 (vm) 。 此功能將于2023年3月1日完全淘汰。 

目前，大約90% 的 IaaS Vm 使用 Azure Resource Manager。 如果您透過 ASM 使用 IaaS 資源，請立即開始規劃您的遷移。 請于2023年3月1日之前完成，以利用 [Azure Resource Manager](../azure-resource-manager/management/index.yml)。

使用傳統部署模型建立的 Vm 將遵循 [新式生命週期原則](https://support.microsoft.com/help/30881/modern-lifecycle-policy) 來淘汰。

## <a name="how-does-this-affect-me"></a>此變更會對我造成什麼影響？ 

- 從2020年2月28日起，在2020年2月，未透過 ASM 使用 IaaS Vm 的客戶將無法再建立 (傳統) 的 Vm。 
- 在2023年3月1日，客戶將無法再使用 ASM 啟動 IaaS Vm。 任何仍在執行或配置的程式都會停止並解除配置。 
- 在2023年3月1日，未遷移至 Azure Resource Manager 的訂用帳戶，將會收到有關刪除任何剩餘 Vm (傳統) 的時程表。  

這項淘汰 *不會影響下列* Azure 服務和功能： 
- Azure 雲端服務 
- Vm (傳統) *不* 使用的儲存體帳戶 
- Vm (傳統) *不* 使用的虛擬網路 
- 其他傳統資源

## <a name="what-actions-should-i-take"></a>我應該採取哪些動作？ 

立即開始規劃您的遷移至 Azure Resource Manager。 

1. 建立所有受影響 Vm 的清單： 

   - 在[Azure 入口網站的 VM 窗格](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines)上，**虛擬機器類型 (傳統) **的 vm，全都是訂用帳戶內受影響的所有 vm。 
   - 您也可以使用 [入口網站](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) 或 [PowerShell](../governance/resource-graph/concepts/work-with-data.md) 來查詢 Azure Resource Graph，以查看所有已加上旗標的 vm 清單 (傳統) 以及所選訂用帳戶的相關資訊。 
   - 在2020年2月8日和9月2日，我們已向訂用帳戶擁有者傳送電子郵件，其中包含這些 Vm (傳統) 的所有訂用帳戶清單。 請使用它們來建立這份清單。 

1. [深入瞭解](./windows/migration-classic-resource-manager-overview.md) 如何將 [Linux](./linux/migration-classic-resource-manager-plan.md) 和 [Windows](./windows/migration-classic-resource-manager-plan.md) vm (傳統) 遷移至 Azure Resource Manager。 如需詳細資訊，請參閱傳統的常見問題 [至 Azure Resource Manager 的遷移](./migration-classic-resource-manager-faq.md)。

1. 建議您開始使用 [平臺支援遷移工具](./windows/migration-classic-resource-manager-overview.md) 來遷移現有的 vm，方法有三個簡單的步驟：驗證、準備和認可。 此工具的設計是要在最短的時間內遷移您的 Vm，而不需要停機。 

   1. 第一個步驟「驗證」不會影響您現有的部署，並提供所有不支援的案例清單來進行遷移。 
   1. 請流覽因應措施的 [清單](./windows/migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) 來修正您的部署，並讓它準備好進行遷移。 
   1. 在修正所有驗證錯誤之後，最好不要在準備和認可步驟期間遇到任何問題。 認可成功之後，您的部署會即時移轉至 Azure Resource Manager，然後可以透過 Azure Resource Manager 所公開的新 Api 來管理。 

   如果遷移工具不適用於您的遷移，您可以探索 [其他計算供應](/azure/architecture/guide/technology-choices/compute-decision-tree) 專案以進行遷移。 因為有許多 Azure 計算供應專案，而且它們彼此不同，所以我們無法為其提供平臺支援的遷移路徑。  

1. 針對將訂用帳戶新增至允許清單的技術問題、問題和協助，請 [聯絡支援人員](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})。

1. 請儘快完成遷移以防止業務影響，並充分利用 Azure Resource Manager 改善的效能、安全性及新功能。 

## <a name="what-resources-are-available-for-this-migration"></a>此遷移有哪些資源可用？

- [Microsoft 問&答](/answers/topics/azure-virtual-machines-migration.html)： microsoft 和社區支援的遷移。

- [Azure 遷移支援](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})：在遷移期間提供技術協助的專屬支援小組。

- [Microsoft Fast track](https://www.microsoft.com/fasttrack)：快速追蹤可協助合格客戶進行此遷移的規劃 & 執行。 [提名您自己](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0)。  

- 如果您的公司/組織已與 Microsoft 合作，或與 Microsoft 代表 (（例如雲端解決方案架構設計人員） (Csa) 或技術帳戶管理員 (TAMs) # A5，請與他們合作以取得其他資源來進行遷移。