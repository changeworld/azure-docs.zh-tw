---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169924"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[允許的位置](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |此原則可讓您限制您的組織在部署資源時可指定的位置。 它可用來強制執行地理合規性需求。 排除資源群組、Microsoft.AzureActiveDirectory/b2cDirectories，以及使用「全球」區域的資源。 |deny |1.0.0 |
|[允許資源群組的位置](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |此原則可讓您限制貴組織可在其中建立資源群組的位置。 它可用來強制執行地理合規性需求。 |deny |1.0.0 |
|[允許的資源類型](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |此原則可讓您指定組織所能部署的資源類型。 只有支援「標籤」和「位置」的資源類型會受此原則影響。 若要限制所有資源，請複製此原則，並將 [模式] 變更為 [全部]。 |deny |1.0.0 |
|[稽核資源位置是否符合資源群組位置](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |稽核資源位置是否符合其資源群組位置 |稽核 |1.0.0 |
|[稽核自訂 RBAC 規則的使用方式](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |稽核內建角色，例如「擁有者、參與者、讀取者」，而不是自訂的 RBAC 角色，這些角色容易發生錯誤。 使用自訂角色會視為例外狀況，而且需要嚴格審查和威脅模型化 |Audit, Disabled |1.0.0 |
|[自訂訂用帳戶擁有者角色不應存在](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |此原則可確保不存在自訂訂用帳戶擁有者角色。 |Audit, Disabled |1.0.0 |
|[不允許的資源類型](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |此原則可讓您指定組織不得部署的資源類型。 |拒絕 |1.0.0 |
