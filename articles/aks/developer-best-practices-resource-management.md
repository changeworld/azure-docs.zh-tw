---
title: 開發人員最佳做法 - Azure Kubernetes Services (AKS) 中的資源管理
description: 了解應用程式開發人員在 Azure Kubernetes Services (AKS) 中管理資源的最佳做法
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 66676c90e73d1886c86d8afda8cbbecce239a005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259522"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>應用程式開發人員在 Azure Kubernetes Services (AKS) 中管理資源的最佳做法

當您在 Azure Kubernetes Service (AKS) 中開發和執行應用程式時，必須考量幾個重要環節。 您管理應用程式部署的方式，可能會對您所提供的服務產生使用者體驗方面的負面影響。 在 AKS 中開發和執行應用程式時請留意一些可供遵循的最佳做法，以利您成功開發和執行。

這篇最佳做法文章主要會從應用程式開發人員的觀點說明如何執行您的叢集和工作負載。 如需系統管理最佳做法的相關資訊，請參閱[叢集操作員在 Azure Kubernetes Service (AKS) 中進行隔離和資源管理的最佳做法][operator-best-practices-isolation]。 在本文中，您將了解：

> [!div class="checklist"]
> * Pod 資源要求和限制為何
> * 如何使用 Dev Spaces 和 Visual Studio Code 來開發及部署應用程式
> * 如何使用 `kube-advisor` 工具來檢查部署的問題

## <a name="define-pod-resource-requests-and-limits"></a>定義 Pod 資源要求和限制

**最佳作法指引** - 對您 YAML 資訊清單中的所有 Pod 設定 Pod 要求和限制。 如果 AKS 叢集使用*資源配額*，而您未定義這些值，則您的部署可能會遭到拒絕。

在 AKS 叢集內管理計算資源的主要方式，是使用 Pod 要求和限制。 這些要求和限制可讓 Kubernetes 排程器得知應為 Pod 指派哪些計算資源。

* **Pod CPU/記憶體請求**定義該窗格定期需要的一組 CPU 和記憶體量。
    * 當 Kubernetes 計畫程式嘗試在節點上放置 pod 時，pod 請求用於確定哪個節點有足夠的資源可用於計畫。
    * 不設置 pod 請求會將其預設為定義的限制。
    * 監視應用程式的性能以調整這些請求非常重要。 如果發出的請求不足，則應用程式可能會由於過度調度節點而收到性能下降。 如果請求被高估，您的應用程式可能會增加計畫的難度。
* **Pod CPU/記憶體限制**是窗格可以使用的最大 CPU 和記憶體量。 這些限制有助於定義在節點不穩定時由於資源不足而應殺死哪些 pod。 如果沒有適當的限制，吊艙將被殺死，直到資源壓力解除。
    * Pod 限制有助於定義 Pod 何時失去對資源消耗的控制。 當超過限制時，將確定該窗格的優先順序，以便進行殺滅，以維護節點運行狀況並將對共用節點的 pod 的影響降至最低。
    * 不設置 pod 限制預設為給定節點上的最高可用值。
    * 您設定的 Pod 限制不應高於節點所能支援的數量。 每個 AKS 節點都會保留一定數量的 CPU 和記憶體供核心 Kubernetes 元件使用。 您的應用程式可能會耗用太多節點上的資源，而使其他 Pod 無法成功執行。
    * 同樣，在白天或一周的不同時間監視應用程式的性能非常重要。 確定峰值需求的時數，並將 pod 限制與滿足應用程式最大需求所需的資源對齊。

在 pod 規範中，根據上述資訊定義這些請求和限制是**最佳做法，非常重要**。 如果不包含這些值，Kubernetes 計畫程式不能考慮應用程式説明安排決策所需的資源。

如果計畫程式將 Pod 放在資源不足的節點上，應用程式性能將降低。 強烈建議群集管理員在需要設置資源請求和限制的命名空間上設置*資源配額*。 如需詳細資訊，請參閱 [AKS 叢集的資源配額][resource-quotas]。

當您定義 CPU 要求或限制時，其值會以 CPU 單位來測量。 
* *1.0* CPU 等同於節點上的一個基礎虛擬 CPU 核心。 
* GPU 也會使用相同的測量方式。
* 您可以定義以毫核為單位測量的分數。 例如 *，100m*是基礎 vCPU 內核的*0.1。*

