---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5accc38a8693fba5934b1182ebdafe8921540d98
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170206"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[\[有限的預覽\]：\[AKS\] 在 AKS 中不允許特殊權限容器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerNoPrivilege_EnforceRegoPolicy.json) |此原則不允許在 Azure Kubernetes Service 叢集中建立特殊權限容器。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 在 AKS 中強制使用 HTTPS 輸入](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHttpsOnly_EnforceRegoPolicy.json) |此原則在 Azure Kubernetes Service 叢集中強制使用 HTTPS 輸入。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 強制 AKS 中只有內部負載平衡器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/LoadbalancerNoPublicIPs_EnforceRegoPolicy.json) |此原則會強制 Azure Kubernetes Service 叢集中的負載平衡器不要使用公用 IP。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 強制在 AKS 中的 Pod 使用標籤](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/PodEnforceLabels_EnforceRegoPolicy.json) |此原則會強制為 Azure Kubernetes Service 叢集中的 Pod 提供指定標籤。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 強制輸入主機名稱在 AKS 叢集中的各個命名空間均不重複](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHostnamesConflict_EnforceRegoPolicy.json) |此原則會強制在 Azure Kubernetes Service 叢集中的命名空間使用唯一的輸入主機名稱。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 確保容器只在 AKS 中允許的連接埠上接聽](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedPorts_EnforceRegoPolicy.json) |此原則會強制容器只在 Azure Kubernetes Service 叢集中允許的連接埠上接聽。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 確定 AKS 中的容器定義了 CPU 與記憶體資源限制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerResourceLimits_EnforceRegoPolicy.json) |此原則可確保 CPU 和記憶體資源限制會在 Azure Kubernetes Service 叢集中的容器上定義。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 確保 AKS 中只有允許的容器映像](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedImages_EnforceRegoPolicy.json) |此原則會確認 Azure Kubernetes Service 叢集中只執行允許的容器映像。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
|[\[有限的預覽\]：\[AKS\] 確保服務只在 AKS 中允許的連接埠上接聽](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ServiceAllowedPorts_EnforceRegoPolicy.json) |此原則會強制服務只在 Azure Kubernetes Service 叢集中允許的連接埠上接聽。 有限的預覽原則僅適用於已註冊的訂用帳戶。 若要註冊，請移至 https://aka.ms/akspolicyonboarding 。 如需使用此原則的指示，請移至 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy, Disabled |1.0.0-preview |
