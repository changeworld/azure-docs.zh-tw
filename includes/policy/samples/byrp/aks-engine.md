---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ec5eecfd8a303b897f8e91beb16dcab2dcf07f8f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84704779"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[在 Kubernetes 叢集中不允許特殊權限的容器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4) |此原則不允許在 Kubernetes 叢集中建立特殊權限容器。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege_EnforceOPAConstraint.json) |
|[在 Kubernetes 叢集中強制使用 HTTPS 輸入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |此原則在 Kubernetes 叢集中強制使用 HTTPS 輸入。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly_EnforceOPAConstraint.json) |
|[在 Kubernetes 叢集中強制執行內部負載平衡器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3fc4dc25-5baf-40d8-9b05-7fe74c1bc64e) |此原則會強制 Kubernetes 叢集中的負載平衡器不要使用公用 IP。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/LoadbalancerNoPublicIPs_EnforceOPAConstraint.json) |
|[在 Kubernetes 叢集中的 Pod 上強制使用標籤](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F46592696-4c7b-4bf3-9e45-6c2763bdc0a6) |此原則會強制為 Kubernetes 叢集中的 Pod 提供指定標籤。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/PodEnforceLabels_EnforceOPAConstraint.json) |
|[確保容器 CPU 及記憶體資源限制未超過 Kubernetes 叢集中指定的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe345eecc-fa47-480f-9e88-67dcc122b164) |此原則會確保定義的容器 CPU 和記憶體資源限制未超過 Kubernetes 叢集中指定的限制。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits_EnforceOPAConstraint.json) |
|[確保容器只會接聽 Kubernetes 叢集中允許的連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F440b515e-a580-421e-abeb-b159a61ddcbc) |此原則會強制容器只在 Kubernetes 叢集中允許的連接埠上接聽。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts_EnforceOPAConstraint.json) |
|[確保 Kubernetes 叢集中只有允許的容器映像](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffebd0533-8e55-448f-b837-bd0e06f16469) |此原則會確認 Kubernetes 叢集中只執行允許的容器映像。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages_EnforceOPAConstraint.json) |
|[確保服務只會接聽 Kubernetes 叢集中允許的連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F233a2a17-77ca-4fb1-9b6b-69223d272a44) |此原則會強制服務只在 Kubernetes 叢集中允許的連接埠上接聽。 如需使用此原則的指示，請造訪 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint, disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts_EnforceOPAConstraint.json) |