在下列單一 NGINX Pod 的基本範例中，Pod 會要求 *100m* 的 CPU 時間和 *128Mi* 的記憶體。 Pod 的資源限制設為 *250m* 的 CPU 和 *256Mi* 的記憶體：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

如需關於資源測量和指派的詳細資訊，請參閱[管理容器的計算資源][k8s-resource-limits]。

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>對 AKS 叢集開發和偵錯應用程式

**最佳做法指引** - 開發小組應使用 Dev Spaces 對 AKS 叢集進行部署和偵錯。 這個開發模型可確保在應用程式部署至生產環境之前，均實作了角色型存取控制、網路或儲存體需求。

透過 Azure Dev Spaces，您將可直接對 AKS 叢集開發、偵錯和測試應用程式。 小組內的開發人員可互相合作，而在整個應用程式生命週期中進行建置及測試。 您可以繼續使用現有的工具，例如 Visual Studio 或 Visual Studio Code。 為 Dev Spaces 安裝的延伸模組會提供在 AKS 叢集中執行和偵錯應用程式的選項：

![使用 Dev Spaces 在 AKS 叢集中進行應用程式的偵錯](media/developer-best-practices-resource-management/dev-spaces-debug.png)

這種使用 Dev Spaces 的整合式開發和測試程序可降低對於本機測試環境的需求，例如 [minikube][minikube]。 因為您可以對 AKS 叢集進行開發和測試。 如上一節所提到的，使用命名空間可透過邏輯方式隔離叢集，而讓此叢集可受到保護和隔離。 當您的應用程式準備好要部署到生產環境時，您將可放心部署，因為您的開發全都是在實際的 AKS 叢集中完成的。

Azure 開發空間適用于在 Linux 窗格和節點上運行的應用程式。

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>使用適用於 Kubernetes 的 Visual Studio Code 延伸模組

**最佳作法指引** - 在撰寫 YAML 資訊清單時，請安裝並使用適用於 Kubernetes 的 VS Code 延伸模組。 您也可以使用整合式部署解決方案的延伸模組，這對於不常與 AKS 叢集互動的應用程式擁有者可能有所幫助。

[適用於 Kubernetes 的 Visual Studio Code 延伸模組][vscode-kubernetes]可協助您對 AKS 開發和部署應用程式。 此延伸模組會提供 Kubernetes 資源以及 Helm 圖表和範本的 IntelliSense。 您也可以在 VS Code 中瀏覽、部署及編輯 Kubernetes 資源。 此延伸模組也提供對於要在 Pod 規格中設定的資源要求或限制進行 IntelliSense 檢查的功能：

![適用於 Kubernetes 的 VS Code 延伸模組針對遺漏的記憶體限制發出的相關警告](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>使用 kube-advisor 定期檢查應用程式的問題

**最佳實踐指南**- 定期運行最新版本的`kube-advisor`開源工具，以檢測群集中的問題。 如果您在現有的 AKS 叢集上套用資源配額，請先執行 `kube-advisor` 以尋找未定義資源要求和限制的 Pod。

[kube-advisor][kube-advisor]工具是一個關聯的 AKS 開源專案，用於掃描 Kubernetes 群集並報告它發現的問題。 一個實用的檢查，就是找出沒有備妥資源要求和限制的 Pod。

kube-Advisor 工具可以報告 Windows 應用程式和 Linux 應用程式的 PodSpecs 中缺少的資源請求和限制，但 kube-advisor 工具本身必須安排在 Linux 窗格中。 您可以使用窗格配置中的[節點選擇器][k8s-node-selector]安排具有特定作業系統的 pod 在節點池上運行。

在裝載許多開發小組和應用程式的 AKS 叢集中，若沒有這些資源要求和限制集，就可能難以追蹤 Pod。 最佳做法是在 AKS 叢集上定期執行 `kube-advisor`。

## <a name="next-steps"></a>後續步驟

這篇最佳做法文章主要會從叢集操作員的觀點說明如何執行您的叢集和工作負載。 如需系統管理最佳做法的相關資訊，請參閱[叢集操作員在 Azure Kubernetes Service (AKS) 中進行隔離和資源管理的最佳做法][operator-best-practices-isolation]。

若要實作這些最佳做法，請參閱下列文章：

* [使用 Dev Spaces 部署][dev-spaces]
* [使用 kube-advisor 定期檢查問題][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
