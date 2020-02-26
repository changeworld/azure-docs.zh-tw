---
title: 在 Azure Kubernetes Service 中安裝 Linkerd （AKS）
description: 瞭解如何安裝和使用 Linkerd，以在 Azure Kubernetes Service （AKS）叢集中建立服務網格
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593703"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中安裝 Linkerd （AKS）

[Linkerd][linkerd-github]是開放原始碼服務網格和[由 cncf 發展專案][linkerd-cncf]。 Linkerd 是一種超細服務網格，提供包括流量管理、服務身分識別和安全性、可靠性和可檢視性的功能。 如需 Linkerd 的詳細資訊，請參閱官方[LINKERD 常見問題][linkerd-faq]和[Linkerd 架構][linkerd-architecture]檔。

本文說明如何安裝 Linkerd。 Linkerd `linkerd` 用戶端二進位檔會安裝到您的用戶端電腦上，而 Linkerd 元件則會安裝到 AKS 上的 Kubernetes 叢集中。

> [!NOTE]
> 這些指示參考 Linkerd 版本 `stable-2.6.0`。
>
> Linkerd `stable-2.6.x` 可以針對 Kubernetes `1.13+`版本執行。 您可以在[GitHub Linkerd 版本][linkerd-github-releases]中找到其他穩定和 edge Linkerd 版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 下載並安裝 Linkerd Linkerd 用戶端二進位檔
> * 在 AKS 上安裝 Linkerd
> * 驗證 Linkerd 安裝
> * 存取儀表板
> * 從 AKS 卸載 Linkerd

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集（已啟用 RBAC 的 Kubernetes `1.13` 和更新版本），並已建立與叢集的 `kubectl` 連接。 如果您需要這些專案的協助，請參閱[AKS 快速入門][aks-quickstart]。

所有 Linkerd pod 都必須排程在 Linux 節點上執行-此安裝程式是下面詳述的安裝方法中的預設值，不需要額外的設定。

本文將 Linkerd 安裝指引分成幾個不同的步驟。 其結果與官方 Linkerd 入門[指南][linkerd-getting-started]的結構相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>在 AKS 上安裝 Linkerd

在安裝 Linkerd 之前，我們將執行安裝前檢查，以判斷控制平面是否可以安裝在我們的 AKS 叢集上：

```console
linkerd check --pre
```

您應該會看到類似下列的內容，指出您的 AKS 叢集是 Linkerd 的有效安裝目標：

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

現在是時候安裝 Linkerd 元件了。 使用 `linkerd` 和 `kubectl` 二進位檔，將 Linkerd 元件安裝到您的 AKS 叢集中。 系統會自動建立 `linkerd` 命名空間，並將元件安裝到此命名空間中。

```console
linkerd install | kubectl apply -f -
```

Linkerd 會部署一些物件。 您會看到上述 `linkerd install` 命令輸出中的清單。 根據您的叢集環境，部署 Linkerd 元件大約需要1分鐘的時間才能完成。

此時，您已將 Linkerd 部署至 AKS 叢集。 為了確保我們已成功部署 Linkerd，讓我們繼續進行下一節，以[驗證 Linkerd 安裝](#validate-the-linkerd-installation)。

## <a name="validate-the-linkerd-installation"></a>驗證 Linkerd 安裝

確認已成功建立資源。 使用[kubectl get svc][kubectl-get]和[kubectl get pod][kubectl-get]命令來查詢 `linkerd` 命名空間，其中 Linkerd 元件是由 `linkerd install` 命令所安裝：

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

下列範例輸出顯示現在應該正在執行的服務和 pod （已排程在 Linux 節點上）：

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd 會透過 `linkerd` 用戶端二進位檔提供命令，以驗證是否已成功安裝和設定 Linkerd 控制平面。

```console
linkerd check
```

您應該會看到類似下列的內容，表示您的安裝成功：

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>存取儀表板

Linkerd 隨附的儀表板可讓您深入瞭解服務網格和工作負載。 若要存取儀表板，請使用 `linkerd dashboard` 命令。 此命令會利用[kubectl 埠轉送][kubectl-port-forward]，在您的用戶端電腦和 AKS 叢集中的相關 pod 之間建立安全連線。 然後，它會自動在您的預設瀏覽器中開啟儀表板。

```console
linkerd dashboard
```

此命令也會建立埠轉寄，並傳回 Grafana 儀表板的連結。

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>從 AKS 卸載 Linkerd

> [!WARNING]
> 從執行中的系統刪除 Linkerd 可能會導致您的服務之間發生流量相關的問題。 請確定您已在不 Linkerd 的情況下，將您的系統布建為正常運作，再繼續進行。

首先，您必須移除資料平面 proxy。 從工作負載命名空間移除任何自動 Proxy 插入[注釋][linkerd-automatic-proxy-injection]，並推出您的工作負載部署。 您的工作負載應該不會再有任何相關聯的資料平面元件。

最後，移除控制平面，如下所示：

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>後續步驟

若要探索 Linkerd 的更多安裝和設定選項，請參閱下列官方 Linkerd 指導方針：

- [Linkerd-Helm 安裝][linkerd-install-with-helm]
- [Linkerd-多階段安裝以滿足角色許可權][linkerd-multi-stage-installation]

您也可以使用來遵循其他案例：

- [Linkerd emojivoto 示範][linkerd-demo-emojivoto]
- [Linkerd 書籍示範][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
