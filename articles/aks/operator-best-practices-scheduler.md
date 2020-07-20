---
title: 操作員最佳做法 - Azure Kubernetes Services (AKS) 中的基本排程器功能
description: 了解叢集操作員在使用基本排程器功能 (例如，Azure Kubernetes Service (AKS) 中的資源配額和 Pod 中斷預算) 時的最佳做法
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84704738"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) 中基本排程器功能的最佳做法

您在管理 Azure Kubernetes Service (AKS) 中的叢集時，往往需要隔離小組和工作負載。 Kubernetes 排程器提供了一些功能，以讓您控制計算資源的分配，或限制維護事件的影響。

本最佳做法文章著重於叢集操作員的基本 Kubernetes 排程功能。 在本文中，您將學會如何：

> [!div class="checklist"]
> * 使用資源配額提供固定的資源數量給小組或工作負載
> * 使用 Pod 中斷預算限制排程維護的影響
> * 使用 `kube-advisor` 工具檢查是否有遺漏的 Pod 資源要求和限制

## <a name="enforce-resource-quotas"></a>強制執行資源配額

**最佳做法指引** - 請在命名空間層級規劃和套用資源配額。 如果 Pod 未定義資源要求和限制，則拒絕該部署。 監視資源使用量，並視需要調整配額。

資源要求和限制會放置在 Pod 規格中。 Kubernetes 排程器會在部署期間使用這些限制來尋找叢集中可用的節點。 這些限制和要求可在個別 Pod 層級中運作。 如需如何定義這些值的詳細資訊，請參閱[定義 Pod 資源要求和限制][resource-limits]

若要提供一個方式來保留及限制跨開發小組或專案的資源，請使用「資源配額」**。 這些配額會定義在命名空間上，且可用來對下列基礎設定配額：

* **計算資源**，例如 CPU 和記憶體或 GPU。
* **儲存體資源**，包括磁碟區的總數，或是指定儲存體類別的磁碟空間數量。
* **物件計數**，例如可建立的祕密、服務或作業數目上限。

Kubernetes 不會過量使用資源。 一旦資源要求或限制的累計總和超過指派的配額後，任何進一步的部署都不會成功。

當您定義資源配額時，命名空間中建立的 Pod 都必須在其 Pod 規格中提供限制或要求。 如果未提供這些值，您可以拒絕部署。 相反地，您可以[設定命名空間的預設要求和限制][configure-default-quotas]。

下列名為 dev-app-team-quotas.yaml** 的範例 YAML 資訊清單會設定總共只能有 10** 個 CPU、20 Gi** 的記憶體和 10** 個 Pod 的固定限制：

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

您可以藉由指定命名空間來套用這個資源配額，例如 dev-apps**：

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

請與您的應用程式開發人員和擁有者合作，了解他們的需求並套用適當的資源配額。

如需可用資源物件、範圍和優先順序的詳細資訊，請參閱 [Kubernetes 中的資源配額][k8s-resource-quotas]。

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>使用 Pod 中斷預算規劃可用性

**最佳做法指引** - 為維護應用程式的可用性，請定義 Pod 中斷預算 (PDB) 以確定叢集中可用的 Pod 數目下限。

有兩個干擾性事件會導致 Pod 遭到移除：

* 「非自發性中斷」** 是超過叢集操作員或應用程式擁有者一般控制力的事件。
  * 這些非自願中斷包含實體機器上的硬體故障、核心異常或節點 VM 遭到刪除
* 「自發性中斷」** 是叢集操作員或應用程式擁有者所要求的事件。
  * 這些自發性中斷包括叢集升級、部署範本更新，或不小心刪除 Pod。

您可以在部署中使用您 Pod 的多個複本來降低非自發性中斷。 在 AKS 叢集中執行多個節點也有助於避免這些非自發性中斷的發生。 針對自發性中斷，Kubernetes 會提供「Pod 中斷預算」**，以讓叢集操作員定義可用資源計數下限或無法使用的資源計數上限。 這些 Pod 中斷預算可讓您規劃當發生自發性中斷事件時，部署或複本集要如何回應。

如果要升級叢集或更新部署範本，Kubernetes 排程器會先確定其他節點上已排程另外的 Pod，才讓自發性中斷事件繼續進行。 排程器在等到叢集的其他節點上已成功排程所定義數量的 Pod，才會將節點重新開機。

讓我們看看一個複本集範例，此複本集具有五個執行 NGINX 的 Pod。 複本集內的 pod 會被指派標籤 `app: nginx-frontend` 。 在自發性中斷事件 (例如，叢集升級) 發生期間，您想要確定至少有三個 Pod 會繼續執行。 PodDisruptionBudget** 物件的下列 YAML 資訊清單會定義這些需求：

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

您也可以定義百分比 (例如，60%**)，以便能夠自動補償相應增加 Pod 數目的複本集。

您可以在複本集內定義無法使用的執行個體數目上限。 同樣地，也可以定義無法使用的 Pod 上限百分比。 下列 Pod 中斷預算 YAML 資訊清單會定義複本集內不能有超過兩個 Pod 無法使用：

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

在定義了 Pod 中斷預算之後，請將其建立到 AKS 叢集內，就如同您對任何其他 Kubernetes 物件的做法：

```console
kubectl apply -f nginx-pdb.yaml
```

請與您的應用程式開發人員和擁有者合作，了解他們的需求並套用適當的 Pod 中斷預算。

如需如何使用 Pod 中斷預算的詳細資訊，請參閱[指定應用程式的中斷預算][k8s-pdbs]。

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>使用 kube-advisor 定期檢查叢集的問題

**最佳做法指引**-定期執行最新版本的 `kube-advisor` 開放原始碼工具，以偵測您叢集中的問題。 如果您在現有的 AKS 叢集上套用資源配額，請先執行 `kube-advisor` 以尋找未定義資源要求和限制的 Pod。

[Kube advisor][kube-advisor]工具是一個相關聯的 AKS 開放原始碼專案，可掃描 Kubernetes 叢集並報告發現的問題。 一個實用的檢查，就是找出沒有備妥資源要求和限制的 Pod。

kube-advisor 工具可以報告適用於 Windows 應用程式和 Linux 應用程式的 PodSpecs 中的資源要求和限制遺漏，但是 kube-advisor 工具本身必須在 Linux Pod 上排程。 您可以使用 Pod 設定中的[節點選取器][k8s-node-selector]，排程 Pod 在具有特定作業系統的節點集區上執行。

在裝載多個開發小組和應用程式的 AKS 叢集中，若沒有這些資源要求和限制集，就可能難以追蹤 Pod。 最佳做法是在您的 AKS 叢集上定期執行 `kube-advisor`，特別是如果您未對命名空間指派資源配額時。

## <a name="next-steps"></a>後續步驟

本文著重於 Kubernetes 排程器的基本功能。 如需 AKS 中叢集作業的相關詳細資訊，請參閱下列最佳作法：

* [多租用戶和叢集隔離][aks-best-practices-cluster-isolation]
* [Kubernetes 排程器的進階功能][aks-best-practices-advanced-scheduler]
* [驗證與授權][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
