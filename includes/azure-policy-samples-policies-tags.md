---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5a9d60cd019e044fd19a8670c0843a8d155f5433
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166583"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[將標籤新增至資源群組](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |當建立或更新了任何遺失指定標籤的資源群組時，便新增此標籤和值。 您可以藉由觸發補救工作來補救現有的資源群組。 如果標籤以不同的值存在，則不會遭到變更。 |修改 |1.0.0 |
|[將標籤新增至資源](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |當建立或更新了任何遺失指定標籤的資源時，便新增此標籤和值。 您可以藉由觸發補救工作來補救現有的資源。 如果標籤以不同的值存在，則不會遭到變更。 資源群組上的標記不會修改。 |修改 |1.0.0 |
|[新增或置換資源群組上的標籤](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |當建立或更新了任何資源群組時，便新增或取代此標籤和值。 您可以藉由觸發補救工作來補救現有的資源群組。 |修改 |1.0.0 |
|[新增或置換資源上的標籤](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |當建立或更新了任何資源時，便新增或取代此標籤和值。 您可以藉由觸發補救工作來補救現有的資源。 資源群組上的標記不會修改。 |修改 |1.0.0 |
|[附加標籤及其預設值](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |當建立或更新了任何遺失指定標籤的資源時，便附加此標籤和值。 在這些資源有所變更之前，不會修改在套用此原則之前所建立的資源標籤。 不會套用至資源群組。 有新的「修改」效果原則可支援補救現有資源上的標籤 (請參閱 https://aka.ms/modifydoc) 。 |附加 |1.0.0 |
|[將標籤及其預設值附加至資源群組](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |當建立或更新了任何遺失指定標籤的資源群組時，便附加此標籤和值。 在這些資源群組有所變更之前，不會修改在套用此原則之前所建立的資源群組標籤。 有新的「修改」效果原則可支援補救現有資源上的標籤 (請參閱 https://aka.ms/modifydoc) 。 |附加 |1.0.0 |
|[附加來自資源群組的標籤及其值](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |當建立或更新了任何遺失指定標籤的資源時，便附加來自資源群組的這個標籤及其值。 在這些資源有所變更之前，不會修改在套用此原則之前所建立的資源標籤。 有新的「修改」效果原則可支援補救現有資源上的標籤 (請參閱 https://aka.ms/modifydoc) 。 |附加 |1.0.0 |
|[從資源群組繼承標籤](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |當建立或更新了任何資源時，新增或取代來自父代資源群組中的指定標籤和值。 您可以藉由觸發補救工作來補救現有的資源。 |修改 |1.0.0 |
|[從資源群組繼承標籤 (若遺漏)](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |當建立或更新了任何遺失指定標籤的資源時，便新增來自父代資源群組的這個標籤及其值。 您可以藉由觸發補救工作來補救現有的資源。 如果標籤以不同的值存在，則不會遭到變更。 |修改 |1.0.0 |
|[必須具備指定的標籤](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |強制存在標籤。 不會套用至資源群組。 |deny |1.0.0 |
|[資源群組必須具備指定的標籤](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |施行資源群組必須具備標籤。 |deny |1.0.0 |
|[需要標籤及其值](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |強制必要的標籤和其值。 不會套用至資源群組。 |deny |1.0.0 |
|[需要資源群組上的標籤和其值](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |強制資源群組上必要的標籤及其值。 |deny |1.0.0 |
