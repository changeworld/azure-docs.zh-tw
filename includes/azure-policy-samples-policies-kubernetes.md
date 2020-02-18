---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 49ba27ef559748a8805160558d48b7b7c2cbe80f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170156"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[\[預覽\]：\[AKS 引擎\]在 Kubernetes 叢集中不允許特殊權限容器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege_EnforceOPAConstraint.json) |此原則不允許在 Kubernetes 叢集中建立特殊權限容器。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\]在 Kubernetes 叢集中強制使用 HTTPS 輸入](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly_EnforceOPAConstraint.json) |此原則在 Kubernetes 叢集中強制使用 HTTPS 輸入。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\]在 Kubernetes 叢集中強制執行內部負載平衡器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/LoadbalancerNoPublicIPs_EnforceOPAConstraint.json) |此原則會強制 Kubernetes 叢集中的負載平衡器不要使用公用 IP。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\]在 Kubernetes 叢集中的 Pod 上強制使用標籤](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/PodEnforceLabels_EnforceOPAConstraint.json) |此原則會強制為 Kubernetes 叢集中的 Pod 提供指定標籤。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\]強制在 Kubernetes 叢集中的命名空間使用唯一的輸入主機名稱](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHostnamesConflict_EnforceOPAConstraint.json) |此原則會強制在 Kubernetes 叢集中的命名空間使用唯一的輸入主機名稱。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\] 確保容器 CPU 和記憶體資源限制未超過 Kubernetes 叢集中指定的限制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits_EnforceOPAConstraint.json) |此原則會確保定義的容器 CPU 和記憶體資源限制未超過 Kubernetes 叢集中指定的限制。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\] 確保容器只在 Kubernetes 叢集中允許的連接埠上接聽](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts_EnforceOPAConstraint.json) |此原則會強制容器只在 Kubernetes 叢集中允許的連接埠上接聽。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\] 確認 Kubernetes 叢集中只有允許的容器映像](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages_EnforceOPAConstraint.json) |此原則會確認 Kubernetes 叢集中只執行允許的容器映像。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
|[\[預覽\]：\[AKS 引擎\] 確保服務只在 Kubernetes 叢集中允許的連接埠上接聽](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts_EnforceOPAConstraint.json) |此原則會強制服務只在 Kubernetes 叢集中允許的連接埠上接聽。 如需使用此原則的指示，請移至 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint, disabled |1.0.0-preview |
