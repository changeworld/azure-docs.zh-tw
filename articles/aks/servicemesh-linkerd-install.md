---
title: '在 Azure Kubernetes Service (AKS 中安裝 Linkerd) '
description: 瞭解如何安裝和使用 Linkerd 以在 (AKS) 叢集的 Azure Kubernetes Service 中建立服務網格
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5b2ec220bbf14a79247ed2c78a0e84f4717fae3
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684231"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS 中安裝 Linkerd) 

[Linkerd][linkerd-github] 是開放原始碼服務網格和 [CNCF 發展專案][linkerd-cncf]。 Linkerd 是一種超細服務網格，其提供的功能包括流量管理、服務身分識別、安全性、可靠性和可檢視性。 如需 Linkerd 的詳細資訊，請參閱官方 [LINKERD 常見問題][linkerd-faq] 和 [Linkerd 架構][linkerd-architecture] 檔。

本文說明如何安裝 Linkerd。 Linkerd `linkerd` 用戶端二進位檔會安裝到您的用戶端電腦上，而 Linkerd 元件則會安裝到 AKS 上的 Kubernetes 叢集中。

> [!NOTE]
> 這些指示參考 Linkerd 版本 `stable-2.6.0` 。
>
> Linkerd `stable-2.6.x` 可以針對 Kubernetes 版本執行 `1.13+` 。 您可以在 [GitHub Linkerd 版本][linkerd-github-releases]中找到其他穩定和 edge Linkerd 版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 下載並安裝 Linkerd Linkerd 用戶端二進位檔
> * 在 AKS 上安裝 Linkerd
> * 驗證 Linkerd 安裝
> * 存取儀表板
> * 從 AKS 卸載 Linkerd

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集 (Kubernetes 和更新版本 `1.13` ，並已啟用 KUBERNETES RBAC) 並已建立與叢集的連線 `kubectl` 。 如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]。

所有 Linkerd pod 都必須排程在 Linux 節點上執行-此設定是以下詳述的安裝方法中的預設值，不需要額外的設定。

本文將 Linkerd 安裝指引分成數個分開的步驟。 結果與官方 Linkerd 入門 [指南][linkerd-getting-started]的結構相同。

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

在安裝 Linkerd 之前，我們會執行預先安裝檢查，以判斷是否可以在 AKS 叢集上安裝控制平面：

```console
linkerd check --pre
```

您應該會看到類似下面的內容，表示您的 AKS 叢集是適用于 Linkerd 的有效安裝目標：

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

現在就是安裝 Linkerd 元件的時候了。 使用 `linkerd` 和 `kubectl` 二進位檔將 Linkerd 元件安裝到您的 AKS 叢集中。 `linkerd`將會自動建立命名空間，並將元件安裝到此命名空間中。

```console
linkerd install | kubectl apply -f -
```

Linkerd 會部署多個物件。 您會看到上述命令的輸出清單 `linkerd install` 。 Linkerd 元件的部署大約需要1分鐘的時間才能完成，取決於您的叢集環境。

至此，您已將 Linkerd 部署到您的 AKS 叢集。 為了確保成功部署 Linkerd，讓我們繼續進行下一節以 [驗證 Linkerd 安裝](#validate-the-linkerd-installation)。

## <a name="validate-the-linkerd-installation"></a>驗證 Linkerd 安裝

確認已成功建立資源。 您可以使用 [kubectl get svc][kubectl-get] 和 [kubectl get pod][kubectl-get] 命令來查詢 `linkerd` 命名空間，此命名空間是由命令安裝 Linkerd 元件 `linkerd install` ：

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

下列範例輸出顯示 (排程在 Linux 節點上的服務和 pod) 現在應該正在執行：

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

Linkerd 透過用戶端二進位檔提供命令， `linkerd` 以驗證是否已成功安裝和設定 Linkerd 控制平面。

```console
linkerd check
```

您應該會看到如下所示的內容，表示您的安裝已成功：

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

Linkerd 隨附的儀表板，可讓您深入瞭解服務網格和工作負載。 若要存取儀表板，請使用 `linkerd dashboard` 命令。 此命令會利用 [kubectl 埠轉送][kubectl-port-forward] ，在您的用戶端電腦與 AKS 叢集中的相關 pod 之間建立安全的連接。 然後，它會在您的預設瀏覽器中自動開啟儀表板。

```console
linkerd dashboard
```

此命令也會建立埠轉送，並傳回 Grafana 儀表板的連結。

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>從 AKS 卸載 Linkerd

> [!WARNING]
> 從執行中系統刪除 Linkerd 可能會導致您的服務之間發生流量相關問題。 請確定您為系統提供的布建在沒有 Linkerd 的情況下仍可正常運作，然後再繼續進行。

首先，您必須移除資料平面 proxy。 從工作負載命名空間移除任何自動 Proxy 插入 [注釋][linkerd-automatic-proxy-injection] ，並推出您的工作負載部署。 您的工作負載應該不再有任何相關聯的資料平面元件。

最後，移除控制平面，如下所示：

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>後續步驟

若要探索更多 Linkerd 的安裝和設定選項，請參閱下列官方 Linkerd 指導方針：

- [Linkerd-Helm 安裝][linkerd-install-with-helm]
- [Linkerd-要滿足角色許可權的多階段安裝][linkerd-multi-stage-installation]

您也可以使用下列內容來執行其他案例：

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
